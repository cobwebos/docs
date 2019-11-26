---
title: 标准协定 |Microsoft
description: Azure Marketplace 和 AppSource 的标准协定
services: Azure, Marketplace, Compute, Storage, Networking
author: ChJenk
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 11/19/2019
ms.author: v-chjen
ms.openlocfilehash: dc8edf2b6e4a1204e7edd515da9323896049eb13
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74228219"
---
# <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>Microsoft 商用 marketplace 的标准协定

为了简化客户的采购过程并降低软件供应商的法律复杂性，Microsoft 为 Microsoft 商用 marketplace 提供了标准合同，有助于促进 marketplace 中的交易。 商业 marketplace 发布者可以选择以标准合同提供其软件，而客户只需来审查并接受一次，而不是手工编写自定义条款和条件。 可在此处找到标准协定： [https://go.microsoft.com/fwlink/?linkid=2041178](https://go.microsoft.com/fwlink/?linkid=2041178)。

产品/服务的条款和条件是在合作伙伴中心或云合作伙伴门户中创建产品/服务时定义的。 你可以选择使用 Microsoft 商业应用商店的标准合同，而不是提供你自己的自定义条款和条件。

>[!Note]
>使用 Microsoft 商业应用商店的标准合同发布产品/服务后，将无法使用自己的自定义条款和条件。 它是 "或" 方案。 你可以在标准合同*或*你自己的条款和条件下提供解决方案。 如果要修改标准约定的条款，可以通过标准协定修正来实现。

## <a name="standard-contract-amendments"></a>标准协定改正

标准协定改正允许发布者为简单起见选择标准协定，并为其产品或企业提供自定义的条款。 如果客户已查看并接受 Microsoft 标准合同，则他们只需要查看对约定的修正。

对于商业 marketplace 发布者，可以使用两种改正：

* 通用修正：这些修正适用于所有客户的标准协定。 通用修正在购买流程中的每个产品/服务的客户上显示。 客户必须接受标准合同条款和修正条款，然后才能使用产品/服务。

* 自定义修正：这些修正是仅通过 Azure 租户 Id 面向特定客户的标准协定的特殊修正。 发布者可以选择要以其为目标的租户。 只有租户中的客户会看到产品/服务购买流程中的自定义修订条款。  客户必须接受标准合同条款和修正条款，然后才能使用产品/服务。

>[!Note]
>这两种类型的改正堆栈彼此之上。 以自定义修正为目标的客户也会在购买过程中获得标准合同的通用修正。

可以为以下产品/服务类型使用 Microsoft 商业应用商店的标准协定： Azure 应用程序（解决方案模板和托管应用程序）、虚拟机、容器、容器应用程序、IoT Edge 模块和 SaaS.

## <a name="customer-experience"></a>客户体验

在 Azure marketplace 或 AppSource 的发现体验期间，客户将能够查看与产品/服务关联的条款，作为 Microsoft 商业市场和任何通用修正的标准合同。

![Azure 门户客户发现体验。](media/marketplace-publishers-guide/azure-discovery-process.png)

在 Azure 门户的购买过程中，客户将能够查看与产品/服务关联的条款，作为 Microsoft 商业应用商店的标准合同以及任何通用和/或特定于租户的修正。

![Azure 门户客户购买体验。](media/marketplace-publishers-guide/azure-purchase-process.png)

## <a name="api"></a>API

客户可以使用 AzureRmMarketplaceTerms 来检索产品/服务的条款，并接受它。 标准协定和关联的修正案将在 cmdlet 的输出中返回。
