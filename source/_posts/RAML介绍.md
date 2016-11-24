---
title: RAML介绍
date: 2016-11-24 11:04:26
tags:
---


## RAML是什么
>**Write once. Use many. Creative laziness encouraged**
>RESTful API Modeling Language (RAML) makes it easy to manage the whole API lifecycle from design to sharing. It's concise - you only write what you need to define - and reusable. It is machine readable API design that is actually human friendly.


 RAML是一种语言，可以使用RAML描述基于Restful架构的API

## RAML可以做什么
 RAML定义出的文件可以通过MuleSoft提供的RAML for Jax-RS插件，生成基于JAX-RS协议的Java代码。同时Java代码也可以通过此插件生成相对应的RAML文件。
  
## 方式
- Maven 插件
- eclipse插件
- 命令行

### Maven插件
#### RAML to JAX-RS
---
这个Maven plug-in用来生成JAX-RS协议的接口，支持基于单一或多个RAML文件

##### Maven 组件
Maven 组件仓库地址
- https://repository-master.mulesoft.org/releases/ - release repository
- https://repository-master.mulesoft.org/snapshots/ - snaphots repository

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

##### 配置中的Schema
| Schema      |     Default Value |   Optional   |
| :-------- | --------:| :------: |
| sourceDirectory    |   field2 |  yes  |
| basePackageName    |   field2 |  yes  |
| jaxrsVersion    |   field2 |  yes  |
| useJsr303Annotations    |   field2 |  yes  |
| jsonMapper    |   field2 |  yes  |
| extensions    |   field2 |  yes  |
| customAnnotator    |   field2 |  yes  |

当你运行`mvn raml:generate`时，RAML定义的内容就会被处理生成相应的Java代码，同样如果你配置了相应的executions那么在运行`mvn compile` or `mvn package`这个插件也会执行。
