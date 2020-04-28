---
title: 在 Azure 通知中心针对 APNS 进行基于令牌的 (HTTP/2) 身份验证 | Microsoft Docs
description: 本主题介绍如何将新的令牌身份验证用于 APNS。
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
ms.openlocfilehash: 448b5c38371024c2eae900f4f87b343ee0a3b36a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "76263806"
---
# <a name="token-based-http2-authentication-for-apns"></a>针对 APNS 进行基于令牌的 (HTTP/2) 身份验证

## <a name="overview"></a>概述

本文介绍如何将新的 APNS HTTP/2 协议与基于令牌的身份验证配合使用。

使用新协议的主要好处包括：

* 与生成证书相比，生成令牌相对较为简单
* 不再有过期日期 – 用户可以控制身份验证令牌及其吊销
* 有效负载现在最大可以达到 4 KB
* 同步反馈
* 采用的是 Apple 的最新协议 - 证书仍然使用二进制协议（已标记为弃用）

可通过两个步骤来使用此新机制：

* 从 Apple 开发人员帐户门户获取所需的信息。
* 使用新信息配置通知中心。

通知中心现已设置为对 APNS 使用新的身份验证系统。

请注意，如果已进行迁移而不再使用 APNS 的证书凭据，则令牌属性将覆盖系统中的证书，但应用程序仍可顺利地接收通知。

## <a name="obtaining-authentication-information-from-apple"></a>从 Apple 获取身份验证信息

若要启用基于令牌的身份验证，需要获取 Apple 开发人员帐户中的以下属性：

### <a name="key-identifier"></a>密钥标识符

可以从 Apple 开发人员帐户中“密钥”页的“证书、标识符和配置文件”下获取密钥标识符：  

![](./media/notification-hubs-push-notification-http2-token-authentification/keys.png)

![](./media/notification-hubs-push-notification-http2-token-authentification/obtaining-auth-information-from-apple.png)

### <a name="application-identifier-and-application-name"></a>应用程序标识符和应用程序名称

开发人员帐户中的“证书、标识符和配置文件”页还提供了应用程序名称和标识符： 

![](./media/notification-hubs-push-notification-http2-token-authentification/app-name.png)

### <a name="configure-via-the-net-sdk-or-the-azure-portal"></a>通过 .NET SDK 或 Azure 门户进行配置

可以使用[最新的客户端 SDK](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs) 或者在 Azure 门户中，将中心配置为使用基于令牌的身份验证。 若要在门户中启用基于令牌的身份验证，请登录到 Azure 门户并转到通知中心的“设置”>“Apple (APNS)”面板。  从“身份验证模式”属性中选择“令牌”，以使用所有相关令牌属性更新中心。  

![配置令牌](./media/notification-hubs-push-notification-http2-token-authentification/azure-portal-apns-settings.png)

* 输入从 Apple 开发人员帐户检索的属性。
* 选择应用程序模式（“生产”或“沙盒”）。  
* 单击“保存”按钮以更新 APNS 凭据。 

基于令牌的凭据由以下字段组成：

* **密钥 ID**：在 Apple 开发人员门户中生成的私钥的标识符；例如 `2USFGKSKLT`。
* **团队 ID**：也称为“前缀”或“应用前缀”。 这是 Apple 开发人员门户中的组织标识符；例如 `S4V3D7CHJR`。
* **捆绑 ID**：也称为“应用 ID”。 这是应用程序的捆绑标识符；例如 `com.microsoft.nhubsample2019`。 请注意，可对多个应用使用一个密钥。 此值在发送通知时将映射到 `apns-topic` HTTP 标头，并用于将特定应用程序指定为目标。
* **令牌**：也称为“密钥”或“私钥”。 此值是从 Apple 开发人员门户上生成的 .p8 文件中获取的。 必须为密钥启用 APNS（生成密钥时在 Apple 开发人员门户上选择 APNS）。 在 NH 门户/API 中提供此值时，必须去除其中的 PEM 头部/尾部。
* **终结点**：这是通知中心门户边栏选项卡中的一个切换开关，且是 API 中的一个字符串字段。 有效值为 `https://api.push.apple.com` or `https://api.sandbox.push.apple.com`进行求值的基于 SQL 语言的筛选器表达式。 通知中心在生产或沙盒环境使用此值来发送通知。 此值必须与应用中的 `aps-environment` 权利相匹配，否则，生成的 APNS 设备令牌将与环境不匹配，并且无法发送通知。

下面是演示了正确用法的代码示例：

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
