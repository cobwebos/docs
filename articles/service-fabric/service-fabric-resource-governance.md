---
title: "Azure Service Fabric 容器和服务的资源调控 | Microsoft Docs"
description: "Azure Service Fabric 允许指定在容器内部或外部运行的服务的资源限制。"
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 7/27/2017
ms.author: subramar
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: e490dfba28b8a270bf0e6022333f148c965bc6dc
ms.contentlocale: zh-cn
ms.lasthandoff: 05/10/2017

---

# <a name="resource-governance"></a>资源调控 

在同一节点或群集上运行多项服务时，其中一项服务可能会消耗更多的资源，从而使其他服务得不到足够的资源。 此问题被称为邻近干扰问题。 Service Fabric 允许开发人员指定每个服务的保留和限制，在保证资源的同时限制其资源使用情况。 

## <a name="resource-governance-metrics"></a>资源调控指标 

每个[服务包](service-fabric-application-model.md)的 Service Fabric 中支持资源调控。 可在代码包之间进一步划分分配到服务包的资源。 指定的资源限制也意味着资源的保留。 Service Fabric 支持使用两个内置[指标](service-fabric-cluster-resource-manager-metrics.md)指定每个服务包的 CPU 和内存：

* CPU（指标名称 `ServiceFabric:/_CpuCores`）：一个核心是主机上可用的逻辑核心，并且所有节点上的所有核心都具有相同的加权。
* 内存（指标名称 `ServiceFabric:/_MemoryInMB`）：内存以 MB 表示，并映射到计算机上可用的物理内存。

只提供软保留保证 - 运行时拒绝打开可用资源超出的新服务包。 但是，如果节点上放置了另一个可执行文件或容器，则可能会违反原始保留保证。

为了获得这两个指标，[群集资源管理器](service-fabric-cluster-resource-manager-cluster-description.md)跟踪总群集容量、群集中每个节点上的负载以及群集中剩余的资源。 这两个指标等效于任何其他用户或自定义指标，并且可通过它们使用所有现有功能：
* 群集可根据这两个指标进行[均衡](service-fabric-cluster-resource-manager-balancing.md)（默认行为）。
* 群集可根据这两个指标进行[碎片整理](service-fabric-cluster-resource-manager-defragmentation-metrics.md)。
* [描述群集](service-fabric-cluster-resource-manager-cluster-description.md)时，可为这两个指标设置缓冲容量。

这些指标不支持[动态负载报告](service-fabric-cluster-resource-manager-metrics.md)，在创建时即定义了这些指标的负载。

## <a name="cluster-set-up-for-enabling-resource-governance"></a>设置群集以启用资源调控

应在群集的每个节点类型中手动定义容量，如下所示：

```xml
    <NodeType Name="MyNodeType">
      <Capacities>
        <Capacity Name="ServiceFabric:/_CpuCores" Value="4"/>
        <Capacity Name="ServiceFabric:/_MemoryInMB" Value="2048"/>
      </Capacities>
    </NodeType>
```
 
仅在用户服务上允许资源调控，而非任何系统服务上。 指定容量时，必须为系统服务留出未分配的内核和内存。 为了获得最佳性能，还应在群集清单中打开以下设置： 

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


## <a name="next-steps"></a>后续步骤
* 若要了解有关群集资源管理器的详细信息，请参阅[此文](service-fabric-cluster-resource-manager-introduction.md)。
* 若要了解有关应用程序模型、服务包、代码包以及如何将副本映射到它们的详细信息，请阅读[此文](service-fabric-application-model.md)。

