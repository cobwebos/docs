---
title: 在 Azure Migrate 中评估计算 |Microsoft 文档
description: 概述 Azure Migrate 服务中的评估计算。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/25/2018
ms.author: raynew
ms.openlocfilehash: 7900a02ba9112b910589d04850a4cd5d52e044d2
ms.sourcegitcommit: 156364c3363f651509a17d1d61cf8480aaf72d1a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/25/2018
ms.locfileid: "39249183"
---
# <a name="assessment-calculations"></a>评估计算

[Azure Migrate](migrate-overview.md) 会评估要迁移到 Azure 的本地工作负载。 本文提供了有关如何计算评估的信息。


## <a name="overview"></a>概述

Azure Migrate 评估包含三个阶段。 评估的第一步是适用性分析，第二步是大小估计，最后一步是每月成本估计。 如果一台计算机通过了前一个阶段，则只能按顺序进入下一个阶段。 例如，如果一台计算机未通过 Azure 适用性检查，将被标记为不适合迁移到 Azure，并且不会进行大小调整和成本估算。


## <a name="azure-suitability-analysis"></a>Azure 适用性分析

并非所有计算机都适合在云上运行，因为云有自己的限制和要求。 Azure Migrate 评估每个本地计算机迁移到 Azure 的适用性，并将计算机分类为以下类别之一：
- **Azure 已就绪** - 计算机可以照原样迁移到 Azure，无需任何更改。 它将在 Azure 中启动且具备完整的 Azure 支持。
- **Azure 有条件的就绪** - 计算机可在 Azure 中启动，但可能不具有完整的 Azure 支持。 例如，Azure 中不支持具有旧版 Windows Server OS 的计算机。 在将这些计算机迁移到 Azure 之前，需要特别注意，并遵循评估中建议的修正指南以在迁移前修复准备问题。
- **Azure 未就绪** - 计算机不会在 Azure 中启动。 例如，如果本地计算机附加有超过 4 TB 大小的磁盘，则该计算机无法在 Azure 上托管。 你需要遵循评估中建议的修正指南以在迁移到 Azure 前修复准备问题。 标记为 Azure 未就绪的计算机不会进行适度大小调整和成本估算。
- **就绪性未知** - 由于 vCenter Server 中的可用数据不足，Azure Migrate 无法找到计算机的就绪情况。

Azure Migrate 检查计算机属性和来宾操作系统，以确认本地计算机的 Azure 迁移就绪性。

### <a name="machine-properties"></a>计算机属性
Azure Migrate 检查本地 VM 的以下属性来确认 VM 是否可在 Azure 上运行。

**属性** | **详细信息** | **Azure 迁移就绪性状态**
--- | --- | ---
**启动类型** | Azure 支持启动类型为 BIOS（而非 UEFI）的 VM。 | 如果启动类型为 UEFI，则状态为 Auzre 有条件的就绪。
**核心数** | 计算机中的内核数必须等于或小于 Azure VM 支持的最大内核数量 (32)。<br/><br/> 如果性能历史记录可用，Azure Migrate 会考虑已利用的内核数以进行比较。 如果在评估设置中指定了舒适因子，则将已利用的内核数乘以此舒适因子。<br/><br/> 如果没有任何性能历史记录，Azure Migrate 将使用已分配的内核数，而不应用舒适因子。 | 如果内核数大于 32，则为未就绪。
内存 | 计算机内存大小必须等于或小于 Azure VM 允许的最大内存（Azure M 系列 Standard_M128m&nbsp;<sup>2</sup> 上为 3892 GB）。 [了解详细信息](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/sizes-memory.md#m-series)。<br/><br/> 如果性能历史记录可用，Azure Migrate 会考虑已利用的内存以进行比较。 如果指定了舒适因子，则将已利用的内存乘以此舒适因子。<br/><br/> 如果没有任何历史记录，将使用已分配的内存，而不应用舒适因子。<br/><br/> | 如果内存大小大于 448 GB，则为未就绪。
**存储磁盘** | 分配的磁盘大小必须为 4 TB (4096 GB) 或更小。<br/><br/> 连接到计算机的磁盘（包括操作系统磁盘）数必须为 65 个或更少。 | 如果有任何磁盘的大小大于 4 TB，或计算机附加了超过 65 个磁盘，则为未就绪。
**网络** | 连接到计算机的 NIC 数必须为 32 个或更少。 | 如果计算机有超过 32 个 NIC，则为未就绪

### <a name="guest-operating-system"></a>来宾操作系统
除了 VM 属性，Azure Migrate 还会查看本地 VM 的来宾 OS 以确认该 VM 是否可在 Azure 上运行。

> [!NOTE]
> Azure Migrate 认定 vCenter Server 中所指定的 OS 执行以下分析。 由于 Azure Migrate 执行的发现是基于设备的，它没有办法验证 VM 内部运行的 OS 是否与 vCenter Server 中所指定的 OS 相同。

Azure Migrate 使用以下逻辑来基于操作系统确认 VM 的 Azure 迁移就绪性。

**操作系统** | **详细信息** | **Azure 迁移就绪性状态**
--- | --- | ---
Windows Server 2016 和所有 SP | Azure 提供完全支持。 | Azure 已就绪
Windows Server 2012 R2 和所有 SP | Azure 提供完全支持。 | Azure 已就绪
Windows Server 2012 和所有 SP | Azure 提供完全支持。 | Azure 已就绪
Windows Server 2008 R2 和所有 SP | Azure 提供完全支持。| Azure 已就绪
Windows Server 2008（32 位和 64 位） | Azure 提供完全支持。 | Azure 已就绪
Windows Server 2003、2003 R2 | 这些操作系统的支持日期已结束，需要[自定义支持协议 (CSA)](https://aka.ms/WSosstatement) 以获取 Azure 支持。 | Azure 有条件的就绪，请考虑在迁移到 Azure 前升级 OS。
Windows 2000、98、95、NT、3.1、MS-DOS | 这些操作系统的支持日期已结束，计算机可以在 Azure 中启动，但 Azure 不提供 OS 支持。 | Azure 有条件的就绪，建议在迁移到 Azure 前升级 OS。
Windows Client 7、8 和 10 | Azure 仅支持 Visual Studio 订阅。 | Azure 有条件的就绪
Windows Vista、XP Professional | 这些操作系统的支持日期已结束，计算机可以在 Azure 中启动，但 Azure 不提供 OS 支持。 | Azure 有条件的就绪，建议在迁移到 Azure 前升级 OS。
Linux | Azure 予以认可这些 [Linux 操作系统](../virtual-machines/linux/endorsed-distros.md)。 其他 Linux 操作系统可能在 Azure 中启动，但建议在迁移到 Azure 前将 OS 升级到认可的版本。 | 如果版本受到认可，则为 Azure 已就绪。<br/><br/>如果版本不受认可，则为 Azure 有条件的就绪。
其他操作系统<br/><br/> 例如 Oracle Solaris、Apple Mac 操作系统、FreeBSD 等。 | Azure 不认可这些操作系统。 计算机课在 Azure 中启动，但 Azure 不提供 OS 支持。 | Azure 有条件的就绪，建议在迁移到 Azure 前安装已升级的 OS。  
vCenter Server 中指定为“其他”的 OS | 在此情况下，Azure Migrate 无法确认 OS。 | 就绪性未知。 确保 VM 内部运行的 OS 在 Azure 中受到支持。
32 位操作系统 | 计算机可在 Azure 中启动，但 Azure 可能不会提供完全支持。 | Azure 有条件的就绪，在迁移到 Azure 前考虑将计算机的 OS 从 32 位 OS 升级到 64 位 OS。

## <a name="sizing"></a>调整大小

计算机被标记为 Azure 已就绪后，Azure Migrate 会调整 VM 大小及其用于 Azure 的磁盘大小。 如果评估属性中指定的大小调整条件是执行基于性能的大小调整，则 Azure Migrate 将考虑计算机的性能历史记录以确认 Azure 中的 VM 大小和磁盘类型。 当你超额分配了本地 VM 但利用率较低，而又希望适当调整 Azure 中的 VM 大小以节省成本时，此方法非常有用。

> [!NOTE]
> Azure Migrate 从 vCenter Server 收集本地 VM 的性能历史记录。 为了确保准确调整大小，请确保 vCenter Server 中的统计信息设置设为级别 3，并等待至少一天再发起本地 VM 发现。 如果 vCenter Server 中的统计信息设置低于级别 3，则不会收集磁盘和网络的性能数据。

如果不想考虑 VM 大小调整的性能历史记录而希望按原样将 VM 迁移到 Azure，可指定大小调整条件为“按本地”，这样 Azure Migrate 便会基于本地配置调整 VM 大小，而不会考虑利用率数据。 在这种情况下，磁盘的大小调整将根据你在评估属性中指定的存储类型（标准磁盘或高级磁盘）来完成

### <a name="performance-based-sizing"></a>基于性能的大小

对于基于性能的大小调整，Azure Migrate 首先从附加到 VM 的磁盘开始，然后是网络适配器，再基于本地 VM 的计算要求映射 Azure VM。

- 存储：Azure Migrate 尝试将附加到计算机的每个磁盘映射到 Azure 中的磁盘。

    > [!NOTE]
    > Azure Migrate 仅支持托管磁盘进行评估。

    - 为获取有效的磁盘 I/O 每秒 (IOPS) 和吞吐量 (MBps)，Azure Migrate 将用舒适因子乘以磁盘 IOPS 和吞吐量。 基于有效的 IOPS 和吞吐量值，Azure Migrate 将确认磁盘应映射到 Azure 中的标准磁盘还是高级磁盘。
    - 如果 Azure Migrate 找不到具有所需 IOPS 和吞吐量的磁盘，则会将计算机标记为不适用于 Azure。 [详细了解](../azure-subscription-service-limits.md#storage-limits) Azure 对每磁盘和 VM 的限制。
    - 如果找到了一组合适的磁盘，Azure Migrate 将选择支持存储冗余方法以及在评估设置中指定的位置的磁盘。
    - 如果有多个合格的磁盘，将选择成本最低的磁盘。
    - 如果磁盘的性能数据不可用，那么所有磁盘都将映射到 Azure 中的标准磁盘。

- 网络：Azure Migrate 将尝试查找可支持本地计算机所附加的网络适配器数量以及这些网络适配器所要求的性能的 Azure VM。
    - 为获取本地 VM 的有效网络性能，Azure Migrate 将从计算机（网络流出）、跨所有网络适配器聚合每秒传输的数据 (MBps)，并应用舒适因子。 此数值用于查找可支持所需网络性能的 Azure VM。
    - 除了网络性能，它还会考虑 Azure VM 是否可支持所需的网络适配器数量。
    - 如果没有任何网络性能数据可用，则 VM 大小调整仅考虑网络适配器计数。

- 计算：计算存储和网络需求后，Azure Migrate 将考虑 CPU 和内存需求以查找 Azure 中合适的 VM 大小。
    - Azure Migrate 查看已利用的内核和内存，并应用舒适因子来获取有效的内核和内存。 将基于该数量尝试在 Azure 中查找合适的 VM 大小。
    - 如果找不到合适的大小，计算机将标记为不适用于 Azure。
    - 如果找到了合适的大小，Azure Migrate 将应用存储和网络计算。 然后它将应用位置和定价层设置，以提供最终的 VM 大小建议。
    - 如果有多个合格的 Azure VM 大小，建议选择成本最低的那一个。

### <a name="as-on-premises-sizing"></a>按本地大小调整
如果大小调整条件为“按本地大小调整”，Azure Migrate 不会考虑 VM 和磁盘的性能历史记录，并将基于本地分配的大小分配 Azure 中的 VM SKU。 同样对于磁盘的大小调整，它将查找在评估属性中指定的存储类型（标准/高级），并相应地建议磁盘类型。 默认存储类型为高级磁盘。

### <a name="confidence-rating"></a>置信度分级
在 Azure Migrate 中进行的每个基于性能的评估都会与置信度分级相关联。置信度分级分为 1 星到 5 星（1 星表示置信度最低，5 星表示置信度最高）。 为评估分配置信度时，会考虑到进行评估计算时所需数据点的可用性。 对评估的置信度分级可以用来评估 Azure Migrate 提供的大小建议的可靠性。 置信度分级不适用于本地评估。

对于基于性能的大小调整，Azure Migrate 需要 VM 的 CPU 和内存的利用率数据。 此外，对于每个附加到 VM 的磁盘，它需要磁盘 IOPS 和吞吐量数据。 同样，对于每个附加到 VM 的网络适配器，Azure Migrate 需要网络流入/流出量才能执行基于性能的大小调整。 如果上述利用率数据在 vCenter Server 中均不可用，则 Azure Migrate 提供的大小建议可能不可靠。 根据可用数据点的百分比，提供评估的置信度分级，如下所示：

   **数据点的可用性** | **置信度分级**
   --- | ---
   0%-20% | 1 星
   21%-40% | 2 星
   41%-60% | 3 星
   61%-80% | 4 星
   81%-100% | 5 星

由于以下某个原因，评估时并非所有数据点都可用：
- vCenter Server 中的统计信息设置未设置为级别 3。 如果 vCenter Server 中的统计设置低于级别 3，则不会从 vCenter Server 收集磁盘和网络的性能数据。 在这种情况下，Azure Migrate 针对磁盘和网络提供的建议不考虑利用率。 在不考虑磁盘的 IOPS/吞吐量的情况下，Azure Migrate 无法确定磁盘是否需要 Azure 中的高级磁盘，因此，在这种情况下，Azure Migrate 建议所有磁盘是标准磁盘。
- 在启动发现之前，vCenter Server 中的统计设置已短时间设置为级别 3。 例如，如果在今天将统计设置级别更改为 3，并在明天（24 小时后）使用收集器设备启动发现，则可考虑此方案。 如果是创建一天的评估，你就有了所有数据点，对评估的置信度分级将是 5 星。 但是，如果在评估属性中将性能时段更改为一个月，则置信度分级会下降，因为最后一个月的磁盘和网络性能数据将不可用。 若要考虑最后一个月的性能数据，建议将 vCenter Server 统计设置保留为级别 3 一个月，然后再启动发现。
- 一些 VM 在进行评估计算期间关闭。 如果某些 VM 停机了一段时间，则 vCenter Server 不会有该时段的性能数据。
- 在进行评估计算期间创建了一些 VM。 例如，如果要针对最后一个月的性能历史记录创建评估，但仅仅在一周前，在环境中创建了一些 VM， 则在这种情况下，新建 VM 的性能历史记录并非在整个期间都有。

> [!NOTE]
> 如果任何评估的置信度分级低于 4 星，建议将 vCenter Server 统计设置级别更改为 3，等待要考虑进行评估的期间（1 天/1 周/1 月），然后进行发现和评估。 如果前述操作无法完成，则基于性能的大小调整可能不可靠，建议通过更改评估属性切换到“按本地大小调整”。

## <a name="monthly-cost-estimation"></a>每月成本估计

大小建议完成后，Azure Migrate 将计算迁移后计算和存储成本。

- 计算成本：使用建议的 Azure VM 大小，Azure Migrate 使用计费 API 来计算 VM 每月成本。 该计算会考虑操作系统、软件保障、预留实例、VM 运行时间、位置和货币设置。 它将所有计算机的成本求和，计算每月总计算成本。
- 存储成本：一台计算机的每月存储成本的计算方法为，将连接到该计算机的所有磁盘的每月成本求和。 Azure Migrate 通过将所有计算机的存储成本求和，计算每月总存储成本。 当前该计算不考虑在评估设置中指定的优惠。

成本以在评估设置中指定的币种显示。


## <a name="next-steps"></a>后续步骤

[为本地 VMware VM 创建评估](tutorial-assessment-vmware.md)
