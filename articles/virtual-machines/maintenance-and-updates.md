---
title: 维护和更新
description: 概述在 Azure 中运行的虚拟机的维护和更新。
author: shants123
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 05/22/2020
ms.author: shants
ms.openlocfilehash: 3cf126caaaa0c518574418aca194ebd82cc4d6b9
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2020
ms.locfileid: "91972061"
---
# <a name="maintenance-for-virtual-machines-in-azure"></a>Azure 中虚拟机的维护

Azure 定期更新平台，以提高虚拟机的主机基础结构的可靠性、性能及安全性。 此类更新的目的包括修补托管环境中的软件组件、升级网络组件以及硬件解除授权，等等。 

更新极少会影响到托管的 VM。 如果更新确实会造成影响，Azure 将选择影响最小的方法进行更新：

- 如果更新不需要重新启动，则会在更新主机时暂停 VM，或者将 VM 实时迁移到已更新的主机。 
- 如果维护需要重新启动，则你会收到计划内维护通知。 Azure 还会提供一个时间范围，方便你在适合自己的时间自行启动维护。 除非紧急执行维护，否则自我维护时段通常为 35 天。 Azure 当前正在投资技术，以减少因计划内平台维护而需要重启 VM 的情况。 有关管理计划内维护的说明，请参阅“使用 Azure [CLI](maintenance-notifications-cli.md)、[PowerShell](maintenance-notifications-powershell.md) 或[门户](maintenance-notifications-portal.md)处理计划内维护通知”。

本页介绍 Azure 如何执行上述两种类型的维护。 有关非计划事件（服务中断）的详细信息，请参阅 [管理 Windows VM 的可用性](./manage-availability.md)或适用于 [Linux](./manage-availability.md) 的相应文章。

可以通过使用 [Windows](./windows/scheduled-events.md) 或 [Linux](./linux/scheduled-events.md) 的计划事件，在 VM 中获取有关即将进行的维护的通知。



## <a name="maintenance-that-doesnt-require-a-reboot"></a>不需要重新启动的维护

大多数平台更新不会影响客户 VM。 如果无法避免更新产生影响，Azure 会选择对客户 VM 影响最小的更新机制。 

大多数有影响的维护只会导致 VM 暂停 10 秒以下。 在某些情况下，Azure 使用内存预留维护机制。 这些机制最长会将 VM 暂停 30 秒，并在 RAM 中预留内存。 VM 随后会恢复，其时钟会自动同步。 

内存预留维护适用于 90% 以上的 Azure VM。 它不适用于 G、M、N 和 H 系列。 Azure 逐渐趋向于使用实时迁移技术并改进内存保留维护机制，目的是缩短暂停时间。  

将以一次一个容错域的方式应用无需重启的维护操作。 如果从平台监视工具接收到任何运行状况警告信号，会停止这些操作。 

这些类型的更新可能会影响某些应用程序。 将 VM 实时迁移到另一主机时，某些敏感的工作负荷可能会有几分钟出现导致 VM 暂停的性能略微下降情况。 若要针对 VM 维护做好准备并降低 Azure 维护期间的影响，可尝试为此类应用程序使用适用于 [Windows](./windows/scheduled-events.md) 或 [Linux](./linux/scheduled-events.md) 的计划事件。 

若要更好地控制包括零影响和无需重启的更新在内的所有维护活动，可以使用“维护控制”功能。 必须使用 [Azure 专用主机](./dedicated-hosts.md)或[独立 VM](../security/fundamentals/isolation-choices.md)。 如果使用维护控制，可以选择跳过所有平台更新并在 35 天的推广时段中选择所需的时间来应用更新。 有关详细信息，请参阅[使用“维护控制”和 Azure CLI 来控制更新](maintenance-control.md)。


### <a name="live-migration"></a>实时迁移

实时迁移是一个无需重新启动的操作，可为 VM 预留内存。 它会导致暂停或冻结，但持续时间通常不超过 5 秒。 实时迁移适用于除 G、M、N 和 H 系列外的所有基础结构即服务 (IaaS) VM。 符合条件的 VM 代表了可部署到 Azure 机群的 90% 以上的 IaaS VM。 

出现以下情况时，Azure 平台将启动实时迁移：
- 计划内维护
- 硬件故障
- 分配优化

某些计划内维护方案使用实时迁移，你可以使用计划事件来提前了解实时迁移操作何时开始。

当 Azure 机器学习算法预测即将发生的硬件故障，或者当你想要优化 VM 分配时，也可以使用实时迁移来移动 VM。 有关用于检测降级硬件实例的预测建模的详细信息，请参阅[使用预测性机器学习和实时迁移提高 Azure VM 的复原能力](https://azure.microsoft.com/blog/improving-azure-virtual-machine-resiliency-with-predictive-ml-and-live-migration/?WT.mc_id=thomasmaurer-blog-thmaure)。 实时迁移通知将显示在 Azure 门户上的“监视”和“服务运行状况”日志中以及“计划事件”中（如果使用这些服务）。

## <a name="maintenance-that-requires-a-reboot"></a>需要重新启动的维护

在极少数情况下，VM 需重新启动以进行计划内维护，这种情况下系统会提前通知。 计划内维护有两个阶段：自助服务阶段和计划内维护阶段。

自助服务阶段通常持续四周，你将在 VM 上启动维护。 在自助服务期间，可以查询每个 VM 来了解其状态，以及上次维护请求的结果。

启动自助维护时，VM 会重新部署到已更新的某个节点。 由于 VM 重新启动，临时磁盘会丢失，而与虚拟网络接口关联的动态 IP 地址会更新。

如果在自助维护期间出错，操作将会停止，VM 不会更新，而你可以使用相应的选项来重试自助维护。 

自助维护阶段结束时，计划内维护阶段随即开始。 在此阶段，仍可以查询维护阶段，但不能自行启动维护。

有关管理需要重新启动的维护的详细信息，请参阅“使用 Azure [CLI](maintenance-notifications-cli.md)、[PowerShell](maintenance-notifications-powershell.md) 或[门户](maintenance-notifications-portal.md)处理计划内维护通知”。 

### <a name="availability-considerations-during-scheduled-maintenance"></a>计划内维护期间的可用性注意事项 

如果你决定等到计划内维护阶段开始，为了保持 VM 的最高可用性，应考虑到一些要素。 

#### <a name="paired-regions"></a>配对区域

每个 Azure 区域将与同一邻近地理范围内的另一区域配对。 它们共同构成了一个区域对。 在计划内维护阶段，Azure 只会更新一个区域对中单个区域内的 VM。 例如，更新美国中北部的 VM 时，Azure 不会同时更新美国中南部的任何 VM。 但是，北欧等其他区域可以与美国东部同时进行维护。 了解区域对的工作原理有助于更好地跨区域分配 VM。 有关详细信息，请参阅 [Azure 区域对](../best-practices-availability-paired-regions.md)。

#### <a name="availability-sets-and-scale-sets"></a>可用性集和规模集

在 Azure VM 上部署工作负荷时，可以在可用性集中创建 VM，向应用程序提供高可用性。 使用可用性集，可以确保在需要重启的中断或维护事件期间，至少有一个 VM 可用。

在可用性集中，个别 VM 可分布在最多 20 个更新域中。 在计划性维护期间，任何给定时间都只更新一个更新域。 更新域不一定要按顺序更新。 

虚拟机规模集是一种 Azure 计算资源，支持将一组相同的 VM 作为单个资源进行部署和管理。 规模集自动跨 UD 进行部署，此类更新域就像可用性集中的 VM 一样。 在计划内维护期间使用规模集时，就像使用可用性集一样，在任意给定时间只会更新一个 UD。

有关设置 VM 以实现高可用性的详细信息，请参阅 [管理 Windows VM 的可用性](./manage-availability.md)或适用于 [Linux](./manage-availability.md) 的相应文章。

#### <a name="availability-zones"></a>可用性区域

可用性区域是 Azure 区域中独特的物理位置。 每个区域由一个或多个数据中心组成，这些数据中心配置了独立电源、冷却和网络。 为确保能够进行复原，所有已启用的区域中必须至少有三个单独的区域。 

可用性区域是容错域和更新域的组合。 如果在 Azure 区域的三个区域中创建三个或更多 VM，则 VM 将有效分布在三个容错域和三个更新域中。 Azure 平台会识别更新域上的此分布，以确保不同区域中的 VM 不会同时更新。

每个基础结构更新在单个地区中按区域依次推出。 但区域 1 和区域 2 中的部署可以同时进行。 并非所有部署都会按顺序执行。 但单个部署可以一次只在一个区域中推出，以降低风险。

## <a name="next-steps"></a>后续步骤 

可以使用 [Azure CLI](maintenance-notifications-cli.md)、[Azure PowerShell](maintenance-notifications-powershell.md) 或[门户](maintenance-notifications-portal.md)来管理计划内维护。