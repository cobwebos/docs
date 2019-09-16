---
title: Azure Migrate Server 评估创建评估的最佳实践
description: 提供有关 Azure Migrate Server 评估创建评估的提示。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: raynew
ms.openlocfilehash: a07e83c805fc422c861f6c644a59da0b42bb8072
ms.sourcegitcommit: a819209a7c293078ff5377dee266fa76fd20902c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/16/2019
ms.locfileid: "71007716"
---
# <a name="best-practices-for-creating-assessments"></a>创建评估的最佳实践

[Azure Migrate](migrate-overview.md) 在一个中心位置提供多种工具，帮助你发现、评估应用、基础结构和工作负荷并将其迁移到 Microsoft Azure。 该中心包含 Azure Migrate 工具，以及第三方独立软件供应商 (ISV) 的产品/服务。

本文汇总了使用 Azure Migrate Server 评估工具创建评估时的最佳做法。

## <a name="about-assessments"></a>关于评估

使用 Azure Migrate Server 评估创建的评估是数据的时间点快照。 Azure Migrate 中有两种类型的评估。

**评估类型** | **详细信息** | **数据**
--- | --- | ---
**基于性能** | 基于收集的性能数据提出建议的评估 | VM 大小建议基于 CPU 和内存使用率数据。<br/><br/> 磁盘类型建议 (标准 HDD/SSD 或高级托管磁盘) 基于本地磁盘的 IOPS 和吞吐量。
**按本地** | 评估不使用性能数据来提出建议。 | VM 大小建议基于本地 VM 大小<br/><br> 建议的磁盘类型基于在评估的 "存储类型" 设置中选择的内容。

### <a name="example"></a>示例
例如，如果你有一个本地 VM，其四个内核的利用率为 20%，内存为 8 GB，利用率为 10%，则评估将如下所示：

- **基于性能的评估**:
    - 基于内核 (4 x 0.20 = 0.8) 和内存 (8 GB x 0.10 = 0.8) 利用率标识有效内核和内存。
    - 应用在评估属性（假定 1.3 x）中指定的舒适因素，以获取用于调整大小的值。 
    - 建议 Azure 中最接近的 VM 大小，该大小可支持 ~ 1.04 核心（0.8 x 1.3）和 ~ 1.04 GB （0.8 x 1.3）内存。

- **按原样 (作为本地) 评估**:
    -  建议具有四个核心的 VM;8 GB 内存。

## <a name="best-practices-for-creating-assessments"></a>创建评估的最佳实践

Azure Migrate 设备会持续分析你的本地环境, 并将元数据和性能数据发送到 Azure。 遵循以下最佳实践来创建评估:

- **创建按为评估**:在 Azure Migrate 门户中显示的计算机后, 可以立即创建 "按原样" 评估。
- **创建基于性能的评估**:设置发现后, 建议你等待至少一天, 然后再运行基于性能的评估:
    - 收集性能数据会耗费时间。 等待至少一天可确保在运行评估之前有足够的性能数据点。
    - 当你运行基于性能的评估时, 请确保为评估持续时间分析你的环境。 例如, 如果你创建的评估的 "性能持续时间" 设置为 "一周", 则在开始发现后, 你需要等待至少一周的时间来收集所有数据点。 如果不这样做, 评估将不会获得五星评级。
- **重新计算评估**:由于评估是时间点快照, 因此它们不会自动更新为最新的数据。 若要使用最新数据更新评估, 需要重新计算。

## <a name="best-practices-for-confidence-ratings"></a>置信度评级的最佳实践

当你运行基于性能的评估时, 从1星级 (最低) 到5星 (最高) 的置信度评级将获得评估。 有效使用置信度:
- Azure Migrate Server 评估需要 VM CPU/内存的利用率数据。
- 对于附加到本地 VM 的每个磁盘, 都需要读/写 IOPS/吞吐量数据。
- 对于附加到 VM 的每个网络适配器, 它需要网络输入/输出数据。

根据所选持续时间的可用数据点数百分比, 按下表所述提供评估的置信度评级。

   **数据点可用性** | **置信度分级**
   --- | ---
   0%-20% | 1 星
   21%-40% | 2 星
   41%-60% | 3 星
   61%-80% | 4 星
   81%-100% | 5 星


## <a name="common-assessment-issues"></a>常见评估问题

下面介绍了一些影响评估的常见环境问题。

###  <a name="out-of-sync-assessments"></a>不同步评估

如果在创建评估后在组中添加或删除计算机, 你创建的评估将标记为 "不**同步**"。再次运行评估 (**重新计算**) 以反映组更改。

### <a name="outdated-assessments"></a>过时评估

如果对组中已评估的 Vm 进行本地更改, 则将评估标记为**过时**。 若要反映这些更改, 请再次运行评估。

### <a name="low-confidence-rating"></a>低置信度分级

评估可能不包含所有数据点的原因有很多:

- 在创建评估的过程中，你没有对环境进行分析。 例如, 如果要创建性能持续时间设置为一周的*基于性能的评估*, 则必须在开始发现所有数据点后至少等待一周。 随时可以单击 "**重新计算**" 查看最新的适用置信度级别。 仅当你创建*基于性能的*评估时, 置信度才适用。

- 一些 VM 在进行评估计算期间关闭。 如果某些 VM 停机了一段时间，则服务器评估将无法收集该时段的性能数据。

- 启动服务器评估中的发现之后，基本不再创建 VM。 例如，如果要针对最后一个月的性能历史记录创建评估，但仅仅在一周前，在环境中创建了一些 VM， 在这种情况下，新 VM 的性能数据在整个过程中都不可用，并且置信度分级会较低。


## <a name="next-steps"></a>后续步骤

- [了解](concepts-assessment-calculation.md)如何计算评估。
- [了解](how-to-modify-assessment.md)如何自定义评估。
