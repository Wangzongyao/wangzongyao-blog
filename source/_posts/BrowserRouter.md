---
title: react-router：browserRouter与hashRouter
date: 2020-08-30 00:00:00
tags: react router
---

### react-router【browserRouter与hashRouter】

#### 一、前言

构建react项目中，控制路由跳转使用react-router，react-router是以[history](https://github.com/ReactTraining/history/blob/master/docs/getting-started.md)为基础的

- A "**browser history**" is for use in modern web browsers that support the [HTML5 history API](http://diveintohtml5.info/history.html) (see [cross-browser compatibility](http://caniuse.com/#feat=history))
- A "**hash history**" is for use in web browsers where you want to store the location in the [hash](https://developer.mozilla.org/en-US/docs/Web/API/HTMLHyperlinkElementUtils/hash) portion of the current URL <u>to avoid sending it to the server when the page reloads</u>
- A "**memory history**" is used as a reference implementation that may be used <u>in non-browser environments</u>, like [React Native](https://facebook.github.io/react-native/) or tests

由于hash值变化不会导致浏览器向服务器发出请求，而且hash改变会触发hashchange事件，浏览器的进后退也能对其进行控制，所以人们在 html5 的 history 出现前，基本都是使用 hash 来实现前端路由的

##### 备注

1.浏览器history对象是window对象的一部分，可通过 window.history 属性对其进行访问，其操作的是浏览器的**session history**，即当前回话窗口的history。这是H5提供新的特新，也是以上依赖包的基础；

2.hash值变化不会导致浏览器向服务器发出请求，且hash改变会触发hashchange事件，浏览器的进后退也能对其进行控制。在 html5 的 history 出现前，基本都是使用 hash 来实现前端路由的；

3.history依赖包提供了history.listen(myListener)方法，以供使用监听路由变化的逻辑；

4.关于使用browserRouter时，页面404的解释：没有配置historyApiFallback，即页面没有初始化路由与组件的联系（[connect-history-api-fallback](https://www.npmjs.com/package/connect-history-api-fallback)），导致的该路由没有可渲染的组件。可通过webpack配置`devServer：{historyApiFallback: true}`解决；

#### 二、browser与hash对比说明

##### 1.hashRouter

使用 URL 中的 hash（#）部分去创建路由

##### 2.browserRouter

使用浏览器中的 History API 用于处理 URL，因此在browserHistory 模式事，URL 是指向**服务器真实 URL 的资源路径**！

