# Objective C Tips
Tips on Objective-C development

## UITableView

* dequeueReusableCellWithIdentifier的两个方法的区别，带indexPath的方法总是返回一个cell（也就是说不可能为空），另一个方法是有可能为nil的;即：在`- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath`方法中可以省略以下代码：

```objective-c
if (cell == nil) {
    cell = [[UITableViewCell alloc] initWithStyle:UITableViewCellStyleDefault reuseIdentifier:CellIdentifier];
｝
```




##读这些的源码
* AFN
* SDWebImage
* ReactiveCocoa
* GPUImage
* Masonry
* MBProgressHUD