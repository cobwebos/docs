<properties
   pageTitle="资源平衡器服务描述"
   description="有关配置供资源平衡器使用的服务描述的概述 | Microsoft Azure"
   services="service-fabric"
   documentationCenter=".net"
   authors="abhic"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.date="09/15/2015"
   wacn.date=""/>

# 服务描述概述

## 放置约束
服务上的[放置约束](/documentation/articles/service-fabric-placement-constraint)是一种机制，特定服务实例将使用该机制来选择所需的节点属性。它们和节点属性一样是键/值对，用于描述属性名称和服务对值的需求。个别语句可以和简单的布尔逻辑分组在一起，以创建必要的约束。请注意，Service Fabric 资源平衡器会解释约束。

服务的放置约束可通过服务或应用程序清单来定义，也可直接在代码中定义。

服务清单提供 `ServiceTypes` 的定义。

``` xml
<ServiceTypes>
    <StatefulServiceType ServiceTypeName="QueueReplica" HasPersistedState="false" >
      <PlacementConstraints>(HasDisk == true  &amp;&amp; SpindleCount &gt;= 4)</PlacementConstraints>
    </StatefulServiceType>
</ServiceTypes>

```

应用程序清单提供 `ServiceTemplates` 或 `DefaultServices` 的定义。

``` xml
<ServiceTemplates>
    <StatefulService ServiceTypeName="QueueReplica">
      <SingletonPartition></SingletonPartition>
      <PlacementConstraints>(HasDisk == true  &amp;&amp; SpindleCount &gt;= 4)</PlacementConstraints>
    </StatefulService>
  </ServiceTemplates>

  <DefaultServices>
    <Service Name="QR">
      <StatefulService MinReplicaSetSize="3" ServiceTypeName="QueueReplica" TargetReplicaSetSize="3">
        <SingletonPartition/>
        <PlacementConstraints>(HasDisk == true  &amp;&amp; SpindleCount &gt;= 4)</PlacementConstraints>
      </StatefulService>
    </Service>
  </DefaultServices>
```

``` cpp
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
serviceDescription.PlacementConstraints = "(HasDisk == true  && SpindleCount >= 4)";
Task t = fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

下图显示在具有节点的群集中，可放置此服务的唯一有效节点为 N5：

![放置约束][Image1]

如果服务针对某个属性指定约束，但该属性的特定节点尚未定义，则不论指定什么值，都不会将该节点视为匹配项。

## 服务相关性
服务相关性是一种特殊形式的放置约束，可描述基于各种原因彼此具有本地依赖关系的不同服务。这意味着，这两个服务必须在相同节点上运行才能生效。虽然这种体系结构在 Service Fabric 应用程序中并不常见，但 Service Fabric 认为它可以作为特定类型的旧版应用程序的过渡体系结构，因此提供了这一功能。

在两个服务之间建立服务相关性可确保这些服务的主要副本始终并存在同一个节点上。

相关性关系由父子式层次结构表示，也就是会有“父项”和“子项”。 在这种关系中，第一个建立的服务为父项，第二个建立的为子服务。这种关系可建模为“硬”约束。

服务相关性目前具有以下限制：

- 它不能跨无状态服务和有状态服务使用。
- 它不能跨具有不同实例计数的无状态服务使用。例如，两个无状态服务在创建时应具有相同的 InstanceCount 属性。
- 它不能跨具有不同数量的副本的有状态可变或持久性服务使用。例如，两个服务必须具有相同的指定的 Target 和 Min ReplicaSetSizes 值。
- 它不能与分区服务结合使用。每个服务都必须有 FABRIC\_PARTITION\_SCHEME\_SINGLETON 分区方案。
- 相关性关系和服务描述的其他属性一样，都会在创建服务时设置，而且无法修改。
- 不允许使用服务链。如果有多个服务必须纳入相关性关系，则必须使用“星型”模型。

``` xml
<ServiceTemplates>
  <StatelessService ServiceTypeName="StatelessCalculatorService" InstanceCount="5">
    <SingletonPartition></SingletonPartition>
      <ServiceCorrelations>
        <ServiceCorrelation Scheme="Affinity" ServiceName="fabric:/otherApplication/parentService"/>
      </ServiceCorrelations>
  </StatelessService>
</ServiceTemplates>
```

以下代码示例显示 DefaultServices 定义的交替使用。

``` xml
<DefaultServices>
  <Service Name="childService">
    <StatelessService InstanceCount="3" ServiceTypeName="calculatorService">
      <SingletonPartition/>
     <ServiceCorrelations>
        <ServiceCorrelation Scheme="Affinity" ServiceName="fabric:/otherApplication/parentService"/>
      </ServiceCorrelations>
    </StatelessService>
  </Service>
</DefaultServices>
```

以下代码示例显示如何在创建包含应用程序之后创建相关性关系。

``` cpp
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
ServiceCorrelationDescription affinityDescription = new ServiceCorrelationDescription();
affinityDescription.Scheme = ServiceCorrelationScheme.Affinity;
affinityDescription.ServiceName = new Uri("fabric:/otherApplication/parentService");
serviceDescription.Correlations.Add(affinityDescription);
Task t = fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

## 资源平衡指标
服务放置约束和节点属性代表硬规则和策略（用于描述哪些服务排列有效），而资源平衡指标通常有助于描述群集内服务的最佳排列方式。服务所定义的指标具有若干属性，用于描述指标在群集内的使用方式。

### 使用默认指标
首先要了解的是，对服务而言，处理资源指标完全是一项可选操作，并且 Service Fabric 资源平衡器会默认使用一些“内置”系统指标来执行基本资源平衡，而不需要服务或群集管理员执行任何操作。Service Fabric 资源平衡器会在群集内跟踪并平衡的默认指标为 PrimaryCount、ReplicaCount 和 Count。PrimaryCount 是节点上主要有状态服务副本的计数。ReplicaCount 是节点上所有有状态副本的计数。Count 是节点上所有服务对象（包括无状态服务和有状态服务）的总计数。一般认为 PrimaryCount 指标是最重要的，该指标具有最高权重，如下一节所述。PrimaryCount 指标后面是 ReplicaCount 指标，然后是 Count 指标。

该图显示三个有状态服务（每个服务具有三个副本）的默认平衡示例。

![默认指标][Image2]

对许多服务而言，默认指标通常已经足够，除非有其他指标或功能的特定需求，否则都应使用默认指标。

### 定义自定义指标
如果对于特定服务，默认指标不够用，或者该服务对特定资源（比如磁盘空间或内存）有已知的需求，则应该使用自定义指标。当服务有一或多个必须取得平衡以防止争用的资源时，自定义指标非常有用。这些指标的使用方式在群集内的副本之间有很大的差异。例如，某个主要副本可能使用 100% 的指标，而另一个可能仅使用 20%。你也可以使用自定义指标来捕获计算机上严格限制的任何资源，比如内存、磁盘空间或连接。有时候，你也可以创建其他自定义指标，以捕获或代表在节点上没有上限的指标，但务必要从服务的角度来代表工作及资源耗用，例如“目前未完成交易”指标。虽然该指标不太可能针对指定节点来限制“容量”，但仍可通过确保将该指标均匀分布于整个群集来获得性能优势。

请注意，如果服务定义了任何自定义指标，该服务就不会使用系统提供的默认指标。默认指标仍然可用，但必须在创建时就明确将它们重新包含在服务的指标列表中。

### 指标名称
当你创建服务时，只要定义指标名称，就可以创建自定义指标。请注意，Service Fabric 资源平衡器会依据指标名称来关联指标。如果指标在节点定义内或跨服务作为容量使用，则指标名称必须完全匹配，Service Fabric 资源平衡器才能将它们关联起来。

### 指标权重
当服务定义多个指标时，你也可以定义这些指标的指标权重。不同的指标权重可指示 Service Fabric 资源平衡器哪些指标对服务的功能来说更加重要。例如，内存中的队列可能会受网络带宽的影响，但是最大的约束可能还是来自节点上实际的内存使用量。因此，虽然该队列可能有多个指标，但代表内存使用量的指标具有最高权重。同样地，持久性数据库可能同时依赖于内存和磁盘使用量。虽然有限的内存会降低处理复杂查询的能力，但磁盘空间耗尽会阻碍后续的存储操作，这可能会造成更严重的问题。因此，持久性存储可能会选择将磁盘空间设为二者中具有较高权重的指标。

请注意，仅当 Service Fabric 资源平衡器无法完全平衡一组指标，而必须寻找让某个指标整体上最终比另一个指标更为失衡的解决方案时，才会在其中使用权重。在此情况下，比起权重较高的指标，会将权重较低的指标判断为平衡优先级较低，因此 Service Fabric 资源平衡器会提供偏向较高加权指标的提案。

此外，如果 Service Fabric 资源平衡器根据名称判断多个服务定义了相同的指标，则会将这些指标视为同一指标。在指标相同但是权重不同的情况下，资源平衡器会计算指定权重的平均值，以确定要在运行时使用的实际权重。因此，当某个服务将权重定义为零，表示它不需要平衡，而另一个服务将相同指标定义为具有高权重时，实际权重会取两者中间的值。

![指标权重][Image3]

此示例说明两个指标权重方案如何导致不同的平衡结果，其中，优先级较高的指标整体上会更妥善地分布。在上面左侧的示例中，B 更平衡；而在右侧的示例中，A 更平衡。由于此示例中没有平衡解决方案可以平衡这两个指标，因此 Service Fabric 资源平衡器会使用指标的权重来判断何者比较重要，也就是在平衡期间要偏向哪个指标。

请注意，如果服务需要跟踪某个指标，但不需要针对这项指标进行平衡，则可将指标权重设为零。例如，假设某个指标在数个节点上都有明确的限制，但对于这些节点，“跨节点平分使用率”指标（资源平衡通常可保证此点）并不重要。对于这一类不需要平衡但必须强制在节点上设置限制的指标，可将平衡权重定义为零。在运行时，Service Fabric 资源平衡器会强制使用容量，但不会尝试主动平衡指标，即使该指标的使用情况在节点之间非常失衡也是如此。

### 主要或次要角色的指标默认负载
在定义服务的指标时，应提供当服务为主要或次要角色时，该指标预期的耗用量。这项信息不仅有助于资源平衡器一开始就以有效的方式放置服务，还可以在整个生存期内，充当服务对相关指标的实际使用量的极佳近似值。Service Fabric 资源平衡器不仅会在放置你的服务时自动将此指标耗用量纳入考虑范围，在它必须基于平衡或因群集中的其他变更而移动副本时，也会这么做。如果服务的负载相当容易预测且非常稳定，表示你可通过设置这些值，让服务选择不在运行时报告负载。

代码示例显示某个服务完全定义两个自定义的指标，一个用于内存使用量，另一个用于磁盘空间：

``` cpp
ServiceLoadMetricDescription memoryMetric = new ServiceLoadMetricDescription();
memoryMetric.Name = "MemoryInMb";
memoryMetric.Weight = ServiceLoadMetricWeight.High;
memoryMetric.PrimaryDefaultLoad = 100;
memoryMetric.SecondaryDefaultLoad = 50;

ServiceLoadMetricDescription diskMetric = new ServiceLoadMetricDescription();
diskMetric.Name = "DiskInMb";
diskMetric.Weight = ServiceLoadMetricWeight.Medium;
diskMetric.PrimaryDefaultLoad = 1024;
diskMetric.SecondaryDefaultLoad = 750;

serviceDescription.Metrics.Add(memoryMetric);
serviceDescription.Metrics.Add(diskMetric);

Task t = fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

以下代码示例通过服务清单显示 ServiceTypes 的定义：

``` xml
<ServiceTypes>
  <StatefulServiceType ServiceTypeName="QueueReplica" HasPersistedState="true">
    <LoadMetrics>
      <LoadMetric Name="MemoryInMb" Weight="High" PrimaryDefaultLoad="100" SecondaryDefaultLoad="50"/>
      <LoadMetric Name="DiskInMb" Weight="Medium" PrimaryDefaultLoad="1024" SecondaryDefaultLoad="750"/>
    </LoadMetrics>
  </StatefulServiceType>
</ServiceTypes>
```

以下代码示例通过应用程序清单显示 ServiceTemplates 的定义：

``` xml
<ServiceTemplates>
   <StatefulService ServiceTypeName="QueueReplica">
     <SingletonPartition></SingletonPartition>
     <LoadMetrics>
       <LoadMetric Name="MemoryInMb" Weight="High" PrimaryDefaultLoad="100" SecondaryDefaultLoad="50"/>
       <LoadMetric Name="DiskInMb" Weight="Medium" PrimaryDefaultLoad="1024" SecondaryDefaultLoad="750"/>
     </LoadMetrics>
   </StatefulService>
 </ServiceTemplates>
```
以下代码示例通过应用程序清单显示 DefaultServices 的定义：

``` xml
<DefaultServices>
  <Service Name="QueueServiceInstance">
    <StatefulService MinReplicaSetSize="3" ServiceTypeName="QueueService" TargetReplicaSetSize="3">
      <SingletonPartition/>
      <LoadMetrics>
        <LoadMetric Name="MemoryInMb" Weight="High" PrimaryDefaultLoad="100" SecondaryDefaultLoad="50"/>
        <LoadMetric Name="DiskInMb" Weight="Medium" PrimaryDefaultLoad="1024" SecondaryDefaultLoad="750"/>
      </LoadMetrics>
    </StatefulService>
  </Service>
</DefaultServices>
```

由于默认负载值不会更新（除非服务特别选择要在运行时通过代码来报告负载），因此这些值也可用来建立功能更加丰富的“容量保留”资源平衡模型。比方说，如果工作负荷一般分为若干个大小的存储段，而且可以在节点上随时放置已知数量的工作单元，则可创建自定义指标“单元”，再根据单元来定义节点容量和服务默认负载。


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 后续步骤

有关详细信息，请参阅：[资源平衡器体系结构](/documentation/articles/service-fabric-resource-balancer-architecture)和[放置约束](/documentation/articles/service-fabric-placement-constraint)

[Image1]: ./media/service-fabric-resource-balancer-service-description/PC.png
[Image2]: ./media/service-fabric-resource-balancer-service-description/DM.png
[Image3]: ./media/service-fabric-resource-balancer-service-description/MW.png
 
<!---HONumber=Mooncake_0321_2016-->