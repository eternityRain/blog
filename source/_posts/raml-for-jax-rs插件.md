---
title: raml-for-jax-rs插件
date: 2016-11-24 16:20:51
tags:
---

## 简介

raml-for-jax-rs插件是mulesoft开发的，是实现被JAX-RS注解的java代码与Raml Api描述之间相互转换的工具。
其中包含jaxrs-to-raml和raml-to-jaxrs两个主要工程，分别实现一种转换过程。
  
## 设计原则
### RAML生成JAX-RS
所有发行版本（Eclipse Plugin，Maven Plugin和Jar）的工作方式如下：

- 生成接口，并在RAML定义改变的情况下重新生成接口
- 一个接口被生成一个顶级resource，该接口下面的子接口称为sub-resouce被作为不同的方法定义在同一个接口下
- 为每个资源操作创建响应对象包装器，以便指导实现者仅生成与RAML定义兼容的结果
- 为不属于JAX-RS核心规范的http方法生成自定义注解（PATCH）
- 对象基于代表 请求/响应 实体的json schemas来生成
- 英语是接口和方法中常用的语言

#### 当前支持
- JAX-RS 1.1 和 2.0
- 除`@Pattern`外，JSR-303注解都支持，因为Raml用的是ECMA 262/Perl 5模式，而javax.validation用的是Java模式，`@Min/@Max`支持对raml中定义的非小数最大最小值的限制
- 模型对象基于JSON schemas生成，带有Jackson1,2或者Gson注解
- 基于XML Schemas生成带有JAXB注解的结果

#### JAX-RS注解
- Path
- Consumes, Produces.
- QueryParam, FormParam, PathParam, HeaderParam.
- DELETE, GET, HEAD, OPTIONS, POST, PUT.
- DefaultValue.

#### 扩展注解

验证注解：  

- NotNull
- Min
- DecimalMin
- Max
- DecimalMax

## 支持方式

该插件支持以下几种使用方式

- Maven 插件
- eclipse插件
- 命令行


### Maven插件

这里我们着重对Raml to JAX-RS方式中Maven插件的使用进行介绍

#### RAML to JAX-RS

这个Maven插件用来基于raml Api生成JAX-RS注释的java接口，支持基于单一或多个RAML文件

##### Maven 组件
Maven 组件仓库地址  
- [https://repository-master.mulesoft.org/releases/](https://repository-master.mulesoft.org/releases/) - release repository  
- [https://repository-master.mulesoft.org/snapshots/](https://repository-master.mulesoft.org/snapshots/) - snaphots repository  

##### 使用方式

需要在Project中的Pom文件中增加以下plug-in内容， 例如：

    <plugin>
    <groupId>org.raml.plugins</groupId>
    <artifactId>raml-jaxrs-maven-plugin</artifactId>
    <version>1.3.4</version>
    <configuration>
        <!-- Use sourcePaths if you want to provide a single RAML file or a list of RAML files -->
        <sourceDirectory>${basedir}/raml</sourceDirectory>
        <!-- Optionally configure outputDirectory if you don't like the default value: ${project.build.directory}/generated-sources/raml-JAX-RS -->
        <!-- Replace with your package name -->
        <basePackageName>com.acme.api</basePackageName>
        <!-- Valid values: 1.1 2.0 -->
        <jaxrsVersion>2.0</jaxrsVersion>
        <useJsr303Annotations>false</useJsr303Annotations>
        <!-- Valid values: jackson1 jackson2 gson none -->
        <jsonMapper>jackson2</jsonMapper>
        <removeOldOutput>true</removeOldOutput>
        <!-- Optionally set extensions to a list of fully qualified names of classes
        that implement org.raml.jaxrs.codegen.core.ext.GeneratorExtension -->
        <!-- for example:
        <extensions>
            <param>com.abc.AuthorizationAnnotationExtension</param>
            <param>com.abc.ParameterFilterExtension</param>
        </extensions>
        Custom annotator for json schema to pojo convertor
        <customAnnotator>com.abc.MyCustomAnnotator</customAnnotator>
        -->
    </configuration>
    <executions>
        <execution>
            <goals>
                <goal>generate</goal>
            </goals>
            <phase>generate-sources</phase>
        </execution>
    </executions>
	</plugin>

##### 配置说明

| Schema               |     Mean          |   Optional              |
| :--------            | --------------    | ------                  |
| sourceDirectory      |   raml文件目录     |  custom                 |
| sourcePaths          |   raml文件路径集合  | custom                  |
| outputDirectory      |   输出目录         | custom                  |
| basePackageName      |   生成的基础包路径  |  custom                 |
| removeOldOutput      |   是否删除上一次的输出| boolean                |
| jaxrsVersion         |   JAX-RS规范版本   |1.1 or 2.0               |
| useJsr303Annotations | 是否使用JSR303规范        |  boolean                |
| jsonMapper           | json与java映射规范        | jackson1 jackson2 gson none  |
| extensions           |  扩展对Resource的生成控制  |  custom  |
| customAnnotator      |   json schema到pojo的定制化注解器 |  custom  |
| jsonMapperConfiguration | 对于json映射的配置 | "generateBuilders","includeHashcodeAndEquals","includeToString","useLongIntegers" |
|       skip           | 跳过插件的执行 | boolean |
|    modelPackageName  | 设置model对象所在的目录名称(basepackage+modelpackage,如果schema中已经指定了javapackage，则此配置不生效) | custom | 
| generateClientProxy  | 是否生成客户端代理(只生成proxy，覆盖model和resource)  | boolean |
| mapToVoid            | true:如果resource定义的方法的response没有body，生成的java method返回值将会是void，不是wrapper response | boolean |
|useTitlePropertyForSchemaNames |将title属性作为schema的名称(没试出来) | boolean |
| asyncResourceTrait            | 【待定】 | |


运行`mvn raml:generate`时，RAML定义的内容就会被处理生成相应的Java代码，同样在运行`mvn compile` 或者 `mvn package`命令时这个插件也会执行。
