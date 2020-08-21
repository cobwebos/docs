---
title: 确定发布选项 - Microsoft 商业市场
description: 本文介绍了向 Microsoft AppSource 和 Azure 市场发布产品/服务的资格标准和要求。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 07/30/2020
ms.openlocfilehash: 4a27ace4e45d00a2fbb35b9e0e3e9b7c03383681
ms.sourcegitcommit: e0785ea4f2926f944ff4d65a96cee05b6dcdb792
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/21/2020
ms.locfileid: "88705871"
---
# <a name="determine-your-publishing-option"></a>确定发布选项

为产品/服务选择的发布选项直接关系到资格要求和商业市场 GTM 权益。 更重要的是，选择的发布选项和产品/服务类型定义了用户如何与商业市场产品/服务进行交互。

若要配置你的产品/服务，你将需要了解以下主要的商业市场概念：发布选项、产品/服务类型和配置，以及将控制产品/服务在商业市场在线商店中的显示方式和位置的调用操作。

在本文中，将学习以下内容：

- 如何确定适用于你的解决方案的在线商店。
- 每个在线商店中提供了哪些发布选项和操作调用。
- 每个发布选项都提供了哪些提供类型。

## <a name="commercial-marketplace-publishing-options"></a>商业市场发布选项

下表显示了 Microsoft AppSource 和 Azure 市场中的不同产品/服务类型的发布选项。

|   | **商品（联系）**  | **商品（试用）**  | **免费** | **BYOL** | **事务处理**|
| :--------- | :----------- | :------------ | :----------- | :---------- |:---------- |
| **虚拟机** |  |  |  | Azure 市场 |  Azure 市场 |
| **Azure 应用（多 VM）** |  |  | Azure 市场 | Azure 市场 | Azure 市场  |
| **容器映像** |  |  | Azure 市场 | Azure 市场 |   |
| **IoT Edge 模块** |  |  | Azure 市场 | Azure 市场 |   |
| **托管服务** |  |  |  | Azure 市场 |   |
| **咨询服务** | 这两个在线商店 |  |  |  |   |
| **SaaS 应用** | 这两个在线商店 | 这两个在线商店 | 这两个在线商店 |  | 这两个在线商店 * |
| **Microsoft 365 应用** | AppSource | AppSource |  |  | AppSource**  |
| **Dynamics 365 加载项** |  AppSource | AppSource |  |  |   |
| **PowerApps** | AppSource |AppSource  |  |  |   |

&#42; Microsoft AppSource 中的 SaaS 应用程序事务处理产品/服务目前仅支持信用卡。

&#42;&#42; Microsoft 365 产品/服务可免费安装，并可通过 SaaS 产品/服务作为授权服务实现盈利。 有关详细信息，请参阅[通过 Microsoft 商业市场利用 Office 365 加载项实现盈利](/office/dev/store/monetize-addins-through-microsoft-commercial-marketplace)。

## <a name="choose-a-listing-option"></a>选择列表选项

可用的列表选项提供了不同的客户参与，同时为你提供访问潜在客户和 [商业 marketplace 权益](https://docs.microsoft.com/azure/marketplace/gtm-your-marketplace-benefits)的权限。 请注意与发布选项对应的列表选项：

| **发布选项**    | **说明**  |
| :------------------- | :-------------------|
| **列表** | 直接列出应用程序或服务，这样商业市场用户就可以通过“与我联系”广告语请求你与客户联系。 |
| **试用** | 通过商业市场增加曝光率，自动预配解决方案的试用体验，让潜在用户在有限的时间内免费使用你的 SaaS、IaaS 或 Microsoft 应用内体验，然后再决定是否购买。 用于试用发布选项的广告语是：“免费试用版”或“体验版” 。 |
| **BYOL** | 使用商业市场来增加曝光率、自动预配解决方案，并独立完成财务交易。 BYOL 产品/服务类型非常适合本地到云的迁移。 广告语是“立即获取”。
| **事务处理** | 事务处理产品/服务通过商业市场销售。 Microsoft 负责计费和收费。 广告语是“立即获取”。|

> [!Note]
> 使用“事务处理”发布选项时，在选择套餐类型和创建套餐之前，了解定价、计费、发票和付款注意事项非常重要。 若要了解详细信息，请参阅[商业市场事务处理功能](./marketplace-commercial-transaction-capabilities-and-considerations.md)。

## <a name="selecting-an-online-store"></a>选择在线商店

每个在线商店都提供独特的客户需求，并面向特定的受众。 你的产品/服务类型、功能和类别将确定你的产品/服务的发布位置。 类别和子类别基于目标受众映射到每个在线商店：

**Microsoft AppSource** 面向寻找业务线或行业解决方案的业务用户，以及针对 Dynamics 365、Microsoft 365 和能源平台的咨询服务。

**Azure Marketplace** 面向 IT 专业人员和开发人员，寻找在 azure 上或在 azure 上构建的解决方案以及可加快使用 azure 的咨询服务。

选择与目标受众最相符的类别和子类别。 例如，web 应用程序防火墙应发布到安全类别下的 Azure Marketplace，因为目标受众是 IT 专业人员。 合同管理应用应改为发布到 "销售" 类别下的 AppSource，因为目标受众是业务用户。 如果选择不正确的类别或子类别，可能会导致产品/服务发布到错误的在线商店。

### <a name="publishing-to-both-online-stores-saas-offers-only"></a>发布到两个在线商店 (SaaS 仅提供) 

SaaS 产品/服务可发布到 Azure Marketplace 或 AppSource。 如果你的 SaaS 产品 */* 服务适用于 (Azure Marketplace) 的技术受众和业务受众 (AppSource) ，请选择适用于每个在线商店的类别和/或子类别。 发布到这两个在线商店的产品/服务的价值主张应为 IT 专业人员 *和* 业务用户。

> [!IMPORTANT]
> 通过 Azure Marketplace 和 Azure 门户提供按流量计费的 SaaS 产品/服务。 只有专用计划的 SaaS 产品可通过 Azure 门户提供。

| 计费计费 | 公共计划 | 私有计划 | 可用项： |
|---|---|---|---|
| 是             | 是         | 否           | Azure Marketplace 和 Azure 门户 |
| 是             | 是         | 是          | Azure Marketplace 和 Azure 门户 * |
| 是             | 否          | 是          | 仅 Azure 门户 |
| 否              | 否          | 是          | 仅 Azure 门户 |

&#42; 产品/服务的私有计划将仅通过 Azure 门户提供

例如，仅 (没有公共计划) 的产品/服务将由 Azure 门户中的客户购买，具有按流量计费的计费和私有计划。 详细了解 [Microsoft 商业 marketplace 中的专用产品/服务](private-offers.md)。

### <a name="categories"></a>类别

类别和子类别基于目标受众映射到每个在线商店。 选择与产品/服务和目标受众最相符的类别和子类别。 可以选择：

- 至少一个和多达两个类别。 您可以选择主要和次要类别。
- 每个主节点和/或辅助类别最多包含两个子类别。 如果未选择任何子类别，则在所选类别下，你的产品/服务将仍然可发现。

[!INCLUDE [categories and subcategories](./includes/categories.md)]

## <a name="next-steps"></a>后续步骤

- 确定发布选项以后，即可选择用于呈现产品/服务的[产品/服务类型](./publisher-guide-by-offer-type.md)。
- 查看发布选项中按套餐类型划分的资格要求，以便最终确定套餐的选择和配置。
- 有关解决方案如何映射到产品/服务类型和配置的示例，请参阅按在线商店发布模式。
