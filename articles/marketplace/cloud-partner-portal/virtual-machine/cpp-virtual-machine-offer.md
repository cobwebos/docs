---
title: Azure Marketplace 中的虚拟机产品/服务
description: 在 Azure 市场中发布 VM 套餐的过程概述。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/04/2018
ms.author: dsindona
ms.openlocfilehash: b857cf8b7485beb884adf77d99f82d965f55a0ad
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "82142802"
---
# <a name="virtual-machine-offer"></a>虚拟机套餐

> [!IMPORTANT]
> 从2020年4月13日开始，我们将开始将 Azure 虚拟机产品/服务的管理转移到合作伙伴中心。 迁移后，你将在合作伙伴中心创建和管理你的产品/服务。 按照[创建 Azure 虚拟机产品/服务](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-vm-create-offer)中的说明来管理迁移的产品/服务。

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
