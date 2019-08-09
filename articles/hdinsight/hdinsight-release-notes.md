---
title: Azure HDInsight 发行说明
description: Azure HDInsight 的最新发行说明。 获取 Hadoop、Spark、R Server、Hive 和更多工具的开发技巧和详细信息。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/08/2019
ms.openlocfilehash: b6e9a340a1fdcbe3ee24b8c81d171ade04c63139
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/09/2019
ms.locfileid: "68880037"
---
# <a name="release-notes-for-azure-hdinsight"></a>Azure HDInsight 发行说明

本文提供有关**最新** Azure HDInsight 版本更新的信息。 有关较早版本的信息，请参阅 [HDInsight 发行说明存档](hdinsight-release-notes-archive.md)。

> [!IMPORTANT]  
> Linux 是 HDInsight 3.4 或更高版本上使用的唯一操作系统。 有关详细信息，请参阅 [HDInsight 版本控制文章](hdinsight-component-versioning.md)。

## <a name="summary"></a>总结

Azure HDInsight 是 Azure 中最受企业客户青睐的开源 Apache Hadoop 和 Apache Spark 分析服务之一。

## <a name="new-features"></a>新增功能

有关 HDInsight 4.0 的重要更改的详细信息。，请参阅 [HDI 4.0 中有哪些新增功能？](../hdinsight/hdinsight-version-release.md)。

## <a name="component-versions"></a>组件版本

下面提供了所有 HDInsight 4.0 组件的正式 Apache 版本。 列出的组件是可用的最新稳定版本。

- Apache Ambari 2.7.1
- Apache Hadoop 3.1.1
- Apache HBase 2.0.0
- Apache Hive 3.1.0
- Apache Kafka 1.1.1, 2.1。0
- Apache Mahout 0.9.0+
- Apache Oozie 4.2.0
- Apache Phoenix 4.7.0
- Apache Pig 0.16.0
- Apache Ranger 0.7.0
- Apache Slider 0.92.0
- Apache Spark 2.3.1, 2.4。0
- Apache Sqoop 1.4.7
- Apache TEZ 0.9.1
- Apache Zeppelin 0.8.0
- Apache ZooKeeper 3.4.6

除了上面所列的版本以外，Apache 组件的较高版本有时也会捆绑在 HDP 分发版中。 在这种情况下，这些较高版本会列在“技术预览”表中，并且在生产环境中不应替换为上述列表中的 Apache 组件版本。

## <a name="apache-patch-information"></a>Apache 修补程序信息

有关 HDInsight 4.0 中可用的修补程序的详细信息，请参阅下表中适用于每个产品的修补程序列表。

| 产品名称 | 修补程序信息 |
|---|---|
| Ambari | [Ambari 修补程序信息](https://docs.hortonworks.com/HDPDocuments/Ambari-2.7.1.0/bk_ambari-release-notes/content/ambari_relnotes-2.7.1.0-patch-information.html) |
| Hadoop | [Hadoop 修补程序信息](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_hadoop.html) |
| Hbase | [HBase 修补程序信息](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_hbase.html) |
| Hive  | 此版本提供 Hive 3.1.0，但不提供其他 Apache 修补程序。  |
| Kafka | 此版本提供 Kafka 1.1.1，但不提供其他 Apache 修补程序。 |
| Oozie | [Oozie 修补程序信息](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_oozie.html) |
| Phoenix | [Phoenix 修补程序信息](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_phoenix.html) |
| Pig | [Pig 修补程序信息](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_pig.html) |
| Ranger | [Ranger 修补程序信息](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_ranger.html) |
| Spark | [Spark 修补程序信息](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_spark.html) |
| Sqoop | 此版本提供 Sqoop 1.4.7，但不提供其他 Apache 修补程序。 |
| Tez | 此版本提供 Tez 0.9.1，但不提供其他 Apache 修补程序。 |
| Zeppelin | 此版本提供 Zeppelin 0.8.0，但不提供其他 Apache 修补程序。 |
| Zookeeper | [Zookeeper 修补程序信息](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_zookeeper.html) |

## <a name="fixed-common-vulnerabilities-and-exposures"></a>修复了常见漏洞和透露

有关此版本中已解决的安全问题的详细信息，请参阅 Hortonworks 编写的[修复了 HDP 3.0.1 的常见漏洞和透露](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/cve.html)。

## <a name="known-issues"></a>已知问题

### <a name="replication-is-broken-for-secure-hbase-with-default-installation"></a>使用默认安装选项时 Secure HBase 的复制中断

对于 HDInsight 4.0，请执行以下步骤：

1. 启用群集间通信。
1. 登录到活动的头节点。
1. 使用以下命令下载一个脚本以启用复制：

    ```
    sudo wget https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_enable_replication.sh
    ```
1. 键入命令 `sudo kinit <domainuser>`。
1. 键入以下命令以运行该脚本：

    ```
    sudo bash hdi_enable_replication.sh -m <hn0> -s <srclusterdns> -d <dstclusterdns> -sp <srcclusterpasswd> -dp <dstclusterpasswd> -copydata
    ```
对于 HDInsight 3.6，请执行以下步骤：

1. 登录到活动的 HMaster ZK。
1. 使用以下命令下载一个脚本以启用复制：
    ```
    sudo wget https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_enable_replication.sh
    ```
1. 键入命令 `sudo kinit -k -t /etc/security/keytabs/hbase.service.keytab hbase/<FQDN>@<DOMAIN>`。
1. 输入以下命令：

    ```bash
    sudo bash hdi_enable_replication.sh -s <srclusterdns> -d <dstclusterdns> -sp <srcclusterpasswd> -dp <dstclusterpasswd> -copydata
    ```

### <a name="phoenix-sqlline-stops-working-after-migrating-hbase-cluster-to-hdinsight-40"></a>将 HBase 群集迁移到 HDInsight 4.0 后，Phoenix Sqlline 停止工作

请执行以下步骤：

1. 删除以下 Phoenix 表：
    1. `SYSTEM.FUNCTION`
    1. `SYSTEM.SEQUENCE`
    1. `SYSTEM.STATS`
    1. `SYSTEM.MUTEX`
    1. `SYSTEM.CATALOG`
1. 如果无法删除其中的任何表，请重启 HBase 以清除与表建立的任何连接。
1. 再次运行 `sqlline.py`。 Phoenix 将重新创建在步骤 1 中删除的所有表。
1. 重新生成 HBase 数据的 Phoenix 表和视图。

### <a name="phoenix-sqlline-stops-working-after-replicating-hbase-phoenix-metadata-from-hdinsight-36-to-40"></a>将 HBase Phoenix 元数据从 HDInsight 3.6 复制到 4.0 之后，Phoenix Sqlline 停止工作

请执行以下步骤：

1. 在执行复制之前，请转到目标 4.0 群集并执行 `sqlline.py`。 此命令将生成类似于 `SYSTEM.MUTEX` 和 `SYSTEM.LOG` 且只存在于 4.0 中的 Phoenix 表。
1. 删除以下表：
    1. `SYSTEM.FUNCTION`
    1. `SYSTEM.SEQUENCE`
    1. `SYSTEM.STATS`
    1. `SYSTEM.CATALOG`
1. 启动 HBase 复制

## <a name="deprecation"></a>弃用

Apache Storm 和机器学习服务在 HDInsight 4.0 中不可用。
