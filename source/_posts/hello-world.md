---
title: webpack：可替换模板字符串[name]/[id]/...等变量名释义
date: 2020-08-29 00:00:00
tags: webpack
---
### webpack【**可替换模板字符串**】

#### 一、前言

在webpack中，常遇到[name]/[hash]/[id]等占位符，这些占位符多是用来解决**多个入口起点**(entry point)、**代码拆分**(code splitting)或各种插件(plugin)创建**多个 bundle**时的名称或者lib。

webpack中通过内置的[TemplatedPathPlugin](https://github.com/webpack/webpack/blob/master/lib/TemplatedPathPlugin.js)实现了占位符的替换处理工作。

#### 二、名词解析

##### 1.[name]

```javascript
{
    entry: { index: "./index.js" },
    output: { filename: "[name].bundle.js" }
}
```

[name]一般指的是entry入口定义的输入文件key值，例如此处为index。

多个入口key分别对应各自的[name]。

##### 2.[id]

模块标识符(module identifier)。

id一般用于动态加载模块时，一次构建后，动态加载模块生成的id 。

##### 3.[hash]

模块标识符(module identifier)的 hash。

hash是有webpack是一次构建打包生成的hash。

##### 4.[chunkhash]

根据入口的每一个chunk的内容，生成独有的一份hash。webpack以js为入口，每一个入口通过配置合理的插件可以差分多个文件，例如剥离CSS等，但这些资源文件都归属一个chunk。

一旦chunk内容发生（可以理解为入口文件未做剥离前的文件构成发生改变），该chunk的hash就会发生变化。

##### 5.[contenthash]

文件内容 hash，**每个资源**生成的 hash 都是不同的。


