---
title: 评估大量 VMware VM，以便通过 Azure 迁移迁移到 Azure
description: 介绍如何使用 Azure 迁移服务评估大量 VMware VM 以迁移到 Azure。
ms.topic: how-to
ms.date: 03/23/2020
ms.openlocfilehash: d404583b1bad474a5e24e8c7cf060aeb80d610bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336852"
---
# <a name="assess-large-numbers-of-vmware-vms-for-migration-to-azure"></a>评估大量 VMware VM 以迁移到 Azure


本文介绍如何使用 Azure 迁移服务器评估工具评估大量本地 VMware VM 以迁移到 Azure。

[Azure Migrate](migrate-services-overview.md) 在一个中心位置提供多种工具，帮助你发现、评估应用、基础结构和工作负荷并将其迁移到 Microsoft Azure。 该中心包含 Azure Migrate 工具，以及第三方独立软件供应商 (ISV) 的产品/服务。 

在本文中，学习如何：
> [!div class="checklist"]
> * 计划规模评估。
> * 配置 Azure 权限，并为评估准备 VMware。
> * 创建 Azure 迁移项目，并创建评估。
> * 在规划迁移时查看评估。


> [!NOTE]
> 如果您想尝试一个概念验证，以评估几个 VM 之前进行大规模评估，请按照我们的[教程系列](tutorial-prepare-vmware.md)操作

## <a name="plan-for-assessment"></a>评估计划

在规划评估大量 VMware VM 时，需要考虑几点：

- **规划 Azure 迁移项目**：了解如何部署 Azure 迁移项目。 例如，如果数据中心位于不同的地理位置，或者您需要将发现、评估或迁移相关的元数据存储在不同的地理位置，则可能需要多个项目。 
- **计划设备**：Azure 迁移使用本地 Azure 迁移设备（部署为 VMware VM）来持续发现 VM。 设备监视环境更改，如添加 VM、磁盘或网络适配器。 它还会向 Azure 发送有关元数据和性能数据的元数据和性能数据。 您需要确定需要部署多少台设备。
- **计划帐户进行发现**：Azure 迁移设备使用有权访问 vCenter Server 的帐户来发现 VM 以进行评估和迁移。 如果您发现超过 10，000 个 VM，则设置多个帐户。


## <a name="planning-limits"></a>规划限制
 
使用此表中总结的限制进行规划。

**规划** | **限制**
--- | --- 
**Azure 迁移项目** | 评估项目中多达 35，000 个 VM。
**Azure Migrate 设备** | 设备可以在 vCenter 服务器上发现多达 10，000 台 VM。<br/> 设备只能连接到单个 vCenter 服务器。<br/> 设备只能与单个 Azure 迁移项目关联。<br/>  任意数量的设备都可以与单个 Azure 迁移项目相关联。 <br/><br/> 
**组** | 单个组中最多可以添加 35，000 个 VM。
**Azure 迁移评估** | 您可以在单个评估中评估多达 35，000 个 VM。

考虑到这些限制，下面是一些示例部署：


**vCenter 服务器** | **服务器上的 VM** | **建议** | **操作**
---|---|---
一种 | < 10，000 | 一个 Azure 迁移项目。<br/> 一个设备。<br/> 一个 vCenter 帐户用于发现。 | 设置设备，使用帐户连接到 vCenter 服务器。
一种 | > 10，000 | 一个 Azure 迁移项目。<br/> 多个设备。<br/> 多个 vCenter 帐户。 | 为每 10，000 个 VM 设置设备。<br/><br/> 设置 vCenter 帐户，并将库存划分以将帐户的访问限制为少于 10，000 个 VM。<br/> 使用帐户将每个设备连接到 vCenter 服务器。<br/> 您可以分析使用不同设备发现的计算机之间的依赖项。
多个 | < 10，000 |  一个 Azure 迁移项目。<br/> 多个设备。<br/> 一个 vCenter 帐户用于发现。 | 设置设备，使用帐户连接到 vCenter 服务器。<br/> 您可以分析使用不同设备发现的计算机之间的依赖项。
多个 | > 10，000 | 一个 Azure 迁移项目。<br/> 多个设备。<br/> 多个 vCenter 帐户。 | 如果 vCenter 服务器发现< 10，000 个 VM，请为每个 vCenter 服务器设置一个设备。<br/><br/> 如果 vCenter Server 发现> 10，000 个 VM，则为每个 10，000 个 VM 设置一个设备。<br/> 设置 vCenter 帐户，并将库存划分以将帐户的访问限制为少于 10，000 个 VM。<br/> 使用帐户将每个设备连接到 vCenter 服务器。<br/> 您可以分析使用不同设备发现的计算机之间的依赖项。


## <a name="plan-discovery-in-a-multi-tenant-environment"></a>在多租户环境中规划发现

如果您计划使用多租户环境，则可以在 vCenter 服务器上对发现进行限定。

- 您可以将设备发现范围设置为 vCenter Server 数据中心、群集群集或文件夹、主机主机或文件夹或单个 VM。
- 如果环境在租户之间共享，并且希望单独发现每个租户，则可以对设备用于发现的 vCenter 帐户的访问范围。 
    - 如果租户共享主机，则可能需要按 VM 文件夹进行范围。 如果 vCenter 帐户在 vCenter VM 文件夹级别授予访问权限，则 Azure 迁移无法发现 VM。 若要按 VM 文件夹限定发现范围，确保在 VM 级别为 vCenter 帐户分配只读访问权限即可实现此目的。 [了解详情](set-discovery-scope.md)。

## <a name="prepare-for-assessment"></a>准备评估

准备 Azure 和 VMware 进行服务器评估。 

1. 验证[VMware 支持要求和限制](migrate-support-matrix-vmware.md)。
2. 设置 Azure 帐户与 Azure 迁移交互的权限。
3. 准备 VMware 进行评估。

按照[本教程](tutorial-prepare-vmware.md)中的说明配置这些设置。


## <a name="create-a-project"></a>创建一个项目

根据您的规划要求，请执行以下操作：

1. 创建 Azure 迁移项目。
2. 将 Azure 迁移服务器评估工具添加到项目中。

[了解详细信息](how-to-add-tool-first-time.md)

## <a name="create-and-review-an-assessment"></a>创建和审核评估

1. 为 VMware VM 创建评估。
1. 审查评估，为迁移规划做准备。


按照[本教程](tutorial-assess-vmware.md)中的说明配置这些设置。
    

## <a name="next-steps"></a>后续步骤

本文内容：
 
> [!div class="checklist"] 
> * 计划为 VMware VM 扩展 Azure 迁移评估
> * 准备 Azure 和 VMware 进行评估
> * 创建 Azure 迁移项目并运行评估
> * 审查了为迁移做准备的评估。

现在，[了解如何](concepts-assessment-calculation.md)计算评估，以及如何[修改评估](how-to-modify-assessment.md)。
