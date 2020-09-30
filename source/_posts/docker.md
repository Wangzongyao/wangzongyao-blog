1.容器的概念

传统容器是系统上的进程，通常具有以下三个特征：资源限制、安全约束、虚拟分离，[戳~](https://opensource.com/article/18/1/history-low-level-container-runtimes)

2.image

文件的使用

 [Docker Hub](https://hub.docker.com/) 



```js
docker run -d --name jenkins -p 8081:8080 -v /data/jenkins_home:/var/jenkins_home jenkins/jenkins:lts;
备注：
-d //启动在后台
--name //容器名字
-p //端口映射（8081：宿主主机端口，8080：容器内部端口）
-v //数据卷挂载映射（/data/jenkins_home：宿主主机目录，另外一个即是容器目录）
enkins/jenkins:lts //Jenkins镜像（最新版）
```

备注：数据卷挂载映射说明

Docker容器提供volume数据卷的方式，可以直接挂载宿主机文件或目录到容器里，可以理解为目录映射，这样就可以让所有的容器共享宿主机数据，从而只需要改变宿主机的数据源就能够影响到所有的容器数据，或者当容器意外删除的时候，宿主机的数据仍然有一份，便于容灾。

Docker不存在垃圾回收机制，所以需要手动删除，避免过多占用空间。



