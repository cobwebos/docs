---
title: "Apache Kafka 的高可用性 - Azure HDInsight | Microsoft Docs"
description: "了解如何确保 Azure HDInsight 上 Apache Kafka 的高可用性。 了解如何重新均衡 Kafka 中的分区副本，使之位于 Azure 区域（其中包含 HDInsight）中的不同容错域上。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: 
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/20/2017
ms.author: larryfr
ms.openlocfilehash: fe4cb62e4ceba46b44bdc62b05be4a6b1e2afa3d
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/03/2017
---
# <a name="high-availability-of-your-data-with-apache-kafka-preview-on-hdinsight"></a>通过 HDInsight 上的 Apache Kafka（预览版）实现数据的高可用性

了解如何为 Kafka 主题配置分区副本，充分利用基础硬件机架配置。 此配置可确保存储在 HDInsight 上的 Apache Kafka 中的数据的可用性。

## <a name="fault-and-update-domains-with-kafka"></a>Kafka 的容错域和更新域

容错域是 Azure 数据中心基础硬件的逻辑分组。 每个容错域共享公用电源和网络交换机。 在 HDInsight 群集中实现节点的虚拟机和托管磁盘跨这些容错域分布。 此体系结构可限制物理硬件故障造成的潜在影响。

每个 Azure 区域都有特定数量的容错域。 如需域的列表及其所含容错域的数目，请参阅[可用性集](../../virtual-machines/windows/regions-and-availability.md#availability-sets)文档。

> [!IMPORTANT]
> Kafka 不识别容错域。 在 Kafka 中创建主题时，可能会将所有分区副本存储在同一容错域中。 为了解决此问题，我们提供了 [Kafka 分区重新均衡工具](https://github.com/hdinsight/hdinsight-kafka-tools)。

## <a name="when-to-rebalance-partition-replicas"></a>何时重新均衡分区副本

若要确保 Kafka 数据的最高可用性，应该在以下时间为主题重新均衡分区副本：

* 创建新主题或分区时

* 扩展群集时

## <a name="replication-factor"></a>复制因子

> [!IMPORTANT]
> 建议使用包含三个容错域的 Azure 区域，并使用 3 作为复制因子。

如果必须使用只包含两个容错域的区域，请使用 4 作为复制因子，将副本均衡地分布到两个容错域中。

如需创建主题和设置复制因子的示例，请参阅 [HDInsight 上的 Kafka 入门](apache-kafka-get-started.md)文档。

## <a name="how-to-rebalance-partition-replicas"></a>如何重新均衡分区副本

使用 [Kafka 分区重新均衡工具](https://github.com/hdinsight/hdinsight-kafka-tools)来重新均衡所选主题。 必须通过 SSH 会话运行此工具，以便连接到 Kafka 群集的头节点。

若要详细了解如何使用 SSH 连接到 HDInsight，请参阅[将 SSH 与 HDInsight 配合使用](../hdinsight-hadoop-linux-use-ssh-unix.md)文档。

## <a name="next-steps"></a>后续步骤

* [HDInsight 上的 Kafka 的可伸缩性](apache-kafka-scalability.md)
* [通过 HDInsight 上的 Kafka 进行镜像](apache-kafka-mirroring.md)