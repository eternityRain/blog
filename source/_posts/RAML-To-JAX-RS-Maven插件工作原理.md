---
title: RAML-To-JAX-RS_Maven插件工作原理
date: 2017-05-16 17:29:14
tags:
---

## 前言

这篇关于raml to jax-rs 插件的文章旨在分析该插件的工作原理和对其部分源码进行解析，
如果你还不认识它，可以从[《raml-for-jax-rs插件》](https://eternityrain.github.io/2016/11/24/raml-for-jax-rs%E6%8F%92%E4%BB%B6/)解它并学习它的使用方法。

## 工作原理

raml to jax-rs 插件完成了从 raml -> java 的转换，这其中包括raml的解析工作以及java代码的生成工作。整个工作需要依赖三个主要的部分：
- [raml-java-parser](https://github.com/raml-org/raml-java-parser/tree/0.8.12)
- [raml-jaxrs-codegen-core](https://github.com/mulesoft-labs/raml-for-jax-rs/tree/v1.3.4/raml-to-jaxrs/core)
- [ram-jaxrs-maven-plugin](https://github.com/mulesoft-labs/raml-for-jax-rs/tree/v1.3.4/raml-to-jaxrs/maven-plugin)

其中后面两部分都属于[raml-for-jax-rs](https://github.com/mulesoft-labs/raml-for-jax-rs)项目中的子模块，由mulesoft开发，第一部分由raml-org组织开发。

### raml-java-parser
#### 功能
解析并验证raml

#### 运行机制
说到运行机制，先上一个raml的例子：

    #%RAML 0.8
    title: Contacts App
    version: v1
    schemas:
      - tree: !include schema/tree.json
      - apiCreatedEntity: |
          {  "$schema": "http://json-schema.org/draft-03/schema",
             "type": "object",
             "description": "Response JSON for successful POST operation",
             "properties": {
               "id":  { "type": "string", "required": true }
             }
          }
      - apiContact: !include schema/api-contact.json
    
    /contacts:
      # note, changing the displayName will change the name of the generated class in ${base.package.name}.resource
      displayName: Contacts
      /{id}:
        get:
          description: Retrieves specific contact by id
          responses:
            200:
              body:
                application/json:
                   schema: tree
            400:
              description: Bad Request
            500:
              description: Internal Server Error

raml语法是在yaml的语法规范之上又定义了一套针对REST的规范，所以解析raml就分为yaml解析和raml解析两部分。
如果解析上面的raml，会有以下几步：

1. 验证yaml语法，并将raml转化为树形结构对象；
2. 树形对象进入raml验证阶段，程序以广度优先遍历的方式层层获取树形的node，同时依据已有的配置对该树对象进行验证，这里的配置不止一类：
     - 定义每种不同的node下允许出现的子node类型来控制raml语言中key的范围，如：title是必须有的标签；resouce 可以有get,post等action，但是不能在其下定义 "ben" 这样的action；
     - 定义node对应的handler可以控制raml中key对应的value的范围，如：body下面的mimeType可以是"application/json"，"application/xml"，"application/x-www-form-urlencoded"，"multipart/form-data"这几种；
     - 定义node对应的rule可以控制value的规则，如：baseUri标签对应BaseUriRule，该rule中通过正则规范了value必须符合URI结构。

通过这两步可以完全验证raml文件是否符合raml规范。

### raml-jaxrs-codegen-core
#### 功能
集成`raml-java-parser`对raml进行解析并验证，转换raml对象为java，这里提到一点，生成java model并写成java文件是由sun的 JCodeModel 工具来实现的。
#### 运行机制：
生成过程中主要用到的几个类有 Context , AbstractGenerator , Generator , ClientGenerator , Configuration:
- `Configuration` 用来hold生成过程中的参数；
- `Context` 持有整个过程中 Configuration, Raml object，JCodeModel 实例 ，SchemaMapper；
- `AbstractGenerator` ： `AbstractGenerator`是`Generator`和`ClientGenerator`的模板类，提供`addResourceMethod`方法供它们实现，因为生成interface method的逻辑，Generator 和 ClientGenerator是不一样的。其他的功能比如生成interface，添加参数，标记注解，配置验证等公用部分在`AbstractGenerator`里直接提供。

生成过程在以上几个类的配合下进行：验证raml对象、解析json schema并生成java model、依据configuration 决定解析resource并生成interface model的方式、将mode写成java文件。

### ram-jaxrs-maven-plugin
#### 功能
提供raml-to-jaxrs 的maven插件功能，对外暴露raml生成java过程中可配置的参数，将控制权交由使用者。

#### 运行机制
这里就是比较薄的一层了，仅仅是将用户配置的参数转变为 core 中的 `Configuration`对象，然后先去验证raml文件头部的`"#%RAML **"`行是否存在，接下来根据`configuration`调用`Generator`或 `ClientGenerator`的`run()`方法。

## 研究源码后的一些想法

基于以上的研究结果，现在可以根据需要，自由生成特定的java代码，同时也可以自定义raml新的语法规则。