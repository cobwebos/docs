---
title: "使用 Azure HDInsight 对 Storm 进行故障排除 | Microsoft Docs"
description: "获取有关在 Azure HDInsight 中使用 Apache Storm 时遇到的常见问题的解答。"
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
ms.date: 11/2/2017
ms.author: raviperi
ms.openlocfilehash: 399ed17e997baf5dcf484f7798d3c4679522c633
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2017
---
# <a name="troubleshoot-storm-by-using-azure-hdinsight"></a>使用 Azure HDInsight 对 Storm 进行故障排除

了解处理 Apache Ambari 中的 Apache Storm 有效负载时的最常见问题及其解决方法。

## <a name="how-do-i-access-the-storm-ui-on-a-cluster"></a>如何在群集上访问 Storm UI？
可以使用两个选项从浏览器访问 Storm UI：

### <a name="ambari-ui"></a>Ambari UI
1. 转到 Ambari 仪表板。
2. 在服务列表中，选择“Storm”。
3. 在“快速链接”菜单中，选择“Storm UI”。

### <a name="direct-link"></a>直接链接
可通过以下 URL 访问 Storm UI：

https://\<群集 DNS 名称\>/stormui

示例：

 https://stormcluster.azurehdinsight.net/stormui

## <a name="how-do-i-transfer-storm-event-hub-spout-checkpoint-information-from-one-topology-to-another"></a>如何将 Storm 事件中心 Spout 检查点信息从一个拓扑传输到另一个拓扑？

开发可使用 HDInsight Storm 事件中心 Spout .jar 文件从 Azure 事件中心读取数据的拓扑时，必须在新群集上部署同名的拓扑。 但是，必须在旧群集上保留已提交到 Apache ZooKeeper 的检查点数据。

### <a name="where-checkpoint-data-is-stored"></a>检查点数据的存储位置
事件中心 Spout 将偏移检查点数据存储在 ZooKeeper 中的两个根路径下：
- 非事务 Spout 检查点存储在 /eventhubspout 中。
- 事务 Spout 检查点数据存储在 /transactional 中。

### <a name="how-to-restore"></a>如何还原
若要获取可用于将数据导出 ZooKeeper 并使用新名称将其导入回到 ZooKeeper 的脚本和库，请参阅 [HDInsight Storm 示例](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/tools/zkdatatool-1.0)。

lib 文件夹中有一些 .Jar 文件，其中包含导出/导入操作的实现。 bash 文件夹包含一个示例脚本，该脚本演示如何从旧群集上的 ZooKeeper 服务器导出数据，并将数据导入回到新群集上的 ZooKeeper 服务器。

在 ZooKeeper 节点中运行 [stormmeta.sh](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/tools/zkdatatool-1.0/bash/stormmeta.sh) 脚本即可导出再导入数据。 需将该脚本更新为正确的 Hortonworks 数据平台 (HDP) 版本。 （我们正努力使这些脚本在 HDInsight 中通用化。 可以通过群集上的任何节点运行通用脚本，而无需用户进行修改。）

导出命令会将元数据写入所设置位置中的 Apache Hadoop 分布式文件系统 (HDFS) 路径（在 Azure Blob 存储或 Azure Data Lake Store 存储中）。

### <a name="examples"></a>示例

#### <a name="export-offset-metadata"></a>导出偏移元数据
1. 使用 SSH 在需要从中导出检查点偏移数据的群集上转到 ZooKeeper 群集。
2. （更新 HDP 版本字符串之后）运行以下命令，将 ZooKeeper 偏移数据导出到 /stormmetadta/zkdata HDFS 路径：

    ```apache   
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter export /eventhubspout /stormmetadata/zkdata
    ```

#### <a name="import-offset-metadata"></a>导入偏移元数据
1. 使用 SSH 在需要从中导出检查点偏移数据的群集上转到 ZooKeeper 群集。
2. （更新 HDP 版本字符串之后）运行以下命令，将 ZooKeeper 偏移数据从 HDFS 路径 /stormmetadata/zkdata 导入到目标群集上的 ZooKeeper 服务器：

    ```apache
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter import /eventhubspout /home/sshadmin/zkdata
    ```
   
#### <a name="delete-offset-metadata-so-that-topologies-can-start-processing-data-from-the-beginning-or-from-a-timestamp-that-the-user-chooses"></a>删除偏移元数据，使拓扑能够从头开始或者从用户所选的时间戳开始处理数据
1. 使用 SSH 在需要从中导出检查点偏移数据的群集上转到 ZooKeeper 群集。
2. （更新 HDP 版本字符串之后）运行以下命令，删除当前群集中的所有 ZooKeeper 偏移数据：

    ```apache
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter delete /eventhubspout
    ```

## <a name="how-do-i-locate-storm-binaries-on-a-cluster"></a>如何在群集上查找 Storm 二进制文件？
当前 HDP 堆栈的 Storm 二进制文件在 /usr/hdp/current/storm-client 中。 在头节点和工作节点上，此位置是相同的。
 
/usr/hdp 中可能包含特定 HDP 版本的多个二进制文件（例如 /usr/hdp/2.5.0.1233/storm）。 /usr/hdp/current/storm-client 文件与群集上运行的最新版本建立了符号链接。

有关详细信息，请参阅[使用 SSH 连接到 HDInsight 群集](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix)和 [Storm](http://storm.apache.org/)。
 
## <a name="how-do-i-determine-the-deployment-topology-of-a-storm-cluster"></a>如何确定 Storm 群集的部署拓扑？
首先，请识别连同 HDInsight Storm 一起安装的所有组件。 Storm 群集由四个节点类别组成：

* 网关节点
* 头节点
* ZooKeeper 节点
* 辅助角色节点
 
### <a name="gateway-nodes"></a>网关节点
网关节点是一个网关和反向代理服务，可用于公开访问活动的 Ambari 管理服务。 它还处理 Ambari 群首选举。
 
### <a name="head-nodes"></a>头节点
Storm 头节点运行以下服务：
* Nimbus
* Ambari 服务器
* Ambari 指标服务器
* Ambari 指标收集器
 
### <a name="zookeeper-nodes"></a>ZooKeeper 节点
HDInsight 附带一个三节点 ZooKeeper 仲裁。 仲裁大小是固定的，不可重新配置。
 
群集中的 Storm 服务配置为自动使用 ZooKeeper 仲裁。
 
### <a name="worker-nodes"></a>辅助角色节点
Storm 工作节点运行以下服务：
* 监督器
* 用于运行拓扑的辅助角色 Java 虚拟机 (JVM)
* Ambari 代理
 
## <a name="how-do-i-locate-storm-event-hub-spout-binaries-for-development"></a>如何查找用于开发的 Storm 事件中心 Spout 二进制文件？
 
有关在拓扑中使用 Storm 事件中心 Spout .jar 文件的详细信息，请参阅以下资源。
 
### <a name="java-based-topology"></a>基于 Java 的拓扑
[使用 Storm on HDInsight 从 Azure 事件中心处理事件 (Java)](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-storm-develop-java-event-hub-topology)
 
### <a name="c-based-topology-mono-on-hdinsight-34-linux-storm-clusters"></a>基于 C# 的拓扑（HDInsight 3.4+ Linux Storm 群集上的 Mono）
[使用 Storm on HDInsight 从 Azure 事件中心处理事件 (C#)](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-storm-develop-csharp-event-hub-topology)
 
### <a name="latest-storm-event-hub-spout-binaries-for-hdinsight-35-linux-storm-clusters"></a>HDInsight 3.5+ Linux Storm 群集的最新 Storm 事件中心 Spout 二进制文件
若要了解如何使用适用于 HDInsight 3.5+ Linux Storm 群集的最新 Storm 事件中心 Spout，请参阅 mvn-repo [自述文件](https://github.com/hdinsight/mvn-repo/blob/master/README.md)。
 
### <a name="source-code-examples"></a>源代码示例
参阅有关如何在 Azure HDInsight 群集上使用 Apache Storm 拓扑（以 Java 编写）从 Azure 事件中心读取和写入数据的[示例](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub)。
 
## <a name="how-do-i-locate-storm-log4j-configuration-files-on-clusters"></a>如何在群集上查找 Storm Log4J 配置文件？
 
识别 Storm 服务的 Apache Log4J 配置文件。
 
### <a name="on-head-nodes"></a>在头节点上
从 /usr/hdp/\<HDP version\>/storm/log4j2/cluster.xml 读取 Nimbus Log4J 配置。
 
### <a name="on-worker-nodes"></a>在工作节点上
从 /usr/hdp/\<HDP version\>/storm/log4j2/cluster.xml 读取监督器 Log4J 配置。
 
从 /usr/hdp/\<HDP version\>/storm/log4j2/worker.xml 读取工作节点 Log4J 配置文件。
 
示例：/usr/hdp/2.6.0.2-76/storm/log4j2/cluster.xml /usr/hdp/2.6.0.2-76/storm/log4j2/worker.xml

### <a name="see-also"></a>另请参阅
[使用 Azure HDInsight 进行故障排除](../../hdinsight/hdinsight-troubleshoot-guide.md)