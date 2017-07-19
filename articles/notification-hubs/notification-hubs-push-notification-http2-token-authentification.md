---
title: "Azure 通知中心中 APNS 的基于令牌的 (HTTP/2) 身份验证 | Microsoft Docs"
description: "本主题说明如何利用 APNS 的新令牌身份验证"
services: notification-hubs
documentationcenter: .net
author: kpiteira
manager: erikre
editor: 
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 05/17/2017
ms.author: kapiteir
ms.translationtype: Human Translation
ms.sourcegitcommit: 6adaf7026d455210db4d7ce6e7111d13c2b75374
ms.openlocfilehash: 5a21bcd9f12fc3f96b17a556ba15526c35ababe2
ms.contentlocale: zh-cn
ms.lasthandoff: 06/22/2017


---
# <a name="token-based-http2-authentication-for-apns"></a>APNS 的基于令牌的 (HTTP/2) 身份验证
## <a name="overview"></a>概述
本文详细介绍如何将新的 APNS HTTP/2 协议用于基于令牌的身份验证。

使用新协议的关键优势包括：
-   令牌生成相对而言麻烦更少（相较于证书）
-   不再有过期日期 – 用户可以控制身份验证令牌及其吊销
-   有效负载现在最高可达 4 KB
- 同步反馈
-   正在使用 Apple 的最新协议 – 证书仍使用二进制协议（已被标记为弃用）

只需两步即可成功使用此新机制：
1.  从 Apple 开发人员帐户门户中获取必要信息
2.  使用这些新信息配置通知中心

现在一切就绪，通知中心可以开始使用新的 APNS 身份验证系统。 

如果从使用 APNS 证书凭据迁移而来，请注意：
- 令牌属性会覆盖我们的系统中用户的证书，
- 但应用程序仍会持续无缝接收通知。

## <a name="obtaining-authentication-information-from-apple"></a>从 Apple 获取身份验证信息
若要启用基于令牌的身份验证，需要 Apple 开发人员帐户的以下属性：
### <a name="key-identifier"></a>密钥标识符
可以从 Apple 开发人员帐户中的“密钥”页获取密钥标识符

![](./media/notification-hubs-push-notification-http2-token-authentification/obtaining-auth-information-from-apple.png)

### <a name="application-identifier--application-name"></a>应用程序标识符和应用程序名称
可通过开发人员帐户的应用 ID 页获取应用程序名称。 
![](./media/notification-hubs-push-notification-http2-token-authentification/app-name.png)

可通过开发人员帐户的成员身份详细信息页获取应用程序标识符。
![](./media/notification-hubs-push-notification-http2-token-authentification/app-id.png)


### <a name="authentication-token"></a>身份验证令牌
为应用程序生成令牌后，可以下载身份验证令牌。 有关如何生成此令牌的详细信息，请参阅 [Apple 开发人员文档](http://help.apple.com/xcode/mac/current/#/dev11b059073?sub=dev1eb5dfe65)。

## <a name="configuring-your-notification-hub-to-use-token-based-authentication"></a>配置通知中心以使用基于令牌的身份验证
### <a name="configure-via-the-azure-portal"></a>通过 Azure 门户进行配置
若要在门户中启用基于令牌的身份验证，请登录到 Azure 门户，转到通知中心 >“通知服务”> APNS 面板。 

有一项新属性 – 身份验证模式。 选择令牌后可以更新中心的所有相关令牌属性。

![](./media/notification-hubs-push-notification-http2-token-authentification/azure-portal-apns-settings.png)

- 输入检索自 Apple 开发人员帐户的属性， 
- 选择应用程序模式（“生产”或“沙盒”） 
- 单击“保存”以更新 APNS 凭据。 

### <a name="configure-via-management-api-rest"></a>通过管理 API (REST) 进行配置

可以使用我们的[管理 API](https://msdn.microsoft.com/library/azure/dn495827.aspx) 将通知中心更新为使用基于令牌的身份验证。
根据要配置的应用程序是“沙盒”应用还是“生产”应用（在 Apple 开发人员帐户中指定），使用相应终结点之一：

- “沙盒”终结点：[https://api.development.push.apple.com:443/3/device](https://api.development.push.apple.com:443/3/device)
- “生产”终结点：[https://api.push.apple.com:443/3/device](https://api.push.apple.com:443/3/device)

> [!IMPORTANT]
> 基于令牌的身份验证需要的 API 版本为：2017-04 或更高版本。
> 
> 

下面是一个用于通过基于令牌的身份验证更新中心的 PUT 请求示例：


        PUT https://{namespace}.servicebus.windows.net/{Notification Hub}?api-version=2017-04
          "Properties": {
            "ApnsCredential": {
              "Properties": {
                "KeyId": "<Your Key Id>",
                "Token": "<Your Authentication Token>",
                "AppName": "<Your Application Name>",
                "AppId": "<Your Application Id>",
                "Endpoint":"<Sandbox/Production Endpoint>"
              }
            }
          }
        

### <a name="configure-via-the-net-sdk"></a>通过 .NET SDK 进行配置
使用我们的[最新客户端 SDK](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/1.0.8) 可以将中心配置为使用基于令牌的身份验证。 

以下是一个演示正确用法的代码示例：


        NamespaceManager nm = NamespaceManager.CreateFromConnectionString(_endpoint);
        string token = "YOUR TOKEN HERE";
        string keyId = "YOUR KEY ID HERE";
        string appName = "YOUR APP NAME HERE";
        string appId = "YOUR APP ID HERE";
        NotificationHubDescription desc = new NotificationHubDescription("PATH TO YOUR HUB");
        desc.ApnsCredential = new ApnsCredential(token, keyId, appId, appName);
        desc.ApnsCredential.Endpoint = @"https://api.development.push.apple.com:443/3/device";
        nm.UpdateNotificationHubAsync(desc);

## <a name="reverting-to-using-certificate-based-authentication"></a>还原到使用基于证书的身份验证
可通过使用前述的任一方法并传递证书（而非令牌属性），随时还原到使用基于证书的身份验证。 该操作会覆盖以前存储的凭据。

