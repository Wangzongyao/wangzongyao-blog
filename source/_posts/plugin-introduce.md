---
title: webpack【plugin-introduce】
date: 2020-08-30 00:00:00
tags: webpack
---

### webpack【plugin-introduce】

#### 一、前言

一句话概括plugin的使用场景和作用

##### 二、Summary

【[mini-css-extract-plugin](https://www.npmjs.com/package/mini-css-extract-plugin#extracting-css-based-on-entry)】：将CSS提取到单独的文件中。它为每个包含CSS的JS文件创建一个CSS文件。它支持CSS和SourceMap的按需加载。

【[optimize-css-assets-webpack-plugin](https://www.npmjs.com/package/optimize-css-assets-webpack-plugin)】：在Webpack构建期间搜索CSS资产，并优化\最小化CSS（默认情况下，它使用[cssnano，](http://github.com/ben-eb/cssnano)但可以指定自定义CSS处理器）。

