---
title: Azure HDInsight 虚拟网络体系结构
description: 了解在 Azure 虚拟网络中创建 HDInsight 群集时可用的资源。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/31/2019
ms.openlocfilehash: 0a1139f7bf1711a5f6d980e67a8a9027bfd3af52
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73665323"
---
# <a name="azure-hdinsight-virtual-network-architecture"></a>Azure HDInsight 虚拟网络体系结构

本文介绍在将 HDInsight 群集部署到自定义的 Azure 虚拟网络中时提供的资源。 此信息可帮助你将本地资源连接到 Azure 中的 HDInsight 群集。 有关 Azure 虚拟网络的详细信息，请参阅[什么是 Azure 虚拟网络？](../virtual-network/virtual-networks-overview.md)。

## <a name="resource-types-in-azure-hdinsight-clusters"></a>Azure HDInsight 群集中的资源类型

Azure HDInsight 群集包含不同类型的虚拟机（或节点）。 每个节点类型对于系统的正常运行发挥着相应的作用。 下表汇总了这些节点类型及其在群集中的作用。

| 类型 | 说明 |
| --- | --- |
| 头节点 |  对于除 Apache Storm 以外的所有群集类型，头节点托管用于管理分布式应用程序的执行的进程。 头节点也是可以通过 SSH 连接到其中并执行应用程序的节点。连接后，系统会协调这些应用程序，使其可在不同的群集资源上运行。 所有群集类型的头节点数目固定为 2 个。 |
| ZooKeeper 节点 | ZooKeeper 协调执行数据处理的节点之间的任务。 它还执行头节点的主控选择，并跟踪哪个头节点正在运行特定的主服务。 ZooKeeper 节点数目固定为 3 个。 |
| 工作器节点 | 表示支持数据处理功能的节点。 可以在群集中添加或删除工作器节点，以缩放计算能力和控制成本。 |
| R Server 边缘节点 | R Server 边缘节点是可以通过 SSH 连接到其中并执行应用程序的节点。连接后，系统会协调这些应用程序，使其可在不同的群集资源上运行。 边缘节点不参与群集内部的数据分析。 此节点还托管 R Studio Server，使你能够使用浏览器运行 R 应用程序。 |
| 区域节点 | 对于 HBase 群集类型，区域节点（也称为数据节点）将运行区域服务器。 区域服务器为 HBase 管理的一部分数据提供服务，并对其进行管理。 可以在群集中添加或删除区域节点，以缩放计算能力和控制成本。|
| Nimbus 节点 | 对于 Storm 群集类型，Nimbus 节点提供类似于头节点的功能。 Nimbus 节点通过 Zookeeper 将任务分配给群集中的其他节点，Zookeeper 协调 Storm 拓扑的运行。 |
| 监督器节点 | 对于 Storm 群集类型，监督器节点执行 Nimbus 节点所提供的、执行所需处理的指令。 |

## <a name="basic-virtual-network-resources"></a>基本虚拟网络资源

下图显示了 HDInsight 节点和网络资源在 Azure 中的位置。

![在 Azure 自定义 VNET 中创建的 HDInsight 实体的关系图](./media/hdinsight-virtual-network-architecture/hdinsight-vnet-diagram.png)

将 HDInsight 部署到 Azure 虚拟网络时提供的默认资源包括上表中所述的群集节点类型，以及用于支持在虚拟网络与外部网络之间进行通信的网络设备。

下表汇总了将 HDInsight 部署到自定义 Azure 虚拟网络时创建的 9 个群集节点。

| 资源类型 | 提供的数量 | 详细信息 |
| --- | --- | --- |
|头节点 | two |    |
|Zookeeper 节点 | three | |
|工作器节点 | two | 此数字根据群集的配置和规模而异。 Apache Kafka 至少需要 3 个工作器节点。  |
|网关节点 | two | 网关节点是在 Azure 中创建的、但不会在订阅中显示的 Azure 虚拟机。 如果需要重新启动这些节点，请联系支持人员。 |

在与 HDInsight 配合使用的虚拟网络中，会自动创建以下网络资源：

| 网络资源 | 提供的数量 | 详细信息 |
| --- | --- | --- |
|负载均衡 | three | |
|网络接口 | 9 个 | 此值基于普通群集，在此类群集中，每个节点具有自身的网络接口。 9 个接口分别用于上表中所述的 2 个头节点、3 个 ZooKeeper 节点、2 个工作器节点和 2 个网关节点。 |
|公共 IP 地址 | two |    |

## <a name="endpoints-for-connecting-to-hdinsight"></a>用于连接 HDInsight 的终结点

可通过三种方式访问 HDInsight 群集：

- 虚拟网络 (`CLUSTERNAME.azurehdinsight.net`) 外部的 HTTPS 终结点。
- 直接连接到位于 `CLUSTERNAME-ssh.azurehdinsight.net` 中的头节点的 SSH 终结点。
- 虚拟网络 (`CLUSTERNAME-int.azurehdinsight.net`) 内部的 HTTPS 终结点。 请注意此 URL 中的“-int”。 此终结点将解析为该虚拟网络中的专用 IP，无法从公共 Internet 访问。

在这 3 个终结点中，每个终结点分配有一个负载均衡器。

此外，将为 2 个终结点提供公共 IP 地址，以便从虚拟网络外部进行连接。

1. 将为负载均衡器分配 1 个公共 IP，以便从 Internet `CLUSTERNAME.azurehdinsight.net` 连接到群集时使用完全限定的域名 (FQDN)。
1. 第二个公共 IP 地址用于仅限 SSH 的域名 `CLUSTERNAME-ssh.azurehdinsight.net`。

## <a name="next-steps"></a>后续步骤

- [使用专用终结点保护传入虚拟网络中 HDInsight 群集的流量](https://azure.microsoft.com/blog/secure-incoming-traffic-to-hdinsight-clusters-in-a-vnet-with-private-endpoint/)
