# Objective C Tips
Tips on Objective-C development

## UITableView

* dequeueReusableCellWithIdentifier的两个方法的区别：

	####网上版本
	带indexPath的方法总是返回一个cell（也就是说不可能为空），另一个方法是有可能为nil的;即：在`- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath`方法中可以省略以下代码：

`objective-c if (cell == nil) {cell = [[UITableViewCell alloc] initWithStyle;UITableViewCellStyleDefault reuseIdentifier:CellIdentifier];｝`
	####自测版本
	* 在iOS9.3和iOS8.1下测试，只要为tableview注册了相应的cell类，无论用两种方法中的哪一种，都不用手动创建就能获得cell，不会为nil。 
	* 然而如果没有为tableview注册cell类，则`dequeueReusableCellWithIdentifier:forIndexPath:`会crash，crash原因为“must register a nib or a class for the identifier or connect a prototype cell in a storyboard”，即`dequeueReusableCellWithIdentifier:forIndexPath:`方法必须与register方法配套使用。
	* 但如果没有为tableview注册cell类，`dequeueReusableCellWithIdentifier:`方法也不会崩溃，只是会返回nil，此时需要我们手动创建cell，如果未创建，则程序会crash，crash原因为“UITableView failed to obtain a cell from its dataSource”，即此时tableView无法获取到cell实例。

##  Rules

1. 减少宏的使用，如果要使用常量 使用const 的方式，如果使用函数 使用inline 方式
2. 变量命名使用驼峰方式，不建议(匈牙利命名，user_name)的方式
3. 通知的命名带上类型，通知的字符串使用 bundleid + 类名 + 功能名 比如 NSString * const LoginViewControllerLoginSuccessNotification = @“com.baidu.www.LoginViewController.LoginSuccess”,如需外部使用，用extern的方式
4. 命名准确表达一个变量或者方法的作用，如果不能表达，那么请拆成俩或者更多（第一个方法做一件事情）
5. 不建议使用pch，一方面是增加了编译时间，另一方面是不利于工程的结构化
6. 运算的前后添加空格，减少三目运算符的使用
7. 方法避免超过20行，循环避免超过两层
8. 不要使用Common文件夹，没有文件有它的归属
9. 使用属性还是成员变量的方式，我个人还是推荐使用属性，这里我自己还需要好好权衡，以后再作补充
10. 未完成或者pending的地方做上明显的标记提醒自己，我个人的建议是#warning ????????
11. 使用#pragma mark - 分割重要的模块，在ViewController中我的建议是
- Life Cycle
- Make Views
- Make Constraints
- Make Model / Data
- Actions
- Delegate
- Setter
- Others
12. 不建议使用frame去写UI。虽然代码效率高，但是很难写好，不利于修改，导致开发效率相对较低
13. 抓包charles,手机和电脑同一个wifi环境,设置手机代理端口:8888
14. cocoapods管理三方框架，现在另一个也比较流行Carthage
15. 不推荐使用try catch 的方式处理异常，会导致内存泄露，推荐NSError或者 NSAssert(condition, desc, ...)
16. XCode 插件管理工具Alcatraz


##读这些的源码
* AFN
* SDWebImage
* ReactiveCocoa
* GPUImage
* Masonry
* MBProgressHUD