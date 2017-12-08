title: '"overflow:hidden"导致城市选择不能上下滑动'
overflow: hidden"导致城市列表不能上下滑动"
author: LS
categories: 手册
date: 2017-12-07 11:10:39
tags:
---
前言
=========

最近公司app迭代测试，测试同事提了个bug是`社保认证页面选择完省份后，城市选择列表无法上下移动（三星S6机型）`。这是个H5页面上的功能，然后后台再转为jsp上传服务器再给个地址到前端这边，前端这边是用reactNative的WebView引用这个地址展示出来。

![](http://osph37mc5.bkt.clouddn.com/0503.png)

## 检查areaSelect function

第一眼我就觉得应该是个兼容问题。但是上手测试机发现是只有一边`省份选择`才能滑动，`城市选择`不能动。并且H5或者jsp地址在三星S6浏览器均表现正常时，我觉得会不会是reactNative的WebView不兼容的原因呢，于是在定义`transform`的style上补充前缀

![](http://osph37mc5.bkt.clouddn.com/0501.png)

另外一开始我只定义了移动端触摸事件，为保险我根据环境区分电脑端和移动端添加触摸事件。

![](http://osph37mc5.bkt.clouddn.com/0502.png)

更新以上代码后发现问题还是存在，而且app不好调试。

后来经同事在网上查询有人提议去掉`overflow:hidden`就解决问题了，后来我修改UI布局左右改为上下后，这个滑动不了的问题不存在了。

![](http://osph37mc5.bkt.clouddn.com/0504.png)

由此得出结论省份`overflow:hidden`遮住了城市选择滑动列表，但是具体原因不是很清楚（笑哭）。