---
title: 使用 Azure HDInsight 对 Hive 进行故障排除
description: 获取有关使用 Apache Hive 和 Azure HDInsight 的常见问题的解答。
keywords: Azure HDInsight, Hive, 常见问题解答, 故障排除指南, 常见问题
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: troubleshooting
ms.date: 08/15/2019
ms.openlocfilehash: 9169bd9a63666238e9d6b97d86bf1e9e10312c1b
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/17/2019
ms.locfileid: "71076513"
---
# <a name="troubleshoot-apache-hive-by-using-azure-hdinsight"></a>使用 Azure HDInsight 对 Apache Hive 进行故障排除

了解处理 Apache Ambari 中的 Apache Hive 有效负载时的最常见问题及其解决方法。

## <a name="how-do-i-export-a-hive-metastore-and-import-it-on-another-cluster"></a>如何导出 Hive 元存储并将其导入另一个群集？

### <a name="resolution-steps"></a>解决步骤

1. 使用安全外壳 (SSH) 客户端连接到 HDInsight 群集。 有关详细信息，请参阅[其他阅读材料](#additional-reading-end)。

2. 在要从中导出元存储的 HDInsight 群集上运行以下命令：

    ```apache
    for d in `hive -e "show databases"`; do echo "create database $d; use $d;" >> alltables.sql ; for t in `hive --database $d -e "show tables"` ; do ddl=`hive --database $d -e "show create table $t"`; echo "$ddl ;" >> alltables.sql ; echo "$ddl" | grep -q "PARTITIONED\s*BY" && echo "MSCK REPAIR TABLE $t ;" >> alltables.sql ; done; done
    ```

   此命令会生成名为 allatables.sql 的文件。

3. 将 alltables.sql 文件复制到新的 HDInsight 群集，并运行以下命令：

    ```apache
    hive -f alltables.sql
    ```

解决方法步骤中的代码假设新群集上的数据路径与旧群集上的相同。 如果数据路径不同，则可以手动编辑生成`alltables.sql`的文件以反映任何更改。

### <a name="additional-reading"></a>其他阅读材料

- [使用 SSH 连接到 HDInsight 群集](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="how-do-i-locate-hive-logs-on-a-cluster"></a>如何在群集上查找 Hive 日志？

### <a name="resolution-steps"></a>解决步骤

1. 使用 SSH 连接到 HDInsight 群集。 有关详细信息，请参阅**其他阅读材料**。

2. 若要查看 Hive 客户端日志，请使用以下命令：

   ```apache
   /tmp/<username>/hive.log
   ```

3. 若要查看 Hive 元存储日志，请使用以下命令：

   ```apache
   /var/log/hive/hivemetastore.log
   ```

4. 若要查看 Hive 服务器日志，请使用以下命令：

   ```apache
   /var/log/hive/hiveserver2.log
   ```

### <a name="additional-reading"></a>其他阅读材料

- [使用 SSH 连接到 HDInsight 群集](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="how-do-i-launch-the-hive-shell-with-specific-configurations-on-a-cluster"></a>如何在群集上使用特定配置启动 Hive shell？

### <a name="resolution-steps"></a>解决步骤

1. 启动 Hive shell 时，请指定配置键值对。 有关详细信息，请参阅[其他阅读材料](#additional-reading-end)。

   ```apache
   hive -hiveconf a=b
   ```

2. 若要在 Hive shell 中列出所有有效配置，请使用以下命令：

   ```apache
   hive> set;
   ```

   例如，在控制台上使用以下命令启动已启用调试日志记录的 Hive shell：

   ```apache
   hive -hiveconf hive.root.logger=ALL,console
   ```

### <a name="additional-reading"></a>其他阅读材料

- [Hive 配置属性](https://cwiki.apache.org/confluence/display/Hive/Configuration+Properties)

## <a name="how-do-i-analyze-tez-dag-data-on-a-cluster-critical-path"></a>如何在群集关键路径上分析 Apache Tez DAG 数据？

### <a name="resolution-steps"></a>解决步骤

1. 若要在群集关键图中分析 Apache Tez 有向无环图 (DAG)，请使用 SSH 连接到 HDInsight 群集。 有关详细信息，请参阅[其他阅读材料](#additional-reading-end)。

2. 请在命令提示符处运行以下命令：

   ```apache
   hadoop jar /usr/hdp/current/tez-client/tez-job-analyzer-*.jar CriticalPath --saveResults --dagId <DagId> --eventFileName <DagData.zip> 
   ```

3. 若要列出可用于分析 Tez DAG 的其他分析程序，请使用以下命令：

   ```apache
   hadoop jar /usr/hdp/current/tez-client/tez-job-analyzer-*.jar
   ```

   必须提供一个示例程序作为第一个参数。

   有效的程序名称包括：
    - **ContainerReuseAnalyzer**：列显 DAG 中的容器重用详细信息
    - **CriticalPath**：查找 DAG 的关键路径
    - **LocalityAnalyzer**：列显 DAG 中的区域详细信息
    - **ShuffleTimeAnalyzer**：分析 DAG 中的随机排列时间详细信息
    - **SkewAnalyzer**：分析 DAG 中的偏斜详细信息
    - **SlowNodeAnalyzer**：列显 DAG 中的节点详细信息
    - **SlowTaskIdentifier**：列显 DAG 中的慢速任务详细信息
    - **SlowestVertexAnalyzer**：列显 DAG 中的最慢顶点详细信息
    - **SpillAnalyzer**：列显 DAG 中的溢出详细信息
    - **TaskConcurrencyAnalyzer**：列显 DAG 中的任务并发性详细信息
    - **VertexLevelCriticalPathAnalyzer**：在 DAG 中查找顶点级别的关键路径

### <a name="additional-reading"></a>其他阅读材料

- [使用 SSH 连接到 HDInsight 群集](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="how-do-i-download-tez-dag-data-from-a-cluster"></a>如何从群集下载 Tez DAG 数据？

#### <a name="resolution-steps"></a>解决步骤

可使用两种方法收集 Tez DAG 数据：

- 通过命令行：

    使用 SSH 连接到 HDInsight 群集。 在命令提示符下运行以下命令：

  ```apache
  hadoop jar /usr/hdp/current/tez-client/tez-history-parser-*.jar org.apache.tez.history.ATSImportTool -downloadDir . -dagId <DagId>
  ```

- 使用 Ambari Tez 视图：

  1. 转到 Ambari。
  2. 转到 Tez 视图（隐藏在右上角的磁贴图标下面）。
  3. 选择想要查看的 DAG。
  4. 选择“下载数据”。

### <a name="additional-reading-end"></a>其他阅读材料

[使用 SSH 连接到 HDInsight 群集](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

- 通过[Azure 社区支持](https://azure.microsoft.com/support/community/)获得 azure 专家的解答。

- [@AzureSupport](https://twitter.com/azuresupport)连接-官方 Microsoft Azure 帐户来改善客户体验。 将 Azure 社区连接到正确的资源：答案、支持和专家。

- 如果需要更多帮助，可以从 [Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择“支持”，或打开“帮助 + 支持”中心。 有关更多详细信息，请参阅[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 Microsoft Azure 订阅中包含对订阅管理和计费支持的访问权限，并且通过一个[Azure 支持计划](https://azure.microsoft.com/support/plans/)提供技术支持。
