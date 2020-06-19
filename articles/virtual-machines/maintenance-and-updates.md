---
title: 维护和更新
description: 概述 Azure 中运行的虚拟机的维护和更新。
author: shants123
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/22/2020
ms.author: shants
ms.openlocfilehash: 97605b0cdc7ac6368b21e9427f64e4bca7e35d4a
ms.sourcegitcommit: fc0431755effdc4da9a716f908298e34530b1238
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/24/2020
ms.locfileid: "83815938"
---
# <a name="maintenance-for-virtual-machines-in-azure"></a>Azure 中虚拟机的维护

Azure 定期更新平台，以提高虚拟机的主机基础结构的可靠性、性能及安全性。 此类更新的用途包括修补宿主环境中的软件组件、升级网络组件或将硬件解除授权等。 

这些更新对托管 VM 的影响非常小。 当更新确实会产生影响时，Azure 会选择影响最小的更新方法：

- 如果更新操作无需重启计算机，则在更新主机或将 VM 实时迁移到已更新的主机时会暂停 VM。 
- 如果维护操作需要重启计算机，会向你发送关于计划内维护的通知。 Azure 还会提供一个时间范围，方便你在适合自己的时间自行启动维护。 自行维护时段通常为 35 天，除非维护很紧急。 Azure 当前正在投资技术，以减少因计划内平台维护而需要重启 VM 的情况。 有关管理计划内维护的说明，请参阅“如何使用 Azure [CLI](maintenance-notifications-cli.md)、[PowerShell](maintenance-notifications-powershell.md) 或[门户](maintenance-notifications-portal.md)来处理计划内维护通知”。

本页介绍 Azure 如何执行两种类型的维护。 有关非计划内事件（中断）的详细信息，请参阅 [管理 Windows VM 的可用性](./windows/manage-availability.md)或适用于 [Linux](./linux/manage-availability.md) 的相应文章。

可以通过使用 [Windows](./windows/scheduled-events.md) 或 [Linux](./linux/scheduled-events.md) 的计划事件，在 VM 中获取有关即将进行的维护的通知。



## <a name="maintenance-that-doesnt-require-a-reboot"></a>不需要重新启动的维护

大多数平台更新不会影响客户 VM。 如果无法避免更新产生影响，Azure 会选择对客户 VM 影响最小的更新机制。 

在执行大多数非零影响的维护时，会将 VM 暂停不到 10 秒。 在某些情况下，Azure 使用内存保留维护机制。 这些机制将 VM 暂停最多 30 秒，并在 RAM 中保留内存。 VM 恢复后，会自动同步时钟。 

内存保留维护适用于 90% 以上的 Azure VM。 它不适用于 G、M、N 和 H 系列。 Azure 越来越多地使用实时迁移技术并改进内存保留维护机制以减少暂停持续时间。  

将以一次一个容错域的方式应用无需重启的维护操作。 如果从平台监视工具接收到任何运行状况警告信号，会停止这些操作。 

这些类型的更新可能会影响某些应用程序。 将 VM 实时迁移到不同的主机时，某些敏感的工作负荷可能会在导致 VM 暂停的几分钟内出现略微的性能下降。 若要针对 VM 维护做好准备并降低 Azure 维护期间的影响，可尝试为此类应用程序使用适用于 [Windows](./windows/scheduled-events.md) 或 [Linux](./linux/scheduled-events.md) 的计划事件。 

若要更好地控制包括零影响和无需重启的更新在内的所有维护活动，可以使用“维护控制”功能。 必须使用 [Azure 专用主机](./linux/dedicated-hosts.md)或[独立 VM](../security/fundamentals/isolation-choices.md)。 如果使用维护控制，可以选择跳过所有平台更新并在 35 天的推广时段中选择所需的时间来应用更新。 有关详细信息，请参阅[使用“维护控制”和 Azure CLI 来控制更新](maintenance-control.md)。


### <a name="live-migration"></a>实时迁移

实时迁移是不需要重新启动并会为 VM 保留内存的操作。 该操作会导致暂停或冻结，且持续时间通常不超过 5 秒。 实时迁移适用于除 G、M、N 和 H 系列外的所有基础结构即服务 (IaaS) VM。 符合条件的 VM 涵盖了 90% 以上的部署到 Azure 产品和服务的 IaaS VM。 

Azure 平台会在以下情况下启动实时迁移：
- 计划内维护
- 硬件故障
- 分配优化

某些计划内维护方案会使用实时迁移，可以使用“计划事件”提前了解会在何时启动实时迁移操作。

当 Azure 机器学习算法预测即将发生硬件故障时，或需要优化 VM 分配时，也可以使用实时迁移来移动 VM。 有关可用于检测降级的硬件实例的预测性建模的详细信息，请参阅[通过预测性机器学习和实时迁移提升 Azure VM 复原能力](https://azure.microsoft.com/blog/improving-azure-virtual-machine-resiliency-with-predictive-ml-and-live-migration/?WT.mc_id=thomasmaurer-blog-thmaure)。 实时迁移通知会显示在 Azure 门户的“监视”和“服务运行状况”日志中以及“计划事件”中（如果使用了这些服务）。

## <a name="maintenance-that-requires-a-reboot"></a>需要重新启动的维护

极少情况下，需要为计划内维护重启 VM，这种情况会提前发送通知。 计划内维护有两个阶段：自助式阶段和计划性维护阶段。

在自助式阶段（通常需要四周）期间，将开始在 VM 上实施维护。 在自助式阶段内，可以通过查询每个 VM 来了解其状态，并查看上次维护请求的结果。

在启动自助式维护时，VM 将重新部署到已经更新的节点。 由于 VM 重启，临时磁盘会丢失，而与虚拟网络接口关联的动态 IP 地址会更新。

如果在自助式维护期间发生错误，操作会停止，VM 不会更新，你可以选择重试自助式维护。 

自助式阶段结束时，将进入计划性维护阶段。 在此阶段，仍可以查询维护阶段，但不能自行启动维护。

有关管理需要重启的维护的说明，请参阅“如何使用 Azure [CLI](maintenance-notifications-cli.md)、[PowerShell](maintenance-notifications-powershell.md) 或[门户](maintenance-notifications-portal.md)来处理计划内维护通知”。 

### <a name="availability-considerations-during-scheduled-maintenance"></a>计划内维护期间的可用性注意事项 

如果你决定一直等到计划性维护阶段，则为了保持 VM 的最高可用性，需注意一些事项。 

#### <a name="paired-regions"></a>配对区域

每个 Azure 区域都与同一邻近地域内的另一个区域配对。 它们共同构成了一个区域对。 在计划性维护阶段内，Azure 只会更新一个区域对中单个区域的 VM。 例如，更新美国中北部的 VM 时，Azure 不会同时更新美国中南部的任何 VM。 但是，北欧等其他区域可以与美国东部同时进行维护。 了解区域对的工作原理有助于更好地跨区域分配 VM。 有关详细信息，请参阅 [Azure 区域对](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)。

#### <a name="availability-sets-and-scale-sets"></a>可用性集和规模集

在 Azure VM 上部署工作负荷时，可以在可用性集中创建 VM，向应用程序提供高可用性。 使用可用性集，可以确保在需要重启的中断或维护事件期间，至少有一个 VM 可用。

在可用性集中，个别 VM 可分布在最多 20 个更新域中。 在计划性维护期间，仅一个更新域会在任意指定时间更新。 更新域不一定按顺序更新。 

虚拟机规模集是一种 Azure 计算资源，支持将一组相同的 VM 作为单个资源进行部署和管理。 规模集自动跨更新域进行部署，此类更新域就像可用性集中的 VM 一样。 与使用可用性集一样，使用规模集时，在计划性维护期间内的任何给定时间都只会更新一个更新域。

有关设置 VM 以实现高可用性的详细信息，请参阅 [管理 Windows VM 的可用性](./windows/manage-availability.md)或适用于 [Linux](./linux/manage-availability.md) 的相应文章。

#### <a name="availability-zones"></a>可用性区域

可用性区域是 Azure 区域中独特的物理位置。 每个区域由一个或多个数据中心组成，这些数据中心配置了独立电源、冷却和网络。 为确保能够进行复原，所有已启用的区域中必须至少有三个单独的区域。 

可用性区域是容错域和更新域的组合。 如果在 Azure 区域的三个区域中创建三个或更多 VM，则 VM 将有效分布在三个容错域和三个更新域中。 Azure 平台会识别更新域上的此分布，以确保不同区域中的 VM 不会同时更新。

每个基础结构更新在单个地区中按区域依次推出。 但区域 1 和区域 2 中的部署可以同时进行。 并非所有部署都会按顺序执行。 但单个部署可以一次只在一个区域中推出，以降低风险。

## <a name="next-steps"></a>后续步骤 

可以使用 [Azure CLI](maintenance-notifications-cli.md)、[Azure PowerShell](maintenance-notifications-powershell.md) 或[门户](maintenance-notifications-portal.md)来管理计划内维护。 
