---
title: 在 Azure Migrate 中评估计算 |Microsoft 文档
description: 概述 Azure Migrate 服务中的评估计算。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/15/2019
ms.author: raynew
ms.openlocfilehash: 87aa48c992b7887ce86c43cac29910dcc57b3e91
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234287"
---
# <a name="assessment-calculations"></a>评估计算

[Azure Migrate](migrate-services-overview.md)提供了一个中心中心, 用于跟踪从本地应用程序和工作负荷到 Azure 的发现、评估和迁移。 该中心提供用于评估和迁移的 Azure Migrate 工具, 以及第三方独立软件供应商 (ISV) 产品。

服务器评估是 Azure Migrate 中的一种工具, 用于评估要迁移到 Azure 的本地服务器。 本文提供了有关如何计算评估的信息。

## <a name="whats-in-an-assessment"></a>评估内容有哪些？

**属性** | **详细信息**
--- | ---
**目标位置** | 要迁移到的 Azure 位置。<br/> 服务器评估目前支持以下目标区域:澳大利亚东部、澳大利亚东南部、巴西南部、加拿大中部、加拿大东部、印度中部、美国中部、中国东部、中国北部、东亚、美国东部、东2、德国中部、德国东北部、日本东部、日本西部、韩国中部、韩国南部、北部美国中部、北欧、美国中南部、东南亚、印度南部、英国南部、英国西部、US Gov 亚利桑那州、US Gov 德克萨斯州、US Gov 弗吉尼亚州、美国西部、西欧、印度西部、美国西部和西2。
**存储类型** | 你可以使用此属性来指定你想要移动到 Azure 中的磁盘类型。 <br/><br/> 对于按本地大小调整, 你可以将目标存储类型指定为高级托管磁盘、标准 SSD 托管磁盘或标准 HDD 托管磁盘。 对于基于性能的大小调整, 你可以将目标磁盘类型指定为自动、高级托管磁盘、标准 HDD 托管磁盘或标准 SSD 托管磁盘。<br/><br/> 将存储类型指定为自动时，将根据磁盘的性能数据（IOPS 和吞吐量）完成磁盘建议。 如果将存储类型指定为 "高级"/"标准", 则评估将在选定的存储类型中建议使用磁盘 SKU。 如果要实现 99.9% 的单个实例 VM SLA, 可能需要将存储类型指定为高级托管磁盘。 这可确保推荐评估中的所有磁盘作为高级托管磁盘。 请注意，Azure Migrate 仅支持使用托管磁盘进行迁移评估。
**预订实例 (RI)** | 此属性可帮助你指定是否在 Azure 中使用了[保留实例](https://azure.microsoft.com/pricing/reserved-vm-instances/), 评估中的成本估算会在 RI 折扣中完成。 目前仅支持 Azure Migrate 中的“即用即付”产品/服务。
**“大小调整”条件** | 用于正确调整 Azure Vm 大小的条件。 可以执行*基于性能*的大小调整或将 vm 调整*为本地*大小, 而无需考虑性能历史记录。
**性能历史记录** | 用于评估计算机性能数据的持续时间。 仅当调整大小标准*基于性能*时, 此属性才适用。
**百分位使用率** | 进行适当大小调整时要考虑的性能样本集的百分位值。 仅当调整大小*基于性能*时, 此属性才适用。
**VM 系列** |     可以指定想要考虑进行适当大小调整的 VM 系列。 例如，如果你不打算将生产环境迁移到 Azure 中的 A 系列 VM，可以从列表或系列中排除 A 系列，则只在所选系列中完成适当大小调整。
**舒适因子** | 在评估期间, Azure Migrate 服务器评估会考虑使用缓冲区 (舒适因素)。 该缓冲应用到 VM 的机器使用率数据（CPU、内存、磁盘和网络）上。 舒适因子考虑到季节性使用特点、短期性能历史记录，以及未来使用量可能会增加等问题。<br/><br/> 例如，一个使用率为 20% 的 10 核 VM 通常相当于一个 2 核 VM。 但是，如果舒适因子为 2.0x，则结果就变成一个 4 核 VM。
**产品** | 加入的 [Azure 套餐](https://azure.microsoft.com/support/legal/offer-details/)。 Azure Migrate 会进行相应的成本估算。
**货币** | 计费货币。
**折扣 (%)** | 基于 Azure 套餐获得的任何特定订阅的折扣。<br/> 默认设置是 0%。
**VM 运行时间** | 如果 VM 不会在 Azure 中全天候运行，则可指定运行持续时间（每月的天数和每天的小时数），然后系统就会进行相应的成本估算。<br/> 默认值为“每月 31 天和每天 24 小时”。
**Azure 混合权益** | 指定您是否具有软件保障并且有资格使用[Azure 混合权益](https://azure.microsoft.com/pricing/hybrid-use-benefit/)。 若设为“是”，Windows VM 采用非 Windows Azure 价格。 默认值为“是”。

## <a name="how-are-assessments-calculated"></a>评估如何计算？

Azure Migrate 服务器评估中的评估使用收集的有关本地服务器的元数据进行计算。 评估计算分为三个阶段:对于每个服务器, 评估计算从 Azure 适用性分析开始, 后跟规模调整, 最后是每月成本估计。 如果服务器通过上一个阶段, 则该服务器将移动到更高的阶段。 例如, 如果服务器未通过 Azure 适用性检查, 则会将其标记为不适用于 Azure, 并且不会对相同的大小和成本进行成本调整。

## <a name="azure-suitability-analysis"></a>Azure 适用性分析

并非所有计算机都适用于在 Azure 中运行。 Azure Migrate Server 评估将评估要迁移到 Azure 的每个本地计算机, 并将评估的计算机分类为以下适用性类别之一:
- **Azure 已就绪** - 计算机可以照原样迁移到 Azure，无需任何更改。 它将在 Azure 中启动且具备完整的 Azure 支持。
- **Azure 有条件的就绪** - 计算机可在 Azure 中启动，但可能不具有完整的 Azure 支持。 例如，Azure 中不支持具有旧版 Windows Server OS 的计算机。 在将这些计算机迁移到 Azure 之前，需要特别注意，并遵循评估中建议的修正指南以在迁移前修复准备问题。
- **Azure 未就绪** - 计算机不会在 Azure 中启动。 例如, 如果本地计算机上附加的磁盘大小超过 64 TB, 则不能在 Azure 上托管。 你需要遵循评估中建议的修正指南以在迁移到 Azure 前修复准备问题。 标记为 Azure 未就绪的计算机不会进行适度大小调整和成本估算。
- **就绪情况未知**-Azure Migrate 无法找到计算机的就绪, 因为从本地环境收集的元数据不足。

Azure Migrate Server 评估检查计算机属性和来宾操作系统, 以确定本地计算机的 Azure 准备情况。

### <a name="machine-properties"></a>计算机属性

服务器评估查看本地 VM 的以下属性, 以确定它是否可在 Azure 上运行。

**属性** | **详细信息** | **Azure 迁移就绪性状态**
--- | --- | ---
**启动类型** | Azure 支持启动类型为 BIOS（而非 UEFI）的 VM。 | 如果启动类型为 UEFI，则状态为有条件的就绪。
**核心数** | 计算机中的内核数必须等于或小于 Azure VM 支持的最大内核数量（128 个内核）。<br/><br/> 如果性能历史记录可用，Azure Migrate 会考虑已利用的内核数以进行比较。 如果在评估设置中指定了舒适因子，则将已利用的内核数乘以此舒适因子。<br/><br/> 如果没有任何性能历史记录，Azure Migrate 将使用已分配的内核数，而不应用舒适因子。 | 如果小于或等于限制，则状态为就绪。
**内存** | 计算机内存大小必须等于或小于 Azure VM 允许的最大内存（Azure M 系列 Standard_M128m&nbsp;<sup>2</sup> 上为 3892 GB）。 [了解详细信息](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)。<br/><br/> 如果性能历史记录可用，Azure Migrate 会考虑已利用的内存以进行比较。 如果指定了舒适因子，则将已利用的内存乘以此舒适因子。<br/><br/> 如果没有任何历史记录，将使用已分配的内存，而不应用舒适因子。<br/><br/> | 如果在限制范围内，则状态为就绪。
**存储磁盘** | 已分配的磁盘大小必须32TB 或更少。<br/><br/> 连接到计算机的磁盘（包括操作系统磁盘）数必须为 65 个或更少。 | 如果在限制范围内，则状态为就绪。
**网络** | 连接到计算机的 NIC 数必须为 32 个或更少。 | 如果在限制范围内，则状态为就绪。

### <a name="guest-operating-system"></a>来宾操作系统
Azure Migrate Server 评估连同 VM 属性一起查看计算机的来宾操作系统, 以确定它是否可在 Azure 上运行。

> [!NOTE]
> 对于 VMware Vm, Azure Migrate Server 评估使用 vCenter Server 中为 VM 指定的操作系统进行来宾操作系统分析。 对于在 VMware 上运行的 Linux Vm, 它当前不识别来宾操作系统的确切内核版本。

Azure Migrate Server 评估使用以下逻辑来识别基于操作系统的 Azure 就绪情况。

**操作系统** | **详细信息** | **Azure 迁移就绪性状态**
--- | --- | ---
Windows Server 2016 和所有 SP | Azure 提供完全支持。 | 已做好 Azure 迁移准备
Windows Server 2012 R2 和所有 SP | Azure 提供完全支持。 | 已做好 Azure 迁移准备
Windows Server 2012 和所有 SP | Azure 提供完全支持。 | 已做好 Azure 迁移准备
Windows Server 2008 R2 和所有 SP | Azure 提供完全支持。| 已做好 Azure 迁移准备
Windows Server 2008（32 位和 64 位） | Azure 提供完全支持。 | 已做好 Azure 迁移准备
Windows Server 2003、2003 R2 | 这些操作系统的支持日期已结束，需要[自定义支持协议 (CSA)](https://aka.ms/WSosstatement) 以获取 Azure 支持。 | Azure 有条件的就绪，请考虑在迁移到 Azure 前升级 OS。
Windows 2000、98、95、NT、3.1、MS-DOS | 这些操作系统的支持日期已结束，计算机可以在 Azure 中启动，但 Azure 不提供 OS 支持。 | Azure 有条件的就绪，建议在迁移到 Azure 前升级 OS。
Windows Client 7、8 和 10 | Azure 仅支持 [Visual Studio 订阅。](https://docs.microsoft.com/azure/virtual-machines/windows/client-images) | 已做好特定条件下的 Azure 迁移准备
Windows 10 专业版桌面 | Azure 提供了对[多租户托管权限](https://docs.microsoft.com/azure/virtual-machines/windows/windows-desktop-multitenant-hosting-deployment)的支持。 | 已做好特定条件下的 Azure 迁移准备
Windows Vista、XP Professional | 这些操作系统的支持日期已结束，计算机可以在 Azure 中启动，但 Azure 不提供 OS 支持。 | Azure 有条件的就绪，建议在迁移到 Azure 前升级 OS。
Linux | Azure 予以认可这些 [Linux 操作系统](../virtual-machines/linux/endorsed-distros.md)。 其他 Linux 操作系统可能在 Azure 中启动，但建议在迁移到 Azure 前将 OS 升级到认可的版本。 | 如果版本受到认可，则为 Azure 已就绪。<br/><br/>如果版本不受认可，则为 Azure 有条件的就绪。
其他操作系统<br/><br/> 例如, Oracle Solaris、Apple Mac OS 等。 | Azure 不认可这些操作系统。 计算机课在 Azure 中启动，但 Azure 不提供 OS 支持。 | Azure 有条件的就绪，建议在迁移到 Azure 前安装已升级的 OS。  
vCenter Server 中指定为“其他”  的 OS | 在此情况下，Azure Migrate 无法确认 OS。 | 就绪性未知。 确保 VM 内部运行的 OS 在 Azure 中受到支持。
32 位操作系统 | 计算机可在 Azure 中启动，但 Azure 可能不会提供完全支持。 | Azure 有条件的就绪，在迁移到 Azure 前考虑将计算机的 OS 从 32 位 OS 升级到 64 位 OS。

## <a name="sizing"></a>大小调整

将计算机标记为 "就绪" 后, "服务器评估" 将执行大小调整, 这涉及到为本地 VM 标识适当的 Azure VM 和磁盘 SKU。 根据指定的评估属性, 大小建议会有所不同。

- 如果评估使用*基于性能的大小调整*, Azure Migrate 会考虑计算机的性能历史记录来识别 Azure 中的 VM 大小和磁盘类型。 如果已过度分配本地 VM, 但利用率很低, 并且想要在 Azure 中将 VM 调整为节省成本, 此方法特别有用。 此方法可帮助你在迁移过程中优化大小。
- 如果你不想考虑 VM 大小的性能数据, 并想要按原样将本地计算机与 Azure 一起使用, 则可以指定 "大小调整" 条件, 如 "*本地*" 和 "服务器评估", 然后根据本地情况调整 vm 的大小配置, 而不考虑利用率数据。 在此情况下, 将根据你在评估属性 (标准 HDD 磁盘、标准 SSD 磁盘或高级磁盘) 中指定的存储类型来调整磁盘大小。

### <a name="performance-based-sizing"></a>基于性能的大小

对于基于性能的大小调整, Azure Migrate 服务器评估从附加到 VM 的磁盘开始, 后跟网络适配器, 然后根据本地 VM 的计算要求映射 Azure VM SKU。 Azure Migrate 设备对本地环境进行配置, 以便收集 CPU、内存、磁盘和网络适配器的性能数据。

**性能数据收集**

- 对于 VMware Vm, Azure Migrate 设备每隔20秒收集一次实时采样点, 对于 Hyper-v Vm, 将每隔30秒收集一次实时示例点。
- 然后, 该设备汇总每10分钟收集的样本点, 并将最近10分钟的最大值发送到 Azure Migrate Server 评估。
- Azure Migrate Server 评估存储最近一个月的所有10分钟的样本点, 具体取决于为*性能历史记录*和*百分比利用率*指定的评估属性, 它将标识适当的数据点应该用于正确调整大小的。 例如, 如果将性能历史记录设置为一天, 百分比利用率为 95%, 则它将使用最近一天10分钟的示例点, 按升序对它们进行排序, 并选取第95百分位值进行右调整。
- 然后, 将上述值与舒适系数相乘, 以获取每个指标 (CPU 使用率、内存使用率、磁盘 IOPS (读取和写入)、磁盘吞吐量 (读取和写入)、磁盘吞吐量 (传入和传出) 的有效性能利用率数据。设备会收集。
- 确定有效利用率值后, 计算、存储和网络大小将按如下方式完成:

**存储大小**:Azure Migrate 尝试将附加到计算机的每个磁盘映射到 Azure 中的磁盘。

> [!NOTE]
> Azure Migrate：服务器评估只支持托管磁盘进行评估。

  - 添加了磁盘的读取和写入 IOPS, 以获取所需的总 IOPS, 并添加同样的读取和写入吞吐量值, 以获取每个磁盘的总吞吐量
  - 如果已将存储类型指定为 "自动", 则根据 "有效 IOPS" 和 "吞吐量" 值, Azure Migrate 服务器评估将确定是否应将磁盘映射到 Azure 中的标准 HDD、标准 SSD 或高级磁盘。 如果存储类型设置为标准 HDD/SSD/Premium, 则它将尝试在所选存储类型 (标准 HDD/SSD/高级磁盘) 内查找磁盘 SKU。
  - 如果服务器评估找不到具有所需 IOPS & 吞吐量的磁盘, 则会将计算机标记为不适用于 Azure。
  - 如果找到一组合适的磁盘, 则会选择支持在评估设置中指定的位置的磁盘。
  - 如果有多个合格的磁盘，将选择成本最低的磁盘。
  - 如果任何磁盘的性能数据不可用, 则使用磁盘 (磁盘大小) 的配置数据在 Azure 中查找标准 SSD 磁盘。

**网络大小**:Azure Migrate Server 评估尝试查找可支持连接到本地计算机的网络适配器数量和这些网络适配器所需性能的 Azure VM。
    - 为了获得本地 VM 的有效网络性能, 服务器评估会将每秒传输的数据 (MBps) 从计算机 (网络输出) 中聚合到所有网络适配器上, 并应用舒适因子。 此数值用于查找可支持所需网络性能的 Azure VM。
    - 除了网络性能，它还会考虑 Azure VM 是否可支持所需的网络适配器数量。
    - 如果没有任何网络性能数据可用，则 VM 大小调整仅考虑网络适配器计数。

**计算大小**:计算存储和网络需求后, Azure Migrate 服务器评估会将 CPU 和内存要求视为在 Azure 中查找合适的 VM 大小。
    - Azure Migrate 查看有效利用的核心和内存, 以在 Azure 中查找合适的 VM 大小。
    - 如果找不到合适的大小，计算机将标记为不适用于 Azure。
    - 如果找到了合适的大小，Azure Migrate 将应用存储和网络计算。 然后它将应用位置和定价层设置，以提供最终的 VM 大小建议。
    - 如果有多个合格的 Azure VM 大小，建议选择成本最低的那一个。

### <a name="as-on-premises-sizing"></a>按本地大小调整

如果使用作为*本地大小调整*, 则服务器评估不会考虑 vm 和磁盘的性能历史记录, 并根据本地分配的大小在 Azure 中分配 VM SKU。 同样, 对于磁盘大小调整, 它会查看评估属性 (标准 HDD/SSD/Premium) 中指定的存储类型, 并相应地推荐磁盘类型。 默认存储类型为高级磁盘。

## <a name="confidence-ratings"></a>置信度评级
Azure Migrate 中的每个基于性能的评估都与从一个 (最低) 到五个开始 (最高) 的置信度分级相关联。
- 为评估分配置信度时，会考虑到进行评估计算时所需数据点的可用性。
- 对评估的置信度分级可以用来评估 Azure Migrate 提供的大小建议的可靠性。
- 置信度评级不适用于作为本地评估。
- 对于基于性能的大小调整, Azure Migrate 服务器评估需求:
    - CPU 和 VM 内存的利用率数据。
    - 此外，对于每个附加到 VM 的磁盘，它需要磁盘 IOPS 和吞吐量数据。
    - 同样, 对于附加到 VM 的每个网络适配器, 置信度分级都需要网络 i/o 才能执行基于性能的大小调整。
    - 如果 vCenter Server 中没有以上使用数量, 则大小建议可能不可靠。

根据可用数据点的百分比，提供评估的置信度分级，如下所示：

   **数据点的可用性** | **置信度分级**
   --- | ---
   0%-20% | 1 星
   21%-40% | 2 星
   41%-60% | 3 星
   61%-80% | 4 星
   81%-100% | 5 星

### <a name="low-confidence-ratings"></a>低置信度分级

评估可能获得低置信度的几个原因:

- 你未在创建评估的持续时间内分析环境。 例如, 如果你创建了 "性能持续时间" 设置为一天的评估, 则需要在开始发现所有数据点后等待至少一天。
- 在计算评估期间, 某些 Vm 已关闭。 如果任何 Vm 断电了一段时间, Azure Migrate 服务器评估无法收集该时间段的性能数据。
- 在计算评估的期间内创建了一些 Vm。 例如, 如果为最后一个月的性能历史记录创建评估, 但某些 Vm 只是在一周前的环境中创建的, 则新 Vm 的性能历史记录不会在整个持续时间内。

> [!NOTE]
> 如果任何评估的置信度低于5星级, 我们建议你等待至少一天时间, 让设备对环境进行配置, 然后重新计算评估。 如果不这样做, 基于性能的大小调整可能不可靠, 我们建议你将评估切换为用作本地大小调整。

## <a name="monthly-cost-estimation"></a>每月成本估计

调整大小建议完成后, Azure Migrate 在迁移后计算计算和存储成本。

- **计算成本**：使用建议的 Azure VM 大小，Azure Migrate 使用计费 API 来计算 VM 每月成本。
    - 该计算会考虑操作系统、软件保障、预留实例、VM 运行时间、位置和货币设置。
    - 它将所有计算机的成本求和，计算每月总计算成本。
- **存储成本**：计算机的每月存储成本是通过聚合附加到计算机的所有磁盘的每月成本来计算的
    - Azure Migrate Server 评估通过聚合所有计算机的存储成本来计算每月总存储成本。
    - 当前该计算不考虑在评估设置中指定的优惠。

成本以在评估设置中指定的币种显示。


## <a name="next-steps"></a>后续步骤

为[VMware vm](tutorial-assess-vmware.md)或[hyper-v vm](tutorial-assess-hyper-v.md)创建评估。
