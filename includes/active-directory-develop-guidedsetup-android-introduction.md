---
title: include 文件
description: include 文件
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/19/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: 23b7ca44b72b8840579f369954f41f554d4c8852
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/25/2018
ms.locfileid: "36943413"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-api-from-an-android-app"></a>从 Android 应用登录用户并调用 Microsoft Graph API

本指南演示了本机 Android 应用程序如何从 Azure Active Directory v2 终结点获取访问令牌并调用 Microsoft Graph API 或其他需要访问令牌的 API。

完成本指南后，应用程序将能够接受个人帐户（包括 outlook.com、live.com 和其他帐户）进行登录，还能够接受使用 Azure Active Directory 的任何公司或组织的工作和学校帐户进行登录。 然后，应用程序将调用由 Azure Active Directory v2 终结点保护的一个 API。  

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>本指南生成的示例应用的工作原理
![此示例的工作原理](media/active-directory-develop-guidedsetup-android-intro/android-intro.png)

根据本指南创建的示例应用程序基于以下方案：使用一个 Android 应用程序来查询某个 Web API（在本例中为 Microsoft Graph API），该 Web API 接受来自 Azure Active Directory v2 终结点的令牌。 对于此方案，应用程序通过授权标头将所需的令牌添加到 HTTP 请求。 Microsoft 身份验证库 (MSAL) 替你处理令牌获取和续订。

## <a name="prerequisites"></a>先决条件
* 本指导式设置面向 Android Studio，但也适用任何其他 Android 应用程序开发环境。 
* 需要 Android SDK 21 或更高版本（建议使用 SDK 25）。
* 对于此版本的 Android MSAL，必须使用 Google Chrome 或使用采用了自定义选项卡 Web 浏览器。

> [!NOTE]
> Android Visual Studio 模拟器中没有包括 Google Chrome。 对于测试此代码，建议在已安装 Google Chrome 的 API 25 版模拟器或 API 21 版（或更新版本）映像上进行。

## <a name="handling-token-acquisition-for-accessing-protected-web-apis"></a>负责获得用于访问受保护 Web API 的令牌

对用户进行身份验证后，示例应用程序将收到一个访问令牌，可用于查询受 Azure Active Directory v2 保护的 Microsoft Graph API 或 Web API。

诸如 Microsoft Grap 之类的 API 要求具有访问令牌才允许访问特定资源。 例如，需要具有访问令牌才能读取用户的个人资料、访问用户的日历或发送电子邮件。 应用程序可通过指定 API 作用域来使用 MSAL 请求访问令牌，从而访问这些资源。 然后对于针对受保护资源发出的每个调用，将此访问令牌添加到 HTTP 授权标头。 

MSAL 负责管理缓存和刷新访问令牌，因此应用程序无需执行这些任务。

## <a name="libraries"></a>库

本指南使用以下库：

|库|说明|
|---|---|
|[com.microsoft.identity.client](http://javadoc.io/doc/com.microsoft.identity.client/msal)|Microsoft 身份验证库 (MSAL)|
