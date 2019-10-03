---
title: 通过 Azure Migrate 评估要迁移到 Azure 的大量 Hyper-v Vm |Microsoft Docs
description: 介绍如何使用 Azure Migrate 服务评估大量 Hyper-v Vm 以便迁移到 Azure。
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: raynew
ms.openlocfilehash: c1ae3a9ed8a775161aaf85ab2c91b1e43113d2e2
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70279434"
---
# <a name="assess-large-numbers-of-hyper-v-vms-for-migration-to-azure"></a>评估大量要迁移到 Azure 的 Hyper-v Vm

本文介绍如何使用 Azure Migrate Server 评估工具评估大量用于迁移到 Azure 的本地 Hyper-v Vm。

[Azure Migrate](migrate-services-overview.md) 在一个中心位置提供多种工具，帮助你发现、评估应用、基础结构和工作负荷并将其迁移到 Microsoft Azure。 该中心包含 Azure Migrate 工具，以及第三方独立软件供应商 (ISV) 的产品/服务。 


在本文中，学习如何：
> [!div class="checklist"]
> * 大规模规划评估。
> * 配置 Azure 权限，并准备 Hyper-v 进行评估。
> * 创建 Azure Migrate 项目，并创建评估。
> * 在规划迁移时，请查看评估。


> [!NOTE]
> 如果要在评估规模之前尝试使用概念证明来评估几个 Vm，请遵循我们的[系列教程](tutorial-prepare-hyper-v.md)

## <a name="plan-for-assessment"></a>规划评估

规划大量 Hyper-v Vm 的评估时，需要考虑以下几个问题：

- **规划 Azure Migrate 项目**：确定如何部署 Azure Migrate 项目。 例如，如果你的数据中心位于不同的地理位置，或者你需要在不同的地理位置存储发现、评估或与迁移相关的元数据，则可能需要多个项目。
- **规划设备**：Azure Migrate 使用部署为 Hyper-v VM 的本地 Azure Migrate 设备，以持续发现用于评估和迁移的 Vm。 设备会监视环境更改，如添加 Vm、磁盘或网络适配器。 它还会将有关它们的元数据和性能数据发送到 Azure。 需要确定要部署的设备的数量。


## <a name="planning-limits"></a>规划限制
 
使用此表中汇总的限制进行规划。

**规划** | **限制**
--- | --- 
**Azure Migrate 项目** | 在项目中评估最多35000个 Vm。
**Azure Migrate 设备** | 设备最多可以发现 5000 Vm。<br/> 一个设备最多可以连接到300个 Hyper-v 主机。<br/> 设备只能与单个 Azure Migrate 项目相关联。<br/> 可以将任意数量的设备与单个 Azure Migrate 项目相关联。 <br/><br/> 
**组** | 最多可以在一个组中添加35000个 Vm。
**Azure Migrate 评估** | 在单个评估中，最多可以评估 35000 Vm。



## <a name="other-planning-considerations"></a>其他规划注意事项

- 若要从设备启动发现，你必须选择每个 Hyper-v 主机。 
- 如果正在运行多租户环境，则当前无法仅发现属于特定租户的 Vm。 

## <a name="prepare-for-assessment"></a>准备评估

为服务器评估准备 Azure 和 Hyper-v。 

1. 验证[hyper-v 支持要求和限制](migrate-support-matrix-hyper-v.md)。
2. 设置 Azure 帐户的权限以与 Azure Migrate 进行交互
3. 准备 Hyper-v 主机和 Vm

按照[本教程](tutorial-prepare-hyper-v.md)中的说明配置这些设置。

## <a name="create-a-project"></a>创建一个项目

按照规划要求，执行以下操作：

1. 创建 Azure Migrate 项目。
2. 将 Azure Migrate 服务器评估工具添加到项目。

[了解详细信息](how-to-add-tool-first-time.md)

## <a name="create-and-review-an-assessment"></a>创建和查看评估

1. 为 Hyper-v Vm 创建评估。
1. 查看评估以准备迁移规划。

[了解](tutorial-assess-hyper-v.md)有关创建和查看评估的详细信息。
    

## <a name="next-steps"></a>后续步骤

本文内容：
 
> [!div class="checklist"] 
> * 计划为 Hyper-v Vm 缩放 Azure Migrate 评估
> * 已准备好 Azure 和 Hyper-v 以进行评估
> * 创建 Azure Migrate 项目并运行评估
> * 查看评估以准备迁移。

现在，[了解如何](concepts-assessment-calculation.md)计算评估，以及如何[修改评估](how-to-modify-assessment.md)
