# Tips at work list #

## Programe Languages ##
- Objective C
- Swift
- JavaScript
- Python
- C++
- MarkDown

## Tool ##
- XCode
- iterm2 zsh的安装以及主题的配置oh-my-zsh [oh-my-zsh](https://github.com/robbyrussell/oh-my-zsh) 
- alfred workflow的自定义
- sublime shift + cmd + p 万能快捷键 
- git 
当iOS，pb文件总是有冲突的 [解决方案](http://roadfiresoftware.com/2015/09/automatically-resolving-git-merge-conflicts-in-xcodes-project-pbxproj/)
- chrome
- owncloud
- brew
- ssh
- curl
- sed

## Website ##
- github
- gitlab
- teambition
- stackoverflow
- develop.apple.com

## Others ##
* 删除所有的.DS_Store		`sudo find ~/ -name ".DS_Store" -type f -delete`
* 禁止.DS_Store 产生		`defaults write com.apple.desktopservices DSDontWriteNetworkStores true`
* 列出5000端口被哪个占用  `lsof -i:5000`

* 购买苹果账号注意点：注册的名字使用真实姓名，付款人和注册人的信息要一致visa或者master的信用卡付款，否则需要上传身份证验证，整个过程可能需要两到三个工作日

* 增加远程的仓库。比如你从开源的仓库fork出来需要同步的时候，那么此时xxx就是开源仓库url，在本地仓库中执行 git remote add upstream xxx


## mac起一个flask写的web服务器，iphone访问流程
1. 将mac 和 iPhone 连接到同一个wifi环境下
2. app.run(host='10.220.19.191', port=5000) 一定要配置这两项，不可写localhost
3. 将防火墙什么的关了。起服务，然后就可以正常工作了
4. 有时候可能需要断一下wifi

## 学习新技术的步骤
1. 熟练语言语法
2. 框架api
3. 业务逻辑
4. 项目架构
