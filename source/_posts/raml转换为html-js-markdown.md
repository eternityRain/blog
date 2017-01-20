---
title: raml转换为html,js,markdown
date: 2016-12-12 10:35:00
tags:
---

通过raml将接口设计完成后，我们需要提供简洁可读的直观视图和说明文档。  
这里我们通过node.js来实现转换

## Raml2html

[官方地址](https://github.com/raml2html/raml2html)

### raml版本支持

raml2html 4和更高的版本仅支持raml 1.0语法转换，对于raml 0.8语法，需要使用raml2html的3.x版本

### 安装

`npm i -g raml2html`


**NOTE:**如果是raml 0.8,执行 `npm i -g raml2html@3.0.0`

### 使用

raml2html --help  
raml2html example.raml > example.html


## Raml2md

[官方地址](https://github.com/raml2html/raml2md)

### 安装

`npm i -g raml2md`

### 使用

raml2md example.raml > example.md  
raml2md -i example.raml -o example.md

以上两种方式都可以实现

## Raml javascript 生成器

[官方地址](https://github.com/mulesoft/raml-client-generator)

### 安装

npm install raml-client-generator -g

### 使用

raml-to-client api.raml -o api-client -l javascript