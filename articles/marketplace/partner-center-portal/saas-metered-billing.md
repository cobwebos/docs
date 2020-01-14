---
title: 使用 marketplace 计量服务计量计费 |Azure Marketplace
description: 此文档是使用灵活计费模型的 Isv 发布 SaaS 产品/服务的指南。
author: MaggiePucciEvans
manager: evansma
ms.author: evansma
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: f1b60831e08383ee455cd3afbfbc21ed27445ffc
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/14/2020
ms.locfileid: "75934168"
---
# <a name="metered-billing-using-the-marketplace-metering-service"></a>使用 marketplace 计量服务的计费计费

利用 Marketplace 计量服务，可以在基于非标准单元收费的商业市场计划中创建软件即服务（SaaS）产品/服务。  在发布此产品/服务之前，需要定义计费维度，如带宽、票证或处理的电子邮件。  然后，客户根据他们对这些维度的消耗进行支付，系统会通过 Marketplace 计量事件发生时通过 Marketplace 计量服务 API 向 Microsoft 发出通知。  

## <a name="prerequisites-for-metered-billing"></a>计费计费的先决条件

为了使 SaaS 产品使用按流量计费，必须执行以下操作：

* 按照[创建 SaaS 服务](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-saas-offer)中所述，满足[销售通过 Microsoft 产品/服务](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-saas-offer#sell-through-microsoft)的所有产品/服务的要求。
* 集成了[SaaS 履单 api](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2) ，使客户能够预配和连接到你的产品/服务。  
* 应为服务的客户的计费**费率**定价模型进行配置。  维度是对平面费率定价模型的可选扩展。 
* 与[Marketplace 计量服务 api](./marketplace-metering-service-apis.md)集成，通知 Microsoft 计费事件。

>[!Note]
>Marketplace 计量服务仅适用于单层费率计费模型，不适用于每用户计费模式。

## <a name="how-metered-billing-fits-in-with-pricing"></a>按计费计费的计费方式

当涉及到定义产品/服务的定价模型时，必须了解产品/服务层次结构。

* 每个 SaaS 产品/服务都配置为通过 Microsoft 进行销售。  发布产品/服务后，无法更改此设置。
* 每个 SaaS 产品（配置为通过 Microsoft 销售）都可以有一个或多个计划。 用户订阅 SaaS 产品/服务，但它是通过 Microsoft 在计划上下文中购买的。
* 每个计划都有一个与之关联的定价模型：**平面速率**或**每用户**。 产品/服务中的所有计划都必须与同一定价模型相关联。 例如，不能有一个产品/服务的计划为 "平面费率定价模型"，另一个为 "每用户" 定价模型。
* 在为平面费率计费模型配置的每个计划中，至少包括一项周期性费用（可能为 $0）：
    * 定期**每月**费用：用户购买计划时每月定期支付的按月计费。
    * 定期**年**费：在用户购买计划时，按年度定期支付的每年费用。
* 除了定期收费以外，该计划还可以包括用于对客户进行计费的可选维度，而不包括在平整费率中。   每个维度表示你的服务将使用[Marketplace 计量服务 API](./marketplace-metering-service-apis.md)与 Microsoft 通信的计费单位。

## <a name="sample-offer"></a>示例产品

例如，Contoso 是一个名为 Contoso Notification Services （CNS） SaaS 服务的发布者。 CNS 允许客户通过电子邮件或文本发送通知。 Contoso 注册为 "合作伙伴中心" 中的发布者，以便向 Azure 客户发布产品/服务。  有两个与 CN 关联的计划，如下所述：

* 基本计划
    * 每月发送10000封电子邮件和1000文本
    * 超过10000封电子邮件，为每个100电子邮件支付 $1
    * 超过1000个文本，每个文本支付 $0.02
* 高级计划
    * 向 350/月发送50000封电子邮件和10000文本
    * 超过50000封电子邮件，为每个100电子邮件支付 $0。5
    * 超过10000个文本，每个文本支付 $0.01

订阅了 CNS 服务的 Azure 客户将能够根据所选的计划，每月发送包含数量的文本和电子邮件。  Contoso 会衡量所含数量的使用量，而不会向 Microsoft 发送任何使用事件。  当客户消耗的数量超过所含数量时，无需更改计划或执行其他任何操作。  Contoso 将衡量超出所含数量的超额，并向 Microsoft 开始向 Microsoft 发送使用情况事件，以使用[Marketplace 计量服务 API](./marketplace-metering-service-apis.md)进行其他使用。  Microsoft 将根据发布者指定的额外使用量向客户收取费用。

## <a name="billing-dimensions"></a>计费维度

计费维度用于向客户传达使用软件的计费方式，还用于向 Microsoft 传达使用情况事件。 它们的定义如下：

* **维度标识符**：发出使用事件时引用的不可变标识符。
* **维度名称**：与维度关联的显示名称，例如 "已发送短信"。
* **度量单位**：计费单位的说明，例如 "每短信" 或 "每100封电子邮件"。
* **每单位价格**：维度的一个单位的价格。  
* **每月期限的包含数量**：每月每月要支付每月费用的客户的维度数量，必须是整数。
* **年度条款的已包含数量**：每月每月要支付定期年费的客户的维度数量，必须是整数。

在产品/服务的所有计划中共享计费维度。  某些属性适用于所有计划中的维度，其他属性则是特定于计划的。

定义维度本身的属性在产品/服务的所有计划中共享。  在发布产品/服务之前，从任何计划的上下文中对这些属性所做的更改将影响所有计划的维度定义。  发布产品/服务后，将无法再编辑这些属性。  这些属性包括：

* 标识符
* 名称
* 计量单位

维度的其他属性特定于每个计划，并且可以有不同于计划的值。  在发布计划之前，你可以编辑这些值，只有此计划会受到影响。  发布计划后，这些属性将不再可编辑。  这些属性包括：

* 价格/单位
* 每月客户的已包含数量 
* 年度客户的已包含数量 

维度还具有两个特殊概念： "enabled" 和 "无限大"：

* "**已启用**" 指示此计划参与此维度。  如果创建的新计划不基于此维度发送使用情况事件，则您可能希望不选中此项。  此外，在首次发布计划后添加的任何新维度在已发布的计划中都将显示为 "未启用"。  对于客户所见的计划，禁用的维度不会显示在任何维度列表中。
* **无穷大，用**无穷符号 "∞" 表示，指示此计划参与此维度，但不会对此维度使用计量。  如果要向客户指示此维度所表示的功能包含在计划中，但没有使用限制。  具有无限使用量的维度将显示在客户所见的计划的维度列表中，指示该维度永远不会对此计划收取费用。

>[!Note] 
>明确支持以下方案： <br> -可以将新维度添加到新计划。  对于任何已发布的计划，将不启用新维度。 <br> -你可以在没有任何维度的情况下发布**单层**计划，然后添加新计划并为该计划配置一个新维度。 不会为已发布的计划启用新维度。

## <a name="constraints"></a>约束

### <a name="trial-behavior"></a>试用行为

使用 marketplace 计量服务的计费计费与提供免费试用版不兼容。  不能将计划配置为使用计量计费和免费试用版。

### <a name="locking-behavior"></a>锁定行为

由于与 Marketplace 计量服务一起使用的维度表示了解客户将如何为服务付费，因此，一旦您发布维度的所有详细信息，就不能再对其进行编辑。  在发布之前，必须为计划完全定义维度，这一点很重要。
  
使用维度发布产品/服务后，将无法再更改该维度的产品/服务详细信息：

* 标识符
* 名称
* 计量单位

发布计划后，将无法再更改计划级别的详细信息：

* 价格/单位
* 每月期限的已包含数量
* 年度条款的已包含数量
* 是否为计划启用维度

### <a name="upper-limits"></a>上限

可为单个产品/服务配置的最大维度数量是18个唯一维度。

## <a name="get-support"></a>获取支持

如果你有以下其中一项，则可以打开支持票证。

* Marketplace 计量服务 API 的技术问题。
* 由于你端的错误或错误而需要升级的问题（例如 使用情况事件错误）。
* 与计费计费相关的任何其他问题。 

请按照以下步骤提交支持票证：

1. 转到[支持页](https://support.microsoft.com/supportforbusiness/productselection?sapId=48734891-ee9a-5d77-bf29-82bf8d8111ff)。 将自动为您填充前几个下拉菜单。 对于 Marketplace 支持，请将产品系列标识为**云和在线服务**，将产品作为**Marketplace 发布者**。  不要更改预填充的下拉菜单选项。
2. 在 "选择产品版本" 下，选择 "Live 产品/**服务管理**"。
3. 在 "选择最能描述此问题的类别" 下，选择 " **SaaS 应用**"。
4. 在 "选择最能描述此问题的问题" 下，选择 "**按流量计费**"。
5. 通过选择 "**下一步**" 按钮，你将被定向到 "**问题详细信息**" 页，你可以在其中输入有关问题的更多详细信息。

有关更多发布者支持选项，请参阅[合作伙伴中心的商业市场计划支持](https://docs.microsoft.com/azure/marketplace/partner-center-portal/support)。

## <a name="next-steps"></a>后续步骤

- 有关详细信息，请参阅[Marketplace 计量服务 api](./marketplace-metering-service-apis.md) 。
