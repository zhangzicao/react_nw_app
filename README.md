# 用 react 和 webpack 快速建构 NW.js 项目
``` bash
这是一个用于构建nw桌面应用的模板。相关技术：react16、webpack4、NW.js0.34.1、Node.js
```

## 介绍
本模板提供将NW.js配置和webpack配置分离开，减少了代码的侵入性。dev模式下会启动应用，支持热更新、浏览器tools调试、react开发工具，速度极快。npm run buildApp可构建桌面应用程序。

>NW.js(原名 node-webkit) 与 Electron 相似，提供了一个能通过 JavaScript 和 HTML 创建桌面应用的平台，同时集成 Node 来授予网页访问底层系统的权限。


## 目录
本模板基于react-create-app创建的模板修改，下面介绍本模板与初始模板的区别。

``` bash
|-dist  桌面应用的发布目录
|-nw_config NW.js相关配置和插件目录
    |-extension    扩展插件目录
    |-package.json 开发时NW.js的json配置文件
    |-package-build.json 打包时NW.js的json配置文件
|-public
    |-logo.ico 桌面应用图标
    |-logo.png 桌面应用图标
|-scripts
    |-build.js 新增：打包时创建应用程序
    |-start.js 新增：开发时使用应用程序预览、开发
```


## NPM命令

``` bash
# 下载依赖
npm install

# 开发应用，开启热替换服务器 localhost:3000
npm run dev

# 构建web应用
npm run build

# 构建web应用并打印报告
npm run build --report

# build后使用，通过已经构建好的web页面，构建桌面应用
npm run buildApp

# 测试
npm run test
```

## 下载NW.js 

**网络不太好**

nw的sdk大小较大，且不翻墙速度可能很慢，容易下载失败

这时候可以先下载好 nw 的 sdk包到本地，[https://nwjs.io/](https://nwjs.io/)

下载位置加入为：C:\Users\Administrator\Downloads\0.34.1\nwjs-sdk-v0.34.1-win-x64.zip
``` bash
npm i nw --nwjs_build_type=sdk --nwjs_urlbase=file://C:\\Users\\Administrator\\Downloads\\ --save
```

## 实现的核心代码

#### 1) start.js
``` javascript
// Run the app
  var closed=false;
  var nwDev = exec(nwPath + ' ' + rootPath+"\\nw_config\\", { cwd: rootPath+"\\nw_config\\" }, function(err, stdout, stderr) {
    console.log(err)
    process.exit(0)
    closed = true
  })

  nwDev.stdout.on('data', console.log)
  nwDev.stdout.on('error', console.error)

  // 退出时也关闭 NW 进程
  process.on('exit', exitHandle)
  process.on('uncaughtException', exitHandle)

  function exitHandle(e) {
    if (!closed) nwDev.kill()
    console.log(e || '233333, bye~~~')
  }
```

#### 2) build.js
``` javascript
var tmpJson=require("../nw_config/package-build.json");
var baseJson=require("../package.json");
tmpJson=Object.assign({},baseJson,tmpJson)
fs.writeFileSync(path.relative(process.cwd(), paths.appBuild)+"/package.json", JSON.stringify(tmpJson, null, '  '), 'utf-8')
```