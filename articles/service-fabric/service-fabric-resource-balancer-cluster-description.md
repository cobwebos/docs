<properties
   pageTitle="资源平衡器群集描述 | Microsoft Azure"
   description="通过在资源平衡器中指定容错域、升级域、节点属性和节点容量来描述 Service Fabric 群集。"
   services="service-fabric"
   documentationCenter=".net"
   authors="GaugeField"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.date="09/03/2015"
   wacn.date=""/>

# 描述 Service Fabric 群集

Azure Service Fabric 中的资源平衡器提供多种群集描述机制。在运行时，资源平衡器会使用这些信息，确保放置服务的方式可保障群集中运行的服务的高可用性，以及群集资源的最大使用率。资源平衡器用来描述群集的功能包括容错域、升级域、节点属性和节点容量。除此之外，资源平衡器还有一些可以调校其性能的配置选项。

## 关键概念

### 容错域

容错域可让群集管理员定义可能因共享的物理依赖项（比如电源和网络源）而同时发生故障的物理节点。容错域通常代表这些共享依赖项的相关层次结构，它包含可能从容错域树中的较高点一起发生故障的更多节点。下图显示根据分层容错域来架构的数个节点（按数据中心、机架和边栏选项卡排序）。

![通过故障域组织的节点][Image1]

 在运行时，Azure Resource Manager 会考虑群集中的容错域，并尝试分散指定服务的副本，以便将它们全都放在不同的容错域中。在任何一个容错域发生故障时，此过程有助于确保该服务及其状态的可用性不遭到破坏。下图显示即使有足够的空间将服务副本集中在一个或两个域中，仍将其分散于数个容错域中。

![分布在容错域之间的服务副本][Image2]

容错域在群集清单内配置。每个节点均定义为位于特定的容错域内。在运行时，Resource Manager 会整合所有节点的报告，以便对系统中的所有容错域形成一个完整的概观。

### 升级域

升级域是 Resource Manager 所使用的另一项信息。与容错域类似，升级域描述几乎在相同时间关闭以进行升级的节点集。升级域不分层，因此可以视作一种标记。

容错域由群集中节点的物理布局来定义，升级域则由群集管理员根据相关策略来确定。这些策略与群集内的升级相关。升级域越多，升级就越精确，因为升级域可以限制对正在运行的群集和服务的影响，并防止任何故障对大量服务造成影响。根据其他策略（比如 Service Fabric 在升级域之后、移到下一个域之前应等待的时间），较多的升级域也可能增加完成群集升级所需的时间。

出于这些原因，Resource Manager 会收集升级域的信息，并将副本分散至群集中的各个升级域，就像容错域一样。升级域不一定与容错域一一对应，但通常也不应该一一对应。下图显示位于先前定义的容错域之上的包含数个升级域的层。Resource Manager 仍会将副本分散到各个域中，以免副本集中在某些容错域或升级域中，藉此确保服务的高可用性，不受群集中的升级或故障所影响。

![升级域][Image3]

升级域和容错域均配置为群集清单内节点定义的一部分，如下所示：

``` xml
<Infrastructure>
    <WindowsServer>
      <NodeList>
        <Node NodeTypeRef="NodeType01" IsSeedNode="true" IPAddressOrFQDN="localhost" NodeName="Node1" FaultDomain="fd:/RACK1/BLADE1" UpgradeDomain="ud:/UD1"/>
        <Node NodeTypeRef="NodeType01" IsSeedNode="false" IPAddressOrFQDN="localhost" NodeName="Node2" FaultDomain="fd:/RACK2/BLADE1" UpgradeDomain="ud:/UD2"/>
        <Node NodeTypeRef="NodeType01" IsSeedNode="true" IPAddressOrFQDN="localhost" NodeName="Node3" FaultDomain="fd:/RACK3/BLADE2" UpgradeDomain="ud:/UD3"/>
        <Node NodeTypeRef="NodeType01" IsSeedNode="false" IPAddressOrFQDN="localhost" NodeName="Node4" FaultDomain="fd:/RACK2/BLADE2" UpgradeDomain="ud:/UD1"/>
        <Node NodeTypeRef="NodeType01" IsSeedNode="true" IPAddressOrFQDN="localhost" NodeName="Node5" FaultDomain="fd:/RACK1/BLADE2" UpgradeDomain="ud:/UD2"/>
        </NodeList>
    </WindowsServer>
</Infrastructure>
```
> [AZURE.NOTE] 在 Azure 部署中，由 Azure 分配容错域和升级域。因此，Azure 基础结构选项中节点和角色的定义不包含容错域或升级域信息。

### 节点属性
节点属性是用户定义的键/值对，可为指定节点提供额外的元数据。节点属性的示例包括节点是否有硬盘或视频卡、其硬盘的轴数、核心和其他物理属性。

节点属性也可用来指定较抽象的属性，以帮助制定放置策略决策。例如，可将“颜色”分配给群集内的数个节点，作为将群集分割成不同节的依据。以下代码示例显示，节点的节点属性通过群集清单定义为节点类型定义的一部分。然后，可将节点属性应用于群集内的多个节点。

NodeName、NodeType、FaultDomain 和 UpgradeDomain 放置属性均有默认值。Service Fabric 会自动为你提供默认值，以便创建服务时使用。用户不应使用相同的名称来指定自己的放置属性。

``` xml
<NodeTypes>
  <NodeType Name="NodeType1">
    <PlacementProperties>
      <Property Name="HasDisk" Value="true"/>
      <Property Name="SpindleCount" Value="4"/>
      <Property Name="HasGPU" Value="true"/>
      <Property Name="NodeColor" Value="blue"/>
      <Property Name="NodeName" Value="Node1"/>
      <Property Name="NodeType" Value="NodeType1"/>
      <Property Name="FaultDomain" Value="fd:/RACK1/BLADE1"/>
      <Property Name="UpgradeDomain" Value="ud:/UD1"/>
    </PlacementProperties>
  </NodeType>
    <NodeType Name="NodeType2">
    <PlacementProperties>
      <Property Name="HasDisk" Value="false"/>
      <Property Name="SpindleCount" Value="-1"/>
      <Property Name="HasGPU" Value="false"/>
      <Property Name="NodeColor" Value="green"/>
    </PlacementProperties>
  </NodeType>
</NodeTypes>
```

在运行时，资源平衡器会使用节点属性信息，确保将需要特定功能的服务放在适当的节点上。

### 节点容量
节点容量是一种键/值对，用来定义特定节点可以使用的特定资源的名称和数量。以下代码示例显示了一个节点，该节点具有名为“MemoryInMB”的指标容量，而且默认有 2048 MB 的可用内存。容量通过群集清单定义，就像节点属性一样。

``` xml
<NodeType Name="NodeType03">
  <Capacities>
    <Capacity Name="MemoryInMB" Value="2048"/>
    <Capacity Name="DiskSpaceInGB" Value="1024"/>
  </Capacities>
</NodeType>
```
![节点容量][Image4]

由于节点上运行的服务可以通过报告负载来更新其容量需求，因此，资源平衡器还会定期检查节点是否达到或超过其任意指标的容量。如果是，资源平衡器可将服务移至负载较少的节点，以减少资源争用并提升整体性能和使用率。

请注意，虽然指定指标也可以在节点类型的属性节中列示，但如果是可以在运行时使用的节点的属性，则最好列为容量。将指定指标另外列为属性时，可允许依赖于“最低硬件需求”的服务查询含有放置约束的节点。如果在运行时，其他服务也使用该资源的话，就可能需要这么做。建议同时将其纳为容量，以便资源平衡器采取其他操作。

创建新的服务时，Service Fabric 资源平衡器会使用现有节点的容量及现有服务的使用量的相关信息，判断是否有足够容量可容纳整个新服务。如果容量不足，则会拒绝“创建服务”请求并显示一条解释容量问题的错误消息。


### 资源平衡器配置

在群集清单中，以下配置值定义了资源平衡器的整体行为。

*平衡阈值* 控制群集在资源平衡器响应之前针对某个特定指标可能达到的失衡程度。平衡阈值是指资源平衡器重新平衡群集之前，允许存在的最常使用节点与最少使用节点的比率上限。

下图显示了两个示例，其中，指定指标的平衡阈值为 10。

![平衡阈值][Image5]

请注意，在这个阶段，节点上的“使用率”并不考虑节点容量所决定的节点大小。它只考虑目前针对指定指标报告的节点绝对使用量。

以下代码示例显示，指标的平衡阈值通过群集清单内的 FabricSettings 元素，针对每个指标进行配置。

``` xml
<FabricSettings>
  <Section Name="MetricBalancingThresholds">
    <Parameter Name="MetricName" Value="10"/>
  </Section>
</FabricSettings>
```

*活动阈值* 可限制资源平衡器运行的频率，方法是限制资源平衡器在出现大量绝对负载时进行响应的情况。这样一来，如果以特定的指标来说，群集不是非常忙碌，资源平衡器就不会运行，即使群集内有少量指标非常不平衡也是如此。这种方法可防止进行实质效益很低的群集重新平衡所带来的资源浪费。下图显示指标的平衡阈值为 4，活动阈值为 1536。

![活动阈值][Image6]

另请注意，对于同一指标，必须同时超出活动阈值和平衡阈值，资源平衡器才会运行。触发两个不同指标的其中一项阈值时，资源平衡器不会运行。

以下代码示例显示，与平衡阈值一样，活动阈值通过群集清单内的 FabricSettings 元素，针对每个指标进行配置。

``` xml
<FabricSettings>
  <Section Name="MetricActivityThresholds">
    <Parameter Name="MetricName" Value="1536"/>
  </Section>
</FabricSettings>
```

- PLBRefreshInterval - 控制资源平衡器扫描信息且必须检查约束冲突的频率。约束冲突包括：不符合的放置约束、所需实例或副本数量不足的服务、某个指标超出容量的节点、容错域或升级域过载，或群集失衡。资源平衡器使用平衡阈值和活动阈值以及群集节点上的当前负载视图来识别约束违规。刷新间隔以秒为单位定义，默认值为 1。也可以使用 MinConstraintCheckInterval 和 MinPlacementInterval 这两个新的间隔来控制约束检查与放置的频率。如果定义这些新的参数，就无法使用和定义 PLBRefreshInterval。

- MinConstraintCheckInterval - 控制资源平衡器扫描信息且必须检查约束冲突的频率。约束冲突包括：不符合的放置约束、所需实例或副本数量不足的服务、某个指标超出容量的节点、容错域或升级域过载，或群集失衡。资源平衡器使用平衡阈值和活动阈值以及群集节点上的当前负载视图来识别约束违规。约束检查间隔以秒为单位定义。如果未定义约束检查间隔，其默认值将等于 PLBRefreshInterval 的值。（两个值不能同时指定。）

- MinPlacementInterval - 控制资源平衡器检查是否有需要放置的新实例或副本并尝试加以放置的频率。放置间隔以秒为单位定义。如果未定义放置间隔，其默认值将等于 PLBRefreshInterval 的值。（两个值不能同时指定。）

- MinLoadBalancingInterval - 设置资源平衡回合间隔的最短时间。如果资源平衡器已根据来自上次 PLBRefreshInterval 间隔期间所执行的扫描的信息采取操作，那么，至少要等相同时间过后才会再次运行。它以秒为单位定义，默认值为 5。

请注意，这些值具有攻击性，但是，总的来说，只有在达到指定指标的平衡阈值和活动阈值时，才会在群集中进行负载平衡。以下代码示例显示，如果特定群集不需要精确主动的负载平衡，可以通过 FabricSettings 元素中的配置，将这些值设置得较为保守。在此示例配置中，两个约束检查之间最短的时间间隔为 10 秒，放置最短时间为 5 秒，而负载平衡会每隔 5 分钟发生一次。请注意，在此情况中，未定义 PLBRefreshInterval。

``` xml
<Section Name="PlacementAndLoadBalancing">
  <Parameter Name="MinPlacementInterval" Value="5" />
  <Parameter Name="MinConstraintChecknterval" Value="10" />
  <Parameter Name="MinLoadBalancingInterval" Value="600" />
</Section>
```

在下面的第二个示例配置中，我们定义了 PLBRefreshInterval 和 MinLoadBalancingInterval。由于 PLBRefreshInterval 设置为 2 秒，MinPlacementInterval 和 MinConstraintCheckInterval 的值也必须设置为 2 秒。

``` xml
<Section Name="PlacementAndLoadBalancing">
  <Parameter Name="PLBRefreshInterval" Value="2" />
  <Parameter Name="MinLoadBalancingInterval" Value="600" />
</Section>
```

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 后续步骤

有关详细信息，请参阅：[资源平衡器体系结构](/documentation/articles/service-fabric-resource-balancer-architecture)


[Image1]: ./media/service-fabric-resource-balancer-cluster-description/FD1.png
[Image2]: ./media/service-fabric-resource-balancer-cluster-description/FD2.png
[Image3]: ./media/service-fabric-resource-balancer-cluster-description/UD.png
[Image4]: ./media/service-fabric-resource-balancer-cluster-description/NC.png
[Image5]: ./media/service-fabric-resource-balancer-cluster-description/Config.png
[Image6]: ./media/service-fabric-resource-balancer-cluster-description/Thresholds.png
 

<!---HONumber=Mooncake_0314_2016-->