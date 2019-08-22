---
title: ASP.NET Core 2 学习笔记：(1) 开始
date: 2019-02-05 20:55:17
category: [Backend]
tags: [ASP.NET Core MVC]
---



最近几年.NET Core发展比较迅速，似乎要取代.NET Framework，ASP.NET也随之发布.NET Core版本。虽然名称沿用ASP.NET，但相对于ASP.NET确实有许多架构上的差异，可以说除了名称外，已然是两个不同的框架。



#### 前言

要开发.NET Core必须要安装.NET Core SDK，所以先到官网下载.NET Core SDK的安装文件，[请到官网下载](https://www.microsoft.com/net)。

.NET Core作为跨平台的框架，不再像 .NET Framework 要依附在 Windows系统才能运行，所以你可以按照你需要的版本进行下载及安装。

安装完成后，可以通过 .NET Core CLI(Command-Line Interface)确认.NET Core SDK安装的版本，指令如下：

~~~bash
dotnet --version
~~~



#### 建立网站

先建立一个项目文件夹 CoreWebsite，然后在该文件夹中执行.NET Core CLI 创建网站的指令：

![](/resource/image/ASPNETCORE2-LearninNotes-1-Beging/corenewweb.png)



.NET Core CLI 会在该文件夹，创建一个空的 ASP.NET Core 模板，內容如下：

![](/resource/image/ASPNETCORE2-LearninNotes-1-Beging/corenewwebfolder.png)



~~~c
obj/                            # 项目暂存目录
wwwroot/                        # 网站根目录 (空的)
MyWebsite.csproj                # 项目文件
Program.cs                      # 入口
Startup.cs                      # 网站的相关设置
~~~



#### 启动网站

创建完成后，就可以用 .NET Core CLI 启动网站了。启动网站指令：

~~~bash
dotnet run
~~~



.NET Core CLI 默认会启动一个`http://localhost:5000/`的站点，用浏览器打开此链接就可以看到 ASP.NET Core 网站了。如下：

![](/resource/image/ASPNETCORE2-LearninNotes-1-Beging/dotnetrunweb.png)



#### 用VS Code进行开发

.NET Core 都已经跨平台了，开发工具当然也不能局限于 Visual Studio IDE (Visual Studio 2019/2017/2015 等)。基本上纯文字编辑器搭配 .NET Core CLI 就可以开发 ASP.NET Core 了，但沒有断点调试或 Autocomplete 开发有些辛苦。如果是 Windows系統，最推荐的当然还是 Visual Studio IDE，再来就是 Visual Studio Code (VS Code)。

VS Code是一套可安裝插件的文字编辑器，同时支持 Windows、Mac 及 Linux 版本，即轻量又免费。
只要安装增强插件就变成了IDE，并且支持多种编程语言。[下载位置](https://code.visualstudio.com/)。



#### 安装插件

打开VS Code 可以在左边看到五个Icon，点击最下面的那么Extensions图标，并在Extensions搜索框中输入**C#**,便可以找到**C#**插件安装。如下图：

![](/resource/image/ASPNETCORE2-LearninNotes-1-Beging/vscodeextension.png)



#### 打开项目

VS Code 和一般的文字编辑器有些不同，它是以文件夹为工作区域，打开一个目录，就等于打开了一个项目。从上方工具栏中**文件**->**打开文件夹** 选择ASP.NET Core项目目录，大概隔几秒后，VS Code 会提示是否要帮此项目加入Build/Debug的设置。如下图：

![](/resource/image/ASPNETCORE2-LearninNotes-1-Beging/vscodeprogram.png)



#### Build/Debug设置

如果沒有自动提示加入 Build/Debug 设置，可以在左边 Icon，点击倒数第二個 Debug 图标，手动加入 Build/Debug 设置（**添加配置**）。如下步骤：

![](/resource/image/ASPNETCORE2-LearninNotes-1-Beging/addbuild.png)

![](/resource/image/ASPNETCORE2-LearninNotes-1-Beging/addbuild1.png)



设置完成后，VS Code 会自动创建 *.vscode* 目录及设置文件 *launch.json*、*tasks.json*。目录结构如下如下：

~~~bash
vscode/                         # VS Code 配置目录
  launch.json                   # 用 VS Code 启动项目的设置
  tasks.json                    # 定义 launch.json 会用到的指令
obj/                            # 项目暂存目录
wwwroot/                        # 网站根目录 (空的)
MyWebsite.csproj                # 项目文件
Program.cs                      # 入口
Startup.cs                      # 网站的相关设置
~~~



#### 断点调试

调试方式有两种：

1.右击**调试**->**启动调试**，如下图：

![](/resource/image/ASPNETCORE2-LearninNotes-1-Beging/debug1.png)

2.点击VS Code左侧第四个调试图标，然后点击绿色小三角，如下图：

![](/resource/image/ASPNETCORE2-LearninNotes-1-Beging/debug2.png)



当执行到该断点后，就会停下來，并在 Debug 侧栏显示当前变量的状态等，也可以用鼠标移到变量上查看变量的內容。如下：

![](/resource/image/ASPNETCORE2-LearninNotes-1-Beging/debug3.png)



调试方式跟大部分的IDE都差不多，可以Step over、Step in/out 等。
如此一来就可以用 VS Code 轻松开发 ASP.NET Core了。



