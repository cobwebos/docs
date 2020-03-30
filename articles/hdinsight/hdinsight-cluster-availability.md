---
title: 监视：阿帕奇·安巴里& Azure 监视器日志 - Azure HDInsight
description: 了解如何使用 Ambari 和 Azure Monitor 日志监视群集的运行状况与可用性。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/06/2020
ms.openlocfilehash: 383366fa3e436c79bed28a7c47f1e9daa5f0d9de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77060162"
---
# <a name="how-to-monitor-cluster-availability-with-apache-ambari-and-azure-monitor-logs"></a>如何使用 Apache Ambari 和 Azure 监视器日志监视群集可用性

HDInsight 群集包含 Apache Ambari（提供运行状况信息概览和预定义的警报）与 Azure Monitor 日志集成（提供可查询的指标和日志以及可配置的警报）。

本文档演示如何使用这些工具监视群集，并演练一些示例，以配置 Ambari 警报、监视节点可用性速率以及创建 Azure 监视器警报，当一个或多个节点未收到检测信号时触发在五个小时。

## <a name="ambari"></a>Ambari

### <a name="dashboard"></a>仪表板

通过在 Azure 门户中的 HDInsight 概述的**群集仪表板**部分中选择**Ambari 主页**链接，可以访问 Ambari 仪表板，如下所示。 或者，可以通过`https://CLUSTERNAME.azurehdinsight.net`在浏览器中导航来访问它，其中 CLUSTERNAME 是群集的名称。

![HDInsight 资源门户视图](media/hdinsight-cluster-availability/azure-portal-dashboard-ambari.png)

然后系统会提示您输入群集登录用户名和密码。 请输入创建群集时选择的凭据。

然后，您将被带到 Ambari 仪表板，该仪表板包含显示少量指标的小部件，以便快速概览 HDInsight 群集的运行状况。 这些小组件显示活动的 DataNode（工作器节点）和 JournalNode（zookeeper 节点）数目、NameNode（头节点）运行时间等指标，以及特定群集类型的指标，例如 Spark 和 Hadoop 群集的 YARN ResourceManager 运行时间。

![阿帕奇·安巴里使用仪表板显示](media/hdinsight-cluster-availability/apache-ambari-dashboard.png)

### <a name="hosts--view-individual-node-status"></a>主机 – 查看单个节点的状态

还可以查看单个节点的状态信息。 选择 **"主机"** 选项卡以查看群集中所有节点的列表，并查看有关每个节点的基本信息。 每个节点名称左侧的绿色勾选标记表示该节点上的所有组件已运行。 如果组件位于节点上，您将看到一个红色警报三角形，而不是绿色检查。

![HDInsight 阿帕奇安巴里主机视图](media/hdinsight-cluster-availability/apache-ambari-hosts1.png)

然后，您可以在节点**的名称**上选择以查看该特定节点的更详细的主机指标。 此视图显示每个组件的状态/可用性。

![阿帕奇·安巴里托管单节点视图](media/hdinsight-cluster-availability/apache-ambari-hosts-node.png)

### <a name="ambari-alerts"></a>Ambari 警报

Ambari 还提供多个可配置的警报来针对特定的事件发出通知。 触发警报时，警报显示在 Ambari 的左上角，红色徽章包含警报数。 选择此徽章将显示当前警报的列表。

![阿帕奇·安巴里电流警报计数](media/hdinsight-cluster-availability/apache-ambari-alerts.png)

要查看警报定义及其状态的列表，请选择"**警报"** 选项卡，如下所示。

![Ambari 警报定义视图](media/hdinsight-cluster-availability/ambari-alerts-definitions.png)

Ambari 提供许多与可用性相关的预定义警报，其中包括：

| 警报名称                        | 描述   |
|---|---|
| DataNode 运行状况摘要           | 如果存在不正常的 DataNode，则会触发此服务级别的警报|
| NameNode 高可用性运行状况 | 如果活动 NameNode 或备用 NameNode 未运行，则触发此服务级别警报。|
| 可用 JournalNode 百分比    | 如果群集中已关闭的 JournalNode 数目大于配置的“严重”阈值，则会触发此警报。 此值聚合了 JournalNode 进程检查的结果。 |
| 可用 DataNode 百分比       | 如果群集中已关闭的 DataNode 数目大于配置的“严重”阈值，则会触发此警报。 此值聚合了 DataNode 进程检查的结果。|

可在[此处](https://docs.microsoft.com/azure/hdinsight/hdinsight-high-availability-linux#ambari-web-ui)找到帮助监视群集可用性的完整 Ambari 警报列表。

要查看警报的详细信息或修改条件，请选择警报**的名称**。 以“DataNode 运行状况摘要”为例。**** 您可以查看警报的说明以及将触发"警告"或"严重"警报的特定条件以及条件的检查间隔。 要编辑配置，请选择"配置"框右上角的 **"编辑**"按钮。

![阿帕奇安巴里警报配置](media/hdinsight-cluster-availability/ambari-alert-configuration.png)

在此处可以编辑说明，更重要的是，可以编辑警告或严重警报的检查间隔和阈值。

![Ambari 警报配置编辑视图](media/hdinsight-cluster-availability/ambari-alert-configuration-edit.png)

在此示例中，可以设置为存在 2 个不正常的 DataNode 时触发严重警报，存在 1 个不正常的 DataNode 时只触发警告。 完成编辑后选择 **"保存**"。

### <a name="email-notifications"></a>电子邮件通知

还可以选择为 Ambari 警报配置电子邮件通知。 为此，请单击“警报”选项卡左上角的“操作”按钮，然后单击“管理通知”。************

![Ambari“管理通知”操作](media/hdinsight-cluster-availability/ambari-manage-notifications.png)

此时会打开一个用于管理警报通知的对话框。 **+** 选择对话框底部的，并填写所需的字段，向 Ambari 提供发送电子邮件的电子邮件服务器详细信息。

> [!TIP]
> 设置 Ambari 电子邮件通知后，在管理多个 HDInsight 群集时，可以方便地在一个位置接收警报。

## <a name="azure-monitor-logs-integration"></a>Azure Monitor 日志集成

Azure 监视器日志允许将多个资源（如 HDInsight 群集）生成的数据收集和聚合到一个位置，以实现统一的监视体验。

作为先决条件，您需要一个日志分析工作区来存储收集的数据。 如果您尚未创建，可以按照此处的说明：[创建日志分析工作区](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)。

### <a name="enable-hdinsight-azure-monitor-logs-integration"></a>启用 HDInsight Azure Monitor 日志集成

从门户中的 HDInsight 群集资源页中，选择**Azure 监视器**。 然后，选择**启用**并从下拉列表中选择日志分析工作区。

![HDInsight 运营管理套件](media/hdinsight-cluster-availability/azure-portal-monitoring.png)

### <a name="query-metrics-and-logs-tables"></a>查询指标和日志表

启用 Azure 监视器日志集成后（这可能需要几分钟时间），导航到**日志分析工作区**资源并选择 **"日志**"。

![日志分析工作区日志](media/hdinsight-cluster-availability/hdinsight-portal-logs.png)

日志列出了许多示例查询，例如：

| 查询名称                      | 描述                                                               |
|---------------------------------|---------------------------------------------------------------------------|
| 目前的计算机可用性    | 每小时绘制发送日志的计算机数的图表                     |
| 列出检测信号                 | 列出过去一小时的所有计算机检测信号                           |
| 每台计算机的最后一个检测信号 | 显示每台计算机发送的最后一个检测信号                             |
| 不可用的计算机           | 列出过去 5 小时未发送检测信号的所有已知计算机 |
| 可用率               | 计算每台已连接计算机的可用率                |

例如，通过选择"在该查询上运行"来**运行****可用性速率**示例查询，如上图所示。 这会以百分比显示群集中每个节点的可用率。 如果启用了多个 HDInsight 群集，可以将指标发送到同一日志分析工作区，您将看到显示这些群集中所有节点的可用性速率。

![日志分析工作区记录"可用性率"示例查询](media/hdinsight-cluster-availability/portal-availability-rate.png)

> [!NOTE]  
> 可用率是按 24 小时期限测量的，因此，群集至少需要运行 24 小时才能显示准确的可用率。

可以单击右上角的“固定”将此表固定到共享仪表板。**** 如果您没有任何可写共享仪表板，则可以在此处了解如何创建仪表板：[在 Azure 门户中创建和共享仪表板](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards#publish-and-share-a-dashboard)。

### <a name="azure-monitor-alerts"></a>Azure Monitor 警报

还可以设置当某个指标的值或某个查询的结果符合特定条件时要触发的 Azure Monitor 警报。 例如，让我们创建一个警报，在一个或多个节点在 5 小时内未发送检测信号时发送电子邮件（即假定为不可用）。

从**日志**，通过选择"**运行**"该查询来运行 **"不可用的计算机**示例查询"，如下所示。

![日志分析工作区日志"不可用的计算机"示例](media/hdinsight-cluster-availability/portal-unavailable-computers.png)

如果所有节点都可用，则此查询应返回当前零结果。 单击“新建警报规则”开始为此查询配置警报。****

![Log Analytics 工作区 - 新建警报规则](media/hdinsight-cluster-availability/portal-logs-new-alert-rule.png)

警报有三个组成部分：要为其创建规则的资源（在本例中为 Log Analytics 工作区）、触发该警报的条件，以及确定触发警报时发生的操作的操作组。******
单击如下所示的**条件标题**完成信号逻辑配置。

![门户警报创建规则条件](media/hdinsight-cluster-availability/portal-condition-title.png)

这将打开**配置信号逻辑**。

按如下所示设置“警报逻辑”部分：****

*基于：结果数，条件：大于，阈值：0。*

由于此查询只返回不可用的节点作为结果，如果结果数大于 0，应会激发警报。

在“计算依据”部分设置“时段”，并根据检查不可用节点的频率设置“频率”。************

为此警报的目的，您希望确保 **"期间=频率"。** 可在[此处](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log#log-search-alert-rule---definition-and-types)找到有关时段、频率和其他警报参数的详细信息。

完成配置信号逻辑后选择 **"完成**"。

![警报规则配置信号逻辑](media/hdinsight-cluster-availability/portal-configure-signal-logic.png)

如果尚未现有操作组，请单击"**操作组**"部分下的 **"新建**"。

![警报规则创建新的操作组](media/hdinsight-cluster-availability/portal-create-new-action-group.png)

这将打开 **"添加操作组**"。 选择**操作组名称**、**短名称**、**订阅**和**资源组**。 在“操作”部分下，选择一个**操作名称**，并选择“电子邮件/短信/推送/语音”作为“操作类型”。************

> [!NOTE]
> 除了“电子邮件/短信/推送/语音”以外，警报还可以触发其他几个操作，例如 Azure 函数、逻辑应用、Webhook、ITSM 和自动化 Runbook。 [了解更多信息。](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups#action-specific-information)

这将打开**电子邮件/短信/推送/语音**。 选择收件人的**姓名**，**选中**“电子邮件”框，然后键入要将警报发送到的电子邮件地址。**** 在 **"电子邮件/短信/推送/语音**"中选择 **"确定"，** 然后在 **"添加操作组"** 中完成操作组的配置。

![警报规则创建添加操作组](media/hdinsight-cluster-availability/portal-add-action-group.png)

关闭这些边栏选项卡后，应会看到你的操作组已列在“操作组”部分下。**** 最后，键入**警报规则名称**和**说明**并选择一种**严重性**来完成“警报详细信息”部分中的操作。**** 单击“创建警报规则”以完成操作。****

![门户创建警报规则完成](media/hdinsight-cluster-availability/portal-create-alert-rule-finish.png)

> [!TIP]
> 指定“严重性”是一个强大的功能，可在创建多个警报时使用它。**** 例如，可以创建一个警报以便在一个头节点出现故障时引发“警告”警报（严重性 1），并创建另一个警报以便在两个头节点同时出现故障时（这种情况很少见）引发“严重”警报（严重性 0）。

当满足此警报的条件时，警报将触发，您将收到一封电子邮件，其中包含警报详细信息，如下所示：

![Azure 监视器警报电子邮件示例](media/hdinsight-cluster-availability/portal-oms-alert-email.png)

您还可以通过访问**日志分析工作区**中的**警报**来查看已触发的所有警报（按严重性分组）。

![Log Analytics 工作区警报](media/hdinsight-cluster-availability/hdi-portal-oms-alerts.png)

选择严重性分组（如上文突出显示的**Sev 1）** 将显示已触发的所有严重性警报的记录，如下所示：

![日志分析工作区包含一个警报](media/hdinsight-cluster-availability/portal-oms-alerts-sev1.png)

## <a name="next-steps"></a>后续步骤

- [HDInsight 中的 Apache Hadoop 群集的可用性和可靠性](hdinsight-high-availability-linux.md)
