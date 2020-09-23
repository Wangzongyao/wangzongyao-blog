---
title: webpack【快速刷新fast-refresh】
date: 2020-08-29 00:00:00
tags: webpack
---

### What Is Fast Refresh?

### 什么是快速刷新？

It's a reimplementation of "hot reloading" with full support from React. It's originally [shipping for React Native](https://twitter.com/dan_abramov/status/1169687758849400832) but most of the implementation is platform-independent. The plan is to use it across the board — as a replacement for purely userland solutions (like `react-hot-loader`).

这是一个重新实施的“热重载”与全面支持的反应。它最初是为 React Native 提供的，但是大多数实现都是独立于平台的。该公司计划全面使用它，作为纯用户界面解决方案(比如 react-hot-loader)的替代品。

快速刷新源自于TS，详细概念[戳](https://reactnative.cn/docs/next/fast-refresh)

### Can I Use Fast Refresh on the Web?

### 我可以在网上使用快速刷新吗？

Theoretically, yes, that's the plan. Practically, someone needs to integrate it with bundlers common on the web (e.g. Webpack, Parcel). I haven't gotten around to doing that yet. Maybe someone wants to pick it up. This comment is a rough guide for how you’d do it.

理论上来说，是的，这就是计划。实际上，有人需要将它与网络上常见的捆绑机(如 Webpack、 Parcel)进行整合。我还没有抽出时间来做这件事。也许有人想把它捡起来。这个评论是一个粗略的指南，告诉你如何去做。

### What Does It Consist Of?

### 它由什么组成？

Fast Refresh relies on several pieces working together:

快速刷新依赖于几个部分一起工作:

- "Hot module replacement" mechanism in the module system.

   

  模块系统中的“热模块替换”机制

  - That is usually also provided by the bundler. 这通常也是由捆绑机提供的
  - E.g. in webpack, 例如在 webpack 中,`module.hot` API lets you do this. aPI 允许你这样做

- React renderer 16.9.0+ (e.g. React DOM 16.9)

   

  反应渲染器16.9.0 + (例如反应 DOM 16.9)

  - Or 或者`react-reconciler@0.21.0` or higher for custom renderers 或更高的定制渲染器

- `react-refresh/runtime` entry point 入口点

- `react-refresh/babel` Babel plugin 巴别塔插件

You'll probably want to work on the integration part. I.e. integrating `react-refresh/runtime` with Webpack "hot module replacement" mechanism.

您可能希望处理集成部分，即将 react-refresh/runtime 与 Webpack“热模块替换”机制集成。

### What's Integration Looking Like?

### 整合是什么样的？

**⚠️⚠️⚠️ TO BE CLEAR, THIS IS A GUIDE FOR PEOPLE WHO WANT TO IMPLEMENT THE INTEGRATION THEMSELVES. THIS IS NOT A GUIDE FOR BEGINNERS OR PEOPLE WHO WANT TO START USING FAST REFRESH IN THEIR APPS. PROCEED AT YOUR OWN CAUTION! ⚠️⚠️⚠️**

Something something something 要明确，这是一个指南的人谁想要实施一体化自己。这不是一个指导初学者或人谁想开始使用快速刷新在他们的应用程序。按照你自己的谨慎行事！Something something something something

There are a few things you want to do minimally:

以下是一些你最低限度需要做的事情:

- Enable HMR in your bundler (e.g. webpack) 在捆绑器中启用 HMR (例如 webpack)
- Ensure React is 16.9.0+ 确保反应是16.9.0 +
- Add 添加`react-refresh/babel` to your Babel plugins 你的 Babel 插件

At that point your app should crash. It should contain calls to `$RefreshReg$` and `$RefreshSig$` functions which are undefined.

这时你的应用程序应该崩溃了，它应该包含对 $RefreshReg $和 $RefreshSig $函数的调用，这两个函数没有定义。

Then you need to create a new JS entry point which *must run before any code in your app*, including `react-dom` (!) This is important; if it runs after `react-dom`, nothing will work. That entry point should do something like this:

然后您需要创建一个新的 JS 入口点，该入口点必须在应用程序中的任何代码(包括 react-dom (!))之前运行这一点很重要; 如果它在反应之后运行，那么什么都不会起作用。这个切入点应该是这样的:

```
if (process.env.NODE_ENV !== 'production' && typeof window !== 'undefined') {
  const runtime = require('react-refresh/runtime');
  runtime.injectIntoGlobalHook(window);
  window.$RefreshReg$ = () => {};
  window.$RefreshSig$ = () => type => type;
}
```

This should fix the crashes. But it still won't do anything because these `$RefreshReg$` and `$RefreshSig$` implementations are noops. Hooking them up is the meat of the integration work you need to do.

这应该可以修复系统崩溃。但是它仍然不能做任何事情，因为这些 $RefreshReg $和 $RefreshSig $实现都是 noops。将它们连接起来是您需要完成的集成工作的主要部分。

How you do that depends on your bundler. I suppose with webpack you could write a loader that adds some code before and after every module executes. Or maybe there's some hook to inject something into the module template. Regardless, what you want to achieve is that every module looks like this:

如何做到这一点取决于你的捆绑机。我认为使用 webpack，您可以编写一个加载程序，在每个模块执行之前和之后添加一些代码。或者可能有一些钩子将某些内容注入模块模板。不管怎样，你想要实现的是每个模块都是这样的:

```
// BEFORE EVERY MODULE EXECUTES

var prevRefreshReg = window.$RefreshReg$;
var prevRefreshSig = window.$RefreshSig$;
var RefreshRuntime = require('react-refresh/runtime');

window.$RefreshReg$ = (type, id) => {
  // Note module.id is webpack-specific, this may vary in other bundlers
  const fullId = module.id + ' ' + id;
  RefreshRuntime.register(type, fullId);
}
window.$RefreshSig$ = RefreshRuntime.createSignatureFunctionForTransform;

try {

  // !!!
  // ...ACTUAL MODULE SOURCE CODE...
  // !!!

} finally {
  window.$RefreshReg$ = prevRefreshReg;
  window.$RefreshSig$ = prevRefreshSig;
}
```

The idea here is that our Babel plugin emits calls to this functions, and then our integration above ties those calls to the module ID. So that the runtime receives strings like `"path/to/Button.js Button"` when a component is being registered. (Or, in webpack's case, IDs would be numbers.) Don't forget that both Babel transform and this wrapping must *only* occur in development mode.

这里的想法是我们的 Babel 插件发出对这个函数的调用，然后我们上面的集成将这些调用绑定到模块 ID。因此，运行时接收类似“ path/to/Button”的字符串。当一个组件正在注册时。(或者，在 webpack 的例子中，id 是数字。)不要忘记 Babel 转换和这种封装只能在开发模式下进行。

As alternative to wrapping the module code, maybe there's some way to add a try/finally like this around the place where the bundler actually initializes the module factory. Like we do [here](https://github.com/facebook/metro/blob/febdba2383113c88296c61e28e4ef6a7f4939fda/packages/metro/src/lib/polyfills/require.js#L355-L379) in Metro (RN bundler). **This would probably be better** because we wouldn't need to bloat up every module, or worry about introducing illegal syntax, e.g. when wrapping `import` with in `try / finally`.

作为包装模块代码的替代方法，也许可以在实际初始化模块工厂的位置附近添加一个 try/finally，如下所示。就像我们在麦德龙(RN bundler)做的一样。这可能会更好，因为我们不需要增加每个模块，或者担心引入非法语法，例如在使用 try/finally 包装 import 时。

Once you hook this up, you have one last problem. Your bundler doesn't know that you're handling the updates, so it probably reloads the page anyway. You need to tell it not to. This is again bundler-specific, but the approach I suggest is to check whether *all of the exports are React components*, and in that case, "accept" the update. In webpack it could look like something:

一旦你把它连接起来，你就有了最后一个问题。您的捆绑器不知道您正在处理更新，所以它可能会重新加载页面。你需要告诉它不要这样做。这仍然是特定于捆绑器的，但我建议的方法是检查所有出口是否都是 React 组件，在这种情况下，“接受”更新。在 webpack 中，它可以看起来像这样:

```
// ...ALL MODULE CODE...

const myExports = module.exports; 
// Note: I think with ES6 exports you might also have to look at .__proto__, at least in webpack

if (isReactRefreshBoundary(myExports)) {
  module.hot.accept(); // Depends on your bundler
  enqueueUpdate();
}
```

What is `isReactRefreshBoundary`? It's a thing that enumerates over exports shallowly and determines whether it only exports React components. That's how you decide whether to accept an update or not. I didn't copy paste it here but [this implementation](https://github.com/facebook/metro/blob/febdba2383113c88296c61e28e4ef6a7f4939fda/packages/metro/src/lib/polyfills/require.js#L748-L774) could be a good start. (In that code, `Refresh` refers to `react-refresh/runtime` export).

什么是 reactrefreshboundary？这是一个简单列举出口并决定是否只出口反应元件的事情。这就是你决定是否接受更新的方式。我没有复制粘贴它在这里，但这个实现可以是一个很好的开始。(在该代码中，Refresh 指的是 react-Refresh/runtime export)。

You'll also want to [manually register all exports](https://github.com/codesandbox/codesandbox-client/commit/b3633367c18b0ac664fd2c4419e8e1c76333b890) because Babel transform will only call `$RefreshReg$` for functions. If you don't do this, updates to classes won't be detected.

您还需要手动注册所有导出，因为 Babel 转换只会为函数调用 $RefreshReg $。如果不这样做，就不会检测到类的更新。

Finally, the `enqueueUpdate()` function would be something shared between modules that debounces and performs the actual React update.

```
const runtime = require('react-refresh/runtime');

let enqueueUpdate = debounce(runtime.performReactRefresh, 30);
```

By this point you should have something working.

### Nuances

### 细微差别

There are some baseline experience expectations that I care about that go into "Fast Refresh" branding. It should be able to gracefully recover from a syntax error, a module initialization error, or a rendering error. I won't go into these mechanisms in detail, but I feel very strongly that you shouldn't call your experiment "Fast Refresh" until it handle those cases well.

Unfortunately, I don't know if webpack can support all of those, but we can ask for help if you get to a somewhat working state but then get stuck. For example, I've noticed that webpack's `accept()` API makes error recovery more difficult (you need to accept a *previous* version of the module after an error), but there's a way to hack around that. Another thing we'll need to get back to is to automatically "register" all exports, and not just the ones found by the Babel plugin. For now, let's ignore this, but if you have something that works for e.g. webpack, I can look at polishing it.

Similarly, we'd need to integrate it with an "error box" experience, similar to `react-error-overlay` we have in Create React App. That has some nuance, like the error box should disappear when you fix an error. That also takes some further work we can do once the foundation's in place.

Let me know if you have any questions!



翻译链接：https://github.com/facebook/react/issues/16604#issuecomment-528663101

