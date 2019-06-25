---
title: include 文件
description: include 文件
services: virtual-machines
author: shants123
ms.service: virtual-machines
ms.topic: include
ms.date: 4/30/2019
ms.author: shants
ms.custom: include file
ms.openlocfilehash: c2931fa410cf92755a5df5b7129dcf93de900930
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172930"
---
Azure 定期更新其平台以提高可靠性、 性能和安全的虚拟机的主机基础结构。 这些更新的用途范围修补到升级网络组件或解除硬件授权在托管环境中的软件组件。 

更新很少会影响托管的 Vm。 当更新产生影响时，Azure 会选择最有影响力的方法更新：

- 如果更新不需要重新启动，VM 已暂停时，会在更新主机或 VM 实时迁移到已更新的主机。

- 如果维护需要重新启动，则要通知的计划内维护。 Azure 还提供了在其中您可以自行启动维护，在适用于您的时间的时间范围。 通常，除非维护是紧急，自助维护时段为 30 天。 Azure 投资的技术来减少计划内的平台维护需要重新启动的 Vm 数。 

本页介绍 Azure 如何执行两种类型的维护。 有关计划外事件 （中断） 的详细信息，请参阅 [管理的 Windows Vm 的可用性](../articles/virtual-machines/windows/manage-availability.md)或为相应的文章[Linux](../articles/virtual-machines/linux/manage-availability.md)。

在 VM 内，可以获取有关通过即将发生的维护通知[使用计划事件的 Windows](../articles/virtual-machines/windows/scheduled-events.md)或对于[Linux](../articles/virtual-machines/linux/scheduled-events.md)。

有关管理计划内的维护的说明，请参阅[处理计划内维护通知适用于 Linux](../articles/virtual-machines/linux/maintenance-notifications.md)或为相应的文章[Windows](../articles/virtual-machines/windows/maintenance-notifications.md)。

## <a name="maintenance-that-doesnt-require-a-reboot"></a>不需要重新启动的维护

如前面所述，大多数平台更新不会影响客户 Vm。 时不产生影响更新不可行，Azure 会选择最有影响到客户的 Vm 的更新机制。 

大多数的非零影响维护不超过 10 秒暂停 VM。 在某些情况下，Azure 使用内存保留维护机制。 这些机制暂停 VM 多达 30 秒，保留 RAM 中的内存。 然后恢复 VM，并且其时钟将自动同步。 

内存保留维护适用于超过 90%的 Azure Vm。 它不适用于 G、 M、 N 和 H 系列。 Azure 越来越多地使用实时迁移技术并提高内存保留维护机制，以减少暂停持续时间。  

这些不需要重新启动的维护操作是一次应用一个容错域。 如果收到任何警告运行状况信号，停止它们。 

这些类型的更新可能会影响某些应用程序。 实时迁移到不同的主机 VM 时，某些敏感的工作负荷可能会显示在通向 VM 暂停几分钟的性能稍有下降。 若要准备 VM 维护和降低 Azure 维护产生的影响，请尝试[使用计划事件的 Windows](../articles/virtual-machines/windows/scheduled-events.md)或[Linux](../articles/virtual-machines/linux/scheduled-events.md)此类应用程序。 Azure 致力于为这些敏感的应用程序的维护控制功能。 

### <a name="live-migration"></a>实时迁移

实时迁移是一项操作，不需要重新启动，并为 VM 保留内存。 它会导致暂停或冻结，通常持续时间不超过 5 秒。 除了 G、 M、 N 和 H 系列，所有基础结构即服务 (IaaS) Vm，都可进行实时迁移。 符合条件的虚拟机表示 90%以上的 IaaS vm 的部署到 Azure 的全套产品。 

Azure 平台在以下方案中启动实时迁移：
- 计划内维护
- 硬件故障
- 分配优化

某些计划内维护方案中使用实时迁移，可以使用计划事件提前了解实时迁移操作将启动。

实时迁移也可用来在 Azure 机器学习算法预测即将出现的硬件故障或当你想要优化的 VM 分配时移动虚拟机。 有关检测到的降级硬件实例的预测性建模的详细信息，请参阅[使用预测性机器学习和实时迁移的改进 Azure VM 恢复](https://azure.microsoft.com/blog/improving-azure-virtual-machine-resiliency-with-predictive-ml-and-live-migration/?WT.mc_id=thomasmaurer-blog-thmaure)。 如果您使用这些服务，实时迁移通知显示在 Azure 门户中的监视器和服务运行状况日志以及如下所示的计划事件。

## <a name="maintenance-that-requires-a-reboot"></a>需要重新启动的维护

Vm 需要重新启动计划内维护的情况很少，则会通知您提前。 计划内的维护有两个阶段： 自助式阶段和计划性的维护阶段。

期间*自助服务阶段*，这通常持续的四个星期，在 Vm 上启动维护。 作为自助服务的一部分，您可以查询每个 VM，若要查看其状态和上次维护请求的结果。

在启动自助式维护时，VM 将重新部署到已经更新的节点。 由于 VM 重新启动，临时磁盘将丢失，并且将更新与虚拟网络接口关联的动态 IP 地址。

如果在自助服务维护，则停止操作过程中出现错误不会更新 VM，并可选择重试自助式维护。 

自助服务阶段结束时，*计划性的维护阶段*开始。 在此阶段，您仍可以查询维护阶段，但不能自行启动维护。

有关管理需要重启的维护的详细信息，请参阅[处理计划内维护通知适用于 Linux](../articles/virtual-machines/linux/maintenance-notifications.md)或为相应的文章[Windows](../articles/virtual-machines/windows/maintenance-notifications.md)。 

### <a name="availability-considerations-during-scheduled-maintenance"></a>计划内维护期间的可用性注意事项 

如果您决定要等到在计划性的维护阶段，有几件事，应考虑在保持 Vm 的最高的可用性。 

#### <a name="paired-regions"></a>配对区域

每个 Azure 区域与同一地理区域内的另一个区域配对。 总之，它们使区域对。 在计划性的维护阶段，Azure 会在更新仅在单个区域的区域对 Vm。 例如，同时更新美国中北部中的 VM，Azure 不会更新美国中南部中的任何 VM 在同一时间。 但是，北欧等其他区域可以与美国东部同时进行维护。 了解区域对的工作原理有助于更好地跨区域分配 VM。 有关详细信息，请参阅 [Azure 区域对](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)。

#### <a name="availability-sets-and-scale-sets"></a>可用性集和规模集

在部署 Azure Vm 上的工作负荷时，您可以在创建 Vm，*可用性集*以向你的应用程序提供高可用性。 使用可用性集，您可以确保在发生故障或维护需要重新启动的事件，至少一个 VM 可用。

在可用性集中，各个 VM 可分布在最多 20 个更新域 (UD) 中。 在计划维护期间在任何给定时间更新只有一个 UD。 Ud 不一定按顺序更新。 

虚拟机*规模集*是一种 Azure 计算资源，可用于部署和管理一组相同的 Vm 作为单个资源。 规模集自动部署在 Ud，像是在可用性集中的 Vm。 作为与可用性集时使用规模集，只有一个 UD 更新在任何给定时间计划维护期间。

有关设置 Vm 以实现高可用性的详细信息，请参阅 [管理你的 Windows Vm 的可用性](../articles/virtual-machines/windows/manage-availability.md)或为相应的文章[Linux](../articles/virtual-machines/linux/manage-availability.md)。
