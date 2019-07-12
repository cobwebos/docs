---
title: 访问大量的 HYPER-V Vm 迁移到 Azure 中使用 Azure Migrate |Microsoft Docs
description: 介绍如何访问大量的 HYPER-V Vm 迁移到 Azure 中使用 Azure Migrate 服务。
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: raynew
ms.openlocfilehash: 95704f2694892b349d0967fca2160dabd990b472
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811539"
---
# <a name="assess-large-numbers-of-hyper-v-vms-for-migration-to-azure"></a>访问大量的 HYPER-V Vm 迁移到 Azure 的

本文介绍如何评估的本地 HYPER-V Vm 迁移到 Azure，使用 Azure Migrate Server 评估工具的较大的数字 (> 1000)。

[Azure Migrate](migrate-services-overview.md)提供一个中心工具，可帮助你发现、 评估和将应用程序、 基础结构和工作负荷迁移到 Microsoft Azure。 在中心包括 Azure 迁移工具和第三方独立软件供应商 (ISV) 产品/服务。 


在本文中，学习如何：
> [!div class="checklist"]
> * 对于大规模的评估计划。
> * 配置 Azure 权限，并准备进行评估的 HYPER-V。
> * 创建 Azure Migrate 项目，并创建评估。
> * 在规划迁移，请查看该评估。


> [!NOTE]
> 如果你想要试用的概念要大规模评估之前，评估几个 Vm，请遵循我们[系列教程](tutorial-prepare-hyper-v.md)

## <a name="plan-for-assessment"></a>评估计划

当规划大数量的 HYPER-V Vm 的评估，有几个事项需要注意：

- **计划 Azure Migrate 项目**:了解如何部署 Azure Migrate 项目。 例如，如果您的数据中心位于不同的地理区域，或者需要将发现、 评估或迁移相关的元数据存储在另一个地理位置，可能需要多个项目。
- **计划设备**:Azure Migrate 使用本地 Azure Migrate 设备，部署为 HYPER-V VM，以持续发现对于评估和迁移的 Vm。 设备监视环境更改，例如添加 Vm、 磁盘或网络适配器。 它还将有关它们的元数据和性能数据发送到 Azure。 您需要找出多少设备部署。


## <a name="planning-limits"></a>规划限制
 
使用用于规划此表中汇总的限制。

**规划** | **限制**
--- | --- 
**Azure Migrate 项目** | 评估要在项目中的最多 10,000 个 Vm。
**Azure Migrate 设备** | 一台设备可以发现最多 5000 个 Vm。<br/> 一台设备可以连接到最大 300 的 HYPER-V 主机。<br/> 一台设备只能与单个 Azure Migrate 项目相关联。<br/><br/> 
**Azure Migrate 评估** | 您可以评估单次评估最多 10,000 个 Vm。



## <a name="other-planning-considerations"></a>其他规划注意事项

- 若要从设备启动发现，你必须选择每个 HYPER-V 主机。 
- 如果要运行的多租户环境，你当前无法发现属于特定租户的 Vm。 

## <a name="prepare-for-assessment"></a>准备进行评估

准备 Azure 和 HYPER-V server 评估。 

1. 验证是否[HYPER-V 支持要求和限制](migrate-support-matrix-hyper-v.md)。
2. 设置 Azure 帐户与 Azure Migrate 进行交互的访问权限
3. 准备 HYPER-V 主机和虚拟机

按照中的说明[本教程](tutorial-prepare-hyper-v.md)配置这些设置。

## <a name="create-a-project"></a>创建一个项目

根据你的规划要求，执行以下步骤：

1. 创建 Azure Migrate 项目。
2. 向项目添加 Azure 迁移服务器评估工具。

[了解详细信息](how-to-add-tool-first-time.md)

## <a name="create-and-review-an-assessment"></a>创建和查看评估

1. 创建适用于 HYPER-V Vm 的评估。
1. 查看评估中针对迁移规划的准备。

[了解详细信息](tutorial-assess-hyper-v.md)有关创建和查看评估。
    

## <a name="next-steps"></a>后续步骤

本文内容：
 
> [!div class="checklist"] 
> * 计划扩展的 HYPER-V Vm 的 Azure Migrate 评估
> * 已准备好 Azure 和 HYPER-V 进行评估
> * 创建一个 Azure Migrate 项目并运行评估
> * 查看评估为迁移做准备。

现在，[了解如何](concepts-assessment-calculation.md)计算评估，以及如何[修改评估](how-to-modify-assessment.md)
