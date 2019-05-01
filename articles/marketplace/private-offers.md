---
title: 专用套餐 | Azure
description: Azure 市场中面向应用和服务发布者的专用套餐。
services: Azure, Marketplace, Compute
documentationcenter: ''
author: qianw211
manager: pabutler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 11/1/2018
ms.author: qianw211
ms.openlocfilehash: c3294aaa2b6c88e08028c8958d2ab3a7297aea74
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60735914"
---
# <a name="private-offers"></a>专用套餐

[Microsoft Azure 市场](https://azuremarketplace.microsoft.com/)中的专用套餐可让发布者创建仅向目标客户显示的 SKU。

## <a name="unlock-enterprise-deals-with-private-offers"></a>让企业使用专用套餐达成交易

有越来越多的企业客户正在使用网上市场查找、试用和购买云解决方案。 借助专用套餐，发布者可以使用市场以私密方式与目标客户共享自定义的解决方案，为企业提供以下功能：

- 议价：让发布者进一步开放公开销售套餐的折扣和还价。
- 专属条款和条件：让发布者针对特定的客户定制条款和条件。
- 专业配置：让发布者根据单个客户的需求定制其虚拟机、Azure 应用程序和 SaaS 应用套餐。 在向所有客户全面推出新产品之前，此选项还可让发布者提供产品功能的预览访问。

专用套餐使发布者能够利用公开市场的规模和全球可用性，使用所需的控制机制灵活协商和提供自定义的交易方式与配置。 这些功能共同开启了一扇大门，让企业自信地采用云市场。  现在，企业可根据自己的预期与需求采购和销售解决方案。

专用套餐目前适用于虚拟机、Azure 应用程序（实现为解决方案模板或托管应用程序）和 SaaS 应用套餐。 与公共套餐一样，可以通过[云合作伙伴门户](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus)创建和管理专用套餐。  在几分钟之内，即可授予或撤销客户对专用套餐的访问权限。

## <a name="creating-private-offers-using-skus-and-plans"></a>使用 SKU 和计划创建专用套餐

对于包含公共 SKU 或计划的新套餐或现有套餐，发布者可以通过创建新的 SKU 或计划轻松创建新的专用变体，并将其标记为专用。  [专用 SKU](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus) 和计划是套餐的组成部分，只能由目标客户看到和购买。 专用 SKU 和计划可以重复使用已为公共 SKU 或计划发布的基本映像和/或套餐元数据。 此选项可让发布者创建公共套餐的多个专用变体，而无需发布相同基本映像和套餐元数据的多个版本。 （仅适用于虚拟机和 Azure 应用程序套餐）当某个专用 SKU 与公共 SKU 共享基本映像时，对套餐基本映像所做的任何更改将使用该基本映像在所有公共和专用 SKU 之间传播。

对于仅包含专用 SKU 或计划的新套餐，发布者可与往常一样创建套餐，然后将 SKU 或计划标记为专用。 与套餐不关联的客户无法通过 [Azure 市场](https://azuremarketplace.microsoft.com)或 [Azure 门户](https://azure.microsoft.com/features/azure-portal/)发现或访问仅包含专用 SKU 或计划的套餐。

## <a name="targeting-customers-with-private-offers"></a>确定专用套餐的目标客户
对于新的和现有的专用套餐，发布者可以使用订阅标识符来确定目标客户。 使用虚拟机或 Azure 应用程序套餐的发布者可将专用 SKU 的可用性限制为单个 Azure 订阅 ID，或上传最多包含 20,000 个 Azure 订阅 ID 的 CSV。 使用 SaaS 应用专用套餐时，发布者可以关联 Azure 订阅 ID 或租户 ID，以通过手动方法或 CSV 上传方法来限制专用计划的可用性。

认证并发布套餐后，可以使用“同步专用订阅”功能，在数分钟内从 SKU 或计划中更新或删除客户。 此功能可让发布者快速轻松地更新可以看到专用 SKU 或计划的客户列表，且无需重新认证或重新发布套餐。

## <a name="deploying-private-offers"></a>部署专用套餐

只能通过 [Azure 门户](https://azure.microsoft.com/features/azure-portal/)发现专用套餐，无法通过 [Azure 市场](https://azuremarketplace.microsoft.com)查看。 登录到 Azure 门户后，客户可以选择市场导航元素来访问其专用套餐。 专用套餐也会显示在搜索结果中，并与其他任何套餐一样，可以通过命令行和 Azure 资源管理器模板进行部署。

![[专用套餐]](./media/marketplace-publishers-guide/private-offer.png)

专用套餐也会显示在搜索结果中。 只需查看“专用”徽章即可。

> [!Note]
> 通过云解决方案提供商计划 (CSP) 的经销商建立的订阅不支持专用产品/服务。

## <a name="next-steps"></a>后续步骤

若要利用这些新功能，可在 [Azure 市场](https://azuremarketplace.microsoft.com/sell)中开始销售产品。
