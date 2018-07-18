---
title: Service Fabric 群集Resource Manager - 相关性 | Microsoft 文档
description: 概述如何配置 Service Fabric 服务的相关性
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: ''
ms.assetid: 678073e1-d08d-46c4-a811-826e70aba6c4
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 57abea79a620aa83e16ad4cc2fd78a4294f2b278
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2018
ms.locfileid: "34204849"
---
# <a name="configuring-and-using-service-affinity-in-service-fabric"></a>在 Service Fabric 中配置和使用服务相关性
相关性是一个控件，主用于帮助简化将已存在且较大型的单体式应用程序转换到云和微服务领域。 它也可以用作提升服务性能的优化，不过这样做可能会产生副作用。

假设正在将某个较大型应用，或者在设计时未将微服务纳入考虑的应用引入 Service Fabric 或任何分布式环境中。 这种类型的转换很常见。 首先将整个应用提升到环境中，将其打包，并确保它能正常运行。 然后，开始将其分解成不同的、可彼此通信的较小服务。

最终可能会发现应用程序将遇到一些问题。 该问题通常是由下列类别的原因之一导致的：

1. 单体式应用中的某个组件 X 与组件 Y 之间具有未记录的依赖关系，刚刚将这些组件转换成了独立服务。 由于这些服务现在群集中的不同节点上运行，因此这种依赖关系已破坏。
2. 这些组件通过（本地命名管道 | 共享内存 | 磁盘上的文件）进行通信，并且出于性能原因，它们实际需要能够立即写入到本地共享资源。 稍后可能会删除该硬依赖关系。
3. 一切都没问题，但事实上，这两个组件之间的对话频繁且性能敏感。 将它们移到独立的服务时，整体应用程序性能将受到严重影响，或者延迟会增大。 因此，整个应用程序不符合预期。

在这些情况下，我们不想要失去重构工作，也不想回到单体结构。 最后一个条件甚至可能是作为纯优化所需要的。 但是，直到我们可以重新设计组件，使其像服务一样自然运行（或直到我们可以通过其他某种方式达到性能预期）为止，我们将需要某种意义上的定位。

怎么办？ 嗯，可以尝试启用相关性。

## <a name="how-to-configure-affinity"></a>如何配置相关性
若要设置相关性，可以定义两个不同服务之间的相关性关系。 可以将相关性想象成在一个服务上“指向”另一个服务，同时假设“这个服务只有在那个服务正在运行的位置运行”。 有时我们将这种相关性称为父子关系（将子级指向父级）。 相关性可确保将一个服务的副本或实例放置在与另一个服务相同的节点上。

```csharp
ServiceCorrelationDescription affinityDescription = new ServiceCorrelationDescription();
affinityDescription.Scheme = ServiceCorrelationScheme.Affinity;
affinityDescription.ServiceName = new Uri("fabric:/otherApplication/parentService");
serviceDescription.Correlations.Add(affinityDescription);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

> [!NOTE]
> 子服务只能参与到单个相关性关系。 若要将子服务一次关联到两个父服务，有以下几个选项：
> - 颠倒关系（将 parentService1 和 parentService2 指向当前子服务），或
> - 按照约定将其中一个父服务指定为中心，并将所有服务指向该服务。 
>
> 在群集中导致的放置行为应是相同的。
>

## <a name="different-affinity-options"></a>不同的相关性选项
相关性通过多种相互关联的架构之一来表示，有两种不同的模式。 相关性的最常见模式是所谓的 NonAlignedAffinity 模式。 在 NonAlignedAffinity 下，不同服务的副本或实例放在相同节点上。 另一种模式是 AlignedAffinity。 对齐的相关性仅适用于有状态服务。 配置两个有状态服务实现对齐的相关性，可确保这些服务的主副本与其他服务的主副本位于相同的节点上。 它还能确保这些服务的每个辅助副本对位于相同的节点上。 也可以针对有状态服务配置 NonAlignedAffinity（但不太常见）。 使用 NonAlignedAffinity 时，两个有状态服务的不同副本会在相同的节点上运行，但二者的主副本最终会在不同的节点上。

<center>
![相关性模式及其影响][Image1]
</center>

### <a name="best-effort-desired-state"></a>尽力而为的所需状态
相关性关系是以尽力而为的方式获取的。 它不提供在同一可执行进程中运行所提供的归置或可靠性保证。 具有相关性关系的服务是本质上不同的实体，可能失败或者被单独移动。 相关性关系也会中断，但这些换行符是临时的。 例如，容量限制可能代表相关性关系中只有某些服务对象能够适用于给定的节点。 在这种情况下，即使有相关性关系，但由于存在其他限制，也无法强制实施这种关系。 如果可以这样做，冲突会稍后自动更正。

### <a name="chains-vs-stars"></a>链形与星形
目前，群集 Resource Manager 无法为相关性关系链建模。 这意味着，如果有一个服务是某一个相关性关系中的子级，则该服务不能是另一个相关性关系中的父级。 如果想要为这种关系建模，需要有效地将它建模为星形而不是链形。 要从链形转为星形，请改为将最下层子级的父级设置为第一个子级的父级。 可能需要执行此操作多次，具体取决于服务的排列方式。 如果没有自然父级服务，可能需要创建一个用作占位符。 根据需求，可能还需要查看一下[应用程序组](service-fabric-cluster-resource-manager-application-groups.md)。

<center>
![相关性关系上下文中的链形与星形][Image2]
</center>

目前关于相关性关系的另一个要注意事项是它们是双向的。 这意味着相关性规则只强制子级放置在父级的所在之处。 不能确保父级位于子级的所在之处。 还请务必注意，相关性关系并不完美，或者无法立即强制执行，因为不同的服务具有不同的生命周期，会失败并且会单独移动。 例如，假设父级由于故障突然故障转移到另一个节点。 群集资源管理器和故障转移管理器会先处理故障转移，因为保证服务之间同步、一致和可用是优先考虑的。 故障转移完成后，相关性关系立即破裂，但群集资源管理器会认为一切都正常，直到它发现子级未与父级在一起。 这些种类的检查会定期执行。 若要深入了解群集资源管理器如何评估约束，可访问[本文](service-fabric-cluster-resource-manager-management-integration.md#constraint-types)，[此文](service-fabric-cluster-resource-manager-balancing.md)详细介绍如何配置评估这些约束的频率。   


### <a name="partitioning-support"></a>分区支持
对于相关性，要注意的最后一点是，不支持分区父级的相关性关系。 可能最终会支持已分区的父服务，但目前不支持。

## <a name="next-steps"></a>后续步骤
- 有关配置服务的详细信息，请参阅[了解如何配置服务](service-fabric-cluster-resource-manager-configure-services.md)
- 若要将服务限于一小部分计算机或聚合服务负载，请使用[应用程序组](service-fabric-cluster-resource-manager-application-groups.md)

[Image1]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resrouce-manager-affinity-modes.png
[Image2]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resource-manager-chains-vs-stars.png