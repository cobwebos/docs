<properties urlDisplayName="Use the Mobile Services .NET Backend" pageTitle="使用移动服务 .NET 后端 - Azure 移动服务" metaKeywords="" description="了解 Azure 移动服务的 .NET 后端编程模型的详细信息，包括如何使用表数据、API、身份验证和计划的作业" metaCanonical="" services="" documentationCenter="Mobile" title="Use the Mobile Services .NET Backend" authors="mahender" solutions="" manager="dwrede" editor="mollybos" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="11/11/2014" ms.author="mahender" />
# 使用移动服务 .NET 后端

<div class="dev-center-tutorial-subselector"><a href="/zh-cn/documentation/articles/mobile-services-dotnet-backend-how-to-use/" title=".NET backend" class="current">.NET 后端</a> | <a href="/zh-cn/documentation/articles/mobile-services-how-to-use-server-scripts/"  title="JavaScript backend">JavaScript 后端</a></div>

本文提供有关如何在 Azure 移动服务中使用 .NET 后端的详细信息和示例。本主题分为以下部分：

+ [简介](#intro)
+ [表操作](#table-scripts)

## <a name="intro"></a>简介

借助移动服务 .NET 后端，你可以使用 [ASP.NET Web API](http://www.asp.net/web-api) 和偏好的 .NET 语言编写丰富的后端业务逻辑。移动服务将小型编程模型图面公开为 [NuGet 包](http://www.nuget.org/packages?q=%22mobile+services+.net+backend%22)，以帮助从适用于 Windows、Android、iOS 等的跨平台移动服务客户端 SDK 无缝访问你的服务。这些 API 还可确保尽可能简单地添加身份验证和推送通知支持。但是，由于大部分编程模型基于 Web API，开发人员十分熟悉，因此应能得心应手。 

## <a name="table-scripts"></a>表操作

移动服务 .NET 后端提供通用"表"抽象，代表数据库存储的基于 CRUD 的 HTTP API。此抽象可将数据存储考虑因素与业务逻辑区分开来，使移动服务以一致的基于 JSON 的有线格式公开不同的数据存储，而跨平台移动服务客户端 SDK 原本即可识别此格式。 

此编程模型的根是 [**TableController<T>**](http://msdn.microsoft.com/library/dn643359.aspx) 类，此类只是针对 CRUD 数据访问模式自定义的常规 Web API [**ApiController**](http://msdn.microsoft.com/library/system.web.http.apicontroller.aspx)。**TableController** 可以使用各种数据存储，包括 SQL（通过 [Entity Framework](http://msdn.microsoft.com/data/ef.aspx)）、[Azure 表存储](/zh-cn/documentation/services/storage/)、[MongoDB](http://www.mongodb.org)，或你自己的自定义存储。
