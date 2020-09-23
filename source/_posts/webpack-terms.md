---
title: webpack【名词术语】
date: 2020-08-29 00:00:00
tags: webpack
---

### webpack【名词术语】

```javascript
{
  	entry: {
    		index: ["./index.js","./src/index.js"],
      	entry: ["./src/entry.js"]
  	}
}
```

##### 一、名词术语

1.【**modules**】：`"./index.js"`、`"./src/index.js"` and `"./src/entry.js"` ( any other modules that are dependencies of these entry points)

2.【**Chunks**】：index、entry

3.【**Bundles**】：index、entry

**备注**：如果配置中添加了source maps，split css等，意味着chunks:bundles = 1:2；

 

4.mode：

webpack运行时还会根据mode设置一个全局变量process.env.NODE_ENV,这里的process.env.NODE_ENV不是node中的环境变量,而是webpack.DefinePlugin中定义的全局变量,允许你根据不同的环境执行不同的代码.

