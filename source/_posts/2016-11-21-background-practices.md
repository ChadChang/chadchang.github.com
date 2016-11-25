---
title: Common Background Practice
tags:
  - iOS
  - objc.io
date: 2016-11-21 23:04:56
---


[Common Background Practices - objc.io issue #2](https://www.objc.io/issues/2-concurrency/common-background-practices/) 的整理筆記

<!--more-->

### import 一大組資料 with progress bar

Start Import
``` objc
ImportOperation* operation = [[ImportOperation alloc] initWithStore:self.store fileName:fileName];

//注意：更新 progress 必須在 main thread
operation.progressCallback = ^(float progress) {
[[NSOperationQueue mainQueue] addOperationWithBlock:^
{
    self.progressIndicator.progress = progress;
}];
};
[self.operationQueue addOperation:operation];
 ```

ImportOperation
``` objc ImportOperation.m

- (id)initWithStore:(Store*)store fileName:(NSString*)name
{
    self = [super init];
    if(self) {
        self.store = store;
        self.fileName = name;
    }
    return self;
}


- (void)main
{
    // TODO: can we use new in the name? I think it's bad style, any ideas for a better name?
    self.context = [self.store newPrivateContext];
    self.context.undoManager = nil;

    [self.context performBlockAndWait:^
    {
        [self import];
    }];
}

- (void)import
{
    NSString* fileContents = [NSString stringWithContentsOfFile:self.fileName encoding:NSUTF8StringEncoding error:NULL];
    NSArray* lines = [fileContents componentsSeparatedByCharactersInSet:[NSCharacterSet newlineCharacterSet]];
    NSInteger count = lines.count;
    // 每 100 行才更新一次 progress 避免阻塞
    NSInteger progressGranularity = count/100;
    __block NSInteger idx = -1;
    [fileContents enumerateLinesUsingBlock:^(NSString* line, BOOL* shouldStop)
    {
        idx++;
        if (idx == 0) return; // header line

		 // 支持取消功能
        if(self.isCancelled) {
            *shouldStop = YES;
            return;
        }

        NSArray* components = [line csvComponents];

        if (components.count < 5) {
            NSLog(@"couldn't parse: %@", components);
            return;
        }

        [Stop importCSVComponents:components intoContext:self.context];

        if (idx % progressGranularity == 0) {
            self.progressCallback(idx / (float) count);
        }
        if (idx % ImportBatchSize == 0) {
            [self.context save:NULL];
        }
    }];
    self.progressCallback(1);
    [self.context save:NULL];
}
```
###  Asynchronous Networking

不可以使用以下代碼，因為不可以取消，會阻塞 thread，在並行時也需要多一個 thread

``` objc
dispatch_async(backgroundQueue, ^{
   NSData* contents = [NSData dataWithContentsOfURL:url]
   dispatch_async(dispatch_get_main_queue(), ^{
      // 處理 data
   });
});
```

使用 `NSURLConnection` or `NSURLSessionConnection` 來解決


###  File I/O in the Background

當文件太大時，則需要一行一行的讀取而不是一次將整個文件讀入

``` objc Reader
@interface Reader : NSObject
- (void)enumerateLines:(void (^)(NSString*))block
            completion:(void (^)())completion;
- (id)initWithFileAtPath:(NSString*)path;
@end

// implement
- (void)enumerateLines:(void (^)(NSString*))block
            completion:(void (^)())completion
{
    if (self.queue == nil) {
        self.queue = [[NSOperationQueue alloc] init];
        self.queue.maxConcurrentOperationCount = 1;
    }
    self.callback = block;
    self.completion = completion;
    self.inputStream = [NSInputStream inputStreamWithURL:self.fileURL];
    self.inputStream.delegate = self;
    [self.inputStream scheduleInRunLoop:[NSRunLoop currentRunLoop]
                                forMode:NSDefaultRunLoopMode];
    [self.inputStream open];
}
```

inputStream 的 delegate

``` objc
- (void)stream:(NSStream*)stream handleEvent:(NSStreamEvent)eventCode
{
    switch (eventCode) {
        ...
        case NSStreamEventHasBytesAvailable: {
            NSMutableData *buffer = [NSMutableData dataWithLength:4 * 1024];
            NSUInteger length = [self.inputStream read:[buffer mutableBytes]
                                             maxLength:[buffer length]];
            if (0 < length) {
                [buffer setLength:length];
                __weak id weakSelf = self;
                [self.queue addOperationWithBlock:^{
                    [weakSelf processDataChunk:buffer];
                }];
            }
            break;
        }
        ...
    }
}

- (void)processDataChunk:(NSMutableData *)buffer
{
    if (self.remainder != nil) {
        [self.remainder appendData:buffer];
    } else {
        self.remainder = buffer;
    }
    [self.remainder obj_enumerateComponentsSeparatedBy:self.delimiter
                                            usingBlock:^(NSData* component, BOOL last) {
        if (!last) {
            [self emitLineWithData:component];
        } else if (0 < [component length]) {
            self.remainder = [component mutableCopy];
        } else {
            self.remainder = nil;
        }
    }];
}
```
