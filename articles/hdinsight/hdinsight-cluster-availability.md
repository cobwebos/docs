---
title: 如何监视群集的 Ambari 和 Azure Monitor 日志可用性
description: 了解如何使用 Ambari 和 Azure Monitor 日志来监视群集运行状况和可用性。
keywords: 监视、 ambari、 监视、 日志分析、 警报、 可用性、 运行状况
ms.reviewer: jasonh
author: tylerfox
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: tyfox
ms.openlocfilehash: 459de569916af14b0efea0ff08b92e5c93ed2369
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2019
ms.locfileid: "64718920"
---
# <a name="how-to-monitor-cluster-availability-with-ambari-and-azure-monitor-logs"></a>如何监视群集的 Ambari 和 Azure Monitor 日志可用性

HDInsight 群集包含 Apache Ambari 中，它提供了快速和预定义的警报的运行状况信息，以及 Azure Monitor 日志集成，它提供可查询指标和日志，以及可配置的警报。

本文档演示如何使用这些工具来监视群集，并指导完成配置 Ambari 警报、 监视节点可用性速率，并创建 Azure Monitor 警报触发时检测信号尚未收到来自一个或多个节点的一些示例在五个小时。

## <a name="ambari"></a>Ambari

### <a name="dashboard"></a>仪表板

可以通过单击访问 Ambari 仪表板**Ambari 家庭**中的链接**群集仪表板**HDInsight 概述边栏选项卡中所示在 Azure 门户中的部分。 或者，可以通过在浏览器中输入以下 URL 访问它[https://\<clustername\>。 azurehdinsight.net](https://clustername.azurehdinsight.net/)

![HDInsight 资源的门户视图](media/hdinsight-cluster-availability/portal-overview.png)

系统然后会提示输入群集登录用户名和密码。 输入的凭据创建群集时选择。

您然后将转到 Ambari 仪表板，其中包含小组件，可显示少量的指标，使你的 HDInsight 群集的运行状况的快速概述。 这些小组件显示度量值，如实时数据节点 （辅助角色节点） 和 JournalNodes （zookeeper 节点） 的数量 NameNodes （头节点） 运行时间为特定于某些群集类型，以及指标，如 Spark 和 Hadoop 群集的 YARN ResourceManager 正常运行时间。

![Ambari 仪表板](media/hdinsight-cluster-availability/ambari-dashboard.png)

### <a name="hosts--view-individual-node-status"></a>主机 – 查看单个节点状态

此外可以查看各个节点的状态信息。 单击**主机**选项卡以查看你的群集中的所有节点的列表并查看有关每个节点的基本信息。 每个节点名称左侧的绿色复选标记指示所有组件都已启动的节点上。 如果组件已关闭节点上，您将看到一个红色警报三角形而不是绿色复选标记。

![Ambari 主机视图](media/hdinsight-cluster-availability/ambari-hosts.png)

您可以单击**名称**的节点可查看更多详细的该特定节点的主机指标。 此视图显示每个组件的状态/可用性。

![Ambari 主机单一节点视图](media/hdinsight-cluster-availability/ambari-hosts-node.png)

### <a name="ambari-alerts"></a>Ambari 警报

Ambari 还提供了几个可配置的警报可提供某些事件的通知。 触发警报后，他们会看到在 Ambari 中包含的警报数旁边的红色标记的左上角。 单击此标记会显示当前警报的列表。

![Ambari 警报的计数](media/hdinsight-cluster-availability/ambari-alerts.png)

若要查看警报定义和及其状态的列表，请单击**警报**选项卡，如下所示。

![Ambari 警报定义视图](media/hdinsight-cluster-availability/ambari-alerts-definitions.png)

Ambari 提供许多预定义的警报与相关的可用性，包括：

| 警报名称                        | 描述                                                                                                                                                                           |
|-----------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| DataNode 运行状况摘要           | 如果存在不正常的 DataNode，则会触发此服务级别的警报                                                                                                                |
| NameNode 高可用性运行状况 | 如果主动 NameNode 或待机 NameNode 未运行，则会触发此服务级别的警报。                                                                              |
| 可用 JournalNode 百分比    | 如果群集中已关闭的 JournalNode 数目大于配置的“严重”阈值，则会触发此警报。 此值聚合了 JournalNode 进程检查的结果。 |
| 可用 DataNode 百分比       | 如果群集中已关闭的 DataNode 数目大于配置的“严重”阈值，则会触发此警报。 此值聚合了 DataNode 进程检查的结果。       |

Ambari 的完整列表警报可以找到群集的可用性监视的帮助[此处](https://docs.microsoft.com/azure/hdinsight/hdinsight-high-availability-linux#ambari-web-ui)，

若要查看警报详细信息或修改条件，请单击**名称**的警报。 采取**DataNode 运行状况摘要**作为示例。 可以看到的警报，以及将触发一个警告或严重警报和条件检查间隔的特定条件的说明。 若要编辑的配置，请单击**编辑**右上角的配置中的按钮。

![Ambari 警报配置](media/hdinsight-cluster-availability/ambari-alert-configuration.png)

在这里，可以编辑说明以及更重要的是，检查时间间隔和警告或严重警报的阈值。

![Ambari 警报配置编辑视图](media/hdinsight-cluster-availability/ambari-alert-configuration-edit.png)

在此示例中，您可以制作 2 个不正常的数据，每个节点都触发严重警报和 1 不正常 DataNode 仅触发一条警告。 单击**保存**完成编辑。

### <a name="email-notifications"></a>电子邮件通知

可以选择性地配置 Ambari 警报的电子邮件通知。 若要执行此操作，如果为 on**警报**选项卡上，单击**操作**按钮在左上方，然后**管理通知。**

![Ambari 管理通知操作](media/hdinsight-cluster-availability/ambari-manage-notifications.png)

将打开一个对话框用于管理警报通知。 单击**+** 底部的对话框和必填字段，Ambari 提供电子邮件服务器从其发送电子邮件的详细信息，请填写。

> [!TIP]
> 设置 Ambari 电子邮件通知可以是管理多个 HDInsight 群集时在一个位置接收警报的好方法。

## <a name="azure-monitor-logs-integration"></a>Azure 监视器日志集成

Azure 监视器将记录生成的多个资源，例如 HDInsight 群集，以先收集和聚合在一个位置以实现统一的监视体验使数据。

作为先决条件，你将需要 Log Analytics 工作区来存储所收集的数据。 如果尚未创建一个，则可以按照此处的说明：[创建 Log Analytics 工作区](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)。

### <a name="enable-hdinsight-azure-monitor-logs-integration"></a>启用 HDInsight Azure Monitor 日志集成

从门户中的 HDInsight 群集资源页，单击**Operations Management Suite**边栏选项卡。 然后，单击**启用**并从下拉列表中选择 Log Analytics 工作区。

![HDInsight Operations Management Suite 边栏选项卡](media/hdinsight-cluster-availability/portal-enable-oms.png)

### <a name="query-metrics-and-logs-tables-in-the-logs-blade"></a>查询日志边栏选项卡中的指标和日志表

一旦启用了 Azure Monitor 日志集成 （这可能需要几分钟时间，） 导航到您**Log Analytics 工作区**资源，然后单击**日志**边栏选项卡

![Log Analytics 工作区中日志边栏选项卡](media/hdinsight-cluster-availability/portal-logs.png)

**日志**边栏选项卡列出多个示例查询，例如：

| 查询名称                      | 描述                                                               |
|---------------------------------|---------------------------------------------------------------------------|
| 今天的计算机可用性    | 图表的计算机发送日志，每小时数                     |
| 列表的检测信号                 | 列出最近一个小时内的所有计算机检测信号                           |
| 每台计算机的最后一个检测信号 | 显示发送的每台计算机的最后一个检测信号                             |
| 不可用的计算机           | 列出过去 5 个小时内未发送检测信号的所有已知的计算机 |
| 可用性速率               | 计算每个连接的计算机的可用性速率                |

例如，运行**可用性速率**通过单击示例查询**运行**基于该查询，如上面的屏幕截图中所示。 这将显示每个节点的可用性速率在群集中所占百分比。 如果已启用多个 HDInsight 群集，以将指标发送到同一个 Log Analytics 工作区，您将看到显示这些群集中的所有节点的可用性速率。

![Log Analytics 工作区中日志边栏选项卡可用性率示例查询](media/hdinsight-cluster-availability/portal-availability-rate.png)

> [!NOTE] 
> 可用性速率度量 24 小时内，因此你的群集需要运行至少 24 小时，才能看到准确的费率。

可以通过单击固定此表与共享仪表板**Pin**在右上角。 如果还没有任何可写共享仪表板，可以了解如何创建一个此处：[创建和共享在 Azure 门户中的仪表板](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards#publish-a-dashboard-and-manage-access-control)。

### <a name="azure-monitor-alerts"></a>Azure 监视器警报

您还可以设置 Azure 监视器将发出警报的触发时间的指标值或查询的结果符合特定条件。 例如，让我们创建一个或多个节点在 5 个小时内未发送检测信号时发送一封电子邮件警报 （即假定为不可用）。

从**日志**边栏选项卡上，运行**不可用的计算机**通过单击示例查询**运行**基于该查询，如下所示。

![Log Analytics 工作区中日志边栏选项卡不可用的计算机示例查询](media/hdinsight-cluster-availability/portal-unavailable-computers.png)

如果所有节点都都可用，此查询应现在返回 0 个结果。 单击**新的警报规则**若要开始配置你为此查询的警报。

![Log Analytics 工作区中新的警报规则](media/hdinsight-cluster-availability/portal-logs-new-alert-rule.png)

有三个组件对警报：*资源*要为其创建规则 （Log Analytics 工作区在此情况下），*条件*触发警报，并*操作组*来确定触发警报时，会发生什么情况。

单击**条件标题**，若要完成配置信号逻辑，如下所示。

![警报规则条件](media/hdinsight-cluster-availability/portal-condition-title.png)

这将打开**配置信号逻辑**边栏选项卡。

设置**警报逻辑**部分，如下所示：

*基于：结果，条件数：大于阈值：0.*

由于此查询仅返回不可用节点作为结果，结果数是否过大于 0，因此应触发警报。

中**评估基于**部分中，设置**段**并**频率**根据你想要检查的不可用节点的频率。

请注意，在此警报，你想要确保**时间段 = 频率。** 可以找到有关时间、 频率和其他警报参数的详细信息[此处](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log#log-search-alert-rule---definition-and-types)。

单击**完成**完后配置信号逻辑。

![警报规则配置信号逻辑](media/hdinsight-cluster-availability/portal-configure-signal-logic.png)

如果你还没有为现有操作组，请单击**创建新**下**操作组**部分。

![警报规则的新操作组](media/hdinsight-cluster-availability/portal-create-new-action-group.png)

这将打开**添加操作组**边栏选项卡。 选择**操作组名称**，**短名称**，**订阅**，和**资源组。** 下**操作**部分中，选择**操作名称**，然后选择**电子邮件/短信/推送/语音**作为**操作类型。**

> [!NOTE]
> 有其他几个操作，除了电子邮件/短信/推送/语音，如 Azure 函数、 逻辑应用、 Webhook、 ITSM 和自动化 Runbook 可触发警报。 [了解更多信息。](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups#action-specific-information)

这将打开**电子邮件/短信/推送/语音**边栏选项卡。 选择**名称**收件人**检查****电子邮件**框，然后键入你希望将警报发送到的电子邮件地址。 单击**确定**中**电子邮件/短信/推送/语音**边栏选项卡，然后在**添加操作组**边栏选项卡中完成配置操作组。

![警报规则添加操作组](media/hdinsight-cluster-availability/portal-add-action-group.png)

这些边栏选项卡关闭后，会看到操作组列入**操作组**部分。 最后，完成**警报详细信息**通过键入部分**警报规则名称**并**说明**，然后选择**严重性**。
单击**创建警报规则**来完成。

![创建警报规则完成](media/hdinsight-cluster-availability/portal-create-alert-rule-finish.png)

> [!TIP]
> 指定的功能**严重性**是一个功能强大的工具，可以创建多个警报时使用。 例如，可以向下创建一个警报发出警告 (严重性 1)，如果单个头节点和两个头节点不可靠事件中引发严重 （严重性级别 0） 的其他警报停机。

当满足此警报的条件时，将触发警报，您将收到包含如下警报的详细信息的电子邮件：

![Azure 监视器警报电子邮件](media/hdinsight-cluster-availability/alert-email.png)

您还可以查看已触发，按严重性分组，通过转到的所有警报**警报**边栏选项卡中的你**Log Analytics 工作区**。

![Log Analytics 工作区警报](media/hdinsight-cluster-availability/portal-alerts.png)

单击严重性分组 (即**严重性级别 1**上面突出显示为) 将显示为已不再像下面的严重性的所有警报的记录：

![Log Analytics 工作区严重性 1 警报](media/hdinsight-cluster-availability/portal-alerts-sev-1.png)

## <a name="next-steps"></a>后续步骤
- [在 HDInsight 中 Apache Hadoop 群集的可用性和可靠性](hdinsight-high-availability-linux.md)
