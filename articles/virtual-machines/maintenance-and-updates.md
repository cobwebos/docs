---
title: 维护和更新
description: 概述 Azure 中运行的虚拟机的维护和更新。
author: shants123
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 11/18/2019
ms.author: shants
ms.openlocfilehash: eaf7616b3bd69828829342b4dca9247c009d3475
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79250226"
---
# <a name="maintenance-for-virtual-machines-in-azure"></a>Azure 中虚拟机的维护

Azure 会定期更新其平台，以提高虚拟机的主机基础结构的可靠性、性能和安全性。 此类更新的用途包括修补宿主环境中的软件组件、升级网络组件或将硬件解除授权等。 

更新很少影响托管的 Vm。 当更新确实有效果时，Azure 会选择最少的有影响力方法来进行更新：

- 如果更新不需要重新启动，则会在更新主机时暂停 VM，或者将 VM 实时迁移到已更新的主机。 
- 如果维护需要重新启动，则会通知你计划内维护。 Azure 还提供了一个时间范围，你可以在该时段内自行开始维护。 自维护时段通常是30天，除非维护是紧急的。 Azure 正在投资技术，减少计划平台维护需要重新启动 Vm 的事例数。 有关管理计划内维护的说明，请参阅使用 Azure [CLI](maintenance-notifications-cli.md)、 [PowerShell](maintenance-notifications-powershell.md)或[门户](maintenance-notifications-portal.md)处理计划内维护通知。

本页介绍 Azure 如何执行两种类型的维护。 有关计划外事件（中断）的详细信息，请参阅管理适用于 [Windows 的 vm 的可用性](./windows/manage-availability.md)或适用于[Linux](./linux/manage-availability.md)的相应文章。

在 VM 中，你可以[使用适用于 Windows](./windows/scheduled-events.md)或[Linux](./linux/scheduled-events.md)的 Scheduled Events 获取有关即将发生的维护的通知。



## <a name="maintenance-that-doesnt-require-a-reboot"></a>不需要重新启动的维护

大多数平台更新不会影响客户 Vm。 如果无法更新，Azure 将选择最有影响力客户 Vm 的更新机制。 

大多数非零影响维护将使 VM 暂停不到10秒。 在某些情况下，Azure 使用内存保留维护机制。 这些机制将 VM 暂停最多30秒，并保留 RAM 中的内存。 然后，会恢复 VM，并且其时钟会自动同步。 

内存保留维护适用于超过90% 的 Azure Vm。 它不适用于 G、M、N 和 H 系列。 Azure 越来越多地使用实时迁移技术，并改进了内存保留维护机制，以减少暂停的持续时间。  

不需要重新启动的这些维护操作一次应用一个容错域。 如果它们收到任何警告运行状况信号，则会停止。 

这些类型的更新可能会影响某些应用程序。 将 VM 实时迁移到另一台主机时，某些敏感的工作负荷可能会在几分钟内导致 VM 暂停，这会稍微降低性能。 若要准备 VM 维护并降低 Azure 维护期间的影响，请尝试为此类应用程序[使用适用于 Windows](./windows/scheduled-events.md)或[Linux](./linux/scheduled-events.md)的 Scheduled Events。 

公共预览版中还提供了一项功能、维护控制，可帮助管理不需要重新启动的维护。 必须使用[Azure 专用主机](./linux/dedicated-hosts.md)或[独立 VM](../security/fundamentals/isolation-choices.md)。 维护控制允许在35天滚动窗口中选择跳过平台更新，并在选择的时间应用更新。 有关详细信息，请参阅[控制包含维护控制的更新和 Azure CLI](maintenance-control-cli.md)。


### <a name="live-migration"></a>实时迁移

实时迁移是不需要重新启动并保留 VM 内存的操作。 这会导致暂停或冻结，通常不超过5秒。 除 G、M、N 和 H 系列外，所有基础结构即服务（IaaS） Vm 均适用于实时迁移。 符合条件的 Vm 表示部署到 Azure 汽油的 IaaS Vm 超过90%。 

Azure 平台会在以下情况下启动实时迁移：
- 计划内维护
- 硬件故障
- 分配优化

某些计划内维护方案使用实时迁移，你可以在实时迁移操作启动时，使用 Scheduled Events 提前了解。

当 Azure 机器学习算法预测即将发生的硬件故障时，或者当你想要优化 VM 分配时，还可以使用实时迁移来移动 Vm。 有关检测降级硬件实例的预测建模的详细信息，请参阅[通过预测性机器学习和实时迁移改进 AZURE VM 复原](https://azure.microsoft.com/blog/improving-azure-virtual-machine-resiliency-with-predictive-ml-and-live-migration/?WT.mc_id=thomasmaurer-blog-thmaure)。 如果使用这些服务，实时迁移通知会出现在 "监视" 和 "服务运行状况日志" 中的 "Azure 门户" 和 "Scheduled Events" 中。

## <a name="maintenance-that-requires-a-reboot"></a>需要重新启动的维护

在极少数情况下，需要重新启动 Vm 以进行计划内维护时，会提前通知你。 计划内维护有两个阶段：自助服务阶段和计划的维护阶段。

在*自助服务阶段*（通常持续四周）期间，会在 vm 上开始维护。 作为自助服务的一部分，你可以查询每个 VM 以查看其状态和上次维护请求的结果。

在启动自助式维护时，VM 将重新部署到已经更新的节点。 由于 VM 重新启动，临时磁盘会丢失，并且将更新与虚拟网络接口关联的动态 IP 地址。

如果在自助维护期间出现错误，则该操作将停止，不会更新 VM，你可以选择重试自助服务维护。 

当自助服务阶段结束时，将开始*计划的维护阶段*。 在此阶段，你仍可以查询维护阶段，但无法自行开始维护。

有关管理需要重启的维护的详细信息，请参阅使用 Azure [CLI](maintenance-notifications-cli.md)、 [PowerShell](maintenance-notifications-powershell.md)或[门户](maintenance-notifications-portal.md)**处理计划内维护通知**。 

### <a name="availability-considerations-during-scheduled-maintenance"></a>计划内维护期间的可用性注意事项 

如果你决定一直等到计划的维护阶段，你应考虑使用几个事项来维护 Vm 的最高可用性。 

#### <a name="paired-regions"></a>配对区域

每个 Azure 区域与同一地理位置中的另一个区域配对。 它们共同构成了一个区域对。 在计划的维护阶段，Azure 只更新区域对中单个区域的 Vm。 例如，在美国中北部更新 VM 时，Azure 不会同时更新美国中南部的任何 VM。 但是，北欧等其他区域可以与美国东部同时进行维护。 了解区域对的工作原理有助于更好地跨区域分配 VM。 有关详细信息，请参阅 [Azure 区域对](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)。

#### <a name="availability-sets-and-scale-sets"></a>可用性集和规模集

在 Azure Vm 上部署工作负荷时，可以在*可用性集中*创建 vm，以向应用程序提供高可用性。 使用可用性集，可以确保在需要重新启动的中断或维护事件期间，至少有一个 VM 可用。

在可用性集中，个别 VM 可分布在最多 20 个更新域中。 在计划内维护期间，在任何给定时间只更新一个更新域。 更新域不一定按顺序更新。 

虚拟机*规模集*是一种 Azure 计算资源，可用于将一组相同的 vm 作为单个资源进行部署和管理。 规模集自动部署在 UDs 上，就像可用性集中的 Vm 一样。 与可用性集一样，在使用规模集时，在计划维护期间，在任何给定时间都只更新一个 UD。

有关设置 Vm 以实现高可用性的详细信息，请参阅 [管理](./windows/manage-availability.md)适用于 Windows 的 vm 的可用性或适用于[Linux](./linux/manage-availability.md)的相应文章。

#### <a name="availability-zones"></a>可用性区域

可用性区域是 Azure 区域内的唯一物理位置。 每个区域由一个或多个数据中心组成，这些数据中心配置了独立电源、冷却和网络。 为确保能够进行复原，所有已启用的区域中必须至少有三个单独的区域。 

可用性区域是容错域和更新域的组合。 如果在 Azure 区域中的三个区域之间创建三个或更多 Vm，则 Vm 会有效地分布在三个容错域和三个更新域中。 Azure 平台会识别更新域上的此分布，以确保不同区域中的 VM 不会同时更新。

每个基础结构更新按区域在单个区域中推出。 不过，你可以在区域1中进行部署，并同时在不同的部署中区域2。 部署不是全部序列化。 不过，单个部署一次只推出一个区域来降低风险。

## <a name="next-steps"></a>后续步骤 

您可以使用[Azure CLI](maintenance-notifications-cli.md)、 [Azure PowerShell](maintenance-notifications-powershell.md)或[门户](maintenance-notifications-portal.md)来管理计划内维护。 