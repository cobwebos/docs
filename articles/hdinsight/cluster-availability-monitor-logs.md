---
title: 如何在 HDInsight 中通过 Azure Monitor 日志监视群集可用性
description: 了解如何使用 Azure Monitor 日志来监视群集的运行状况和可用性。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/01/2020
ms.openlocfilehash: 5527a27ab94c350a345bb916206057069c3d16da
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2020
ms.locfileid: "82723163"
---
# <a name="how-to-monitor-cluster-availability-with-azure-monitor-logs-in-hdinsight"></a>如何在 HDInsight 中通过 Azure Monitor 日志监视群集可用性

HDInsight 群集包括 Azure Monitor 日志集成，提供查询指标和日志以及可配置的警报。 本文介绍如何使用 Azure Monitor 来监视群集。

## <a name="azure-monitor-logs-integration"></a>Azure Monitor 日志集成

使用 Azure Monitor 日志，可以在一个位置收集和聚合多个资源（例如 HDInsight 群集）生成的数据，以实现统一的监视体验。

作为先决条件，你需要一个 Log Analytics 工作区来存储所收集的数据。 如果尚未创建一个，可以按照此处的说明操作：[创建 Log Analytics 工作区](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)。

## <a name="enable-hdinsight-azure-monitor-logs-integration"></a>启用 HDInsight Azure Monitor 日志集成

在门户中的 "HDInsight 群集资源" 页上，选择**Azure Monitor**。 然后，选择 "**启用**"，然后从下拉 Log Analytics 选择 "工作区"。

![HDInsight Operations Management Suite](media/cluster-availability-monitor-logs/azure-portal-monitoring.png)

## <a name="query-metrics-and-logs-tables"></a>查询指标和日志表

启用 Azure Monitor 日志集成后（这可能需要几分钟的时间），请导航到**Log Analytics 工作区**资源，然后选择 "**日志**"。

![Log Analytics 工作区日志](media/cluster-availability-monitor-logs/hdinsight-portal-logs.png)

日志列出了多个示例查询，例如：

| 查询名称                      | 说明                                                               |
|---------------------------------|---------------------------------------------------------------------------|
| 目前的计算机可用性    | 每小时绘制发送日志的计算机数的图表                     |
| 列出检测信号                 | 列出过去一小时的所有计算机检测信号                           |
| 每台计算机的最后一个检测信号 | 显示每台计算机发送的最后一个检测信号                             |
| 不可用的计算机           | 列出过去 5 小时未发送检测信号的所有已知计算机 |
| 可用率               | 计算每台已连接计算机的可用率                |

例如，通过在该查询上选择 "**运行**" 来运行**可用性速率**示例查询，如上面的屏幕截图中所示。 这会以百分比显示群集中每个节点的可用率。 如果已启用多个 HDInsight 群集以将指标发送到相同的 Log Analytics 工作区，则会看到显示的这些群集中所有节点的可用性费率。

![Log Analytics 工作区日志 "可用性率" 示例查询](media/cluster-availability-monitor-logs/portal-availability-rate.png)

> [!NOTE]  
> 可用率是按 24 小时期限测量的，因此，群集至少需要运行 24 小时才能显示准确的可用率。

可以单击右上角的“固定”将此表固定到共享仪表板。**** 如果没有任何可写共享仪表板，可以在此处查看如何创建仪表板：[在 Azure 门户中创建和共享仪表板](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards#publish-and-share-a-dashboard)。

## <a name="azure-monitor-alerts"></a>Azure Monitor 警报

还可以设置当某个指标的值或某个查询的结果符合特定条件时要触发的 Azure Monitor 警报。 例如，让我们创建一个警报，以便在一个或多个节点在5小时内未发送检测信号（即假设为不可用时）发送电子邮件。

从**日志**中，通过选择 "**运行**" 查询来运行 "**不可用计算机**" 示例查询，如下所示。

![Log Analytics 工作区日志 "不可用计算机" 示例](media/cluster-availability-monitor-logs/portal-unavailable-computers.png)

如果所有节点都可用，此查询现在应返回零结果。 单击“新建警报规则”开始为此查询配置警报。****

![Log Analytics 工作区 - 新建警报规则](media/cluster-availability-monitor-logs/portal-logs-new-alert-rule.png)

警报有三个组成部分：要为其创建规则的资源（在本例中为 Log Analytics 工作区）、触发该警报的条件，以及确定触发警报时发生的操作的操作组。******
单击如下所示的**条件标题**完成信号逻辑配置。

![门户警报创建规则条件](media/cluster-availability-monitor-logs/portal-condition-title.png)

这将打开 "**配置信号逻辑**"。

按如下所示设置“警报逻辑”部分：****

*基于：结果数，条件：大于，阈值：0。*

由于此查询只返回不可用的节点作为结果，如果结果数大于 0，应会激发警报。

在“计算依据”部分设置“时段”，并根据检查不可用节点的频率设置“频率”。************

对于此警报，需要确保**Period = Frequency。** 可在[此处](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log#log-search-alert-rule---definition-and-types)找到有关时段、频率和其他警报参数的详细信息。

完成配置信号逻辑后，选择 "**完成**"。

![警报规则配置信号逻辑](media/cluster-availability-monitor-logs/portal-configure-signal-logic.png)

如果还没有现有操作组，请单击 "**操作组**" 部分下的 "**新建**"。

![警报规则创建新的操作组](media/cluster-availability-monitor-logs/portal-create-new-action-group.png)

这将打开 "**添加操作组**"。 选择**操作组名称**、**短名称**、**订阅**和**资源组**。 在“操作”部分下，选择一个**操作名称**，并选择“电子邮件/短信/推送/语音”作为“操作类型”。************

> [!NOTE]
> 除了“电子邮件/短信/推送/语音”以外，警报还可以触发其他几个操作，例如 Azure 函数、逻辑应用、Webhook、ITSM 和自动化 Runbook。 [了解更多信息。](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups#action-specific-information)

这将打开**电子邮件/短信/推送/语音**。 选择收件人的**姓名**，**选中**“电子邮件”框，然后键入要将警报发送到的电子邮件地址。**** 在**电子邮件/短信/推送/语音**中选择 **"确定"** ，然后在 "**添加操作组**" 中完成配置操作组。

![警报规则创建添加操作组](media/cluster-availability-monitor-logs/portal-add-action-group.png)

关闭这些边栏选项卡后，应会看到你的操作组已列在“操作组”部分下。**** 最后，键入**警报规则名称**和**说明**并选择一种**严重性**来完成“警报详细信息”部分中的操作。**** 单击“创建警报规则”以完成操作。****

![门户创建预警规则完成](media/cluster-availability-monitor-logs/portal-create-alert-rule-finish.png)

> [!TIP]
> 指定“严重性”是一个强大的功能，可在创建多个警报时使用它。**** 例如，可以创建一个警报以便在一个头节点出现故障时引发“警告”警报（严重性 1），并创建另一个警报以便在两个头节点同时出现故障时（这种情况很少见）引发“严重”警报（严重性 0）。

当满足此警报的条件时，将触发警报，你会收到一封包含警报详细信息的电子邮件，如下所示：

![Azure Monitor 警报电子邮件示例](media/cluster-availability-monitor-logs/portal-oms-alert-email.png)

你还可以通过转到**Log Analytics 工作区**中的**警报**，查看所有已触发的警报，按严重性分组。

![Log Analytics 工作区警报](media/cluster-availability-monitor-logs/hdi-portal-oms-alerts.png)

在严重性分组（即上面突出显示的**严重性 1** ）上选择会显示如下所示严重性的所有警报的记录：

![Log Analytics 工作区严重性一个警报](media/cluster-availability-monitor-logs/portal-oms-alerts-sev1.png)

## <a name="next-steps"></a>后续步骤

* [群集可用性 - Apache Ambari](./hdinsight-cluster-availability.md)
* [使用 Azure Monitor 日志](hdinsight-hadoop-oms-log-analytics-tutorial.md)