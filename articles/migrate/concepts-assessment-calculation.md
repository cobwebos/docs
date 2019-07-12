---
title: 在 Azure Migrate 中评估计算 |Microsoft 文档
description: 概述 Azure Migrate 服务中的评估计算。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: raynew
ms.openlocfilehash: a328549307772cbdf470160cc1ad713fe1ee5e05
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67805988"
---
# <a name="assessment-calculations"></a>评估计算

Azure Migrate Server 评估来评估本地工作负荷适合迁移到 Azure。 本文提供了有关如何计算评估的信息。


[Azure Migrate](migrate-services-overview.md)提供中心，以跟踪发现、 评估和迁移的本地应用和工作负荷，及私钥/公钥云实例，迁移到 Azure。 中心提供了评估和迁移，以及第三方独立软件供应商 (ISV) 产品/服务的 Azure 迁移工具。

## <a name="overview"></a>概述

Azure Migrate Server 评估评估包含三个阶段。 评估的第一步是适用性分析，第二步是大小估计，最后一步是每月成本估计。 如果一台计算机通过了前一个阶段，则只能按顺序进入下一个阶段。 例如，如果一台计算机未通过 Azure 适用性检查，将被标记为不适合迁移到 Azure，并且不会进行大小调整和成本估算。


## <a name="whats-in-an-assessment"></a>评估内容有哪些？

**属性** | **详细信息**
--- | ---
**目标位置** | 要迁移到的 Azure 位置。<br/><br/> Azure Migrate 当前支持这些目标区域：澳大利亚东部、 澳大利亚东南部、 巴西南部、 加拿大中部、 加拿大东部、 印度中部、 美国中部、 中国东部、 中国北部、 亚洲东部、 美国东部、 美国东部 2、 德国中部、 德国东北部日本东部、 日本西部、 韩国中部、 韩国南部、 北部美国中部、 欧洲北部、 美国中南部、 亚洲东南部、 印度南部、 英国南部、 英国西部、 美国亚利桑那州的政府、 美国弗吉尼亚德克萨斯州、 美国弗吉尼亚州政府、 美国中西部、 西欧、 印度西部、 美国西部和美国西部 2 区。<br/> 默认情况下，目标区域设置为“美国西部 2”。
**存储类型** | 标准 HHD 标准磁盘/SSD 磁盘/高级。<br/><br/> 如果指定的存储类型为自动评估中，磁盘建议基于磁盘 （IOPS 和吞吐量） 的性能数据。<br/><br/> 如果您指定为高级/标准存储类型，选择评估建议的磁盘 SKU 中的存储类型。<br/><br/> 如果你想要实现 99.9%的单实例 VM SLA，你可以将存储类型为高级托管磁盘。 然后会为高级托管磁盘，建议在评估中的所有磁盘。 <br/> Azure Migrate 仅支持使用托管磁盘进行迁移评估。<br/> 
**保留的实例 (RI)** | 如果你具有保留 Azure 中的实例，请指定此属性。 在评估中的成本的估计将 RI 折扣考虑在内。 保留的实例当前都是仅对即用即付 Azure Migrate 中提供了受支持。
**“大小调整”条件** | 用于映射到适当大小的 Vm。 可以基于性能的大小调整或-在本地，而无需考虑性能历史记录。
**性能历史记录** | 要考虑评估 VM 性能的持续时间。 基于性能的大小调整时，此属性才适用。
**百分位使用率** | 用于调整 Vm 的性能样本百分位值。 基于性能的大小调整时，此属性才适用。
**VM 系列** | 用于大小评估的 VM 系列。 例如，如果不打算将生产环境迁移到 Azure 中的 A 系列 VM，可以从列表或系列中排除 A 系列。 大小调整仅取决于所选系列。
**舒适因子** | 在评估期间，azure Migrate Server 评估会考虑到缓冲 （舒适因子）。 该缓冲应用到 VM 的机器使用率数据（CPU、内存、磁盘和网络）上。 舒适因子考虑到季节性使用特点、短期性能历史记录，以及未来使用量可能会增加等问题。<br/><br/> 例如，一个使用率为 20% 的 10 核 VM 通常相当于一个 2 核 VM。 但是，如果舒适因子为 2.0x，则结果就变成一个 4 核 VM。
**产品** | 加入的 [Azure 套餐](https://azure.microsoft.com/support/legal/offer-details/)。 Azure Migrate 会进行相应的成本估算。
**货币** | 计费货币。 
**折扣 (%)** | 基于 Azure 套餐获得的任何特定订阅的折扣。<br/> 默认设置是 0%。
**VM 运行时间** | 如果 VM 不会在 Azure 中全天候运行，则可指定运行持续时间（每月的天数和每天的小时数），然后系统就会进行相应的成本估算。<br/> 默认值为“每月 31 天和每天 24 小时”。
**Azure 混合权益** | 指定是否具有软件保障和符合条件[Azure 混合权益](https://azure.microsoft.com/pricing/hybrid-use-benefit/)。 若设为“是”，Windows VM 采用非 Windows Azure 价格。 



## <a name="azure-suitability-analysis"></a>Azure 适用性分析

并非所有计算机都都适合在 Azure 中运行。 Azure Migrate Server 评估评估每个本地计算机上的进行迁移，并将计算机分类到以下适用性类别之一：
- **Azure 已就绪** - 计算机可以照原样迁移到 Azure，无需任何更改。 它将在 Azure 中启动且具备完整的 Azure 支持。
- **Azure 有条件的就绪** - 计算机可在 Azure 中启动，但可能不具有完整的 Azure 支持。 例如，Azure 中不支持具有旧版 Windows Server OS 的计算机。 在将这些计算机迁移到 Azure 之前，需要特别注意，并遵循评估中建议的修正指南以在迁移前修复准备问题。
- **Azure 未就绪** - 计算机不会在 Azure 中启动。 例如，如果本地计算机附加有超过 4 TB 大小的磁盘，则该计算机无法在 Azure 上托管。 你需要遵循评估中建议的修正指南以在迁移到 Azure 前修复准备问题。 标记为 Azure 未就绪的计算机不会进行适度大小调整和成本估算。
- **就绪性未知** - 由于 vCenter Server 中的可用数据不足，Azure Migrate 无法找到计算机的就绪情况。



### <a name="machine-properties"></a>计算机属性

Azure Migrate 检查本地 VM，以确定是否它可以在 Azure 中运行的以下属性。

**属性** | **详细信息** | **Azure 迁移就绪性状态**
--- | --- | ---
**启动类型** | Azure 支持启动类型为 BIOS（而非 UEFI）的 VM。 | 如果启动类型为 UEFI，则状态为有条件的就绪。
**核心数** | 计算机中的内核数必须等于或小于 Azure VM 支持的最大内核数量（128 个内核）。<br/><br/> 如果性能历史记录可用，Azure Migrate 会考虑已利用的内核数以进行比较。 如果在评估设置中指定了舒适因子，则将已利用的内核数乘以此舒适因子。<br/><br/> 如果没有任何性能历史记录，Azure Migrate 将使用已分配的内核数，而不应用舒适因子。 | 如果小于或等于限制，则状态为就绪。
**内存** | 计算机内存大小必须等于或小于 Azure VM 允许的最大内存（Azure M 系列 Standard_M128m&nbsp;<sup>2</sup> 上为 3892 GB）。 [了解详细信息](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)。<br/><br/> 如果性能历史记录可用，Azure Migrate 会考虑已利用的内存以进行比较。 如果指定了舒适因子，则将已利用的内存乘以此舒适因子。<br/><br/> 如果没有任何历史记录，将使用已分配的内存，而不应用舒适因子。<br/><br/> | 如果在限制范围内，则状态为就绪。
**存储磁盘** | 分配的磁盘大小必须为 4 TB (4096 GB) 或更小。<br/><br/> 连接到计算机的磁盘（包括操作系统磁盘）数必须为 65 个或更少。 | 如果在限制范围内，则状态为就绪。
**网络** | 连接到计算机的 NIC 数必须为 32 个或更少。 | 如果在限制范围内，则状态为就绪。

### <a name="guest-operating-system"></a>来宾操作系统
除了 VM 属性，Azure Migrate Server 评估着重于来宾操作系统的计算机上，若要确定是否它可以在 Azure 中运行。

> [!NOTE]
> Azure Migrate Server 评估使用以进行分析的 vCenter Server 中为 VM 指定的操作系统。 Azure Migrate Server 评估是基于设备的 VM 发现，并且不能验证在 VM 上运行的操作系统是否与指定 vCenter Server 中相同。

Azure Migrate Server 评估，使用以下逻辑来标识基于操作系统的 Azure 迁移就绪性。

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
其他操作系统<br/><br/> 例如 Oracle Solaris、Apple Mac 操作系统、FreeBSD 等。 | Azure 不认可这些操作系统。 计算机课在 Azure 中启动，但 Azure 不提供 OS 支持。 | Azure 有条件的就绪，建议在迁移到 Azure 前安装已升级的 OS。  
vCenter Server 中指定为“其他”  的 OS | 在此情况下，Azure Migrate 无法确认 OS。 | 就绪性未知。 确保 VM 内部运行的 OS 在 Azure 中受到支持。
32 位操作系统 | 计算机可在 Azure 中启动，但 Azure 可能不会提供完全支持。 | Azure 有条件的就绪，在迁移到 Azure 前考虑将计算机的 OS 从 32 位 OS 升级到 64 位 OS。

## <a name="sizing"></a>大小调整

计算机被标记为 Azure 已就绪后，Azure Migrate 会调整 VM 大小及其用于 Azure 的磁盘大小。

- 如果评估使用基于性能的大小调整，Azure Migrate 将考虑要确定在 Azure 中的 VM 大小和磁盘类型的计算机的性能历史记录。 如果已过度分配的本地 VM 但利用率较低，并且想正确调整其大小以节约成本的 Azure 中的 VM，此方法将非常有用。
- 如果你是使用作为本地评估，Azure Migrate Server 评估上的本地设置，而无需考虑使用率数据的基于 Vm 的大小。 磁盘大小调整，在这种情况下，基于您在评估属性 （标准磁盘或高级磁盘） 中指定的存储类型。

### <a name="performance-based-sizing"></a>基于性能的大小

对于基于性能的大小调整，Azure Migrate 开始附加到 VM，网络适配器后, 跟的磁盘，然后映射 Azure VM 根据在本地 VM 的计算要求。

- **存储**：Azure Migrate 尝试将附加到计算机的每个磁盘映射到 Azure 中的磁盘。
    - 为获取有效的磁盘 I/O 每秒 (IOPS) 和吞吐量 (MBps)，Azure Migrate 将用舒适因子乘以磁盘 IOPS 和吞吐量。 基于有效的 IOPS 和吞吐量值，Azure Migrate 将确认磁盘应映射到 Azure 中的标准磁盘还是高级磁盘。
    - 如果 Azure Migrate 找不到具有所需 IOPS 和吞吐量的磁盘，则会将计算机标记为不适用于 Azure。 [详细了解](../azure-subscription-service-limits.md#storage-limits) Azure 对每磁盘和 VM 的限制。
    - 如果找到了一组合适的磁盘，Azure Migrate 将选择支持存储冗余方法以及在评估设置中指定的位置的磁盘。
    - 如果有多个合格的磁盘，将选择成本最低的磁盘。
    - 如果磁盘的性能数据不可用，所有磁盘将都映射到 Azure 中的标准磁盘。

- **网络**：Azure Migrate 将尝试查找可支持本地计算机所附加的网络适配器数量以及这些网络适配器所要求的性能的 Azure VM。
    - 为获取本地 VM 的有效网络性能，Azure Migrate 将从计算机（网络流出）、跨所有网络适配器聚合每秒传输的数据 (MBps)，并应用舒适因子。 此数值用于查找可支持所需网络性能的 Azure VM。
    - 除了网络性能，它还会考虑 Azure VM 是否可支持所需的网络适配器数量。
    - 如果没有任何网络性能数据可用，则 VM 大小调整仅考虑网络适配器计数。

- **计算**：计算存储和网络需求后，Azure Migrate 将考虑 CPU 和内存需求以查找 Azure 中合适的 VM 大小。
    - Azure Migrate 查看已利用的内核和内存，并应用舒适因子来获取有效的内核和内存。 将基于该数量尝试在 Azure 中查找合适的 VM 大小。
    - 如果找不到合适的大小，计算机将标记为不适用于 Azure。
    - 如果找到了合适的大小，Azure Migrate 将应用存储和网络计算。 然后它将应用位置和定价层设置，以提供最终的 VM 大小建议。
    - 如果有多个合格的 Azure VM 大小，建议选择成本最低的那一个。

### <a name="as-on-premises-sizing"></a>按本地大小调整

如果使用作为本地大小调整时，服务器评估分配 Azure 根据大小本地中的 VM SKU。 同样对于磁盘的大小调整，它将查找在评估属性中指定的存储类型（标准/高级），并相应地建议磁盘类型。 默认存储类型为高级磁盘。

## <a name="confidence-ratings"></a>置信度评级
在 Azure Migrate 中的每次基于性能的评估是与范围从一个 （最低） 到五个 （最高） 启动的置信度分级相关联。
- 为评估分配置信度时，会考虑到进行评估计算时所需数据点的可用性。
- 对评估的置信度分级可以用来评估 Azure Migrate 提供的大小建议的可靠性。
- 置信度分级并不适用于作为本地评估。
- 对于基于性能的大小调整，Azure Migrate Server 评估需要：
    - CPU 的使用率数据和 VM 内存。
    - 此外，对于每个附加到 VM 的磁盘，它需要磁盘 IOPS 和吞吐量数据。
    - 同样对于每个网络适配器附加到 VM，则置信度分级需要网络 I/O 来进行基于性能的大小调整。
    - 如果上述利用率数据的任何不在 vCenter Server 中可用，大小建议可能不是可靠的。 

根据可用数据点的百分比，提供评估的置信度分级，如下所示：

   **数据点的可用性** | **置信度分级**
   --- | ---
   0%-20% | 1 星
   21%-40% | 2 星
   41%-60% | 3 星
   61%-80% | 4 星
   81%-100% | 5 星

### <a name="low-confidence-ratings"></a>低置信度评级

几个原因评估可能会较低置信度分级的原因：

- 没有要为其创建评估期间分析你的环境。 例如，如果使用性能持续时间设置为 1 天创建评估，需要等待至少一天后开始收集所有的数据点的发现。
- 评估计算期间的某些 Vm 已关闭。 如果任何 Vm 停机了一段时间，Azure Migrate Server 评估不能在该时间段的收集性能数据。
- 为其计算评估期间已创建一些 Vm。 例如，如果创建的性能历史记录的最后一个月的评估，但某些 Vm 已在环境中创建仅一周前，新的 Vm 的性能历史记录将存在的整个持续时间。

  > [!NOTE]
  > 如果任何评估的置信度分级低于 5 星，我们建议至少等待一天，设备无法在环境中，配置文件，然后重新计算评估。 如果不这样做，基于性能的大小调整可能不可靠，我们建议改用评估，以便使用作为本地大小调整。
  
## <a name="monthly-cost-estimation"></a>每月成本估计

大小调整建议完成后，Azure Migrate 迁移后计算计算和存储的成本。

- **计算成本**：使用建议的 Azure VM 大小，Azure Migrate 使用计费 API 来计算 VM 每月成本。
    - 该计算会考虑操作系统、软件保障、预留实例、VM 运行时间、位置和货币设置。
    - 它将所有计算机的成本求和，计算每月总计算成本。
- **存储成本**：一台计算机的每月存储成本加上所有磁盘附加到计算机的每月成本
    - Azure Migrate Server 评估的所有计算机的存储成本求和计算每月的总存储成本。
    - 当前该计算不考虑在评估设置中指定的优惠。

成本以在评估设置中指定的币种显示。


## <a name="next-steps"></a>后续步骤

为创建评估[VMware Vm](tutorial-assess-vmware.md)或[的 HYPER-V Vm](tutorial-assess-hyper-v.md)。
