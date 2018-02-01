title: 使用高大上的pm2代替forever部署nodejs项目
author: LS
categories: 手册
date: 2018-01-29 18:04:47
tags:
---
pm2保驾护航
=========
forever已经out了，严重推荐pm2方式运行nodejs,这是最好的，没有之一。  
内建负载均衡（使用 Node cluster 集群模块）  
后台运行  
0 秒停机重载，我理解大概意思是维护升级的时候不需要停机.  
具有 Ubuntu 和 CentOS 的启动脚本  
停止不稳定的进程（避免无限循环）  
控制台检测  
提供 HTTP API  
远程控制和实时的接口 API ( Nodejs 模块，允许和 PM2 进程管理器交互 )  
[pm2官网 http://pm2.keymetrics.io/](http://pm2.keymetrics.io/)

## 安装环境

首先远程链接到服务器

```
ssh -p 22 app@192.168.1.127
```

并且全局安装nvm来管理node版本。接着安装pm2.

````
[root@localhost /]# npm install -g pm2
开始下载安装文件
  │ └── minimist@0.0.8 
  ├── moment@2.11.2 
  ├─┬ nssocket@0.6.0 
  │ └── lazy@1.0.11 
  ├── pidusage@1.0.1 
  ├─┬ pm2-axon@2.0.9 
  │ ├── amp@0.3.1 
  │ ├── amp-message@0.1.2 
  │ ├── configurable@0.0.1 
  │ └── escape-regexp@0.0.1 
  ├─┬ pm2-axon-rpc@0.3.6 
  │ ├─┬ commander@1.0.5 
  │ │ └── keypress@0.1.0 
  │ └── json-stringify-safe@5.0.1 
  ├─┬ pm2-deploy@0.2.1 
  │ ├── async@1.4.2 
  │ └── tv4@1.0.18 
  ├─┬ pm2-multimeter@0.1.2 
  │ └── charm@0.1.2 
  ├── pmx@0.6.1 
  ├── semver@5.1.0 
  ├── shelljs@0.6.0 
  ├─┬ source-map-support@0.4.0 
  │ └─┬ source-map@0.1.32 
  │   └── amdefine@1.0.0 
  └─┬ vizion@0.2.12 
    └── async@0.9.0 
````

成功。

## 测试个helloworld

helloworld.js

```
var http = require('http');
http.createServer(function (req, res) {
    res.writeHead(200, { 'Content-Type': 'text/plain' }); res.end('Hello World\n'); 
}).listen(1337, "127.0.0.1"); 
console.log('Server running at http://127.0.0.1:1337/');
```

pm2启动试试看

```
[root@localhost www]# pm2 start helloworld.js --name 'helloworld'
[PM2] Spawning PM2 daemon
[PM2] PM2 Successfully daemonized
[PM2] Starting helloworld.js in fork_mode (1 instance)
[PM2] Done.
 Use `pm2 show <id|name>` to get more details about an app
```

执行```pm2 monit```实时监控

![](http://osph37mc5.bkt.clouddn.com/0701.png)

接下来访问该网段下端口为1337的地址就能看见helloworld.js输出的内容了。

## json文件配置多个端口

一般一个服务器我们用不同的端口区别不同的项目地址。这时我们就能用一个json来配置相关端口，环境...

```
{
  "apps": [
  {
    "name": "doc",
    "script": "/home/app/doc/server.js", #当前目录下doc下的server.js
    "watch": false,
    "env": {
        "PORT": "8888",
    }
  },
  {...}
  ]
}
```

配置完之后```pm2 start apps.json```pm2启动apps.json

## pm2常用命令

* 列出所有应用
```
列出所有应用
```
* 启动应用
```
pm2 start appId
```
* 查看日志
```
pm2 logs 
```
* 重启应用
```
pm2 restart appId
```
* 停止应用
```
pm2 stop appId
```
* 查看资源消耗
```
pm2 monit
```
* 开启api访问
```
pm2 web
```