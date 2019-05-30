---
title: 使用 Power BI 直观显示 Apache Hive 数据 - Azure HDInsight
description: 了解如何使用 Microsoft Power BI 直观显示 Azure HDInsight 已处理的 Hive 数据。
keywords: hdinsight,hadoop,hive,交互式查询,交互式 hive,LLAP,odbc
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: hrasheed
ms.openlocfilehash: 1e0c043e484e4eaf2639f76c9af3fef15ad85047
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2019
ms.locfileid: "66237485"
---
# <a name="visualize-apache-hive-data-with-microsoft-power-bi-using-odbc-in-azure-hdinsight"></a>在 Azure HDInsight 中通过 Microsoft Power BI 使用 ODBC 直观显示 Apache Hive 数据

了解如何将 Microsoft Power BI Desktop 连接到使用 ODBC 的 Azure HDInsight 并直观显示 Apache Hive 数据。

>[!IMPORTANT]
> 可以利用 Hive ODBC 驱动程序，通过 Power BI Desktop 中的通用 ODBC 连接器执行导入操作。 但是，鉴于 Hive 查询引擎的非交互性质，不建议对 BI 工作负荷执行此操作。 出于性能考量，[HDInsight 交互式查询连接器](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md)和 [HDInsight Spark 连接器](https://docs.microsoft.com/power-bi/spark-on-hdinsight-with-direct-connect)是更好的选择。

在本教程中，加载将数据从`hivesampletable`到 Power BI 的 Hive 表。 Hive 表包含一些移动电话使用情况数据。 然后在世界地图上绘制使用情况数据：

![HDInsight Power BI 地图报表](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-visualization.png)

这些信息也适用于新的[交互式查询](../interactive-query/apache-interactive-query-get-started.md)群集类型。 有关如何使用直接查询连接到 HDInsight 交互式查询的信息，请参阅[在 Azure HDInsight 中通过 Microsoft Power BI 使用直接查询可视化交互式查询 Hive 数据](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md)。

## <a name="prerequisites"></a>必备组件

在开始阅读本文前，必须具备以下项：

* **HDInsight 群集**。 该群集可以是包含 Hive 的 HDInsight 群集，也可以是新发布的交互式查询群集。 有关创建群集的信息，请参阅[创建群集](apache-hadoop-linux-tutorial-get-started.md#create-cluster)。

* **[Microsoft Power BI Desktop](https://powerbi.microsoft.com/desktop/)** 。 可从 [Microsoft 下载中心](https://www.microsoft.com/download/details.aspx?id=45331)下载副本。

## <a name="create-hive-odbc-data-source"></a>创建 Hive ODBC 数据源

请参阅[创建 Hive ODBC 数据源](apache-hadoop-connect-excel-hive-odbc-driver.md#create-apache-hive-odbc-data-source)。

## <a name="load-data-from-hdinsight"></a>从 HDInsight 加载数据

所有 HDInsight 群集都随附了 hivesampletable Hive 表。

1. 启动 Power BI Desktop。

2. 从顶部菜单中，导航到**主页** > **获取数据** > **更多...** .

    ![HDInsight Power BI 开放数据](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-open-odbc.png)

3. 从**获取数据**对话框中，选择**其他**从左侧，选择**ODBC**右侧，然后选择**Connect**底部。

4. 从**从 ODBC**对话框中，选择数据源从下拉列表中，在上一部分中创建的名称，然后选择**确定**。

5. 从**导航器**对话框中，展开**ODBC > HIVE > 默认**，选择**hivesampletable**，然后选择**负载**。

6. 从**ODBC 驱动程序**对话框中，选择**默认或自定义**，然后选择**Connect**。

## <a name="visualize-data"></a>可视化数据

从上一过程继续进行。

1. 在“可视化”窗格中，选择“地图”  。  它是一个地球图标。

    ![HDInsight Power BI 自定义报表](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-customize.png)
2. 从**字段**窗格中，选择**国家/地区**并**devicemake**。 可以在地图上看到绘制的数据。
3. 展开地图。

## <a name="next-steps"></a>后续步骤

本文介绍了如何使用 Power BI 直观显示 HDInsight 中的数据。  若要了解更多信息，请参阅下列文章：

* [在 Azure HDInsight 中使用 Apache Zeppelin 运行 Apache Hive 查询](../interactive-query/hdinsight-connect-hive-zeppelin.md)。
* [使用 Microsoft Hive ODBC 驱动程序将 Excel 连接到 HDInsight](./apache-hadoop-connect-excel-hive-odbc-driver.md)。
* [使用 Power Query 将 Excel 连接到 Apache Hadoop](apache-hadoop-connect-excel-power-query.md)。
* [使用针对 Visual Studio 的 Data Lake 工具连接到 Azure HDInsight 并运行 Apache Hive 查询](apache-hadoop-visual-studio-tools-get-started.md)。
* [使用用于 Visual Studio Code 的 Azure HDInsight 工具](../hdinsight-for-vscode.md)。
* [将数据上传到 HDInsight](./../hdinsight-upload-data.md)。
