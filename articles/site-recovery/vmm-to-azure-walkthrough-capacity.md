---
title: "规划容量和缩放以使用 Azure Site Recovery 将 Hyper-V VM（包含 VMM）复制到 Azure | Microsoft Docs"
description: "使用 Azure Site Recovery 在 VMM 云中将 Hyper-V VM 复制到 Azure 时，请根据本文规划容量和缩放"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 41c3c83e-6b1a-496a-8179-498c552ef0c7
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/23/2017
ms.author: raynew
ms.openlocfilehash: ab1dc21a829140f8cd2e57837d83a05b0d71bcdf
ms.sourcegitcommit: 422efcbac5b6b68295064bd545132fcc98349d01
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2017
---
# <a name="step-3-plan-capacity-and-scaling-for-hyper-v-with-vmm-to-azure-replication"></a>步骤 3：规划容量和缩放以将 Hyper-V（带 VMM）复制到 Azure

查看[部署先决条件](vmm-to-azure-walkthrough-prerequisites.md)后，使用 [Azure Site Recovery](site-recovery-overview.md) 将 System Center Virtual Machine Manager (VMM) 云中的本地 Hyper-V VM 复制到 Azure 时，请根据本文规划容量和缩放。

阅读本文后，欢迎在页面底部发表任何看法，或者在 [Azure 恢复服务论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)上咨询技术问题。


## <a name="how-do-i-start-capacity-planning"></a>如何开始容量规划？


收集有关复制环境的信息，然后使用此数据以及本文突出显示的注意事项规划容量。


## <a name="gather-information"></a>收集信息

1. 收集有关复制环境的信息，包括 VM 数、每个 VM 的磁盘数和每个磁盘的存储空间。
2. 确定已复制数据的每日更改（改动）率。 请下载 [Hyper-V 容量计划工具](https://www.microsoft.com/download/details.aspx?id=39057)来获取更改率。 我们建议运行此工具一周以上的时间以获取平均值。
 

## <a name="figure-out-capacity"></a>规划容量

根据你已收集的信息，使用 [Site Recovery Capacity Planner 工具](http://aka.ms/asr-capacity-planner-excel)分析源环境和工作负荷，估计带宽需求、源位置所需的服务器资源，以及目标位置中所需的资源（虚拟机和存储等）。 可以通过多种模式运行该工具：

- 快速规划：在此模式下运行该工具时，可以根据 VM 平均数、磁盘、存储和更改率来获取网络和服务器投影。
- 详细规划：在此模式下运行该工具时，可以提供在 VM 级别每个工作负荷的详细信息。 分析 VM 兼容性，获取网络和服务器投影。

现在，运行该工具：

1. 下载该[工具](http://aka.ms/asr-capacity-planner-excel)
2. 若要运行快速规划器，请遵循[这些说明](site-recovery-capacity-planner.md#run-the-quick-planner)，并选择方案“Hyper-V 到 Azure”。
3. 若要运行详细规划器，请遵循[这些说明](site-recovery-capacity-planner.md#run-the-detailed-planner)，并选择方案“Hyper-V 到 Azure”。

## <a name="control-network-bandwidth"></a>控制网络带宽

计算所需带宽后，你有几个选项可用来控制用于复制的带宽量：

* **限制带宽**：复制到 Azure 的 Hyper-V 流量经过特定的 Hyper-V 主机。 可以在主机服务器上限制带宽。
* **影响带宽**：可以使用几个注册表项来控制用于复制的带宽。

### <a name="throttle-bandwidth"></a>限制带宽
1. 在 Hyper-V 主机服务器上打开 Microsoft Azure 备份 MMC 管理单元。 默认情况下，Microsoft Azure 备份的快捷方式位于桌面上或 C:\Program Files\Microsoft Azure Recovery Services Agent\bin\wabadmin 中。
2. 在管理单元中，单击“更改属性”。
3. 在“限制”选项卡上，选择“为备份操作启用 Internet 带宽使用限制”，并设置工作时间和非工作时间的限制。 有效范围为 512 Kbps 到 102 Mbps。

    ![限制带宽](./media/vmm-to-azure-walkthrough-capacity/throttle2.png)

也可以使用 [Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409.aspx) cmdlet 来设置限制。 下面是一个示例：

    $mon = [System.DayOfWeek]::Monday
    $tue = [System.DayOfWeek]::Tuesday
    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth  (512*1024) -NonWorkHourBandwidth (2048*1024)

**Set-OBMachineSetting -NoThrottle** 表示不需要限制。

### <a name="influence-network-bandwidth"></a>影响网络带宽
1. 在注册表中导航到 **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsofte Azure Backup\Replication**。
   * 若要影响复制磁盘上的带宽流量，请修改 **UploadThreadsPerVM** 值，或者创建该项（如果不存在）。
   * 若要影响用于从 Azure 故障回复流量的带宽，请修改 **DownloadThreadsPerVM** 值。
2. 默认值为 4。 在“过度预配型”网络中，这些注册表项需要更改，不能使用默认值。 最大值为 32。 监视流量以优化值。

## <a name="next-steps"></a>后续步骤

转到[第 4 步：计划网络](vmm-to-azure-walkthrough-network.md)。
