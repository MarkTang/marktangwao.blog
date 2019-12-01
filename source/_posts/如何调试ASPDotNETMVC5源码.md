---
title: 如何调试ASP.NET MVC5源码
date: 2019-08-08 21:40
category: [Backend]
tags: [ASP.NET MVC]
---



最近在研究ASP.NET MVC5的源码，于是在想，既然提供了源码，那我们如何进入源码调试？在网上找了一些调试的方法，试了几个都不行，于是折腾了两三个小时，终于弄出来了，下面看看我的操作步骤。

<!--more-->



#### 一、准备工作。本机安装的是VS2017，如图

![](/resource/image/如何调试ASPDotNETMVC5源码/VSVersion.png 'VSVersion')



#### 二、有两种方式可以调试源码

1.直接在源码的解决方案下新建一个ASP.NET MVC5应用程序 。

2.新建一个单独的ASP.NET MVC5解决方案 。

两种方案我都尝试了，不过在这里我就演示方法2。



#### 三、新建MVC解决方案，并获取公钥值

在MVC解决方案的根目录下的Web.config，获取System.Web.Mvc的publicKeyToken；

如下图所示：

![](/resource/image/如何调试ASPDotNETMVC5源码/publicKeyToken.png)



#### 四、重新注册公钥值。



用VS2017自带的命令行工具注册公钥值了，命令是：sn.exe -Vr *,31BF3856AD364E35

![](/resource/image/如何调试ASPDotNETMVC5源码/registeredpublickeyvalue.png)

看到这样的命令就是注册成功了。



#### 六、引用源码中相应的DLL

先将新建的解决方案中默认的System.Web.Mvc dll移除掉，如下图所示：

System.Web.Mvc dll默认引用路径：

![System.Web.Mvc dll默认引用路径](/resource/image/如何调试ASPDotNETMVC5源码/defaultmvcref.png)



移除System.Web.Mvc dll：

![移除System.Web.Mvc dll](/resource/image/如何调试ASPDotNETMVC5源码/removemvcref.png)



以添加项目的方式引用新的System.Web.Mvc dll，如下图所示：

![](/resource/image/如何调试ASPDotNETMVC5源码/addmvccsproj.png)



#### 七、到这里我们就可以调式代码了

因为ASP.NET MVC是运用了管道模型，也就是这个MvcHandler类处理请求，并通过httpmodule将结果返回给客户端。

我们在Global.asax里面的Application_Start加入断点，在MvcHandler的类中开始的位置加入断点，如下图。

![](/resource/image/如何调试ASPDotNETMVC5源码/MvcApplication.png)

![](/resource/image/如何调试ASPDotNETMVC5源码/MvcHandler.png)

![](/resource/image/如何调试ASPDotNETMVC5源码/MvcHandler.BeginProcessRequest.png)



到这里，我们已经成功的进入了源码调试了。



**总结：上面的做法是新建一个解决方案，然后调用dll。**

**那么在源码的解决方案下新建一个项目，具体修改方法也是如上操作。**



