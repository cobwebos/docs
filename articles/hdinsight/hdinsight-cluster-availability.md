---
title: 监视： Apache Ambari & Azure Monitor 日志-Azure HDInsight
description: 了解如何使用 Ambari 和 Azure Monitor 日志来监视群集的运行状况和可用性。
keywords: 监视、ambari、监视、log analytics、警报、可用性和运行状况
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/28/2019
ms.openlocfilehash: eeaef8851035bbb8d2f39bcf9f366118545fcf0f
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73044486"
---
# <a name="how-to-monitor-cluster-availability-with-ambari-and-azure-monitor-logs"></a>如何通过 Ambari 和 Azure Monitor 日志监视群集可用性

HDInsight 群集同时包含 Apache Ambari，它提供运行状况信息和预定义的警报，以及 Azure Monitor 日志集成，提供查询指标和日志以及可配置的警报。

本文档演示如何使用这些工具来监视群集并演练一些示例，以配置 Ambari 警报、监视节点可用性率，并创建在尚未收到来自一个或多个节点的检测信号时触发的 Azure Monitor 警报5小时。

## <a name="ambari"></a>Ambari

### <a name="dashboard"></a>仪表板

可以通过单击 Azure 门户中 "HDInsight 概述" 边栏选项卡的 "**群集仪表板**" 部分中的 " **Ambari" 主页**链接来访问 Ambari 仪表板，如下所示。 或者，可以通过在浏览器中输入以下 URL 来访问该[https://\<clustername\>. azurehdinsight.net](https://clustername.azurehdinsight.net/)

![HDInsight 资源门户视图](media/hdinsight-cluster-availability/portal-oms-overview1.png)

系统将提示你输入群集登录用户名和密码。 输入创建群集时选择的凭据。

然后，你将转到 Ambari 仪表板，其中包含的小组件显示了一些指标，可让你快速了解 HDInsight 群集的运行状况。 这些小组件显示诸如实时 DataNodes （辅助节点）和 Journalnode （zookeeper 节点）、Namenode （头节点）运行时间等指标，以及特定于特定群集类型的指标，例如 Spark 和 Hadoop 群集的 YARN ResourceManager 运行时间。

![Apache Ambari 使用仪表板显示](media/hdinsight-cluster-availability/apache-ambari-dashboard.png)

### <a name="hosts--view-individual-node-status"></a>主机–查看各个节点状态

你还可以查看各个节点的状态信息。 单击 "**主机**" 选项卡以查看群集中所有节点的列表，并查看有关每个节点的基本信息。 每个节点名称左侧的绿色复选标记指示节点上的所有组件都已打开。 如果某个组件在某个节点上关闭，您将看到一个红色的警报三角形，而不是绿色复选标记。

![HDInsight Apache Ambari 主机视图](media/hdinsight-cluster-availability/apache-ambari-hosts1.png)

然后，可以单击节点的**名称**，查看该特定节点的更详细的主机指标。 此视图显示每个组件的状态/可用性。

![Apache Ambari 托管单节点视图](media/hdinsight-cluster-availability/apache-ambari-hosts-node.png)

### <a name="ambari-alerts"></a>Ambari 警报

Ambari 还提供多个可配置的警报，这些警报可提供特定事件的通知。 触发警报时，警报将显示在 Ambari 的左上角，其中包含警报数量的红色标记。 单击此徽章将显示当前警报的列表。

![Apache Ambari 当前警报计数](media/hdinsight-cluster-availability/apache-ambari-alerts.png)

若要查看警报定义及其状态的列表，请单击 "**警报**" 选项卡，如下所示。

![Ambari 警报定义视图](media/hdinsight-cluster-availability/ambari-alerts-definitions.png)

Ambari 提供了许多与可用性相关的预定义警报，其中包括：

| 警报名称                        | 描述                                                                                                                                                                           |
|-----------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| DataNode 运行状况摘要           | 如果有不正常的 DataNodes，则会触发此服务级别警报                                                                                                                |
| NameNode 高可用性运行状况 | 如果 Active NameNode 或待机 NameNode 未运行，则会触发此服务级别警报。                                                                              |
| 可用 Journalnode 百分比    | 如果群集中的 down Journalnode 数量大于配置的严重阈值，则会触发此警报。 它聚合了 JournalNode 进程检查的结果。 |
| 可用 DataNodes 百分比       | 如果群集中的 down DataNodes 数量大于配置的严重阈值，则会触发此警报。 它聚合了 DataNode 进程检查的结果。       |

可在[此处](https://docs.microsoft.com/azure/hdinsight/hdinsight-high-availability-linux#ambari-web-ui)找到帮助监视群集可用性的 Ambari 警报的完整列表。

若要查看警报或修改条件的详细信息，请单击该警报的**名称**。 以**DataNode 运行状况摘要**为例。 你可以查看警报的说明，以及将触发 "警告" 或 "严重" 警报的特定条件，以及条件的检查间隔。 若要编辑配置，请单击 "配置" 框右上角的 "**编辑**" 按钮。

![Apache Ambari 警报配置](media/hdinsight-cluster-availability/ambari-alert-configuration.png)

在此处，可以编辑说明，更重要的是警告或严重警报的检查间隔和阈值。

![Ambari 警报配置编辑视图](media/hdinsight-cluster-availability/ambari-alert-configuration-edit.png)

在此示例中，你可以使2个不正常的 DataNodes 触发器成为关键警报，1个不正常的 DataNode 只触发警告。 完成编辑后，单击 "**保存**"。

### <a name="email-notifications"></a>电子邮件通知

你还可以选择为 Ambari 警报配置电子邮件通知。 为此，请在 "**警报**" 选项卡上，单击左上角的 "**操作**" 按钮，然后单击 "**管理通知"。**

![Ambari 管理通知操作](media/hdinsight-cluster-availability/ambari-manage-notifications.png)

将打开用于管理警报通知的对话框。 单击对话框底部的 " **+** ，填写必填字段，为 Ambari 提供电子邮件服务器的详细信息，以便发送电子邮件。

> [!TIP]
> 设置 Ambari 电子邮件通知可以是在管理多个 HDInsight 群集时在一个位置接收警报的好方法。

## <a name="azure-monitor-logs-integration"></a>Azure Monitor 日志集成

利用 Azure Monitor 日志，可以在一个位置收集和聚合多个资源（例如 HDInsight 群集）生成的数据，以实现统一的监视体验。

作为先决条件，你将需要 Log Analytics 工作区来存储所收集的数据。 如果尚未创建一个工作区，则可以按照以下说明进行操作：[创建 Log Analytics 工作区](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)。

### <a name="enable-hdinsight-azure-monitor-logs-integration"></a>启用 HDInsight Azure Monitor 日志集成

在门户中的 "HDInsight 群集资源" 页上，单击 " **Operations Management Suite** " 边栏选项卡。 然后单击 "**启用**"，然后从下拉列表中选择 Log Analytics 工作区。

![HDInsight Operations Management Suite 边栏选项卡](media/hdinsight-cluster-availability/hdi-portal-oms-enable.png)

### <a name="query-metrics-and-logs-tables-in-the-logs-blade"></a>"日志" 边栏选项卡中的查询指标和日志表

启用 Azure Monitor 日志集成后（这可能需要几分钟的时间），导航到**Log Analytics 工作区**资源，并单击 "**日志**" 边栏选项卡

![Log Analytics 工作区日志 "边栏选项卡](media/hdinsight-cluster-availability/hdinsight-portal-logs.png)

"**日志**" 边栏选项卡列出了几个示例查询，例如：

| 查询名称                      | 描述                                                               |
|---------------------------------|---------------------------------------------------------------------------|
| 目前的计算机可用性    | 图表每小时发送日志的计算机数                     |
| 列出检测信号                 | 列出最近一小时内的所有计算机检测信号                           |
| 每台计算机的最后一个检测信号 | 显示每台计算机发送的最后一个检测信号                             |
| 不可用计算机           | 列出最近5小时内未发送检测信号的所有已知计算机 |
| 可用性比率               | 计算每台连接计算机的可用性比率                |

例如，通过单击该查询上的 "**运行**" 运行 "**可用性率**示例" 查询，如上面的屏幕截图中所示。 这会显示群集中每个节点的可用性率（以百分比表示）。 如果已启用多个 HDInsight 群集以将指标发送到相同的 Log Analytics 工作区，则会看到显示的这些群集中所有节点的可用性费率。

![Log Analytics 工作区日志边栏选项卡的 "可用性率" 示例查询](media/hdinsight-cluster-availability/portal-availability-rate.png)

> [!NOTE] 
> 可用性比率以24小时为单位进行测量，因此，你的群集将需要至少24小时运行，然后才能看到准确的可用性费率。

你可以通过单击右上角的 "**固定**" 将此表固定到共享仪表板。 如果没有任何可写的共享仪表板，可以在此处查看如何创建仪表板：[在 Azure 门户中创建和共享仪表板](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards#publish-and-share-a-dashboard)。

### <a name="azure-monitor-alerts"></a>Azure Monitor 警报

你还可以设置 Azure Monitor 警报，当指标值或查询结果满足某些条件时将触发这些警报。 例如，让我们创建一个警报，以便在一个或多个节点在5小时内未发送检测信号（即假设为不可用时）发送电子邮件。

在 "**日志**" 边栏选项卡中，单击该查询上的 "**运行**" 以运行 "**不可用计算机**" 示例查询，如下所示。

![Log Analytics 工作区日志 "边栏选项卡" 不可用计算机 "示例](media/hdinsight-cluster-availability/portal-unavailable-computers.png)

如果所有节点都可用，此查询现在应返回0个结果。 单击 "**新建警报规则**" 开始为此查询配置警报。

![Log Analytics 工作区新建警报规则](media/hdinsight-cluster-availability/portal-logs-new-alert-rule.png)

有三个警报组件：要为其创建规则的*资源*（在本例中为 "Log Analytics" 工作区）、触发警报的*条件*，*以及用于确定*当警报激发.

单击 "**条件标题**" （如下所示）以完成信号逻辑配置。

![门户警报创建规则条件](media/hdinsight-cluster-availability/portal-condition-title.png)

这会打开 "**配置信号逻辑**" 边栏选项卡。

设置**警报逻辑**部分，如下所示：

*基于：结果数，条件：大于，阈值：0。*

由于此查询只返回不可用的节点作为结果，如果结果数超过0，则会触发警报。

在 "**计算依据**" 部分中，根据要检查不可用节点的频率，设置**时间段**和**频率**。

请注意，出于此警报的目的，您需要确保**Period = Frequency。** 可在[此处](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log#log-search-alert-rule---definition-and-types)找到有关期间、频率和其他警报参数的详细信息。

完成配置信号逻辑后，请单击 "**完成**"。

![警报规则配置信号逻辑](media/hdinsight-cluster-availability/portal-configure-signal-logic.png)

如果还没有现有操作组，请单击 "**操作组**" 部分下的 "**新建**"。

![警报规则创建新操作组](media/hdinsight-cluster-availability/portal-create-new-action-group.png)

这将打开 "**添加操作组**" 边栏选项卡。 选择**操作组名称**、**短名称**、**订阅**和**资源组。** 在 "**操作**" 部分下，选择 "**操作名称**" 并选择 "**电子邮件/短信/推送/语音**" 作为 "**操作类型"。**

> [!NOTE]
> 除了电子邮件/短信/推送/语音（如 Azure Function、逻辑应用、Webhook、ITSM 和自动化 Runbook）以外，还可以执行其他一些操作。 [了解更多信息。](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups#action-specific-information)

这将打开**电子邮件/短信/推送/语音**边栏选项卡。 选择收件人的**名称**，**选中**"**电子邮件**" 框，然后键入要向其发送警报的电子邮件地址。 在**电子邮件/短信/推送/语音**边栏选项卡中单击 **"确定"** ，然后在 "**添加操作组**" 边栏选项卡中完成操作组的配置。

![警报规则创建添加操作组](media/hdinsight-cluster-availability/portal-add-action-group.png)

关闭这些边栏选项卡后，应会看到 "**操作**组" 部分下列出的操作组。 最后，通过键入**警报规则名称**和**说明**并选择**严重性**来完成 "**警报详细信息**" 部分。
单击 "**创建警报规则**" 完成操作。

![门户创建预警规则完成](media/hdinsight-cluster-availability/portal-create-alert-rule-finish.png)

> [!TIP]
> 指定**严重性**的功能是一种功能强大的工具，可在创建多个警报时使用。 例如，如果一个头节点出现故障，则可以创建一个警报，以便在这两个头节点出现故障的情况下引发警告（严重性1）。

当满足此警报的条件时，将触发警报，你会收到一封包含警报详细信息的电子邮件，如下所示：

![Azure Monitor 警报电子邮件示例](media/hdinsight-cluster-availability/portal-oms-alert-email.png)

你还可以通过转到**Log Analytics 工作区**中的 "**警报**" 边栏选项卡，查看所有已触发的警报，按严重性分组。

![Log Analytics 工作区警报](media/hdinsight-cluster-availability/hdi-portal-oms-alerts.png)

单击 "严重性" 分组（即上面突出显示的 "**严重性 1"** ）将显示此严重性的所有警报的记录，如下所示：

![Log Analytics 工作区严重性1警报](media/hdinsight-cluster-availability/portal-oms-alerts-sev1.png)

## <a name="next-steps"></a>后续步骤
- [HDInsight 中 Apache Hadoop 群集的可用性和可靠性](hdinsight-high-availability-linux.md)
