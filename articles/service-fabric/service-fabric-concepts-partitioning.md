<properties
   pageTitle="Service Fabric 服务分区"
   description="介绍如何对 Service Fabric 服务进行分区"
   services="service-fabric"
   documentationCenter=".net"
   authors="appi101"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.date="08/26/2015"
   wacn.date=""/>

# Service Fabric 服务分区
通过支持对服务状态进行分区并让每个分区在总状态的子集上运行，Service Fabric 让可伸缩的有状态服务的生成更为简单。每个分区成为[高度可用](documentation/articles/service-fabric-availability-services)的单元。各分区的副本将分布在群集中的各个节点并保持平衡。

> [AZURE.NOTE]虽然无状态服务也可以进行分区，但这种情况对大多数 Service Fabric 服务而言都是罕见且不必要的。

共有三种不同的分区方案。

## 单独分区方案
此方案用于指定服务无需进行分区。

## 命名分区方案
此方案用于为服务的每个分区指定一组固定的名称。可以按相应名称查找各个分区。

## 范围分区方案
此方案用于指定整数范围（由低键和高键标识）和分区数目 (n)。它将创建 n 个分区，每个分区负责未重叠的子范围。示例：采用低键 0、高键 99 和计数 4 的范围分区方案（适用于具有三个副本的服务）将创建如下所示的 4 个分区。

![范围分区](./media/service-fabric-concepts-partitioning/range-partitioning.png)

常见的方法是为数据集中的唯一键创建哈希值。一些常见的键示例有：车辆识别号 (VIN)、员工 ID 或唯一字符串。然后使用该唯一键创建一个长哈希代码（键范围取模）以用作你的键。可以指定所允许键范围的上限和下限。

此外，应当估计充足的分区数量以处理最坏情况的资源加载（如内存限制或网络带宽），但也不可太高而使分区过于稀疏。

### 选择哈希算法
哈希法的重要部分是选择哈希算法。一个重要考虑事项是：目标是否是对相邻的类似键进行分组（局部敏感哈希法），或者活动是否应广泛分布在所有分区上（分发哈希法）。

有关选择常规哈希代码算法的良好资源是[哈希函数的维基百科网页](http://en.wikipedia.org/wiki/Hash_function)。

> [AZURE.NOTE]创建方案后，无法更改分区数或服务使用的分区方案类型。

## 后续步骤

有关 Service Fabric 概念的信息，请参阅以下内容：

- [Service Fabric 服务的可用性](documentation/articles/service-fabric-availability-services)

- [Service Fabric 服务的可伸缩性](documentation/articles/service-fabric-concepts-scalability)
 

<!---HONumber=74-->