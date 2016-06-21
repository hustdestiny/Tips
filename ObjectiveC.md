# Objective C Tips
Tips on Objective-C development

## UITableView

* dequeueReusableCellWithIdentifier的两个方法的区别：

	####网上版本
	带indexPath的方法总是返回一个cell（也就是说不可能为空），另一个方法是有可能为nil的;即：在`- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath`方法中可以省略以下代码：

	```objective-c
if (cell == nil) {
    cell = [[UITableViewCell alloc] initWithStyle:UITableViewCellStyleDefault reuseIdentifier:CellIdentifier];
｝
```
	####自测版本
	* 在iOS9.3和iOS8.1下测试，只要为tableview注册了相应的cell类，无论用两种方法中的哪一种，都不用手动创建就能获得cell，不会为nil。 
	* 然而如果没有为tableview注册cell类，则`dequeueReusableCellWithIdentifier:forIndexPath:`会crash，crash原因为“must register a nib or a class for the identifier or connect a prototype cell in a storyboard”，即`dequeueReusableCellWithIdentifier:forIndexPath:`方法必须与register方法配套使用。
	* 但如果没有为tableview注册cell类，`dequeueReusableCellWithIdentifier:`方法也不会崩溃，只是会返回nil，此时需要我们手动创建cell，如果未创建，则程序会crash，crash原因为“UITableView failed to obtain a cell from its dataSource”，即此时tableView无法获取到cell实例。



##读这些的源码
* AFN
* SDWebImage
* ReactiveCocoa
* GPUImage
* Masonry
* MBProgressHUD