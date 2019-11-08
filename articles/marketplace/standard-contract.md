---
title: 标准协定 |Microsoft
description: Azure Marketplace 和 AppSource 中的标准协定
services: Azure, Marketplace, Compute, Storage, Networking
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 07/05/2019
ms.author: ellacroi
ms.openlocfilehash: 17c1bf9d20b6f2e3ec450ff7bfb54fe61494ff09
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819494"
---
# <a name="standard-contract"></a>标准合同

为了简化客户的采购过程并降低软件供应商的法律复杂性，Microsoft 提供了标准合同模板，以帮助促进 marketplace 中的事务。 Azure Marketplace 发布者可以选择在标准合同下提供其软件，而客户只需要来审查和接受一次，而不是手工编写自定义条款和条件。 可在此处找到标准协定： [https://go.microsoft.com/fwlink/?linkid=2041178](https://go.microsoft.com/fwlink/?linkid=2041178)。 

在云合作伙伴门户中创建产品/服务时，将在 "Marketplace" 选项卡上定义产品/服务的条款和条件。 通过将设置更改为 "是"，可以启用标准协定选项。

![启用标准协定选项](media/marketplace-publishers-guide/standard-contract.png)

>[!Note] 
>如果选择使用标准协定，则[云解决方案提供商](./cloud-solution-providers.md)通道仍需要单独的条款和条件。

## <a name="standard-contract-amendments"></a>标准协定改正

标准协定改正允许发布者为简单起见选择标准协定，并为其产品或企业提供自定义的条款。  如果客户已查看并接受 Microsoft 标准合同，则他们只需要查看对约定的修正。

Azure Marketplace 发布服务器提供两种改正：

* 通用修正：这些修正适用于所有客户的标准协定。 全局修正将显示在购买流程中产品的每个客户上。

![通用修正](media/marketplace-publishers-guide/universal-amendaments.png)

* 自定义修正： Azure Marketplace 还提供了针对租户的自定义修正设置。 它们是仅面向特定客户的标准合同的特殊修正。 发布者可以选择要以其为目标的租户。 该租户中的客户将按标准合同和目标修正案购买产品。

![自定义改正](media/marketplace-publishers-guide/custom-amendaments.png)

>[!Note] 
>以自定义修正为目标的客户也会在购买过程中获得标准条款的通用修订。

>[!Note]
>以下产品/服务类型支持标准约定修正： Azure 应用程序（解决方案模板和托管应用程序）、虚拟机、容器、容器应用程序。

### <a name="customer-experience"></a>客户体验

在 Azure 门户中的购买过程中，客户将能够查看与该产品相关的条款，如 Microsoft 标准合同和修正条款。

![Azure 门户客户体验。](media/marketplace-publishers-guide/ibiza-customer-experience.png)

### <a name="api"></a>API

客户可以使用 `Get-AzureRmMarketplaceTerms` 来检索产品/服务的条款，并接受该产品/服务。 标准协定和关联的修正案将在 cmdlet 的输出中返回。

---
