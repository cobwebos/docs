---
title: "Azure AD v2 ASP.NET Web 服务器获取已启动 – 简介 |Microsoft 文档"
description: "与传统 web 基于浏览器应用程序使用 OpenID Connect 标准在 ASP.NET 解决方案上实现 Microsoft 登录"
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: 8062923b6270ec6253dc231a3db4333cf4666b42
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="add-sign-in-with-microsoft-to-an-aspnet-web-app"></a>在使用 Microsoft 登录添加到 ASP.NET web 应用程序

本指南演示如何使用 Microsoft ASP.NET MVC 解决方案使用传统的 web 浏览器基于的应用程序使用 OpenID Connect 实现登录。 

在本指南结束时，你的应用程序将能够接受的登录单元的个人帐户 （包括 outlook.com、 live.com 和其他） 以及工作和学校帐户从任何公司或组织与 Azure Active Directory 集成。 

> 本指南需要 Visual Studio 2015 Update 3 或 Visual Studio 2017。  没有它？  [免费下载 Visual Studio 2017](https://www.visualstudio.com/downloads/)

## <a name="how-this-guide-works"></a>本指南的工作原理

![本指南的工作原理](media/active-directory-serversidewebapp-aspnetwebappowin-intro/aspnetbrowsergeneral.png)

本指南基于浏览器访问 ASP.NET web 站点的位置的方案请求用户通过登录按钮进行身份验证。 在此方案中，大部分工作来呈现网页出现服务器端。

## <a name="libraries"></a>库

本指南使用以下库：

|库|描述|
|---|---|
|[Microsoft.Owin.Security.OpenIdConnect](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/)|可让应用程序使用 OpenIdConnect 进行身份验证中间件|
|[Microsoft.Owin.Security.Cookies](https://www.nuget.org/packages/Microsoft.Owin.Security.Cookies)|可让应用程序维护用户会话使用 cookie 的中间件|
|[Microsoft.Owin.Host.SystemWeb](https://www.nuget.org/packages/Microsoft.Owin.Host.SystemWeb)|使基于 OWIN 的应用程序可以在使用 ASP.NET 请求管道的 IIS 上运行|

