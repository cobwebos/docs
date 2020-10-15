---
title: Microsoft 商业市场的标准协定
description: 合作伙伴中心的 Azure 市场和 AppSource 的标准协定
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 05/20/2020
ms.openlocfilehash: 3079cb18f7bc88d691081a7970f5833e8b8d6887
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2020
ms.locfileid: "92078181"
---
# <a name="standard-contract-for-microsoft-commercial-marketplace"></a>Microsoft 商业市场的标准协定

Microsoft 为 Microsoft 商业市场提供标准协定。 这有助于简化客户的采购流程，为软件供应商降低法务方面的复杂性，并促进市场交易。 作为商业市场中的发行商，你可以选择在[标准协定](https://go.microsoft.com/fwlink/?linkid=2041178)下提供你的软件，而不是制定自定义条款和条件，客户只需要审查和接受一次即可使用产品。

在合作伙伴中心中创建产品/服务时，需要定义产品/服务的条款和条件。 可以选择使用 Microsoft 商业市场的标准协定，而不是提供自己的自定义条款和条件。

>[!Note]
>使用 Microsoft 商业应用商店的标准合同发布产品/服务后，将无法使用自己的自定义条款和条件。 可以根据标准协定或自己的条款和条件提供解决方案。 自定义条款和条件在服务级别定义，适用于所有计划;在合作伙伴中心的产品/服务的 " **属性** " 页中编写自定义条款和条件。 如果要修改标准协定的条款，可以通过标准协定改正来实现。

## <a name="standard-contract-amendments"></a>标准协定修正

“标准协定修正”允许发布者为简单起见选择标准协定的同时为其产品或业务使用自定义条款。 如果客户已经查看并接受了 Microsoft 标准协定，只需要查看对协定的修正内容即可。

有两种修正可供商业市场发行商使用：

* 通用修正：这些修正内容适用于所有客户的标准协定。 在购买流程中，将向产品/服务的所有客户显示通用修正。 客户在使用产品/服务之前，必须接受标准协定的条款和修正内容。

* 自定义修正：这些修正是对标准协定的特殊修正内容，仅通过 Azure 租户 ID 面向特定客户提供。 发行商可以选择要面向的租户。 只有租户中的客户才能在产品/服务的购买流程中看到自定义修正条款。  客户在使用产品/服务之前，必须接受标准协定的条款和修正。

>[!Note]
>这两种修正相互叠加。 在购买过程中，可看到自定义修正内容的客户也会看到标准协定的通用修正内容。 修正范围限制为4000个字符，包括空格。

可以利用 Microsoft 商业市场的标准协定提供以下类型的产品/服务：Azure 应用程序（解决方案模板和托管应用程序）、虚拟机和 SaaS。

## <a name="customer-experience"></a>客户体验

在 Azure 市场或 AppSource 的发现体验过程中，客户将能够看到与产品/服务相关的 Microsoft 商业市场的标准协定条款和任何通用修正内容。

![Azure 门户客户发现体验。](media/marketplace-publishers-guide/azure-discovery-process.png)

在 Azure 门户的购买流程中，客户将能够看到与产品/服务相关的 Microsoft 商业市场的标准协定条款和任何通用的和/或特定于租户的修正内容。

![Azure 门户客户购买体验。](media/marketplace-publishers-guide/azure-purchase-process.png)

## <a name="api"></a>API

客户可以使用 Get-AzureRmMarketplaceTerms 检索产品/服务的条款并接受它。 Cmdlet 的输出中会返回标准协定和相关修正内容。
