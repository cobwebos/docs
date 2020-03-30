---
title: 使用 Azure 通知中心发送 APNS VOIP 通知
description: 了解如何通过 Azure 通知中心（不受官方支持）发送 APNS VOIP 通知。
author: sethmanheim
ms.author: sethm
ms.date: 3/23/2020
ms.topic: how-to
ms.service: notification-hubs
ms.openlocfilehash: c99af881b8f93b75633741c2352dc5df17dd2963
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80146883"
---
# <a name="use-apns-voip-through-notification-hubs-not-officially-supported"></a>通过通知中心使用 APNS VOIP（不受官方支持）

可以通过 Azure 通知中心使用 APNS VOIP 通知;然而，没有官方支持这一方案。

## <a name="considerations"></a>注意事项

如果您仍然选择通过通知中心发送 APNS VOIP 通知，请注意以下限制：

- 发送 VOIP 通知需要`apns-topic`将标头设置为应用程序捆绑包 ID 和`.voip`后缀。 例如，对于具有捆绑 ID`com.microsoft.nhubsample`的示例应用，`apns-topic`应将标头设置为`com.microsoft.nhubsample.voip.`

   此方法不能很好地使用 Azure 通知中心，因为应用的捆绑 ID 必须配置为集线器的 APNS 凭据的一部分，并且无法更改该值。 此外，通知中心不允许在运行时重写`apns-topic`标头的值。

   要发送 VOIP 通知，必须使用`.voip`应用捆绑包 ID 配置单独的通知中心。

- 发送 VOIP 通知要求将`apns-push-type`标头设置为 值`voip`。

   为了帮助客户过渡到 iOS 13，通知中心尝试推断`apns-push-type`标头的正确值。 推理逻辑故意很简单，以避免违反标准通知。 遗憾的是，此方法会导致 VOIP 通知出现问题，因为 Apple 将 VOIP 通知视为不符合标准通知的相同规则的特殊情况。

   要发送 VOIP 通知，必须为`apns-push-type`标头指定显式值。

- 通知中心将 APNS 负载限制为 4 KB，如 Apple 所记录的那样。 对于 VOIP 通知，Apple 允许有效负载高达 5 KB。 通知中心不区分标准和 VOIP 通知;因此，通知中心不会区分"通知"和"VOIP"通知。因此，所有通知都限制为 4 KB。

   要发送 VOIP 通知，您不得超过 4 KB 有效负载大小限制。

## <a name="next-steps"></a>后续步骤

有关更多信息，请参见下列链接：

- [文档和`apns-push-type`标头和值，包括 VOIP 通知的特殊情况。 `apns-topic` ](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/sending_notification_requests_to_apns)

- [有效负载大小限制的文档](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification)。

- [iOS 13 的通知中心更新](push-notification-updates-ios-13.md#apns-push-type)。
