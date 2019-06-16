---
title: Azure HDInsight 虚拟网络体系结构
description: 在 Azure 虚拟网络中创建的 HDInsight 群集时，请了解可用的资源。
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: hrasheed
ms.openlocfilehash: 41420497bffd0abdc598e4c86b2dbda1466b2ce1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66252851"
---
# <a name="azure-hdinsight-virtual-network-architecture"></a>Azure HDInsight 虚拟网络体系结构

本文介绍了在部署到自定义的 Azure 虚拟网络的 HDInsight 群集时存在的资源。 此信息将帮助你的本地资源连接到 Azure 中的 HDInsight 群集。 有关 Azure 虚拟网络的详细信息，请参阅[什么是 Azure 虚拟网络？](../virtual-network/virtual-networks-overview.md)。

## <a name="resource-types-in-azure-hdinsight-clusters"></a>在 Azure HDInsight 群集中的资源类型

Azure HDInsight 群集具有不同类型的虚拟机或节点。 每个节点类型系统的操作中起作用。 下表总结了这些节点类型和群集中的其角色。

| Type | 描述 |
| --- | --- |
| 头节点 |  除了 Apache Storm 的所有群集类型，头节点都托管的管理的分布式应用程序执行的进程。 头节点是还可以通过 ssh 连接到的节点，并执行应用程序，然后进行协调以运行跨群集资源。 头节点的数目被固定的两个用于所有群集类型。 |
| ZooKeeper 节点 | Zookeeper 协调执行数据处理的节点之间的任务。 它还 does 头节点、 群首选举并跟踪哪个头节点正在运行特定的主服务。 ZooKeeper 节点数被固定的三个。 |
| 辅助角色节点 | 表示支持数据处理功能的节点。 可以添加或从群集缩放的计算能力和管理成本中删除辅助角色节点。 |
| R Server 边缘节点 | R Server 边缘节点表示你可以通过 ssh 连接到的节点，并执行应用程序，然后进行协调以运行跨群集资源。 边缘节点不参与群集内的数据分析。 此节点还承载 R Studio Server，使你能够运行 R 应用程序使用浏览器。 |
| 区域节点 | 对于 HBase 群集类型，区域服务器在运行 （也称为数据节点） 的区域节点。 区域服务器提供服务和管理 HBase 管理的数据的一部分。 可以添加或从要缩放的计算能力和管理成本的群集删除区域节点。|
| Nimbus 节点 | 对于 Storm 群集类型，Nimbus 节点提供类似于头节点的功能。 Nimbus 节点将任务分配给通过 Zookeeper，协调的 Storm 拓扑运行的群集中的其他节点。 |
| 监督器节点 | 对于 Storm 群集类型，在监督器节点执行到执行所需的处理的 Nimbus 节点提供的说明。 |

## <a name="basic-virtual-network-resources"></a>基本虚拟网络资源

下图显示了 Azure 中的 HDInsight 节点和网络资源的位置。

![创建自定义 Azure VNET 中的 HDInsight 实体的关系图](./media/hdinsight-virtual-network-architecture/vnet-diagram.png)

HDInsight 部署到 Azure 虚拟网络时存在的默认资源包括上表中，以及支持之间的虚拟网络和外部网络进行通信的网络设备中所述的群集节点类型。

下表总结了 HDInsight 部署到自定义的 Azure 虚拟网络时，会创建 9 个群集节点。

| 资源类型 | 数量 | 详细信息 |
| --- | --- | --- |
|头节点 | two |    |
|Zookeeper 节点 | three | |
|辅助角色节点 | two | 此数字可以差异取决于群集配置和缩放。 Apache Kafka 需要三个辅助角色节点的最小值。  |
|网关节点 | two | 网关节点是 Azure 虚拟机的创建在 Azure 上，但不会显示在你的订阅。 如果您需要重新启动这些节点，请联系支持部门。 |

在与 HDInsight 一起使用的虚拟网络中自动创建存在的以下网络资源：

| 网络资源 | 数量 | 详细信息 |
| --- | --- | --- |
|负载均衡 | three | |
|网络接口 | 九个 | 此值基于标准群集，其中每个节点都有其自己的网络接口。 在两个头节点、 三个 zookeeper 节点、 两个辅助角色节点，和上一个表中提到的两个网关节点是九个接口。 |
|公共 IP 地址 | two |    |

## <a name="endpoints-for-connecting-to-hdinsight"></a>用于连接到 HDInsight 的终结点

您可以访问以下三种方式在 HDInsight 群集：

- HTTPS 终结点在虚拟网络外部`CLUSTERNAME.azurehdinsight.net`。
- 直接连接到在头节点的 SSH 终结点`CLUSTERNAME-ssh.azurehdinsight.net`。
- 虚拟网络中的 HTTPS 终结点`CLUSTERNAME-int.azurehdinsight.net`。 请注意，"-int"在此 URL。 此终结点将解析为该虚拟网络内的专用 IP 并不是可从公共 internet 访问。

每个分配的负载均衡器这些三个终结点。

公共 IP 地址也将提供给允许从虚拟网络外部的连接的两个终结点。

1. 一个公共 IP 分配给负载均衡器的完全限定的域名 (FQDN) 从 internet 连接到群集时要使用`CLUSTERNAME.azurehdinsight.net`。
1. 第二个公共 IP 地址用于 SSH 的唯一域名`CLUSTERNAME-ssh.azurehdinsight.net`。

## <a name="next-steps"></a>后续步骤

* [保护传入流量发往专用终结点的虚拟网络中的 HDInsight 群集](https://azure.microsoft.com/blog/secure-incoming-traffic-to-hdinsight-clusters-in-a-vnet-with-private-endpoint/)
