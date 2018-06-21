---
title: 使用 Azure Site Recovery 从 Azure 故障回复到 VMware | Microsoft Docs
description: 将虚拟机故障转移到 Azure 之后，可以启动故障回复将虚拟机恢复到本地。 了解故障回复的步骤。
author: nsoneji
ms.service: site-recovery
ms.topic: conceptual
ms.date: 05/20/2018
ms.author: nisoneji
ms.openlocfilehash: 1e8e2384d6d5672e29e6d7bc28f0772cd5b78519
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/20/2018
ms.locfileid: "36287511"
---
# <a name="fail-back-from-azure-to-an-on-premises-site"></a>从 Azure 故障回复到本地站点

本文介绍如何将虚拟机从 Azure 虚拟机故障回复到本地 VMware 环境。 根据[在 Azure Site Recovery 中故障转移](site-recovery-failover.md)教程将 VMware 虚拟机或 Windows/Linux 物理服务器从本地站点故障转移到 Azure 以后，请按本文中的说明进行故障回复。

## <a name="prerequisites"></a>先决条件
- 确保你已阅读有关[不同类型的故障回复](concepts-types-of-failback.md)和相应注意事项的详细信息。

> [!WARNING]
> 如果[已完成迁移](migrate-overview.md#what-do-we-mean-by-migration)、已将虚拟机移至另一资源组或已删除 Azure 虚拟机，则无法进行故障回复。 如果禁用虚拟机的保护，则无法进行故障回复。

> [!WARNING]
> 受保护且已故障转移到 Azure 的 Windows Server 2008 R2 SP1 物理服务器无法故障回复。

> [!NOTE]
> 如果已故障转移 VMware 虚拟机，则无法故障回复到 Hyper-V 主机。


- 在继续下一步之前，请完成重新保护步骤，以便虚拟机处于复制状态并且可以启动故障转移回本地站点的故障转移。 有关详细信息，请参阅[如何在本地重新保护 Azure 中的虚拟机](vmware-azure-reprotect.md)。

- 在执行故障回复之前，请确保 vCenter 处于连接状态。 否则，断开磁盘连接并将其附加回到虚拟机的操作会失败。

- 在故障转移到 Azure 的过程中，本地站点可能无法访问，因此配置服务器可能不可用或关闭。 在重新保护和故障回复期间，本地配置服务器应运行且处于连接正常状态。 

- 故障回复期间，虚拟机必须位于配置服务器数据库中，否则故障回复不会成功。 请确保定期计划服务器备份。 如果发生灾难，需要使用原始 IP 地址还原服务器，让故障回复正常工作。

- 在触发重新保护/故障回复之前，主目标服务器不应当具有任何快照。

## <a name="overview-of-failback"></a>故障回复概述
故障转移到 Azure 以后，可通过执行以下步骤故障回复到本地站点：

1. [重新保护](vmware-azure-reprotect.md)虚拟机，使其开始复制到本地站点中运行的 VMware 虚拟机。 在此过程中，还需要：

    * 设置本地主目标。 对 Windows 虚拟机使用 Windows 主目标，对 Linux 虚拟机使用 [Linux 主目标](vmware-azure-install-linux-master-target.md)。
    * 设置[进程服务器](vmware-azure-set-up-process-server-azure.md)。
    * 启动[重新保护](vmware-azure-reprotect.md)以关闭本地虚拟机，并将 Azure 虚拟机的数据与本地磁盘同步。

2. 将 Azure 中的虚拟机复制到本地站点后，启动从 Azure 到本地站点的故障转移。

3. 对数据进行故障回复以后，对本地虚拟机进行重新保护，使之开始复制到 Azure。

有关快速概述，请观看以下有关如何故障回复到本地站点的视频：
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video5-Failback-from-Azure-to-On-premises/player]


## <a name="steps-to-fail-back"></a>故障回复的步骤

> [!IMPORTANT]
> 启动故障回复之前，请确保已完成虚拟机重新保护。 虚拟机在其运行状况为“正常”时应处于受保护状态。 若要重新保护虚拟机，请参阅[如何重新保护](vmware-azure-reprotect.md)。

1. 在“复制的项”页中选择虚拟机。 右键单击该虚拟机并选择“计划外故障转移”。
2. 在“确认故障转移”中，确认故障转移方向为从 Azure 转移。 然后选择用于故障转移的恢复点（最新恢复点，或最新的应用一致性恢复点）。 应用一致性点会在最新的时间点之后，并将导致丢失部分数据。
3. 故障转移期间，Site Recovery 会关闭 Azure 中的虚拟机。 检查故障回复是否按预期完成后，可以检查 Azure 中的虚拟机是否已关闭。
4. 要从 Azure 删除故障转移的虚拟机，必须进行**提交**。 右键单击受保护的项，并选择“提交”。 某个作业将删除 Azure 中已故障转移的虚拟机。


## <a name="to-what-recovery-point-can-i-fail-back-the-virtual-machines"></a>可以将虚拟机故障回复到哪个恢复点？

在故障回复期间，可以使用两个选项来故障回复虚拟机/恢复计划。

- 如果选择最新处理的时间点，则所有虚拟机都将故障转移到其最新的可用时间点。 如果恢复计划中有一个复制组，则复制组的每个虚拟机都将故障转移到其独立的最新时间点。

  除非虚拟机至少有一个恢复点，否则无法对其进行故障回复。 除非恢复计划的所有虚拟机至少有一个恢复点，否则无法对恢复计划进行故障回复。

  > [!NOTE]
  > 最新恢复点是一个在崩溃时保持一致的恢复点。

- 如果选择应用程序一致恢复点，则单个虚拟机故障回复将恢复到其最新的可用应用程序一致恢复点。 如果恢复计划具有复制组，则每个复制组都将恢复到其公共的可用恢复点。
应用程序一致恢复点在时间上可能会落后，并且可能会有数据丢失。

## <a name="what-happens-to-vmware-tools-post-failback"></a>故障回复后，VMware 工具会发生什么情况？

对于 Windows 虚拟机，Site Recovery 在故障转移过程中禁用 VMware 工具。 在 Windows 虚拟机故障回复期间，会重新启用 VMware 工具。 


## <a name="reprotect-from-on-premises-to-azure"></a>从本地到 Azure 进行重新保护
故障回复完成并且已启动提交后，会删除 Azure 中的已恢复虚拟机。 现在，虚拟机将回到本地站点，但不受保护。 若要再次开始复制到 Azure，请执行以下操作：

1. 在“保管库” > “设置” > “已复制的项”中，选择已故障回复的虚拟机，然后选择“重新保护”。
2. 输入用于将数据发送回 Azure 的进程服务器值。
3. 选择“确定”启动重新保护作业。

> [!NOTE]
> 本地虚拟机启动后，会留出一段时间让代理重新注册到配置服务器（最多 15 分钟）。 在此期间，重新保护会失败并返回一条错误消息，指出未安装代理。 请等待几分钟，并重试重新保护。

## <a name="next-steps"></a>后续步骤

重新保护作业完成后，虚拟机将复制回 Azure，你可以执行[故障转移](site-recovery-failover.md)再次将虚拟机移到 Azure。


