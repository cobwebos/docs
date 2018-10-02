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
ms.date: 09/13/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: a1cd25012461ae8bb445dcb1de8fe5be49e04760
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2018
ms.locfileid: "47060715"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-from-an-android-app"></a>从 Android 应用将用户登录并调用 Microsoft Graph

在本教程中，你将了解如何构建 Android 应用程序并将其集成到 Microsoft 标识平台中。 具体而言，此应用会将用户登录，获取用于调用 Microsoft Graph API 的访问令牌，并针对 Microsoft Graph API 发出基本请求。  

完成本指南后，应用程序将接受个人 Microsoft 帐户（包括 outlook.com、live.com 和其他帐户）进行登录，还能够接受使用 Azure Active Directory 的任何公司或组织的工作或学校帐户进行登录。 

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>本指南生成的示例应用的工作原理
![此示例的工作原理](media/active-directory-develop-guidedsetup-android-intro/android-intro.png)

此示例中的应用会将用户登录并代表他们获取数据。  将通过一个远程 API（在本例中为 Microsoft Graph API）访问该数据，该 API 要求授权并且还受 Microsoft 标识平台保护。 

更具体地说， 
* 你的应用将启动一个用于让用户登录的网页。
* 将为你的应用颁发 Microsoft Graph API 的一个访问令牌。
* 该访问令牌将包括在对 Web API 的 HTTP 请求中。
* 处理 Microsoft Graph 响应。 

此示例使用 Android 的 Microsoft 身份验证库 (MSAL) 来协调以及帮助进行授权。MSAL 将自动续订令牌，在设备上的其他应用之间提供 SSO，帮助管理帐户，以及处理大多数条件访问案例。 

## <a name="prerequisites"></a>先决条件
* 此指导式设置使用的是 Android Studio 3.0。 
* 必须使用 Android 21 或更高版本（建议使用 25+）。
* 对于此版本的 Android MSAL，必须使用 Google Chrome 或使用采用了自定义选项卡 Web 浏览器。

## <a name="library"></a>库

本指南使用以下身份验证库：

|库|说明|
|---|---|
|[com.microsoft.identity.client](http://javadoc.io/doc/com.microsoft.identity.client/msal)|Microsoft 身份验证库 (MSAL)|
