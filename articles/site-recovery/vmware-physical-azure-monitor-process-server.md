---
title: 监视 Azure Site Recovery 进程服务器
description: 本文介绍如何监视用于 VMware VM/物理服务器灾难恢复 Azure Site Recovery 进程服务器
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: 54c161c40c881d7626f79fc9bfe1ec1c160480ae
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79257064"
---
# <a name="monitor-the-process-server"></a>监视进程服务器

本文介绍如何监视[Site Recovery](site-recovery-overview.md)进程服务器。

- 在将本地 VMware Vm 和物理服务器的灾难恢复设置为 Azure 时，将使用进程服务器。
- 默认情况下，进程服务器在配置服务器上运行。 默认情况下，在部署配置服务器时安装。
- （可选）若要缩放和处理更多的复制计算机和更大的复制流量，你可以部署更多的横向扩展进程服务器。

[详细了解](vmware-physical-azure-config-process-server-overview.md)进程服务器的角色和部署。

## <a name="monitoring-overview"></a>监视概述

由于进程服务器具有如此多的角色，尤其是在复制的数据缓存、压缩和传输到 Azure 时，因此必须定期监视进程服务器运行状况，这一点非常重要。

在许多情况下，通常会影响进程服务器的性能。 影响性能的问题将对 VM 运行状况产生级联影响，最终将进程服务器及其复制的计算机推送到严重状态。 情况包括：

- 大量 Vm 使用进程服务器，接近或超过建议的限制。
- 使用进程服务器的 Vm 的变动率较高。
- Vm 和进程服务器之间的网络吞吐量不足以将复制数据上传到进程服务器。
- 进程服务器和 Azure 之间的网络吞吐量不足，无法将进程服务器中的复制数据上传到 Azure。

所有这些问题都可能会影响 Vm 的恢复点目标（RPO）。 

**为什么?** 由于为 VM 生成恢复点需要 VM 上的所有磁盘都有一个共同的点。 如果一个磁盘的变动率较高，复制速度很慢，或者进程服务器不是最佳的，它会影响恢复点的创建效率。

## <a name="monitor-proactively"></a>主动监视

若要避免进程服务器出现问题，请务必执行以下操作：

- 使用[容量和大小调整指南](site-recovery-plan-capacity-vmware.md#capacity-considerations)了解进程服务器的特定要求，并确保根据建议部署并运行进程服务器。
- 监视警报并在出现问题时进行故障排除，使进程服务器能够有效地运行。


## <a name="process-server-alerts"></a>处理服务器警报

进程服务器生成了多个运行状况警报，在下表中进行了总结。

**警报类型** | **详细信息**
--- | ---
![正常][green] | 进程服务器已连接并且运行正常。
![警告][yellow] | 过去15分钟 > 80% 的 CPU 使用率
![警告][yellow] | 过去15分钟 > 80% 的内存使用率
![警告][yellow] | 过去15分钟 < 30% 的缓存文件夹可用空间
![警告][yellow] | Site Recovery 每五分钟监视挂起/传出数据，并估计在30分钟内无法将进程服务器缓存中的数据上传到 Azure。
![警告][yellow] | 过去15分钟内未运行进程服务器服务
![严重][red] | 过去15分钟 > 95% 的 CPU 使用率
![严重][red] | 过去15分钟 > 95% 的内存使用率
![严重][red] | 过去15分钟 < 25% 的缓存文件夹可用空间
![严重][red] | Site Recovery 每五分钟监视挂起/传出数据，并估计在45分钟内无法将进程服务器缓存中的数据上传到 Azure。
![严重][red] | 在15分钟内没有来自进程服务器的检测信号。

![表键](./media/vmware-physical-azure-monitor-process-server/table-key.png)

> [!NOTE]
> 进程服务器的总体运行状况状态基于生成的最差警报。



## <a name="monitor-process-server-health"></a>监视进程服务器运行状况

可以按如下所示监视进程服务器的运行状况状态： 

1. 若要监视复制的计算机及其进程服务器的复制运行状况和状态，请在保管库中 > "**复制的项**"，然后单击要监视的计算机。
2. 在 "**复制运行状况**" 中，可以监视 VM 运行状况状态。 单击状态以向下钻取以了解错误详细信息。

    ![VM 仪表板中的进程服务器运行状况](./media/vmware-physical-azure-monitor-process-server/vm-ps-health.png)

4. 在 "**进程服务器运行状况**" 中，可以监视进程服务器的状态。 向下钻取详细信息。

    ![VM 仪表板中的进程服务器详细信息](./media/vmware-physical-azure-monitor-process-server/ps-summary.png)

5. 还可以使用 VM 页面上的图形表示形式来监视运行状况。
    - 如果存在与之相关的警告，则横向扩展进程服务器将以橙色突出显示，如果有任何严重问题，则为红色。 
    - 如果进程服务器在配置服务器上的默认部署中运行，则会相应地突出显示配置服务器。
    - 若要向下钻取，请单击配置服务器或进程服务器。 请注意任何问题以及任何修正建议。

你还可以在**Site Recovery 基础结构**"下监视保管库中的进程服务器。 在 "**管理 Site Recovery 基础结构**" 中，单击 "**配置服务器**"。 选择与进程服务器关联的配置服务器，并向下钻取到 "进程服务器详细信息"。


## <a name="next-steps"></a>后续步骤

- 如果有任何进程服务器问题，请遵循我们的[故障排除指南](vmware-physical-azure-troubleshoot-process-server.md)
- 如需更多帮助，请在 [Azure Site Recovery 论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr)中提问。 

[green]: ./media/vmware-physical-azure-monitor-process-server/green.png
[yellow]: ./media/vmware-physical-azure-monitor-process-server/yellow.png
[red]: ./media/vmware-physical-azure-monitor-process-server/red.png
