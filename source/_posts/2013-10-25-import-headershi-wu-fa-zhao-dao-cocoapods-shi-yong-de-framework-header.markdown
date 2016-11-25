---
layout: post
title: "import header時無法找到cocoapods 使用的framework header"
date: 2013-10-25 12:30
comments: true
tags:
- iOS
- CocoaPods
---

在 .h file 中想要 import cocoapods 中的 lib 時，如果找不到，可以在 user header search path 中加上```"${PODS_ROOT}/BuildHeaders"```，並設為 recursive。

Reference: 

[http://stackoverflow.com/questions/12002905/ios-build-fails-with-cocoapods-cannot-find-header-files](http://stackoverflow.com/questions/12002905/ios-build-fails-with-cocoapods-cannot-find-header-files)