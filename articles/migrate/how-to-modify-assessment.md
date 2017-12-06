---
title: "自定义 Azure 迁移评估设置 | Microsoft Docs"
description: "介绍如何使用 Azure Migration Planner 设置和运行将 VMware VM 迁移到 Azure 的评估"
services: migrate
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: a068b9c7-5f87-4fe1-90b9-3be48d91aa3f
ms.service: migrate
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/21/2017
ms.author: raynew
ms.openlocfilehash: 20aeb2073ad307952f92c8377bc9d78169f1756c
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/29/2017
---
# <a name="customize-an-assessment"></a>自定义评估

[Azure Migrate](migrate-overview.md) 使用默认设置创建评估。 创建评估后，可以使用本文中的说明修改这些默认设置。


## <a name="edit-assessment-values"></a>编辑评估值

1. 在 Azure Migrate 项目的“评估”页上选择评估，然后单击“编辑属性”。
2. 根据下表修改设置。

    **设置** | **详细信息** | **默认**
    --- | --- | ---
    **目标位置** | 要迁移到的 Azure 位置。 |  美国西部 2 是默认位置。
    **存储冗余** | Azure VM 会在迁移后使用的存储类型。 | 目前仅支持[本地冗余存储 (LRS)](../storage/common/storage-redundancy.md#locally-redundant-storage) 复制。
    **舒适因子** | 舒适因子是在评估过程中使用的缓冲区。 使用它来应对季节性使用、短性能历史记录、将来的可能增长。 | 默认设置是1.3x。
    **性能历史记录** | 在评估性能历史记录时使用的时间。 | 默认值为一个月。
    **百分位使用率** | 要考虑性能历史记录的百分位值。 | 默认为 95%。
    **定价层** | 可以指定 VM 的[定价层](https://azure.microsoft.com/blog/basic-tier-virtual-machines-2/)。  | 默认使用[标准](../virtual-machines/windows/sizes-general.md)层。
    **产品** | 适用的 [Azure 产品](https://azure.microsoft.com/support/legal/offer-details/)。 | [即用即付](https://azure.microsoft.com/offers/ms-azr-0003p/)是默认设置。
    **货币** | 计费货币。 | 默认为美元。
    **折扣 (%)** | 在任何产品上收到的任何特定订阅的折扣。 | 默认设置是 0%。
    **Azure 混合使用权益** | 指示是否要在 [Azure 混合使用权益](https://azure.microsoft.com/pricing/hybrid-use-benefit/)中登记。 如果设置为“是”，则考虑 Windows VM 的非 Windows Azure 价格。 | 默认值为“是”。

3. 单击“保存”更新评估。


## <a name="next-steps"></a>后续步骤

[详细了解](concepts-assessment-calculation.md)评估计算方式。
