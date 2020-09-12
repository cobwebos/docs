---
title: 产品/服务展示最佳做法 - Microsoft 商业市场
description: 了解适用于 Microsoft AppSource 和 Azure 市场产品/服务的上市展示最佳做法。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 07/06/2020
ms.openlocfilehash: c3ffcea27fa16fd4f5449b42407a7e4779ca27f9
ms.sourcegitcommit: 9c262672c388440810464bb7f8bcc9a5c48fa326
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2020
ms.locfileid: "89420925"
---
# <a name="offer-listing-best-practices"></a>套餐列出最佳做法

本文提供了有关创建和吸引 Microsoft 商用 marketplace 产品/服务的建议。 下表概述了有关在合作伙伴中心填写产品/服务信息的最佳做法。 若要分析你的产品/服务的业绩，请在合作伙伴中心转到[市场见解仪表板](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/marketplaceinsights)。 

## <a name="online-store-offer-details"></a>在线商店产品/服务详细信息

| 设置 | 最佳做法 |
|:--- |:--- |  
| 产品名称 | 对于应用程序，提供清晰的标题，其中包括搜索关键字，以帮助客户发现你的产品/服务。 <br> <br> 对于咨询服务，请遵循以下格式： [产品名称： [Duration] [产品类型] (例如，Contoso：2周实现)  |
| 套餐说明 | 在前几个句子中提供说明产品/服务的价值主张的清楚说明。  请记住，这些句子可以在搜索引擎结果中使用。 值主张的核心组件应包括： <ul> <li>产品或解决方案的说明。 </li> <li> 受益于产品或解决方案的用户角色。 </li> <li> 客户需要或获得产品或解决方案地址。 </li> </ul> <br> 尽可能使用行业标准词汇或基于权益的措辞。  不要依赖于特性和功能来销售产品。  而应该强调提供的价值。 <br> <br> 对于咨询服务列表，请明确陈述您提供的专业服务。 |

> [!IMPORTANT]
> 请确保您的产品/服务说明符合 **[Microsoft 商标和品牌准则](https://www.microsoft.com/en-us/legal/intellectualproperty/trademarks/usage/general.aspx)** ，以及其他相关的特定于产品的指导原则，在提及 microsoft 商标和 microsoft 软件、产品和服务的名称时，也是如此。

## <a name="online-store-listing-details"></a>在线商店列表详细信息

不同在线商店的类别和行业将适用于不同的产品/服务类型。

| 在线商店 | 类别 <br>按在线商店 | 类别 <br>按在线商店 | 行业 <br> 对于 AppSource |
| :------------------- |:----------------:|:------:|:-------------:|
| **产品/服务类型**   |  **Azure 市场**  | **AppSource**  |
| Azure 应用 | X | |
| 容器 | X | |
| 咨询服务 | | | X |
| Dynamics 365 & Power Platform 的客户参与 | | X | X |
| Dynamics 365 财务 & 供应链管理 | | X | X | 
| Dynamics 365 Business Central | | X | X |
| IoT Edge 模块 | X | |
| Power BI | | X | X |
| SaaS | X | X | X |
| Azure 虚拟机 |  X |    |

### <a name="categories"></a>类别

Microsoft AppSource 和 Azure Marketplace 是提供不同解决方案类型的在线商店。 Azure Marketplace 提供了在或 Azure 上构建的 IT 解决方案。  Microsoft AppSource 提供业务解决方案，如行业 SaaS 应用程序、Dynamics 365 外接程序、Microsoft 365 加载项和 Power Platform 应用。

类别和子类别基于解决方案类型映射到每个在线商店。 你的产品/服务将发布到 Microsoft AppSource 或 Azure Marketplace，具体取决于产品/服务的产品/服务和类别/子类别选择的产品/服务类型。 

选择最适合你的解决方案类型的类别和子类别。 可以选择：

* 最多包含两个类别，其中包括一个主类别和一个辅助类别 (可选) 。
* 每个主节点和/或辅助类别最多包含两个子类别。 如果未选择任何子类别，你的产品/服务仍将仅在所选类别上可发现。

[!INCLUDE [categories and subcategories](./includes/categories.md)]

#### <a name="important-saas-offers-and-microsoft-365-add-ins"></a>重要说明： SaaS 提供和 Microsoft 365 外接程序

请参阅 " [产品/服务类型](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-overview) " 中的指南，了解有关使用情况的详细信息。 对于 SaaS 产品/服务，产品/服务的交易功能以及类别选择将确定发布产品/服务的在线商店：

| SaaS 套餐    | SaaS 套餐   | SaaS 套餐  | SaaS 套餐   | SaaS 套餐   | SaaS 套餐   | SaaS 套餐    | 适用的在线商店| 适用的在线商店 |
|:-------------:|:---:|:--------:|:---------:|:--:|:--:|:---:|:---------------------:|:-------------:|
| 计费计费 | Microsoft 365 外接程序 | 与我联系 | 事务 (至少有1个计划)  | 仅私有计划 | 仅限公用计划 | 公用 & 专用计划 | AppSource | Azure 市场 |
|  | X |  |  |  |  |  | X |  |
| X |  |  | X | X |  |  |  | X |
| X |  |  | X |  | X |  |  | X |
| X |  |  | X |  |  | X |  | X<sup>2</sup> |
|  |  |  | X | X |  |  |  | X |
|  |  |  | X |  | X |  | X<sup>1</sup> | X<sup>1</sup> |
|  |  |  | X |  |  | X | X<sup>1</sup> | X<sup>1、2</sup> |
|  |  | X |  |  |  |  | x<sup>1</sup> | X<sup>1</sup> | 

1. 根据类别/子类别和行业选择
2. 具有私有计划的产品/服务将发布到 Azure 门户


### <a name="industries"></a>行业

行业选择仅适用于发布到 AppSource 的产品/服务和在 Azure 市场中发布的咨询服务。  如果你的产品/服务可满足行业特定的需求，请选择相应的行业和/或垂直市场，并在产品/服务说明中列出行业特定的功能。 最多可以选择两 (2) 个行业；对于选定的每个行业，最多可以选择两 (2) 个垂直市场。

>[!Note]
>对于 Azure Marketplace 中的咨询服务产品/服务，没有行业纵向。

| **行业** |  **个纵向市场** |
| :------------------- | :----------------|
| **农业** | |
| **结构 & 构造** | |
| **汽车** | |
| **Distribution** | 家 <br> 包裹 & 包裹寄送 |  
| **教育** | 高等教育 <br> 主 & 辅助 Edu/K-12 <br> 博物馆 & 库 |
| **金融服务** | 银行 & 资本市场 <br> 保险 | 
| 政府 |  防御 & 情报 <br> 民用政府 <br> 公共安全 & 司法 |
| **医疗保健** | 运行状况 Payor <br> 运行状况提供程序 <br> 医药业 | 
| **宾馆 & 旅行** | 旅游和运输业 <br> 宾馆 & 休闲 <br> 餐厅 & 食物服务 | 
| **制造 & 资源** | 化学 & Agrochemical <br> 离散制造业 <br> 能源 | 
| **媒体 & 通信** | 媒体 & 娱乐 <br> 电信 | 
| **其他公共部门行业** | 林 & 钓鱼 <br> 非营利组织 | 
| **专业服务** | 合作伙伴专业服务 <br> Legal | 
| **房地产** | |

仅 Microsoft AppSource 行业：

| **行业** |  **个纵向市场** |
| :------------------- | :----------------|
| **零售 & 消费品** | 经销商 <br> 消费品 |

### <a name="applicable-products"></a>适用的产品

选择可与你的应用配合使用的适用产品，以便该产品/服务在 AppSource 中显示在所选产品下。

### <a name="search-keywords"></a>搜索关键字

关键字可帮助客户找到你的产品/服务。 确定产品/服务的高频搜索关键字，并将其包含在产品/服务摘要和说明中，以及产品/服务展示详细信息部分的关键字部分中。

## <a name="online-store-marketing-details"></a>在线商店市场营销详细信息
| 设置 | 最佳做法 |
|:--- |:--- |  
| 提供徽标 (PNG 格式，从216×216到 350 x 350 px) ：应用详细信息页 | 针对数字媒体设计和优化徽标。<br>将 PNG 格式的徽标上传到产品/服务的应用详细信息列表页。 合作伙伴中心会将其调整为所需的徽标大小。 |
|  (PNG 格式提供徽标，48×48像素) ：搜索页面 | 合作伙伴中心将从你上传的大型徽标生成此徽标。 稍后，可以选择将此替换为其他映像。 |
| “了解详细信息”文档 | 在“了解详细信息”下包含支持性的销售和市场营销资产，例如：<ul><li>白皮书</li><li> 手册</li><li>清单；</li><li> PowerPoint 演示文稿</li></ul><br>以 PDF 格式保存所有文件。 此处的目标应该是教育客户，而不是推销。<br><br>将应用登陆页的链接添加到所有文档，并添加 URL 参数来帮助跟踪访问量和试用量。 |
| 视频：仅限 AppSource、咨询服务和 SaaS 产品/服务 | 最强大的视频能够以叙事形式传达产品/服务的价值：<ul> <li> 让客户而不是你的公司成为故事的主角。 </li> <li> 视频应该解决目标客户面临的首要难题及其目标。 </li> <li> 建议长度：60-90 秒。</li> <li> 包含使用视频名称的搜索关键字。 </li> <li> 考虑添加其他视频，例如操作说明、入门或客户荐言。 </li> </ul> |
| 屏幕截图 (1280&nbsp;&times;&nbsp;720) | 最多添加 5 张屏幕截图：<br>在文件名中包含搜索关键字。 |

## <a name="link-to-your-offer-page-from-your-website"></a>从网站链接到套餐页

当你从网站上的 AppSource 或 Azure Marketplace 徽章链接到商业 Marketplace 中的列表时，你可以通过在 URL 末尾包含以下查询参数来支持强分析和报告：
* **src**：包括路由到 AppSource 的流量的来源（例如网站、LinkedIn 或 Facebook）。
* **mktcmpid**：市场营销活动 ID，可以包含字母、数字、下划线和连字符的任意组合，最多 16 个字符（例如 blogpost_12）。

以下示例 URL 包含前面所述的两个查询参数：`https://appsource.microsoft.com/product/dynamics-365/mscrm.04931187-431c-415d-8777-f7f482ba8095?src=website&mktcmpid=blogpost_12`

通过在 AppSource URL 中添加参数，可以在[合作伙伴中心](https://partner.microsoft.com/dashboard/commercial-marketplace/)的分析仪表板中查看营销活动的效果。

## <a name="next-steps"></a>后续步骤

详细了解[商业市场权益](./gtm-your-marketplace-benefits.md)。

登录到[合作伙伴中心](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership)以创建并配置产品/服务。
