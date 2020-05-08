---
title: Microsoft 商业应用商店中的专用产品/服务
description: 适用于应用和服务发布者的 Microsoft 商用 marketplace 中的私有产品/服务。
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/19/2020
ms.author: dsindona
ms.openlocfilehash: e449f65ca65a679aa6c6823938fa561b84e38368
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82872380"
---
# <a name="private-offers-in-the-microsoft-commercial-marketplace"></a>Microsoft 商业应用商店中的专用产品/服务

[Microsoft Azure 市场](https://azuremarketplace.microsoft.com/)中的专用产品/服务，发布者可以创建仅对目标客户可见的计划。 本文介绍专用产品/服务的选项和优点。

## <a name="unlock-enterprise-deals-with-private-offers"></a>通过专用产品/服务解锁企业交易

有越来越多的企业客户正在使用网上市场查找、试用和购买云解决方案。 借助专用套餐，发布者可以使用市场以私密方式与目标客户共享自定义的解决方案，为企业提供以下功能：

- 议价：让发布者进一步开放公开销售套餐的折扣和还价。**
- 专属条款和条件：让发布者针对特定的客户定制条款和条件。**
- 专业配置：让发布者根据单个客户的需求定制其虚拟机、Azure 应用程序和 SaaS 应用套餐。** 在向所有客户全面推出新产品之前，此选项还可让发布者提供产品功能的预览访问。

专用套餐使发布者能够利用公开市场的规模和全球可用性，使用所需的控制机制灵活协商和提供自定义的交易方式与配置。 这些功能共同开启了一扇大门，让企业自信地采用云市场。 现在，企业可根据自己的预期与需求采购和销售解决方案。

专用套餐目前适用于虚拟机、Azure 应用程序（实现为解决方案模板或托管应用程序）和 SaaS 应用套餐。 

<!--- Like public offers, private offers can be created and managed via the [Cloud Partner Portal](./cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md).  Customers can be granted or revoked access to private offers in minutes.
--->

## <a name="creating-private-offers-using-skus-and-plans"></a>使用 Sku 和计划创建专用产品/服务

对于包含公共 SKU 或计划的新套餐或现有套餐，发布者可以通过创建新的 SKU 或计划轻松创建新的专用变体，并将其标记为专用。**  

<!--- [Private SKUs](./cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md) ---> 

专用 SKU 和计划是套餐的组成部分，只能由目标客户看到和购买。 专用 SKU 和计划可以重复使用已为公共 SKU 或计划发布的基本映像和/或套餐元数据。 此选项可让发布者创建公共套餐的多个专用变体，而无需发布相同基本映像和套餐元数据的多个版本。 仅适用于虚拟机和 Azure 应用程序，当私有 SKU 与公共 SKU 共享基本映像时，对产品/服务基本映像所做的任何更改都将使用该基本映像传播到所有公共和私有 Sku。

对于仅包含专用 SKU 或计划的新套餐，发布者可与往常一样创建套餐，然后将 SKU 或计划标记为专用。** 仅具有私有 Sku 或计划的产品/服务将无法通过 Microsoft 商用 marketplace 或不与产品/服务关联的客户的[Azure 门户](https://azure.microsoft.com/features/azure-portal/)来发现或访问。

## <a name="targeting-customers-with-private-offers"></a>将客户定位到专用产品/服务

对于新的和现有的专用套餐，发布者可以使用订阅标识符来确定目标客户。 使用虚拟机或 Azure 应用程序套餐的发布者可将专用 SKU 的可用性限制为单个 Azure 订阅 ID，或上传最多包含 20,000 个 Azure 订阅 ID 的 CSV。 使用 SaaS 应用专用产品/服务时，发布者可以使用手动或 CSV 上传方法关联租户 ID，以限制私有计划的可用性。

认证并发布套餐后，可以使用“同步专用订阅”功能，在数分钟内从 SKU 或计划中更新或删除客户。 利用此功能，发布者可以快速轻松地更新显示了私有 SKU 或计划的客户列表，而无需证明或再次发布该产品/服务。

## <a name="deploying-private-offers"></a>部署专用产品/服务

仅可通过[Azure 门户](https://azure.microsoft.com/features/azure-portal/)发现专用产品/服务，而不会显示在[Microsoft AppSource](https://appsource.microsoft.com/)或[Azure Marketplace](https://azuremarketplace.microsoft.com)中。 若要详细了解如何发布到不同的商用 marketplace 商店，请参阅[确定发布选项](./determine-your-listing-type.md)。

登录到 Azure 门户后，客户可以选择市场导航元素来访问其专用套餐。 专用产品/服务还将显示在搜索结果中，并可通过命令行和 Azure 资源管理器模板进行部署，就像任何其他产品。

![[专用套餐]](./media/marketplace-publishers-guide/private-offer.png)

专用套餐也会显示在搜索结果中。 只需查找 "专用" 徽章即可。

>[!Note]
>通过云解决方案提供商计划（CSP）的分销商建立的订阅不支持专用产品/服务。

<!---
## Next steps

To start using private offers, follow the steps in the [Private SKUs and Plans](./cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md) guide.

--->