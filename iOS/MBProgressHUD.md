# MBProgressHUD[传送](https://github.com/jdg/MBProgressHUD)

1. CoreGraphics 的绘制
2. Timer和CadisplayLink的使用
3. UIView的动画
4. MotionEffects
5. intrinsicContentSize
6. setter方法
7. 主线程的宏

1. 框架特别的细致，很老iOS版本的风格与现在截然不同，因此做了特殊处理，👍，不得不服
``` Objective-C
    BOOL isLegacy = kCFCoreFoundationVersionNumber < kCFCoreFoundationVersionNumber_iOS_7_0;
    _contentColor = isLegacy ? [UIColor whiteColor] : [UIColor colorWithWhite:0.f alpha:0.7f];
```

2. 忽略警告的编译预处理指令
``` Objective-C
#pragma clang diagnostic push
#pragma clang diagnostic ignored "-Wdeprecated-declarations"
        self.bezelView.alpha = self.opacity;
#pragma clang diagnostic pop
```

3. NSTimer的强引用问题

timer需要加入到runloop中才能正常运行，默认加入的currentrunloop的default模式下，这种runloop就持有了timer，如果timer不执行invalidate，runloop就会一直持有。timer会持有target。所以如果希望在dealloc中执行timer的invalidate，那么就会循环引用。runloop --> timer --> self.(另外target传递一个weakSelf为什么不起作用，我还需要在理解一下学习一下)
