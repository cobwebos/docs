---
title: "在 Azure HDInsight 中使用 Zeppelin 运行 Hive 查询 | Microsoft Docs"
description: "了解如何使用 Zeppelin 运行 Hive 查询。"
keywords: "hdinsight,hadoop,hive,交互式查询,LLAP"
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive,
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2017
ms.author: jgao
ms.translationtype: HT
ms.sourcegitcommit: 0e862492c9e17d0acb3c57a0d0abd1f77de08b6a
ms.openlocfilehash: b44321619f2aa94a6d98624ab1ee35a598fb6fc8
ms.contentlocale: zh-cn
ms.lasthandoff: 09/27/2017

---
# <a name="use-zeppelin-to-run-hive-queries-in-azure-hdinsight"></a>在 Azure HDInsight 中使用 Zeppelin 运行 Hive 查询 

HDInsight 交互式查询群集包括可用来运行交互式 Hive 查询的 Zeppelin 笔记本。 本文介绍如何使用 Zeppelin 在 Azure HDInsight 中运行 Hive 查询。 

## <a name="prerequisites"></a>先决条件
在开始阅读本文前，必须具备以下项：

* **HDInsight 交互式查询群集**。 若要创建 HDInsight 群集，请参阅[创建群集](hdinsight-hadoop-linux-tutorial-get-started.md#create-cluster)。  请确保选择“交互式查询”类型。 

## <a name="create-a-zeppelin-note"></a>创建 Zeppelin 笔记

1. 浏览到以下 URL：

        https://CLUSTERNAME.azurehdinsight.net/zeppelin
    将 **CLUSTERNAME** 替换为群集名称。

2. 输入 Hadoop 用户名和密码。 在 Zeppelin 页中，可以创建新笔记，也可以打开现有笔记。 HiveSample 包含一些示例 Hive 查询。  

    ![HDInsight 交互式查询 zeppelin](./media/hdinsight-connect-hive-zeppelin/hdinsight-hive-zeppelin.png)
3. 单击“创建新笔记”。
4. 键入或选择以下值：

    - 笔记名称：输入笔记的名称。
    - 默认解释器：选择 **JDBC**。

5. 单击“创建笔记”。
6. 运行以下 Hive 查询：

        %jdbc(hive)
        show tables

    ![HDInsight 交互式查询 zeppelin 运行查询](./media/hdinsight-connect-hive-zeppelin/hdinsight-hive-zeppelin-query.png)

    第一行中的 **%jdbc(hive)** 语句告诉笔记本使用 Hive JDBC 解释程序。

    该查询将返回一个名为 *hivesampletable* 的 Hive 表。

    以下是可以针对 hivesampletable 运行的附加的两个 Hive 查询。 

        %jdbc(hive)
        select * from hivesampletable limit 10

        %jdbc(hive)
        select ${group_name}, count(*) as total_count
        from hivesampletable
        group by ${group_name=market,market|deviceplatform|devicemake}
        limit ${total_count=10}

    与传统 Hive 相比，返回查询结果的速度更快。


## <a name="next-steps"></a>后续步骤
本文介绍了如何使用 Power BI 直观显示 HDInsight 中的数据。  若要了解更多信息，请参阅下列文章：

* [在 Azure HDInsight 中使用 Microsoft Power BI 直观显示 Hive 数据](./hdinsight-connect-hive-power-bi.md)。
* [使用 Microsoft Hive ODBC 驱动程序将 Excel 连接到 HDInsight](./hdinsight-connect-excel-hive-odbc-driver.md)。
* [使用 Power Query 将 Excel 连接到 Hadoop](./hdinsight-connect-excel-power-query.md)。
* [使用针对 Visual Studio 的 Data Lake 工具连接到 Azure HDInsight 并运行 Hive 查询](./hdinsight-hadoop-visual-studio-tools-get-started.md)。
* [使用用于 Visual Studio Code 的 Azure HDInsight 工具](hdinsight-for-vscode.md)。
* [将数据上传到 HDInsight](./hdinsight-upload-data.md)。

