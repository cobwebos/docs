---
title: "查询 Azure Log Analytics，以监视 Azure HDInsight 群集 | Microsoft Docs"
description: "了解如何在 Azure Log Analytics 上运行查询，以监视在 HDInsight 群集中运行的作业。"
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/11/2017
ms.author: nitinme
ms.translationtype: HT
ms.sourcegitcommit: 190ca4b228434a7d1b30348011c39a979c22edbd
ms.openlocfilehash: 8fe91bed69a1c06367346041d8caba4aaee4c82a
ms.contentlocale: zh-cn
ms.lasthandoff: 09/09/2017

---
# <a name="query-azure-log-analytics-to-monitor-hdinsight-clusters-preview"></a>查询 Azure Log Analytics，以监视 Azure HDInsight 群集（预览）

本文中提供了如何将 Azure Log Analytics 用于 Azure HDInsight 群集的一些方案。 最常见的三种方案是：

* 分析 OMS 中的 HDInsight 群集指标
* 搜索 HDInsight 群集的特定日志消息
* 根据群集中发生的事件，创建警报

## <a name="prerequisites"></a>先决条件

* 必须先配置 HDInsight 群集，才能使用 Azure Log Analytics。 有关说明，请参阅[将 Azure Log Analytics 用于 HDInsight 群集](hdinsight-hadoop-oms-log-analytics-tutorial.md)。

* 必须已将特定于 HDInsight 群集的管理解决方案添加到 OMS 工作区，如[将 HDInsight 群集管理解决方案添加到 Log Analytics](hdinsight-hadoop-oms-log-analytics-management-solutions.md) 中所述。

## <a name="analyze-hdinsight-cluster-metrics-in-oms"></a>分析 OMS 中的 HDInsight 群集指标

本部分提供有关查找 HDInsight 群集的特定指标的分步指导。

1. 打开 OMS 仪表板。 在 Azure 门户中，打开与 Azure Log Analytics 相关联的 HDInsight 群集边栏选项卡，单击“监视”选项卡，然后单击“打开 OMS 仪表板”。

    ![打开 OMS 仪表板](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-open-oms-dashboard.png "Open OMS dashboard")

2. 在 OMS 仪表板的主屏幕中，单击“日志搜索”。

    ![打开日志搜索](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-click-log-search.png "Open Log Search")

3. 在日志搜索窗口中的“在此处开始搜索”文本框中，键入 `*`，为所有配置为使用 Azure Log Analytics 的所有 HDInsight 群集搜索所有可用指标的所有指标。 按 Enter。

    ![搜索所有指标](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics.png "Search all metrics")

4. 应该看到如下输出。

    ![搜索所有指标输出](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics-output.png "Search all metrics output")

5. 在左窗格的“类型”类别下，搜索要深入了解的指标。 在本教程中，我们选择使用 `metrics_resourcemanager_queue_root_default_CL`。 选择指标对应的复选框，然后单击“应用”。

    > [!NOTE]
    > 可能需要单击“[+]更多”按钮来查找所需指标。 此外，“应用”按钮位于列表底部，因此必须向下滚动才能看到它。
    > 
    >    
    请注意，文本框中的查询现改为以下屏幕截图中突出显示的框中的内容：

    ![搜索特定指标](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-metrics.png "Search for specific metrics")

6. 现在可以更深入了解此特定指标。 例如，现在可以根据 10 分钟时间间隔中，按群集名称分类的所用资源的平均数来优化现有输出。 在查询文本框中键入以下查询。

        * (Type=metrics_resourcemanager_queue_root_default_CL) | measure avg(UsedAMResourceMB_d) by ClusterName_s interval 10minute

    ![搜索特定指标](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-more-specific-metrics.png "Search for specific metrics")

7. 可以使用以下查询，基于 10 分钟时间范围内使用最大比例资源（以及 90% 和 95%）的时间优化结果，而不是基于所用资源的平均数进行优化。

        * (Type=metrics_resourcemanager_queue_root_default_CL) | measure max(UsedAMResourceMB_d) , pct95(UsedAMResourceMB_d), pct90(UsedAMResourceMB_d)  by ClusterName_s interval 10minute

    ![搜索特定指标](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-more-specific-metrics-1.png "Search for specific metrics")

## <a name="search-for-specific-log-messages-in-hdinsight-clusters"></a>搜索 HDInsight 群集中的特定日志消息

此部分提供有关在特定时间范围内查找错误消息的分步指导。 此处的步骤只是演示如何找到感兴趣的错误消息的一个示例。 可以使用任何可用属性来查找要查找的错误。

1. 打开 OMS 仪表板。 在 Azure 门户中，打开与 Azure Log Analytics 相关联的 HDInsight 群集边栏选项卡，单击“监视”选项卡，然后单击“打开 OMS 仪表板”。

    ![打开 OMS 仪表板](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-open-oms-dashboard.png "Open OMS dashboard")

2. 在 OMS 仪表板的主屏幕中，单击“日志搜索”。

    ![打开日志搜索](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-click-log-search.png "Open Log Search")

3. 在日志搜索窗口中的“在此处开始搜索”文本框中，键入 `"Error"`（含引号），为所有配置为使用 Azure Log Analytics 的所有 HDInsight 群集搜索全部错误消息。 按 Enter。

    ![搜索全部错误](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-errors.png "Search all errors")

4. 应该看到如下输出。

    ![搜索全部错误输出](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-errors-output.png "Search all errors output")

5. 在左窗格的“类型”类别下，搜索要深入了解的错误类型。 在本教程中，我们选择使用 `log_sparkappsexecutors_CL`。 选择指标对应的复选框，然后单击“应用”。

    ![搜索特定错误](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error.png "Search for specific error")

        
6. 请注意，文本框中的查询现在更改为下面的突出显示框中的查询，且结果经过优化，仅显示所选类型的错误。

    ![搜索特定错误输出](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-output.png "Search for specific errors output")

7. 现在可通过使用左窗格中的可用选项来深入了解此特定错误。 例如，可以优化查询，仅查看特定辅助角色节点的错误消息。

    ![搜索特定错误输出](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-refined.png "Search for specific errors output")

8. 可以通过从左窗格中选择相关的时间值来进一步放大错误发生的时间。

    ![搜索特定错误输出](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-time.png "Search for specific errors output")

9. 现在，可找到要寻找的特定错误。 可以单击“[+] 显示更多”来查看实际的错误消息。

    ![搜索特定错误输出](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-arrived.png "Search for specific errors output")

## <a name="create-alerts-to-track-events"></a>创建警报跟踪事件

创建警报的第一步是到达基于其触发警报的查询。 为简单起见，让我们使用以下查询，该查询可提供 HDInsight 群集上运行的失败的应用程序的列表。

    * (Type=metrics_resourcemanager_queue_root_default_CL) AppsFailed_d>0 

可以根据需要使用任何查询创建警报。

1. 打开 OMS 仪表板。 在 Azure 门户中，打开与 Azure Log Analytics 相关联的 HDInsight 群集边栏选项卡，单击“监视”选项卡，然后单击“打开 OMS 仪表板”。

    ![打开 OMS 仪表板](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-open-oms-dashboard.png "Open OMS dashboard")

2. 在 OMS 仪表板的主屏幕中，单击“日志搜索”。

    ![打开日志搜索](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-click-log-search.png "Open Log Search")

3. 在日志搜索窗口的“在此处开始搜索”文本框中，粘贴要在其上创建警报的查询，按 Enter，然后单击“警报”按钮。

    ![输入查询以创建警报](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert-query.png "Enter query to create an alert")

4. 在“添加警报规则”窗口中，输入用于创建警报的查询和其他详细信息，然后单击“保存”。

    ![输入查询以创建警报](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert.png "Enter query to create an alert")

    在此屏幕截图中，我们只在警报查询检索输出时发送电子邮件通知。

5. 还可以编辑或删除现有警报。 为此，在 OMS 门户中的任何页面，单击“设置”图标。

    ![输入查询以创建警报](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-edit-alert.png "Enter query to create an alert")

6. 在“设置”页面，单击“警报”，查看创建的警报。 还可以启用或禁用警报，对警报进行编辑或删除操作。 有关详细信息，请参阅[使用 Log Analytics 中的警报规则](../log-analytics/log-analytics-alerts-creating.md)。

## <a name="see-also"></a>另请参阅

* [使用 OMS Log Analytics](https://blogs.msdn.microsoft.com/wei_out_there_with_system_center/2016/07/03/oms-log-analytics-create-tiles-drill-ins-and-dashboards-with-the-view-designer/)
* [在 Log Analytics 中创建警报规则](../log-analytics/log-analytics-alerts-creating.md)

