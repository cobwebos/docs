---
title: 有关 Azure Site Recovery 监视的常见问题
description: 获取使用内置监视功能和 Azure Monitor (Log Analytics) 进行 Azure Site Recovery 监视时出现的常见问题的解答
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.date: 07/31/2019
ms.topic: conceptual
ms.author: raynew
ms.openlocfilehash: 66ce267581d4748ea51a3dcbd7caa61907115cc1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "82131159"
---
# <a name="common-questions-about-site-recovery-monitoring"></a>有关 Site Recovery 监视的常见问题

本文回答使用内置 Site Recovery 监视功能和 Azure Monitor (Log Analytics) 来监视 Azure [Site Recovery](site-recovery-overview.md) 时出现的常见问题。

## <a name="general"></a>常规

### <a name="how-is-the-rpo-value-logged-different-from-the-latest-available-recovery-point"></a>记录的 RPO 值与最新可用恢复点有何不同？

Site Recovery 使用多步骤异步过程将计算机复制到 Azure。

- 在复制的倒数第二步，计算机上发生的最近更改将连同元数据一起复制到日志/缓存存储帐户。
- 这些更改将连同用于标识可恢复点的标记一起写入到目标区域中的存储帐户/托管磁盘。
- 现在，Site Recovery 可以生成计算机的可恢复点。
- 此时，表示已符合 RPO，可将更改上传到存储帐户。 换而言之，此时的计算机 RPO 等于从对应于可恢复点的时间戳开始消逝的时间。
- 现在，Site Recovery 会从存储帐户中选取上传的数据，并将其应用到为计算机创建的副本磁盘。
- 然后，Site Recovery 生成一个恢复点，并使此恢复点可用于故障转移时的恢复操作。
- 因此，最新可用恢复点表示与已处理并已应用到副本磁盘的最新恢复点对应的时间戳。


如果复制源计算机或本地基础结构服务器上的系统时间不正确，则会导致计算出的 RPO 值有偏差。 为准确报告 RPO，请确保所有服务器和计算机上的系统时钟准确。



## <a name="inbuilt-site-recovery-logging"></a>内置 Site Recovery 日志记录


### <a name="why-is-the-vm-count-in-the-vault-infrastructure-view-different-from-the-total-count-shown-in-replicated-items"></a>保管库基础结构视图中的 VM 计数为何与“复制的项”中显示的总计数不同？

保管库基础结构视图已根据复制方案划分了范围。 只有当前选定的复制方案中的计算机才包含在该视图中。 此外，我们只统计配置为复制到 Azure 的 VM。 已故障转移的计算机或者复制回到本地站点的计算机不会在该视图中统计。

### <a name="why-is-the-count-of-replicated-items-in-essentials-different-from-the-total-count-of-replicated-items-on-the-dashboard"></a>“概要”中的已复制项计数为何与仪表板上的已复制项总计数不同？

只有已完成初始复制的计算机才会包含在“概要”显示的计数中。 “复制的项”总数包括保管库中的所有计算机，其中包括正在进行初始复制的计算机。

## <a name="azure-monitor-logging"></a>Azure Monitor 日志记录


### <a name="how-often-does-site-recovery-send-resource-logs-to-azure-monitor-log"></a>Site Recovery 每隔多久将资源日志发送到 Azure Monitor 日志？ 

- AzureSiteRecoveryReplicationStats 和 AzureSiteRecoveryRecoveryPoints 每 15 分钟发送一次。  
- AzureSiteRecoveryReplicationDataUploadRate 和AzureSiteRecoveryProtectedDiskDataChurn 每五分钟发送一次。 
- 可以通过触发器发送 AzureSiteRecoveryJobs，在作业完成时也会发送它。
- 只要生成事件，就会发送 AzureSiteRecoveryEvents。 
- 只要环境变化，就会发送 AzureSiteRecoveryReplicatedItems。 通常情况下，数据刷新时间在更改后的 15 分钟。 

### <a name="how-long-is-data-kept-in-azure-monitor-logs"></a>数据保留在 Azure Monitor 日志中多长时间？ 

默认情况下，保留期为 31 天。 可以在 Log Analytics 工作区的“使用情况和估计成本”部分延长该期限。 单击“数据保留期”，然后选择范围。

### <a name="whats-the-size-of-the-resource-logs"></a>资源日志的大小是多少？ 

日志大小通常为 15-20 KB。 


## <a name="next-steps"></a>后续步骤

了解如何使用 [Site Recovery 内置监视功能](site-recovery-monitor-and-troubleshoot.md)或 [Azure Monitor](monitor-log-analytics.md) 进行监视。


