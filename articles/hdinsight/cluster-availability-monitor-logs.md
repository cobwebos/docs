---
title: 如何在 HDInsight 中使用 Azure Monitor 日志监视群集可用性
description: 了解如何使用 Azure Monitor 日志监视群集的运行状况与可用性。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.date: 08/12/2020
ms.openlocfilehash: 19e3f1a157ee2c042dfebfc96c9b51c3c4698ebc
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2020
ms.locfileid: "88163724"
---
# <a name="how-to-monitor-cluster-availability-with-azure-monitor-logs-in-hdinsight"></a>如何在 HDInsight 中使用 Azure Monitor 日志监视群集可用性

HDInsight 群集包括 Azure Monitor 日志集成，它提供可查询的指标和日志，以及可配置的警报。 本文介绍如何使用 Azure Monitor 来监视群集。

## <a name="azure-monitor-logs-integration"></a>Azure Monitor 日志集成

使用 Azure Monitor 日志可在一个位置收集与聚合多个资源（例如 HDInsight 群集）生成的数据，以实现统一监视体验。

作为先决条件，需要创建一个 Log Analytics 工作区来存储收集的数据。 如果尚未创建，可按照以下说明创建：[创建 Log Analytics 工作区](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)。

## <a name="enable-hdinsight-azure-monitor-logs-integration"></a>启用 HDInsight Azure Monitor 日志集成

在门户上的 HDInsight 群集资源页中，选择“Azure Monitor”。**** 然后选择“启用”并从下拉列表中选择你的 Log Analytics 工作区。****

![HDInsight Operations Management Suite](media/cluster-availability-monitor-logs/azure-portal-monitoring.png)

默认情况下，会在除边缘节点以外的所有群集节点上安装 OMS 代理。 由于未在群集边缘节点上安装 OMS 代理，因此默认情况下，Log Analytics 中没有针对边缘节点的遥测。

## <a name="query-metrics-and-logs-tables"></a>查询指标和日志表

启用 Azure Monitor 日志集成后（这可能需要几分钟时间），导航到“Log Analytics 工作区”资源并选择“日志”。**** ****

![Log Analytics 工作区日志](media/cluster-availability-monitor-logs/hdinsight-portal-logs.png)

日志列出了多个示例查询，例如：

| 查询名称                      | 说明                                                               |
|---------------------------------|---------------------------------------------------------------------------|
| 目前的计算机可用性    | 每小时绘制发送日志的计算机数的图表                     |
| 列出检测信号                 | 列出过去一小时的所有计算机检测信号                           |
| 每台计算机的最后一个检测信号 | 显示每台计算机发送的最后一个检测信号                             |
| 不可用的计算机           | 列出过去 5 小时未发送检测信号的所有已知计算机 |
| 可用率               | 计算每台已连接计算机的可用率                |

例如，选择该查询对应的“运行”以运行“可用率”示例查询，如以上屏幕截图中所示。**** **** 这会以百分比显示群集中每个节点的可用率。 如果已启用多个 HDInsight 群集来将指标发送到相同的 Log Analytics 工作区，则会看到所有节点的可用性费率 (排除显示的那些群集中) 边缘节点。

![Log Analytics 工作区日志的“可用率”示例查询](media/cluster-availability-monitor-logs/portal-availability-rate.png)

> [!NOTE]  
> 可用率是按 24 小时期限测量的，因此，群集至少需要运行 24 小时才能显示准确的可用率。

可以单击右上角的“固定”将此表固定到共享仪表板。**** 如果没有任何可写的共享仪表板，可在以下文章中了解如何创建共享仪表板：[在 Azure 门户中创建和共享仪表板](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards#publish-and-share-a-dashboard)。

## <a name="azure-monitor-alerts"></a>Azure Monitor 警报

还可以设置当某个指标的值或某个查询的结果符合特定条件时要触发的 Azure Monitor 警报。 例如，让我们创建一个警报，以便在一个或多个节点在 5 小时内未发送检测信号时（即，假设这些节点不可用）发送电子邮件。

在“日志”中，选择该查询对应的“运行”以运行“不可用的计算机”示例查询，如下所示。**** **** ****

![Log Analytics 工作区日志中的“不可用的计算机”示例](media/cluster-availability-monitor-logs/portal-unavailable-computers.png)

如果所有节点可用，此查询应返回零个结果。 单击“新建警报规则”开始为此查询配置警报。****

![Log Analytics 工作区 - 新建警报规则](media/cluster-availability-monitor-logs/portal-logs-new-alert-rule.png)

警报有三个组成部分：要为其创建规则的资源（在本例中为 Log Analytics 工作区）、触发该警报的条件，以及确定触发警报时发生的操作的操作组。** ** **
单击如下所示的**条件标题**完成信号逻辑配置。

![门户警报 - 创建规则条件](media/cluster-availability-monitor-logs/portal-condition-title.png)

此时会打开“配置信号逻辑”。****

按如下所示设置“警报逻辑”部分：****

*依据:结果数，条件:大于，阈值:0。*

由于此查询只返回不可用的节点作为结果，如果结果数大于 0，应会激发警报。

在“计算依据”部分设置“时段”，并根据检查不可用节点的频率设置“频率”。**** **** ****

对于此警报，请确保“时段”值与“频率”值相同。**** 可在[此处](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log#log-search-alert-rule---definition-and-types)找到有关时段、频率和其他警报参数的详细信息。

完成信号逻辑配置后，选择“完成”。****

![警报规则 - 配置信号逻辑](media/cluster-availability-monitor-logs/portal-configure-signal-logic.png)

如果没有现有的操作组，请单击“操作组”部分下的“新建”。**** ****

![警报规则 - 创建新的操作组](media/cluster-availability-monitor-logs/portal-create-new-action-group.png)

此时会打开“添加操作组”。**** 选择**操作组名称**、**短名称**、**订阅**和**资源组**。 在“操作”部分下，选择一个**操作名称**，并选择“电子邮件/短信/推送/语音”作为“操作类型”。**** **** ****

> [!NOTE]
> 除了“电子邮件/短信/推送/语音”以外，警报还可以触发其他几个操作，例如 Azure 函数、逻辑应用、Webhook、ITSM 和自动化 Runbook。 [了解详细信息。](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups#action-specific-information)

此时会打开“电子邮件/短信/推送/语音”。**** 选择收件人的**姓名**，**选中**“电子邮件”框，然后键入要将警报发送到的电子邮件地址。**** 在“电子邮件/短信/推送/语音”中选择“确定”，然后在“添加操作组”中完成操作组的配置。**** **** ****

![创建警报规则 - 添加操作组](media/cluster-availability-monitor-logs/portal-add-action-group.png)

关闭这些边栏选项卡后，应会看到你的操作组已列在“操作组”部分下。**** 最后，键入**警报规则名称**和**说明**并选择一种**严重性**来完成“警报详细信息”部分中的操作。**** 单击“创建警报规则”以完成操作。****

![门户 - 完成创建警报规则](media/cluster-availability-monitor-logs/portal-create-alert-rule-finish.png)

> [!TIP]
> 指定“严重性”是一个强大的功能，可在创建多个警报时使用它。**** 例如，可以创建一个警报以便在一个头节点出现故障时引发“警告”警报（严重性 1），并创建另一个警报以便在两个头节点同时出现故障时（这种情况很少见）引发“严重”警报（严重性 0）。

如果符合此警报的条件，则会激发该警报，你会收到一封电子邮件，其中包含如下所示的警报详细信息：

![Azure Monitor 警报电子邮件示例](media/cluster-availability-monitor-logs/portal-oms-alert-email.png)

还可以转到 **Log Analytics 工作区**中的“警报”，查看所有已激发的警报（按严重性分组）。****

![Log Analytics 工作区警报](media/cluster-availability-monitor-logs/hdi-portal-oms-alerts.png)

选择某个严重性分组（例如，上图中突出显示的“严重性 1”）会显示具有该严重性的所有已激发警报的记录，如下所示：****

![Log Analytics 工作区严重性 1 警报](media/cluster-availability-monitor-logs/portal-oms-alerts-sev1.png)

## <a name="next-steps"></a>后续步骤

* [群集可用性 - Apache Ambari](./hdinsight-cluster-availability.md)
* [使用 Azure Monitor 日志](hdinsight-hadoop-oms-log-analytics-tutorial.md)
