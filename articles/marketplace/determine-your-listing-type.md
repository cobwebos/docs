---
title: 确定发布选项 - Microsoft 商业市场
description: 本文介绍了向 Microsoft AppSource 和 Azure 市场发布产品/服务的资格标准和要求。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 07/30/2020
ms.openlocfilehash: 37647a9591c0f686e4fc3f1fd858baa46e01f7ac
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2020
ms.locfileid: "87498066"
---
# <a name="determine-your-publishing-option"></a>确定发布选项

为产品/服务选择的发布选项直接关系到资格要求和商业市场 GTM 权益。 更重要的是，选择的发布选项和产品/服务类型定义了用户如何与商业市场产品/服务进行交互。

为配置产品/服务，你需要了解下面的重要商业市场概念：发布选项、产品/服务类型和配置，以及广告语，它们决定了产品/服务在商业市场店面中的显示方式和位置。

在本文中，将学习以下内容：

- 如何确定适合解决方案的店面
- 每个店面中提供哪些发布选项和广告语
- 适用于每个发布选项的具体套餐类型

## <a name="commercial-marketplace-publishing-options"></a>商业市场发布选项

下表显示了 Microsoft AppSource 和 Azure 市场中的不同产品/服务类型的发布选项。

|   | **商品（联系）**  | **商品（试用）**  | **免费** | **BYOL** | **事务处理**|
| :--------- | :----------- | :------------ | :----------- | :---------- |:---------- |
| **虚拟机** |  |  |  | Azure 市场 |  Azure 市场 |
| **Azure 应用（多 VM）** |  |  | Azure 市场 | Azure 市场 | Azure 市场  |
| **容器映像** |  |  | Azure 市场 | Azure 市场 |   |
| **IoT Edge 模块** |  |  | Azure 市场 | Azure 市场 |   |
| **托管服务** |  |  |  | Azure 市场 |   |
| **咨询服务** | 两个商店均发布 |  |  |  |   |
| **SaaS 应用** | 两个商店均发布 | 两个商店均发布 | 两个商店均发布 |  | 两个商店均发布* |
| **Microsoft 365 应用** | AppSource | AppSource |  |  | AppSource**  |
| **Dynamics 365 加载项** |  AppSource | AppSource |  |  |   |
| **PowerApps** | AppSource |AppSource  |  |  |   |

&#42; Microsoft AppSource 中的 SaaS 应用程序事务处理产品/服务目前仅支持信用卡。

&#42;&#42; Microsoft 365 产品/服务可免费安装，并可通过 SaaS 产品/服务作为授权服务实现盈利。 有关详细信息，请参阅[通过 Microsoft 商业市场利用 Office 365 加载项实现盈利](/office/dev/store/monetize-addins-through-microsoft-commercial-marketplace)。

## <a name="choose-a-call-to-action"></a>选择操作调用

可用的发布选项提供差异化的客户参与度，并允许你访问潜在客户共享资料和[商业市场权益](https://docs.microsoft.com/azure/marketplace/gtm-your-marketplace-benefits)。 请注意与发布选项相对应的广告语：

| **发布选项**    | **说明**  |
| :------------------- | :-------------------|
| **列表** | 直接列出应用程序或服务，这样商业市场用户就可以通过“与我联系”广告语请求你与客户联系。 |
| **试用** | 通过商业市场增加曝光率，自动预配解决方案的试用体验，让潜在用户在有限的时间内免费使用你的 SaaS、IaaS 或 Microsoft 应用内体验，然后再决定是否购买。 用于试用发布选项的广告语是：“免费试用版”或“体验版” 。 |
| **BYOL** | 使用商业市场来增加曝光率、自动预配解决方案，并独立完成财务交易。 BYOL 产品/服务类型非常适合本地到云的迁移。 广告语是“立即获取”。
| **事务处理** | 事务处理产品/服务通过商业市场销售。 Microsoft 负责计费和收费。 广告语是“立即获取”。|

> [!Note]
> 使用“事务处理”发布选项时，在选择套餐类型和创建套餐之前，了解定价、计费、发票和付款注意事项非常重要。 若要了解详细信息，请参阅[商业市场事务处理功能](./marketplace-commercial-transaction-capabilities-and-considerations.md)。

## <a name="selecting-a-storefront"></a>选择商店

两个商店各自满足独特的客户需求，并面向特定受众。 你的产品/服务类型、功能和类别将确定你的产品/服务的发布位置。 类别和子类别基于目标受众映射到每个店面：

**Microsoft AppSource**面向寻找业务线或行业解决方案的业务用户，以及针对 Dynamics 365、Microsoft 365 和能源平台的咨询服务。

**Azure Marketplace**面向 IT 专业人员和开发人员，寻找在 azure 上或在 azure 上构建的解决方案以及可加快使用 azure 的咨询服务。

选择与目标受众最相符的类别和子类别。 例如，web 应用程序防火墙应发布到安全类别下的 Azure Marketplace，因为目标受众是 IT 专业人员。 合同管理应用应改为发布到 "销售" 类别下的 AppSource，因为目标受众是业务用户。 选择错误的类别或子类别可能会导致产品/服务发布到错误的店面。

### <a name="publishing-to-both-storefronts-saas-offers-only"></a>发布到这两个商店（仅限 SaaS 提供）

SaaS 产品/服务可发布到 Azure Marketplace 或 AppSource。 如果你的 SaaS 产品 */* 服务适用于技术受众（Azure Marketplace）和业务受众（AppSource），请选择适用于每个店面的类别和/或子类别。 发布到这两个店面的产品/服务的价值主张应扩展到 IT 专业人员*和*业务用户。

> [!IMPORTANT]
> 通过 Azure Marketplace 和 Azure 门户提供按流量计费的 SaaS 产品/服务。 只有专用计划的 SaaS 产品可通过 Azure 门户提供。

| 计费计费 | 公共计划 | 私有计划 | 可用项： |
|---|---|---|---|
| “是”             | “是”         | 否           | Azure Marketplace 和 Azure 门户 |
| “是”             | “是”         | “是”          | Azure Marketplace 和 Azure 门户 * |
| 是             | 否          | 是          | 仅 Azure 门户 |
| 否              | 否          | 是          | 仅 Azure 门户 |

&#42; 产品/服务的私有计划将仅通过 Azure 门户提供

例如，Azure 门户中的客户将购买仅计费计费和专用计划（无公开计划）的产品/服务。 详细了解[Microsoft 商业 marketplace 中的专用产品/服务](private-offers.md)。

### <a name="categories"></a>类别

类别和子类别基于目标受众映射到每个店面。 选择与产品/服务和目标受众最相符的类别和子类别。 可以选择：

- 至少一个和多达两个类别。 您可以选择主要和次要类别。
- 每个主节点和/或辅助类别最多包含两个子类别。 如果未选择任何子类别，则在所选类别下，你的产品/服务将仍然可发现。

[!INCLUDE [categories and subcategories](./includes/categories.md)]

## <a name="next-steps"></a>后续步骤

- 确定发布选项以后，即可选择用于呈现产品/服务的[产品/服务类型](./publisher-guide-by-offer-type.md)。
- 查看发布选项中按套餐类型划分的资格要求，以便最终确定套餐的选择和配置。
- 查看按店面划分的发布模式，例如，解决方案如何映射到套餐类型和配置。
