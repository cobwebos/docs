       <properties title="Getting Started with Mobile Services" pageTitle="" metaKeywords="Azure, Getting Started, Mobile Services" description="" services="mobile-services" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="mobile-services" ms.workload="web" ms.tgt_pltfrm="vs-what-happened" ms.devlang="na" ms.topic="article" ms.date="10/8/2014" ms.author="ghogen, kempb" />

> [AZURE.SELECTOR]
> - [入门](/documentation/articles/vs-mobile-services-dotnet-getting-started/)
> - [发生了什么情况](/documentation/articles/vs-mobile-services-dotnet-what-happened/)

###<span id="whathappened">我的项目发生了什么情况？</span>

#####已添加引用

Azure 移动服务 NuGet 包已添加到你的项目。因此，以下 .NET 引用也已添加到你的项目：

- Microsoft.WindowsAzure.Mobile
- Microsoft.WindowsAzure.Mobile.Ext
- Newtonsoft.Json
- System.Net.Http.Extensions
- System.Net.Http.Primitives 

#####移动服务的连接字符串值

在 App.xaml.cs 文件中，已使用选定移动服务的应用程序 URL 和应用程序密钥创建 **MobileServiceClient** 对象。 

[详细了解移动服务](http://azure.microsoft.com/documentation/services/mobile-services/)
