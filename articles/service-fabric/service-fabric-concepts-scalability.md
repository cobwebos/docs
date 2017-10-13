---
title: "Service Fabric 服务的可伸缩性 | Microsoft 文档"
description: "介绍如何缩放 Service Fabric 服务"
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: ed324f23-242f-47b7-af1a-e55c839e7d5d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 680b996e370f66a5e22644ae1d1bf41d314bb4de
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="scaling-in-service-fabric"></a>在 Service Fabric 中进行缩放
Azure Service Fabric 通过管理服务、分区以及群集的节点上的副本，让生成可缩放的应用程序更简单。 在同一硬件上运行多个工作负荷不仅可实现最大资源使用率，还可提供在如何选择缩放工作负荷方面的灵活性。 

在 Service Fabric 中进行缩放可通过多种不同方式实现：

1. 通过创建或删除无状态服务实例进行缩放
2. 通过创建或删除新命名服务进行缩放
3. 通过创建或删除新命名应用程序实例进行缩放
4. 通过使用分区服务进行缩放
5. 通过在群集中添加节点和从群集删除节点进行缩放 
6. 通过使用群集资源管理器指标进行缩放

## <a name="scaling-by-creating-or-removing-stateless-service-instances"></a>通过创建或删除无状态服务实例进行缩放
在 Service Fabric 中进行缩放的最简单方式之一是结合无状态服务。 创建无状态服务时，会获得定义 `InstanceCount` 的机会。 `InstanceCount` 定义服务启动时创建的该服务代码的运行副本数量。 例如，假设群集中有 100 个节点。 再假设该服务使用 `InstanceCount` 值 10 进行创建。 运行时期间，代码的这 10 个运行副本可能全部都会变得过度繁忙（或可能不够繁忙）。 缩放该工作负荷的一种方式是更改实例数量。 例如，某些监视或管理代码可将现有实例数量更改为 50 或 5，具体取决于根据负载，工作负荷需要缩小还是扩大。 

C#：

```csharp
StatelessServiceUpdateDescription updateDescription = new StatelessServiceUpdateDescription(); 
updateDescription.InstanceCount = 50;
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/app/service"), updateDescription);
```

Powershell：

```posh
Update-ServiceFabricService -Stateless -ServiceName $serviceName -InstanceCount 50
```
### <a name="using-dynamic-instance-count"></a>使用动态实例计数
专门针对无状态服务，Service Fabric 提供了一种自动的方法来更改实例计数。 这使服务能够通过可用的节点数动态地进行缩放。 选择加入此行为的方法是将实例计数设为 1。 InstanceCount =-1 是一条 Service Fabric 指令，表示“在每个节点上运行此无状态服务”。 如果节点数发生更改，Service Fabric 将自动更改实例计数进行匹配，确保在所有有效的节点上运行该服务。 

C#：

```csharp
StatelessServiceDescription serviceDescription = new StatelessServiceDescription();
//Set other service properties necessary for creation....
serviceDescription.InstanceCount = -1;
await fc.ServiceManager.CreateServiceAsync(serviceDescription);
```

Powershell：

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName -Stateless -PartitionSchemeSingleton -InstanceCount "-1"
```

## <a name="scaling-by-creating-or-removing-new-named-services"></a>通过创建或删除新命名服务进行缩放
命名服务实例是群集中某命名应用程序实例内的服务类型的特定实例（请参阅 [Service Fabric 应用程序生命周期](service-fabric-application-lifecycle.md)）。 

新的命名服务实例可在服务变得更繁忙或不繁忙时进行创建（或删除）。 这使请求可在更多服务实例中进行传播，通常允许减少现有服务上的负载。 创建服务时，Service Fabric 群集资源管理器将以分布式方式放置群集中的服务。 确切的决策受群集中的[指标](service-fabric-cluster-resource-manager-metrics.md)和其他放置规则约束。 服务可以通过多种不同方式创建，但最常见的是通过管理操作（如用户调用 [`New-ServiceFabricService`](https://docs.microsoft.com/en-us/powershell/module/servicefabric/new-servicefabricservice?view=azureservicefabricps)）或通过代码调用 [`CreateServiceAsync`](https://docs.microsoft.com/en-us/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync?view=azure-dotnet)。 `CreateServiceAsync` 甚至可从正在群集中运行的其他服务内进行调用。

动态创建服务可用于各种方案，属于通用模式。 例如，请想一想代表特定工作流的有状态服务。 表示工作的调用将向此服务显示，且此服务将执行该工作流的步骤并记录进度。 

将如何对此特定服务进行缩放？ 服务可以是某种形式的多租户，并一次性接受同一工作流的多个不同实例的调用和启动步骤。 但是，这可能使代码更复杂，因为现在它需要考虑同一工作流的多个不同实例，所有实例处于不同的阶段，来自不同的客户。 此外，同时处理多个工作流不能解决缩放问题。 这是因为此服务在某个时间点将占用过多资源，以适应特定计算机。 许多最初并不是针对此模式生成的服务也会因其代码中的一些固有瓶颈或速度减慢而遇到困难。 这些类型的问题将导致服务跟踪的并发工作流数量增多时，服务无法正常运行。  

一种解决方案是为想要跟踪的工作流的每个不同实例创建一个此服务的实例。这是一种很好的模式，无论服务是无状态还是有状态均能正常运行。 为使此模式正常运行，通常会使用用作“工作负荷管理器服务”的另一服务。 此服务的功能是接收请求，并将这些请求路由到其他服务。 管理器在收到消息时可动态创建工作负荷服务的实例，然后将请求传递到这些服务。 给定工作流服务完成其工作后，管理器服务也可接收回调。 管理器接收这些回调后，可删除工作流服务的该实例，或者如果需要更多调用，可将其保留。 

这种管理器的高级版本甚至可以创建它所管理的服务的池。 池有助于确保在传入新请求时，无需等待服务启动。 管理器可以从池中只选取当前不繁忙的工作流服务，或随机路由。 保持服务池可用性可更快速处理新请求，因为请求不太可能必须等待新服务启动。 创建新服务很快，但也不是免费或瞬时完成。 池有助于最大程度减少请求在维护之前需要等待的时间。 当响应时间是最重要因素时，经常使用此管理器和池模式。 请求排队，并在后台创建服务，然后将其传递也是一种常用的管理器模式，如同基于服务当前挂起的工作量的一些跟踪，创建和删除服务。 

## <a name="scaling-by-creating-or-removing-new-named-application-instances"></a>通过创建或删除新命名应用程序实例进行缩放
创建和删除整个应用程序实例类似于创建和删除服务的模式。 对于此模式，一些管理器服务基于其查看的请求和从群集内其他服务接收的信息，制定决策。 

在一些现有应用程序中，什么情况下应使用创建新命名应用程序实例，而不是使用创建新命名服务实例？ 存在一些情况：

  * 新应用程序实例适用于其代码需要在某些特定标识或安全设置下运行的客户。
    * Service Fabric 允许定义不同的代码包在特定标识下运行。 若要在不同标识下启动相同的代码包，需要在不同应用程序实例中进行激活。 请想一想已部署现有客户的工作负荷的情况。 这些可能在特定标识下运行，以便用户监视并控制其对其他资源（如远程数据库或其他系统）的访问。 在这种情况下，新客户注册时，你可能不希望在相同的上下文（进程空间）中激活其代码。 尽管可以这么做，但这将使服务代码难以在特定标识的上下文中执行。 通常，必须具有多个安全、隔离和标识管理代码。 可以使用不同的命名 Service Fabric 应用程序实例，而不是在相同的应用程序实例和相同的进程空间中使用不同的命名服务实例。 这样可使定义不同的标识上下文更容易。
  * 新的应用程序实例也将作为配置的一种方式
    * 默认情况下，应用程序实例内的特定服务类型的所有命名服务实例将在给定节点的同一进程中运行。 这意味着，虽然可以不同的方式配置每个服务实例，但此操作很复杂。 服务必须具有用于在配置包中查找其配置的特定令牌。 通常这就是服务的名称。 这可正常使用，但它会将配置连接到应用程序实例中的单个命名服务实例的名称中。 这可能令人困惑且难以管理，因为配置通常是具有特定于应用程序实例的值的设计时项目。 创建更多的服务总是意味着更多的应用程序升级，以更改配置包内的信息，或者部署新的配置包，使新服务可查找其特定信息。 通常较简单的方式是创建全新的命名应用程序实例。 然后，可以使用应用程序参数设置服务所需的任何配置。 这样一来，在该命名应用程序实例中创建的所有服务均可继承特定的配置设置。 例如，可为每个客户创建不同的应用程序实例（替代所有设置），而不是为每个客户创建包含设置和自定义（如机密或每个客户的资源限制）的单个配置文件。 
  * 新的应用程序将用作升级边界
    * 在 Service Fabric 中，不同的命名应用程序实例将用作升级边界。 升级一个命名应用程序实例不会影响正在运行的另一命名应用程序实例的代码。 不同的应用程序最后会在相同的节点上运行相同代码的不同版本。 在需要做出缩放决策时，这可以是一个考虑因素，因为可以选择新代码是否应遵循与另一服务相同的升级。 例如，假设调用到达负责通过动态创建和删除服务来缩放特定客户的工作负荷的管理器服务。 然而，在这种情况下，调用针对与新客户关联的工作负荷。 大多数客户希望保持相互独立，不仅是因为之前列出的安全性和配置原因，也因为这可在运行特定版本软件和选择升级时间方面提供更高的灵活性。 此外，还可以创建新的应用程序实例，并在其中创建服务，对任何升级可能涉及的服务量进行进一步分区。 执行应用程序升级时，单独的应用程序实例可提供更高的粒度，也支持 A/B 测试和蓝/绿部署。 
  * 现有的应用程序实例已满
    * 在 Service Fabric 中，[应用程序容量](service-fabric-cluster-resource-manager-application-groups.md)是可用于控制特定应用程序实例可用资源量的一个概念。 例如，你可能决定给定服务需要创建另一实例，以便进行缩放。 但是，此应用程序实例的某些指标容量已超。 如果仍应向此特定客户或工作负荷授予更多资源，则可以增加该应用程序的现有容量或创建新的应用程序。 

## <a name="scaling-at-the-partition-level"></a>在分区级别进行缩放
Service Fabric 支持分区。 分区可将服务拆分成若干逻辑和物理部分，每个部分均可单独运行。 这对有状态服务十分有用，因为没有任何副本集需要一次性处理所有调用并操纵所有状态。 [分区概述](service-fabric-concepts-partitioning.md)提供了有关受支持分区方案类型的信息。 每个分区的副本跨群集的节点传播，分发该服务的负载并确保整个服务或任何分区均没有故障点。 

请考虑使用具有低键 0、高键 99 和 4 个分区的范围分区方案的服务。 在包含三个节点的群集中，该服务可能如此处所示，按四个副本共享每个节点上的资源的方式进行布局：

<center>
![三节点式分区布局](./media/service-fabric-concepts-scalability/layout-three-nodes.png)
</center>

如果增加节点数目，Service Fabric 将移动其中的一些现有副本。 例如，假设节点数增加到 4，且已重新分发副本。 现在，服务在每个节点上有 3 个正在运行的副本，每个副本均属于不同的分区。 这可以实现更高的资源利用率，因为新节点不冷。 通常情况下，这还可提高性能，因为每项服务均有更多可用资源。

<center>
![四节点式分区布局](./media/service-fabric-concepts-scalability/layout-four-nodes.png)
</center>

## <a name="scaling-by-using-the-service-fabric-cluster-resource-manager-and-metrics"></a>使用 Service Fabric 群集资源管理器和指标进行缩放
[指标](service-fabric-cluster-resource-manager-metrics.md)是服务向 Service Fabric 表示其资源消耗的方式。 使用指标可使群集资源管理器重新组织和优化群集布局。 例如，群集中可能有充足的资源，但可能未向当前执行操作的服务分配这些资源。 通过使用指标，群集资源管理器可重新组织群集，确保服务有权访问可用资源。 


## <a name="scaling-by-adding-and-removing-nodes-from-the-cluster"></a>通过在群集中添加节点和从群集删除节点进行缩放 
使用 Service Fabric 进行缩放的另一种方法是更改群集大小。 更改群集的大小意味着添加或删除群集中的一个或多个节点类型的节点。 例如，想一想群集中的所有节点均为热的情况。 这意味着群集的资源几乎全部耗尽。 在这种情况下，缩放的最佳方法是将更多节点添加到群集中。 新节点联接群集后，Service Fabric 群集资源管理器将服务移到其中，导致现有节点上的总负载减少。 对于实例计数为 -1 的无状态服务，将自动创建更多服务实例。 这将使某些调用从现有节点移到新节点。 

可通过 Service Fabric Azure 资源管理器 PowerShell 模块向群集添加和删除节点。

```posh
Add-AzureRmServiceFabricNode -ResourceGroupName $resourceGroupName -Name $clusterResourceName -NodeType $nodeTypeName  -NumberOfNodesToAdd 5 
Remove-AzureRmServiceFabricNode -ResourceGroupName $resourceGroupName -Name $clusterResourceName -NodeType $nodeTypeName -NumberOfNodesToRemove 5
```

## <a name="putting-it-all-together"></a>汇总
让我们汇总已在此文中讨论的所有观点，并讨论一个示例。 请考虑以下服务：你想要生成一个充当通讯簿的服务，其中保存名称和联系信息。 

首先，需要考虑多个与缩放相关的问题：要设置多少个用户？ 每个用户将存储多少个联系人？ 如果要在第一次构建服务时解决所有问题，这会很难。 我们假设你将处理具有特定分区计数的单个静态服务。 选错分区计数的后果可能会导致以后遇到缩放问题。 同样，即使选对计数，也可能并没有所需的所有信息。 例如，还需要首先决定群集的大小（节点数和其大小）。 通常难以预测服务将在其生存期内使用的资源数。 也可能很难提前知道服务实际看到的流量模式。 例如，可能人们只在早上首先添加和删除其联系人，或者也可能在一天中均匀分布。 基于此，可能需要动态地扩大和缩小服务。 也许可以学习预测何时需要扩大和缩小服务，但无论哪种方式，可能都需要应对服务不断变化的资源消耗情况。 这可能涉及更改群集的大小，以在重新组织现有资源的使用不足以解决问题时提供更多资源。 

但为什么要尝试为所有用户选择出单个分区方案？ 为什么局限于一项服务和一个静态群集？ 实际情况通常更具动态。 

通过生成进行缩放时，请考虑以下动态模式。 可能需要使其适应你的情况：

1. 构建“管理器服务”，而不是尝试预先为每个人选择分区方案。
2. 管理器服务作业的目标是在客户注册服务时，查看客户信息。 然后根据该信息，管理器服务只为该客户创建实际的联系人存储服务的实例。 如果需要特定配置、隔离或升级，还可决定为此客户启动应用程序实例。 

此动态创建模式有多种好处：

  - 无需提前猜测所有用户的正确分区计数，或者自行生成可无限缩放的单一服务。 
  - 不同的用户无需具有相同的分区计数、副本计数、替换约束、指标、默认负载、服务名称、DNS 设置或在服务或应用程序级别指定的任何其他属性。 
  - 你将获得更多的数据段。 每个客户都有自己的服务副本
    - 可以通过不同方式配置每个客户服务，且授予其更多或更少资源，并且可以根据需要基于客户预期的规模，增加或减少包含的分区或副本数。
      - 例如，假设客户支付了“黄金”层 - 他们可以获取更多副本或更高分区计数，以及通过指标和应用程序容量专用于其服务的资源。
      - 或者假设客户提供的信息指示他们需要的联系人数为“Small”- 则会只得到几个分区，或者甚至可能与其他客户一起放置到共享服务池中。
  - 在等待客户出现时，未在运行一大堆服务实例或副本
  - 如果某个客户要离开，则从服务中删除其信息非常简单，就像让管理器删除它创建的服务或应用程序一样。

## <a name="next-steps"></a>后续步骤
有关 Service Fabric 概念的详细信息，请参阅以下文章：

* [Service Fabric 服务的可用性](service-fabric-availability-services.md)
* [Service Fabric 服务分区](service-fabric-concepts-partitioning.md)
