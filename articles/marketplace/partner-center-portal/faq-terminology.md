---
title: 商业市场分析常见问题解答 & 术语-合作伙伴中心
description: 获取有关合作伙伴中心的商业 marketplace 分析的常见问题的解答。 本文包含用于分析术语的数据字典。
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 42478f6e6d6cb28364fe39aafcb52b3a17aa3c23
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/05/2020
ms.locfileid: "82791470"
---
# <a name="commercial-marketplace-analytics-terminology-and-common-questions"></a>商业市场分析术语和常见问题

本文介绍有关合作伙伴中心分析消息的常见问题，同时提供分析术语的字典。

## <a name="common-questions"></a>常见问题

**我无法查看合作伙伴中心的分析数据。访问这些页面后，会看到以下消息。为什么要这样做呢？**

![尚无适用于你的产品/服务的数据](./media/analytics-faq-no-data.png)

为什么您会收到此消息：

- Marketplace 中的发布产品/服务当前不存在任何购置。 这可能意味着你的产品/服务在 marketplace 中提供，并在产品显示页面中获得客户的视图，但客户尚未采取措施来购买和部署它们。
- 你的产品/服务的发布可能正在进行，但尚未推出。 仅客户可获取实时产品/服务。 若要检查产品/服务的状态，请参阅[分析仪表板](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)中的概述。 有关详细信息，请参阅[商业 marketplace 分析中的摘要仪表板](./summary-dashboard.md)。
- 你的产品/服务可能列出为 "**与我联系**"、"仅限列表" 和 "marketplace 中的客户无法购买"。 虽然这些产品/服务生成潜在顾客，并与你共享，但不能购买这些产品/服务的订单。 若要查看产品/服务列表类型，请参阅 "设置" 页。

**我知道我有分析数据，但显示以下消息：**

![给定日期范围内没有任何数据](./media/analytics-faq-data-range.png)

如果收到此消息，则表示你有分析数据，但所选日期范围内没有数据。 选择不同的日期范围或自定义日期范围，以查看自2010以来的任何数据。 有关详细信息，请参阅[商业 marketplace 分析中的摘要仪表板](./summary-dashboard.md)的日期范围部分。

## <a name="dictionary-of-data-terms"></a>数据字典

| 属性名称 | 报表 | 定义|
|---|---|---|
| Azure 许可证类型 | Customer、Order | 购买 Azure 的客户使用的许可协议类型。 也称为通道 |
| Azure 许可证类型：云解决方案提供商 | Customer、Order | 最终客户通过其云解决方案提供商（充当经销商）购买 Azure 和市场套餐。|
| Azure 许可证类型：企业 | Customer、Order | 最终客户通过直接与 Microsoft 签署的企业协议购买 Azure 和市场套餐。|
| Azure 许可证类型：通过经销商签署的企业协议  | Customer、Order | 最终客户 procures Azure 和你的 Marketplace 通过分销商提供便利，使其与 Microsoft 企业协议。|  |
| Azure 许可证类型：即用即付| Customer、Order | 最终客户 procures Azure 和你的 Marketplace 通过 "即用即付" 协议，直接与 Microsoft 进行签名。||
| 云实例名称| 顺序| 部署 VM 的 Microsoft 云。||
| Cloud Instance Name： Azure Global| 顺序| 公共全局 Microsoft 云。|| |
| 云实例名称： Azure 政府版 | 顺序| 政府特定的 Microsoft 云，适用于以下政府之一：中国、德国或美国美国。| |
| 客户所在城市| 客户| 客户提供的城市名称。 城市可能不同于客户的 Azure 订阅中的城市。||
| 客户通信语言  | 客户| 客户的首选通信语言。||
| 客户公司名称 | Customer、Order | 客户提供的公司名称。 名称可能不同于客户的 Azure 订阅中的城市。|  |
| 客户所在国家/地区 | Customer、Order | 客户提供的国家/地区名称。 国家/地区可能不同于客户的 Azure 订阅中的国家/地区。|  |
| 客户电子邮件| 客户| 最终客户提供的电子邮件地址。 电子邮件可能与客户的 Azure 订阅中的电子邮件地址不同。||
| 客户名字| 客户| 客户提供的名称。 名称可能不同于客户的 Azure 订阅中提供的名称。| |
| 客户 ID | Customer、Order | 分配给客户的唯一标识符。 一个客户可以有零个或多个 Azure Marketplace 订阅。|  |
| 客户邮政编码  | 客户| 客户提供的邮政编码。 代码可能与客户的 Azure 订阅中提供的邮政编码不同。| |
| 客户所在的州/省| 客户| 客户提供的省/市/自治区（address）。 状态可能与客户的 Azure 订阅中提供的状态不同。| |
| 购买日期| 客户| 客户购买您发布的任何产品/服务的第一天。| |
| 丢失日期| 客户| 客户取消上次购买的所有产品/服务的最后日期。||
| 是新客户  | 顺序| 此值将确定新客户首次获取一个或多个产品/服务。 如果在 "获取日期" 的同一日历月份内，值将为 "是"。 如果客户在报告的日历月之前购买了任何产品，则值将为 "否"。 |
| 为预览版 SKU| 顺序| 如果已将 SKU 标记为 "预览"，则该值将告诉你。 如果已相应地标记了 SKU，则值将为 "是"，并且只有授权的 Azure 订阅才能部署和使用此映像。 如果 SKU 未标识为 "预览"，则值将为 "否"。  |
| 促销联系人选择加入| 客户| 此值可让用户了解客户是否主动选择加入发布者提供的促销联系人。 目前我们不会向客户提供该选项，因此，在整个板块中都指示了“否”。 部署此功能后，我们会相应地做出更新。|
| 市场许可证类型| 顺序| 市场套餐的计费方式。||
| 市场许可证类型：通过 Azure 计费| 顺序| Microsoft 是此市场套餐的代理，并代你向客户计费。 （PAYG 信用卡或企业发票）||
| 市场许可证类型：自带许可 | 顺序| VM 需要客户提供的许可证密钥才能进行部署。 Microsoft 不会通过 marketplace 向客户收取以这种方式列出其产品/服务的费用。||
| 市场许可证类型：免费| 顺序| 该产品/服务已配置为可供所有用户免费。 Microsoft 不会向客户收取此产品/服务的使用费用。||
| 市场许可证类型：Microsoft 充当经销商  | 顺序| Microsoft 是此市场套餐的经销商。|  |
| Marketplace 订阅 ID | Customer、Order | 与客户用于购买 Marketplace 产品/服务的 Azure 订阅相关联的唯一标识符。 ID 以前是 Azure 订阅 GUID。||
| 产品名称  | 顺序| Marketplace 产品/服务的名称。|| |
| 产品/服务类型  | 顺序| Microsoft 市场产品的类型。|||
| 套餐类型：托管的应用程序  | 为了 | 如果需要以下条件，请使用 Azure 应用：托管应用产品/服务类型：使用 VM 或整个基于 IaaS 的解决方案为客户部署基于订阅的解决方案。 你或你的客户要求解决方案由合作伙伴管理。 |
| 产品/服务类型： Azure 应用程序| 为了 | 如果你的解决方案需要在简单 VM 之外额外部署和配置自动化，请使用 Azure 应用程序解决方案模板产品类型。||
| 产品/服务类型：咨询服务| 顺序| Azure 市场中的咨询服务有助于将客户与服务联系起来，以支持和扩展他们对 Azure 的使用。| |
| 产品类型：容器 | 顺序| 如果解决方案是预配为基于 Kubernetes 的 Azure 容器服务的 Docker 容器映像，请使用“容器”产品/服务类型。||
| 产品/服务类型： Dynamics 365 Business Central| 顺序| 解决方案与 Dynamics 365 集成以实现财务和运营时，请使用此产品/服务类型| |
| 产品/服务类型： Dynamics 365 for Customer Engagement | 顺序| 如果解决方案与 Dynamics 365 集成以供客户参与，请使用此产品/服务类型。||
| 产品/服务类型： IoT Edge 模块 | 顺序| Azure IoT Edge 模块是 IoT Edge 管理的最小计算单元，可以包含 Microsoft 服务（如 Azure 流分析）、第三方服务或你自己的特定于解决方案的代码。 |
| 产品/服务类型： Power BI 应用程序 | 顺序| 部署与 Power BI 集成的应用程序时，请使用 Power BI 应用程序产品/服务类型。|  |
| 产品/服务类型： SaaS 应用程序| 顺序| 使用 SaaS 应用程序产品/服务类型可让客户以订阅的形式购买基于 SaaS 的技术解决方案。||
| 产品/服务类型：虚拟机 | 顺序| 将虚拟设备部署到与客户关联的订阅时，请使用“虚拟机”产品/服务类型。||
| 产品/服务类型： Visual Studio Marketplace 扩展  | 顺序| Azure DevOps 扩展开发人员以前提供的产品/服务类型。 今后，Azure DevOps 扩展开发人员可以将其扩展直接销售给客户。 扩展产品/服务可以配置为付费或包含试用版。 |
| 订单取消日期| 顺序| 取消市场订单的日期。||
| 订单 ID| 顺序| 适用于 Marketplace 服务的客户订单的唯一标识符。 基于虚拟机使用情况的产品/服务不与订单相关联。| |
| 订单购买日期| 顺序| 创建市场订单的日期。|||
| 订单状态| 顺序| 上次刷新数据时市场订单的状态。|     |
| 订单状态：活动  | 顺序| 客户已购买订单，但尚未取消订单。|         |
| 订单状态：已取消 | 顺序| 客户以前购买了订单，并随后取消了订单。||
| 提供商电子邮件| 客户| Microsoft 与最终客户之间的关系所涉及的提供商的电子邮件地址。 如果客户是通过分销商的企业，则这将是经销商。 如果涉及到云解决方案提供商（CSP），则此为 CSP。|
| Provider Name| 客户| Microsoft 与最终客户之间的关系所涉及的提供者的名称。 如果客户是通过分销商的企业，则这将是经销商。 如果涉及到云解决方案提供商（CSP），则此为 CSP。|
| SKU| 顺序| 发布期间定义的 SKU 名称。 一个套餐可能有许多 SKU，但一个 SKU 只能与一个套餐相关联。||
| 试用结束日期| 顺序| 此订单的试用期将要结束或已结束的日期。||

## <a name="next-steps"></a>后续步骤

- 有关合作伙伴中心商用 marketplace 中可用的分析报表的概述，请参阅[合作伙伴中心的专业市场分析](./analytics.md)。
- 有关为你的产品/服务汇总市场活动的聚合数据的图形、趋势和值，请参阅[商业 marketplace 分析中的摘要仪表板](./summary-dashboard.md)。
- 有关以图形和可下载格式显示的订单的信息，请参阅[商业 marketplace 分析中的 "订单" 仪表板](./orders-dashboard.md)。
- 对于虚拟机（VM）提供使用情况和计量计费指标，请参阅[商业 marketplace 分析中的使用情况仪表板](./usage-dashboard.md)。
- 有关客户的详细信息，包括增长趋势，请参阅[商业 marketplace 分析中的客户仪表板](./customer-dashboard.md)。
- 有关过去30天内下载请求的列表，请参阅[商业 marketplace 分析中的下载仪表板](./downloads-dashboard.md)。
- 若要查看有关 Azure Marketplace 和 AppSource 上的产品/服务的客户反馈的合并视图，请参阅[商业 marketplace 分析中的评级和评论仪表板](./ratings-reviews.md)。
