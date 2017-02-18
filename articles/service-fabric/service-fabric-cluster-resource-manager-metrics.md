---
title: "使用指标管理 Azure 微服务负载 | Microsoft Docs"
description: "了解如何在 Service Fabric 中配置和使用指标管理服务资源消耗。"
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: 0d622ea6-a7c7-4bef-886b-06e6b85a97fb
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/05/2017
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: bb27d279396aa7b670187560cebe2ed074576bad
ms.openlocfilehash: c0e5418574920495bf277f4127b97ff5b07b56ef


---
# <a name="managing-resource-consumption-and-load-in-service-fabric-with-metrics"></a>在 Service Fabric 中使用指标管理资源消耗和负载
在 Service Fabric 中，指标是与服务关切的、由群集中的节点提供的资源相关的一般术语。 一般而言，指标是要管理的，以便处理服务性能的任何信息。

像内存、磁盘和 CPU 使用率等信息，都是指标的示例。 这些是物理指标，即对应于节点上需要管理的物理资源的资源。 指标也可以是（且通常是）逻辑指标。 逻辑指标类似于“MyWorkQueueDepth”或“MessagesToProcess”或“TotalRecords”。 逻辑指标是应用程序定义的，且对应于一些物理资源消耗，但应用程序实际上不知道如何测量它。 这是很常见的。 我们看到用户使用的大多数指标是逻辑指标。 一般来说，这是因为如今许多服务都是使用托管代码编写的。 托管代码意味着从主机进程内可能很难测量和报告单个服务对象的物理资源消耗。 测量和报告自己的指标的复杂度也是为什么我们要提供现成的一些默认指标。

## <a name="default-metrics"></a>默认指标
假设你想要开始体验，但并不知道要使用哪些资源，或者哪些资源对你很重要。 此时，你可以实施自己的想法，在不指定任何指标的情况下创建服务。 这样就可以了！ Service Fabric 群集资源管理器为你选择了一些简单的指标。 我们今天使用的是不指定任何值时的默认指标，称为 PrimaryCount、ReplicaCount 和 Count。 下表显示这些指标各有多少负载与每个服务对象关联：

| 度量值 | 无状态实例负载 | 有状态辅助负载 | 有状态主要负载 |
| --- | --- | --- | --- |
| PrimaryCount |0 |0 |1 |
| ReplicaCount |0 |1 |1 |
| 计数 |1 |1 |1 |

好了，使用这些默认指标会得到什么结果？ 结果就是，对于基本工作负荷，你将获得不错的工作分布。 在下面的示例中，让我们看看当创建两个服务时会发生什么。 第一个服务是具有三个分区的有状态服务，目标副本集大小为&3;。 第二个服务是带有一个分区的无状态服务，实例数为&3;：

<center>
![包含默认指标的群集布局][Image1]
</center>

下面是得到的情况：

* 有状态服务的主要副本并未堆积在单个节点上
* 同一分区的副本不在同一节点上
* 主要副本与辅助副本的总数分布在群集中
* 每个节点上的服务对象总数平均分配

很好！

运行情况非常好，直到开始运行大量的实际工作负荷：所选择的分区方案有没有可能导致所有分区完全平均使用？ 指定服务的负载在一段时间内保持不变的几率为何，或只是现在相同？

实际上，可以运行默认指标，但这样做通常意味着群集利用率会低于期望值。 这是因为默认指标报告并非自适应，并假定一切都是等效的。 在最坏的情况下，仅使用默认值也可能导致过度安排的节点引起性能问题。 使用自定义指标和动态负载报告，我们可以处理得比较好，接下来将探讨这一点。 对于任何严重的工作负荷，所有服务永久等效的可能性是很低的。 若要充分利用群集并避免性能问题，则需要开始了解自定义指标。

## <a name="custom-metrics"></a>自定义指标
创建服务时，已为每个命名服务实例配置了指标。

任何指标都有一些描述它的属性：名称、默认负载和权重。

* 指标名称：指标的名称。 从资源管理器的角度来看，指标名称是群集中指标的唯一标识符。
* 默认负载：默认负载根据服务是无状态还是有状态服务以不同的方式表示。
  * 对于无状态服务，每个指标都具有名为“DefaultLoad”的单个属性
  * 对于有状态服务，可以定义：
    * PrimaryDefaultLoad：此服务使用此指标（主要指标）的默认量
    * SecondaryDefaultLoad：此服务使用此指标（辅助指标）的默认量
* 权重：指标权重定义指标对于此服务的重要程度（相对于其他指标）。

如果定义了自定义指标，并且希望同时使用默认指标，则需要将其重新显式添加。 这是因为要清楚地了解默认指标和自定义指标之间的关系。 例如，与主要服务分布相比，也许你更关心内存消耗或 WorkQueueDepth 方式。

### <a name="defining-metrics-for-your-service---an-example"></a>为服务定义指标 - 示例
假设想要配置一个报告名为“MemoryInMb”的指标的服务，并且也想使用默认指标。 同时假设已完成一些测量，并且知道该服务的主副本通常占用 20 个单位的“MemoryInMb”，而辅助副本则占用 5 个单位。 了解到内存是管理这个特定服务的性能的最重要指标，但仍希望主副本是均衡的。 均衡主副本是一个好主意，这可以使一些节点或容错域的损失不会影响到与之相关的大部分主副本。 除了这些调整之外，还想要使用默认指标。

以下是使用该指标配置创建服务时要编写的代码：

代码：

```csharp
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
StatefulServiceLoadMetricDescription memoryMetric = new StatefulServiceLoadMetricDescription();
memoryMetric.Name = "MemoryInMb";
memoryMetric.PrimaryDefaultLoad = 20;
memoryMetric.SecondaryDefaultLoad = 5;
memoryMetric.Weight = ServiceLoadMetricWeight.High;

StatefulServiceLoadMetricDescription primaryCountMetric = new StatefulServiceLoadMetricDescription();
primaryCountMetric.Name = "PrimaryCount";
primaryCountMetric.PrimaryDefaultLoad = 1;
primaryCountMetric.SecondaryDefaultLoad = 0;
primaryCountMetric.Weight = ServiceLoadMetricWeight.Medium;

StatefulServiceLoadMetricDescription replicaCountMetric = new StatefulServiceLoadMetricDescription();
replicaCountMetric.Name = "ReplicaCount";
replicaCountMetric.PrimaryDefaultLoad = 1;
replicaCountMetric.SecondaryDefaultLoad = 1;
replicaCountMetric.Weight = ServiceLoadMetricWeight.Low;

StatefulServiceLoadMetricDescription totalCountMetric = new StatefulServiceLoadMetricDescription();
totalCountMetric.Name = "Count";
totalCountMetric.PrimaryDefaultLoad = 1;
totalCountMetric.SecondaryDefaultLoad = 1;
totalCountMetric.Weight = ServiceLoadMetricWeight.Low;

serviceDescription.Metrics.Add(memoryMetric);
serviceDescription.Metrics.Add(primaryCountMetric);
serviceDescription.Metrics.Add(replicaCountMetric);
serviceDescription.Metrics.Add(totalCountMetric);

await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

Powershell：

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("MemoryInMb,High,20,5”,"PrimaryCount,Medium,1,0”,"ReplicaCount,Low,1,1”,"Count,Low,1,1”)
```

（提醒：如果只想要使用默认指标，则完全不必要处理指标集合，或者在创建服务时执行任何特殊操作。）

现在我们已经介绍并观看了一个示例，接下来将更详细地介绍这些设置，并探讨将获得的行为。

## <a name="load"></a>负载
定义指标的重点是表示一些负载。 “负载”是特定节点上某个服务实例或副本使用的特定指标数。 可在创建服务时配置预期负载，在创建服务后更新，基于每个服务对象进行报告，或执行上述所有操作。

## <a name="default-load"></a>默认负载
默认负载是默认情况下此服务的每个服务对象（实例或副本）使用的负载。 对于较简单的服务，默认负载是永远不会更新的静态定义并用于服务的生存期。 默认负载对于简单容量规划方案而言非常有用，其中一定量的资源专用于不同的工作负载。

群集资源管理器允许有状态服务为其主要负载和辅助负载指定不同的默认负载，而无状态服务只能指定一个值。 对于有状态服务，主要副本和辅助副本的默认负载通常不同，因为副本在每个角色中执行不同类型的工作。 例如，主要副本通常服务于读取和写入（以及大部分计算负担），而辅助副本则不同。 预期结果是主要副本的默认负载高于辅助副本的默认负载，但实际数量应取决于你自己的测量。

## <a name="dynamic-load"></a>动态负载
假设你的服务已经运行了一段时间。 通过一些监视注意到：

1. 给定服务的某些分区或实例比其他分区或实例消耗更多的资源
2. 某些服务的负载随时间而变化。

很多因素都会导致此类的负载波动。 服务或分区可能与特定客户相关联，或可能对应于在一天中发生变化的工作负载。 无论是什么原因，均无法为默认负载使用单个值。 在某些情况下，为默认负载选择任何值都会出错。 这是一个问题，因为不正确的默认负载会导致群集资源管理器为服务分配资源过量或不足。 因此，即使群集资源管理器认为群集已均衡，节点也处于过度使用或未充分使用状态。 默认负载仍然有用，因为它们可提供一些信息，但大多数情况下并不是实际工作负载的全貌。 正因如此，群集资源管理器允许每个服务对象在运行时更新其自身的负载。 这称作动态负载报告。

动态负载报告可让副本或实例在其生存期中调整其分配/报告的指标负载。 空闲且未运行任何工作的服务副本或实例通常会报告其使用少量的给定指标。 而繁忙的副本或实例则报告他们使用较多的指标。

报告每个副本或实例允许群集资源管理器重新组织群集中的各个服务对象，以确保服务获得所需的资源。 繁忙的服务会有效地从其他副本或当前空闲或执行较少工作的实例“回收”资源。

在 Reliable Service 中，动态报告负载的代码如下所示：

代码：

```csharp
this.ServicePartition.ReportLoad(new List<LoadMetric> { new LoadMetric("MemoryInMb", 1234), new LoadMetric("metric1", 42) });
```

服务副本或实例只能针对其在创建时配置要使用的指标报告负载。 服务可报告的指标列表是创建服务时指定的同一组指标。 与服务关联的指标列表也可能动态更新。 如果服务副本或实例尝试针对当前未配置要使用的指标报告负载，Service Fabric 将记录该报告但予以忽略。 如果同一 API 调用中报告的其他指标有效，则接受并使用这些报表。 这样很好，因为借此可进行更好的试验。 代码可以测量和报告其了解的所有指标，并且操作者可以指定和更新该服务的指标配置，而不必更改代码。 例如，管理员或运营团队可以针对特定服务禁用有错误报告的指标、根据行为重新设置指标的权重，或者仅在通过其他机制部署和验证代码之后启用新指标。

## <a name="mixing-default-load-values-and-dynamic-load-reports"></a>混用默认负载值和动态负载报告
如果默认负载不能满足需求，并且建议使用动态报告负载，那么二者可以一起使用吗？ 能！ 事实上，这是建议使用的配置。 当设置默认负载同时使用动态负载报告时，默认负载将充当估计值，直到动态报告显示。 这非常好，因为它为群集资源管理器分配了一些工作。 默认负载允许群集资源管理器在创建服务对象时将它们放置在合理的位置。 如果没有提供默认负载信息，则服务会随机放置。 当负载报告稍后传入时，群集资源管理器必须不断移动服务。

回顾上一个示例，看看当添加一些自定义指标和动态负载报告时会发生什么。 在此示例中，使用“Memory”作为示例指标。 假设一开始使用以下命令创建有状态服务：

Powershell：

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("MemoryInMb,High,21,11”,"PrimaryCount,Medium,1,0”,"ReplicaCount,Low,1,1”,"Count,Low,1,1”)
```

注意，此语法是 ("MetricName, MetricWeight, PrimaryDefaultLoad, SecondaryDefaultLoad")。

让我们看一下可能的群集布局情况：

<center>
![使用默认和自定义指标均衡的群集][Image2]
</center>

有几个问题值得注意：

* 由于副本或实例在报告其自身负载前使用服务的默认负载，我们知道有状态服务的分区 1 内的副本尚未动态报告负载
* 分区内的辅助副本可以有自己的负载
* 总体来说，指标看起来已均衡。 对于内存，最大和最小负载之间的比率为 1.75（最大负载的节点是 N3，最小负载的节点是 N2，且 28/16 = 1.75）。

仍需解释的一些事项：

* 何者确定 1.75 的比率是否合理？ 群集资源管理器如何判断当前已处于较好状态还是需要执行更多工作？
* 何时发生平衡？
* 内存的权重“很高”是什么意思？

## <a name="metric-weights"></a>指标权重
具备跨不同服务跟踪相同指标的能力是很重要的。 该视图允许群集资源管理器跟踪群集中的消耗，均衡节点间的消耗，并确保节点不会超出容量。 但是，相同指标的重要性对于服务而言可能有所差异。 此外，在具有许多指标和大量服务的群集中，对于所有指标而言，可能不存在能完全均衡的解决方案。 群集资源管理器应如何处理这些情况？

指标权重允许群集资源管理器在没有完美解答时，决定如何均衡群集。 指标权重还允许群集资源管理器以不同方式均衡特定服务。 指标可以有四个不同的权重级别：零、低、中和高。 在考虑是否均衡时，权重为“零”的指标没有任何价值，但其负载对于容量仍有所价值。

群集中不同指标权重的真正影响是群集资源管理器会生成不同的解决方案。 指标权重告知群集资源管理器某些指标比其他指标更重要。 当没有完美的解决方案时，群集资源管理器倾向于使用能更好地均衡较高权重指标的解决方案。 如果一个服务认为某指标不重要，则可能会发现其对该指标的使用不均衡。 这会允许其他服务获得均匀分布，这对服务而言是很重要的。

让我们查看一个负载报告示例，以及不同的指标权重如何在群集中造成不同的分配。 在此示例中，我们看到切换指标的相对权重会导致群集资源管理器首选不同的解决方案并创建不同的服务排列。

<center>
![指标权重示例及其对均衡解决方案的影响][Image3]
</center>

在此示例中有四种不同的服务，所有这些服务针对两个不同的指标 A 和 B 报告不同的值。在某种方案中，所有服务定义指标 A 为最重要（权重 = 高）并且指标 B 为不重要（权重 = 低）。 在此情况下，我们看到群集资源管理器会放置服务，使 MetricA 比 MetricB 更均衡（具有较低的标准偏差）。 在第二个方案中，我们反转指标权重。 结果，群集资源管理器可能会交换服务 A 与 B，以产生 MetricB 比 MetricA 更加均衡的分配。

### <a name="global-metric-weights"></a>全局指标权重
如果 ServiceA 将 MetricA 定义为最重要，并且 ServiceB 完全不在意 MetricA，最终使用的实际权重是什么？

实际上，会为每个指标跟踪多个权重。 第一组权重是每个服务为指标定义的权重。 其他权重是全局权重，该权重是报告该指标的所有服务的平均值。 当计算解决方案的分数时，群集资源管理器会使用这两个权重。 这是因为：服务根据其自己的优先级进行均衡和群集作为整体被正确地分配都是很重要的。

如果群集资源管理器不在意全局和局部均衡，会发生什么状况？ 构造全局均衡的解决方案虽然微不足道，但这会导致单个服务资源分配不佳。 在以下示例中，我们关注用于配置有状态服务的默认指标，并了解只考虑全局均衡时会发生什么情况：

<center>
![全局唯一解决方案的影响][Image4]
</center>

在最上面的示例中，我们只探讨了全局均衡，群集整体上的确达到均衡。 所有节点的主副本计数和总副本计数都相同。 不过，如果查看此分配的实际影响，就不是那么理想：丢失任何节点对特定工作负荷带来不成比例的影响，因为这除取其所有的主副本。 例如，如果第一个节点出现故障，圆形服务的三个不同分区的三个主副本将全部丢失。 相比之下，另外两个服务（三角形和六边形）的分区丢失一个副本，这不会导致中断（除了必须恢复已关闭的副本）。

在最下面的示例中，群集资源管理器已根据全局均衡和按服务的均衡来分发副本。 当计算解决方案的分数时，它将大部分权重分配给全局解决方案，将（可配置的）部分权重分配给单个服务。 可根据针对每个服务配置的指标权重平均值计算全局均衡。 根据服务自身的定义指标权重平衡每个服务。 这可确保服务在其自身内根据其自身需要尽可能地均衡。 因此，如果第一个节点同样发生故障，则丢失的主要副本（和辅助副本）分布于所有服务的所有分区之间。 这对每个分区的影响都是相同的。

## <a name="next-steps"></a>后续步骤
* 有关可用于配置服务的其他选项的详细信息，请查看[了解如何配置服务](service-fabric-cluster-resource-manager-configure-services.md)中提供的其他群集资源管理器配置的相关主题
* 定义重整指标是合并（而不是分散）节点上负载的一种方式。 若要了解如何配置重整，请参阅[此文](service-fabric-cluster-resource-manager-defragmentation-metrics.md)
* 若要了解群集资源管理器如何管理和均衡群集中的负载，请查看有关[平衡负载](service-fabric-cluster-resource-manager-balancing.md)的文章
* 从头开始并[获取 Service Fabric 群集资源管理器简介](service-fabric-cluster-resource-manager-introduction.md)
* 移动成本是向群集资源管理器发出信号，表示移动某些服务比移动其他服务会产生更高成本的方式之一。 若要了解有关移动成本的详细信息，请参阅[此文](service-fabric-cluster-resource-manager-movement-cost.md)

[Image1]:./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-cluster-layout-with-default-metrics.png
[Image2]:./media/service-fabric-cluster-resource-manager-metrics/Service-Fabric-Resource-Manager-Dynamic-Load-Reports.png
[Image3]:./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-metric-weights-impact.png
[Image4]:./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-global-vs-local-balancing.png



<!--HONumber=Jan17_HO4-->


