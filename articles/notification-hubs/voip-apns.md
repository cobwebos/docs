---
title: 通过 Azure 通知中心发送 APNS VOIP 通知
description: 了解如何通过 Azure 通知中心发送 APNS VOIP 通知（不正式支持）。
author: sethmanheim
ms.author: sethm
ms.date: 3/23/2020
ms.topic: how-to
ms.service: notification-hubs
ms.openlocfilehash: c99af881b8f93b75633741c2352dc5df17dd2963
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80146883"
---
# <a name="use-apns-voip-through-notification-hubs-not-officially-supported"></a>通过通知中心使用 APNS VOIP （不正式支持）

可以通过 Azure 通知中心使用 APNS VOIP 通知;但是，对于此方案，不提供任何正式支持。

## <a name="considerations"></a>注意事项

如果仍选择通过通知中心发送 APNS VOIP 通知，请注意以下限制：

- 发送 VOIP 通知时，需要`apns-topic`将标头设置为应用程序捆绑 ID 和`.voip`后缀。 例如，对于具有捆绑 ID `com.microsoft.nhubsample`的示例应用， `apns-topic`标头应设置为`com.microsoft.nhubsample.voip.`

   此方法不适用于 Azure 通知中心，因为应用的捆绑 ID 必须配置为中心的 APNS 凭据的一部分，并且不能更改此值。 此外，通知中心不允许在运行时重写`apns-topic`标头的值。

   若要发送 VOIP 通知，必须使用`.voip`应用捆绑 ID 配置单独的通知中心。

- 发送 VOIP 通知时，需要`apns-push-type`将标头设置为值`voip`。

   为了帮助客户过渡到 iOS 13，通知中心会尝试推断正确的`apns-push-type`标头值。 推理逻辑特意非常简单，目的是避免破坏标准通知。 遗憾的是，此方法会导致 VOIP 通知出现问题，因为 Apple 将 VOIP 通知视为不符合标准通知的特殊情况。

   若要发送 VOIP 通知，必须为`apns-push-type`标头指定一个显式值。

- 通知中心将 APNS 负载限制为 4 KB，如 Apple 所述。 对于 VOIP 通知，Apple 允许高达 5 KB 的负载。 通知中心不区分标准通知和 VOIP 通知;因此，所有通知的大小限制为 4 KB。

   若要发送 VOIP 通知，你不得超过 4 KB 负载大小限制。

## <a name="next-steps"></a>后续步骤

有关更多信息，请参见下列链接：

- [和标头和值的文档，包括 VOIP 通知的特殊情况。 `apns-push-type` `apns-topic` ](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/sending_notification_requests_to_apns)

- [负载大小限制的文档](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification)。

- [IOS 13 的通知中心更新](push-notification-updates-ios-13.md#apns-push-type)。
