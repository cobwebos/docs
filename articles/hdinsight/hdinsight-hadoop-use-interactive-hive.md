---
title: "在 HDInsight 中使用交互式 Hive | Microsoft Docs"
description: "了解如何在 HDInsight 中使用交互式 Hive（LLAP 上的 Hive）。"
keywords: 
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 0957643c-4936-48a3-84a3-5dc83db4ab1a
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2017
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 6971e123b388eab689e02a5c67809509c2ad29c6


---
# <a name="use-interactive-hive-in-hdinsight-preview"></a>在 HDInsight（预览版）中使用交互式 Hive
交互式 Hive（也称为 [Live Long and Process](https://cwiki.apache.org/confluence/display/Hive/LLAP)）是一个新的 HDInsight [群集类型](hdinsight-hadoop-provision-linux-clusters.md#cluster-types)。  交互式 Hive 允许在内存中缓存，这使 Hive 查询更具交互性且速度更快。 这个新功能使 HDInsight 成为世界上性能最高和最具灵活性的工具之一，可通过内存中缓存（使用 Hive 和 Spark）打开云上的大数据解决方案，并通过与 R 服务的深度集成进行高级分析。 

交互式 Hive 群集与 Hadoop 群集有所不同。 交互式 Hive 群集只包含 Hive 服务。 

> [!NOTE]
> MapReduce、Pig、Sqoop、Oozie，以及其他服务很快将从此群集类型中删除。
> 只能通过 Ambari Hive 视图、Beeline 和 Hive ODBC 访问交互式 Hive 群集中的 Hive 服务。 不能通过 Hive 控制台、Templeton、Azure CLI 和 Azure PowerShell 访问。 
> 
> 

## <a name="create-an-interactive-hive-cluster"></a>创建交互式 Hive 群集
交互式 Hive 群集仅受基于 Linux 的群集支持。 有关创建 HDInsight 群集的信息，请参阅[在 HDInsight 中创建基于 Linux 的 Hadoop 群集](hdinsight-hadoop-provision-linux-clusters.md)。

## <a name="execute-hive-from-interactive-hive"></a>从交互式 Hive 中执行 Hive
可选择不同的方式执行 Hive 查询：

* 使用 Ambari Hive 视图运行 Hive
  
    有关使用 Hive 视图的信息，请参阅[在 HDInsight 中将 Hive 与 Hadoop 配合使用](hdinsight-hadoop-use-hive-ambari-view.md)。
* 使用 Beeline 运行 Hive
  
    有关在 HDInsight 上使用 Beeline 的信息，请参阅[通过 Beeline 在 HDInsight 中将 Hive 与 Hadoop 配合使用](hdinsight-hadoop-use-hive-beeline.md)。
  
    使用来自头结点或空边缘节点的 Beeline。  建议使用来自空边缘节点的 Beeline。  有关使用空边缘节点创建 HDInsight 群集的信息，请参阅[在 HDInsight 中使用空边缘节点](hdinsight-apps-use-edge-node.md)。
* 使用 Hive ODBC 运行 Hive
  
    有关使用 Hive ODBC 的信息，请参阅[使用 Microsoft Hive ODBC 驱动程序将 Excel 连接到 Hadoop](hdinsight-connect-excel-hive-odbc-driver.md)。

**查找 JDBC 连接字符串：**

1. 使用以下 URL 登录到 Ambari：https://<ClusterName>.AzureHDInsight.net。
2. 在左侧菜单中，单击“Hive”。
3. 单击突出显示的图标以复制 URL：
   
   ![HDInsight Hadoop 交互式 Hive LLAP JDBC](./media/hdinsight-hadoop-use-interactive-hive/hdinsight-hadoop-use-interactive-hive-jdbc.png)

## <a name="see-also"></a>另请参阅
* [在 HDInsight 中创建基于 Linux 的 Hadoop 群集](hdinsight-hadoop-provision-linux-clusters.md)：了解如何在 HDInsight 中创建交互式 Hive 群集。
* [通过 Beeline 在 HDInsight 中将 Hive 与 Hadoop 配合使用](hdinsight-hadoop-use-hive-beeline.md)：了解如何使用 Beeline 提交 Hive 查询。
* [使用 Microsoft Hive ODBC 驱动程序将 Excel 连接到 Hadoop](hdinsight-connect-excel-hive-odbc-driver.md)：了解如何将 Excel 连接到 Hadoop。




<!--HONumber=Nov16_HO3-->


