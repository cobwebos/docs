---
title: include 文件
description: include 文件
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: ios
ms.workload: identity
ms.date: 03/20/2019
ms.author: dadobali
ms.custom: include file
ms.openlocfilehash: 971ae8cd44f1b345d3a71b8fa4f256c8f25ef961
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/11/2019
ms.locfileid: "59503011"
---
# <a name="call-the-microsoft-graph-api-from-an-ios-application"></a>从 iOS 应用程序调用 Microsoft Graph API

本指南演示本机 iOS 应用程序 (Swift) 如何调用需要 Microsoft 标识平台终结点访问令牌的 API。 本指南说明如何获取访问令牌并将其用于对 Microsoft Graph API 和其他 API 的调用。

完成本指南中的练习后，你的应用程序可以从任何具有 Azure AD 的公司或组织调用受保护的 API。 应用程序可以使用个人帐户（如 outlook.com、live.com 等）以及工作或学校帐户调用受保护的 API。

## <a name="prerequisites"></a>先决条件

- XCode 版本 10.x 是在本指南中创建的示例所必需的。 可从 [iTunes 网站](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12 "XCode 下载 URL") 下载 XCode。
- [Carthage](https://github.com/Carthage/Carthage) 依赖项管理器是包管理所必需的。

## <a name="how-this-guide-works"></a>本指南的工作原理

![演示本教程生成的示例应用的工作原理](media/active-directory-develop-guidedsetup-ios-introduction/iosintro.svg)

在本指南中，示例应用程序允许 iOS 应用程序查询从 Microsoft 标识平台终结点接受的令牌的 Microsoft 图形 API 或 Web API。 在此方案中，通过使用 **Authorization** 标头向 HTTP 请求添加了令牌。 由 Microsoft 身份验证库 (MSAL) 获取和更新令牌。

### <a name="handle-token-acquisition-for-access-to-protected-web-apis"></a>负责获得用于访问受保护 Web API 的令牌

用户进行身份验证后，示例应用程序将收到一个令牌。 该令牌用于查询受 Microsoft 标识平台终结点保护的 Microsoft Graph API 或 Web API。

API（如 Microsoft Graph）需要访问令牌以允许访问特定资源。 需要使用令牌读取用户的配置文件、访问用户的日历、发送电子邮件，等等。 应用程序可通过使用 MSAL 和指定 API 范围来请求访问令牌。 对于针对受保护资源发出的每个调用，将访问令牌添加到 HTTP **Authorization** 标头。

MSAL 负责管理缓存和刷新访问令牌，因此应用程序无需执行这些任务。

## <a name="libraries"></a>库

本指南使用以下库：

|库|说明|
|---|---|
|[MSAL.framework](https://github.com/AzureAD/microsoft-authentication-library-for-objc)|适用于 iOS 的 Microsoft 身份验证库预览|
