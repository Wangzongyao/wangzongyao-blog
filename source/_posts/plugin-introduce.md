---
title: webpack【plugin-introduce】
date: 2020-08-30 00:00:00
tags: webpack
---

### webpack【plugin-introduce】

#### 一、前言

一句话概括plugin的使用场景和作用

##### 二、Summary

【[htmlWebpackPlugin](https://www.npmjs.com/package/html-webpack-plugin)】：根据模板生成html文件，提供页面配置项。

【[clean-webpack-plugin](https://www.npmjs.com/package/clean-webpack-plugin)】：清理文件夹。

【[mini-css-extract-plugin](https://www.npmjs.com/package/mini-css-extract-plugin#extracting-css-based-on-entry)】：将CSS提取到单独的文件中。它为每个包含CSS的JS文件创建一个CSS文件。它支持CSS和SourceMap的按需加载。不建议在开发环境使用，尤其是启用HMR时，配合fast refresh插件【[react-refresh-webpack-plugin](https://www.npmjs.com/package/@pmmmwh/react-refresh-webpack-plugin)】CSS样式更新失效。

【[optimize-css-assets-webpack-plugin](https://www.npmjs.com/package/optimize-css-assets-webpack-plugin)】：在Webpack构建期间搜索CSS资产，并优化\最小化CSS（默认情况下，它使用[cssnano，](http://github.com/ben-eb/cssnano)但可以指定自定义CSS处理器）。

【provide-plugin】：自动加载模块，系统找不到变量时，会自动识别该插件提供的变量ID对应的依赖包或者变量。

【exports-loader】：使用该插件导出一个全局变量（期望用户使用的变量），例如：



```javascript
{
    test: require.resolve('globals.js'), // 导出的文件
    use: 'exports-loader?file,parse=helpers.parse'  // 导出的变量
}
```

