---
title: 标准协定 |Azure
description: Azure Marketplace 和 AppSource 中的标准约定
services: Azure, Marketplace, Compute, Storage, Networking
author: qianw211
ms.service: marketplace
ms.topic: article
ms.date: 07/05/2019
ms.author: ellacroi
ms.openlocfilehash: 80c157423572d356026f257e81d52650ce01d3e8
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2019
ms.locfileid: "67620383"
---
# <a name="standard-contract"></a>标准合同

若要简化客户的采购过程并降低的软件供应商的法律的复杂性，Microsoft 以帮助简化在 marketplace 中的事务提供标准协定模板。 而不是编写自定义条款和条件，Azure Marketplace 发布者可以选择提供标准的合同，客户只需以审查并接受一次软件。 标准协定可在此处找到： [ https://go.microsoft.com/fwlink/?linkid=2041178 ](https://go.microsoft.com/fwlink/?linkid=2041178)。 

在云合作伙伴门户中创建产品/服务时，是在 Marketplace 选项卡上定义的条款和条件的产品/服务。 通过将设置更改为是启用标准协定选项。

![启用标准约定选项](media/marketplace-publishers-guide/standard-contract.png)

>[!Note] 
>选择要使用标准的协定，单独的条款和条件是否仍需要针对[云解决方案提供商](./cloud-solution-providers.md)通道。

## <a name="standard-contract-amendments"></a>标准的合同修正

标准的合同修正允许发布者可选择为简单起见，并使用其产品或业务的自定义条款的标准约定。  客户只需查看修正合同，如果他们已查看并接受 Microsoft 标准合同。

有两种类型的修正适用于 Azure Marketplace 发布者：

* 通用修正：这些修正将全局应用于所有客户的标准约定。 向每个客户的产品中的采购流程显示通用修正。

![通用修正](media/marketplace-publishers-guide/universal-amendaments.png)

* 自定义修正：Azure Marketplace 还提供面向租户的自定义修正设置。 它们是标准约定以仅某些客户为目标的特殊修正。 发布者可以选择他们想要面向的租户。 从该租户的客户将购买标准协定及其目标的修正的产品。

![自定义修正](media/marketplace-publishers-guide/custom-amendaments.png)

>[!Note] 
>目标具有自定义修正客户还将了解到在购买过程的标准条款通用的修正合同。

>[!Note]
>以下产品/服务类型支持标准的合同修正：Azure 应用程序 （解决方案模板和托管应用程序）、 虚拟机、 容器、 容器应用程序。

### <a name="customer-experience"></a>客户体验

在 Azure 门户中购买过程中，客户将能够看到作为 Microsoft 标准合同和修正与产品相关的条款。

![Azure 门户的客户体验。](media/marketplace-publishers-guide/ibiza-customer-experience.png)

### <a name="api"></a>API

客户可以使用`Get-AzureRmMarketplaceTerms`若要检索的产品/服务条款并接受它。 该 cmdlet 的输出中，将返回标准协定及其相关联的修正。

---
