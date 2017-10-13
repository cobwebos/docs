---
title: "Azure AD v2 Android 入门 - 简介 | Microsoft Docs"
description: "Android 应用如何从 Azure Active Directory v2 终结点获取访问令牌并调用 Microsoft Graph API 或需要访问令牌的 API"
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
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="call-the-microsoft-graph-api-from-an-android-app"></a>从 Android 应用调用 Microsoft Graph API

本指南演示本机 Android 应用程序如何从 Azure Active Directory v2 终结点获取访问令牌并调用 Microsoft Graph API 或其他需要访问令牌的 API。

在本指南结束时，应用程序将能使用个人帐户（包括 outlook.com、live.com 和其他帐户）以及具有 Azure Active Directory 的任何公司或组织的工作和学校帐户调用受保护的 API。  

### <a name="how-this-sample-works"></a>此示例的工作原理
![此示例的工作原理](media/active-directory-mobileanddesktopapp-android-intro/android-intro.png)

本指南创建的示例基于：使用 Android 应用程序来查询接受 Azure Active Directory v2 终结点令牌的 Web API，即 Microsoft Graph API。 在此方案中，通过 Authorization 标头向 HTTP 请求添加了令牌。 由 Microsoft 身份验证库 (MSAL) 获取和更新令牌。

### <a name="pre-requisites"></a>先决条件
* 本指导式设置面向 Android Studio，但也适用任何其他 Android 应用程序开发环境。 
* 需要 Android SDK 21 或更新版本（建议使用 SDK 25）。
* 此版本的 Android Microsoft 身份验证库 (MSAL) 需要使用了自定义选项卡的 Google Chrome 或 Web 浏览器。

> 注意：Android Visual Studio 模拟器中不包括 Google Chrome。 对于测试此代码，建议在已安装 Google Chrome 的 API 25 版模拟器或 API 21 版（或更新版本）映像上进行。


### <a name="how-to-handle-token-acquisition-to-access-a-protected-web-api"></a>如何获取用于访问受保护 Web API 的令牌

用户进行身份验证后，示例应用程序将收到一个令牌，可用于查询受 Microsoft Azure Active Directory v2 保护的 Microsoft Graph API 或 Web API。

Microsoft Graph 等 API 需要访问令牌才能允许访问特定资源 - 例如读取用户的个人资料、访问用户的日历或发送电子邮件。 应用程序可通过指定 API 作用域来请求使用 MSAL 的访问令牌，从而访问这些资源。 此访问令牌随后将添加到 HTTP Authorization 标头，在每次调用受保护资源时使用。 

MSAL 负责管理缓存和刷新访问令牌，因此应用程序无需执行这些任务。

### <a name="libraries"></a>库

本指南使用以下库：

|库|说明|
|---|---|
|[com.microsoft.identity.client](http://javadoc.io/doc/com.microsoft.identity.client/msal)|Microsoft 身份验证库 (MSAL)|
