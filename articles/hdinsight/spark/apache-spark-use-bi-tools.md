---
title: "使用 Azure HDInsight 上的数据可视化工具的 Spark BI | Microsoft Docs"
description: "在 HDInsight 群集上使用 Apache Spark BI 使用数据可视化工具进行分析"
keywords: "apache spark bi,spark bi, spark 数据可视化, spark 业务智能"
services: hdinsight
documentationcenter: 
author: mumian
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.assetid: 1448b536-9bc8-46bc-bbc6-d7001623642a
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2017
ms.author: jgao
ms.openlocfilehash: fabf48da80cf44e82068d180c896ebbca7078d18
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/29/2017
---
# <a name="apache-spark-bi-using-data-visualization-tools-with-azure-hdinsight"></a>使用 Azure HDInsight 上的数据可视化工具的 Apache Spark BI

了解如何使用 Power BI 和 Tableau 在 Azure HDInsight 上直观显示 Apache Spark 群集中的数据。

## <a name="prerequisites"></a>先决条件

* HDInsight 上的 Apache Spark 群集。 有关说明，请参阅[在 Azure HDInsight 中创建 Apache Spark 群集](apache-spark-jupyter-spark-sql.md)。
* 群集中的示例数据。 相关说明，请参阅[在 HDInsight Spark 群集上运行交互式查询](apache-spark-load-data-run-query.md)。
* Power BI：[Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/) 和 [Power BI 试用订阅](https://app.powerbi.com/signupredirect?pbi_source=web)（可选）。
* Tableau：[Tableau Desktop](http://www.tableau.com/products/desktop) 和 [Microsoft Spark ODBC 驱动程序](http://go.microsoft.com/fwlink/?LinkId=616229)。


## <a name="hivetable"></a>查看示例数据

[上一教程](apache-spark-load-data-run-query.md)中创建的 Jupyter 笔记本包含用于创建 `hvac` 表的代码。 此表基于在所有 HDInsight Spark 群集上均可用的 CSV 文件（位于 \HdiSamples\HdiSamples\SensorSampleData\hvac\hvac.csv）。 在创建可视化效果之前，让我们查看一下 Spark 集群中的数据。

1. 验证预期的表是否存在。 将以下代码段粘贴到笔记本的空白单元格中，然后按“SHIFT+ENTER”。

        %%sql
        SHOW TABLES

    会看到类似以下内容的输出：

    ![在 Spark 中显示表](./media/apache-spark-use-bi-tools/show-tables.png)

    如果在开始本教程之前关闭笔记本，这会清除 `hvactemptable`，使其不包含在输出中。
    只有元存储中存储的配置单元表（由“isTemporary”列下的“False”表示）可从 BI 工具中进行访问。 在本教程中，我们将连接到创建的 **hvac** 表。

2. 确认表是否包含预期的数据。 将以下代码段粘贴到笔记本的空白单元格中，然后按“SHIFT+ENTER”。

        %%sql
        SELECT * FROM hvac LIMIT 10

    会看到类似以下内容的输出：

    ![在 Spark 中显示 hvac 表中的行](./media/apache-spark-use-bi-tools/select-limit.png)

3. 关闭笔记本以释放资源。 为此，请在笔记本的“文件”菜单中，单击“关闭并停止”。

## <a name="powerbi"></a>使用 Power BI 进行 Spark 数据可视化

现已验证了预期的数据存在，接下来可使用 Power BI 基于该数据创建可视化效果、报告和仪表板。 在本文中，我们将使用静态数据展示一个简单的示例，但如果你希望查看更复杂的流式处理示例，请在评论中告诉我们。

### <a name="create-a-report-in-power-bi-desktop"></a>在 Power BI Desktop 中创建报表
使用 Spark 的前几步是连接到 Power BI Desktop 中的集群，从集群中加载数据，并根据此数据创建基本的可视化效果。

> [!NOTE]
> 本文中演示的连接器目前处于预览状态，但请积极使用该连接器，并通过 [Power BI 社区](https://community.powerbi.com/)网站或 [Power BI Ideas](https://ideas.powerbi.com/forums/265200-power-bi-ideas)（Power BI 建议）提供反馈。

1. 在 Power BI Desktop 的“主页”选项卡上，依次点击“获取数据”和“更多”。

2. 搜索 `Spark`，选择“Azure HDInsight Spark”，然后单击“连接”。

    ![从 Apache Spark BI 中获取数据到 Power BI](./media/apache-spark-use-bi-tools/apache-spark-bi-import-data-power-bi.png "从 Apache Spark BI 中获取数据到 Power BI")

3. 输入群集 URL（格式为 `mysparkcluster.azurehdinsight.net`），选择“DirectQuery”，然后单击“确定”。

    ![连接到 Apache Spark BI](./media/apache-spark-use-bi-tools/connect-to-apache-spark-bi.png "连接到 Apache Spark BI")

    > [!NOTE]
    > Spark 可使用任一连接模式。 若使用 DirectQuery，则报告中会显示更改，但不刷新整个数据集。 如果导入数据，则必须刷新数据集以查看更改。 若要深入了解如何以及何时使用 DirectQuery，请参阅[在 Power BI 中使用 DirectQuery](https://powerbi.microsoft.com/documentation/powerbi-desktop-directquery-about/)。 

4. 在“用户名”和“密码”处输入 HDInsight 登录帐户信息（默认帐户为 `admin`），然后单击“连接”。

    ![Spark 群集用户名和密码](./media/apache-spark-use-bi-tools/user-password.png "Spark 群集用户名和密码")

5. 选择 `hvac` 表，稍后即可看到数据预览。 然后单击“加载”。

    ![Spark 群集用户名和密码](./media/apache-spark-use-bi-tools/apache-spark-bi-select-table.png "Spark 群集用户名和密码")

    Power BI Desktop 现拥有连接到 Spark 群集和从 `hvac` 表中加载数据所需的全部信息。 该表格及表中各列显示在“字段”窗格中。

    ![在 Apache Spark BI 仪表板上列出表](./media/apache-spark-use-bi-tools/apache-spark-bi-display-tables.png "在 Apache Spark BI 仪表板上列出表")

7. 生成可视化效果，展示每栋建筑物的目标温度与实际温度之间的差异： 

    1. 在“可视化”窗格中，选择“分区图”。 将“BuildingID”字段拖到“轴”，并将“ActualTemp”和“TargetTemp”字段拖至“值”。

        ![使用 Apache Spark BI 创建 Spark 数据可视化](./media/apache-spark-use-bi-tools/apache-spark-bi-add-value-columns.png "使用 Apache Spark BI 创建 Spark 数据可视化")

    2. 默认情况下，视觉效果会显示 **ActualTemp** 和 **TargetTemp** 的总和。 对于这两个字段，在下拉列表中选择“平均值”即可求出每栋建筑物的实际和目标温度的平均值。

        ![使用 Apache Spark BI 创建 Spark 数据可视化](./media/apache-spark-use-bi-tools/apache-spark-bi-average-of-values.png "使用 Apache Spark BI 创建 Spark 数据可视化")

    3. 数据视觉效果应与屏幕截图中类似。 在视觉效果上移动光标可获取相关数据的工具提示。

        ![使用 Apache Spark BI 创建 Spark 数据可视化](./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph.png "使用 Apache Spark BI 创建 Spark 数据可视化")

11. 依次单击“文件”和“保存”，再输入文件名 `spark.pbix` 。 

### <a name="publish-the-report-to-the-power-bi-service-optional"></a>将报表发布到 Power BI 服务（可选）
Power BI Desktop 中现显示功能完整的报表，你可就此停住，但很多人想要使用 Power BI 服务，便于在整个组织中轻松共享报表和仪表板。 

在本部分中，你将发布所创建的 Power BI Desktop 文件中内含的数据集和报表。 然后将可视化效果从报表固定到仪表板。 仪表板通常用于重点处理报表中的数据子集；此报表中只有一个可视化效果，但仍可用于完成此步骤。

1. 在 Power BI Desktop 的“主页”选项卡上，单击“发布”。

    ![从 Power BI Desktop 发布](./media/apache-spark-use-bi-tools/apache-spark-bi-publish.png "从 Power BI Desktop 发布")

2. 选择要将数据集发布和报告到的工作区，然后单击“选择”。 在下图中，默认选择“我的工作区”。

    ![选择要将数据集发布和报告到的工作区](./media/apache-spark-use-bi-tools/apache-spark-bi-select-workspace.png "选择要将数据集发布和报告到的工作区") 

3. Power BI Desktop 返回成功消息后，单击“在 Power BI 中打开 'spark.pbix'”。

    ![发布成功，请单击输入凭据](./media/apache-spark-use-bi-tools/apache-spark-bi-publish-success.png "发布成功，请单击输入凭据") 

4. 在 Power BI 服务中，单击“输入凭据”。

    ![在 Power BI 服务中输入凭据](./media/apache-spark-use-bi-tools/apache-spark-bi-enter-credentials.png "在 Power BI 服务中输入凭据")

5. 单击“编辑凭据”。

    ![在 Power BI 服务中编辑凭据] (./media/apache-spark-use-bi-tools/apache-spark-bi-edit-credentials.png "在 Power BI 服务中编辑凭据")

6. 输入 HDInsight 登录帐户信息（通常是 Power BI Desktop 中使用的默认 `admin` 帐户），然后单击“登录”。

    ![登录 Spark 群集](./media/apache-spark-use-bi-tools/apache-spark-bi-sign-in.png "登录 Spark 群集")

7. 在左窗格中，转到“工作区” > “我的工作区” > “报表”，然后单击“spark”。

    ![报表位于左窗格的报表下](./media/apache-spark-use-bi-tools/apache-spark-bi-service-left-pane.png "报表位于左窗格的报表下")

    还可在左窗格中的“数据集”下找到“spark”。

8. 现可在服务中使用 Power BI Desktop 中创建的视觉对象。 若要将此视觉对象固定到仪表板，请将鼠标悬停在视觉对象上，再单击图钉图标。

    ![Power BI 服务中的报表](./media/apache-spark-use-bi-tools/apache-spark-bi-service-report.png "Power BI 服务中的报表")

9. 选择“新的仪表板”，输入名称 `SparkDemo`，再单击“固定”。

    ![固定到新的仪表板](./media/apache-spark-use-bi-tools/apache-spark-bi-pin-dashboard.png "固定到新仪表板")

10. 在报表中，单击“转到仪表板”。 

    ![转到仪表板](./media/apache-spark-use-bi-tools/apache-spark-bi-open-dashboard.png "转到仪表板")

视觉对象已固定到仪表板 - 可在报表中添加其他视觉对象，并将其固定在同一仪表板上。 有关报表和仪表板的详细信息，请参阅 [Power BI 中的报表](https://powerbi.microsoft.com/documentation/powerbi-service-reports/)和 [Power BI 中的仪表板](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/)。

## <a name="tableau"></a>使用 Tableau Desktop 进行 Spark 数据可视化

> [!NOTE]
> 本部分仅适用于在 Azure HDInsight 中创建的 Spark 1.5.2 群集。
>
>

1. 在运行本 Apache Spark BI 教程的计算机上安装 [Tableau Desktop](http://www.tableau.com/products/desktop)。

2. 确保该计算机上还安装了 Microsoft Spark ODBC 驱动程序。 可从[此处](http://go.microsoft.com/fwlink/?LinkId=616229)安装该驱动程序。

1. 启动 Tableau Desktop。 在左窗格中，从要连接到的服务器列表中单击“Spark SQL”。 如果左窗格中未按默认列出 Spark SQL，可以单击“更多服务器”来找到它。
2. 在 Spark SQL 连接对话框中，按以下屏幕截图中所示提供值，并单击“确定”。

    ![连接到 Apache Spark BI 的群集](./media/apache-spark-use-bi-tools/connect-to-tableau-apache-spark-bi.png "连接到 Apache Spark BI 的群集")

    仅当已在计算机上安装了 [Microsoft Spark ODBC 驱动程序](http://go.microsoft.com/fwlink/?LinkId=616229)时，身份验证下拉列表才将“Microsoft Azure HDInsight 服务”列作一个选项。
3. 在下一个屏幕上，从“架构”下拉列表中单击“查找”图标，并单击“默认”。

    ![为 Apache Spark BI 查找架构](./media/apache-spark-use-bi-tools/tableau-find-schema-apache-spark-bi.png "为 Apache Spark BI 查找架构")
4. 对于“表”字段，请再次单击“查找”图标以列出群集中的所有 Hive 表。 应会看到以前使用笔记本创建的 **hvac** 表。

    ![为 Apache Spark BI 查找表](./media/apache-spark-use-bi-tools/tableau-find-table-apache-spark-bi.png "为 Apache Spark BI 查找表")
5. 将表拖放到右侧顶部的框中。 Tableau 将导入数据，并以红色框突出显示架构。

    ![为 Apache Spark BI 将表添加到 Tableau](./media/apache-spark-use-bi-tools/tableau-add-table-apache-spark-bi.png "为 Apache Spark BI 将表添加到 Tableau")
6. 单击左下角的“工作表 1”选项卡。 针对每个日期生成一种视觉效果，用于显示所有建筑物的目标温度和实际温度平均值。 将“日期”和“建筑物 ID”拖到“列”，将“实际温度”/“目标温度”拖到“行”。 在“标记”下面选择“区域”，以使用区域映射进行 Spark 数据可视化。

     ![添加用于 Spark 数据可视化的字段](./media/apache-spark-use-bi-tools/spark-data-visualization-add-fields.png "添加用于 Spark 数据可视化的字段")
7. 默认情况下，温度字段显示为聚合值。 如果想要改为显示平均温度，可以从下拉列表中执行该操作，如下所示。

    ![将温度用于 Spark 数据可视化](./media/apache-spark-use-bi-tools/spark-data-visualization-average-temperature.png "将温度用于 Spark 数据可视化")

8. 也可以将一个温度映射叠加在另一个温度映射之上，以更好地感受目标温度和实际温度之间的差异。 将鼠标移到下方区域映射的角落，直到出现以红色圆圈突出显示的控点形状为止。 将该映射拖到另一映射的顶部。看到以红色矩形突出显示的形状时松开鼠标。

    ![合并用于 Spark 数据可视化的映射](./media/apache-spark-use-bi-tools/spark-data-visualization-merge-maps.png "合并用于 Spark 数据可视化的映射")

     数据可视化效果应会发生变化，如屏幕截图中所示：

    ![Spark 数据可视化的 Tableau 输出](./media/apache-spark-use-bi-tools/spark-data-visualization-tableau-output.png "Spark 数据可视化的 Tableau 输出")
9. 单击“保存”以保存工作表。 可以创建仪表板，并在其中添加一个或多个工作表。

## <a name="next-steps"></a>后续步骤

到目前为止，已学习如何创建群集、创建用于查询数据的 Spark 数据帧，然后从 BI 工具访问这些数据。 现在可以查看有关如何管理群集资源，以及调试在 HDInsight Spark 群集中运行的作业的说明。

* [管理 Azure HDInsight 中 Apache Spark 群集的资源](apache-spark-resource-manager.md)
* [Track and debug jobs running on an Apache Spark cluster in HDInsight（跟踪和调试 HDInsight 中的 Apache Spark 群集上运行的作业）](apache-spark-job-debugging.md)

