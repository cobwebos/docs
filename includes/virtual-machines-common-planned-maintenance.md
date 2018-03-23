---
title: include 文件
description: include 文件
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 03/09/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 193003cef0aed464596e913c0df86e6123292b9f
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/16/2018
---
Azure 定期执行更新，以提高虚拟机的主机基础结构的可靠性、性能及安全性。 此类更新包括修补宿主环境（例如操作系统、虚拟机监控程序以及主机上部署的各种代理）中的软件组件、升级网络组件以及硬件解除授权等多项内容。 大多数此类更新在执行时不会影响托管的虚拟机。 但是，也会存在更新产生影响的情况：

- 如果维护不需重启，Azure 会在更新主机时使用就地迁移来暂停 VM。

- 如果维护需重启，你会收到一个通知，其中会说明计划维护的时间。 在这些示例中，系统还会向你提供一个时间范围，方便你在适合自己的时间自行启动维护。

本页介绍 Microsoft Azure 如何执行两种类型的维护。 有关计划外事件（中断）的详细信息，请参阅“管理适用于 [Windows] (../articles/virtual-machines/windows/manage-availability.md) 或 [Linux](../articles/virtual-machines/linux/manage-availability.md) 的虚拟机的可用性”。

在虚拟机中运行的应用程序可以通过适用于 [Windows](../articles/virtual-machines/windows/instance-metadata-service.md) 或 [Linux] (../articles/virtual-machines/linux/instance-metadata-service.md) 的 Azure 元数据服务收集即将发布的更新的相关信息。

有关管理计划维护的“操作说明”信息，请参阅 [Linux](../articles/virtual-machines/linux/maintenance-notifications.md) 或 [Windows](../articles/virtual-machines/windows/maintenance-notifications.md) 的“处理计划维护通知”。

## <a name="in-place-vm-migration"></a>就地 VM 迁移

当更新不需要完整的重启时，可以使用就地实时迁移。 在更新过程中，虚拟机会暂停约 30 秒，保留在 RAM 中的内存，而宿主环境则会应用必需的更新和修补程序。 然后，虚拟机会进行恢复，其时钟会自动同步。

对于可用性集中的 VM，一次更新一个更新域。 一个更新域 (UD) 中的所有 VM 都会进行暂停、更新和恢复，然后计划内维护就会转到下一 UD。

这些类型的更新可能会影响某些应用程序。 执行实时事件处理（例如媒体流或转码）或高吞吐量网络方案的应用程序可能无法容忍暂停 30 秒钟。 <!-- sooooo, what should they do? --> 


## <a name="maintenance-requiring-a-reboot"></a>需要重启的维护

需要重启 VM 进行计划内维护时，会提前通知你。 计划内维护有两个阶段：自助式时段和计划维护时段。

自助式时段允许在 VM 上启动维护。 在此时段内，可以通过查询每个 VM 来了解其状态，并查看上次维护请求的结果。

启动自助式维护时，VM 会转到已更新的某个节点，然后重启。 由于 VM 重启，临时磁盘会丢失，而与虚拟网络接口关联的动态 IP 地址会更新。

如果在启动自助式维护的过程中出错，系统会停止操作，不更新 VM，并会将其从计划内维护迭代中删除。 系统稍后会与你联系并提供新计划，让你有进行自助式维护的新机会。 

自助式维护时段过后，就会开始计划维护时段。 在这段时间内，仍可以查询维护时段，但不能再自行启动维护。

## <a name="availability-considerations-during-planned-maintenance"></a>计划内维护期间的可用性注意事项 

如果你决定一直等到计划内维护时段，则为了保持 VM 的最高可用性，需注意一些事项。 

### <a name="paired-regions"></a>配对区域

每个 Azure 区域与同一地理位置中另一个区域配对，共同组成一个区域对。 在计划内维护期间，Azure 只会更新一个区域对中单个区域的 VM。 例如，更新美国中北部的虚拟机时，Azure 不会同时更新美国中南部的任何虚拟机。 但是，北欧等其他区域可以与美国东部同时进行维护。 了解区域对的工作原理有助于更好地跨区域分配 VM。 有关详细信息，请参阅 [Azure 区域对](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)。

### <a name="availability-sets-and-scale-sets"></a>可用性集和规模集

在 Azure VM 上部署工作负荷时，可以在可用性集中创建 VM，向应用程序提供高可用性。 这样可确保在发生故障或维护事件期间，至少有一个虚拟机可用。

在可用性集中，各个 VM 可分布在最多 20 个更新域 (UD) 中。 在计划内维护期间，仅一个更新域会在任意指定时间受影响。 请注意，不一定按顺序来影响更新域。 

虚拟机规模集是一种 Azure 计算资源，支持将一组相同的 VM 作为单个资源进行部署和管理。 规模集自动跨更新域进行部署，此类更新域就像可用性集中的 VM 一样。 使用规模集时，就像使用可用性集一样，在任何给定的时间都只会影响单个更新域。

有关配置虚拟机以实现高可用性的详细信息，请参阅“管理 [Windows](../articles/virtual-machines/windows/manage-availability.md) 或 [Linux](../articles/virtual-machines/linux/manage-availability.md) 虚拟机的可用性”。
