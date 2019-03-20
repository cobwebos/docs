---
title: include 文件
description: include 文件
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/20/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 5a1ff10901d10fb274a6fa1418f04e1f20113cb7
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2019
ms.locfileid: "58203573"
---
# <a name="add-sign-in-with-microsoft-to-an-aspnet-web-app"></a>向 ASP.NET Web 应用添加 Microsoft 登录功能

本指南演示如何通过基于传统 Web 浏览器的使用 OpenID Connect 的应用程序，使用 ASP.NET MVC 解决方案实现 Microsoft 登录。

在本指南结束时，应用程序将能接受使用个人帐户（包括 outlook.com、live.com 和其他帐户）以及与 Azure Active Directory 集成的任何公司或组织的工作和学校帐户进行登录。

> 本指南需要 Visual Studio 2015 Update 3 或 Visual Studio 2017。  尚未安装？  [免费下载 Visual Studio 2017](https://www.visualstudio.com/downloads/)

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>本指南生成的示例应用的工作原理

![演示如何按本教程适用生成示例应用](media/active-directory-develop-guidedsetup-aspnetwebapp-intro/aspnetbrowsergeneral-updated.png)

本指南创建的示例应用程序基于这样的场景：用户使用浏览器访问 ASP.NET 网站，该网站要求用户通过登录按钮进行身份验证。 在此方案中，呈现网页的大部分工作在服务器端完成。

## <a name="libraries"></a>库

本指南使用以下库：

|库|描述|
|---|---|
|[Microsoft.Owin.Security.OpenIdConnect](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/)|让应用程序可使用 OpenIdConnect 进行身份验证的中间件|
|[Microsoft.Owin.Security.Cookies](https://www.nuget.org/packages/Microsoft.Owin.Security.Cookies)|允许应用程序使用 Cookie 维持用户会话的中间件|
|[Microsoft.Owin.Host.SystemWeb](https://www.nuget.org/packages/Microsoft.Owin.Host.SystemWeb)|允许基于 OWIN 的应用程序使用 ASP.NET 请求管道在 IIS 上运行|
