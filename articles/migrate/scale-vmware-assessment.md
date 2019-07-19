---
title: 通过 Azure Migrate 评估要迁移到 Azure 的大量 VMware Vm |Microsoft Docs
description: 介绍如何使用 Azure Migrate 服务评估大量 VMware Vm 以便迁移到 Azure。
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/12/2019
ms.author: hamusa
ms.openlocfilehash: c9c57a07100f2ea6db86408826bf74d05c8df5aa
ms.sourcegitcommit: 10251d2a134c37c00f0ec10e0da4a3dffa436fb3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/13/2019
ms.locfileid: "67868680"
---
# <a name="assess-large-numbers-of-vmware-vms-for-migration-to-azure"></a>评估要迁移到 Azure 的大量 VMware Vm


本文介绍如何使用 Azure Migrate Server 评估工具评估要迁移到 Azure 的本地 VMware Vm 的大数字 (1000-35000)

[Azure Migrate](migrate-services-overview.md)提供了一种工具中心, 可帮助你发现、评估和迁移应用、基础结构和工作负载以 Microsoft Azure。 该中心包括 Azure Migrate 工具和第三方独立软件供应商 (ISV) 产品。 

在本文中，学习如何：
> [!div class="checklist"]
> * 大规模规划评估。
> * 配置 Azure 权限, 并准备 VMware 进行评估。
> * 创建 Azure Migrate 项目, 并创建评估。
> * 在规划迁移时, 请查看评估。


> [!NOTE]
> 如果要在评估规模之前尝试使用概念证明来评估几个 Vm, 请遵循我们的[系列教程](tutorial-prepare-vmware.md)

## <a name="plan-for-assessment"></a>规划评估

规划大量 VMware Vm 的评估时, 需要考虑几个问题:

- **规划 Azure Migrate 项目**:确定如何部署 Azure Migrate 项目。 例如, 如果你的数据中心位于不同的地理位置, 或者你需要在不同的地理位置存储发现、评估或与迁移相关的元数据, 则可能需要多个项目。 
- **规划设备**:Azure Migrate 使用部署为 VMware VM 的本地 Azure Migrate 设备来持续发现 Vm。 设备会监视环境更改, 如添加 Vm、磁盘或网络适配器。 它还会将有关它们的元数据和性能数据发送到 Azure。 需要找出需要部署的设备的数量。
- **计划发现帐户**:Azure Migrate 设备使用有权访问 vCenter Server 的帐户, 以便发现用于评估和迁移的 Vm。 如果发现超过10000个 Vm, 请设置多个帐户。


## <a name="planning-limits"></a>规划限制
 
使用此表中汇总的限制进行规划。

**规划** | **限制**
--- | --- 
**Azure Migrate 项目** | 在项目中评估最多35000个 Vm。
**Azure Migrate 设备** | 设备只能连接到单个 vCenter Server。<br/><br/> 设备只能与单个 Azure Migrate 项目相关联。<br/> 设备最多可以发现 vCenter Server 上的 10000 Vm。
**Azure Migrate 评估** | 在单个评估中, 最多可以评估 35000 Vm。

考虑到这些限制, 以下是一些示例部署:


**vCenter 服务器** | **服务器上的 Vm** | **建议** | **Action**
---|---|---
一种 | < 10,000 | 一个 Azure Migrate 项目。<br/> 一台设备。<br/> 一个用于发现的 vCenter 帐户。 | 设置设备, 使用帐户连接到 vCenter Server。
一种 | > 10,000 | 一个 Azure Migrate 项目。<br/> 多个设备。<br/> 多个 vCenter 帐户。 | 为每个 10000 Vm 设置设备。<br/><br/> 设置 vCenter 帐户, 并划分清单, 将帐户的访问权限限制为小于 10000 Vm。<br/> 使用帐户将每个设备连接到 vCenter 服务器。<br/> 你可以分析在不同设备上发现的计算机的依赖关系。
多个 | < 10,000 |  一个 Azure Migrate 项目。<br/> 多个设备。<br/> 一个用于发现的 vCenter 帐户。 | 设置设备, 使用帐户连接到 vCenter Server。<br/> 你可以分析在不同设备上发现的计算机的依赖关系。
多个 | > 10,000 | 一个 Azure Migrate 项目。<br/> 多个设备。<br/> 多个 vCenter 帐户。 | 如果 vCenter Server 发现 < 10000 Vm, 请为每个 vCenter Server 设置一个设备。<br/><br/> 如果 vCenter Server 发现 > 10000 Vm, 请为每个 10000 Vm 设置一个设备。<br/> 设置 vCenter 帐户, 并划分清单, 将帐户的访问权限限制为小于 10000 Vm。<br/> 使用帐户将每个设备连接到 vCenter 服务器。<br/> 你可以分析在不同设备上发现的计算机的依赖关系。


## <a name="plan-discovery-in-a-multi-tenant-environment"></a>在多租户环境中规划发现

如果你计划使用多租户环境, 则可以在 vCenter Server 上确定发现的范围。

- 可以将设备发现范围设置为 vCenter Server 数据中心、群集或群集文件夹、主机或主机的主机或单个 Vm。
- 如果你的环境在租户之间共享, 并且你想要单独发现每个租户, 则可以将对该设备用于发现的 vCenter 帐户的访问范围进行限定。 
    - 如果租户共享主机, 你可能想要按 VM 文件夹来确定范围。 如果 vCenter 帐户有权访问 vCenter VM 文件夹级别, 则 Azure Migrate 无法发现 Vm。 如果你想要按 VM 文件夹确定发现的范围, 可以通过确保 vCenter 帐户具有在 VM 级别分配的只读访问权限来实现此目的。 [在此处](tutorial-assess-vmware.md#scoping-discovery)了解范围发现的详细信息。

## <a name="prepare-for-assessment"></a>准备评估

为服务器评估准备 Azure 和 VMware。 

1. 验证[VMware 支持要求和限制](migrate-support-matrix-vmware.md)。
2. 设置你的 Azure 帐户的权限以与 Azure Migrate 进行交互。
3. 准备 VMware 以进行评估。

按照[本教程](tutorial-prepare-vmware.md)中的说明配置这些设置。


## <a name="create-a-project"></a>创建一个项目

按照规划要求, 执行以下操作:

1. 创建 Azure Migrate 项目。
2. 将 Azure Migrate 服务器评估工具添加到项目。

[了解详细信息](how-to-add-tool-first-time.md)

## <a name="create-and-review-an-assessment"></a>创建和查看评估

1. 为 VMware Vm 创建评估。
1. 查看评估以准备迁移规划。


按照[本教程](tutorial-assess-vmware.md)中的说明配置这些设置。
    

## <a name="next-steps"></a>后续步骤

本文内容：
 
> [!div class="checklist"] 
> * 计划扩展 VMware Vm 的 Azure Migrate 评估
> * 已准备好 Azure 和 VMware 以进行评估
> * 创建 Azure Migrate 项目并运行评估
> * 查看评估以准备迁移。

现在,[了解如何](concepts-assessment-calculation.md)计算评估, 以及如何[修改评估](how-to-modify-assessment.md)。
