---
title: XMLHttpRequest（XHR）详解
date: 2016-11-30 17:45:48
tags: XMLHttpRequest
---

## 引言

本文的阅读者，在阅读这篇文章时你需要拥有html，js，http的相关知识，这样你可以更好的理解我们所谈论的话题，因为我们并不会在文中对它们进行讲解。

## 简介

### 解释定义
XMLHttpRequest是一组API函数集，存在于浏览器的JS内核中，因此它可以被JavaScript这样web浏览器内嵌的脚本语言调用，通过HTTP在浏览器和web服务器之间收发XML或其它数据。


除XML之外，XMLHttpRequest还能用于获取其它格式的数据，如JSON或者甚至纯文本。

### 背景知识

XMLHttp最初是由微软公司发明的，在Internet Explorer 5.0中用作ActiveX对象，可通过JavaScript、VBScript或其它浏览器支持的脚本语言访问。Mozilla的开发人员后来在Mozilla 1.0中实现了一个兼容的版本。之后苹果电脑公司在Safari 1.2中开始支持XMLHttpRequest，而Opera从8.0版开始也宣布支持XMLHttpRequest。
大多数使用了XMLHttpRequest的设计良好的网页，会使用简单的JavaScript函数（ajax）将不同浏览器之间调用XMLHttpRequest的差异性屏蔽，该函数会自动检测浏览器版本并隐藏不同环境的差异。

## 特点

1. XMLHttpRequest最大的好处在于可以动态地更新网页，它无需重新从服务器读取整个网页，也不需要安装额外的插件。该技术被许多网站使用，以实现快速响应的动态网页应用。例如：Google的Gmail服务、Google Suggest动态查找界面以及Google Map地理信息服务。
2. 上一个特点我称之为异步更新，还有一个特点，我把它叫做异步提交（当然也支持同步提交了）。这个特性的异步针对的是服务端，也就是说可以异步请求服务端，这样不会因为服务端响应时间过长而让页面出现假死情况。

## 运行机制

[这里](http://blog.csdn.net/github_35758702/article/details/53410654)有一篇文章介绍了JavaScript的运行机制。

通过其中如下片段可以发现XMLHttpRequest的实现原理：

> 单线程就意味着，所有任务需要排队，前一个任务结束，才会执行后一个任务。如果前一个任务耗时很长，后一个任务就不得不一直等着。
如果排队是因为计算量大，CPU忙不过来，倒也算了，但是很多时候CPU是闲着的，因为IO设备（输入输出设备）很慢（比如Ajax操作从网络读取数据），不得不等着结果出来，再往下执行。
JavaScript语言的设计者意识到，这时主线程完全可以不管IO设备，挂起处于等待中的任务，先运行排在后面的任务。等到IO设备返回了结果，再回过头，把挂起的任务继续执行下去。
于是，所有任务可以分成两种，一种是同步任务（synchronous），另一种是异步任务（asynchronous）。同步任务指的是，在主线程上排队执行的任务，只有前一个任务执行完毕，才能执行后一个任务；异步任务指的是，不进入主线程、而进入"任务队列"（task queue）的任务，只有"任务队列"通知主线程，某个异步任务可以执行了，该任务才会进入主线程执行。
具体来说，异步执行的运行机制如下：（同步执行也是如此，因为它可以被视为没有异步任务的异步执行。）

>（1）所有同步任务都在主线程上执行，形成一个执行栈（execution context stack）。
>（2）主线程之外，还存在一个"任务队列"（task queue）。只要异步任务有了运行结果，就在"任务队列"之中放置一个事件。
>（3）一旦"执行栈"中的所有同步任务执行完毕，系统就会读取"任务队列"，看看里面有哪些事件。那些对应的异步任务，于>是结束等待状态，进入执行栈，开始执行。
>（4）主线程不断重复上面的第三步。

>下图就是主线程和任务队列的示意图。
>
>![enter image description here](http://image.beekka.com/blog/2014/bg2014100801.jpg)
>
>只要主线程空了，就会去读取"任务队列"，这就是JavaScript的运行机制。这个过程会不断重复。

>![enter image description here](http://image.beekka.com/blog/2014/bg2014100802.png)
>
>上图中，主线程运行的时候，产生堆（heap）和栈（stack），栈中的代码调用各种外部API，它们在"任务队列"中加入各种事件（click，load，done）。只要栈中的代码执行完毕，主线程就会去读取"任务队列"，依次执行那些事件所对应的回调函数。

## 代码

执行栈中的代码（同步任务），总是在读取"任务队列"（异步任务）之前执行。请看下面这个例子。

    var req = new XMLHttpRequest();
    req.open('GET', url);    
    req.onload = function (){};    
    req.onerror = function (){};    
    req.send();

上面代码中的req.send方法是Ajax操作向服务器发送数据，它是一个异步任务，意味着只有当前脚本的所有代码执行完，系统才会去读取"任务队列"。所以，它与下面的写法等价。

    var req = new XMLHttpRequest();
    req.open('GET', url);
    req.send();
    req.onload = function (){};    
    req.onerror = function (){};  

也就是说，指定回调函数的部分（onload和onerror），在send()方法的前面或后面无关紧要，因为它们属于执行栈的一部分，系统总是执行完它们，才会去读取"任务队列"。

## Ajax

Ajax是基于XMLHttpRequest实现的，前面提到过，不同浏览器之间调用XMLHttpRequest会有差异性，如下：

**Internet Explorer**

	xmlhttp_request = new ActiveXObject(“Msxml2.XMLHTTP.3.0″); //3.0或4.0, 5.0
	xmlhttp_request = new ActiveXObject(“Msxml2.XMLHTTP”);
	xmlhttp_request = new ActiveXObject(“Microsoft.XMLHTTP”);

**Mozilla﹑Netscape﹑Safari**

	xmlhttp_request = new XMLHttpRequest();

下面是Ajax源码：

	var xmlHttp
	
	function showHint(str)
	{
	
	  if (str.length==0)
	    { 
	    document.getElementById("txtHint").innerHTML="";
	    return;
	    }
	
	  xmlHttp=GetXmlHttpObject()
	
	  if (xmlHttp==null)
	    {
	    alert ("您的浏览器不支持AJAX！");
	    return;
	    }
	
	  var url="gethint.asp";
	  url=url+"?q="+str;
	  url=url+"&sid="+Math.random();
	  xmlHttp.onreadystatechange=stateChanged;
	  xmlHttp.open("GET",url,true);
	  xmlHttp.send(null);
	} 
	
	function stateChanged() 
	{ 
	if (xmlHttp.readyState==4)
	{ 
	document.getElementById("txtHint").innerHTML=xmlHttp.responseText;
	}
	}
	
	function GetXmlHttpObject()
	{
	  var xmlHttp=null;
	  try
	    {
	    // Firefox, Opera 8.0+, Safari
	    xmlHttp=new XMLHttpRequest();
	    }
	  catch (e)
	    {
	    // Internet Explorer
	    try
	      {
	      xmlHttp=new ActiveXObject("Msxml2.XMLHTTP");
	      }
	    catch (e)
	      {
	      xmlHttp=new ActiveXObject("Microsoft.XMLHTTP");
	      }
	    }
	return xmlHttp;
	}



可以看到，Ajax只是对XMLHttpRequest进行了封装，将不同浏览器之间调用XMLHttpRequest的差异性对外屏蔽。