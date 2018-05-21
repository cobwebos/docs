---
title: Azure Service Fabric 容器和服务的资源治理 | Microsoft Docs
description: Azure Service Fabric 允许指定在容器内部或外部运行的服务的资源限制。
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: ''
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/9/2017
ms.author: subramar
ms.openlocfilehash: 49c7e2c99cce13880781a67806543b1cde0c12b6
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2018
---
# <a name="resource-governance"></a>资源调控 

在同一节点或群集上运行多个服务时，其中一个服务可能会占用更多资源，导致相应流程中的其他服务缺少资源。 这种问题称为“邻近干扰”问题。 借助 Azure Service Fabric，开发者可以为每个服务指定资源预留和限制，从而保证并限制资源使用。

> 继续阅读本文之前，建议先熟悉 [Service Fabric 应用程序模型](service-fabric-application-model.md)和 [Service Fabric 托管模型](service-fabric-hosting-model.md)。
>

## <a name="resource-governance-metrics"></a>资源调控指标 

根据[服务包](service-fabric-application-model.md)，Service Fabric 支持资源治理。 可以在代码包之间进一步划分分配到服务包的资源。 指定的资源限制也意味着资源预留。 Service Fabric 支持使用两个内置[指标](service-fabric-cluster-resource-manager-metrics.md)，为每个服务包指定 CPU 和内存：

* CPU（指标名称 `servicefabric:/_CpuCores`）：主机计算机上可用的逻辑内核。 所有节点上的全部内核都进行了相同的加权。

* 内存（指标名称 `servicefabric:/_MemoryInMB`）：内存以 MB 为单位，映射到计算机上可用的物理内存。

对于这两个指标，[群集资源管理器](service-fabric-cluster-resource-manager-cluster-description.md)跟踪总群集容量、群集中每个节点上的负载以及群集中剩余的资源。 这两个指标等同于其他任何用户指标或自定义指标。 现有全部功能都可以与它们结合使用：
* 群集可根据这两个指标进行[均衡](service-fabric-cluster-resource-manager-balancing.md)（默认行为）。
* 群集可根据这两个指标进行[碎片整理](service-fabric-cluster-resource-manager-defragmentation-metrics.md)。
* [描述群集](service-fabric-cluster-resource-manager-cluster-description.md)时，可为这两个指标设置缓冲容量。

这些指标不支持[动态负载报告](service-fabric-cluster-resource-manager-metrics.md)，在创建时即定义了这些指标的负载。

## <a name="resource-governance-mechanism"></a>资源治理机制

Service Fabric 运行时当前不提供资源保留。 当进程或容器打开时，运行时就会将资源限制设置为创建时定义的负载。 此外，如果资源超额，运行时还会拒绝打开新服务包。 为了更好地理解此过程的工作原理，请以下面包含两个 CPU 内核的节点为例（等同于内存治理机制）：

1. 首先，在节点上放置一个需要一个 CPU 内核的容器。 运行时打开此容器，并将 CPU 限制设置为一个内核。 此容器无法使用多个内核。

2. 然后，在节点上放置一个服务副本，相应的服务包指定一个 CPU 内核作为资源限制。 运行时打开代码包，并将它的 CPU 限制设置为一个内核。

此时，限制之和等于节点容量。 进程和容器各使用一个内核在运行，互不干扰。 Service Fabric 不再放置其他任何指定 CPU 限制的容器或副本。

不过，在两种情况下，其他进程可能会争用 CPU。 在这种情况下，示例中的进程和容器可能会遇到邻近干扰问题：

* 混用治理和非治理服务和容器：如果用户创建服务时没有指定任何资源治理，运行时将它视为不占用任何资源，能够将它放置在示例中的节点上。 在这种情况下，这一新进程实际上会占用部分 CPU，占用的是已在节点上运行的服务的份额。 此问题有两种解决方案。 在同一群集中不混用治理和非治理服务，或使用[放置约束](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)，阻止这两种类型的服务最终位于同一组节点上。

* 其他进程在 Service Fabric 外的节点上启动（例如，OS 服务）：在这种情况下，Service Fabric 外的进程也会与现有服务争用 CPU。 此问题的解决方案是，考虑 OS 开销以正确设置节点容量，如下一部分中所示。

## <a name="cluster-setup-for-enabling-resource-governance"></a>启用资源治理所需的群集设置

当节点启动并加入群集时，Service Fabric 会先检测可用内存量和可用内核数，再设置这两个资源的节点容量。 

为了给操作系统以及可能在节点上运行的其他进程留出缓冲空间，Service Fabric 只使用节点上 80% 的可用资源。 此百分比可进行配置，并且可在群集清单中进行更改。 

以下示例介绍如何指示 Service Fabric 使用 50% 的可用 CPU 和 70% 的可用内存： 

```xml
<Section Name="PlacementAndLoadBalancing">
    <!-- 0.0 means 0%, and 1.0 means 100%-->
    <Parameter Name="CpuPercentageNodeCapacity" Value="0.5" />
    <Parameter Name="MemoryPercentageNodeCapacity" Value="0.7" />
</Section>
```

如果需要完全手动设置节点容量，可以使用常规机制来描述群集中的节点。 下面的示例展示了如何设置有四个内核和 2GB 内存的节点： 

```xml
    <NodeType Name="MyNodeType">
      <Capacities>
        <Capacity Name="servicefabric:/_CpuCores" Value="4"/>
        <Capacity Name="servicefabric:/_MemoryInMB" Value="2048"/>
      </Capacities>
    </NodeType>
```

如果已启用自动检测可用资源，并在群集清单中手动定义了节点容量，Service Fabric 会检查节点中的资源是否足以支持用户定义的容量：
* 如果清单中定义的节点容量小于或等于节点上的可用资源，Service Fabric 使用清单中指定的容量。

* 如果清单中定义的节点容量大于可用资源，Service Fabric 使用可用资源作为节点容量。

如果不需要，可以禁用自动检测可用资源。 若要禁用此功能，请更改以下设置：

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


## <a name="specify-resource-governance"></a>指定资源治理 

应用程序清单（ServiceManifestImport 部分）中指定了资源调控限制，如以下示例所示：

```xml
<?xml version='1.0' encoding='UTF-8'?>
<ApplicationManifest ApplicationTypeName='TestAppTC1' ApplicationTypeVersion='vTC1' xsi:schemaLocation='http://schemas.microsoft.com/2011/01/fabric ServiceFabricServiceModel.xsd' xmlns='http://schemas.microsoft.com/2011/01/fabric' xmlns:xsi='http://www.w3.org/2001/XMLSchema-instance'>

  <!--
  ServicePackageA has the number of CPU cores defined, but doesn't have the MemoryInMB defined.
  In this case, Service Fabric sums the limits on code packages and uses the sum as 
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
  
在此示例中，服务包 ServicePackageA 在驻留的节点上拥有一个内核的资源。 此服务包有两个代码包（CodeA1 和 CodeA2），并且都指定了 `CpuShares` 参数。 CpuShares 512:256 的比例将核心划分到两个代码包中。 

因此，在此示例中，CodeA1 分得三分之二个内核，CodeA2 分得三分之一个内核（和相同的软保证预留）。 如果没有为代码包指定 CpuShares，Service Fabric 会在这两个代码包之间平分内核。

内存限制是绝对的，所以这两个代码包都限制为 1024 MB 内存（和相同的软保证保留）。 代码包（容器或进程）无法分配到超出此限制的内存。如果尝试这样做，则会抛出内存不足异常。 若要强制执行资源限制，服务包中的所有代码包均应指定内存限制。

### <a name="using-application-parameters"></a>使用应用程序参数

指定资源调控时，可使用[应用程序参数](service-fabric-manage-multiple-environment-app-configuration.md)管理多个应用配置。 下例展示应用程序参数的用法：

```xml
<?xml version='1.0' encoding='UTF-8'?>
<ApplicationManifest ApplicationTypeName='TestAppTC1' ApplicationTypeVersion='vTC1' xsi:schemaLocation='http://schemas.microsoft.com/2011/01/fabric ServiceFabricServiceModel.xsd' xmlns='http://schemas.microsoft.com/2011/01/fabric' xmlns:xsi='http://www.w3.org/2001/XMLSchema-instance'>

  <Parameters>
    <Parameter Name="CpuCores" DefaultValue="4" />
    <Parameter Name="CpuSharesA" DefaultValue="512" />
    <Parameter Name="CpuSharesB" DefaultValue="512" />
    <Parameter Name="MemoryA" DefaultValue="2048" />
    <Parameter Name="MemoryB" DefaultValue="2048" />
  </Parameters>

  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName='ServicePackageA' ServiceManifestVersion='v1'/>
    <Policies>
      <ServicePackageResourceGovernancePolicy CpuCores="[CpuCores]"/>
      <ResourceGovernancePolicy CodePackageRef="CodeA1" CpuShares="[CpuSharesA]" MemoryInMB="[MemoryA]" />
      <ResourceGovernancePolicy CodePackageRef="CodeA2" CpuShares="[CpuSharesB]" MemoryInMB="[MemoryB]" />
    </Policies>
  </ServiceManifestImport>
```

在此示例中，会为生产环境设置默认参数，其中每个服务包具有 4 核和 2 GB 内存。 可使用应用程序参数文件更改默认值。 在此示例中，一个参数文件可以用来本地测试应用程序，其中它获得的资源将少于生产中所得： 

```xml
<!-- ApplicationParameters\Local.xml -->

<Application Name="fabric:/TestApplication1" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="CpuCores" DefaultValue="2" />
    <Parameter Name="CpuSharesA" DefaultValue="512" />
    <Parameter Name="CpuSharesB" DefaultValue="512" />
    <Parameter Name="MemoryA" DefaultValue="1024" />
    <Parameter Name="MemoryB" DefaultValue="1024" />
  </Parameters>
</Application>
```

> [!IMPORTANT] 
> 从 Service Fabric 6.1 版开始，可使用应用程序参数指定资源调控。<br> 
>
> 使用应用程序参数指定资源调控时，Service Fabric 无法降级到 6.1 之前的版本。 


## <a name="other-resources-for-containers"></a>容器的其他资源
除了 CPU 和内存之外，还可以为容器指定其他资源限制。 这些限制是在代码包一级指定，并在容器启动时应用。 这些资源与 CPU 和内存不同，群集资源管理器不会注意到它们，也不会针对它们进行任何容量检查或负载均衡。 

* MemorySwapInMB：容器可使用的交换内存量。
* MemoryReservationInMB：内存治理软限制，仅当在节点上检测到内存争用时，才强制执行此限制。
* CpuPercent：容器可使用的 CPU 百分比。 如果为服务包指定了 CPU 限制，将有效忽略此参数。
* MaximumIOps：容器可使用（读取和写入）的最大 IOPS。
* MaximumIOBytesps：容器可使用（读取和写入）的最大 IO（字节/秒）。
* BlockIOWeight：相对于其他容器的块 IO 权重。

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
* 若要详细了解群集资源管理器，请阅读 [Service Fabric 群集资源管理器简介](service-fabric-cluster-resource-manager-introduction.md)。
* 若要详细了解应用程序模型、服务包、代码包以及如何将副本映射到它们，请阅读 [Service Fabric 中的应用程序建模](service-fabric-application-model.md)。
