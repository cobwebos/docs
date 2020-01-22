---
title: 通过 Azure Migrate 评估用于迁移到 Azure 的大量物理服务器 |Microsoft Docs
description: 介绍如何使用 Azure Migrate 服务评估大量物理服务器以迁移到 Azure。
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 01/19/2020
ms.author: hamusa
ms.openlocfilehash: a19a1b6e7416667079ab07fc5440ee8828c26bf4
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/21/2020
ms.locfileid: "76294365"
---
# <a name="assess-large-numbers-of-physical-servers-for-migration-to-azure"></a>评估大量物理服务器以迁移到 Azure

本文介绍如何使用 Azure Migrate Server 评估工具评估大量用于迁移到 Azure 的本地物理服务器。

[Azure Migrate](migrate-services-overview.md) 在一个中心位置提供多种工具，帮助你发现、评估应用、基础结构和工作负荷并将其迁移到 Microsoft Azure。 该中心包含 Azure Migrate 工具，以及第三方独立软件供应商 (ISV) 的产品/服务。 


在本文中，学习如何：
> [!div class="checklist"]
> * 大规模规划评估。
> * 配置 Azure 权限，并准备物理服务器进行评估。
> * 创建 Azure Migrate 项目，并创建评估。
> * 在规划迁移时，请查看评估。


> [!NOTE]
> 如果要在评估规模之前尝试使用概念证明来评估一些服务器，请遵循我们的[系列教程](tutorial-prepare-physical.md)。

## <a name="plan-for-assessment"></a>规划评估

规划大量物理服务器的评估时，需要考虑几个问题：

- **规划 Azure Migrate 项目**：了解如何部署 Azure Migrate 项目。 例如，如果你的数据中心位于不同的地理位置，或者你需要在不同的地理位置存储发现、评估或与迁移相关的元数据，则可能需要多个项目。
- **计划设备**： Azure Migrate 使用部署在 Windows 计算机上的本地 Azure Migrate 设备来持续发现服务器以进行评估和迁移。 设备会监视环境更改，如添加 Vm、磁盘或网络适配器。 它还会将有关它们的元数据和性能数据发送到 Azure。 需要确定要部署的设备的数量。


## <a name="planning-limits"></a>规划限制
 
使用此表中汇总的限制进行规划。

**规划** | **限制**
--- | --- 
**Azure Migrate 项目** | 在项目中评估最多35000服务器。
**Azure Migrate 设备** | 设备最多可以发现250服务器。<br/> 设备只能与单个 Azure Migrate 项目相关联。<br/> 可以将任意数量的设备与单个 Azure Migrate 项目相关联。 <br/><br/> 
**分组** | 最多可以在一个组中添加35000服务器。
**Azure Migrate 评估** | 在单个评估中，最多可以评估35000服务器。


## <a name="other-planning-considerations"></a>其他规划注意事项

- 若要从设备启动发现，你必须选择每个物理服务器。 

## <a name="prepare-for-assessment"></a>准备评估

为服务器评估准备 Azure 和物理服务器。 

1. 验证[物理服务器支持的要求和限制](migrate-support-matrix-physical.md)。
2. 设置你的 Azure 帐户的权限以与 Azure Migrate 进行交互。
3. 准备物理服务器。

按照[本教程](tutorial-prepare-physical.md)中的说明配置这些设置。

## <a name="create-a-project"></a>创建一个项目

按照规划要求，执行以下操作：

1. 创建 Azure Migrate 项目。
2. 将 Azure Migrate 服务器评估工具添加到项目。

[了解详细信息](how-to-add-tool-first-time.md)

## <a name="create-and-review-an-assessment"></a>创建和查看评估

1. 为物理服务器创建评估。
1. 查看评估以准备迁移规划。

[了解](tutorial-assess-physical.md)有关创建和查看评估的详细信息。
    

## <a name="next-steps"></a>后续步骤

本文内容：
 
> [!div class="checklist"] 
> * 计划扩展物理服务器 Azure Migrate 评估。
> * 已准备好 Azure 和物理服务器进行评估。
> * 创建 Azure Migrate 项目并运行评估。
> * 查看评估以准备迁移。

现在，[了解如何](concepts-assessment-calculation.md)计算评估，以及如何[修改评估](how-to-modify-assessment.md)。
