---
title: "HIVE 故障排除 - Azure HDInsight | Microsoft Docs"
description: "使用 Hive 常见问题解答，获取有关 Azure HDInsight 平台上 Hive 的常见问题解答。"
keywords: "Azure HDInsight, Hive, 常见问题解答, 故障排除指南, 常见问题"
services: Azure HDInsight
documentationcenter: na
author: dharmeshkakadia
manager: 
editor: 
ms.assetid: 15B8D0F3-F2D3-4746-BDCB-C72944AA9252
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/7/2017
ms.author: dharmeshkakadia
ms.translationtype: HT
ms.sourcegitcommit: f76de4efe3d4328a37f86f986287092c808ea537
ms.openlocfilehash: 67b096c0585f7b73a57b784683944052d1cfaa33
ms.contentlocale: zh-cn
ms.lasthandoff: 07/11/2017


---

# <a name="hive-troubleshooting"></a>HIVE 故障排除

本文介绍处理 Apache Ambari 中的 HIVE 有效负载时遇到的最常见问题及其解决方法。

## <a name="how-do-i-export-a-hive-metastore-and-import-it-on-another-cluster"></a>如何导出 Hive 元存储并将其导入另一个群集

### <a name="issue"></a>问题：

需要导出 Hive 元存储并将其导入另一个 HDInsight 群集。  

### <a name="resolution-steps"></a>解决步骤： 

1. 使用安全外壳 (SSH) 客户端连接到 HDInsight 群集（请查看下面的“延伸阅读”部分）。
1. 在要从中导出元存储的 HDInsight 群集上运行以下命令：

```apache
for d in `hive -e "show databases"`; do echo "create database $d; use $d;" >> alltables.sql ; for t in `hive --database $d -e "show tables"` ; do ddl=`hive --database $d -e "show create table $t"`; echo "$ddl ;" >> alltables.sql ; echo "$ddl" | grep -q "PARTITIONED\s*BY" && echo "MSCK REPAIR TABLE $t ;" >> alltables.sql ; done; done
```

此命令会生成名为 `allatables.sql` 的文件。

- 将 `alltables.sql` 文件复制到新的 HDInsight 群集，运行以下命令：

```apache
hive -f alltables.sql
```

此代码假设新群集上的数据路径与旧群集上的相同。 如果不同，可以手动编辑生成的  
`alltables.sql` 文件以反映任何更改。

### <a name="further-reading"></a>延伸阅读：

- [使用 SSH 连接到 HDInsight 群集](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix)


## <a name="how-do-locate-hive-logs-on-a-cluster"></a>如何在群集上查找 Hive 日志

### <a name="issue"></a>问题：

需要在 HDInsight 群集上查找 Hive 客户端、元存储和 hiveserver 的日志。  

### <a name="resolution-steps"></a>解决步骤： 

- 使用安全外壳 (SSH) 客户端连接到 HDInsight 群集（请查看下面的“延伸阅读”部分）。
- 可在以下位置找到 Hive 客户端日志：

```apache
/tmp/<username>/hive.log 
```

- 可在以下位置找到 Hive 元存储日志：

```apache
/var/log/hive/hivemetastore.log 
```

- 可在以下位置找到 Hiveserver 日志：

```apache
/var/log/hive/hiveserver2.log 
```

### <a name="further-reading"></a>延伸阅读：

- [使用 SSH 连接到 HDInsight 群集](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix)

## <a name="how-do-i-launch-hive-shell-with-specific-configurations-on-a-cluster"></a>如何在群集上使用特定的配置启动 Hive shell

### <a name="issue"></a>问题：

启动时需要在 HDInsight 群集上替代或指定 Hive shell 配置。  

### <a name="resolution-steps"></a>解决步骤： 

- 启动 Hive shell 时指定配置键值对（请查看下面的“延伸阅读”部分）：

```apache
hive -hiveconf a=b 
```

- 使用以下命令在 Hive shell 中列出所有有效配置：

```apache
hive> set;
```

例如，在控制台上使用以下命令启动已启用调试日志记录的 Hive shell：
             
```apache
hive -hiveconf hive.root.logger=ALL,console 
```

### <a name="further-reading"></a>延伸阅读：

- [Hive 配置属性](https://cwiki.apache.org/confluence/display/Hive/Configuration+Properties)


## <a name="how-do-i-analyze-tez-dag-data-on-a-cluster-critical-path"></a>如何在群集关键路径上分析 Tez DAG 数据

### <a name="issue"></a>问题：

需要分析 Tez 有向无环图 (DAG) 信息，尤其是 HDInsight 群集中关键路径上的信息

### <a name="resolution-steps"></a>解决步骤：
 
- 使用安全外壳 (SSH) 客户端连接到 HDInsight 群集（请查看下面的“延伸阅读”部分）。

- 在命令提示符下运行以下命令：
   
```apache
hadoop jar /usr/hdp/current/tez-client/tez-job-analyzer-*.jar CriticalPath --saveResults --dagId <DagId> --eventFileName <DagData.zip> 
```

- 使用以下命令列出可用于分析 Tez DAG 的其他分析程序：

```apache
hadoop jar /usr/hdp/current/tez-client/tez-job-analyzer-*.jar
```

必须指定一个示例程序作为第一个参数。

有效的程序名称为：ContainerReuseAnalyzer：列显 DAG 中的容器重用详细信息；CriticalPath：查找 DAG 的关键路径；LocalityAnalyzer：列显 DAG 中的区域详细信息；ShuffleTimeAnalyzer：分析 DAG 中的随机排列时间详细信息；SkewAnalyzer：分析 DAG 中的偏斜详细信息；SlowNodeAnalyzer：列显 DAG 中的节点详细信息；SlowTaskIdentifier：列显 DAG 中的慢速任务详细信息；SlowestVertexAnalyzer：列显 DAG 中的最慢顶点详细信息；SpillAnalyzer：列显 DAG 中的溢出详细信息；TaskConcurrencyAnalyzer：列显 DAG 中的任务并发性详细信息；VertexLevelCriticalPathAnalyzer：查找 DAG 中顶点级别的关键路径


### <a name="further-reading"></a>延伸阅读：

- [使用 SSH 连接到 HDInsight 群集](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix)



## <a name="how-do-i-download-tez-dag-data-from-a-cluster"></a>如何从群集下载 Tez DAG 数据

#### <a name="issue"></a>问题：

需要从 HDInsight 群集下载 Tez 有向无环图 (DAG) 信息。

#### <a name="resolution-steps"></a>解决步骤：

可使用两种方法收集 Tez DAG 数据。

- 通过命令行：
 
    使用安全外壳 (SSH) 客户端连接到 HDInsight 群集。 在命令提示符下运行以下命令：
   
```apache
hadoop jar /usr/hdp/current/tez-client/tez-history-parser-*.jar org.apache.tez.history.ATSImportTool -downloadDir . -dagId <DagId> 
```

- 或者，可以使用 Ambari Tez 视图：
   
转到 Ambari --> 转到 Tez 视图（隐藏在右上角的磁贴图标下面）--> 单击所需的 DAG --> 单击“下载数据”。

#### <a name="further-reading"></a>延伸阅读：

1) [使用 SSH 连接到 HDInsight 群集](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix)








