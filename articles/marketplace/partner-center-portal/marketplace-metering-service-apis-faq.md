---
title: 市场计量服务 API - 常见问题 |Azure 应用商店
description: 在 Azure 应用商店中发出 SaaS 产品/服务的使用。
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/11/2019
ms.openlocfilehash: 6e5b691a41ef283449f9eeeb90e9d01a91616146
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275775"
---
# <a name="marketplace-metering-service-apis---faq"></a>Azure 市场计量服务 API - 常见问题解答

Azure 用户订阅包含按流量计费的 SaaS 服务后，将跟踪客户正在使用的每个计费维度的消耗量。 如果消耗量超过为客户选择的术语设置的包含数量，则服务将向 Microsoft 发出使用事件。

## <a name="emit-usage-events"></a>发出使用事件

>[!Note]
>本节仅适用于 SaaS 产品/服务，其中至少有一个计划在发布产品/服务时定义了计量服务维度。

![发出使用事件](media/isv-emits-usage-event.png)

有关发出使用事件事件的 API 协定的信息，请参阅[SaaS 批处理使用事件 API。](./marketplace-metering-service-apis.md#batch-usage-event)

### <a name="how-often-is-it-expected-to-emit-usage"></a>它预期多久发出一次用法？

理想情况下，您应在过去一小时每小时发出一次用法，仅当前一小时有用法时。

### <a name="what-is-the-maximum-delay-between-the-time-an-event-occurs-and-the-time-a-usage-event-is-emitted-to-microsoft"></a>事件发生的时间与向 Microsoft 发出使用事件之间的最大延迟是多少？

理想情况下，对于过去一小时发生的事件，每小时都会发出使用事件。 但是，预计会有延误。 允许的最大延迟为 24 小时，之后将不接受使用事件。

例如，如果使用事件发生在一天的下午 1 点，则您必须在第二天下午 1 点之前发出与此事件关联的使用事件。 这意味着，在系统发出使用有停机时间的情况下，它可以恢复，然后发送使用事件的时间间隔发生使用，而不会丢失保真度。

### <a name="what-happens-when-you-send-more-than-one-usage-event-on-the-same-hour"></a>当您在同一小时内发送多个使用事件时会发生什么情况？

每小时间隔仅接受一个使用事件。 小时间隔从分钟 0 开始，以第 59 分钟结束。  如果在同一小时间隔内发出多个使用事件，则任何后续使用事件将作为重复项丢弃。

### <a name="what-happens-when-you-emit-usage-for-a-saas-subscription-that-has-been-unsubscribed-already"></a>当您发出已取消订阅的 SaaS 订阅的使用量时会发生什么情况？

删除 SaaS 订阅后，将不接受发送到市场平台的任何使用事件。

### <a name="can-you-get-a-list-of-all-saas-subscriptions-including-active-and-unsubscribed-subscriptions"></a>能否获取所有 SaaS 订阅的列表，包括活动订阅和取消订阅订阅？

是，当您调用 API`GET /saas/subscriptions`时，它包含所有 SaaS 订阅的列表。 每个 SaaS 订阅的响应中的状态字段捕获订阅是活动还是取消订阅。 对列表订阅的调用此时最多返回 100 个订阅。

## <a name="next-steps"></a>后续步骤

- 有关详细信息[，请参阅应用商店计量服务 API。](./marketplace-metering-service-apis.md)
