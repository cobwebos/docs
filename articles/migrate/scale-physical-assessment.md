---
title: 使用 Azure 迁移评估大量物理服务器以迁移到 Azure |微软文档
description: 介绍如何使用 Azure 迁移服务评估大量物理服务器以迁移到 Azure。
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 01/19/2020
ms.author: hamusa
ms.openlocfilehash: a19a1b6e7416667079ab07fc5440ee8828c26bf4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76294365"
---
# <a name="assess-large-numbers-of-physical-servers-for-migration-to-azure"></a>评估大量物理服务器以迁移到 Azure

本文介绍如何使用 Azure 迁移服务器评估工具评估大量本地物理服务器以迁移到 Azure。

[Azure Migrate](migrate-services-overview.md) 在一个中心位置提供多种工具，帮助你发现、评估应用、基础结构和工作负荷并将其迁移到 Microsoft Azure。 该中心包含 Azure Migrate 工具，以及第三方独立软件供应商 (ISV) 的产品/服务。 


在本文中，学习如何：
> [!div class="checklist"]
> * 计划规模评估。
> * 配置 Azure 权限，并准备物理服务器进行评估。
> * 创建 Azure 迁移项目，并创建评估。
> * 在规划迁移时查看评估。


> [!NOTE]
> 如果你想尝试一个概念验证，以评估几个服务器之前评估规模，请按照我们的[教程系列](tutorial-prepare-physical.md)。

## <a name="plan-for-assessment"></a>评估计划

在规划评估大量物理服务器时，需要考虑几点：

- **规划 Azure 迁移项目**：了解如何部署 Azure 迁移项目。 例如，如果数据中心位于不同的地理位置，或者您需要将发现、评估或迁移相关的元数据存储在不同的地理位置，则可能需要多个项目。
- **计划设备**：Azure 迁移使用部署在 Windows 计算机上的本地 Azure 迁移设备来不断发现用于评估和迁移的服务器。 设备监视环境更改，如添加 VM、磁盘或网络适配器。 它还会向 Azure 发送有关元数据和性能数据的元数据和性能数据。 您需要确定要部署的设备数。


## <a name="planning-limits"></a>规划限制
 
使用此表中总结的限制进行规划。

**规划** | **限制**
--- | --- 
**Azure 迁移项目** | 评估项目中多达 35，000 台服务器。
**Azure Migrate 设备** | 设备最多可以发现 250 台服务器。<br/> 设备只能与单个 Azure 迁移项目关联。<br/> 任意数量的设备都可以与单个 Azure 迁移项目相关联。 <br/><br/> 
**组** | 单个组中最多可以添加 35，000 台服务器。
**Azure 迁移评估** | 您可以在单个评估中评估多达 35，000 台服务器。


## <a name="other-planning-considerations"></a>其他规划注意事项

- 要从设备开始发现，您必须选择每个物理服务器。 

## <a name="prepare-for-assessment"></a>准备评估

准备 Azure 和物理服务器以进行服务器评估。 

1. 验证[物理服务器支持要求和限制](migrate-support-matrix-physical.md)。
2. 设置 Azure 帐户与 Azure 迁移交互的权限。
3. 准备物理服务器。

按照[本教程](tutorial-prepare-physical.md)中的说明配置这些设置。

## <a name="create-a-project"></a>创建一个项目

根据您的规划要求，请执行以下操作：

1. 创建 Azure Migrate 项目。
2. 将 Azure 迁移服务器评估工具添加到项目中。

[了解详细信息](how-to-add-tool-first-time.md)

## <a name="create-and-review-an-assessment"></a>创建和审核评估

1. 为物理服务器创建评估。
1. 审查评估，为迁移规划做准备。

[详细了解](tutorial-assess-physical.md)创建和审核评估。
    

## <a name="next-steps"></a>后续步骤

本文内容：
 
> [!div class="checklist"] 
> * 计划为物理服务器缩放 Azure 迁移评估。
> * 已准备好的 Azure 和物理服务器进行评估。
> * 创建了 Azure 迁移项目并运行了评估。
> * 审查了为迁移做准备的评估。

现在，[了解如何](concepts-assessment-calculation.md)计算评估，以及如何[修改评估](how-to-modify-assessment.md)。
