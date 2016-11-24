---
title: JAX-RS规范的版本.实现.jar对应关系
date: 2016-09-27 14:46:09
tags: [rest,jsr]
categories: JAX-RS
---

## 简介
---
JAX-RS: Java API for RESTful Web Services是一个Java编程语言的应用程序接口,支持按照 表象化状态转变 (REST)架构风格创建Web服务. JAX-RS使用了Java SE 5引入的Java 标注来简化Web服务客户端和服务端的开发和部署。

## 版本说明
---

### 名词解释

#### JCP:Java Community Process

  >一个开放的国际组织，主要由Java开发者以及被授权者组成，职能是发展和更新。
  >Java技术和JCP两者的原创者都是SUN计算机公司。然而，JCP已经由SUN于1995年创造Java的非正式过程，演进到如今有数百名来自世界各地Java代表成员一同监督Java发展的正式程序。
  >JCP维护的规范包括J2ME、J2SE、J2EE，XML，OSS，JAIN等。组织成员可以提交JSR（Java Specification Requests），通过特定程序以后，进入到下一版本的规范里面。
       
#### JSR:Java Specification Requests
>Java 规范提案。是指向JCP(Java Community Process)提出新增一个标准化技术规范的正式请求。任何人都可以提交JSR，以向Java平台增添新的API和服务。JSR已成为Java界的一个重要标准。
>即为一个规范代号：JSR311，JSR303等
>完整JSR列表请查看[JCP官网](https://www.jcp.org/en/jsr/all)

### 版本  
JSR311: 即代号为311的规范请求，对应JAX-RS规范，版本为 1.0  
JSR399: 即代号为399的规范请求，对应JAX-RS规范，版本为 2.0

## JAX-RS对应实现
---

### 何为实现
对于规范的实现，可以理解为面向对象语言中实现接口的概念。接口定义的即是一种规范、一种约束，由具体的模块来去完整的诠释这种规范，此为实现。  

下面是不同组织用来诠释JAX-RS规范的不同产品：

* Apache CXF，开源的Web服务框架。
* Jersey， 由Sun提供的JAX-RS的参考实现。
* RESTEasy，JBoss的实现。
* Restlet，由Jerome Louvel和Dave Pawson开发，是最早的REST框架，先于JAX-RS出现。
* Apache Wink，一个Apache软件基金会孵化器中的项目，其服务模块实现JAX-RS规范

这里讨论Jersey和RESTEasy：  
Jersey1.x：JAX-RS 1.0   
Jersey2.x：JAX-RS 2.0

RESTEasy1.x/2.x：JAX-RS 1.0  
RESTEasy3.x：JAX-RS 2.0

## Jar包区别   
----------

在JAX-RS规范的具体应用中,经常会遇见jsr311-api.jar,jsr311-api.jar,jaxrs-api.jar,jersey-core这几种包，下面对这些包的区别做了描述：

* jsr311-api：JAX-RS 1.0 的官方规范包
* javax.ws.rs-api：JAX-RS 2.0 的官方规范包  

（以上为官方标准规范包，其中包含对应的注解和一些辅助类）

* jaxrs-api：RESTEasy规范包，其中包含对应版本JAX-RS规范的内容以及自定义的异常和接口
* jersey-core：Jersey规范包，包含对应版本JAX-RS规范的内容以及部分实现，余下实现在jersey的其他包中