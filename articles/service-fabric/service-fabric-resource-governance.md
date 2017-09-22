---
title: "Azure Service Fabric 容器和服务的资源调控 | Microsoft Docs"
description: "Azure Service Fabric 允许指定在容器内部或外部运行的服务的资源限制。"
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/9/2017
ms.author: subramar
ms.translationtype: HT
ms.sourcegitcommit: 47ba7c7004ecf68f4a112ddf391eb645851ca1fb
ms.openlocfilehash: 62ce17533f8c195b873431089e1f1f47cb0bcbec
ms.contentlocale: zh-cn
ms.lasthandoff: 09/14/2017

---

# <a name="resource-governance"></a>资源调控 

在同一节点或群集上运行多项服务时，其中一项服务可能会消耗更多的资源，从而使其他服务得不到足够的资源。 此问题被称为邻近干扰问题。 Service Fabric 允许开发人员指定每个服务的保留和限制，在保证资源的同时限制其资源使用情况。

>
> 继续阅读此文章之前，应先熟悉 [Service Fabric 应用程序模型](service-fabric-application-model.md)和 [Service Fabric 承载模型](service-fabric-hosting-model.md)。
>

## <a name="resource-governance-metrics"></a>资源调控指标 

每个[服务包](service-fabric-application-model.md)的 Service Fabric 中支持资源调控。 可在代码包之间进一步划分分配到服务包的资源。 指定的资源限制也意味着资源的保留。 Service Fabric 支持使用两个内置[指标](service-fabric-cluster-resource-manager-metrics.md)指定每个服务包的 CPU 和内存：

* CPU（指标名称 `servicefabric:/_CpuCores`）：一个核心是主机上可用的逻辑核心，并且所有节点上的所有核心都具有相同的加权。
* 内存（指标名称 `servicefabric:/_MemoryInMB`）：内存以 MB 表示，并映射到计算机上可用的物理内存。

为了获得这两个指标，[群集资源管理器](service-fabric-cluster-resource-manager-cluster-description.md)跟踪总群集容量、群集中每个节点上的负载以及群集中剩余的资源。 这两个指标等效于任何其他用户或自定义指标，并且可通过它们使用所有现有功能：
* 群集可根据这两个指标进行[均衡](service-fabric-cluster-resource-manager-balancing.md)（默认行为）。
* 群集可根据这两个指标进行[碎片整理](service-fabric-cluster-resource-manager-defragmentation-metrics.md)。
* [描述群集](service-fabric-cluster-resource-manager-cluster-description.md)时，可为这两个指标设置缓冲容量。

这些指标不支持[动态负载报告](service-fabric-cluster-resource-manager-metrics.md)，在创建时即定义了这些指标的负载。

## <a name="resource-governance-mechanism"></a>资源调控机制

Service Fabric 运行时当前不提供资源保留。 打开一个进程或容器时，运行时会将资源限制设置为创建时定义的负载。 此外，可用资源超额时，运行时将拒绝打开新服务包。 为更好地理解该进程的工作原理，下面以具有 2 个 CPU 内核（内存调控机制等效）的节点为例：

1. 首先，在该节点上放置容器，需要 1 个 CPU 内核。 运行时将打开该容器，并将 CPU 限制设置为 1 个内核。 容器就不能使用超过 1 个核心。
2. 然后，在该节点上放置服务副本，相应服务包指定 1 个 CPU 内核的限制。 运行时将打开该代码包，并将其 CPU 限制设置为 1 个内核。

此时，限制值之和等于节点的容量，进程和容器各使用 1 个内核运行，彼此互不干扰。 当容器或副本指定 CPU 限制时，Service Fabric 不再会将其置于该节点上。 但在下面这两种情况下，其他进程可能会争用 CPU，并且本例中的进程和容器可能会遇到干扰问题：

* 混合使用调控和非调控服务和容器：如果用户创建服务时没有指定任何资源调控，运行时将视为它不消耗任何资源，能够将其置于示例中的节点上。 在这种情况下，这一新进程将有效地使用部分 CPU，但会对已在节点上运行的服务造成损失。 此问题的解决方案是，在同一群集中不混合使用调控和非调控服务，或者使用[放置约束](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)，这样它们最终便不会位于同一组节点上。
* 如果 Service Fabric 外的节点上启动了其他进程（例如某些 OS 服务），该进程也会与现有服务争用 CPU。 此问题的解决方案是，考虑 OS 开销以正确设置节点容量，如下一部分中所示。

## <a name="cluster-set-up-for-enabling-resource-governance"></a>设置群集以启用资源调控

节点启动并加入群集时，Service Fabric 将检测可用内存量和可用内核数，并会为这两个资源设置节点容量。 为给操作系统以及其他无法在节点上运行的进程留一些缓冲空间，Service Fabric 只会使用节点上 80% 的可用资源。 此百分比可进行配置，并且可在群集清单中进行更改。 以下示例介绍如何指示 Service Fabric 使用 50% 的可用 CPU 和 70% 的可用内存： 

```xml
<Section Name="PlacementAndLoadBalancing">
    <!-- 0.0 means 0%, and 1.0 means 100%-->
    <Parameter Name="CpuPercentageNodeCapacity" Value="0.5" />
    <Parameter Name="MemoryPercentageNodeCapacity" Value="0.7" />
</Section>
```

在需要完全手动设置节点容量的情况下，也可使用常规机制描述群集中的节点来实现该操作。 以下示例显示如何设置具有 4 个内核和 2 GB 内存的节点： 

```xml
    <NodeType Name="MyNodeType">
      <Capacities>
        <Capacity Name="servicefabric:/_CpuCores" Value="4"/>
        <Capacity Name="servicefabric:/_MemoryInMB" Value="2048"/>
      </Capacities>
    </NodeType>
```

启用自动检测可用资源，并在群集清单中手动定义节点容量后，Service Fabric 将检查节点是否具有足够资源来支持用户定义的容量：
* 如果清单中定义的节点容量小于或等于节点上的可用资源，则 Service Fabric 将使用清单中指定的容量。
* 如果清单中定义的节点容量大于可用资源，Service Fabric 将使用可用资源作为节点容量。

不需要自动检测可用资源时，可通过更改以下设置完全将其禁用：

```xml
<Section Name="PlacementAndLoadBalancing">
    <Parameter Name="AutoDetectAvailableResources" Value="false" />
</Section>
```

为了获得最佳性能，还应在群集清单中打开以下设置： 

```xml
<Section Name="PlacementAndLoadBalancing">
    <Parameter Name="PreventTransientOvercommit" Value="true" /> 
    <Parameter Name="AllowConstraintCheckFixesDuringApplicationUpgrade" Value="true" />
</Section>
```


## <a name="specifying-resource-governance"></a>指定资源调控 

应用程序清单（ServiceManifestImport 部分）中指定了资源调控限制，如以下示例所示：

```xml
<?xml version='1.0' encoding='UTF-8'?>
<ApplicationManifest ApplicationTypeName='TestAppTC1' ApplicationTypeVersion='vTC1' xsi:schemaLocation='http://schemas.microsoft.com/2011/01/fabric ServiceFabricServiceModel.xsd' xmlns='http://schemas.microsoft.com/2011/01/fabric' xmlns:xsi='http://www.w3.org/2001/XMLSchema-instance'>
  <Parameters>
  </Parameters>
  <!--
  ServicePackageA has the number of CPU cores defined, but doesn't have the MemoryInMB defined.
  In this case, Service Fabric will sum the limits on code packages and uses the sum as 
  the overall ServicePackage limit.
  -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName='ServicePackageA' ServiceManifestVersion='v1'/>
    <Policies>
      <ServicePackageResourceGovernancePolicy CpuCores="1"/>
      <ResourceGovernancePolicy CodePackageRef="CodeA1" CpuShares="512" MemoryInMB="1000" />
      <ResourceGovernancePolicy CodePackageRef="CodeA2" CpuShares="256" MemoryInMB="1000" />
    </Policies>
  </ServiceManifestImport>
```
  
在此示例中，服务包 ServicePackageA 在放置它的节点上获得一个核心。 此服务包包含两个代码包（CodeA1 和 CodeA2），并且都指定 `CpuShares` 参数。 CpuShares 512:256 的比例将核心划分到两个代码包中。 因此，在此示例中，CodeA1 获得核心的三分之二，CodeA2 获得核心的三分之一（和相同的软保证保留）。 没有为代码包指定 CpuShares 的情况下，Service Fabric 会在其中平均分配内核。

内存限制是绝对的，所以这两个代码包都限制为 1024 MB 内存（和相同的软保证保留）。 代码包（容器或进程）无法分配超出此限制的内存，尝试执行此操作会引发内存不足异常。 若要强制执行资源限制，服务包中的所有代码包均应指定内存限制。

## <a name="other-resources-for-containers"></a>容器的其他资源
除 CPU 和内存外，还可为容器指定其他资源限制。 这些限制在代码包级别指定，在启动容器时应用。 这些资源与 CPU 和内存不同，群集资源管理器不会注意它们，也不会针对它们进行任何容量检查或负载均衡。 

* MemorySwapInMB - 容器可使用的交换内存量。
* MemoryReservationInMB - 内存调控软限制，仅当在节点上检测到内存争用时才强制执行此限制。
* CpuPercent - 容器可使用的 CPU 百分比。 如果为服务包指定了 CPU 限制，将有效忽略此参数。
* MaximumIOps - 容器可使用的最大 IOPS（读取和写入）。
* MaximumIOBytesps - 容器可使用（读取和写入）的最大 IO（字节/秒）。
* BlockIOWeight - 相对于其他容器的块 IO 权重。

这些资源可与 CPU 和内存组合。 以下示例显示如何为容器指定其他资源：

```xml
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ResourceGovernancePolicy CodePackageRef="FrontendService.Code" CpuPercent="5"
            MemorySwapInMB="4084" MemoryReservationInMB="1024" MaximumIOPS="20" />
        </Policies>
    </ServiceManifestImport>
```

## <a name="next-steps"></a>后续步骤
* 若要了解有关群集资源管理器的详细信息，请参阅[此文](service-fabric-cluster-resource-manager-introduction.md)。
* 要了解有关应用程序模型、服务包、代码包以及如何将副本映射到它们的详细信息，请阅读[此文](service-fabric-application-model.md)。

