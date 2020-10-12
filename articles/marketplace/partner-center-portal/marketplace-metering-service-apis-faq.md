---
title: 计量服务 Api 常见问题-Microsoft 商用 marketplace
description: Microsoft AppSource 和 Azure Marketplace 中的 SaaS 服务 Api 有关 SaaS 服务的常见问题解答。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 06/01/2020
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: ddec23f695396b8322d51da62158a97456096ae8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87319994"
---
# <a name="marketplace-metered-billing-apis---faq"></a>Marketplace 计费的计费 Api-常见问题

一旦客户订阅 SaaS 服务，或使用按流量计费的托管应用计划 Azure 应用程序，就可以跟踪所使用的每个计费维度的消耗情况。  如果消耗超出了为客户选择的期限设置的所含数量，你的服务会向 Microsoft 发出使用情况事件。

## <a name="for-both-saas-offers-and-managed-apps"></a>适用于 SaaS 产品和托管应用

### <a name="how-often-is-it-expected-to-emit-usage"></a>预期出现使用量的频率如何？

理想情况下，仅当前一小时内使用时，才应在过去一小时内每小时发出使用情况。

### <a name="is-there-a-maximal-period-between-one-emission-and-the-next-one"></a>两次发射之间是否有最长的时间段？

没有此类限制。 仅在发生时发出使用情况。 例如，如果你只需要为每个订阅生存期提交一个使用单位，则可以执行此操作。

### <a name="what-is-the-maximum-delay-between-the-time-an-event-occurs-and-the-time-a-usage-event-is-emitted-to-microsoft"></a>事件发生的时间与向 Microsoft 发送使用事件的时间之间的最大延迟是多少？

理想情况下，对于过去一小时内发生的事件，将每小时发出一次使用事件。 但会出现延迟。 允许的最大延迟为24小时，超过此时间后将不接受使用事件。 最佳做法是收集每小时的使用情况，并在一小时结束时发出一个事件。

例如，如果在一天的下午1日发生使用情况事件，则在下一天，你将有一个到下一天下午1：  如果系统发出使用情况关闭，则它可以恢复并发送使用情况事件发生的小时间隔，而不会造成保真度损失。

如果在实际使用后已超过24小时，你仍可以使用更高的使用情况事件发出已使用的单位。  但是，这种做法可能会损害最终客户的计费事件报表的可信度。  建议你避免每日/每周发送一次发射的计量。  这会更难理解客户的实际使用情况，以及解决有关使用情况事件可能引发的问题。

每隔一小时发送使用情况的另一个原因是避免用户在发布者发送每日/每周/每月辐射事件之前取消订阅的情况。

### <a name="what-happens-when-you-send-more-than-one-usage-event-in-the-same-hour"></a>在同一小时内发送多个使用事件时会发生什么情况？

一小时的间隔只接受一个使用事件。 小时间隔从第0分钟开始，到第59分钟结束。  如果在同一小时内发出了多个使用事件，则所有后续使用事件将被删除为重复项。

### <a name="what-happens-when-the-customer-cancels-the-purchase-within-the-time-allowed-by-the-cancellation-policy"></a>如果客户在取消策略允许的时间内取消购买，会发生什么情况？

无需支付平整速率，但超额使用将为。

### <a name="can-custom-meter-plans-be-used-for-one-time-payments"></a>自定义计量计划是否可用于一次性支付？

是的，您可以将自定义维度定义为单时间付款的一个单位，并为每个客户仅发出一次。

### <a name="can-custom-meter-plans-be-used-to-tiered-pricing-model"></a>自定义计量计划是否可以用于分层定价模型？

是的，它可以通过每个自定义维度来实现，每个维度表示一个价格层。

例如，Contoso 希望在第一封1000封电子邮件中为每封电子邮件收费，每封电子邮件 $0.4 1000 和5000电子邮件，以及每封电子邮件的 $0.2 $0.5。 它们可以定义三个自定义维度，它们对应于三个电子邮件定价层。 如果电子邮件的数量低于1000，则发出第一个维度的单位，然后在每个维度的数量介于1000和5000之间，最后是第三个维度的单位（超过5000封电子邮件）。

### <a name="what-happens-if-the-marketplace-metering-service-has-an-outage"></a>如果 Marketplace 计量服务发生服务中断，会发生什么情况？

如果 ISV 发送自定义计量并收到错误，这可能是由 Microsoft 端上的问题导致的， (通常在未) 错误的情况下接受相似事件，则 ISV 应等待，然后重试发出。

如果错误仍然存在，请重新提交自定义计量器， (将数量) 累计。 继续此过程，直到收到非错误响应。

## <a name="for-saas-offers-only"></a>仅适用于 SaaS 服务

### <a name="what-happens-when-you-emit-usage-for-a-saas-subscription-that-has-been-unsubscribed-already"></a>当你为已取消订阅的 SaaS 订阅发出使用情况时会发生什么情况？

删除 SaaS 订阅后，将不会接受任何发送到 marketplace 平台的使用事件。

只能为订阅状态 (中的订阅发出使用情况，而不能为 `PendingFulfillmentStart` 、 `Suspended` 或 `Unsubscribed` 状态) 中的订阅发出。

唯一的例外是报告 SaaS 订阅之前的时间的使用情况。

例如，客户在今天下午3日取消了 SaaS 订阅。 现在为下午5点，发布者仍可以在今天晚上6点到下午3点之间为此 SaaS 订阅发出使用情况。

### <a name="can-you-get-a-list-of-all-saas-subscriptions-including-active-and-unsubscribed-subscriptions"></a>是否可以获取所有 SaaS 订阅的列表，包括活动订阅和取消订阅的订阅？

是的，当你调用 [获取订阅列表 API](pc-saas-fulfillment-api-v2.md#subscription-apis) 时，它将包含所有 SaaS 订阅的列表。 每个 SaaS 订阅的响应中的 "状态" 字段将捕获订阅是处于活动状态还是已取消订阅。

### <a name="are-the-start-and-end-dates-of-saas-subscription-term-and-overage-usage-emission-connected"></a>SaaS 订阅条款的开始和结束日期和已连接的超额使用情况是否已连接？

在已 *订阅* 状态的现有 SaaS 订阅的任何时间点，都可以发出超额事件。 发布者负责根据计费计划中定义的策略发出使用事件。 必须根据 SaaS 订阅中定义的日期计算超额。 

例如，如果发布者定义了一个 SaaS 计划，其中每月 $100 的费率包含1000的电子邮件，则1000以上每封电子邮件都通过自定义维度 $1 计费。

如果客户在1月6日购买并激活了该订阅，则将从当天起开始计算以该标准速率包含的1000电子邮件。 因此，在订阅的第一个月 (月底结束之前) 只发送900封电子邮件，客户将仅为此订阅的第一个月支付固定费率，并且发布者在1月6日到2月5日之间不会发出超额使用情况事件。 2月6日，将自动续订订阅，计数将再次开始。 如果在2月15日客户已发送1000封电子邮件，则发送到3月5日之前发送的电子邮件的剩余部分将按电子邮件)  ($1 计费，具体取决于发布者发出的超额使用情况事件。

## <a name="next-steps"></a>后续步骤

- 有关详细信息，请参阅 [Marketplace 计量服务 api](./marketplace-metering-service-apis.md)。
