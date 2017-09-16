---
title: "在 HDInsight 中使用 Interactive Hive - Azure | Microsoft Docs"
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
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/06/2017
ms.author: jgao
ms.translationtype: HT
ms.sourcegitcommit: eeed445631885093a8e1799a8a5e1bcc69214fe6
ms.openlocfilehash: fa8b4126865788549217d89f19627f20739f8540
ms.contentlocale: zh-cn
ms.lasthandoff: 09/07/2017

---
# <a name="use-interactive-hive-in-hdinsight-preview"></a>在 HDInsight（预览版）中使用交互式 Hive
交互式 Hive（也称为 [Live Long and Process](https://cwiki.apache.org/confluence/display/Hive/LLAP)）是一个新的 HDInsight [群集类型](hdinsight-hadoop-provision-linux-clusters.md#cluster-types)。  交互式 Hive 允许在内存中缓存，这使 Hive 查询更具交互性且速度更快。 这项新功能使 HDInsight 成为世界上性能、灵活性和开放性最高的，具有内存中缓存（使用 Hive 和 Spark）和高级分析功能（通过与 R 服务的深度集成）的云端大数据解决方案之一。 

交互式 Hive 群集与 Hadoop 群集有所不同。 交互式 Hive 群集只包含 Hive 服务。 

> [!NOTE]
> MapReduce、Pig、Sqoop、Oozie 以及其他服务很快将从此群集类型中删除。
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


