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
这个例子也介绍了使用take运算符从序列开始后返回固定数目的元素

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

## Working with Subjects
一个Subject是一个有序的桥接或者是代理，它是Rx的实现，它扮演的角色即使observer也是Observable。因为他是个Observer,它可以订阅一个或者多个Observables，又因为它也是个Observable，它可以传递他观察到的元素 通过重新发射他们，并且他们可以发射新的元素。
```
extension ObservableType {
    func addObserver(_ id: String) -> Disposable {
        return subscribe { print("Subscription:", id, "Event:", $0) }
    }
}

func writeSequenceToConsole<O: ObservableType>(name: String, sequence: o) -> Disposable {
    return sequence.subscribe {event in 
        print("Subscription: \(name), event: \(event)")
    }
}

```

### PublishSubject
在订阅时对所有他们的观察者广播新的事件

```
example("PublishSubject") {
    let disposeBag = DisposeBag()
    let subject = PublishSubject<String>()

    subject.addObserver("1").addDisposableTo(disposeBag)
    subject.onNext("A")
    subject.onNext("B")
    subject.onNext("C")

    subject.addObserver("2").addDisposableTo(disposeBag)
    subject.onNext("D")
    subject.onNext("E")
}
```
注意
这个例子也介绍了使用 onNext(_:)的便利方法，相当于使用了on(.next(_:)),它使用提供的元素产生一个新的Next事件发射给他的订阅者们。也有OnError(_:)和onCompleted()的便利方法，相当于on(.error(_:))和on(.completed)

### ReplaySubject
广播新的事件给所有的订阅者，而且指定之前事件的bufferSize数字给新的订阅者
```
example("ReplaySubject") {
    let disposeBag = DisposeBag()
    let subject = ReplaySubject<String>.create(bufferSize: 1)
    subject.addObserver("1").addDisposableTo(disposeBag)
    subject.onNext("dog")
    subject.onNext("cat")

    subject.addObserver("2").addDisposableTo(disposeBag)
    subject.onNext("apple")
    subject.onNext("banala")
}
```

### BehaviorSubject
将所以之前的value也都广播发送给订阅者
```
example("BehaviorSubject") {
    let disposeBag = DisposeBag()
    let subject = BehaviorSubject(value: "apple")

    subject.addObserver("1").addDisposableTo(disposeBag)
    subject.onNext("dog")
    subject.onNext("cat")

    subject.addObserver("2").addDisposableTo(disposeBag)
    subject.onNext("A")
    subject.onNext("B")

    subject.addObserver("3").addDisposableTo(disposeBag)
    subject.onNext("apple")
    subject.onNext("orange")
}
```
笔记
注意什么在之前的例子中丢失了？一个完成的事件。PublishSubject, ReplaySubject 和 BehaviorSubject 不会自动发射完成事件，当他们将要被回收的时候。

Variable
封装了一个BehaviorSubject, 所以它将会发射绝大部分value给新的订阅者。并且Variable也维护当前的value状态。Variable将不会发射错误事件。然而，它将会发射一个完成事件并且在deinit中结束。
```
example("Variable") {
    let disposeBag = Disposable()
    let variable = Variable("apple")

    variable.asObservable().addObserver("1").addDisposableTo(disposeBag)
    variable.value = "dog"
    variable.value = "cat"

    variable.asObservable().addObserver("2").addDisposableTo(disposeBag)
    variable.value = "A"
    variable.value = "B"
}
```
笔记
用Variable实例调用asObservable()方法 为了访问它底层的BehaviSubject序列。Variables 没有实现on运算符(onNext(_:)),但是取而代之的暴露一个value属性，这个属性可以用来获得当前的value，同时也可以设置一个新的值。设置一个新的value将会添加底层的BehaviorSubject序列中

## Combination Operators
运算符会组合多个Observable源到一个Observable。

### startWith
在它从source开始发射元素之前，发射指定的元素序列

```
example("startWith") {
    let disposeBag = DisposeBag()
    Observable.of("dog", "cat", "mouse", "rabbit")
        .startWith("1")
        .startWith("2")
        .startWith("3")
        .subscribe(onNext: {print($0) })
        .addDisposable(disposeBag)
}
```
笔记
正如这个例子表明的，startWith 可以以后进先出为基础，也就是说，每一个成功的startWith元素将会预先放在主要的startWith元素之前。

### merge
将源Observable序列组合成一个新的Observable序列，并且会发射每一个元素正如它从source Observable序列一样

```
example("merge") {
    let disposeBag = DisposeBag()
    let subject1 = PublishSubject<String>()
    let subject2 = PublishSubject<String>()

    Observable.of(subject1, subject2)
        .merge()
        .subscribe(onNext: { print($0)})
        .addDisposableTo(disposeBag)

    subject1.onNext("A")
    subject1.onNext("B")

    subject2.onNext("1")
    subject2.onNext("2")

    subject1.onNext("ab")
    subject2.onNext("3")
}
```

### zip
将最多将8个source Observable序列组合一个新的Observable序列，并且将会发射每一个组合而成的Observable序列的元素
```
example("zip") {
    let disposeBag = DisposeBag()
    let stringSubject = PublishSubject<String>()
    let intSubject = PublishSubject<Int>()
    Observable.zip(stringSubject, intSubject) { stringSubject, intElement in]
        "\(stringElement)\(intElement)"
        }
        .subscribe(onNext: { print($0) })
        .addDisposableTo(disposeBag)
    stringElement.onNext("A")
    stringElement.onNext("B")

    intSubject.onNext(1)
    intSubject.onNext(2)

    stringSubject.onNext("AB")
    intSubject.onNext(3)
}
```


## combineLastest
将最多8个source Observable 序列组合成一个新的Observable序列，一旦所有source序列发射至少一个元素，将最新的元素组合而成的序列将会发射元素。任何soure Observable序列发射一个新的元素
```
example("combineLastest") {
    let disposeBag = DisposeBag()
    let stringSubject = PublishSubject<String>()
    let intSubject = PublishSubject<Int>()

    Observable.combineLastest(stringSubject, intSubject) { stringElement, intElement in
        "\(stringElement)\(intElement)"
        }
        .subscribe(onNext: { print($0) })
        .addDisposableTo(disposeBag)
    stringElement.onNext("A")
    stringElement.onNext("B")
    intSubject.onNext(1)
    intSubject.onNext(2)
    stringSubject.onNext("AB")
}
```

```
example("Array.combineLastest") {
    let disposeBag = DisposeBag()
    let stringObservable = Observable.just("1")
    let fruitObservable = Observable.from(["apple", "orange", "banana"])
    let animalObservable = Observable.of("dog", "cat", "mouse", "rabbit")

    Observable.combineLastest([stringObservable, fruitObservable, animalObservable]) {
        "\($0[0])\($0[1])\($0[2])"
        }
        .subscribe(onNext: { print($0) })
        .addDisposableTo(disposeBag)
}
```
笔记
combineLastest 扩展在Array 需要所有的Observable序列都是同一个类型

## switchLatest
将由Observable序列发射的元素转换为Observable序列，并从最近的内部Observable序列发射元素。

```
example("switchLatest") {
    let disposeBag = DisposeBag()
    let subject1 = BehaviorSubject(value: "football")
    let subject2 = BehaviorSubject(value: "apple")
    let variable = Variable(subject1)

    variable.asObservable()
        .switchLatest()
        .subscribe(onNext: { print($0) })
        .addDisposableTo(disposeBag)

    subject1.onNext("basketball")
    subject1.onNext("vallball")

    variable.value = subject2

    subject1.onNext("ball")
    subject2.onNext("xxx")
}
```
笔记
在这个例子中，在设置了variable.value值为subject2之后添加ball元素没有任何的影响，因为仅仅是最近的内部的Observable序列将会发射元素。

## Transforming Operators
运算符它们将下一个元素发射通过一个Observable序列

### map
将Observable序列发射的元素应用一个转换闭包，返回一个新的转换后Observable序列
```
example("map") {
    let disposeBag = DisposeBag()
    Observable.of(1, 2, 3)
        .map { $0 * $0 }
        .subscribe(onNext: { print($0) })
        .addDisposableTo(disposeBag)
}
```

### flapMap and flatMapLatest
转换Observable序列发射的元素到Observable序列中去，并且两个Observable序列合并成一个序列。当你有一个Observable序列它自己发射Observable序列的时候特别有作用，并且当你想要能够对新的Observable序列做出响应的时候。flatMap和flatMapLatest的不同点是，flatMapLatest将仅仅发射最近的Observable序列的元素。
```
example("flatMap and flatMapLatest") {
    let disposeBag = DisposeBag()
    struct Player {
        var score: Variable<Int>
    }
    let man = Player(score: Variable(80))
    let woman = Player(score: Variable(90))

    let player =Variable(man)
    player.asObservable()
        .flatmap { $0.score.asObservable() }
        .subscribe(onNext: { print($0) })
        .addDisposableTo(disposeBag)

    man.score.value = 85
    player.value = woman

    man.score.value = 95
    woman.score.value = 100
}
```
笔记
在这个例子中，使用flatMap可能好出乎意料的结果。在赋值woman 给 player.value后，woman.value 将要开始发射元素。但是之前的内部的Observable序列man.score 也将要发射元素。通过改变flatMap到faltMapLatest,仅仅最近的内部序列woman.score 将会发射元素。那么设置man.score.value 到95不会产生影响
笔记
flatMapLatest 时间上是map和switchLatest运算的组合

### scan
有一个seed开始，对Observable序列发射的每个元素应用计算闭包，返回一个单元素的Observable序列
```
example("scan") {
    let disposeBag = DisposeBag()
    Observable.of(10, 100, 1000)
        .scan(1){ aggregateValue, newValue in
            aggregateValue + newValue
        }
        .subscribe(onNext: { print($0) })
        .addDisposableTo(disposeBag)
}
```

## Filtering and Conditional Operators
这些运算符对于source Observable序列中发射出来的元素进行选择

### filter
仅仅发射那些从Observable序列中发射出来并满足指定条件的元素

```
example("filter") {
    let disposeBag = DisposeBag()
    Observable.of(
        "1", "2", "3",
        "4", "5", "6",
        "7", "8", "9")
        .filter {
            $0 == "5"
        }
        .subscribe(onNext: {print($0) })
        .addDisposableTo(disposeBag)
}
```

### distinctUntilChanged
抑制Observable序列发射的连续的重复的元素
```
example("distinctUntilChanged"){
    let disposeBag = DisposeBag()
    Observable.of("1", "2", "1", "1", "1", "6", "1")
        .distinctUntilChanged()
        .subscribe(onNext: { print($0) })
        .addDisposableTo(disposeBag)
}
```

### elementAt
仅仅发射在Observable序列指定位置的元素
```
example("elementAt") {
    let disposeBag = DisposeBag()
    Observable.of("0", "1", "2", "3", "4")
        .elementAt(3)
        .subscribe(onNext: { print($0) })
        .addDisposableTo(disposeBag)
}
```

### single
仅仅发射第一个（或者第一个满足条件）的由Observable序列发射的元素。如果Observable序列没有发射元素它将会抛出错误。
```
example("single") {
    let disposeBag = DisposeBag()
    Observable.of("0", "1", "2", "3")
        .single()
        .subscribe(onNext: { print($0) })
        .addDisposableTo(disposeBag)
}


example("single with conditions") {
    let disposeBag = DisposeBag()
    
    Observable.of("🐱", "🐰", "🐶", "🐸", "🐷", "🐵")
        .single { $0 == "🐸" }
        .subscribe { print($0) }
        .addDisposableTo(disposeBag)
    
    Observable.of("🐱", "🐰", "🐶", "🐱", "🐰", "🐶")
        .single { $0 == "🐰" }
        .subscribe { print($0) }
        .addDisposableTo(disposeBag)
    
    Observable.of("🐱", "🐰", "🐶", "🐸", "🐷", "🐵")
        .single { $0 == "🔵" }
        .subscribe { print($0) }
        .addDisposableTo(disposeBag)
}
```

### take
从Observable序列的开头发射指定数目的元素

```
example("take") {
    let disposeBag = DisposeBag()
    Observable.of("1", "2", "3", "4")
        .take(3)
        .subscribe(onNext: {print($0) })
        .addDisposableTo(disposeBag)
}
```

tabeLast
从Observable序列的末尾发射指定数目的元素
```
example("takeLast"){
    let disposeBag = DisposeBag()
    Observable.of("1", "2", "3", "4" ,"5")
        .tabeLast(3)
        .subscribe(onNext: { print($0) })
        .addDisposableTo(disposeBag)
}
```

tableWhile
从Observable序列的开头发射元素,只要它满足条件。
```
example("takeWhile") {
    let disposeBag = DisposeBag()
    Observable.of(1, 2, 3, 4, 5, 6)
        .tableWhile{ $0 < 4}
        .subscribe(onNext: { print($0) })
        .addDisposableTo(disposeBag)
}
```

takeUntil
从Observable序列中发射元素，直到一个引用序列发射了一个元素

```
example("takeUntil"){
    let disposeBag = DisposeBag()

    let sourceSequence = PublishSubject<String>()
    let referenceSequence = PublishSubject<String>()

    sourceSequence
        .takeUntil(referenceSequence)
        .subscribe{ print($0) }
        .addDisposableTo(disposeBag)
    sourceSequence.onNext("cat")
    sourceSequence.onNext("rabbit")
    sourceSequence.onNext("dog")

    referenceSequence.onNext("apple")

    sourceSequence.onNext("frog")
    sourceSequence.onNext("pig")
    sourceSequence.onNext("monkey")
}
```

### skip
过滤发射Observable序列中从头开始指定数目的元素
```
example("skip"){
    let disposeBag = DisposeBag()
    Observable.of("cat", "rabbit", "dog", "frog", "pig")
        .skip(2)
        .subscribe(onNext: { print($0) })
        .addDisposableTo(disposeBag)
}
```

### skipWhile
过滤发射Observable序列中从头开始满足条件的元素
```
example("skip"){
    let disposeBag = DisposeBag()

    Observable.of(1, 2, 3, 4, 5)
        .skipWhile( $0 < 4)
        .subscribe(onNext: { print($0) })
        .addDisposableTo(disposeBag)
}
```

### skipWhileWithIndex
过滤发射Observable序列中从头开始满足条件的元素， 并且发射与下的元素，闭包传递了元素的Index
```
example("skipWhileWithIndex") {
    let disposeBag = DisposeBag()

    Observable.of("cat", "rabbit", "dog", "frog" , "pig")
        .skipWhileWithIndex{ element, index in
            index < 3
        }
        .subscribe(onNext: { print($0) })
        .addDisposableTo(disposeBag)
}
```

### skipUntil
过滤发射source Observable的元素，直到一个引用的Observable序列发射了一个元素
```
example("skipUntil") {
    let disposeBag = DisposeBag()

    let sourceSequence = PublishSubject<String>()
    let referenceSequence = PublishSubject<String>()

    sourceSequence
        .skipUntil(referenceSequence)
        .subscribe(onNext: { print($0) })
        .addDisposableTo(disposeBag)

    sourceSequence.onNext("cat")
    sourceSequence.onNext("rabbit")
    sourceSequence.onNext("dog")

    referenceSequence.onNext("apple")

    sourceSequence.onNext("frog")
    sourceSequence.onNext("pig")
    sourceSequence.onNext("monkey")
}
```

## Mathematical and Aggregate Operators
操作符操作Observable发射出来的全部序列

### toArray
将Observable序列转换成Array，将这个array作为Observable的单个元素发射，并且正常结束。

```
example("toArray") {
    let disposeBag = DisposeBag()

    Observable.range(start: 1, count: 10)
        .toArray()
        .subscribe { print($0) }
        .addDisposable(disposeBag)
}
```

### reduce
从一个seed初值开始，然后对Observable发射的所有的元素应用计算闭包，返回一个聚合之后的元素作为一个单个的元素。

```
example("reduce") {
    let disposeBag = DisposeBag()
    Observable.of(10, 100, 1000)
        .reduce(1, accumulater: +)
        .subscribe(onNext: { print($0) })
        .addDisposableTo(disposeBag)
}
```

### concat
连接OBservable内部序列发射的所有元素，等待在下一个序列发射元素每一个序列正常结束，
```
example("concat") {
    let disposeBag = DisposeBag()
    let subject1 = BehaviorSubject(value: "apple")
    let subject2 = BehaviorSubject(value: "dog")

    let variable = Variable(subject1)

    variable.asObservable()
        .concat()
        .subscribe { print($0) }
        .addDisposableTo(disposeBag)

    subject1.onNext("orange")
    subject1.onNext("banana")

    variable.value = subject2
    subject2.onNext("I would be ignored")
    subject2.onNext("cat")

    subject1.onCompleted()
    subject2.onNext("mouse")
}
```

## Connectable Operators
可连接的可观察序列类似于普通的可观察序列，除了它们在订阅时不开始发射元素，而是仅当调用它们的connect（）方法时。 这样，您可以等待所有预期的订阅者在开始发出元素之前订阅可连接的Observable序列。

```
func sampleWithoutConnectableOperators(){
    printExampleHeader(#function)
    let interval = Observable<Int>.interval(1, scheduler: MainScheduler.instance)

    _ = interval.subscribe(onNext: { print("Subscription: 1, Event: \($0)") })

    delay(5) {
        _ = interval
            .subscribe(onNext: { print("Subscription: 2, Event: \($0)") })
    }
}
```
笔记
interval 创建了一个Observable序列，它每隔一段时间在指定的间隔发射序列。

### publish

将一个source Observable序列转换成一个可连接的序列
```
func sampleWithPublish() {
    printExampleHeader(#function)

    let intSequence = Observable<Int>.interval(1, scheduler: MainScheduler.instance).publish()

    _ = intSequence
        .subscribe(onNext: { print("Subscription 1:, Event: \($0)") })
    delay(2) { _ = intSequence.connect() }
    delay(4) {
        _ = intSequence
            .subscribe(onNext: { print("Subscription 2:, Event: \($0)") })
    }

    delay(6) {
        _ = intSequence
            .subscribe(onNext: { print("Subscription 3:, Event: \($0)") })
    }
}
```

### replay
将一个source Observable序列转换成可连接的序列，并且将会重新发射bufferSize数目的之前发射的元素给每一个订阅者。

```
func sampleWithReplayBuffer() {
    printExampleHeader(#function)

    let intSequence = Observable<Int>.interval(1, scheduler: MainScheduler.instance)
    .replay(5)

    _ = intSequence
        .subscribe(onNext: { print("Subscription 1:, Event: \($0)") })
    delay(2) { _ = intSequence.connect() }
    delay(4) {
        _ = intSequence
            .subscribe(onNext: { print("Subscription 2:, Event: \($0)") })
    }

    delay(8) {
        _ = intSequence
            .subscribe(onNext: { print("Subscription 3:, Event: \($0)") })
    }
}
```

### multicast
将一个source Observable序列转换为一个可连接序列，并且通过指定的Subject广播他的元素。

```
func sampleWithMulticast() {
    printExampleHeader(#function)

    let subject = PublishSubject<Int>()
    _ = subject
        .subscribe(onNext: { print("Subject: \($0)") })
    let intSequence = Observable<Int>.interval(1, scheduler: MainScheduler.instance)
    .multicast(subject)

    _ = intSequence
        .subscribe(onNext: { print("\tSubscription 1:, Event: \($0)") })
    delay(2){ _ = intSequence.connect() }
    delay(4) {
        _ = intSequence
            .subscribe(onNext: { print("\tSubscription 2:, Event: \($0)" })
    }
    delay(6) {
        _ = intSequence
            .subscribe(onNext: { print("\tSubscription 3:, Event: \($0)" })
    }
}
```

## Error Handling Operators
运算符帮助你从Observable序列的error notification中恢复

### catchErrorJustReturn
当Observable序列发射了一个error，它发射一个元素然后发射一个完成元素。
```
example("catchErrorJustReturn") {
    let disposeBag = DisposeBag()
    let sequenceThatFails = PublishSubject<String>()

    sequenceThatFails
        .catchErrorJustReturn("haha")
        .subscribe { print($0) }
        .addDisposableTo(disposeBag)

    sequenceThatFails.onNext("1")
    sequenceThatFails.onNext("2")
    sequenceThatFails.onNext("3")
    sequenceThatFails.onNext("4")
    sequenceThatFails.onError("error")
}
```

### catchError
当发生错误的时候切换到一个恢复的序列

```
example("catchError") {
    let disposeBag = DisposeBag()
    let sequenceThatFails = PublishSubject<String>()
    let recoverSequence = PublishSubject<String>()

    sequenceThatFails
        .catchError {
            print("Error:", $0)
            retrun recoverSequence
        }
        .subscribe { print($0) }
        .addDisposableTo(disposeBag)
    sequenceThatFails.onNext("1")
    sequenceThatFails.onNext("2")
    sequenceThatFails.onNext("3")
    sequenceThatFails.onNext("4")
    sequenceThatFails.onError("error")
    
    recoverSequence.onNext("haha")   
}
```

### retry
当Observable序列发射错误的时候，无限重新发射
```
example("retry") {
    let disposeBag =DisposeBag()
    var count = 1
    let sequenceThatErrors = Observable<String>.create { observer in
        observer.onNext("apple")
        observer.onNext("banana")
        observer.onNext("orange")
        if count == 1 {
            observer.onError("error")
            print("Error encountered")
            count += 1
        }

        observer.onNext("cat")
        observer.onNext("dog")
        observer.onNext("mouse")
        return Disposables.create()
    }

    sequenceThatErrors
        .retry()
        .subscribe(onNext: { print($0) })
        .addDisposableTo(disposeBag)
}
```

### retry(_:)
重复从错误中恢复，并指定一个最大尝试数目。
```
example("retry") {
    let disposeBag =DisposeBag()
    var count = 1
    let sequenceThatErrors = Observable<String>.create { observer in
        observer.onNext("apple")
        observer.onNext("banana")
        observer.onNext("orange")
        if count < 5 {
            observer.onError("error")
            print("Error encountered")
            count += 1
        }

        observer.onNext("cat")
        observer.onNext("dog")
        observer.onNext("mouse")
        observer.onCompleted()
        return Disposables.create()
    }

    sequenceThatErrors
        .retry(3)
        .subscribe(onNext: { print($0) })
        .addDisposableTo(disposeBag)
}
```