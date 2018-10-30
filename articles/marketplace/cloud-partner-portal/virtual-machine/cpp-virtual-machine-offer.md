---
title: Azure 市场中的虚拟机套餐 | Microsoft Docs
description: 在 Azure 市场中发布 VM 套餐的过程概述。
services: Azure, Marketplace, Cloud Partner Portal
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 09/28/2018
ms.author: pbutlerm
ms.openlocfilehash: d3682d18fb849b2d851bae0986f9e61f216aaf2c
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/22/2018
ms.locfileid: "49638874"
---
# <a name="virtual-machine-offer"></a>虚拟机套餐

本部分概述发布虚拟机 (VM) 的要素，旨在指导用户在 [Azure 市场](https://azuremarketplace.microsoft.com)中发布套餐。  从此角度来看，此过程主要划分为以下组成部分：

- [先决条件](./cpp-prerequisites.md) - 列出创建或发布 VM 套餐之前所要满足的技术和业务要求
- [创建 VM 套餐](./cpp-create-offer.md) - 列出使用[云合作伙伴门户](https://cloudpartner.azure.com)创建新 VM 套餐项所要执行的步骤。
- [创建 VM 技术资产](./cpp-create-technical-assets.md) - 如何为 VM 解决方案创建技术资产，以及如何将此包配置为 Azure 市场中的 VM 套餐
- [发布 VM 套餐](./cpp-publish-offer.md) - 如何提交套餐，以便发布 Azure 市场


## <a name="vm-publishing-process-flow"></a>VM 发布流程

下图演示了发布 VM 套餐中的概要步骤。 

![VM 发布过程](./media/publishvm_001.png)

1. 创建套餐 - 配置有关套餐的所有详细信息，包括套餐说明、营销材料、法律文档、支持信息和资产规格。

2. 创建业务和技术资产 - 创建关联的解决方案（此处为 VM 和附加的磁盘）的业务资产（法律文档和营销材料）及技术资产。 

3. 创建 SKU - 创建并提交与套餐关联的 SKU。  必须为要发布的每个映像指定唯一的 SKU。 
 
4. 认证并发布套餐 - 填写套餐和技术资产后，可以提交该套餐。 提交后会启动发布过程，在此期间，会测试、验证并认证该解决方案，然后在市场中将解决方案“上线”。  

## <a name="next-steps"></a>后续步骤

在执行这些步骤之前，必须满足有关将 VM 发布到 Microsoft Azure 市场的[技术和业务要求](./cpp-prerequisites.md)。 
