---
title: Azure 通知中心 iOS 13 更新 |Microsoft Docs
description: 了解 Azure 通知中心的 iOS 13 重大更改
author: sethmanheim
ms.author: sethm
ms.date: 10/16/2019
ms.topic: article
ms.service: notification-hubs
ms.reviewer: jowargo
ms.lastreviewed: 10/16/2019
ms.openlocfilehash: 697e8ba9c9f27e8d5644e3a78950ff006290efe7
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74228145"
---
# <a name="azure-notification-hubs-updates-for-ios-13"></a>iOS 13 的 Azure 通知中心更新

Apple 最近对其公共推送服务做出了一些更改;这些更改主要与 iOS 13 和 Xcode 的版本一致。 本文介绍这些更改对 Azure 通知中心的影响。

## <a name="apns-push-payload-changes"></a>APNS 推送负载更改

### <a name="apns-push-type"></a>APNS 推送类型

Apple 现在要求开发人员通过 APNS API 中的新 `apns-push-type` 标头将通知标识为警报或背景通知。 按照[Apple 的文档](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/sending_notification_requests_to_apns)： "此标头的值必须准确反映通知的有效负载的内容。 如果两者不匹配，或者在所需的系统上缺少标头，则 APN 可能会返回错误、延迟通知的发送或将其完全删除。”

开发人员现在必须在通过 Azure 通知中心发送通知的应用程序中设置此标头。 由于技术限制，客户必须为 APNS 凭据使用基于令牌的身份验证，其中包含此属性的请求。 如果为 APNS 凭据使用基于证书的身份验证，则必须切换到使用基于令牌的身份验证。

下面的代码示例演示如何在通过 Azure 通知中心发送的通知请求中设置此标头属性。

#### <a name="template-notifications---net-sdk"></a>模板通知-.NET SDK

```csharp
var hub = NotificationHubClient.CreateFromConnectionString(...);
var headers = new Dictionary<string, string> {{"apns-push-type", "alert"}};
var tempprop = new Dictionary<string, string> {{"message", "value"}};
var notification = new TemplateNotification(tempprop);
notification.Headers = headers;
await hub.SendNotificationAsync(notification);
```

#### <a name="native-notifications---net-sdk"></a>本机通知-.NET SDK

```csharp
var hub = NotificationHubClient.CreateFromConnectionString(...);
var headers = new Dictionary<string, string> {{"apns-push-type", "alert"}};
var notification = new AppleNotification("notification text", headers);
await hub.SendNotificationAsync(notification);
```

#### <a name="direct-rest-calls"></a>直接 REST 调用

```csharp
var request = new HttpRequestMessage(method, $"<resourceUri>?api-version=2017-04");
request.Headers.Add("Authorization", createToken(resourceUri, KEY_NAME, KEY_VALUE));
request.Headers.Add("ServiceBusNotification-Format", "apple");
request.Headers.Add("apns-push-type", "alert");
```

为了在此转换过程中帮助你，当 Azure 通知中心检测到未设置 `apns-push-type` 的通知时，服务将从通知请求中推断出推送类型并自动设置值。 请记住，你必须将 Azure 通知中心配置为使用基于令牌的身份验证来设置所需的标头;有关详细信息，请参阅[APNS 的基于令牌的（HTTP/2）身份验证](notification-hubs-push-notification-http2-token-authentification.md)。

## <a name="apns-priority"></a>APNS 优先级

另一小的更改，但需要对发送通知的后端应用程序进行更改的另一项更改是要求后台通知的要求现在必须将 `apns-priority` 标头设置为5。 许多应用程序将 `apns-priority` 标头设置为10（表示立即传递），或不设置它并获取默认值（也是10）。

背景通知不再允许将此值设置为10，并且必须为每个请求设置值。 如果缺少此值，Apple 将不会传递背景通知。 例如：

```csharp
var hub = NotificationHubClient.CreateFromConnectionString(...);
var headers = new Dictionary<string, string> {{"apns-push-type", "background"}, { "apns-priority", "5" }};
var notification = new AppleNotification("notification text", headers);
await hub.SendNotificationAsync(notification);
```

## <a name="sdk-changes"></a>SDK 更改

多年来，iOS 开发人员使用发送到推送令牌委托的 `deviceToken` 数据的 `description` 属性提取推送令牌，后端应用程序使用该令牌将通知发送到设备。 对于 Xcode 11，该 `description` 特性更改为不同的格式。 开发人员用于此属性的现有代码现已中断。 我们更新了 Azure 通知中心 SDK 以适应此更改，因此请将应用程序使用的 SDK 更新为版本2.0.4 或更新版本的[Azure 通知中心 IOS SDK](https://github.com/Azure/azure-notificationhubs-ios)。
