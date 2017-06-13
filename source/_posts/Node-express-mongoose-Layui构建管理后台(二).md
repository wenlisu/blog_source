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

Layui是一款采用自身模块规范编写的国产前端UI框架，类似于前一两年大火的bootstrap，个人认为Layui的UI组件明显少于bootstrap，但是在功能的拓展性和与页面的交互上L强于B。  

- Layui结构

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