---
title: include 文件
description: include 文件
services: virtual-machines
author: shants123
ms.service: virtual-machines
ms.topic: include
ms.date: 12/14/2018
ms.author: shants
ms.custom: include file
ms.openlocfilehash: 34723a6ee37e54ea2d81e6d1143672e3ccb30d1e
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/27/2018
ms.locfileid: "53805706"
---
Azure 定期更新平台，以提高虚拟机的主机基础结构的可靠性、性能及安全性。 此类更新包括修补宿主环境中的软件组件、升级网络组件以及硬件解除授权等多项内容。 大多数此类更新不会影响托管的虚拟机。 但是，有些情况下更新确实会产生影响，Azure 会选择影响最小的更新方法：

- 如果可以进行无需重启的更新，则在更新主机或将其实时迁移到已更新的主机时暂停 VM。

- 如果维护需重启，你会收到一个通知，其中会说明计划维护的时间。 Azure 还会提供一个时间范围，方便你在适合自己的时间自行启动维护。 Azure 正在投资技术，以减少必须重启 VM 以进行计划平台维护的情况。 

本页介绍 Azure 如何执行两种类型的维护。 有关非计划事件（故障）的详细信息，请参阅管理适用于 [Windows](../articles/virtual-machines/windows/manage-availability.md) 或 [Linux](../articles/virtual-machines/linux/manage-availability.md) 的虚拟机的可用性。

可以通过使用 [Windows](../articles/virtual-machines/windows/scheduled-events.md) 或 [Linux](../articles/virtual-machines/linux/scheduled-events.md) 的计划事件获取有关即将进行的维护的 VM 内通知。

有关管理计划维护的“操作说明”信息，请参阅 [Linux](../articles/virtual-machines/linux/maintenance-notifications.md) 或 [Windows](../articles/virtual-machines/windows/maintenance-notifications.md) 的“处理计划维护通知”。

## <a name="memory-preserving-maintenance"></a>内存保留维护

大多数无需重启的更新的目标是 VM 暂停少于 10 秒。 在某些情况下，使用内存保留维护机制，暂停 VM 最多 30 秒并保留 RAM 中的内存。 然后，虚拟机会进行恢复，其时钟会自动同步。 Azure 越来越多地使用实时迁移技术并改进内存保留维护机制以减少暂停持续时间。

各容错域将逐一应用这些无需重启的维护操作，如果收到任何警告运行状况信号，则进度停止。 

这些类型的更新可能会影响某些应用程序。 如果将 VM 实时迁移到不同的主机，则某些敏感的工作负荷可能会在导致 VM 暂停的几分钟内出现略微的性能下降。 此类应用程序可以受益于使用 [Windows](../articles/virtual-machines/windows/scheduled-events.md) 或 [Linux](../articles/virtual-machines/linux/scheduled-events.md) 的计划事件来准备 VM 维护，并且在 Azure 维护期间不会产生任何影响。 Azure 还致力于为极敏感应用程序维护控制功能。 


## <a name="maintenance-requiring-a-reboot"></a>需要重启的维护

极少情况下，需要重启 VM 进行计划内维护时，会提前通知。 计划内维护有两个阶段：自助式时段和计划维护时段。

自助式时段允许在 VM 上启动维护。 在此时段内，可以通过查询每个 VM 来了解其状态，并查看上次维护请求的结果。

在启动自助式维护时，VM 将重新部署到已经更新的节点。 由于 VM 重启，临时磁盘会丢失，而与虚拟网络接口关联的动态 IP 地址会更新。

如果在启动自助式维护的过程中出错，则操作会停止，VM 不会更新，并且允许你重试自助式维护。 

自助式维护时段过后，就会开始计划维护时段。 在这段时间内，仍可以查询维护时段，但不能自行启动维护。

有关管理需要重启的维护的信息，请参阅 [Linux](../articles/virtual-machines/linux/maintenance-notifications.md) 或 [Windows](../articles/virtual-machines/windows/maintenance-notifications.md)的“处理计划维护通知”。 

### <a name="availability-considerations-during-scheduled-maintenance"></a>计划内维护期间的可用性注意事项 

如果你决定一直等到计划性维护时段，则为了保持 VM 的最高可用性，需注意一些事项。 

#### <a name="paired-regions"></a>配对区域

每个 Azure 区域与同一地理位置中另一个区域配对，共同组成一个区域对。 在计划性维护阶段内，Azure 只会更新一个区域对中单个区域的 VM。 例如，更新美国中北部的 VM 时，Azure 不会同时更新美国中南部的任何 VM。 但是，北欧等其他区域可以与美国东部同时进行维护。 了解区域对的工作原理有助于更好地跨区域分配 VM。 有关详细信息，请参阅 [Azure 区域对](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)。

#### <a name="availability-sets-and-scale-sets"></a>可用性集和规模集

在 Azure VM 上部署工作负荷时，可以在可用性集中创建 VM，向应用程序提供高可用性。 这可确保在发生故障或重启式维护事件期间，至少有一台 VM 可用。

在可用性集中，各个 VM 可分布在最多 20 个更新域 (UD) 中。 在计划性维护期间，仅一个更新域会在任意指定时间更新。 不一定按顺序来更新更新域。 

虚拟机规模集是一种 Azure 计算资源，支持将一组相同的 VM 作为单个资源进行部署和管理。 规模集自动跨更新域进行部署，此类更新域就像可用性集中的 VM 一样。 使用规模集时，就像使用可用性集一样，在计划性维护期间内的任何给定时间都只会更新单个更新域。

有关配置虚拟机以实现高可用性的详细信息，请参阅管理 [Windows](../articles/virtual-machines/windows/manage-availability.md) 或 [Linux](../articles/virtual-machines/linux/manage-availability.md) VM 的可用性。
