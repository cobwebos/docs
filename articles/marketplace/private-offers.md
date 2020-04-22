---
title: 私人优惠 |Azure 应用商店
description: Azure 市场中面向应用和服务发布者的专用套餐。
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/19/2020
ms.author: dsindona
ms.openlocfilehash: efda9fb66a65f1f2cd40de8bf32a8a3fa99aea7f
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/21/2020
ms.locfileid: "81681432"
---
# <a name="private-offers"></a>专用产品/服务

[Microsoft Azure 市场](https://azuremarketplace.microsoft.com/)中的专用套餐可让发布者创建仅向目标客户显示的 SKU。

## <a name="unlock-enterprise-deals-with-private-offers"></a>解锁企业交易与私人优惠

有越来越多的企业客户正在使用网上市场查找、试用和购买云解决方案。 借助专用套餐，发布者可以使用市场以私密方式与目标客户共享自定义的解决方案，为企业提供以下功能：

- 议价：让发布者进一步开放公开销售套餐的折扣和还价。**
- 专属条款和条件：让发布者针对特定的客户定制条款和条件。**
- 专业配置：让发布者根据单个客户的需求定制其虚拟机、Azure 应用程序和 SaaS 应用套餐。** 在向所有客户全面推出新产品之前，此选项还可让发布者提供产品功能的预览访问。

专用套餐使发布者能够利用公开市场的规模和全球可用性，使用所需的控制机制灵活协商和提供自定义的交易方式与配置。 这些功能共同开启了一扇大门，让企业自信地采用云市场。 现在，企业可根据自己的预期与需求采购和销售解决方案。

专用套餐目前适用于虚拟机、Azure 应用程序（实现为解决方案模板或托管应用程序）和 SaaS 应用套餐。 

<!--- Like public offers, private offers can be created and managed via the [Cloud Partner Portal](./cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md).  Customers can be granted or revoked access to private offers in minutes.
--->

## <a name="creating-private-offers-using-skus-and-plans"></a>使用 SKU 和计划创建私人优惠

对于包含公共 SKU 或计划的新套餐或现有套餐，发布者可以通过创建新的 SKU 或计划轻松创建新的专用变体，并将其标记为专用。**  

<!--- [Private SKUs](./cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md) ---> 

专用 SKU 和计划是套餐的组成部分，只能由目标客户看到和购买。 专用 SKU 和计划可以重复使用已为公共 SKU 或计划发布的基本映像和/或套餐元数据。 此选项可让发布者创建公共套餐的多个专用变体，而无需发布相同基本映像和套餐元数据的多个版本。 对于虚拟机和 Azure 应用程序，仅当专用 SKU 与公共 SKU 共享基本映像时，对产品/服务基本映像的任何更改都将使用该基本映像在所有公共和私有 SKU 中传播。

对于仅包含专用 SKU 或计划的新套餐，发布者可与往常一样创建套餐，然后将 SKU 或计划标记为专用。** 只有私有 SKU 或计划的产品/服务将不能通过 Microsoft 商业市场或[Azure 门户](https://azure.microsoft.com/features/azure-portal/)被与产品/服务无关的客户发现或访问。

## <a name="targeting-customers-with-private-offers"></a>通过私人优惠定位客户

对于新的和现有的专用套餐，发布者可以使用订阅标识符来确定目标客户。 使用虚拟机或 Azure 应用程序套餐的发布者可将专用 SKU 的可用性限制为单个 Azure 订阅 ID，或上传最多包含 20,000 个 Azure 订阅 ID 的 CSV。 使用 SaaS App 私人产品/服务时，发布者可以使用手动或 CSV 上载方法关联租户 ID 以限制私人计划的可用性。

认证并发布套餐后，可以使用“同步专用订阅”功能，在数分钟内从 SKU 或计划中更新或删除客户。 此功能使发布商无需再次认证或发布产品/服务，即可快速轻松地更新向其提交私有 SKU 或计划的客户列表。

## <a name="deploying-private-offers"></a>部署私人产品/服务

专用产品/服务只能通过 Azure[门户](https://azure.microsoft.com/features/azure-portal/)进行发现，并且不会显示在[Microsoft AppSource](https://appsource.microsoft.com/)或[Azure 应用商店](https://azuremarketplace.microsoft.com)中。 要了解有关发布到不同商业市场店面的信息，请参阅[确定发布选项](./determine-your-listing-type.md)。

登录到 Azure 门户后，客户可以选择市场导航元素来访问其专用套餐。 私有产品/服务也会显示在搜索结果中，并且可以通过命令行和 Azure 资源管理器模板（如任何其他产品/服务）进行部署。

![[专用套餐]](./media/marketplace-publishers-guide/private-offer.png)

专用套餐也会显示在搜索结果中。 只需查找"私人"徽章。

>[!Note]
>通过云解决方案提供商计划 （CSP） 的经销商建立的订阅不支持私人优惠。

<!---
## Next steps

To start using private offers, follow the steps in the [Private SKUs and Plans](./cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md) guide.

--->