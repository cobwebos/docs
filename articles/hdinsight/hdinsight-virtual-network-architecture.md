---
title: Azure HDInsight 虚拟网络体系结构
description: 了解在 Azure 虚拟网络中创建 HDInsight 群集时可用的资源。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/31/2019
ms.openlocfilehash: b3f622b360f565ef5b16d5376cb1aa2498655017
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79272144"
---
# <a name="azure-hdinsight-virtual-network-architecture"></a>Azure HDInsight 虚拟网络体系结构

本文介绍将 HDInsight 群集部署到自定义 Azure 虚拟网络时存在的资源。 此信息将帮助你将本地资源连接到 Azure 中的 HDInsight 群集。 有关 Azure 虚拟网络的详细信息，请参阅[什么是 Azure 虚拟网络？](../virtual-network/virtual-networks-overview.md)。

## <a name="resource-types-in-azure-hdinsight-clusters"></a>Azure HDInsight 群集中的资源类型

Azure HDInsight 群集具有不同类型的虚拟机或节点。 每个节点类型在系统的操作中扮演着一个角色。 下表汇总了群集中的这些节点类型及其角色。

| 类型 | 说明 |
| --- | --- |
| 头节点 |  对于除 Apache Storm 之外的所有群集类型，头节点承载管理分布式应用程序执行的进程。 头节点也是可通过 SSH 连接并执行应用程序的节点，这些应用程序随后协调为跨群集资源运行。 所有群集类型的头节点数都固定为2。 |
| ZooKeeper 节点 | Zookeeper 协调正在进行数据处理的节点之间的任务。 它还具有头节点的领导人选举，并跟踪哪个头节点正在运行特定的主服务。 ZooKeeper 节点数固定为三。 |
| 辅助角色节点 | 表示支持数据处理功能的节点。 可以在群集中添加或删除工作节点，以缩放计算功能和管理成本。 |
| R Server 边缘节点 | R Server edge 节点表示你可以通过 SSH 连接到的节点并执行应用程序，这些应用程序随后协调为跨群集资源运行。 边缘节点不参与群集内的数据分析。 此节点还托管 R Studio Server，使你能够使用浏览器运行 R 应用程序。 |
| 区域节点 | 对于 HBase 群集类型，区域节点（也称为数据节点）运行区域服务器。 区域服务器提供并管理由 HBase 管理的部分数据。 可以在群集中添加或删除区域节点，以缩放计算功能和管理成本。|
| Nimbus 节点 | 对于风暴群集类型，Nimbus 节点提供了类似于头节点的功能。 Nimbus 节点通过 Zookeeper 将任务分配给群集中的其他节点，该节点可协调风暴拓扑的运行。 |
| 主管节点 | 对于风暴群集类型，监察员节点执行 Nimbus 节点提供的说明以执行所需的处理。 |

## <a name="resource-naming-conventions"></a>资源命名约定

在群集中对节点进行寻址时，请使用完全限定的域名（Fqdn）。 可以使用[AMBARI API](hdinsight-hadoop-manage-ambari-rest-api.md)获取群集中各种节点类型的 fqdn。 

这些 Fqdn 的格式为 `<node-type-prefix><instance-number>-<abbreviated-clustername>.<unique-identifier>.cx.internal.cloudapp.net`。

对于头节点，`<node-type-prefix>` 将*hn* ，对于工作节点为*w)* ，对于 zookeeper 节点为*zn* 。

如果只需要主机名，请仅使用 FQDN 的第一部分： `<node-type-prefix><instance-number>-<abbreviated-clustername>`

## <a name="basic-virtual-network-resources"></a>基本虚拟网络资源

下图显示了 Azure 中 HDInsight 节点和网络资源的位置。

![在 Azure 自定义 VNET 中创建的 HDInsight 实体示意图](./media/hdinsight-virtual-network-architecture/hdinsight-vnet-diagram.png)

将 HDInsight 部署到 Azure 虚拟网络时提供的默认资源包括上表中提到的群集节点类型，以及支持虚拟网络和外部网络之间的通信的网络设备。

下表总结了在 HDInsight 部署到自定义 Azure 虚拟网络中时创建的九个群集节点。

| 资源类型 | 当前数量 | 详细信息 |
| --- | --- | --- |
|头节点 | two |    |
|Zookeeper 节点 | three | |
|辅助角色节点 | two | 此数字根据群集配置和缩放而有所不同。 Apache Kafka 需要至少三个工作节点。  |
|网关节点 | two | 网关节点是在 Azure 中创建的 Azure 虚拟机，但在你的订阅中不可见。 如果需要重新启动这些节点，请联系支持人员。 |

以下网络资源在用于 HDInsight 的虚拟网络中自动创建：

| 网络资源 | 当前数量 | 详细信息 |
| --- | --- | --- |
|负载均衡器 | three | |
|网络接口 | 份 | 此值基于常规群集，其中每个节点都有其自己的网络接口。 这九个接口适用于两个头节点、三个 zookeeper 节点、两个工作节点和上表中提到的两个网关节点。 |
|公共 IP 地址 | two |    |

## <a name="endpoints-for-connecting-to-hdinsight"></a>用于连接到 HDInsight 的终结点

可以通过以下三种方式访问 HDInsight 群集：

- 位于 `CLUSTERNAME.azurehdinsight.net`虚拟网络外部的 HTTPS 终结点。
- 用于直接连接到 `CLUSTERNAME-ssh.azurehdinsight.net`上的头节点的 SSH 终结点。
- 虚拟网络 `CLUSTERNAME-int.azurehdinsight.net`中的 HTTPS 终结点。 请注意此 URL 中的 "-int"。 此终结点将解析为该虚拟网络中的专用 IP，并且无法从公共 internet 进行访问。

这三个终结点都分配有一个负载均衡器。

公共 IP 地址还提供给允许从虚拟网络外部进行连接的两个终结点。

1. 将一个公共 IP 分配给负载均衡器，以用于从 internet `CLUSTERNAME.azurehdinsight.net`连接到群集时要使用的完全限定的域名（FQDN）。
1. 第二个公共 IP 地址仅用于 SSH 域名 `CLUSTERNAME-ssh.azurehdinsight.net`。

## <a name="next-steps"></a>后续步骤

- [使用专用终结点将传入流量保护到虚拟网络中的 HDInsight 群集](https://azure.microsoft.com/blog/secure-incoming-traffic-to-hdinsight-clusters-in-a-vnet-with-private-endpoint/)
