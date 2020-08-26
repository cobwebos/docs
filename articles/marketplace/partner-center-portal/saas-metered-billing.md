---
title: 使用 marketplace 计量服务计量计费 |Azure Marketplace
description: 此文档是使用灵活计费模型的 Isv 发布 SaaS 产品/服务的指南。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/08/2020
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: d6c46575ed497c5067b3ffc5c745e79f814bc212
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/28/2020
ms.locfileid: "87304371"
---
# <a name="metered-billing-using-the-marketplace-metering-service"></a>使用 marketplace 计量服务的计费计费

利用 Marketplace 计量服务，可以创建按非标准单元收费的软件即服务（SaaS）产品/服务。  在发布此产品/服务之前，需要定义计费维度，如带宽、票证或处理的电子邮件。  然后，客户根据他们对这些维度的消耗进行支付，系统会通过 Marketplace 计量事件发生时通过 Marketplace 计量服务 API 向 Microsoft 发出通知。  

## <a name="prerequisites-for-metered-billing"></a>计费计费的先决条件

要使 SaaS 产品使用计费计费，必须首先执行以下操作：

* 按照[创建 SaaS 服务](./create-new-saas-offer.md)中所述，满足[销售通过 Microsoft 产品/服务](./create-new-saas-offer.md#sell-through-microsoft)的所有产品/服务的要求。
* 集成了[SaaS 履单 api](./pc-saas-fulfillment-api-v2.md) ，使客户能够预配和连接到你的产品/服务。  
* 在向客户收取服务费用时，为**平面费率**定价模型配置。  维度是对平面费率定价模型的可选扩展。 
* 与[Marketplace 计量服务 api](./marketplace-metering-service-apis.md)集成，通知 Microsoft 计费事件。

然后，SaaS 产品/服务可与[Marketplace 计量服务 api](./marketplace-metering-service-apis.md)集成，通知 Microsoft 计费事件。

>[!Note]
>Marketplace 计量服务仅适用于单层费率计费模型，不适用于每用户计费模式。

## <a name="how-metered-billing-fits-in-with-pricing"></a>按计费计费的计费方式

了解产品/服务层次结构在定义产品/服务及其定价模型时非常重要。

* 每个 SaaS 产品/服务都配置为通过 Microsoft 进行销售。  发布产品/服务后，将无法更改此选项。
* 每个 SaaS 产品（配置为通过 Microsoft 销售）都可以有一个或多个计划。  用户订阅 SaaS 产品/服务，但它是通过 Microsoft 在计划上下文中购买的。
* 每个计划都有一个与之关联的定价模型：**平面速率**或**每用户**。 产品/服务中的所有计划都必须与同一定价模型相关联。 例如，不能有一个产品/服务的计划适用于单层定价模型，另一个则为每用户定价模型。
* 在为平面费率计费模型配置的每个计划中，至少包括一项周期性费用（可能为 $0）：
    * 定期**每月**费用：在用户购买计划时每月定期支付的按月计费。
    * 定期**年**费：在用户购买计划时，按年度定期支付的每年费用。
* 除了定期收费以外，还可以包含用于对客户收费的可选自定义维度，使其不包含在平整费率中。  每个维度表示你的服务将使用[Marketplace 计量服务 API](./marketplace-metering-service-apis.md)与 Microsoft 通信的计费单位。

## <a name="sample-offer"></a>示例产品

例如，Contoso 是一个名为 Contoso Notification Services （CNS） SaaS 服务的发布者。 CNS 允许其客户通过电子邮件或文本发送通知。 Contoso 注册为 "合作伙伴中心" 中的发布者，以将 SaaS 产品/服务发布到 Azure 客户。  有两个与 CN 关联的计划，如下所述：

* 基本计划
    * 每月发送10000封电子邮件和1000文本（按月收费）
    * 超过10000封电子邮件，为每个100电子邮件支付 $1
    * 超过1000个文本，每个文本支付 $0.02

    [![基本计划定价](./media/saas-basic-pricing.png "单击以放大视图")](./media/saas-basic-pricing.png)

* 高级计划
    * 对于每年的 $3500，发送50000封电子邮件和10000文本，每年的5分钟电子邮件和1M 文本
    * 超过50000封电子邮件，为每个100电子邮件支付 $0。5
    * 超过10000个文本，每个文本支付 $0.01

    [![高级计划定价](./media/saas-premium-pricing.png "单击以放大视图")](./media/saas-premium-pricing.png)

* 企业计划
    * 每月发送无数封电子邮件和50000短信
    * 超过50000个文本每 txt 支付 $0.005

    [![企业计划定价](./media/saas-enterprise-pricing.png "单击以放大视图")](./media/saas-enterprise-pricing.png)

根据所选的计划，向 CNS SaaS 产品/服务购买订阅后，将能够在订阅详细信息-起始日期和结束日期中发送包含数量的文本和电子邮件。  Contoso 将使用情况计算在基准中包含的数量，而不向 Microsoft 发送任何使用事件。 当客户消耗的数量超过所含数量时，无需更改计划或执行其他任何操作。  Contoso 将衡量超出所含数量的超额，并开始向 Microsoft 发送使用情况事件，使用[Marketplace 计量服务 API](./marketplace-metering-service-apis.md)对超额使用情况收费。  同时，Microsoft 会根据发布者在自定义维度中指定的超额使用情况向客户收取费用。 超额计费将在下一个计费周期（每月，但对于某些客户可以是每季度或更早的时候）完成。  对于每月单层计划，会在每个月发生超额支付的费用。  对于 "每年" 平面费率计划，一旦使用了 "每年基准" 中包含的数量，自定义计量器发出的所有附加使用量将按每个计费周期（每月）的超额计费，直到订阅年份结束。

## <a name="billing-dimensions"></a>计费维度

每个计费维度都定义了一个自定义单元，ISV 可以通过它发出使用事件。  计费维度还用于与客户沟通，了解如何使用该软件进行计费。  它们的定义如下：

* **ID**：发出使用事件时引用的不可变维度标识符。
* **显示名称**：与维度关联的显示名称，例如 "已发送短信"。
* **度量单位**：计费单位的说明，例如，"每短信" 或 "每100封电子邮件"。
* **每单位价格（美元**）：维度的一个单位的价格。  它可以为0。 
* **"基本" 中包含的月度数量**：按月支付每月费用的客户每月的维度数量必须是整数。 它可以为0或无限制。
* **"基数" 中包含的年数量**：每年包含的维度数量，适用于支付定期年费用的客户，必须是整数。 可以为0或无限制。

在产品/服务的所有计划中共享计费维度。  某些属性适用于所有计划中的维度，其他属性则是特定于计划的。

定义维度本身的属性在产品/服务的所有计划中共享。  在发布产品/服务之前，从任何计划的上下文中对这些属性所做的更改将影响所有计划的维度定义。  发布产品/服务后，将无法再编辑这些属性。  这些属性包括：

* ID
* 显示名称
* 计量单位

维度的其他属性特定于每个计划，并且可以有不同于计划的值。  在发布计划之前，你可以编辑这些值，只有此计划会受到影响。  发布计划后，这些属性将不再可编辑。  这些属性包括：

* 每单位价格（美元）
* 基数中包含的月度数量  
* BaseIncluded 中包含的年数量  

维度还具有两个特殊概念： "enabled" 和 "无限大"：

* "**已启用**" 指示此计划参与此维度。  如果要创建一个新计划，该计划不会基于此维度发送使用情况事件，可能需要将此选项保留为未选中状态。  此外，在首次发布计划后添加的任何新维度在已发布的计划中都将显示为 "未启用"。  对于客户所见的计划，禁用的维度不会显示在任何维度列表中。
* **无限大表示的**无穷符号 "∞" 表示此计划参与此维度，但不会针对此维度发出使用情况。  如果要向客户指示此维度所表示的功能包含在计划中，但没有使用限制。  具有无限使用量的维度将显示在客户所见的计划的维度列表中，指示该维度永远不会对此计划收取费用。

>[!Note] 
>明确支持以下方案： <br> -可以将新维度添加到新计划。  对于任何已发布的计划，将不启用新维度。 <br> -你可以在没有任何维度的情况下发布**单层**计划，然后添加新计划并为该计划配置一个新维度。 不会为已发布的计划启用新维度。

### <a name="setting-dimension-price-per-unit-per-supported-market"></a>设置每个单位的维度价格每个受支持市场

与平整费率定价一样，计费维度价格可根据支持的国家/地区设置。 发布者需要使用合作伙伴中心的定价数据导入和导出功能。

1. 首先定义所需的维度，并标记支持哪些市场。 
1. 然后将此数据导出到文件中。
1. 为每个国家/地区添加正确的价格，并在合作伙伴中心导入该文件。

计量器的 UI 会更改，以反映只能在文件中显示维度的价格。

[![Marketplace 计量服务维度](./media/metering-service-dimentions.png "单击以放大视图")](./media/metering-service-dimentions.png)


### <a name="private-plan"></a>私有计划

与平面速率计划一样，具有维度的计划可以设置为私有计划，只能由计划定义的受众访问。

## <a name="constraints"></a>约束

### <a name="trial-behavior"></a>试用行为

使用 marketplace 计量服务的计费计费与提供免费试用版不兼容。  不能将计划配置为使用计量计费和免费试用版。

### <a name="locking-behavior"></a>锁定行为

由于与 Marketplace 计量服务一起使用的维度表示了解客户将如何为服务付费，因此，一旦您发布维度的所有详细信息，就不能再对其进行编辑。  在发布之前，必须为计划完全定义维度，这一点很重要。

使用维度发布产品/服务后，将无法再更改该维度的产品/服务详细信息：

* ID
* 显示名称
* 计量单位

发布计划后，将无法再更改计划级别的详细信息：

* 每单位价格（美元）
* 基数中包含的月度数量
* 基数中包含的年数量
* 是否为计划启用维度

### <a name="upper-limits"></a>上限

可为单个产品/服务配置的最大维度数量是18个唯一维度。

## <a name="get-support"></a>获取支持

如果你有以下问题之一，你可以打开支持票证。

* Marketplace 计量服务 API 的技术问题。
* 由于你端的错误或错误而需要升级的问题（例如 使用情况事件错误）。
* 与计费计费相关的任何其他问题。

按照对[合作伙伴中心中的 "商业市场" 计划的支持](./support.md)中的说明，了解发布者支持选项并与 Microsoft 建立支持票证。

## <a name="next-steps"></a>后续步骤

- 有关详细信息，请参阅[Marketplace 计量服务 api](./marketplace-metering-service-apis.md) 。
