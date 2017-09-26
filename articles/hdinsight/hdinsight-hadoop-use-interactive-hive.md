---
title: "通过 Azure HDInsight 使用交互式 Hive | Microsoft Docs"
description: "了解如何通过 HDInsight 使用交互式 Hive (Hive LLAP)。"
keywords: 
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 0957643c-4936-48a3-84a3-5dc83db4ab1a
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/06/2017
ms.author: jgao
ms.translationtype: HT
ms.sourcegitcommit: 190ca4b228434a7d1b30348011c39a979c22edbd
ms.openlocfilehash: 6c4e3d0e97eb9ad4500d684c7da3b19c5669994f
ms.contentlocale: zh-cn
ms.lasthandoff: 09/09/2017

---
# <a name="use-interactive-hive-with-hdinsight-preview"></a>通过 HDInsight 使用交互式 Hive（预览）
交互式 Hive（也称为 Hive LLAP 或 [Live Long and Process](https://cwiki.apache.org/confluence/display/Hive/LLAP)）是新的 Azure HDInsight [群集类型](hdinsight-hadoop-provision-linux-clusters.md#cluster-types)。 交互式 Hive 支持内存中缓存，这可提高 Hive 查询速度和交互性。 

交互式 Hive 群集与 Hadoop 群集有所不同。 交互式 Hive 群集只包含 Hive 服务。 

> [!NOTE]
> MapReduce、Pig、Sqoop、Oozie，以及其他服务很快将从此群集类型中删除。
> 仅可通过 Ambari Hive 视图、Beeline 和 Microsoft Hive 开放式数据库连接驱动程序 (Hive ODBC) 访问交互式 Hive 群集中的 Hive 服务。 不能通过 Hive 控制台、Templeton、Azure 命令行工具 (Azure CLI) 或 Azure PowerShell 对其进行访问。 
> 
> 

## <a name="create-an-interactive-hive-cluster"></a>创建交互式 Hive 群集
仅可在基于 Linux 的群集上使用交互式 Hive 群集。 有关如何创建 HDInsight 群集的信息，请参阅[在 HDInsight 中创建基于 Linux 的 Hadoop 群集](hdinsight-hadoop-provision-linux-clusters.md)。

## <a name="execute-hive-queries-from-interactive-hive"></a>从交互式 Hive 中执行 Hive 查询
若要执行 Hive 查询，可以使用以下选项：

* 使用 Ambari Hive 视图运行 Hive。
  
    有关使用 Hive 视图的信息，请参阅[在 HDInsight 中将 Hive 与 Hadoop 配合使用](hdinsight-hadoop-use-hive-ambari-view.md)。
* 使用 Beeline 运行 Hive。
  
    有关在 HDInsight 上使用 Beeline 的信息，请参阅[通过 Beeline 在 HDInsight 中将 Hive 与 Hadoop 配合使用](hdinsight-hadoop-use-hive-beeline.md)。
  
    可以使用来自头结点或空边缘节点的 Beeline。 建议使用来自空边缘节点的 Beeline。 有关如何使用空边缘节点创建 HDInsight 群集的信息，请参阅[在 HDInsight 中使用空边缘节点](hdinsight-apps-use-edge-node.md)。
* 使用 Hive ODBC 运行 Hive。
  
    有关如何使用 Hive ODBC 的信息，请参阅[使用 Microsoft Hive ODBC 驱动程序将 Excel 连接到 Hadoop](hdinsight-connect-excel-hive-odbc-driver.md)。

若要查找 Java Database Connectivity (JDBC) 连接字符串：

1. 使用以下 URL 登录到 Ambari：https://\<cluster name\>.AzureHDInsight.net。
2. 在左侧菜单中，选择“Hive”。
3. 若要复制 URL，请选择剪贴板图标：
   
   ![HDInsight Hadoop 交互式 Hive LLAP JDBC](./media/hdinsight-hadoop-use-interactive-hive/hdinsight-hadoop-use-interactive-hive-jdbc.png)

## <a name="next-steps"></a>后续步骤
* 了解如何[在 HDInsight 中创建交互式 Hive 群集](hdinsight-hadoop-provision-linux-clusters.md)。
* 了解如何[使用 Beeline 在 HDInsight 中提交 Hive 查询](hdinsight-hadoop-use-hive-beeline.md)。
* 了解如何[使用 Microsoft Hive ODBC 驱动程序将 Excel 连接到 Hadoop](hdinsight-connect-excel-hive-odbc-driver.md)。


