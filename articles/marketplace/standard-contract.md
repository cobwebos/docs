---
title: 标准合同 |Azure 应用商店
description: Azure 应用商店和应用源的标准协定
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: dsindona
ms.openlocfilehash: facb500299f7196e0e1387c3a7267a917d13a3a6
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/21/2020
ms.locfileid: "81681445"
---
# <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>微软商业市场的标准合同

为了简化客户的采购流程并降低软件供应商的法律复杂性，Microsoft 为 Microsoft 商业市场提供了标准合同，以帮助促进市场交易。 商业市场出版商可以选择根据标准合同提供他们的软件，而不是制定自定义条款和条件，客户只需审查并接受一次。 标准合同可以在这里找到： [https://go.microsoft.com/fwlink/?linkid=2041178](https://go.microsoft.com/fwlink/?linkid=2041178).

在合作伙伴中心创建产品/服务时，将定义优惠的条款和条件。 您可以选择为 Microsoft 商业市场使用标准合同，而不是提供您自己的自定义条款和条件。

>[!Note]
>使用 Microsoft 商业市场的标准合约发布产品/服务后，您将无法使用自己的自定义条款和条件。 这是一个"或"方案。 您要么根据标准合同*或*您自己的条款和条件提供您的解决方案。 如果您想修改标准合同的条款，您可以通过标准合同修订来修改。

## <a name="standard-contract-amendments"></a>标准合同修订

标准合同修订允许发布商选择标准合同，以便简单，并为其产品或业务提供自定义条款。 客户只需审核并接受 Microsoft 标准合同，就只需查看合同的修订。

商业市场出版商有两种修正案：

* 普遍修改：这些修订适用于所有客户的标准合同。 通用修订显示在采购流程中向产品/服务的每个客户。 客户必须接受标准合同的条款和修改，然后才能使用您的优惠。

* 自定义修订：这些修订是标准合同的特别修订，仅通过 Azure 租户 ID 针对特定客户。 发布者可以选择要定位的租户。 只有租户的客户才会在优惠的采购流程中收到自定义修改条款。  客户必须接受标准合同的条款和修改，然后才能使用您的优惠。

>[!Note]
>这两种类型的修正相互叠加。 以定制修订为目标的客户在购买过程中还将获得标准合同的普遍修订。

您可以利用 Microsoft 商业市场的标准合同来获取以下产品类型：Azure 应用程序（解决方案模板和托管应用程序）、虚拟机、容器、容器应用程序、IoT 边缘模块和 SaaS。

## <a name="customer-experience"></a>客户体验

在 Azure 市场或 AppSource 中的发现体验期间，客户将能够看到与产品/服务相关的条款，即 Microsoft 商业市场的标准合同以及任何通用修订。

![Azure 门户客户发现体验。](media/marketplace-publishers-guide/azure-discovery-process.png)

在 Azure 门户中的购买过程中，客户将能够将与产品/服务相关的条款视为 Microsoft 商业市场的标准合同以及任何通用和/或特定于租户的修订。

![Azure 门户客户购买体验。](media/marketplace-publishers-guide/azure-purchase-process.png)

## <a name="api"></a>API

客户可以使用获取 AzureRmMarketplace术语检索产品/服务的条款并接受它。 标准合同和相关修订将在 cmdlet 的输出中返回。
