---
title: Azure Site Recovery 监视的常见问题
description: 使用内置监视和 Azure Monitor (Log Analytics) 获取有关 Azure Site Recovery 监视的常见问题的解答
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.date: 07/31/2019
ms.topic: conceptual
ms.author: raynew
ms.openlocfilehash: c1d30a9cdd2cd6ca288edd609a2e2e7bee9174d7
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/01/2019
ms.locfileid: "68718257"
---
# <a name="common-questions-about-site-recovery-monitoring"></a>Site Recovery 监视的常见问题

本文解答了有关监视 Azure [Site Recovery](site-recovery-overview.md)、使用内置 Site Recovery 监视和 Azure Monitor (Log Analytics) 的常见问题。

## <a name="general"></a>常规

### <a name="how-is-the-rpo-value-logged-different-from-the-latest-available-recovery-point"></a>不同于最新可用恢复点的 RPO 值记录方式是什么？

Site Recovery 使用多步骤异步过程将计算机复制到 Azure。

- 在复制的倒数第二步，计算机上发生的最近更改将连同元数据一起复制到日志/缓存存储帐户。
- 这些更改连同标识可恢复点的标记一起写入目标区域中的存储帐户/托管磁盘。
- Site Recovery 现在可以为计算机生成可恢复的点。
- 此时, 已经满足迄今为止已上传到存储帐户的更改。 换句话说, 此时的计算机 RPO 等于从与可恢复点对应的时间戳中的时间量。
- 现在，Site Recovery 会从存储帐户中选取上传的数据，并将其应用到为计算机创建的副本磁盘。
- 然后，Site Recovery 生成一个恢复点，并使此恢复点可用于故障转移时的恢复操作。
- 因此, 最新的可用恢复点指示与已处理的最新恢复点对应的时间戳, 并应用于副本磁盘。


复制源计算机或本地基础结构服务器上的系统时间不正确将会歪曲计算后的 RPO 值。 为准确报告 RPO，请确保所有服务器和计算机上的系统时钟准确。



## <a name="inbuilt-site-recovery-logging"></a>内置 Site Recovery 日志记录


### <a name="why-is-the-vm-count-in-the-vault-infrastructure-view-different-from-the-total-count-shown-in-replicated-items"></a>为什么保管库基础结构视图中的 VM 计数与 "复制的项" 中显示的总计数不同？

保管库基础结构视图已根据复制方案划分了范围。 此视图的计数中仅包含当前选定复制方案中的计算机。 此外，我们只统计配置为复制到 Azure 的 VM。 故障转移的计算机或复制回本地站点的计算机不会在视图中计数。

### <a name="why-is-the-count-of-replicated-items-in-essentials-different-from-the-total-count-of-replicated-items-on-the-dashboard"></a>为什么 Essentials 中复制的项的计数与仪表板上复制项的总数不同？

只有完成了初始复制的计算机才会包含在 Essentials 中显示的计数中。 复制的项总数包括保管库中的所有计算机, 包括初始复制当前正在进行的计算机。

## <a name="azure-monitor-logging"></a>Azure Monitor 日志记录


### <a name="how-often-does-site-recovery-send-diagnostic-logs-to-azure-monitor-log"></a>Site Recovery 将诊断日志发送到 Azure Monitor 日志的频率如何？ 

- AzureSiteRecoveryReplicationStats 和 AzureSiteRecoveryRecoveryPoints 每15分钟发送一次。  
- AzureSiteRecoveryReplicationDataUploadRate 和 AzureSiteRecoveryProtectedDiskDataChurn 每五分钟发送一次。 
- AzureSiteRecoveryJobs 在作业的触发器和完成时发送。
- 每次生成事件时都将发送 AzureSiteRecoveryEvents。 
- 任何环境更改时都会发送 AzureSiteRecoveryReplicatedItems。 通常, 在更改后, 数据刷新时间为15分钟。 

### <a name="how-long-is-data-kept-in-azure-monitor-logs"></a>数据在 Azure Monitor 日志中保留多长时间？ 

默认情况下, 保留期为31天。 你可以在 "Log Analytics" 工作区的 "**使用情况和估计成本**" 部分中增加时间段。 单击 "**数据保留**", 然后选择范围。

### <a name="whats-the-size-of-the-diagnostic-logs"></a>诊断日志的大小是多少？ 

通常, 日志的大小为 15-20 KB。 


## <a name="next-steps"></a>后续步骤

了解如何监视[Site Recovery 内置监视](site-recovery-monitor-and-troubleshoot.md)或[Azure Monitor](monitor-log-analytics.md)。


