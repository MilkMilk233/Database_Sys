## CSC3170 Project 技术方案

维护者：

Zhixin Chen, zhixinchen@link.cuhk.edu.cn

last update:2022/3/30

[TOC]

##### 后端组件全表：

| 名称              | 功能                                                         |
| ----------------- | ------------------------------------------------------------ |
| Django框架        | 轻量级Web框架，实现业务逻辑                                  |
| nginx服务(待实现) | 把静态文件与动态端口分开处理，静态文件（数据、图片等资源）实现了缓存，设定一定过期时间，减轻uwsgi的压力，让uwsgi更专注地处理动态请求，nginx处理静态文件访问 |
| postgresql 服务   | 数据库，存储用户信息和其他信息                               |
| uwsgi服务         | uwsgi是一个web服务器，django (实现业务逻辑) 就搭载在其中，uwsgi可以开启多进程和多线程，大幅提升了django服务的并发量（因为web服务很多是I/O密集型，相比于计算密集型，多线程能大幅提高并发量，在我们的项目中，约为7倍） |

##### API全表

服务器地址：10.20.12.91

上面的表格介绍了所有运行的服务，但在用户看来，他们可以看到的是 API。在服务和 API 中间，我们用一些 network 把组件和API联系起来了。下面介绍 API。

| 地址         | 功能     | 连接方式                                       | 可见性   |
| ------------ | -------- | ---------------------------------------------- | -------- |
| :8000/hello/ | 测试界面 | Django搭载在uwsgi上，uwsgi通过uwsgiweb连接外网 | 外网可见 |
|              |          |                                                |          |
|              |          |                                                |          |

##### 技术细节：

Demo：

[[CSC3170 Web测试](https://laxdata.cuhk.edu.cn/hello/)](https://laxdata.cuhk.edu.cn/hello/)



##### 待完成部分：

- 前端页面(html/css/js)
- 数据库与逻辑实现（Mysql/PostgreSQL, prefer latter)
