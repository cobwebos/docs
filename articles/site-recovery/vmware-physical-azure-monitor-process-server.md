---
title: 监视 Azure Site Recovery 进程服务器
description: 本文介绍如何监视 Azure Site Recovery 进程服务器。
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: raynew
ms.openlocfilehash: 4ff52e737438210296b8f2201d5e66e1d38b7bc9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66418282"
---
# <a name="monitor-the-process-server"></a>监视进程服务器

本文介绍如何监视 [Site Recovery](site-recovery-overview.md) 进程服务器。

- 将本地 VMware VM 和物理服务器设置为灾难恢复到 Azure 时，将使用进程服务器。
- 默认情况下，进程服务器在配置服务器上运行。 部署配置服务器时，默认会安装进程服务器。
- （可选）若要缩放和处理更多的复制计算机与复制流量，可以部署更多的横向扩展进程服务器。

[详细了解](vmware-physical-azure-config-process-server-overview.md)进程服务器的角色和部署。

## <a name="monitoring-overview"></a>监视概述

由于进程服务器具有大量的角色（尤其是缓存、压缩复制的数据以及将其传输到 Azure 时），因此必须持续监视进程服务器的运行状况。

在一般情况下，有很多的因素会影响进程服务器的性能。 影响性能的问题会对 VM 的运行状况产生连锁影响，最终将进程服务器及其复制计算机置于一种严重状态。 这些因素包括：

- 大量的 VM 使用进程服务器，使其接近或超过建议的限制。
- 使用进程服务器的 VM 存在较高的变动率。
- VM 与进程服务器之间的网络吞吐量不足，无法将复制数据上传到进程服务器。
- 进程服务器与 Azure 之间的网络吞吐量不足，无法将进程服务器中的复制数据上传到 Azure。

所有这些问题都可能会影响 VM 的恢复点目标 (RPO)。 

**为什么？** 因为生成 VM 的恢复点要求该 VM 上的所有磁盘具有一个共同点。 如果某个磁盘的变动率较高、复制速度缓慢或进程服务器不是最佳服务器，则会影响到恢复点的创建效率。

## <a name="monitor-proactively"></a>主动监视

若要避免进程服务器出现问题，必须：

- 遵循[容量和大小指南](site-recovery-plan-capacity-vmware.md#capacity-considerations)了解进程服务器的具体要求，并确保根据建议部署和运行进程服务器。
- 监视警报并排查发生的问题，使进程服务器保持有效运行。


## <a name="process-server-alerts"></a>进程服务器警报

进程服务器会生成一些运行状况警报，下表对此做了汇总。

**警报类型** | **详细信息**
--- | ---
![Healthy][green] | 进程服务器已连接且正常运行。
![警告][yellow] | 过去 15 分钟的 CPU 利用率超过 80%
![警告][yellow] | 过去 15 分钟的内存使用率超过 80%
![警告][yellow] | 过去 15 分钟的缓存文件夹可用空间小于 30%
![警告][yellow] | 进程服务器服务在过去 15 分钟未运行
![严重][red] | 过去 15 分钟的 CPU 利用率超过 95%
![严重][red] | 过去 15 分钟的内存使用率超过 95%
![严重][red] | 过去 15 分钟的缓存文件夹可用空间小于 25%
![严重][red] | 进程服务器有 15 分钟未发出检测信号。

![表键](./media/vmware-physical-azure-monitor-process-server/table-key.png)

> [!NOTE]
> 进程服务器的总体运行状况是根据生成的最不利警报确定的。



## <a name="monitor-process-server-health"></a>监视进程服务器运行状况

可按如下所述监视进程服务器的运行状况： 

1. 若要监视复制的计算机及其进程服务器的复制运行状况和状态，请在保管库中选择“已复制的项”，然后单击要监视的计算机。 
2. 在“复制运行状况”中，可以监视 VM 的运行状况。  单击该状态可以深入查看错误详细信息。

    ![VM 仪表板中的进程服务器运行状况](./media/vmware-physical-azure-monitor-process-server/vm-ps-health.png)

4. 在“进程服务器运行状况”中，可以监视进程服务器的状态。  深入查看详细信息。

    ![VM 仪表板中的进程服务器详细信息](./media/vmware-physical-azure-monitor-process-server/ps-summary.png)

5. 也可以使用 VM 页上的图形表示形式来监视运行状况。
    - 如果横向扩展进程服务器出现相关的警告，该服务器将以橙色突出显示；如果出现任何严重问题，它将以红色突出显示。 
    - 如果进程服务器在配置服务器上的默认部署中运行，则会相应地突出显示配置服务器。
    - 若要深入查看信息，请单击配置服务器或进程服务器。 注意任何问题和任何补救建议。

还可以在“Site Recovery 基础结构”下监视保管库中的进程服务器。  在“管理 Site Recovery 基础结构”中，单击“配置服务器”。   选择与进程服务器关联的配置服务器，然后深入查看进程服务器详细信息。


## <a name="next-steps"></a>后续步骤

- 如果遇到任何进程服务器问题，请遵循我们的[故障排除指南](vmware-physical-azure-troubleshoot-process-server.md)予以解决
- 如需更多帮助，请在 [Azure Site Recovery 论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr)中提问。 

[green]: ./media/vmware-physical-azure-monitor-process-server/green.png
[yellow]: ./media/vmware-physical-azure-monitor-process-server/yellow.png
[red]: ./media/vmware-physical-azure-monitor-process-server/red.png
