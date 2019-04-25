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
ms.date: 12/04/2018
ms.author: pbutlerm
ms.openlocfilehash: bbe757ccd1d6a37cbcf04f3ecd6dd088ef1ff211
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60343096"
---
# <a name="virtual-machine-offer"></a>虚拟机套餐

|    |    |
|-----------------------------------------------------------------|------------------------------------------|
| 本部分介绍如何将新的虚拟机产品/服务发布到 [Azure 市场](https://azuremarketplace.microsoft.com)。 支持基于 Windows 和基于 Linux 的虚拟机（包含操作系统虚拟硬盘 (VHD) 以及零个或多个数据虚拟硬盘）。 | ![虚拟机图标](./media/virtual-machine-icon.png)  |


## <a name="publishing-overview"></a>发布概述

视频 [Optimize Your Azure Marketplace Offer](https://channel9.msdn.com/Events/Build/2017/P4026?ocid=player)（优化 Azure 市场套餐）详细概述了 Azure 市场，包括如何在该市场（使用虚拟机解决方案）进行发布、如何使用产品页面和可选的“体验版”体验优化用户体验、如何生成用户引导和使用用户引导，以及如何优化客户参与。

> [!VIDEO https://channel9.msdn.com/Events/Build/2017/P4026/player]


## <a name="vm-publishing-process-flow"></a>VM 发布流程

下图演示了发布 VM 套餐中的概要步骤。 

![VM 发布过程](./media/publishvm_001.png)

1. 创建套餐 - 配置有关套餐的所有详细信息，包括套餐说明、营销材料、法律文档、支持信息和资产规格。

2. 创建业务和技术资产 - 创建关联的解决方案（此处为 VM 和附加的磁盘）的业务资产（法律文档和营销材料）及技术资产。 

3. 创建 SKU - 创建并提交与套餐关联的 SKU。  必须为要发布的每个映像指定唯一的 SKU。 
 
4. 认证并发布套餐 - 填写套餐和技术资产后，可以提交该套餐。 提交后会启动发布过程，在此期间，会测试、验证并认证该解决方案，然后在市场中将解决方案“上线”。  

## <a name="next-steps"></a>后续步骤

在执行这些步骤之前，必须满足有关将 VM 发布到 Microsoft Azure 市场的[技术和业务要求](./cpp-prerequisites.md)。 
