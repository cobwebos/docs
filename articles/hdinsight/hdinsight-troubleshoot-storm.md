---
title: "STORM 故障排除 - Azure HDInsight | Microsoft Docs"
description: "使用 Storm 常见问题解答，获取有关 Azure HDInsight 平台上 Storm 的常见问题解答。"
keywords: "Azure HDInsight, Storm, 常见问题解答, 故障排除指南, 常见问题"
services: Azure HDInsight
documentationcenter: na
author: raviperi
manager: 
editor: 
ms.assetid: 74E51183-3EF4-4C67-AA60-6E12FAC999B5
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/7/2017
ms.author: raviperi
ms.translationtype: HT
ms.sourcegitcommit: f76de4efe3d4328a37f86f986287092c808ea537
ms.openlocfilehash: b52462096ce977b94ff9514df650607b05e17030
ms.contentlocale: zh-cn
ms.lasthandoff: 07/11/2017

---

# <a name="storm-troubleshooting"></a>STORM 故障排除

本文介绍处理 Apache Ambari 中的 Storm 有效负载时遇到的最常见问题及其解决方法。

## <a name="how-do-i-access-storm-ui-on-a-cluster"></a>如何在群集上访问 Storm UI

### <a name="issue"></a>问题：
可通过两种方法从浏览器访问 Storm UI：

#### <a name="ambari-ui"></a>Ambari UI
1. 导航到 Ambari 仪表板
1. 在左侧的服务列表中选择“Storm”
1. 从“快速链接”下拉列表菜单中选择“Storm UI”选项

#### <a name="direct-link"></a>直接链接
可通过 URL 访问 Storm UI：

https://\<ClusterDnsName\>/stormui

示例：https://stormcluster.azurehdinsight.net/stormui

## <a name="how-do-i-transfer-storm-eventhub-spout-checkpoint-information-from-one-topology-to-another"></a>如何将 Storm 事件中心 Spout 检查点信息从一个拓扑传输到另一个拓扑

### <a name="issue"></a>问题：
开发可使用 HDInsight 的 Storm 事件中心 Spout jar 从事件中心读取数据的拓扑时，如何在新群集上部署同名的拓扑，同时保持向旧群集中的 Zookeeeper 提交检查点数据？

#### <a name="where-is-checkpoint-data-stored"></a>检查点数据的存储位置
事件中心 Spout 将偏移检查点数据存储在 Zookeeper 中的两个根路径下：
- 非事务 Spout 检查点存储在 /eventhubspout 下
- 事务 Spout 检查点数据存储在 /transactional 下

#### <a name="how-to-restore"></a>如何还原
在 https://github.com/hdinsight/hdinsight-storm-examples/tree/master/tools/zkdatatool-1.0 上可以找到用于将数据导出 Zookeeper 并使用新名称将其导入回来的脚本

lib 文件夹中有一些 Jar 文件，其中包含导入/导出操作的实现。
bash 文件夹包含一个示例脚本，说明如何从旧群集上的 Zookeeper 服务器导出数据，以及将数据导入回到新群集上的 Zookeeper 服务器。

需要在 Zookeeper 节点中运行 [stormmeta.sh](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/tools/zkdatatool-1.0/bash/stormmeta.sh) 脚本才能导入/导出数据。
需要更新该脚本，以更正其中的 HDP 版本字符串。
（HDInsight 正在努力使这些脚本通用化，以便可以通过群集中的任何节点运行这些脚本，而无需最终用户进行修改）。

导出命令会将元数据写入指定位置中的 HDFS 路径（BLOB 或 ADLS 存储）。

### <a name="examples"></a>示例

##### <a name="export-offset-metadata"></a>导出偏移元数据：
1. 通过 SSH 连接到旧群集上需要从中导出检查点偏移数据的 Zookeeper 群集。
1. （更新 hdp 版本字符串之后）运行以下命令，将 Zookeeper 偏移数据导出到 /stormmetadta/zkdata HDFS 路径。

```apache   
   java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter export /eventhubspout /stormmetadata/zkdata
```

##### <a name="import-offset-metadata"></a>导入偏移元数据
1. 通过 SSH 连接到旧群集上需要从中导出检查点偏移数据的 Zookeeper 群集。
1. （更新 hdp 版本字符串之后）运行以下命令，将 Zookeeper 偏移数据从 HDFS 路径 /stormmetadata/zkdata 导入到目标群集上的 Zookeeper 服务器。

```apache
   java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter import /eventhubspout /home/sshadmin/zkdata
```
   
##### <a name="delete-offset-metadata-so-topologies-can-start-processing-data-from-either-beginning-or-timestamp-of-user-choice"></a>删除偏移元数据，使拓扑能够从头开始或者用户所选的时间戳开始处理数据
1. 通过 SSH 连接到旧群集上需要从中导出检查点偏移数据的 Zookeeper 群集。
1. （更新 hdp 版本字符串之后）运行以下命令，删除当前群集的所有 Zookeeper 偏移数据。

```apache
   java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter delete /eventhubspout
```

## <a name="how-do-i-locate-storm-binaries-on-a-cluster"></a>如何在群集上查找 Storm 二进制文件

### <a name="issue"></a>问题：
 了解 HDInsight 群集上 Storm 服务的二进制文件的位置

### <a name="resolution-steps"></a>解决步骤：

可以在 /usr/hdp/current/storm-client 中找到当前 HDP 堆栈的 Storm 二进制文件

在头节点和工作节点上，此位置是相同的。
 
/usr/hdp 下面可能有多个 HDP 版本特定的二进制文件（示例：/usr/hdp/2.5.0.1233/storm）

但是，/usr/hdp/current/storm-client 与群集上运行的最新版本建立了符号链接。

### <a name="further-reading"></a>延伸阅读：
 [使用 SSH 连接到 HDInsight 群集](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) [Storm](http://storm.apache.org/)
 
## <a name="how-do-i-determine-the-deployment-topology-of-a-storm-cluster"></a>如何确定 Storm 群集的部署拓扑
 
### <a name="issue"></a>问题：
 
识别连同 HDInsight Storm 一起安装的所有组件。
 
Storm 群集由 4 个节点类别构成
1. 网关
1. 头节点
1. Zookeeper 节点
1. 辅助角色节点
 
#### <a name="gateway-nodes"></a>网关节点
是一个网关和反向代理服务，可用于公开访问活动的 Ambari 管理服务，以及处理 Ambari 群首选举。
 
#### <a name="zookeeper-nodes"></a>Zookeeper 节点
HDInsight 附带一个 3 节点 Zookeeper 仲裁。
仲裁大小是固定的，不可配置。
 
群集中的 Storm 服务自动配置为使用 ZK 仲裁。
 
#### <a name="head-nodes"></a>头节点
Storm 头节点运行以下服务：
1. Nimbus
1. Ambari 服务器
1. Ambari 指标服务器
1. Ambari 指标收集器
 
#### <a name="worker-nodes"></a>工作节点
 Storm 工作节点运行以下服务：
1. 监督器
1. 用于运行拓扑的辅助角色 JVM
1. Ambari 代理
 
## <a name="how-do-i-locate-storm-eventhub-spout-binaries-for-development"></a>如何查找用于开发的 Storm 事件中心 Spout 二进制文件
 
### <a name="issue"></a>问题：
如何找到有关在拓扑中使用 Storm 事件中心 Spout jar 的详细信息。
 
#### <a name="msdn-articles-on-how-to"></a>有关操作说明的 MSDN 文章
 
##### <a name="java-based-topology"></a>基于 Java 的拓扑
https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-storm-develop-java-event-hub-topology
 
##### <a name="c-based-topology-using-mono-on-hdi-34-linux-storm-clusters"></a>基于 C# 的拓扑（在 HDI 3.4+ Linux Storm 群集上使用 Mono）
https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-storm-develop-csharp-event-hub-topology
 
##### <a name="latest-storm-eventhub-spout-binaries-for-hdi35-linux-storm-clusters"></a>HDI3.5+ Linux Storm 群集的最新 Storm 事件中心 Spout 二进制文件
查看 https://github.com/hdinsight/mvn-repo/blob/master/README.md，了解如何使用适用于 HDI3.5+ Linux Storm 群集的最新 Storm 事件中心 Spout。
 
##### <a name="source-code-examples"></a>源代码示例：
https://github.com/Azure-Samples/hdinsight-java-storm-eventhub
 
## <a name="how-do-i-locate-storm-log4j-configuration-files-on-clusters"></a>如何在群集上查找 Storm Log4J 配置文件
 
### <a name="issue"></a>问题：
 
识别 Storm 服务的 Log4J 配置文件。
 
#### <a name="on-headnodes"></a>在头节点上：
从 /usr/hdp/<HDPVersion>/storm/log4j2/cluster.xml 读取 Nimbus Log4J 配置
 
#### <a name="worker-nodes"></a>工作节点
从 /usr/hdp/<HDPVersion>/storm/log4j2/cluster.xml 读取监督器的 Log4J 配置
 
从 /usr/hdp/<HDPVersion>/storm/log4j2/worker.xml 读取工作节点的 Log4J 配置文件
 
示例：/usr/hdp/2.6.0.2-76/storm/log4j2/cluster.xml /usr/hdp/2.6.0.2-76/storm/log4j2/worker.xml







