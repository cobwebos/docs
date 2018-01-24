---
title: "在 Azure HDInsight 中使用 Power BI 直观显示交互式查询 Hive 数据 | Microsoft Docs"
description: "了解如何使用 Microsoft Power BI 直观显示 Azure HDInsight 已处理的交互式查询 Hive 数据。"
keywords: "hdinsight,hadoop,hive,交互式查询,交互式 hive,LLAP,directquery"
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
ms.date: 12/19/2017
ms.author: jgao
ms.openlocfilehash: 290e600b7be4a6f9fb57afa50bb771e42e6a0624
ms.sourcegitcommit: 4bd369fc472dced985239aef736fece42fecfb3b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2018
---
# <a name="visualize-interactive-query-hive-data-with-microsoft-power-bi-using-direct-query-in-azure-hdinsight"></a>在 Azure HDInsight 中使用 Microsoft Power BI 通过直接查询直观显示交互式查询 Hive 数据

了解如何将 Microsoft Power BI 连接到 Azure HDInsight 交互式查询群集并使用直接查询直观显示 Hive 数据。 在本教程中，从 hivesampletable Hive 表将数据加载到 Power BI 中。 Hive 表包含一些移动电话使用情况数据。 然后在世界地图上绘制使用情况数据：

![HDInsight Power BI 地图报表](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-visualization.png)

有关如何使用 ODBC 连接到 Hive 的信息，请参阅 [在 Azure HDInsight 中使用 Microsoft Power BI 通过 ODBC 直观显示 Hive 数据](../hadoop/apache-hadoop-connect-hive-power-bi.md)。 

## <a name="prerequisites"></a>先决条件
在开始阅读本文前，必须具备以下项：

* **HDInsight 群集**。 该群集可以是包含 Hive 的 HDInsight 群集，也可以是新发布的交互式查询群集。 有关创建群集的信息，请参阅[创建群集](../hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster)。
* **[Microsoft Power BI Desktop](https://powerbi.microsoft.com/desktop/)**。 可从 [Microsoft 下载中心](https://www.microsoft.com/download/details.aspx?id=45331)下载副本。

## <a name="load-data-from-hdinsight"></a>从 HDInsight 加载数据

所有 HDInsight 群集都随附了 hivesampletable Hive 表。

1. 登录 Power BI Desktop。
2. 单击“主页”选项卡，从“外部数据”功能区中单击“获取数据”，然后选择“更多”。

    ![HDInsight Power BI 开放数据](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-open-odbc.png)
3. 从“获取数据”窗格，在搜索框中键入 hdinsight。 如果看不到“HDInsight 交互式查询(beta)”，需要将 Power BI Desktop 更新到最新版本。
4. 单击“HDInsight 交互式查询(beta)”，然后单击“连接”。
5. 单击“继续”关闭“预览连接器”警告对话框。
6. 在“HDInsight 交互式查询”中选择或输入以下信息：

    - **服务器**：输入交互式查询群集名称，例如，myiqcluster.azurehdinsight.net。
    - **数据库**：对于本教程，输入“默认”。
    - **数据连接模式**：对于本教程，选择“DirectQuery”。

    ![HDInsight 交互式查询 power bi directquery 连接](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-interactive-query-power-bi-connect.png)
7. 单击“确定”。
8. 输入 HTTP 用户凭据，然后单击“确定”。  默认用户名为“admin”
9. 在左侧窗格中，选择“hivesampletale”，然后单击“加载”。

    ![HDInsight 交互式查询 power bi hivesampletable](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-interactive-query-power-bi-hivesampletable.png)

## <a name="visualize-data"></a>可视化数据

从上一过程继续进行。

1. 在“可视化”窗格中，选择“地图”。  它是一个地球图标。

    ![HDInsight Power BI 自定义报表](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-customize.png)
2. 在“字段”窗格中，选择 **country** 和 **devicemake**。 可以在地图上看到绘制的数据。
3. 展开地图。

## <a name="next-steps"></a>后续步骤
本文介绍了如何使用 Power BI 直观显示 HDInsight 中的数据。  若要了解更多信息，请参阅下列文章：

* [在 Azure HDInsight 中使用 Microsoft Power BI 通过 ODBC 直观显示 Hive 数据](../hadoop/apache-hadoop-connect-hive-power-bi.md)。 
* [在 Azure HDInsight 中使用 Zeppelin 运行 Hive 查询](./../hdinsight-connect-hive-zeppelin.md)。
* [使用 Microsoft Hive ODBC 驱动程序将 Excel 连接到 HDInsight](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md)。
* [使用 Power Query 将 Excel 连接到 Hadoop](../hadoop/apache-hadoop-connect-excel-power-query.md)。
* [使用针对 Visual Studio 的 Data Lake 工具连接到 Azure HDInsight 并运行 Hive 查询](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)。
* [使用用于 Visual Studio Code 的 Azure HDInsight 工具](../hdinsight-for-vscode.md)。
* [将数据上传到 HDInsight](./../hdinsight-upload-data.md)。
