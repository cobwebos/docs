---
title: "Azure AD v2 Windows 桌面获取已启动 – 简介 |Microsoft 文档"
description: "如何 Windows 桌面.NET (XAML) 应用程序可以调用一个 API，由 Azure Active Directory v2 终结点需要访问令牌"
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
ms.openlocfilehash: 4a695c00fce4deb02261ba58ec95469746bb1486
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="call-the-microsoft-graph-api-from-a-windows-desktop-app"></a>从 Windows 桌面应用中调用 Microsoft Graph API

本指南演示如何获取访问令牌和 Microsoft Graph API 或其他需要从 Azure Active Directory v2 终结点的访问令牌的 Api 调用本机 Windows 桌面.NET (XAML) 应用程序。

在本指南结束时，你的应用程序将能够调用受保护的 API （包括 outlook.com、 live.com 和其他） 的个人帐户使用以及工作和学校从任何公司或组织有 Azure Active Directory 的帐户。  

> 本指南需要 Visual Studio 2015 Update 3 或 Visual Studio 2017。  没有它？ [免费下载 Visual Studio 2017](https://www.visualstudio.com/downloads/)

### <a name="how-this-guide-works"></a>本指南的工作原理

![本指南的工作原理](media/active-directory-mobileanddesktopapp-windowsdesktop-intro/windesktophowitworks.png)

本指南所创建的示例应用程序可以查询 Microsoft Graph API 或 Web API 接受令牌从 Azure Active Directory v2 终结点的 Windows 桌面应用程序。 对于此方案中，令牌添加到通过 Authorization 标头的 HTTP 请求。 令牌获取和续订处理通过 Microsoft 身份验证库 (MSAL)。


### <a name="handling-token-acquisition-for-accessing-protected-web-apis"></a>处理用于访问令牌获取受保护的 Web Api

用户进行身份验证后，示例应用程序将收到一个可用于查询 Microsoft Graph API 或 Microsoft Azure Active Directory v2 所保护的 Web API 的令牌。

例如，Microsoft Graph Api 都需要访问令牌以允许访问特定资源 – 例如，若要读取用户的配置文件，访问用户的日历或发送一封电子邮件。 你的应用程序可以请求使用 MSAL 通过指定 API 作用域中访问这些资源的访问令牌。 然后，此访问令牌将添加到针对受保护资源发出的每个调用的 HTTP 授权标头。 

MSAL 管理缓存和为你刷新访问令牌，因此不需要你的应用程序。


### <a name="nuget-packages"></a>NuGet 包

本指南使用以下 NuGet 包：

|库|描述|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Microsoft 身份验证库 (MSAL)|

