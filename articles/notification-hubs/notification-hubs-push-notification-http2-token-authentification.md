---
title: Azure 通知中心中 APNS 的基于令牌的 (HTTP/2) 身份验证 | Microsoft Docs
description: 本主题介绍如何将新的令牌身份验证用于 APNS
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
editor: jwargo
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 11/14/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 02/13/2019
ms.openlocfilehash: 56689981d6c85c844fefbec6a4ec4aeb041dbc7f
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/15/2019
ms.locfileid: "74111939"
---
# <a name="token-based-http2-authentication-for-apns"></a>APNS 的基于令牌的 (HTTP/2) 身份验证

## <a name="overview"></a>概述

本文介绍如何将新的 APNS HTTP/2 协议用于基于令牌的身份验证。

使用新协议的关键优势包括：

* 令牌生成相对简单（与证书相比）
* 不再有过期日期 – 用户可以控制身份验证令牌及其吊销
* 有效负载现在最高可达 4 KB
* 同步反馈
* 你使用的是 Apple 的最新协议-证书仍使用二进制协议，该协议已标记为要弃用

可以通过两个步骤来执行此新机制：

* 从 Apple 开发人员帐户门户获取所需的信息。
* 用新信息配置通知中心。

通知中心现在已设置为将新的身份验证系统与 APNS 一起使用。

请注意，如果从使用 APNS 的证书凭据迁移，令牌属性将覆盖系统中的证书，但你的应用程序将继续无缝接收通知。

## <a name="obtaining-authentication-information-from-apple"></a>从 Apple 获取身份验证信息

若要启用基于令牌的身份验证，需要 Apple 开发人员帐户中的以下属性：

### <a name="key-identifier"></a>密钥标识符

可以从 Apple 开发人员帐户中 "**证书"、"标识符 & 配置文件**" 下的 "**密钥**" 页获取密钥标识符：

![](./media/notification-hubs-push-notification-http2-token-authentification/keys.png)

![](./media/notification-hubs-push-notification-http2-token-authentification/obtaining-auth-information-from-apple.png)

### <a name="application-identifier-and-application-name"></a>应用程序标识符和应用程序名称

开发人员帐户的 "**证书、标识符 & 配置文件**" 页中还提供了应用程序名称和标识符：

![](./media/notification-hubs-push-notification-http2-token-authentification/app-name.png)

### <a name="configure-via-the-net-sdk-or-the-azure-portal"></a>通过 .NET SDK 或 Azure 门户进行配置

你可以使用[最新的客户端 SDK](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs)或 Azure 门户将中心配置为使用基于令牌的身份验证。 若要在门户中启用基于令牌的身份验证，请登录到 Azure 门户，并 **> Apple （APNS）** "面板中转到通知中心的" 设置 "。 从 "**身份验证模式**" 属性选择 "**标记**"，以通过所有相关标记属性来更新中心。

![配置令牌](./media/notification-hubs-push-notification-http2-token-authentification/azure-portal-apns-settings.png)

* 输入从 Apple 开发人员帐户中检索到的属性。
* 选择应用程序模式（"**生产**" 或 "**沙盒**"）。
* 单击 "**保存**" 按钮以更新 APNS 凭据。

基于令牌的凭据由以下字段组成：

* **密钥 ID**：在 Apple 开发人员门户中生成的私钥的标识符;例如，`2USFGKSKLT`。
* **团队 ID**：也称为 "前缀" 或 "应用前缀"。 这是 Apple 开发人员门户中组织的标识符;例如，`S4V3D7CHJR`。
* **捆绑 id**：也称为 "应用 ID"。 这是应用程序的捆绑标识符;例如，`com.microsoft.nhubsample2019`。 请注意，对于多个应用程序，可以使用一个密钥。 此值在发送通知时映射到 `apns-topic` 的 HTTP 标头，并用于面向特定的应用程序。
* **令牌**：也称为 "密钥" 或 "私钥"。 这是从 Apple 开发人员门户上生成的 p8 文件中获取的。 密钥必须启用 APNS （生成密钥时在 Apple 开发人员门户上选择了 APNS）。 向 NH 门户/API 提供此值时，必须从其中去除 PEM 标头/页脚。
* **终结点**：这是通知中心门户边栏选项卡中的切换，并且是 API 中的字符串字段。 有效值为 `https://api.push.apple.com` 或 `https://api.sandbox.push.apple.com`。 通知中心对生产或沙盒环境使用此值来发送通知。 这必须与应用中的 `aps-environment` 权利匹配，否则，生成的 APNS 设备令牌与环境不匹配，并且通知无法发送。

下面是演示正确用法的代码示例：

```csharp
NamespaceManager nm = NamespaceManager.CreateFromConnectionString(_endpoint);
string token = "YOUR PRIVATE KEY HERE";
string keyId = "YOUR KEY ID HERE";
string appName = "YOUR APP NAME HERE";
string appId = "YOUR APP ID HERE";
NotificationHubDescription desc = new NotificationHubDescription("PATH TO YOUR HUB");
desc.ApnsCredential = new ApnsCredential(token, keyId, appId, appName);
desc.ApnsCredential.Endpoint = @"https://api.development.push.apple.com:443/3/device";
nm.UpdateNotificationHubAsync(desc);
```

## <a name="next-steps"></a>后续步骤

* [在 Azure 门户中创建 Azure 通知中心](create-notification-hub-portal.md)
* [在 Azure 门户中配置通知中心](create-notification-hub-portal.md)
