---
title: cookie【path】
date: 2020-09-28 00:00:00
tags: cookie

---

### cookie【path】

##### 一、浏览器对cookie的设置规则

1. 当cookie的path设置了值不为null的时候，以设置的值为准
2. 当cookie的path为null时候，获取请求的URI的path值 
   1. 当URI的path值是以“/”结尾的时候，直接设置为cookie的path值
   2. 当URI的path值不是以“/”结尾的时候，查看path里面是否有“/” 
      1. 如果有“/”的话，直接截取到最后一个“/”，然后设置为cookie的path值。
      2. 如果没有“/”的话，将cookie的path设置为”/”

##### 二、基于规则的Demo说明

A保存cookie：http://localhost:8080/day06-2/Servlets/A

B保存cookie：http://localhost:8080/day06-2/Servlets/B

访问C时的路径如下：http://localhost:8080/day06-2/Servlets/C【会带B不带A】

访问D时的路径如下：http://localhost:8080/day06-2/Servlets/D 【会带A也带B】

##### 三、总结

当你访问的资源路径包含了哪一个cookie的路径时，就会在请求的时候附带哪一个cookie；

但是当你访问的资源包含了儿子的路径时，那么必须把父亲的cookie带上；

当你访问的路径只包含了父亲的路径时，就只带父亲的路径，不带儿子的；