---
title: 使用 Azure 通知中心发送 APNS VOIP 通知
description: 了解如何通过 Azure 通知中心发送 APNS VOIP 通知（不受官方支持）。
author: sethmanheim
ms.author: sethm
ms.date: 3/23/2020
ms.topic: how-to
ms.service: notification-hubs
ms.openlocfilehash: c99af881b8f93b75633741c2352dc5df17dd2963
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "80146883"
---
# <a name="use-apns-voip-through-notification-hubs-not-officially-supported"></a>通过通知中心使用 APNS VOIP（不受官方支持）

可以通过 Azure 通知中心使用 APNS VOIP 通知；但是，此方案不受官方支持。

## <a name="considerations"></a>注意事项

如果仍然选择通过通知中心发送 APNS VOIP 通知，请注意以下限制：

- 发送 VOIP 通知需要将 `apns-topic` 标头设置为应用程序捆绑 ID + `.voip` 后缀。 例如，对于捆绑 ID 为 `com.microsoft.nhubsample` 的示例应用，`apns-topic` 标头应设置为 `com.microsoft.nhubsample.voip.`

   此方法不太适用于 Azure 通知中心，因为应用的捆绑 ID 必须配置为中心的 APNS 凭据的一部分，并且不能更改值。 此外，通知中心不允许在运行时重写 `apns-topic` 标头的值。

   若要发送 VOIP 通知，必须使用 `.voip` 应用捆绑 ID 配置单独的通知中心。

- 发送 VOIP 通知需要将 `apns-push-type` 标头设置为 `voip` 值。

   为了帮助客户过渡到 iOS 13，通知中心会尝试推断 `apns-push-type` 标头的正确值。 推理逻辑已有意简单化，避免破坏标准通知。 遗憾的是，此方法会导致 VOIP 通知出现问题，因为 Apple 将 VOIP 通知视为特例，其遵循的规则不同于标准通知。

   若要发送 VOIP 通知，必须指定 `apns-push-type` 标头的显式值。

- 通知中心根据 Apple 文档中的规定，将 APNS 有效负载限制为 4 KB。 对于 VOIP 通知，Apple 允许最大 5 KB 的有效负载。 通知中心不区分标准通知和 VOIP 通知;因此，所有通知的大小限制为 4 KB。

   若要发送 VOIP 通知，则不得超过 4 KB 有效负载大小的限制。

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅以下链接：

- [`apns-topic` 和 `apns-push-type` 标头和值的文档，包括 VOIP 通知的特例](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/sending_notification_requests_to_apns)。

- [有效负载大小限制的文档](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification)。

- [适用于 iOS 13 的通知中心更新](push-notification-updates-ios-13.md#apns-push-type)。
