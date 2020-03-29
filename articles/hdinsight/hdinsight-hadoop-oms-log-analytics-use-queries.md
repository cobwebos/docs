---
title: 查询 Azure 监视器日志以监视 Azure HDInsight 群集
description: 了解如何在 Azure 监视器日志上运行查询，以监视在 HDInsight 群集中运行的作业。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/02/2019
ms.openlocfilehash: 65e85548420116bdfcab87fe9f81a20e66226beb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "74803767"
---
# <a name="query-azure-monitor-logs-to-monitor-hdinsight-clusters"></a>查询 Azure 监视器日志以监视 HDInsight 群集

了解如何使用 Azure 监视器日志监视 Azure HDInsight 群集的一些基本方案：

* [分析 HDInsight 群集指标](#analyze-hdinsight-cluster-metrics)
* [创建事件警报](#create-alerts-for-tracking-events)

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>先决条件

您必须将 HDInsight 群集配置为使用 Azure 监视器日志，并将特定于 HDInsight 群集的 Azure 监视器日志监视解决方案添加到工作区。 有关说明，请参阅[使用具有 HDInsight 群集的 Azure 监视器日志](hdinsight-hadoop-oms-log-analytics-tutorial.md)。

## <a name="analyze-hdinsight-cluster-metrics"></a>分析 HDInsight 群集指标

了解如何查找 HDInsight 群集的特定指标。

1. 从 Azure 门户打开关联到 HDInsight 群集的 Log Analytics 工作区。
1. 在 **"常规"** 下，选择 **"日志**"。
1. 在搜索框中键入以下查询，以搜索配置为使用 Azure 监视器日志的所有 HDInsight 群集的所有可用指标的所有可用指标，然后选择"**运行**"。 查看结果。

    ```kusto
    search *
    ```

    ![阿帕奇·安巴里分析搜索所有指标](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics.png "搜索所有指标")

1. 从左侧菜单中，选择 **"筛选"** 选项卡。

1. 在 **"类型"** 下，选择 **"检测信号**"。 然后选择 **"应用&运行**。

    ![日志分析搜索特定指标](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-metrics.png "搜索特定指标")

1. 请注意，文本框中的查询更改为：

    ```kusto
    search *
    | where Type == "Heartbeat"
    ```

1. 您可以使用左侧菜单中提供的选项进行更深入的挖掘。 例如：

    - 要查看来自特定节点的日志：

        ![搜索特定错误输出1](./media/hdinsight-hadoop-oms-log-analytics-use-queries/log-analytics-specific-node.png "搜索特定错误输出1")

    - 要在特定时间查看日志：

        ![搜索特定错误输出2](./media/hdinsight-hadoop-oms-log-analytics-use-queries/log-analytics-specific-time.png "搜索特定错误输出2")

1. 选择 **"应用&运行**并查看结果。 另请注意，查询已更新为：

    ```kusto
    search *
    | where Type == "Heartbeat"
    | where (Computer == "zk2-myhado") and (TimeGenerated == "2019-12-02T23:15:02.69Z" or TimeGenerated == "2019-12-02T23:15:08.07Z" or TimeGenerated == "2019-12-02T21:09:34.787Z")
    ```

### <a name="additional-sample-queries"></a>其他示例查询

基于 10 分钟间隔内使用的资源平均值的示例查询，按群集名称分类：

```kusto
search in (metrics_resourcemanager_queue_root_default_CL) * 
| summarize AggregatedValue = avg(UsedAMResourceMB_d) by ClusterName_s, bin(TimeGenerated, 10m)
```

可以使用以下查询，基于 10 分钟时间范围内使用最大比例资源（以及 90% 和 95%）的时间优化结果，而不是基于所用资源的平均数进行优化：

```kusto
search in (metrics_resourcemanager_queue_root_default_CL) * 
| summarize ["max(UsedAMResourceMB_d)"] = max(UsedAMResourceMB_d), ["pct95(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 95), ["pct90(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 90) by ClusterName_s, bin(TimeGenerated, 10m)
```

## <a name="create-alerts-for-tracking-events"></a>创建用于跟踪事件的警报

创建警报的第一步是到达基于其触发警报的查询。 可以根据需要使用任何查询创建警报。

1. 从 Azure 门户打开关联到 HDInsight 群集的 Log Analytics 工作区。
1. 在 **"常规"** 下，选择 **"日志**"。
1. 运行要在其上创建警报的以下查询，然后选择 **"运行**"。

    ```kusto
    metrics_resourcemanager_queue_root_default_CL | where AppsFailed_d > 0
    ```

    查询列出了 HDInsight 群集上运行失败的应用程序。

1. 选择页面顶部**的新警报规则**。

    ![输入查询以创建警报1](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert-query.png "输入查询以创建警报1")

1. 在“创建规则”窗口中，输入用于创建警报的查询和其他详细信息，然后选择“创建预警规则”********。

    ![输入查询以创建警报2](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert.png "输入查询以创建警报2")

### <a name="edit-or-delete-an-existing-alert"></a>编辑或删除现有警报

1. 从 Azure 门户打开 Log Analytics 工作区。

1. 从左侧菜单中，在 **"监视"** 下，选择 **"警报**"。

1. 向顶部，选择 **"管理警报规则**"。

1. 选择要编辑或删除的警报。

1. 可选择以下选项：“保存”、“放弃”、“禁用”和“删除”****************。

    ![HDInsight Azure 监视器日志警报删除编辑](media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-edit-alert.png)

有关详细信息，请参阅[使用 Azure Monitor 创建、查看和管理指标警报](../azure-monitor/platform/alerts-metric.md)。

## <a name="see-also"></a>请参阅

* [Azure Monitor 中的日志查询入门](../azure-monitor/log-query/get-started-queries.md)
* [在 Azure Monitor 中使用视图设计器创建自定义视图](../azure-monitor/platform/view-designer.md)
