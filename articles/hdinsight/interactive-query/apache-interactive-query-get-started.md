---
title: "通过 Azure HDInsight 使用交互式查询 | Microsoft Docs"
description: "了解如何通过 HDInsight 使用交互式查询 (Hive LLAP)。"
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
ms.date: 02/22/2018
ms.author: jgao
ms.openlocfilehash: 1e094c801ffc9bc51609d119b0e6ad80ff17a091
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/23/2018
---
# <a name="use-interactive-query-with-hdinsight"></a>将交互式查询与 HDInsight 配合使用
交互式查询（也称为 Hive LLAP 或 [Live Long and Process](https://cwiki.apache.org/confluence/display/Hive/LLAP)）是一种 Azure HDInsight [群集类型](../hdinsight-hadoop-provision-linux-clusters.md#cluster-types)。 交互式查询支持内存中缓存，可提高 Hive 查询速度和交互性。

[!INCLUDE [hdinsight-price-change](../../../includes/hdinsight-enhancements.md)] 

交互式查询群集与 Hadoop 群集有所不同。 交互式 Hive 群集只包含 Hive 服务。 

> [!NOTE]
> 仅可通过 Ambari Hive 视图、Beeline 和 Microsoft Hive 开放式数据库连接驱动程序 (Hive ODBC) 访问交互式查询群集中的 Hive 服务。 不能通过 Hive 控制台、Templeton、Azure 命令行工具 (Azure CLI) 或 Azure PowerShell 对其进行访问。 
> 
> 

## <a name="create-an-interactive-query-cluster"></a>创建交互式查询群集
有关创建 HDInsight 群集的信息，请参阅[在 HDInsight 中创建 Hadoop 群集](../hdinsight-hadoop-provision-linux-clusters.md)。 选择“交互式查询”群集类型。

## <a name="execute-hive-queries-from-interactive-query"></a>从交互式查询执行 Hive 查询
若要执行 Hive 查询，可以使用以下选项：

* 使用 Power BI

    请参阅[在 Azure HDInsight 中使用 Power BI 直观显示交互式查询 Hive 数据](./apache-hadoop-connect-hive-power-bi-directquery.md)以及[在 Azure HDInsight 中使用 Power BI 直观显示大数据](../hadoop/apache-hadoop-connect-hive-power-bi.md)。
 
* 使用 Zeppelin

    请参阅[在 Azure HDInsight 中使用 Zeppelin 运行 Hive 查询](../hdinsight-connect-hive-zeppelin.md)。

* 使用 Visual Studio

    请参阅[使用针对 Visual Studio 的 Data Lake 工具连接到 Azure HDInsight 并运行 Hive 查询](../hadoop/apache-hadoop-visual-studio-tools-get-started.md#run-interactive-hive-queries)。

* 使用 Visual Studio Code

    请参阅[将 Visual Studio Code 用于 Hive、LLAP 或 pySpark](../hdinsight-for-vscode.md)。
* 使用 Ambari Hive 视图运行 Hive。
  
    请参阅[将 Hive 视图与 Azure HDInsight 中的 Hadoop 配合使用](../hadoop/apache-hadoop-use-hive-ambari-view.md)。
* 使用 Beeline 运行 Hive。
  
    请参阅[通过 Beeline 将 Hive 与 HDInsight 中的 Hadoop 配合使用](../hadoop/apache-hadoop-use-hive-beeline.md)。
  
    可以使用来自头结点或空边缘节点的 Beeline。 建议使用来自空边缘节点的 Beeline。 有关如何使用空边缘节点创建 HDInsight 群集的信息，请参阅[在 HDInsight 中使用空边缘节点](../hdinsight-apps-use-edge-node.md)。
* 使用 Hive ODBC 运行 Hive。
  
    请参阅[使用 Microsoft Hive ODBC 驱动程序将 Excel 连接到 Hadoop](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md)。

若要查找 Java Database Connectivity (JDBC) 连接字符串：

1. 使用以下 URL 登录到 Ambari：https://\<cluster name\>.AzureHDInsight.net。
2. 在左侧菜单中，选择“Hive”。
3. 若要复制 URL，请选择剪贴板图标：
   
   ![HDInsight Hadoop 交互式查询 LLAP JDBC](./media/apache-interactive-query-get-started/hdinsight-hadoop-use-interactive-hive-jdbc.png)

## <a name="next-steps"></a>后续步骤

* 了解如何[在 HDInsight 中创建交互式查询群集](../hdinsight-hadoop-provision-linux-clusters.md)。
* 了解如何[在 Azure HDInsight 中使用 Power BI 直观显示大数据](../hadoop/apache-hadoop-connect-hive-power-bi.md)。
* 了解如何[在 Azure HDInsight 中使用 Zeppelin 运行 Hive 查询](../hdinsight-connect-hive-zeppelin.md)。
* 了解如何[使用针对 Visual Studio 的 Data Lake 工具运行 Hive 查询](../hadoop/apache-hadoop-visual-studio-tools-get-started.md#run-interactive-hive-queries)。
* 了解如何[使用用于 Visual Studio Code 的 HDInsight 工具](../hdinsight-for-vscode.md)。
* 了解如何[将 Hive 视图与 HDInsight 中的 Hadoop 配合使用](../hadoop/apache-hadoop-use-hive-ambari-view.md)
* 了解如何[使用 Beeline 在 HDInsight 中提交 Hive 查询](../hadoop/apache-hadoop-use-hive-beeline.md)。
* 了解如何[使用 Microsoft Hive ODBC 驱动程序将 Excel 连接到 Hadoop](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md)。

