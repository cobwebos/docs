---
title: 在 Azure HDInsight 中使用 Power BI 直观显示大数据 | Microsoft Docs
description: 了解如何使用 Microsoft Power BI 直观显示 Azure HDInsight 已处理的 Hive 数据。
keywords: hdinsight,hadoop,hive,交互式查询,交互式 hive,LLAP,odbc
services: hdinsight
documentationcenter: ''
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive,
ms.devlang: na
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: jgao
ms.openlocfilehash: 6f4efdebd6fc2833747b4cb72496447e0be03a58
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2018
---
# <a name="visualize-hive-data-with-microsoft-power-bi-using-odbc-in-azure-hdinsight"></a>在 Azure HDInsight 中通过 Microsoft Power BI 使用 ODBC 直观显示 Hive 数据

了解如何使用 ODBC 将 Microsoft Power BI 连接到 Azure HDInsight 并直观显示 Hive 数据。 

>[!IMPORTANT]
> 可以利用 Hive ODBC 驱动程序，通过 Power BI Desktop 中的通用 ODBC 连接器执行导入操作。 但是，鉴于 Hive 查询引擎的非交互性质，不建议对 BI 工作负荷执行此操作。 出于性能考量，[HDInsight 交互式查询连接器](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md)和 [HDInsight Spark 连接器](https://docs.microsoft.com/power-bi/spark-on-hdinsight-with-direct-connect)是更好的选择。

在本教程中，从 hivesampletable Hive 表将数据加载到 Power BI 中。 Hive 表包含一些移动电话使用情况数据。 然后在世界地图上绘制使用情况数据：

![HDInsight Power BI 地图报表](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-visualization.png)

这些信息也适用于新的[交互式查询](../interactive-query/apache-interactive-query-get-started.md)群集类型。 有关如何使用直接查询连接到 HDInsight 交互式查询的信息，请参阅[在 Azure HDInsight 中通过 Microsoft Power BI 使用直接查询可视化交互式查询 Hive 数据](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md)。



## <a name="prerequisites"></a>先决条件
在开始阅读本文前，必须具备以下项：

* **HDInsight 群集**。 该群集可以是包含 Hive 的 HDInsight 群集，也可以是新发布的交互式查询群集。 有关创建群集的信息，请参阅[创建群集](apache-hadoop-linux-tutorial-get-started.md#create-cluster)。
* **[Microsoft Power BI Desktop](https://powerbi.microsoft.com/desktop/)**。 可从 [Microsoft 下载中心](https://www.microsoft.com/download/details.aspx?id=45331)下载副本。

## <a name="create-hive-odbc-data-source"></a>创建 Hive ODBC 数据源

请参阅[创建 Hive ODBC 数据源](apache-hadoop-connect-excel-hive-odbc-driver.md#create-hive-odbc-data-source)。

## <a name="load-data-from-hdinsight"></a>从 HDInsight 加载数据

所有 HDInsight 群集都随附了 hivesampletable Hive 表。

1. 登录 Power BI Desktop。
2. 单击“主页”选项卡，从“外部数据”功能区中单击“获取数据”，然后选择“更多”。

    ![HDInsight Power BI 开放数据](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-open-odbc.png)
3. 在“获取数据”窗格中，单击左侧的“其他”，单击右侧的 **ODBC**，然后单击底部的“连接”。
4. 在“从 ODBC”窗格中，选择在上一部分中创建的数据源名称，然后单击“确定”。
5. 在“导航器”窗格中，依次展开“ODBC”->“ HIVE”->“默认”，选择 **hivesampletable**，然后单击“加载”。

## <a name="visualize-data"></a>可视化数据

从上一过程继续进行。

1. 在“可视化”窗格中，选择“地图”。  它是一个地球图标。

    ![HDInsight Power BI 自定义报表](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-customize.png)
2. 在“字段”窗格中，选择 **country** 和 **devicemake**。 可以在地图上看到绘制的数据。
3. 展开地图。

## <a name="next-steps"></a>后续步骤
本文介绍了如何使用 Power BI 直观显示 HDInsight 中的数据。  若要了解更多信息，请参阅下列文章：

* [在 Azure HDInsight 中使用 Zeppelin 运行 Hive 查询](./../hdinsight-connect-hive-zeppelin.md)。
* [使用 Microsoft Hive ODBC 驱动程序将 Excel 连接到 HDInsight](./apache-hadoop-connect-excel-hive-odbc-driver.md)。
* [使用 Power Query 将 Excel 连接到 Hadoop](apache-hadoop-connect-excel-power-query.md)。
* [使用针对 Visual Studio 的 Data Lake 工具连接到 Azure HDInsight 并运行 Hive 查询](apache-hadoop-visual-studio-tools-get-started.md)。
* [使用用于 Visual Studio Code 的 Azure HDInsight 工具](../hdinsight-for-vscode.md)。
* [将数据上传到 HDInsight](./../hdinsight-upload-data.md)。
