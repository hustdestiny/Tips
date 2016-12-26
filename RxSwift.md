这是一座大山，而我必须要翻越！先从翻译RxSwift官方的资料开始吧！

## 介绍

### 为什么使用RxSwift

我们所写的大量的代码都涉及到响应外部事件。当一个使用者操纵一个control，我们需要写一个@IBAction去处理它的响应。当键盘位置改变的时候，我们需要去观察通知去检测。我们必须提供闭包去执行URL sessions 返回的数据。与此同时我们使用KVO去检测变量地改变。所有这些复杂的系统使得你的代码没有必要地复杂。难道没有一个更好的统一系统能够处理我们所有的call/response 代码吗？Rx就是这样的系统。

RxSwift是Reactive Extensions的官方实现，Reactive Extensions在大部分主流的语言和平台中存在。

### 概念

每一个Observable对象仅仅是一个序列。
Observable序列和Swift的序列类型比较 关键的优势在于它可以接受到异步的元素。这是RxSwift的本质。别的所有的都是建立在这个概念上的。
* 一个 Observable相当于一个序列类型
* 这个 Observable.subscribe(_:) 方法 相当于 SequenceType.generate()
* ObservableType.subscribe(_:) 使用一个Observer参数，它将会自动接受序列发射的事件和元素，取代手动调用next() 返回的generator.

如果一个Observable发射一个next event(Event.next(Element)), 它能够继续发射更多的事件。然而，如果这个Observable发射一个错误事件(Event.error(ErrorType))或者一个完成事件(Event,completed), 这个Observable序列就不能够继续发射事件给他的订阅者了。

序列的语法简明扼要地解释
```next * (error | completed)?
```
这个可以通过图形更可视化解释
--1--2--3--4--5--6--|---->// "|" = 正常结束
--a--b--c--d--e--f--X---->// "X" = 由于错误结束
--tap--tap--------tap---->// "|" = 无穷尽地，就像按钮的点击

注意
这些图被称作为子弹图。你可以学到跟多在RxMarbles.com.

#### Observables and observers(又名 subscribers)
Observables 将不会执行他们的订阅闭包，除非有一个订阅者。在下面的例子中，Observable的闭包将不会被执行，因为他们没有订阅者：

``` Swift
example("Observables with no subscribers"){
    _ = Observable<String>.create{ observerOfString -> Disposable in}
    print("this will never be printed")
    observerOfString.on(.next(""))
    observerOfString.on(.completed)
    return Disposables.create()
    }
}
```

在下面的例子中，这个闭包将会被执行当 subscribe(_:) 被调用的时候：
``` Swift
example("Observable with subscriber"){
    _ = Observable<String>.create { observerOfString in
            print("Observable created")
            observerOfString.on(.next(""))
            observerOfString.on(.completed)
            return Disposables.create()
        }
    .subscribe{ event in 
        print(event)
    }
}
```

注意
请你不要担心这些例子中的Observables是怎么被创建出来的。我们将在接下去的章节中讲解。

注意
subscriber(_:) 返回一个Disposable的实例，这代表着这是可回收的资源正如 subscription。再上一个列子中它被忽略了，但是正常情况下它应该被合理地处理。这正常的意味着把它加到一个DisposeBag实例中。所有接下去的列子将会包含合理的处理，因为，好吧，实践使永久。你可以学到更多这个内容，在Getting Started guide的Disposing section中。

## 创建并且订阅Observables

有好几种方式去创建和订阅Observable序列

Never
创建一个序列永不结束并且永不发射任何事件。
```
example("never"){
    let disposeBag = DisposeBag()
    let neverSequence = Observable<String>.never()
    let neverSequenceSubscription = neverSequence
        .subscribe{ _ in
            print("this will never be printed")
    }
    neverSequenceSubscription.addDisposableTo(disposeBag)
}
```

empty
创建一个空的Observable序列 仅仅发射一个完成事件。
```
example("empty"){
    let disposeBag = DisposeBag()
    Observable<Int>.empty()
    .subscribe{ event in 
        print(event)
    }
    .addDisposableTo(disposeBag)
}
```
注意
这个例子也介绍了链式创建和订阅一个Observable序列

just
创建仅有一个元素的Observable序列
```
example("just"){
    let disposeBag = DisposeBag()
    Observabel.just("a")
        .subscribe {event in
            print(event)
        }
        .addDisposableTo(disposeBag)
}
```

of
创建一个固定数目的Observable序列
```
example("of"){
    let disposeBag = DisposeBag()
    Observable.of("a","b","c","d")
        .subscribe(onNext:{ element in
            print(element)
        })
        .addDisposableTo(disposeBag)
}
```
注意
这个例子也介绍了使用subscribe(onNext:)便利方法。与subscribe(_:)不同，它为所有的事件类型（Next，Error，Completed）订阅了一个事件处理,
subscribe(onNext:)订阅了一个元素处理它将会忽略错误和完成事件仅仅生成Next事件元素。也有subscribe(onError:)和subscribe(onCompleted:)便利方法，使你仅仅订阅这些事件。并且有一个subscribe(onNext:onError:onCompleted:onDisposed:)方法，它允许你去处理一个或者多个事件类型并且subscription在任何情况下结束的，或者释放。

```
someObservable.subscribe(
    onNext: { print("Element:": $0) },
    onError: { print("Error:", $0) },
    onCompleted: { print("Completed") },
    onDisposed: { print("Disposed") }
)
```

from
从SequenceType创建一个Observable序列，正如Array,Dictionary,Set。

```
example("from"){
    let disposeBag = DisposeBag()
    Observable.from(["a","b","c"])
        .subscribe(onNext:{ print($0) })
        .addDisposableTo(disposeBag)
}
```
注意
这个例子也演示了使用默认的参数$0 代替了显示命名参数

create
创建一个自定义的Observable序列。
```
example("create"){
    let disposeBag = DisposeBag()
    let myJust = {(element: String) -> Observable<String> in
        return Observable.create{ observer in
            observer.on(.next(element))
            observer.on(.completed)
            return Disposables.create()
        }
    }
    myJust("apple")
    .subscribe { print($0)}
    .addDisposableTo(disposeBag)
}
```

range
创建一个Observable序列，它发射一个范围内的顺序的整数然后结束。
```
example("range"){
    let disposeBag = DisposeBag()
    Observable.range(start: 1, count: 10)
    .subscibe { print($0) }
    .addDisposableTo(disposeBag)
}
```

repeatElement
创建一个Observable序列，它无限的发射给的元素。
```
example("repeatElement") {
    let disposeBag = DisposeBag()
    Observable.repeatElement("apple")
    .take(3)
    .subscribe(onNext: { print($0) })
    .addDisposableTo(disposeBag)
}
```
注意
这个例子也介绍了使用take操作符从序列开始后返回固定数目的元素

generate
创建一个Observable序列，生成值只要提供的条件是true
```
example("generate"){
    let disposeBag = DisposeBag()
    Observable.generate(
            initialState: 0,
            condition: {$0 < 3},
            iterate: { $0 + 1}
        )
        .subscribe(onNext: { print($0) })
        .addDisposableTo(disposeBag)
}
```

deferred
创建一个新的Observable序列为每一个订阅者。

```
example("deferred"){
    let disposeBag = DisposeBag()
    var count = 1
    let deferredSequence = Observable<String>.deferred{
        print("Creating \(count)")
        count += 1

        return Observable.create { observer in
            print("Emitting...")
            observer.onNext("dog")
            observer.onNext("cat")
            observer.onNext("monkey")
            return Disposables.create()
        }
    }

    deferredSequence
        .subscribe(onNext: {print($0) })
        .addDisposableTo(disposeBag)

    deferredSequence
        .subscribe(onNext: {print($0) })
        .addDisposableTo(disposeBag)
}
```

error
创建一个Observable序列，不发射元素而是直接error并终止。
```
example("error") {
    let disposeBag = DisposeBag()
    Observable<Int>.error(TestError.test)
        .subscribe { print($0) }
        .addDisposableTo(disposeBag)
}
```

doOn
为每一个发射事件和返回值实现一个副作用
```
example("doOn") {
    let disposeBag = DisposeBag()
    Observable.of("a", "b", "c")
        .do(
        onNext: {print("Intercepted:", $0) }, 
        onError: {print("Intercepted error:", $0},
        onCompleted: {print("Completed"}
        )
        .subscribe(onNext: { print($0) })
        .addDisposableTo(disposeBag)
}
```
注意
有doOnNext(_:),doOnError(_:)和doOnCompleted(_:)便利方法去截获这个特殊事件，并且doOn(onNext:onError:onCompleted:)去截获一个或者多个事件在一次调用中。