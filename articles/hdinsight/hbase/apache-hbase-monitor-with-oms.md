---
title: 使用 Azure Log Analytics 监视 HBase - Azure HDInsight | Microsoft Docs
description: 使用 Azure Log Analytics 监视 HDInsight HBase 群集。
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: ashishthaps
manager: jhubbard
editor: cgronlun
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 01/22/2018
ms.author: ashishth
ms.openlocfilehash: 12ec60049cdf267834d251c6c927b35e3c363a4e
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/14/2018
ms.locfileid: "34165292"
---
# <a name="monitor-hbase-with-log-analytics"></a>使用 Log Analytics 监视 HBase

HDInsight HBase 监视使用 Azure Log Analytics 从 HDInsight 群集节点收集 HDInsight HBase 性能指标。 Monitor 提供特定于 HBase 的可视化效果、仪表板和工具用于搜索指标，以及创建自定义监视规则和警报。 可以跨多个 Azure 订阅监视多个 HDInsight HBase 群集的指标。

Log Analytics 是 [Azure](../../operations-management-suite/operations-management-suite-overview.md) 中的一个服务，用于监视云和本地环境，使其保持较高的可用性和性能。 Log Analytics 可以收集云和本地环境中的资源生成的数据以及其他监视工具的数据，针对多个源提供分析。

[Log Analytics 管理解决方案](../../log-analytics/log-analytics-add-solutions.md)可将功能添加到 Log Analytics，提供更多数据和分析工具。 Log Analytics 管理解决方案是逻辑、可视化和数据采集规则的集合，用于提供特定区域的指标。 解决方案还可能定义要收集的新记录类型，可以使用日志搜索或新的用户界面功能分析这些记录。

[见解和分析](https://azure.microsoft.com/pricing/details/insight-analytics/)构建在 Log Analytics 平台基础之上。 可以选择使用 Log Analytics 功能，支付引入服务的每个 GB 的费用，或者将工作区切换到“见解和分析”层，支付服务管理的每个节点的费用。 见解和分析提供 Log Analytics 所提供的功能超集。 HBase 监视解决方案可与 Log Analytics 或见解和分析配合使用。

预配 HDInsight HBase 监视解决方案时，需要创建 Log Analytics 工作区。 每个工作区充当独特的 Log Analytics 环境，其中包含自身的数据存储库、数据源和解决方案。 可以在订阅中创建多个工作区来支持生产和测试等多种环境。

## <a name="provision-hdinsight-hbase-monitoring"></a>预配 HDInsight HBase 监视

1. 使用 Azure 订阅登录到 [Azure 门户](https://portal.azure.com)。
2. 在“新建”窗格中的“Marketplace”下，选择“监视 + 管理”。
3. 在“监视 + 管理”窗格中，单击“查看全部”。

    ![“监视 + 管理”窗格](./media/apache-hbase-monitor-with-oms/monitoring-management-blade.png)  

4. 在列表中，找到“管理解决方案”区段。 在“管理解决方案”的右侧，选择“更多”。

    ![“监视 + 管理”窗格](./media/apache-hbase-monitor-with-oms/management-solutions.png) 

5. 在“管理解决方案”窗格中，选择要添加到工作区的 HDInsight HBase 监视管理解决方案。

    ![“管理解决方案”窗格](./media/apache-hbase-monitor-with-oms/hbase-solution.png)  
6. 在管理解决方案窗格中，检查有关管理解决方案的信息，然后选择“创建”。 
7. 在“管理解决方案名称”窗格中，选择要与管理解决方案关联的现有工作区，或创建新的 Log Analytics 工作区并将其选中。
8. 相应地更改 Azure 订阅、资源组和位置的工作区设置。 
    ![解决方案工作区](./media/apache-hbase-monitor-with-oms/solution-workspace.png)  
9. 选择**创建**。  
10. 若要在相应的工作区中使用此新管理解决方案，请导航到“Log Analytics” >  工作区名称  > “解决方案”。 列表中会显示该管理解决方案的条目。 选择该条目导航到该解决方案。

    ![Log Analytics 解决方案](./media/apache-hbase-monitor-with-oms/log-analytics-solutions.png)  

11. 此时会显示 HDInsight HBase 监视解决方案的窗格。

    ![“HDInsight HBase 解决方案”窗格](./media/apache-hbase-monitor-with-oms/hdinsight-hbase-solution.png) 

12. “摘要”磁贴中未显示任何数据，因为尚未将 HDInsight HBase 群集配置为向 Log Analytics 发送数据。

## <a name="connect-hdinsight-hbase-cluster-to-log-analytics"></a>将 HDInsight HBase 群集连接到 Log Analytics

若要使用 HDInsight HBase 监视提供的工具，需要配置群集，让其将指标从其区域服务器、头节点和 ZooKeeper 节点传输到 Log Analytics。 可以针对 HDInsight HBase 群集运行脚本操作来完成此配置。

### <a name="get-log-analytics-workspace-id-and-workspace-key"></a>获取 Log Analytics 工作区 ID 和工作区密钥

需要获取 Log Analytics 工作区 ID 和工作区密钥，才能让群集中的节点在 Log Analytics 中完成身份验证。 若要获取这些值：

1. 在 Azure 门户上的“HBase 监视”窗格中，选择“概述”。

    ![HBase 监视解决方案窗格](./media/apache-hbase-monitor-with-oms/hdinsight-hbase-solution.png) 

2. 选择“OMS 门户”，在新的浏览器标签页或窗口中启动 OMS 门户。

    ![选择“OMS 门户”](./media/apache-hbase-monitor-with-oms/select-oms-portal.png) 

3. 在 OMS 门户主页中，选择“设置”。

    ![OMS 门户](./media/apache-hbase-monitor-with-oms/oms-portal-settings.png) 

4. 选择“连接的源”、“Linux 服务器”。

    ![依次选择“连接的源”、“Linux 服务器”](./media/apache-hbase-monitor-with-oms/select-linux-servers.png) 

5. 请记下显示的工作区 ID 和主密钥值，因为需要在脚本操作中使用这些值。

### <a name="run-the-script-action"></a>运行脚本操作

若要从 HDInsight HBase 群集收集数据，请针对群集中的所有节点运行脚本操作：

1. 在 Azure 门户中导航到 HDInsight HBase 群集的窗格。
2. 选择“脚本操作”。

    ![脚本操作](./media/apache-hbase-monitor-with-oms/script-actions.png) 

3. 选择“提交新项”。

    ![提交新项](./media/apache-hbase-monitor-with-oms/script-actions-submit-new.png)  

4. 在“提交脚本”操作中，将脚本类型设置为 `"- Custom"`。
5. 提供此脚本的名称。
6. 对于“Bash 脚本 URI”，请粘贴以下 URI：

        https://raw.githubusercontent.com/hdinsight/HDInsightOMS/master/monitoring/script2.sh 

7. 对于“节点类型”，请选择所有三个选项（“头”、“区域”和“ZooKeeper”）。
8. 在“参数”文本框中，输入工作区 ID 和工作区密钥。请将每个值括在引号中，并使用空格分隔。

        "<Workspace ID>" "<Workspace Key>"

9. 选择“在将新节点添加到群集时，坚持执行此脚本操作以便重新运行操作”。

    ![脚本操作设置](./media/apache-hbase-monitor-with-oms/submit-script-action.png)  

10. 选择**创建**。
11. 该脚本操作将运行几分钟。 可以在“脚本操作”窗格中监视其状态。

    ![正在运行脚本操作](./media/apache-hbase-monitor-with-oms/script-action-running.png)  

12. 脚本操作完成后，列表中脚本名称的旁边应会显示一个绿色勾选标记。

    ![已完成脚本操作](./media/apache-hbase-monitor-with-oms/script-action-done.png)  

## <a name="view-the-hdinsight-hbase-monitoring-solution"></a>查看 HDInsight HBase 监视解决方案

脚本操作完成后的几分钟内，监视解决方案中应会显示数据。

1. 在 Azure 门户中，导航到 HDInsight HBase 解决方案的窗格。
2. 选择“概述”选项卡。
3. 在“摘要”下可以看到一个磁贴，指示正在监视的区域服务器数目。

    ![监视摘要磁贴](./media/apache-hbase-monitor-with-oms/monitoring-summary-tile.png)  

4. 选择包含区域服务器计数的磁贴。 此时会显示主仪表板。
5. 通过此仪表板可以访问有关区域的统计信息、使用的预写日志 (WAL) 计数、Log Analytics 搜索的集合（例如区域服务器日志、Phoenix 日志和异常），以及用于可视化相关指标的常用图表集合。 

    ![主仪表板](./media/apache-hbase-monitor-with-oms/main-dashboard.png)  

6. 选择其中的任何一项信息可以向下钻取到“日志搜索”视图，在其中可以缩小查询范围，以及更详细地浏览数据。

## <a name="next-steps"></a>后续步骤

* [在 Log Analytics 中创建警报](../../log-analytics/log-analytics-alerts-creating.md)
* [在 Azure Log Analytics 中使用日志搜索查找数据](../../log-analytics/log-analytics-log-searches.md)
