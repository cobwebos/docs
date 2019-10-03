---
title: include 文件
description: include 文件
services: virtual-machines
author: shants123
ms.service: virtual-machines
ms.topic: include
ms.date: 8/22/2019
ms.author: shants
ms.custom: include file
ms.openlocfilehash: b2a7bbef2c421281780c0191fa32381468899bbf
ms.sourcegitcommit: 3f78a6ffee0b83788d554959db7efc5d00130376
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/26/2019
ms.locfileid: "70020317"
---
Azure 定期更新其平台，以提高虚拟机的主机基础结构的可靠性、性能及安全性。 此类更新的目的包括修补托管环境中的软件组件、升级网络组件以及硬件解除授权，等等。 

更新极少会影响到托管的 VM。 如果更新确实会造成影响，Azure 将选择影响最小的方法进行更新：

- 如果更新不需要重新启动，则会在更新主机时暂停 VM，或者将 VM 实时迁移到已更新的主机。

- 如果维护需要重新启动，则你会收到计划内维护通知。 Azure 还会提供一个时间范围，方便你在合适的时间自行启动维护。 自维护时段通常为35天, 除非维护是紧急的。 Azure 正在投资相关技术，减少进行计划内平台维护时必须重新启动 VM 的情况。 

本页介绍 Azure 如何执行两种类型的维护。 有关非计划事件（服务中断）的详细信息，请参阅 [管理 Windows VM 的可用性](../articles/virtual-machines/windows/manage-availability.md)或适用于 [Linux](../articles/virtual-machines/linux/manage-availability.md) 的相应文章。

在 VM 中，可以使用适用于 [Windows](../articles/virtual-machines/windows/scheduled-events.md) 或 [Linux](../articles/virtual-machines/linux/scheduled-events.md) 的计划事件获取有关即将进行维护的通知。

有关管理计划内维护的说明，请参阅[处理 Linux 的计划内维护通知](../articles/virtual-machines/linux/maintenance-notifications.md)或适用于 [Windows](../articles/virtual-machines/windows/maintenance-notifications.md) 的相应文章。

## <a name="maintenance-that-doesnt-require-a-reboot"></a>不需要重新启动的维护

如前所述，大多数平台更新不影响客户的 VM。 如果更新不可避免地造成影响，Azure 会选择对客户 VM 影响最小的更新机制。 

大多数有影响的维护只会导致 VM 暂停 10 秒以下。 在某些情况下，Azure 使用内存预留维护机制。 这些机制最长会将 VM 暂停 30 秒，并在 RAM 中预留内存。 VM 随后会恢复，其时钟会自动同步。 

内存预留维护适用于 90% 以上的 Azure VM。 它不适用于 G、M、N 和 H 系列。 Azure 逐渐趋向于使用实时迁移技术并改进内存保留维护机制，目的是缩短暂停时间。  

无需重新启动的维护操作每次应用到一个容错域。 如果出现任何警告运行状况信号，这些操作将会停止。 

此类更新可能会影响某些应用程序。 将 VM 实时迁移到另一主机时，某些敏感的工作负荷可能会有几分钟出现导致 VM 暂停的性能略微下降情况。 若要准备 VM 维护并减小 Azure 维护期间造成的影响，请尝试使用此类应用程序的适用于 [Windows](../articles/virtual-machines/windows/scheduled-events.md) 或 [Linux](../articles/virtual-machines/linux/scheduled-events.md) 的计划事件。 Azure 还提供对[Azure 专用主机](../articles/virtual-machines/windows/dedicated-hosts.md)和[隔离 vm](../articles/security/fundamentals/isolation-choices.md)上的此类非零影响平台维护的完全控制。 维护控制功能处于预览状态, 你可以通过提交[注册表单](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR6lJf7DwiQxNmz51ksQvxV9UNUM3UllWUjBMTFZQUFhHUDI0VTBPQlJFNS4u)来请求访问权限。 它可让你选择在35天滚动窗口中选择跳过非零影响平台更新, 并将更新作为批应用。

### <a name="live-migration"></a>实时迁移

实时迁移是一个无需重新启动的操作，可为 VM 预留内存。 它会导致暂停或冻结，但持续时间通常不超过 5 秒。 除 G、M、N 和 H 系列外, 所有基础结构即服务 (IaaS) Vm 均适用于实时迁移。 符合条件的 VM 代表了可部署到 Azure 机群的 90% 以上的 IaaS VM。 

出现以下情况时，Azure 平台将启动实时迁移：
- 计划内维护
- 硬件故障
- 分配优化

某些计划内维护方案使用实时迁移，你可以使用计划事件来提前了解实时迁移操作何时开始。

当 Azure 机器学习算法预测即将发生的硬件故障，或者当你想要优化 VM 分配时，也可以使用实时迁移来移动 VM。 有关用于检测降级硬件实例的预测建模的详细信息，请参阅[使用预测性机器学习和实时迁移提高 Azure VM 的复原能力](https://azure.microsoft.com/blog/improving-azure-virtual-machine-resiliency-with-predictive-ml-and-live-migration/?WT.mc_id=thomasmaurer-blog-thmaure)。 实时迁移通知将显示在 Azure 门户上的“监视”和“服务运行状况”日志中以及“计划事件”中（如果使用这些服务）。

## <a name="maintenance-that-requires-a-reboot"></a>需要重新启动的维护

在极少数情况下，VM 需重新启动以进行计划内维护，这种情况下系统会提前通知。 计划内维护有两个阶段：自助服务阶段和计划内维护阶段。

自助服务阶段通常持续四周，你将在 VM 上启动维护。 在自助服务期间，可以查询每个 VM 来了解其状态，以及上次维护请求的结果。

在启动自助式维护时，VM 将重新部署到已经更新的节点。 由于 VM 重新启动，临时磁盘会丢失，而与虚拟网络接口关联的动态 IP 地址会更新。

如果在自助维护期间出错，操作将会停止，VM 不会更新，而你可以使用相应的选项来重试自助维护。 

自助维护阶段结束时，计划内维护阶段随即开始。 在此阶段，仍可以查询维护阶段，但无法自行启动维护。

有关管理需要重新启动的维护的详细信息，请参阅[处理 Linux 的计划内维护通知](../articles/virtual-machines/linux/maintenance-notifications.md)或适用于 [Windows](../articles/virtual-machines/windows/maintenance-notifications.md) 的相应文章。 

### <a name="availability-considerations-during-scheduled-maintenance"></a>计划内维护期间的可用性注意事项 

如果你决定等到计划内维护阶段开始，为了保持 VM 的最高可用性，应考虑到一些要素。 

#### <a name="paired-regions"></a>配对区域

每个 Azure 区域将与同一邻近地理范围内的另一区域配对。 它们共同构成了一个区域对。 在计划内维护阶段，Azure 只会更新一个区域对中单个区域内的 VM。 例如, 在美国中北部更新 VM 时, Azure 不会同时更新美国中南部的任何 VM。 但是，北欧等其他区域可以与美国东部同时进行维护。 了解区域对的工作原理有助于更好地跨区域分配 VM。 有关详细信息，请参阅 [Azure 区域对](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)。

#### <a name="availability-sets-and-scale-sets"></a>可用性集和规模集

在 Azure VM 上部署工作负荷时，可以在可用性集中创建 VM，向应用程序提供高可用性。 使用可用性集可以确保在发生服务中断或需要重新启动的维护事件期间，至少有一个 VM 可用。

在可用性集中，各个 VM 可分布在最多 20 个更新域 (UD) 中。 在计划内维护期间，在任意给定时间只会更新一个 UD。 UD 不一定要按顺序更新。 

虚拟机规模集是一种 Azure 计算资源，可用于将一组相同的 VM 作为单个资源进行部署和管理。 规模集自动跨 UD 进行部署，此类更新域就像可用性集中的 VM 一样。 在计划内维护期间使用规模集时，就像使用可用性集一样，在任意给定时间只会更新一个 UD。

有关设置 VM 以实现高可用性的详细信息，请参阅 [管理 Windows VM 的可用性](../articles/virtual-machines/windows/manage-availability.md)或适用于 [Linux](../articles/virtual-machines/linux/manage-availability.md) 的相应文章。
