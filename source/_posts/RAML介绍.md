---
title: RAML介绍
date: 2016-11-24 11:04:26
tags:
---


## RAML是什么
>**Write once. Use many. Creative laziness encouraged**  
>RESTful API Modeling Language (RAML) makes it easy to manage the whole API lifecycle from design to sharing. It's concise - you only write what you need to define - and reusable. It is machine readable API design that is actually human friendly.


 RAML的全称是RESTful API Modeling Language，这是一种用来描述基于Restful架构的API（设计API）的语言。它的语法规范是基于YAML的新规范，因此机器与人类都能够轻易地理解其中的内容。  
 
 但是，仅仅简单地创建一个原型是不够的。MuleSoft创建了一种API契约设计周期图，这一设计的前提在于API不仅仅是机器之间的一种契约，同样也是提供商与用户之间的一种契约。

 ![](http://cdn4.infoqstatic.com/statics_s1_20161122-0331/resource/articles/power-of-raml/zh/resources/0923001.png)
 
 在API设计后，可以对外提供模拟的API调用，这样就能从API使用者那得到具体反馈。这些有价值的反馈能够让公司找到设计中的缺陷，例如数据或结构中的不一致性，以及API中的一些令人困惑之处。在这一阶段及时发现设计中的问题非常关键，因为一旦你的API发布之后需要修改，那么在大多数情况下都会破坏向后兼容性，而这将影响API的使用。

 这里就不对RAML的具体语法做说明了，在我的另一篇文章《[raml语法简单介绍](https://eternityrain.github.io/2016/11/24/raml%E8%AF%AD%E6%B3%95%E7%AE%80%E5%8D%95%E4%BB%8B%E7%BB%8D/)》中对语法有相关介绍。

## 为什么用RAML
 RAML的目的不仅仅在于创建更易于理解的规范（你可以将这一工作指派给文档团队，他们会做得更好）而已。RAML的设计者Uri Sarid希望使用者能够打破固有的思维，在开始编写代码之前以一种全新的方式对API进行建模。  
 Roy Fielding博士在他的博客Untangled中指出，现在的人们总有一种倾向，他们只考虑短期的设计，目光只能看到我们所知的、我们目前所想的内容。

 REST风格的发明者Roy Fielding表示：“很不幸，人们很善于处理短期的设计，但对于长期的设计通常就束手无策了。”  

 设计API的一大挑战在于，API通常都会存在较长的一段时间，有可能会存在数年之久。毕竟，API的设计需要开发者投入大量的精力，但同样对于客户来说也是一种极大的投入，因为客户也依赖于这些API，需要基于它们进行开发。从2009年起，Uri开始思考如何解决API的长期设计的挑战，他希望能够找到一种工具，让设计者只需几行代码就能够对API进行建模（或设计），然后快速地生成一个原型，让全球的开发者都能够尝试你的原型并提供反馈。在2013年，随着RAML 0.8的发布，他的梦想终于变成了现实。

 自那时起，人们对于RAML的兴趣就在不断地升温。无论是大型企业还是小公司，他们都开始意识到了RAML所带来的益处：包括以一种人类可读的格式设计API、在设计时就看到这些API将怎样工作、并且能够为API创建一个活动的、全功能的示例，让开发者能够简单地通过点击按键，对这个API发起实际的调用。
 
## 周边生态
 - DESIGN  
对于基于RAML的API设计，通常使用API Workbench或API Designer作为设计平台。它们都是由mulesoft开发的，前者作为Atom的插件使用，后者是单独的一个设计平台。

 - BUILD  
设计好的API可以由多种方式进行build： NodeJS,JAVA,NET,Python,Mule,IOT  
这里我只简单介我们常用的两种。对于JAVA来说，通常我们使用RAML for JAX-RS进行build，简单来说就是把设计好的RAML转换成[JAX-RS规范](https://eternityrain.github.io/2016/09/27/JAX-RS%E8%A7%84%E8%8C%83%E7%9A%84%E7%89%88%E6%9C%AC.%E5%AE%9E%E7%8E%B0.jar%E5%AF%B9%E5%BA%94%E5%85%B3%E7%B3%BB/)的java代码，用于后续的deploy。还有一种是mule的APIkit,它基于anypoint studio进行raml翻译及build。

 - TEST  
我们可以用多数的第三方进行测试，如API Fortress, API Science, Parasoft, Postman, SmartBear。

 - Document  
形成API文档，我们不再需要花大量的时间。运用API Console，RAML to HTML我们即可以根据RAML自动化动态的生成文档。

 - SHARE  
我们可以简单的根据RESTful API为Java,.NET,PHP,Ruby等主流语言生成SDK，也可以使用API Notebook体验全新的API交互方式。API Notebook完全改变了开发人员与API交互的方式，让他们可以使用基本的JavaScript轻松地尝试调用，操作数据，甚至连接到多个API。

**Raml提供了全新的RESTful API设计方式，围绕其周边衍生出若干方便快捷的解决方案。无论是从思维、交互性还是效率上来说，它都是api设计上的一次成功的进步**