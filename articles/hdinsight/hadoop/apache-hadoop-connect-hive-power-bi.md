---
title: 使用 Power BI 直观显示 Apache Hive 数据 - Azure HDInsight
description: 了解如何使用 Microsoft Power BI 直观显示 Azure HDInsight 已处理的 Hive 数据。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/24/2020
ms.openlocfilehash: f1bce0a95b672bb23767fce3d5d092221ccd8321
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82858337"
---
# <a name="visualize-apache-hive-data-with-microsoft-power-bi-using-odbc-in-azure-hdinsight"></a>在 Azure HDInsight 中通过 Microsoft Power BI 使用 ODBC 直观显示 Apache Hive 数据

了解如何使用 ODBC 将 Microsoft Power BI Desktop 连接到 Azure HDInsight 并直观显示 Apache Hive 数据。

> [!IMPORTANT]
> 可以利用 Hive ODBC 驱动程序，通过 Power BI Desktop 中的通用 ODBC 连接器执行导入操作。 但是，鉴于 Hive 查询引擎的非交互性质，不建议对 BI 工作负荷执行此操作。 出于性能考量，[HDInsight 交互式查询连接器](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md)和 [HDInsight Spark 连接器](https://docs.microsoft.com/power-bi/spark-on-hdinsight-with-direct-connect)是更好的选择。

在本文中，从 `hivesampletable` Hive 表将数据加载到 Power BI 中。 Hive 表包含一些移动电话使用情况数据。 然后在世界地图上绘制使用情况数据：

![HDInsight Power BI 地图报表](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-visualization.png)

这些信息也适用于新的[交互式查询](../interactive-query/apache-interactive-query-get-started.md)群集类型。 有关如何使用直接查询连接到 HDInsight 交互式查询的信息，请参阅[在 Azure HDInsight 中通过 Microsoft Power BI 使用直接查询可视化交互式查询 Hive 数据](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md)。

## <a name="prerequisites"></a>必备条件

在开始阅读本文前，必须具备以下项：

* HDInsight 群集。 该群集可以是包含 Hive 的 HDInsight 群集，也可以是新发布的交互式查询群集。 有关创建群集的信息，请参阅[创建群集](apache-hadoop-linux-tutorial-get-started.md)。

* [Microsoft Power BI Desktop](https://powerbi.microsoft.com/desktop/)。 可从 [Microsoft 下载中心](https://www.microsoft.com/download/details.aspx?id=45331)下载副本。

## <a name="create-hive-odbc-data-source"></a>创建 Hive ODBC 数据源

请参阅[创建 Hive ODBC 数据源](apache-hadoop-connect-excel-hive-odbc-driver.md#create-apache-hive-odbc-data-source)。

## <a name="load-data-from-hdinsight"></a>从 HDInsight 加载数据

**Hivesampletable** Hive 表附带了所有 HDInsight 群集。

1. 启动 Power BI Desktop。

1. 在顶部菜单中，导航到**Home** > **获取数据** > **更多 ...**。

    ![HDInsight Excel Power BI 开放数据](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-open-odbc.png)

1. 在“获取数据”**** 对话框中，选择左侧的“其他”****，选择右侧的 **ODBC**，然后选择底部的“连接”****。

1. 在“从 ODBC”**** 对话框中，从下拉列表中选择在上一部分中创建的数据源名称， 然后选择“确定”。 

1. 第一次使用时，将打开“ODBC 驱动程序”**** 对话框。 从左侧菜单中选择“默认值或自定义”****。 然后选择“连接”以打开**导航器******。

1. 在“导航器”**** 对话框中，依次展开“ODBC”>“HIVE”>“默认”****，选择 **hivesampletable**，然后选择“加载”****。

## <a name="visualize-data"></a>可视化数据

从上一过程继续进行。

1. 从可视化效果窗格中选择 "**地图**"，它是一个地球图标。

    ![HDInsight Power BI 自定义报表](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-customize.png)

1. 从 "**字段**" 窗格中，选择 " **country** and **devicemake**"。 可以在地图上看到绘制的数据。

1. 展开地图。

## <a name="next-steps"></a>后续步骤

本文介绍了如何使用 Power BI 直观显示 HDInsight 中的数据。  若要了解详细信息，请参阅以下文章：

* [使用 Microsoft Hive ODBC 驱动程序将 Excel 连接到 HDInsight](./apache-hadoop-connect-excel-hive-odbc-driver.md)。
* [使用 Power Query 将 Excel 连接到 Apache Hadoop](apache-hadoop-connect-excel-power-query.md)。
* [使用 Microsoft Power BI 使用直接查询直观显示交互式查询 Apache Hive 数据](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md)
