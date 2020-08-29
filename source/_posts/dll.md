---
title: webpack【dll】
date: 2020-08-30 00:00:00
tags: webpack
---

### webpack【dll】

#### 一、前言

##### 1.名词解释

dll（Dynamic Link Library）动态链接库，常见在window系统中，当执行某一个程序时，相应的DLL文件就会被调用。

将更改不频繁的代码进行单独编译。这将改善引用程序的编译速度，但是它增加了构建过程的复杂性。

##### 2.为什么使用dll

- 把复用性较高的第三方模块打包到动态链接库中，在不升级这些库的情况下，动态库不需要重新打包，每次构建只重新打包业务代码；
- 亦可将同类资源各自打包第三方，例如react家族、lodash工具库等；

##### 3.如何使用dll

使用`DLLPlugin` 和 `DLLReferencePlugin` 实现了拆分 bundles，同时还大大提升了构建的速度；使用 `AddAssetHtmlPlugin`插件将打包后的 dll bundles插入到页面中；

Github webpack给出了具体的demo和讲解，[戳](https://github.com/webpack/webpack/tree/master/examples/dll-user)

###### 3.1 DLLPlugin

新建webpack.dll.js配置文件，plugin参数配置如下：

```javascript
const LIBRARY = '[name]_[hash]'
output: {
        filename: `${LIBRARY}.dll.js`,
        path: path.join(__dirname, 'dist'),
        library: LIBRARY,
},
plugins: [
        new webpack.DllPlugin({
            // manifest json 文件的绝对路径 (输出文件)
            path: path.join(__dirname, 'dist', '[name]-manifest.json'),
            // 暴露出的 DLL 的函数名
            name: LIBRARY,
        }),
    ],
}
```

运行产出

- *.dll.js：文件里是使用数组保存的模块，索引值就作为id；

- *.manifest.json：文件里，是用来描述对应的dll文件里保存的模块；

  ```json
  {
      "name": "vendors_b5543b7c65236c1de403",
      "content": {
          "./node_modules/@redux-saga/core/dist/io-6de156f3.js": {
              "id": 0,
              "buildMeta": {
                  "exportsType": "namespace",
                  "providedExports": []
              }
          }
      }
  }
  ```

  

###### 3.2 DLLReferencePlugin

在webpack.common.js配置文件中，添加

```JavaScript
 new webpack.DllReferencePlugin({
       context: __dirname,
       // eslint-disable-next-line global-require
       manifest: require('./dist/vendors-manifest.json'),
}),

```

DllReferencePlugin去 manifest.json 文件读取 name 字段的值，把值的内容作为在从全局变量中获取动态链接库中内容时的全局变量名；因此，在 webpack.dll.js中，DllPlugin 中的 name 参数必须和 output.library 中保持一致。

###### 3.3 AddAssetHtmlPlugin

讲打包后的dll插入到入口html中

```javascript
new AddAssetHtmlPlugin({
			filepath: path.resolve(__dirname, 'dist', '*.dll.js'),
}),
```

