# instrument调试 #

问题：手机连接电脑之后调试，instrument不能使用，不能选中

解决方案：重启

我最终的解决步骤：

1. 拔掉iPhone的USB线，重启iPhone
2. 关闭Xcode和Instruments
3. 重新连接iPhone到Mac上
4. 重启Xcode并启动Profile
5. 成功

# xcode 快捷键 #

1. cmd + shift + o(快速打开文件)
2. cmd + shift + 0(打开documentation)
2. ctrl + 6（快速打开方法）

3. cmd +f （在当前文件内查找）
4. cmd + shift + f（在整个工程中查找）
5. cmd + g（寻找下一个）
6. cmd + shift + g（寻找上一个）

7. cmd + ctrl + 上下（切换h,m文件）
8. cmd + ctrl + 左右 (左上角的前进和后退)
9. cmd + alt + 左右（关闭或者展开大括号）

10. cmd+ alt + \[ 和 alt + cmd + \]（选中代码后上下移）
11. cmd + \[ 和 cmd + \](选中代码左右缩进) 

12. cmd + shift + n（新建项目）
13. cmd + n(新建文件)
14. ctrl + space （spotlight）
15. cmd + space (切换输入法)
16. cmd + / (注释或者打开注释)
17. cmd + y(关闭或者打开全局注释)
18. cmd + \\ (设置断点或者打开断点)
19. cmd + r(运行)
20. cmd + b(编译)
21. cmd + .(停止)
22. cmd + ctrl + y (继续运行)
23. （fn） + f6(单步调试)
24. （fn）+ f7(单步进入)
25. （fn）+ f8(单步跳出)

26. cmd + k(清除控制台)

27. cmd + enter(正常编辑模式)
28. cmd + alt + enter（助手模式）
29. cmd + alt + shift +enter（与上一次commit比较的模式）

30. cmd + 0（左边的文件目录）
31. cmd + shift + y（底部的调试框）
32. cmd + alt + 0（右边的工具）
33. cmd + j(焦点跳转到编辑器)
34. cmd + shift + c(焦点跳转到控制台)
35. cmd + shift + j(导航栏跳转到当前正在浏览的文件)
36. cmd +ctrl + e(一次性编辑选中的变量)
37. cmd + shift + \\ 自定义的快捷键（将原来的ctrl + i 修改成这个了）
