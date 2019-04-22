---
title: Azure HDInsight 发行说明
description: Azure HDInsight 的最新发行说明。 获取 Hadoop、 Spark、 R Server、 Hive 和更多开发技巧和详细信息。
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: 5769f90ef69a82497194ff6de01b378acc84deec
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2019
ms.locfileid: "59678374"
---
# <a name="release-notes-for-azure-hdinsight"></a>Azure HDInsight 发行说明

本文提供有关**最新** Azure HDInsight 版本更新的信息。 有关较早版本的信息，请参阅 [HDInsight 发行说明存档](hdinsight-release-notes-archive.md)。

> [!IMPORTANT]  
> Linux 是 HDInsight 3.4 或更高版本上使用的唯一操作系统。 有关详细信息，请参阅 [HDInsight 版本控制文章](hdinsight-component-versioning.md)。

## <a name="summary"></a>摘要

Azure HDInsight 是 Azure 中最受企业客户青睐的开源 Apache Hadoop 和 Apache Spark 分析服务之一。

## <a name="new-features"></a>新增功能

有关使用 HDInsight 4.0 的重要更改的详细信息。，请参阅[什么是 HDI 4.0 中的新增功能？](../hdinsight/hdinsight-version-release.md)。

## <a name="component-versions"></a>组件版本

下面给出了所有 HDInsight 4.0 组件的官方 Apache 版本。 列出的组件是提供的最新稳定版本的版本。

- Apache Ambari 2.7.1
- Apache Hadoop 3.1.1
- Apache HBase 2.0.0
- Apache Hive 3.1.0
- Apache Kafka 1.1.1
- Apache Mahout 0.9.0+
- Apache Oozie 4.2.0
- Apache Phoenix 4.7.0
- Apache Pig 0.16.0
- Apache Ranger 0.7.0
- Apache Slider 0.92.0
- Apache Spark 2.3.1
- Apache Sqoop 1.4.7
- Apache TEZ 0.9.1
- Apache Zeppelin 0.8.0
- Apache ZooKeeper 3.4.6

HDP 分发除了上面列出的版本中有时捆绑中的 Apache 组件的更高版本。 在这种情况下，这些较高版本会列在“技术预览”表中，并且在生产环境中不应替换为上述列表中的 Apache 组件版本。

## <a name="apache-patch-information"></a>Apache 修补程序信息

在 HDInsight 4.0 中提供的修补程序的详细信息，请参阅下表中每个产品列出的修补程序。

| 产品名称 | 修补程序信息 |
|---|---|
| Ambari | [Ambari 修补程序信息](https://docs.hortonworks.com/HDPDocuments/Ambari-2.7.1.0/bk_ambari-release-notes/content/ambari_relnotes-2.7.1.0-patch-information.html) |
| Hadoop | [Hadoop 修补程序信息](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_hadoop.html) |
| HBase | [HBase 修补程序信息](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_hbase.html) |
| Hive  | 此版本提供了 Hive 3.1.0 与任何其他 Apache 修补程序。  |
| Kafka | 此版本提供了 Kafka 1.1.1 与任何其他 Apache 修补程序。 |
| Oozie | [Oozie 修补程序信息](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_oozie.html) |
| Phoenix | [Phoenix 修补程序信息](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_phoenix.html) |
| Pig | [Pig 修补程序信息](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_pig.html) |
| Ranger | [Ranger 修补程序信息](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_ranger.html) |
| Spark | [Spark 修补程序信息](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_spark.html) |
| Sqoop | 此版本提供了 Sqoop 1.4.7 与任何其他 Apache 修补程序。 |
| Tez | 此版本提供了 Tez 0.9.1 与任何其他 Apache 修补程序。 |
| Zeppelin | 此版本提供了 Zeppelin 0.8.0 与任何其他 Apache 修补程序。 |
| Zookeeper | [Zookeeper 修补程序信息](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_zookeeper.html) |

## <a name="fixed-common-vulnerabilities-and-exposures"></a>修复了常见漏洞和透露

有关安全性的详细信息中已解决的问题发行，请参阅 Hortonworks[固定的常见漏洞和披露 hdp 3.0.1](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/cve.html)。

## <a name="known-issues"></a>已知问题

### <a name="replication-is-broken-for-secure-hbase-with-default-installation"></a>复制已中断保护 hbase 使用默认的安装

对于 HDInsight 4.0，请执行以下步骤操作：

1. 启用群集间的通信。
1. 登录到活动头节点。
1. 下载脚本以启用复制使用以下命令：

    ```
    sudo wget https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_enable_replication.sh
    ```
1. 键入命令`sudo kinit <domainuser>`。
1. 键入以下命令以运行该脚本：

    ```
    sudo bash hdi_enable_replication.sh -m <hn0> -s <srclusterdns> -d <dstclusterdns> -sp <srcclusterpasswd> -dp <dstclusterpasswd> -copydata
    ```
HDInsight 3.6，请执行以下操作：

1. 登录到 active HMaster ZK。
1. 下载脚本以启用复制使用以下命令：
    ```
    sudo wget https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_enable_replication.sh
    ```
1. 键入命令`sudo kinit -k -t /etc/security/keytabs/hbase.service.keytab hbase/<FQDN>@<DOMAIN>`。
1. 输入以下命令：

    ```bash
    sudo bash hdi_enable_replication.sh -s <srclusterdns> -d <dstclusterdns> -sp <srcclusterpasswd> -dp <dstclusterpasswd> -copydata
    ```

### <a name="phoenix-sqlline-stops-working-after-migrating-hbase-cluster-to-hdinsight-40"></a>Phoenix Sqlline 迁移后停止工作到 HDInsight 4.0 的 HBase 群集

请执行以下步骤：

1. 删除以下 Phoenix 表：
    1. `SYSTEM.FUNCTION`
    1. `SYSTEM.SEQUENCE`
    1. `SYSTEM.STATS`
    1. `SYSTEM.MUTEX`
    1. `SYSTEM.CATALOG`
1. 如果您不能删除的任何表，重启 HBase 来清除任何连接到的表。
1. 再次运行 `sqlline.py`。 Phoenix 将重新创建所有已删除在步骤 1 中的表。
1. 重新生成 Phoenix 表和为 HBase 数据的视图。

### <a name="phoenix-sqlline-stops-working-after-replicating-hbase-phoenix-metadata-from-hdinsight-36-to-40"></a>从 HDInsight 3.6 的 HBase Phoenix 元数据复制到 4.0 之后，Phoenix Sqlline 停止工作

请执行以下步骤：

1. 之前执行复制，请转到目标 4.0 群集和执行`sqlline.py`。 此命令将生成如 Phoenix 表`SYSTEM.MUTEX`和`SYSTEM.LOG`，只存在于 4.0。
1. 删除以下表：
    1. `SYSTEM.FUNCTION`
    1. `SYSTEM.SEQUENCE`
    1. `SYSTEM.STATS`
    1. `SYSTEM.CATALOG`
1. 启动 HBase 复制

## <a name="deprecation"></a>弃用

Apache Storm 和机器学习服务并不是在 HDInsight 4.0 中提供。
