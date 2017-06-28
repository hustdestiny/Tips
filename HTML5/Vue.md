[Vue](https://cn.vuejs.org/)

尤雨溪大神的项目，前端MVVM框架，个人把它理解为是iOS中的ViewController

通读了文档一遍之后，发现了一个道理。难得不是vue而是webpack...

vue + vuex + vue-router + webpack + cli + element-ui + axios

0. 安装npm, 不做过多的讨论，自行查找
1. 安装cli: 
```bash
 npm install -g vue-cli
```
2. 通过某一个模块初始化项目：
```bash
vue init webpack-simple my-project
```
3. 进入工程目录，安装三方依赖：
```bash
cd my-project
npm install
```
4. 安装别的依赖的包
```bash
npm i element-ui -S
npm i style-loader -S
npm i axios -S
npm i vuex -S
npm i vue-router -S
```
5. 需要将更改webpack.config.js的配置
```json
[{
    test: /\.css$/,
    loader: 'style-loader!css-loader'
  },
  {
    test: /\.(eot|svg|ttf|woff|woff2)(\?\S*)?$/,
    loader: 'file-loader'
  }]
```

6. 通过修改index.html, App.vue, main.js, router.js, children.vue
配置路由
建立vue对象
...

7. 运行项目
```bash
npm run dev
```

8. 注意点 vue文件中template节点下只能有一个子节点

9. Vuex的基本流程  
        UIEvent 
        --> methods 
        --> actions 
        --> mutations 
        --> state 
        --> getters 
        --> computed 
        --> UI 
        --> UIEvent
这样就形成一个单项数据流