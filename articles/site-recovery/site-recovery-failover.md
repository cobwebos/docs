---
title: "Site Recovery 中的故障转移 | Microsoft Docs"
description: "Azure Site Recovery 可以协调虚拟机和物理服务器的复制、故障转移与恢复。 了解有关故障转移到 Azure 或辅助数据中心的信息。"
services: site-recovery
documentationcenter: 
author: prateek9us
manager: gauravd
editor: 
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 2/15/2017
ms.author: pratshar
translationtype: Human Translation
ms.sourcegitcommit: b818d5083f1436035185b1b0d7990b5a36716da4
ms.openlocfilehash: 1fca09ad0c9e1bc72109910cd0dcaf186d6a7c3d
ms.lasthandoff: 02/23/2017


---
# <a name="failover-in-site-recovery"></a>Site Recovery 中的故障转移
本文介绍如何故障转移受 Site Recovery 保护的虚拟机和物理服务器。 

## <a name="prerequisites"></a>先决条件
1. 执行故障转移之前，请先执行[测试故障转移](site-recovery-test-failover-to-azure.md)，确保一切按预期工作。 
1. 执行故障转移之前，请在目标位置[准备好网络](site-recovery-network-design.md)。  


## <a name="run-a-failover"></a>运行故障转移
本过程介绍如何根据[恢复计划](site-recovery-create-recovery-plans.md)运行故障转移。 或者，也可以通过“复制的项”页针对单个虚拟机或物理服务器运行故障转移。


![故障转移](./media/site-recovery-failover/Failover.png)

1. 选择“**恢复计划**” > “*recoveryplan_name*”。 单击“故障转移” 
2. 在“故障转移”屏幕上，选择要故障转移到的“恢复点”。 可以使用以下选项之一：
    1.    **最新**（默认选项）：此选项首先处理已发送到 Site Recovery 服务的所有数据，为每个虚拟机创建恢复点，然后再将其故障转移到该恢复点。 此选项提供最低 RPO（恢复点目标），因为故障转移后创建的虚拟机包含触发故障转移时已复制到 Site Recovery 服务的所有数据。 
    1.  **最新处理**：此选项将恢复计划的所有虚拟机故障转移到已由 Site Recovery 服务处理的最新恢复点。 对虚拟机执行测试故障转移时，还会显示最新处理的恢复点的时间戳。 如果要针对恢复计划执行故障转移，可以转到单个虚拟机，并查看“最新恢复点”磁贴来获取此信息。 由于不会花费时间来处理未经处理的数据，此选项是 RTO（恢复时间目标）较低的故障转移选项。 
    1.    **最新应用一致**：此选项将恢复计划的所有虚拟机故障转移到已由 Site Recovery 服务处理的最新应用程序一致的恢复点。 对虚拟机执行测试故障转移时，还会显示最新应用一致性恢复点的时间戳。 如果要针对恢复计划执行故障转移，可以转到单个虚拟机，并查看“最新恢复点”磁贴来获取此信息。 
    1.    **自定义**：如果正在对虚拟机执行测试故障转移，则可以使用此选项故障转移到特定恢复点。

    > [!NOTE]
    > 仅当故障转移到 Azure 时，选择恢复点的选项才可用。 
    >
    > 


1. 如果恢复计划中的某些虚拟机在上一个轮次中已故障转移，并且这些虚拟机在源位置和目标位置现已处于活动状态，则可以使用“更改方向”选项来确定故障转移的方向。
1. 如果要故障转移到 Azure 并且为云启用了数据加密（仅当通过 VMM 服务器保护 Hyper-V 虚拟机时才适用），请在“加密密钥”中，选择在 VMM 服务器上进行设置期间启用数据加密时颁发的证书。
1. 如果希望 Site Recovery 在触发故障转移之前尝试关闭源虚拟机，请选择“在开始故障转移之前关闭计算机”。 即使关机失败，故障转移也仍会继续。  
          
    > [!NOTE]
    > 对于 Hyper-V 虚拟机，使用此选项还会在触发故障转移之前，尝试将尚未发送到服务的本地数据同步。 
    >
    > 
     
1. 可以在“作业”页上跟踪故障转移进度。 即使在非计划的故障转移期间发生错误，恢复计划也会运行到完成为止。
1. 故障转移后，请登录到虚拟机来对它进行验证。 如果想要转到虚拟机的另一个恢复点，可以使用“更改恢复点”选项。
1. 如果故障转移后的虚拟机符合要求，可以**提交**故障转移。 这会删除服务中的所有恢复点，并且“更改恢复点”选项将不再可用。

## <a name="planned-failover"></a>计划的故障转移
除了故障转移以外，使用 Site Recovery 保护的 Hyper-V 虚拟机也支持**计划的故障转移**。 这是一个不会丢失任何数据的故障转移选项。 触发计划的故障转移时，首先会关闭源虚拟机，同步尚未同步的数据，然后触发故障转移。 

> [!NOTE]
> 在不同的本地站点之间故障转移 Hyper-V 虚拟机时，若要返回到主要本地站点，必须先将虚拟机**反向复制**回到主站点，然后触发故障转移。 如果主虚拟机不可用，则在开始**反向复制**之前，必须从备份还原虚拟机。   
>
> 

## <a name="failover-job"></a>故障转移作业

![故障转移](./media/site-recovery-failover/FailoverJob.png)

触发故障转移时，将执行以下步骤：

1. 先决条件检查：此步骤确保满足故障转移所需的所有条件
1. 故障转移：此步骤处理并准备好数据，以便能够基于这些数据创建 Azure 虚拟机。 如果选择了“最新”恢复点，此步骤将基于发送到服务的数据创建恢复点。
1. 开始：此步骤使用上一步骤中处理的数据创建 Azure 虚拟机。

> [!WARNING]
> **不要取消正在进行的故障转移**：开始故障转移之前，将停止虚拟机的复制。 如果**取消**正在进行的作业，故障转移将会停止，但虚拟机不会开始复制。 无法再次开始复制。 
>
> 



## <a name="using-scripts-in-failover"></a>在故障转移中使用脚本
在执行故障转移时，你可能想要将某些操作自动化。 在[恢复计划](site-recovery-create-recovery-plans.md)中使用脚本或 [Azure 自动化 Runbook](site-recovery-runbook-automation.md) 可以实现此目的。

## <a name="other-considerations"></a>其他注意事项
* **驱动器号** - 若要在故障转移后保留虚拟机上的驱动器号，可将虚拟机的“SAN 策略”设置为 **OnlineAll**。 [了解详细信息](https://support.microsoft.com/en-us/help/3031135/how-to-preserve-the-drive-letter-for-protected-virtual-machines-that-are-failed-over-or-migrated-to-azure)。



## <a name="next-steps"></a>后续步骤
故障转移虚拟机并且本地数据中心可用后，应该在本地数据中心[**重新保护**](site-recovery-how-to-reprotect.md) VMware 虚拟机。

使用[**计划的故障转移**](site-recovery-failback-from-azure-to-hyper-v.md)选项可将 Hyper-V 虚拟机从 Azure **故障回复**到本地。

如果已将 Hyper-V 虚拟机故障转移到 VMM 服务器管理的另一个本地数据中心并且主数据中心可用，请使用“反向复制”选项开始复制回到主数据中心。 


