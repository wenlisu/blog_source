title: Node+express+mongoose+Layui构建管理后台(二)
author: LS
categories: 手册
date: 2017-06-13 16:18:55
tags:
---
前言
=========

[Node+express+mongoose+Layui构建管理后台(一)](https://wenlisu.github.io/2017/06/12/Node+express+mongoose+Layui构建简单管理后台一.html#more)中说了基本环境的搭建，接下来先搞定界面UI部分，[Demo位置](https://github.com/wenlisu/SL)，当初是先完成界面UI的开发，然后把打包压缩好的界面HTML文件放到express里。  

## Layui

Layui是一款采用自身模块规范编写的国产前端UI框架，类似于前一两年大火的bootstrap框架，个人认为Layui的UI组件明显少于bootstrap，但是在功能的拓展性和与页面的交互上L强于B。  

- Layui自带结构

```
 ├─css //css目录
    │  └─modules //模块css目录（一般如果模块相对较大，我们会单独提取）
    │      ├─laydate
    │      ├─layer
    │      │  └─default
    │      └─layim
    │          └─skin
    ├─font  //字体图标目录
    ├─images //图片资源目录（一些表情等）
    │  └─face
    └─lay //JS目录
        ├─dest //经过合并的完整模块
        └─modules //各模块/组件
```

- Layui 定义为“经典模块化”

```
//layui模块的定义
layui.define([mods], function(exports){
  //……
  exports('mod', api);
});  
 
//layui模块的使用
layui.use(['mod1', 'mod2'], function(args){
  var mod = layui.mod1;
  //……
});  
```

- 自定义模块引入

```
//加载一个自定义模块
layui.config({
  base: 'layui_modules/' //你的模块目录
}).use('index'); //加载入口

//加载多个自定义模块
layui.config({
        version: true, //一般用于更新模块缓存
         debug: false //用于开启调试模式
        base: 'layui_modules/'
    }).extend({ //设定模块别名
        index: 'index'
    });
```

还有其他一些功能，具体到[Layui官网](http://www.layui.com/)上查找。

## 后台UI目录结构

```
├── app.js//加载自定义模块和设定子页面加载模式
├── apple-touch-icon.png
├── font//iconfont矢量图
├── images//图片
├── layui_modules//自定义模块
├── modules//子页面合集
│   ├── index
│   ├── qqm.baseNumber
│   ├── qqm.cpu
│   ├── qqm.internet
│   ├── qqm.log
│   ├── qqm.monitor
│   ├── qqm.offlineServer
│   └── qqm.onlineServer
├── plugins//layui必需文件
│   ├── _.js
│   └── layui
├── scripts                
│   └── index.js//index页面js
└── styles//样式
```

## 自定义模块
- fetch  
Fetch 的出现就是为了解决 XHR 的问题  
使用 XHR 发送一个 json 请求一般是这样：

````
var xhr = new XMLHttpRequest();  
xhr.open('GET', url);   
xhr.responseType = 'json';  
xhr.onload = function() {
  console.log(xhr.response);
};  
xhr.onerror = function() {
  console.log("Oops, error");
};  
xhr.send();````

使用 Fetch 后，顿时看起来好一点  

```
fetch(url).then(function(response) {
  return response.json();
}).then(function(data) {
  console.log(data);
}).catch(function(e) {
  console.log("Oops, error");
});
```

使用 ES6 的 箭头函数 后：  

```
fetch(url).then(response => response.json())
  .then(data => console.log(data))
  .catch(e => console.log("Oops, error", e))
```

上面使用 Promise 的写法还是有 Callback 的影子，下面使用 async/await 来做最终优化：  

```
try {
  let response = await fetch(url);
  let data = response.json();
  console.log(data);
} catch(e) {
  console.log("Oops, error", e);
}
// 注：这段代码如果想运行，外面需要包一个 async function
```

- config  
定义环境变量  
- route  
子页面路由跳转，设置面包屑导航
- tab  
设置页面选项卡
- view  
模版渲染  
- commom  
错误提示
- method  
网上下载功能源码修改成自定义模块，以供layui识别   
修改方式：增加一个识别条件  

```
(function webpackUniversalModuleDefinition(root, factory) {
    if (typeof exports === 'object' && typeof module === 'object')
        module.exports = factory();
    ...
    else if (window.layui && layui.define) {
       layui.define(function (exports) {
          exports('echarts', factory());
        });
    } else ...
})(this, function() {
	...
```

- echarts  
百度的纯Javascript的图表库