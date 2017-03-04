# MBProgressHUD1.0.0[传送](https://github.com/jdg/MBProgressHUD)

1. CoreGraphics 的绘制
2. Timer和CadisplayLink的使用
3. UIView的动画
4. MotionEffects
5. intrinsicContentSize
6. setter方法
7. 检测主线程的宏



- 框架特别的细致，很老iOS版本的风格与现在截然不同，因此做了特殊处理，👍，不得不服

``` Objective-C
    BOOL isLegacy = kCFCoreFoundationVersionNumber < kCFCoreFoundationVersionNumber_iOS_7_0;
    _contentColor = isLegacy ? [UIColor whiteColor] : [UIColor colorWithWhite:0.f alpha:0.7f];
```

- 忽略警告的编译预处理指令

``` Objective-C
#pragma clang diagnostic push
#pragma clang diagnostic ignored "-Wdeprecated-declarations"
        self.bezelView.alpha = self.opacity;
#pragma clang diagnostic pop
```

- NSTimer的强引用问题

timer需要加入到runloop中才能正常运行，默认加入的currentrunloop的default模式下，这种runloop就持有了timer，如果timer不执行invalidate，runloop就会一直持有。timer会持有target。所以如果希望在dealloc中执行timer的invalidate，那么就会循环引用。runloop --> timer --> self.(另外target传递一个weakSelf为什么不起作用，我还需要在理解一下学习一下)

补充：
是不是瞬间觉得完美了，呵呵，我只能说少年，你没理解两者之间的区别。在block中，block是对变量进行捕获，意思是对使用到的变量进行拷贝操作，注意是拷贝的不是对象，而是变量自身。拿上面的来说，block中只是对变量wself拷贝了一份，也就是说，block中也定义了一个weak对象，相当于，在block的内存区域中，定义了一个__weak blockWeak对象,然后执行了blockWeak = wself；注意到了没，这里并没有引起对象的持有量的变化，所以没有问题,再看timer的方式，虽然你是将wself传入了timer的构造方法中，我们可以查看NSTimer的

1
+ (NSTimer *)timerWithTimeInterval:(NSTimeInterval)seconds target:(id)target selector:(SEL)aSelector userInfo:(id)userInfo repeats:(BOOL)repeats1
定义,其target的说明The object to which to send the message specified by aSelector when the timer fires. The timer maintains a strong reference to this object until it (the timer) is invalidated,是要强应用这个变量的 也就是说,大概是这样的，__strong strongSelf = wself 强引用了一个弱应用的变量，结果还是强引用，也就是说strongSelf持有了wself所指向的对象(也即是self所只有的对象)，这和你直接传self进来是一样的效果，并不能达到解除强引用的作用！看来只能换个思路了，我直接生成一个临时对象，让Timer强用用这个临时对象，在这个临时对象中弱引用self，可以了吧。
