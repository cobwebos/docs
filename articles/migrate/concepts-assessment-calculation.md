---
title: "在 Azure Migrate 中评估计算 |Microsoft 文档"
description: "概述 Azure Migrate 服务中的评估计算。"
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 12/12/2017
ms.author: raynew
ms.openlocfilehash: 2b274244cc7b7fd0fc3eee22a57a51db77370370
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/12/2017
---
# <a name="assessment-calculations"></a>评估计算

[Azure Migrate](migrate-overview.md) 会评估要迁移到 Azure 的本地工作负载。 本文提供了有关如何计算评估的信息。



## <a name="overview"></a>概述

Azure Migrate 评估包含三个阶段。 评估的第一步是适用性分析，第二步是基于性能的大小估计，最后一步是每月成本估计。 如果一台计算机通过了前一个阶段，则只能按顺序进入下一个阶段。 例如，如果一台计算机未通过 Azure 适用性检查，将被标记为不适合迁移到 Azure，并且不会计算大小和成本。 


## <a name="azure-suitability-analysis"></a>Azure 适用性分析

要迁移到 Azure 的计算机必须满足 Azure 要求和限制。 例如，如果一个本地 VM 磁盘大于 4 TB，则不能在 Azure 上托管。 下表概述了 Azure 符合性检查。 

**检查** | **详细信息**
--- | ---
**启动类型** | 来宾操作系统磁盘的启动类型必须是 BIOS，而不是 UEFI。
**核心数** | 计算机中的内核数必须等于（或小于）Azure VM 支持的最大内核数量 (32)。<br/><br/> 如果性能历史记录可用，Azure Migrate 会考虑已利用的内核数以进行比较。 如果在评估设置中指定了舒适因子，则将已利用的内核数乘以此舒适因子。<br/><br/> 如果没有任何性能历史记录，Azure Migrate 将使用已分配的内核数，而不应用舒适因子。
**内存** | 计算机内存大小必须等于（或小于）Azure VM 允许的最大内存 (448 GB)。 <br/><br/> 如果性能历史记录可用，Azure Migrate 会考虑已利用的内存以进行比较。 如果指定了舒适因子，则将已利用的内存乘以此舒适因子。<br/><br/> 如果没有任何历史记录，将使用已分配的内存，而不应用舒适因子。<br/><br/> 
**Windows Server 2003-2008 R2** | 32 位和 64 位支持。<br/><br/> Azure 仅提供最大努力支持。
**Windows Server 2008 R2 和所有 SP** | 64 位支持。<br/><br/> Azure 提供完全支持。
**Windows Server 2012 和所有 SP** | 64 位支持。<br/><br/> Azure 提供完全支持。
**Windows Server 2012 R2 和所有 SP** | 64 位支持。<br/><br/> Azure 提供完全支持。
**Windows Server 2016 和所有 SP** | 64 位支持。<br/><br/> Azure 提供完全支持。
**Windows Client 7 和更高版本** | 64 位支持。<br/><br/> Azure 仅支持 Visual Studio 订阅。
**Linux** | 64 位支持。<br/><br/> Azure 对这些[操作系统](../virtual-machines/linux/endorsed-distros.md)提供完全支持。
**存储磁盘** | 分配的磁盘大小必须为 4 TB (4096 GB) 或更小。<br/><br/> 连接到计算机的磁盘（包括操作系统磁盘）数必须为 65 个或更少。 
**网络** | 连接到计算机的 NIC 数必须为 32 个或更少。


## <a name="performance-based-sizing"></a>基于性能的大小

计算机被标记为适合迁移到 Azure 后，Azure Migrate 会使用以下条件将其映射到 Azure 中的 VM 大小：

- 存储检查：Azure Migrate 尝试将连接到计算机的每个磁盘映射到 Azure 中的磁盘：Azure Migrate 将每秒 I/O 数 (IOPS) 乘以舒适因子。 同时，它还将每个磁盘的吞吐量（以 MBps 为单位）乘以舒适因子。 这样可得到有效磁盘 IOPS 和吞吐量。 Azure Migrate 基于此值将磁盘映射到 Azure 中的标准或高级磁盘。
    - 如果服务找不到具有所需 IOPS 和吞吐量的磁盘，则会将计算机标记为不适用于 Azure。
    - 如果找到了一组合适的磁盘，Azure Migrate 将选择支持存储冗余方法以及在评估设置中指定的位置的磁盘。
    - 如果有多个合格的磁盘，将选择成本最低的磁盘。
- 存储磁盘吞吐量：[详细了解](../azure-subscription-service-limits.md#storage-limits)有关 Azure 对单个磁盘和 VM 的限制。
- 磁盘类型：Azure Migrate 仅支持托管磁盘。
- 网络检查：Azure Migrate 将尝试查找可以支持本地计算机上 NIC 数量的 Azure VM。
    - 要执行此操作，其将所有 NIC 上每秒从计算机输出（网络流出量）的数据 (MBps) 求和，并对求得的和应用舒适因子。 此数值用于查找可支持所需网络性能的 Azure VM。
    - Azure Migrate 采用 VM 的网络设置，并假设它是数据中心之外的网络。
    - 如果没有任何网络性能数据可用，则 VM 大小仅考虑 NIC 计数。
- 计算检查：计算存储和网络要求后，Azure Migrate 将考虑计算要求：
    - 如果性能数据可用于 VM，将查找已利用的内核数和内存，并应用舒适因子。 将基于该数量尝试在 Azure 中查找合适的 VM 大小。
    - 如果找不到合适的大小，计算机将标记为不适用于 Azure。
    - 如果找到了合适的大小，Azure Migrate 将应用存储和网络计算。 然后它将应用位置和定价层设置，以提供最终的 VM 大小建议。


## <a name="monthly-cost-estimation"></a>每月成本估计

大小建议完成后，Azure Migrate 将计算迁移后计算和存储成本。

- 计算成本：使用建议的 Azure VM 大小，Azure Migrate 使用计费 API 来计算 VM 每月成本。 该计算会考虑操作系统、软件保障、位置和货币设置。 它将所有计算机的成本求和，计算每月总计算成本。
- 存储成本：一台计算机的每月存储成本的计算方法为，将连接到该计算机的所有磁盘的每月成本求和。 Azure Migrate 通过将所有计算机的存储成本求和，计算每月总存储成本。 当前该计算不考虑在评估设置中指定的优惠。

成本以在评估设置中指定的币种显示。 


## <a name="next-steps"></a>后续步骤

[为本地 VMware VM 设置评估](tutorial-assessment-vmware.md)
