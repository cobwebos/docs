---
title: 访问大量 VMware Vm 迁移到 Azure 中使用 Azure Migrate |Microsoft Docs
description: 介绍如何访问大量的 VMware Vm 迁移到 Azure 中使用 Azure Migrate 服务。
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: raynew
ms.openlocfilehash: 6102a1c59be3627b95dc1e0cb1d1d712d5832d2f
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811331"
---
# <a name="assess-large-numbers-of-vmware-vms-for-migration-to-azure"></a>访问大量 VMware Vm 迁移到 Azure 的


本文介绍如何访问较大的数字 (1000年 35000) 的本地 VMware 虚拟机迁移到 Azure，使用 Azure Migrate Server 评估工具

[Azure Migrate](migrate-services-overview.md)提供一个中心工具，可帮助你发现、 评估和将应用程序、 基础结构和工作负荷迁移到 Microsoft Azure。 在中心包括 Azure 迁移工具和第三方独立软件供应商 (ISV) 产品/服务。 

在本文中，学习如何：
> [!div class="checklist"]
> * 对于大规模的评估计划。
> * 配置 Azure 权限，并准备 VMware 进行评估。
> * 创建 Azure Migrate 项目，并创建评估。
> * 在规划迁移，请查看该评估。


> [!NOTE]
> 如果你想要试用的概念要大规模评估之前，评估几个 Vm，请遵循我们[系列教程](tutorial-prepare-vmware.md)

## <a name="plan-for-assessment"></a>评估计划

在规划进行大量的 VMware Vm 评估时，有几个事项需要注意：

- **计划 Azure Migrate 项目**:了解如何部署 Azure Migrate 项目。 例如，如果您的数据中心位于不同的地理区域，或者需要将发现、 评估或迁移相关的元数据存储在另一个地理位置，可能需要多个项目。 
- **计划设备**:Azure Migrate 使用本地 Azure Migrate 设备，部署为 VMware VM，以持续发现 Vm。 设备监视环境更改，例如添加 Vm、 磁盘或网络适配器。 它还将有关它们的元数据和性能数据发送到 Azure。 您需要找出多少部署所需的设备。
- **计划发现的帐户**:Azure Migrate 设备使用的帐户有权访问 vCenter 服务器以发现用于评估和迁移的 Vm。 如果正在发现 10,000 多个 Vm，则设置多个帐户。


## <a name="planning-limits"></a>规划限制
 
使用用于规划此表中汇总的限制。

**规划** | **限制**
--- | --- 
**Azure Migrate 项目** | 评估最多 35,000 Vm 在项目中。
**Azure Migrate 设备** | 一台设备只能连接到单个 vCenter 服务器。<br/><br/> 一台设备只能与单个 Azure Migrate 项目相关联。<br/> 一台设备可以发现 vCenter 服务器上的最多 10,000 个 Vm。
**Azure Migrate 评估** | 您可以评估单次评估最多 35,000 Vm。

具有记住这些限制，下面是一些部署示例：


**vCenter 服务器** | **在服务器上的 Vm** | **建议** | **Action**
---|---|---
一种 | < 10,000 | 一个 Azure Migrate 项目。<br/> 一个设备。<br/> 一个发现的 vCenter 帐户。 | 设置设备、 连接到 vCenter 服务器的帐户。
一种 | > 10,000 | 一个 Azure Migrate 项目。<br/> 多个设备。<br/> 多个 vCenter 帐户。 | 为每隔 10,000 Vm 设置设备。<br/><br/> 设置 vCenter 帐户，并划分库存量，以限制到 10000 个 Vm 的帐户的访问。<br/> 每个设备连接到 vCenter 服务器的帐户。<br/> 你可以跨使用不同的设备发现的计算机分析依赖关系。
多个 | < 10,000 |  一个 Azure Migrate 项目。<br/> 多个设备。<br/> 一个发现的 vCenter 帐户。 | 设置设备、 连接到 vCenter 服务器的帐户。<br/> 你可以跨使用不同的设备发现的计算机分析依赖关系。
多个 | > 10,000 | 一个 Azure Migrate 项目。<br/> 多个设备。<br/> 多个 vCenter 帐户。 | 如果 vCenter 服务器发现 < 10000 Vm 设置每个 vCenter 服务器的设备。<br/><br/> 如果 vCenter 服务器发现 > 10,000 Vm，一台设备的每 10,000 Vm 设置。<br/> 设置 vCenter 帐户，并划分库存量，以限制到 10000 个 Vm 的帐户的访问。<br/> 每个设备连接到 vCenter 服务器的帐户。<br/> 你可以跨使用不同的设备发现的计算机分析依赖关系。


## <a name="plan-discovery-in-a-multi-tenant-environment"></a>计划在多租户环境中的发现

如果你计划为多租户环境，可以将发现 vCenter 服务器上。

- 您可以设置设备发现作用域为 vCenter 服务器数据中心、 群集或群集、 主机或主机或单独的 Vm 的文件夹的文件夹。
- 如果在租户间共享你的环境并不想要单独发现每个租户，可以将对设备使用发现的 vCenter 帐户访问。 
    - 如果租户共享的主机，创建的凭据具有只读访问权限属于特定租户的 vm。 
    - 使用这些凭据进行 Azure Migrate 设备发现。
    - 如果 vCenter 帐户拥有在 vCenter VM 文件夹级别授予的访问权限，azure Migrate 评估无法发现 Vm。 支持的主机和群集的文件夹。 

## <a name="prepare-for-assessment"></a>准备进行评估

准备 Azure 和 VMware server 评估。 

1. 验证是否[VMware 支持要求和限制](migrate-support-matrix-vmware.md)。
2. 设置 Azure 帐户与 Azure Migrate 进行交互的访问权限。
3. 准备 VMware 进行评估。


按照中的说明[本教程](tutorial-prepare-vmware.md)配置这些设置。


## <a name="create-a-project"></a>创建一个项目

根据你的规划要求，执行以下步骤：

1. 创建 Azure Migrate 项目。
2. 向项目添加 Azure 迁移服务器评估工具。

[了解详细信息](how-to-add-tool-first-time.md)

## <a name="create-and-review-an-assessment"></a>创建和查看评估

1. 创建适用于 VMware Vm 评估。
1. 查看评估中针对迁移规划的准备。


按照中的说明[本教程](tutorial-assess-vmware.md)配置这些设置。
    

## <a name="next-steps"></a>后续步骤

本文内容：
 
> [!div class="checklist"] 
> * 计划扩展为 VMware Vm 的 Azure Migrate 评估
> * 已准备好 Azure 和 VMware 进行评估
> * 创建一个 Azure Migrate 项目并运行评估
> * 查看评估为迁移做准备。

现在，[了解如何](concepts-assessment-calculation.md)计算评估，以及如何[修改评估](how-to-modify-assessment.md)。
