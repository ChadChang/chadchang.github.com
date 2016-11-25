---
layout: post
title: "Constants"
date: 2014-03-30 14:06
comments: true
tag:
- iOS
---

Objective-C 的 constants 也是有學問的

常常是用 `#define myConstants var` 來處理一些變數，但更好的作法是用 const

``` objc
// Constants.h
extern NSString * const MY_CONSTANT;

// Constants.m
NSString * const MY_CONSTANT = @"my_constant";
```

當希望此變數不為 global 時，則用 static

``` objc
// Constants.m
static NSString * const CONSTANT = @"my_constant";
```

`#define` 在 code 中是用取代的方式，compiler也沒辦法做 type 檢查，`stringInstance == strConstant` 的方式也比 #define 中用` isEqualToString` 快


當要宣告 integer 時，apple 建議用
``` objc 
NSInteger const counter = 0;
```

但在loop中會有警告` Assignment of read-only variable ‘counter’`，需要用以下方式

``` objc
static int counter = 0;

- (void)showTimer {
counter += 1;
}
```


Ref: 
[Constant in Objective-C](http://webbuilders.wordpress.com/2011/03/02/constant-in-objective-c/)