---
title: 监视 Azure Site Recovery 进程服务器
description: 本文介绍如何监视 Azure Site Recovery 进程服务器。
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 08/24/2019
ms.author: raynew
ms.openlocfilehash: f3ba08bc1d557d53bca2421b5052f496c740b185
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2019
ms.locfileid: "65969328"
---
# <a name="monitor-the-process-server"></a>监视进程服务器

本文介绍如何监视[Site Recovery](site-recovery-overview.md)进程服务器。

- 设置本地 VMware Vm 和物理服务器到 Azure 的灾难恢复时，将使用的进程服务器。
- 默认情况下在配置服务器上运行的进程服务器。 部署配置服务器时，它是默认情况下安装。
- （可选） 到复制的计算机的较大的规模和句柄数和更大的复制流量，可以部署附加的横向扩展进程服务器。

[了解详细信息](vmware-physical-azure-config-process-server-overview.md)有关角色和部署进程服务器。

## <a name="monitoring-overview"></a>监视概述

因为进程服务器具有很多角色，尤其是在复制的数据缓存、 压缩和传输到 Azure，请务必监视持续的进程服务器运行状况。

有很多情况下，通常会影响进程服务器性能。 影响性能的问题会级联作用 VM 运行状况，最终将其复制的计算机和进程服务器推送到严重状态。 情况包括：

- 大量的虚拟机使用的进程服务器，接近或超过建议的限制。
- 使用进程服务器虚拟机具有高改动率。
- Vm 和进程服务器之间的网络吞吐量并不足以复制数据上传到进程服务器。
- 进程服务器与 Azure 之间的网络吞吐量不足以将从进程服务器到 Azure 的复制数据上传。

所有这些问题可能会影响 Vm 的恢复点目标 (RPO)。 

**为什么？** 因为正在为虚拟机生成恢复点需要所有磁盘的 VM 具有公共的点。 如果一个磁盘具有较高更改率、 复制速度较慢，或进程服务器不的最佳选择，它会影响如何有效地创建恢复点。

## <a name="monitor-proactively"></a>主动监视

若要避免与进程服务器的问题，它十分重要：

- 了解针对进程服务器使用的特定要求[容量和大小调整指南](site-recovery-plan-capacity-vmware.md#capacity-considerations)，请确保进程服务器部署，并根据建议运行。
- 监视警报，并解决问题发生时，若要使有效运行的进程服务器。


## <a name="process-server-alerts"></a>进程服务器警报

进程服务器生成的运行状况警报下, 表中汇总的一个数字。

**警报类型** | **详细信息**
--- | ---
![状态良好][green] | 进程服务器已连接并且正常运行。
![警告][yellow] | 在过去 15 分钟的 CPU 利用率 > 80%
![警告][yellow] | 过去 15 分钟内的内存使用率 > 80%
![警告][yellow] | 在过去 15 分钟的缓存文件夹的可用空间 < 30%
![警告][yellow] | 进程服务器服务未运行最后 15 分钟
![严重][red] | 在过去 15 分钟的 CPU 利用率 > 95%
![严重][red] | 过去 15 分钟内的内存使用率 > 95%
![严重][red] | 缓存文件夹的可用空间 < 25%在过去 15 分钟
![严重][red] | 15 分钟的进程服务器无检测信号。

![表键](./media/vmware-physical-azure-monitor-process-server/table-key.png)

> [!NOTE]
> 进程服务器的总体运行状况状态为基础生成的最差警报。



## <a name="monitor-process-server-health"></a>监视进程服务器运行状况

您可以监视的进程服务器的运行状况状态，如下所示： 

1. 若要监视的复制运行状况和状态的复制的计算机，和的进程服务器，在保管库中的 >**复制的项**，单击你想要监视的计算机。
2. 在中**复制运行状况**，可以监视 VM 运行状况状态。 单击要向下钻取有关错误详细信息的状态。

    ![在 VM 仪表板中的进程服务器运行状况](./media/vmware-physical-azure-monitor-process-server/vm-ps-health.png)

4. 在中**进程服务器运行状况**，可以监视进程服务器的状态。 有关详细信息，向下钻取。

    ![在 VM 仪表板中的进程服务器详细信息](./media/vmware-physical-azure-monitor-process-server/ps-summary.png)

5. 此外可以在 VM 页上使用的图形表示形式监视运行状况。
    - 横向扩展进程服务器将突出显示为橙色，如果有与其关联的警告和红色; 如果它具有任何关键问题。 
    - 如果进程服务器运行在配置服务器上的默认部署中，然后将相应地突出配置服务器。
    - 若要向下钻取，请单击配置服务器或进程服务器上。 请注意任何问题和任何修正建议。

你还可以监视进程服务器下的保管库中**Site Recovery 基础结构**。 在中**管理 Site Recovery 基础结构**，单击**配置服务器**。 选择配置服务器与进程服务器和向下钻关联向下到进程服务器详细信息。


## <a name="next-steps"></a>后续步骤

- 如果有任何处理服务器问题，请按照我们[故障排除指南](vmware-physical-azure-troubleshoot-process-server.md)
- 如需更多帮助，请在 [Azure Site Recovery 论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr)中提问。 

[green]: ./media/vmware-physical-azure-monitor-process-server/green.png
[yellow]: ./media/vmware-physical-azure-monitor-process-server/yellow.png
[red]: ./media/vmware-physical-azure-monitor-process-server/red.png
