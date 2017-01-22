1.Rx可以统一action，notification，closures,KVO。
2.Rx中的Observable与普通序列相比优势在于他可以异步接受元素。这也是Rx的本质。
3.序列不会execute their subscription closure unless there is a subscriber.

4.如果创建一个序列。Observable
5.PublishSubject,ReplaySubject,BehaviorSubject,Variable.
6.Combining运算
7.Transforming运算
8.Filtering运算
9.Mathematical和Aggregate运算
10.Connectable运算
11.Error运算
12.Debugging运算

13.Drive
```
let results = query.rx.text.asDriver()
    .throttle(0.3, scheduler: MainScheduler.instance)
    .flatMapLatest { query in
        fetchAutoCompleteItems(query)
            .asDriver(onErrorJustReturn: [])
    }

results
    .map { "\($0.count)" }
    .drive(resultCount.rx.text)               
    .addDisposableTo(disposeBag)

results
    .drive(resultsTableView.rx.items(cellIdentifier: "Cell")) { (_, result, cell) in
        cell.textLabel?.text = "\(result)"
    }
    .addDisposableTo(disposeBag)

```

第一处代码asDriver()将ControlProperty封装成Driver，并且增加了一些内容，满足三个条件1不抛出error，2在主线程，3side effects sharing
第二处代码asDriver(onErrorJustReturn:[])，相当转换成安全的driver
第三处代码drive相当于是个bindTo，优势在于主线程，不会发射错误

14.和rac的比较
RxSwift和Rac有点相似，因为Rac借鉴了大量Rx的概念。
这个项目最主要的目标之一是创建一个简洁的Api，能够和其他Rx的实现对齐，提供一个同步模型，提供更多优化机会，和Swift内置更加一致的错误处理

我们已经决定仅仅依赖Swift/llvm 编译器和不介绍任何额外依赖

也许这些项目的主要的区别在于构建抽象的方法

RxSwift项目的主要目标是提供环境无关地观察序列形式地组合计算。我们目标提升RxSwift在特定平台上的经验。为了去做这个，RxCocoa使用通用计算去建立更实用的抽象，封装了Foundation/Cocoa/UIKit框架。那意味着其他库提供上下文和语义给RxSwift这个通用计算引擎，正如Driver，ControlProperty，ControlEvent和更多。

15.冷热信号的区别
热信号是指序列一创建就会发射元素。冷信号指有订阅者才会发射元素。