---
title: "Azure AD v2 Android 获取已启动 – 简介 |Microsoft 文档"
description: "如何对 Android 应用程序可以获取访问令牌并调用 Microsoft Graph API 或 Api 需要从 Azure Active Directory v2 终结点的访问令牌"
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
ms.openlocfilehash: d933781713456f73aa76db557fdf35672dfb2a29
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="call-the-microsoft-graph-api-from-an-android-app"></a>从 Android 应用程序调用 Microsoft Graph API

本指南演示如何获取访问令牌和 Microsoft Graph API 或其他需要从 Azure Active Directory v2 终结点的访问令牌的 Api 调用本机 Android 应用程序。

在本指南结束时，你的应用程序将能够调用受保护的 API （包括 outlook.com、 live.com 和其他） 的个人帐户使用以及工作和学校从任何公司或组织有 Azure Active Directory 的帐户。  

### <a name="how-this-sample-works"></a>此示例的工作原理
![此示例的工作原理](media/active-directory-mobileanddesktopapp-android-intro/android-intro.png)

创建的本指南的示例基于 Android 应用程序用于查询接受从 Azure Active Directory v2 终结点 – 在此情况下，Microsoft Graph API 的令牌的 Web API 的方案。 对于此方案中，令牌添加到通过 Authorization 标头的 HTTP 请求。 令牌获取和续订处理通过 Microsoft 身份验证库 (MSAL)。

### <a name="pre-requisites"></a>先决条件
* 此指导安装程序将重心放在 Android Studio 中，但也可接受任何其他 Android 应用程序开发环境。 
* 21 或更高版本的 android SDK 是必需的 （建议使用 SDK 25）。
* Google Chrome 或 web 浏览器使用自定义选项卡是适用于 Android 的此版本的 Microsoft 身份验证库 (MSAL) 必需的。

> 注意： Google Chrome 未包含在 Visual Studio 仿真程序上适用于 Android。 我们建议你要测试此代码的仿真程序上使用 API 25 或者的图像与 API 21 或更高版本使用 Google Chrome 安装程序。


### <a name="how-to-handle-token-acquisition-to-access-a-protected-web-api"></a>如何处理访问受保护的 Web API 的令牌获取

用户进行身份验证后，示例应用程序将收到一个可用于查询 Microsoft Graph API 或 Microsoft Azure Active Directory v2 所保护的 Web API 的令牌。

例如，Microsoft Graph Api 都需要访问令牌以允许访问特定资源 – 例如，若要读取用户的配置文件，访问用户的日历或发送一封电子邮件。 你的应用程序可以请求使用 MSAL 通过指定 API 作用域中访问这些资源的访问令牌。 然后，此访问令牌将添加到针对受保护资源发出的每个调用的 HTTP 授权标头。 

MSAL 管理缓存和为你刷新访问令牌，因此不需要你的应用程序。

### <a name="libraries"></a>库

本指南使用以下库：

|库|描述|
|---|---|
|[com.microsoft.identity.client](http://javadoc.io/doc/com.microsoft.identity.client/msal)|Microsoft 身份验证库 (MSAL)|
