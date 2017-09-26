---
title: "Apache Kafka 增加可伸缩性 - Azure HDInsight | Microsoft Docs"
description: "了解如何在 Azure HDInsight 上为 Apache Kafka 群集配置托管磁盘以提高可伸缩性。"
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
ms.date: 09/07/2017
ms.author: larryfr
ms.translationtype: HT
ms.sourcegitcommit: 12c20264b14a477643a4bbc1469a8d1c0941c6e6
ms.openlocfilehash: 41d96958ee999e4d0b304dfd9296f51d53eb3277
ms.contentlocale: zh-cn
ms.lasthandoff: 09/07/2017

---

# <a name="configure-storage-and-scalability-for-apache-kafka-on-hdinsight"></a>在 HDInsight 上为 Apache Kafka 配置存储和可伸缩性

了解如何在 HDInsight 上配置 Apache Kafka 使用的托管磁盘数。

Kafka on HDInsight 在 HDInsight 群集中使用虚拟机的本地磁盘。 由于 Kafka 的 I/O 很高，因此会使用 [Azure 托管磁盘](../virtual-machines/windows/managed-disks-overview.md)提供高吞吐量，并为每个节点提供更多存储。 如果将传统虚拟硬盘 (VHD) 用于 Kafka，每个节点将被限制为 1 TB。 使用托管磁盘，可以使用多个磁盘，实现群集中每个节点 16 TB。

下图提供不带托管磁盘的 Kafka on HDInsight 与带托管磁盘的 Kafka on HDInsight 之间的比较：

![显示每 VM 使用单个 VHD 与每 VM 使用多个托管磁盘的 Kafka on HDInsight 的图表](./media/hdinsight-apache-kafka-scalability/kafka-with-managed-disks-architecture.png)

## <a name="configure-managed-disks-azure-portal"></a>配置托管磁盘：Azure 门户

1. 按照[创建 HDInsight 群集](hdinsight-hadoop-create-linux-clusters-portal.md)中的步骤操作，了解使用门户创建群集的常用步骤。 请勿完成门户创建过程。

2. 在“群集大小”部分中，使用“每个工作节点的磁盘数”字段来配置磁盘数。

    > [!NOTE]
    > 托管磁盘的类型可以为“标准”(HDD) 或“高级”(SSD)。 高级磁盘可与 DS 和 GS 系列 VM 一起使用。 所有其他的 VM 类型使用“标准”。

    ![“群集大小”部分的图像，其中突出显示了每个工作节点的磁盘数](./media/hdinsight-apache-kafka-scalability/set-managed-disks-portal.png)

## <a name="configure-managed-disks-resource-manager-template"></a>配置托管磁盘：Resource Manager 模板

若要控制 Kafka 群集中辅助节点使用的磁盘数，请使用模板的以下部分：

```json
"dataDisksGroups": [
    {
        "disksPerNode": "[variables('disksPerWorkerNode')]"
    }
    ],
```

[https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-mirror-cluster-in-vnet-v2.1.json](https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-mirror-cluster-in-vnet-v2.1.json) 处提供演示如何配置托管磁盘的完整模板。

## <a name="next-steps"></a>后续步骤

有关使用 Kafka on HDInsight 的详细信息，请参阅以下文档：

* [使用 MirrorMaker 创建 Kafka on HDInsight 的副本](hdinsight-apache-kafka-mirroring.md)
* [将 Apache Storm 与 Kafka on HDInsight 结合使用](hdinsight-apache-storm-with-kafka.md)
* [将 Apache Spark 与 Kafka on HDInsight 结合使用](hdinsight-apache-spark-with-kafka.md)
* [通过 Azure 虚拟网络连接到 Kafka](hdinsight-apache-kafka-connect-vpn-gateway.md)

* [Kafka 托管磁盘相关的 HDInsight 博客](https://azure.microsoft.com/blog/announcing-public-preview-of-apache-kafka-on-hdinsight-with-azure-managed-disks/)
