
# 为什么使用Rx
Rx使得编写App以一种声明的方式。

## Bindings
```
Observable.combineLatest(firstName.rx.text, secondName.rx.text){ $0 + " " + $1 }
    .map { "Greetings, \($0)" }
    .bindTo(greetingLabel.rx.test)
```
这也可以和UITableView和UICollectionView一起工作。

```
viewModel
    .rows
    .bindTo(resultsTableView.rx.items(cellIdentifier: "WikipediaSearchCell", cellType: WikipediaSearchCell.self)) { (_, viewModel, cell) in 
        cell.title = viewModel.title
        cell.url = viewModel.url
    }
    .addDisposableTo(disposeBag)
```
官方的建议是总是加上`.addDisposableTo(disposeBag)`,即使没有必要为简单的绑定也加上。

## retries
如果API不会失败将会很美好，但是不幸的是他们会失败。让我们认为这是个API的方法：
`func doSomethingIncredible(forWho: String) throw -> IncredibleThing`
如果你正在使用像这样的一个函数，万一它失败了去重试是一件很困难的事。更不用说复杂的模型指数回退。当然它是可以完成的，但是代码将可能会包含很多不并不关心的状态切换，并且它无法重用。
在理想状况下，你将会想要捕获重试的本质，并且应用它去做任何操作。
这就是你可以怎么使用Rx去完成简单的重试
```
doSomethingIncredible("me")
    .retry(3)
```
你也可以简单地制定自定义的重试操作

## Delegates
取代去做这些单调乏味和不生动的：
```
public func scrollViewDidScroll(scrollView: UIScrollView) { [weak self]
    self?.leftPositionConstraint.constant = scrollView.contentoffset.x
}
```
...write
```
self.resultsTableView
    .rx.contentOffset
    .map { $0.x }
    .bindTo(self.leftPostionConstraint.rx.constant)
```

## KVO
取代：
"TickTock" 将被释放当键值观察仍然注册着。观察信息会泄露，甚至可能错误的附加到别的值。
并且
```
- (void)observeValueForKeyPath:(NSString *) keyPath
                      ofObject:(id) object
                        change:(NSDictionary *)change
                       context:(void *)context
```
使用rx.observe 和 rx.observeWeakly
下面是它们怎么被正确的使用：
```
view.rx.observe(CGRect.self, "frame")
    .subcribe(onNext: { frame in
        print("Got new frame \(frame)")
    })
    .addDisposableTo(disposeBag)
```
或者
```
someSuspiciousViewController
    .rx.observeWeakly(Bool.self, "behavingOk")
    .subscribe(onNext: { behavingOk in
        print("Cats can purr? \(behavingOk)")
    })
    .addDisposableTo(disposeBag)
```

## Notifications
取代使用
```
@available(iOS 4.0, *)
public func addObserverForName(name: String?, Object obj: AnyObject?, queue: NSOperationQueue?, usingBlock block: (NSNotification) -> Void) -> NSObjectProtocal
```
...仅仅写成
```
NSNotificationCenter.default
    .rx.notification(NSNotification.Name.UITextViewDidBeginEditing, Object: myTextView)
    .map { /*do something with data*/ }
    ...
```

## Transient state
瞬态在异步编程中有许多问题，一个经典的例子就是自动搜索框。
如果你不用Rx写一个自动完成搜索框的话，第一个问题可能需要解决的就是当c在abc中被输入的时候，ab的请求将被挂起，这个挂起的请求被取消。好的这个不是太难处理，你仅仅是创建了一个额外的变量去引用挂起的请求。
下一个问题是如果请求失败了，你需要做复杂的重试逻辑。但是好的，捕获重试次数的大量的空间需要被清理。
如果程序将等一段时间在发一个请求之前。在此之后，我们不想我们的服务器除非有人在这个过程中输入了非常长的内容。我们可能需要一个定时器。
还有一个问题是，当搜索正在进行的时候我们屏幕需要显示什么。和我们失败重试的时候显示什么。
写下上面所有问题和也许测试他们就是单调乏味的。这是用Rx写下的相同逻辑的代码
```
searchTextField.rx.text
    .throttle(0.3, scheduler: MainScheduler.instance)
    .distinctUnitlChanged()
    .flatMapLatest { query in
        API.getSearchResults(query)
            .retry(3)
            .startWith([])
            .catchErrorJustReturn([])
    }
    .subscribe(onNext: { results in 
        // bind to UI
    })
    .addDisposableTo(disposeBag)
```
这里没有额外的标记和空间。Rx 照顾了所有的复杂的瞬态。

## Compositional disposal
让我们设想一个剧本，你想要在tableView上显示图片。第一图片将从URL上请求获取，然后还需要解码和模糊。
如果cell划出可见区域，整个过程将被取消，那将是非常棒的，因为带宽和模糊过程的时间非常长。
如果cell进入可见区域，我们不是仅仅立刻开始下载图片，因为用户可能滑动很快，那也是非常棒的，因为那会有很多的请求建立和取消。
如果我们能够限制并发请求图片的数量，那同样是很好的，因为图片的模糊处理是代价很大的。
这是我们用Rx写的：
```
let imageSubscription = imageURLs
    .throttle(0.2, scheduler: MainScheduler.instance)
    .flatMapLatest { imageURL in
        API.fetchImage(imageURL)
    }
    .observeOn(operationScheduler)
    .map { imageData in
        return decodeAndBlurImage(imageData)
    }
    .subscribe(onNext: { blurredImage in 
        imageView.image = blurredImage
        })
    .addDisposableTo(reuseDisposeBag)
```

## Aggregating network requests
如果你需要发起两个网络请求然后在他们都结束的时候合并结果呢？
好的，这时候需要zip 运算
```
let userRequest: Observable<User> = API.getUser("me")
let friendsRequest: Observable<Friends> = API.getFriends("me")
Observable.zip(userRequest, friendsRequest) { user, friends in 
    return (user, friends)
}
.observeOn(MainScheduler.instance)
.subscribe(onNext: { user, friends in 
    })
.addDisposableTo(disposeBag)
```
还有很多更好的Rx的用武之地。

## State
语言允许变化，这使得非常容易访问到全局状态和改变它。不受控制的改变全局的状态非常容易导致组合爆炸。
而在另一方面，当使用一个机智的方式，指令式语言能够写出效率更高的代码离硬件更近。
通常去解决组合爆炸的方式是保证状态尽可能简单，和使用单向数据流模型驱动。
这才是Rx真正闪耀的地方。
Rx优点是一个函数式指令式的框架。它使你能够使用不可变的定义和纯的函数以一种可信的方式去处理可变的状态
那么练习的例子是什么？

## Easy Intergration
如果我们需要创建我们的Observable呢？它很简单。这段代码是从RxCocoa中取出来的，这是就是你所有需要做的去封装一个NSURLSession
```
extension NSURLSession {
    public func response(_ request: URLRequest) -> Observable<(Data, HTTPURLResponse> in 
        return Observable.create{ observer in
            let task = self.base.dataTask(with: request){ (data, response, error) in
                guard let response = response, let data = data else {
                    observer.on(.error(error ?? RxCocoaURLError.unknown))
                    return
                }
                guard let = httpResponse = response as? HTTPURLResponse else {
                    observer.on(.error(RxCocoaURLError.nonHTTPResponse(response: response)))
                    return
                }
                observer.on(.next(data, httpResponse))
                observer.on(.completed)
            }
            let t = task
            t.resume()
            return Disposables.create(with: task.cancel)
        }
    }
}
```

## Benefits
简而言之，使用Rx将会是你的代码：
1.  可组合的： Rx就是组合的昵称
2.  可重用的： 因为它是可组合的
3.  声明式的： 因为定义是不可变的只有数据在改变
4.  可理解和简洁的： 提高抽象级别，消除瞬态
5.  稳定的：   Rx代码通过完全的单元测试
6.  更少的状态量：因为你的程序是单向数据流
7.  没有泄露： 因为资源管理很简单

## It's not all or nothing
在你的app中尽可能多的使用Rx建模是好的想法。
但是你不知道所有的运算符又或者不存在符合你需求的运算符呢？
好吧，所有的Rx运算符都是基于数学的和直觉的。
好消息是10-15个运算符就能够覆盖大部分经典的场景。这个列表已经包含了熟悉的比如map, filter, zip, observeOn,...
但是如果你需要一个不再列表上的运算符呢？好吧，你可以自定义。
如果创建某个运算符十分的困难，或者有一个遗留的状态代码需要使用呢？好吧，你让自己陷入一团乱麻中，但是你可以跳出Rx，处理数据，然后返回它。
