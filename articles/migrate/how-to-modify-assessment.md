---
title: 为 Azure Migrate Server 评估自定义评估 |Microsoft Docs
description: 描述如何自定义 Azure Migrate Server 评估创建的评估
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/15/2019
ms.author: raynew
ms.openlocfilehash: de526da255d0ffb2d4c8f13d87d9b9e230c8bbd7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85561816"
---
# <a name="customize-an-assessment"></a>自定义评估

本文介绍如何自定义 Azure Migrate Server 评估创建的评估。

[Azure Migrate](migrate-services-overview.md) 提供一个中心用于跟踪本地应用、工作负荷与私有云/公有云 VM 的发现、评估及其到 Azure 的迁移。 该中心提供用于评估和迁移的 Azure Migrate 工具，以及第三方独立软件供应商 (ISV) 产品。

你可以使用 Azure Migrate Server 评估工具为本地 VMware Vm 和 Hyper-v Vm 创建评估，以准备迁移到 Azure。 服务器评估工具评估本地服务器以迁移到 Azure IaaS 虚拟机和 Azure VMware 解决方案（AVS）。 

## <a name="about-assessments"></a>关于评估

使用服务器评估创建的评估是数据的时间点快照。 使用“Azure Migrate: 服务器评估”可以运行两种类型的评估。

**评估类型** | **详细信息**
--- | --- 
Azure VM  | 用于将本地服务器迁移到 Azure 虚拟机的评估。 <br/><br/> 可以使用此评估类型评估本地[VMware vm](how-to-set-up-appliance-vmware.md)、 [hyper-v vm](how-to-set-up-appliance-hyper-v.md)和[物理服务器](how-to-set-up-appliance-physical.md)，以便迁移到 Azure。（concepts-assessment-calculation.md）
**Azure VMware 解决方案 (AVS)** | 用于将本地服务器迁移到[Azure VMware 解决方案（AVS）](https://docs.microsoft.com/azure/azure-vmware/introduction)的评估。 <br/><br/> 可以使用此评估类型评估本地[Vmware vm](how-to-set-up-appliance-vmware.md) ，以便迁移到 Azure VMware 解决方案（AVS）。[了解更多](concepts-azure-vmware-solution-assessment-calculation.md)

服务器评估中的 Azure VM 评估提供两个大小调整条件选项：

**调整大小标准** | **详细信息** | **数据**
--- | --- | ---
**基于性能** | 基于收集的性能数据提出建议的评估 | **AZURE vm 评估**： VM 大小建议基于 CPU 和内存使用率数据。<br/><br/> 磁盘类型建议（标准 HDD/SSD 或高级托管磁盘）基于本地磁盘的 IOPS 和吞吐量。<br/><br/> **Azure VMware 解决方案（AVS）评估**： AVS 节点建议基于 CPU 和内存利用率数据。
**按本地** | 评估不使用性能数据来提出建议。 | **AZURE vm 评估**： VM 大小建议基于本地 VM 大小<br/><br> 建议的磁盘类型基于在评估的 "存储类型" 设置中选择的内容。<br/><br/> **Azure VMware 解决方案（AVS）评估**： AVS 节点建议基于本地 VM 大小。


## <a name="how-is-an-assessment-done"></a>评估的完成方式是什么？

Azure Migrate Server 评估中进行的评估分为三个阶段。 评估的第一步是适用性分析，第二步是大小估计，最后一步是每月成本估计。 如果一台计算机通过了前一个阶段，则只能按顺序进入下一个阶段。 例如，如果一台计算机未通过 Azure 适用性检查，将被标记为不适合迁移到 Azure，并且不会进行大小调整和成本估算。 [了解详细信息。](https://docs.microsoft.com/azure/migrate/concepts-assessment-calculation)

## <a name="whats-in-an-azure-vm-assessment"></a>Azure VM 评估有哪些？

**属性** | **详细信息**
--- | ---
**目标位置** | 要迁移到的 Azure 位置。<br/> 服务器评估目前支持以下目标区域：澳大利亚东部、澳大利亚东南部、巴西南部、加拿大中部、加拿大东部、美国东部、美国中部、中国东部、中国北部、东亚、美国东部、东2、德国中部、德国东北部、日本东部、日本西部、韩国中部、韩国南部、美国中北部、印度、英国南部、北欧英国西部、US Gov 亚利桑那州、US Gov 德克萨斯州、US Gov 弗吉尼亚州、美国西部、西欧、印度西部、美国西部和西2。
**存储类型** | 你可以使用此属性来指定你想要移动到 Azure 中的磁盘类型。<br/><br/> 对于按本地大小调整，你可以将目标存储类型指定为高级托管磁盘、标准 SSD 托管磁盘或标准 HDD 托管磁盘。 对于基于性能的大小调整，你可以将目标磁盘类型指定为自动、高级托管磁盘、标准 HDD 托管磁盘或标准 SSD 托管磁盘。<br/><br/> 将存储类型指定为自动时，将根据磁盘的性能数据（IOPS 和吞吐量）完成磁盘建议。 如果将存储类型指定为 "高级"/"标准"，则评估将在选定的存储类型中建议使用磁盘 SKU。 如果要实现99.9% 的单个实例 VM SLA，可能需要将存储类型指定为高级托管磁盘。 这可确保推荐评估中的所有磁盘作为高级托管磁盘。 Azure
**预订实例（RI）** | 此属性可帮助你指定是否在 Azure 中使用了[保留实例](https://azure.microsoft.com/pricing/reserved-vm-instances/)，评估中的成本估算会在 RI 折扣中完成。 目前仅支持 Azure Migrate 中的“即用即付”产品/服务。
**“大小调整”条件** | 用于正确调整 Azure Vm 大小的条件。 可以执行*基于性能*的大小调整或将 vm 调整*为本地*大小，而无需考虑性能历史记录。
**性能历史记录** | 用于评估计算机性能数据的持续时间。 仅当调整大小标准*基于性能*时，此属性才适用。
**百分位使用率** | 进行适当大小调整时要考虑的性能样本集的百分位值。 仅当调整大小*基于性能*时，此属性才适用。
**VM 系列** |     可以指定想要考虑进行适当大小调整的 VM 系列。 例如，如果你不打算将生产环境迁移到 Azure 中的 A 系列 VM，可以从列表或系列中排除 A 系列，则只在所选系列中完成适当大小调整。
**舒适因子** | 在评估期间，Azure Migrate 服务器评估会考虑使用缓冲区（舒适因素）。 该缓冲应用到 VM 的机器使用率数据（CPU、内存、磁盘和网络）上。 舒适因子考虑到季节性使用特点、短期性能历史记录，以及未来使用量可能会增加等问题。<br/><br/> 例如，一个使用率为 20% 的 10 核 VM 通常相当于一个 2 核 VM。 但是，如果舒适因子为 2.0x，则结果就变成一个 4 核 VM。
**产品** | 加入的 [Azure 套餐](https://azure.microsoft.com/support/legal/offer-details/)。 Azure Migrate 会进行相应的成本估算。
**货币** | 计费货币。
**折扣 (%)** | 基于 Azure 套餐获得的任何特定订阅的折扣。<br/> 默认设置是 0%。
**VM 运行时间** | 如果 VM 不会在 Azure 中全天候运行，则可指定运行持续时间（每月的天数和每天的小时数），然后系统就会进行相应的成本估算。<br/> 默认值为“每月 31 天和每天 24 小时”。
**Azure 混合权益** | 指定您是否具有软件保障并且有资格使用[Azure 混合权益](https://azure.microsoft.com/pricing/hybrid-use-benefit/)。 若设为“是”，Windows VM 采用非 Windows Azure 价格。 默认值为“是”。

## <a name="whats-in-an-azure-vmware-solution-avs-assessment"></a>Azure VMware 解决方案（AVS）评估中有哪些？

服务器评估中的 AVS 评估包含以下是：


| **属性** | **详细信息** |
| - | - |
| **目标位置** | 指定要迁移到的 AVS 私有云位置。<br/><br/> 服务器评估中的 AVS 评估目前支持以下目标区域：美国东部、西欧、美国西部。 |
| **存储类型** | 指定要在 AVS 中使用的存储引擎。<br/><br/> 请注意，AVS 评估只支持使用 vSAN 作为默认存储类型。 |
**预订实例（RIs）** | 此属性可帮助你在 AVS 中指定保留实例。 当前 AVS 节点不支持 RIs。 |
**节点类型** | 指定用于映射本地 Vm 的[AVS 节点类型](https://docs.microsoft.com/azure/azure-vmware/concepts-private-clouds-clusters)。 请注意，默认节点类型为 AV36。 <br/><br/> Azure Migrate 将建议将 Vm 迁移到 AVS 所需的节点数。 |
**FTT 设置，RAID 级别** | 指定适用于容忍和 Raid 组合的失败。 选择的 FTT 选项与本地 VM 磁盘要求一起决定了 AVS 中所需的总 vSAN 存储空间。 |
**“大小调整”条件** | 设置要用于 AVS 的虚拟机_大小_的条件。 你可以选择_基于性能_的大小调整，也可以选择_在本地_进行调整，而无需考虑性能历史记录。 |
**性能历史记录** | 设置评估计算机性能数据时要考虑的持续时间。 仅当大小调整条件_基于性能_时，此属性才适用。 |
**百分位使用率** | 指定要用于调整右大小的性能样本集的百分位数值。 此属性仅当调整大小基于性能时才适用。|
**舒适因子** | 在评估期间，Azure Migrate 服务器评估会考虑使用缓冲区（舒适因素）。 该缓冲应用到 VM 的机器使用率数据（CPU、内存、磁盘和网络）上。 舒适因子考虑到季节性使用特点、短期性能历史记录，以及未来使用量可能会增加等问题。<br/><br/> 例如，一个使用率为 20% 的 10 核 VM 通常相当于一个 2 核 VM。 但是，如果舒适因子为 2.0x，则结果就变成一个 4 核 VM。 |
**产品** | 显示您注册的[Azure 产品/服务](https://azure.microsoft.com/support/legal/offer-details/)。 Azure Migrate 会进行相应的成本估算。|
**货币** | 显示帐户的计费货币。 |
**折扣 (%)** | 列出你在 Azure 产品/服务上收到的任何特定于订阅的折扣。 默认设置是 0%。 |
**Azure 混合权益** | 指定您是否具有软件保障并且有资格使用[Azure 混合权益](https://azure.microsoft.com/pricing/hybrid-use-benefit/)。 尽管这不会影响基于节点价格的 Azure VMware 解决方案定价，但客户仍然可以使用 Azure 混合权益，在 AVS 中将其应用于本地 OS 许可证（基于 Microsoft）。 其他软件操作系统供应商必须提供其自己的许可条款，例如 RHEL。 |
**vCPU 超额订阅** | 指定绑定到 AVS 节点中1个物理内核的虚拟核心数的比率。 计算中的默认值为 4 vCPU：1个物理核心 in AVS。 <br/><br/> API 用户可以将此值设置为整数。 请注意，vCPU 超额 > 4:1 可能会开始导致性能降低，但可用于 web 服务器类型的工作负荷。 |

## <a name="edit-assessment-properties"></a>编辑评估属性

若要在创建评估后编辑评估属性，请执行以下操作：

1. 在 Azure Migrate 项目中，单击 "**服务器**"。
2. 在**Azure Migrate：服务器评估**中，单击 "评估计数"。
3. 在 "**评估**" 中，单击相关评估 >**编辑属性**"。
5. 根据上表自定义评估属性。
6. 单击“保存”**** 更新评估。


你还可以在创建评估时编辑评估属性。


## <a name="next-steps"></a>后续步骤

- [了解](concepts-assessment-calculation.md)有关如何计算 Azure VM 评估的详细信息。
- [了解](concepts-azure-vmware-solution-assessment-calculation.md)有关如何计算 AVS 评估的详细信息。