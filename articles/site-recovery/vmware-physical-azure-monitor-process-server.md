---
title: 监视 Azure Site Recovery 进程服务器
description: 本文介绍如何监视用于 VMware VM/物理服务器灾难恢复的 Azure Site Recovery 进程服务器
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: eebaa70cee99380ac67b8f6516a5b08ff2832c86
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86134734"
---
# <a name="monitor-the-process-server"></a>监视进程服务器

本文介绍如何监视 [Site Recovery](site-recovery-overview.md) 进程服务器。

- 在将本地 VMware VM 和物理服务器的灾难恢复设置到 Azure 时，会使用进程服务器。
- 默认情况下，进程服务器在配置服务器上运行。 它会在你部署配置服务器时默认安装。
- （可选）若要缩放和处理更多的复制计算机和更高的复制流量，可以部署更多的横向扩展进程服务器。

[详细了解](vmware-physical-azure-config-process-server-overview.md)进程服务器的角色和部署。

## <a name="monitoring-overview"></a>监视概述

由于进程服务器具有如此多的角色，尤其是在复制数据缓存、压缩和传输到 Azure 时，因此持续监视进程服务器运行状况非常重要。

有一些情况通常会影响进程服务器的性能。 影响性能的问题会对 VM 运行状况产生关联影响，最终导致进程服务器及其复制计算机进入严重状态。 这类情况包括：

- 大量 VM 使用进程服务器，接近或超过建议限制。
- 使用进程服务器的 VM 的改动率较高。
- VM 与进程服务器之间的网络吞吐量不足以将复制数据上传到进程服务器。
- 进程服务器与 Azure 之间的网络吞吐量不足以将复制数据从进程服务器上传到 Azure。

所有这些问题都可能会影响 VM 的恢复点目标 (RPO)。 

为什么？ 因为为 VM 生成恢复点需要 VM 上的所有磁盘具有一个共同点。 如果某个磁盘的改动率较高、复制速度较慢或是进程服务器不是最佳状态，则会影响创建恢复点的效率。

## <a name="monitor-proactively"></a>主动监视

若要避免进程服务器出现问题，请务必：

- 使用[容量和大小调整指南](site-recovery-plan-capacity-vmware.md#capacity-considerations)了解进程服务器的特定要求，并确保根据建议部署并运行进程服务器。
- 监视警报并在出现问题时进行故障排除，使进程服务器保持高效运行。


## <a name="process-server-alerts"></a>进程服务器警报

进程服务器会生成一些运行状况警报，在下表中进行了汇总。

警报类型 | **详细信息**
--- | ---
![正常][green] | 进程服务器已连接并且状态正常。
![警告][yellow] | 过去 15 分钟内的 CPU 使用率 > 80%
![警告][yellow] | 过去 15 分钟内的内存使用率 > 80%
![警告][yellow] | 过去 15 分钟内的缓存文件夹可用空间 < 30%
![警告][yellow] | Site Recovery 每五分钟监视挂起/传出数据，估计进程服务器缓存中的数据在 30 分钟内无法上传到 Azure。
![警告][yellow] | 进程服务器服务在过去 15 分钟内未运行
![严重][red] | 过去 15 分钟内的 CPU 使用率 > 95%
![严重][red] | 过去 15 分钟内的内存使用率 > 95%
![严重][red] | 过去 15 分钟内的缓存文件夹可用空间 < 25%
![严重][red] | Site Recovery 每五分钟监视挂起/传出数据，估计进程服务器缓存中的数据在 45 分钟内无法上传到 Azure。
![严重][red] | 在 15 分钟内没有来自进程服务器的检测信号。

![表键](./media/vmware-physical-azure-monitor-process-server/table-key.png)

> [!NOTE]
> 进程服务器的总体运行状况基于生成的最差警报。



## <a name="monitor-process-server-health"></a>监视进程服务器运行状况

可以按如下所示监视进程服务器的运行状况： 

1. 若要监视复制计算机及其进程服务器的复制运行状况和状态，请在保管库 >“复制的项”中，单击要监视的计算机。
2. 在“复制运行状况”中，可以监视 VM 运行状况。 单击状态以向下钻取错误详细信息。

    ![VM 仪表板中的进程服务器运行状况](./media/vmware-physical-azure-monitor-process-server/vm-ps-health.png)

4. 在“进程服务器运行状况”中，可以监视进程服务器的状态。 向下钻取详细信息。

    ![VM 仪表板中的进程服务器详细信息](./media/vmware-physical-azure-monitor-process-server/ps-summary.png)

5. 还可以使用 VM 页上的图形表示形式来监视运行状况。
    - 横向扩展进程服务器在有与之关联的警告时以橙色突出显示，在有任何严重问题时为红色。 
    - 如果进程服务器在配置服务器上的默认部署中运行，则会相应地突出显示配置服务器。
    - 若要向下钻取，请单击配置服务器或进程服务器。 请记下任何问题以及任何修正建议。

还可以在“Site Recovery 基础结构”下的保管库中监视进程服务器。 在“管理 Site Recovery 基础结构”中，单击“配置服务器”。 选择与进程服务器关联的配置服务器，并向下钻取到进程服务器详细信息。


## <a name="next-steps"></a>后续步骤

- 如果有任何进程服务器问题，请遵循我们的[故障排除指南](vmware-physical-azure-troubleshoot-process-server.md)
- 如需更多帮助，请在[有关 Azure Site Recovery 的 Microsoft Q&A 问题页面](/answers/topics/azure-site-recovery.html)中提问。 

[green]: ./media/vmware-physical-azure-monitor-process-server/green.png
[yellow]: ./media/vmware-physical-azure-monitor-process-server/yellow.png
[red]: ./media/vmware-physical-azure-monitor-process-server/red.png
