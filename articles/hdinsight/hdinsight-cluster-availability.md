---
title: 如何在 Azure HDInsight 中通过 Apache Ambari 监视群集的可用性
description: 了解如何使用 Apache Ambari 监视群集的运行状况和可用性。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,seoapr2020
ms.date: 05/01/2020
ms.openlocfilehash: 4b26128b794a6a667edc578f56ad0bc9fb8303a7
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2020
ms.locfileid: "82691156"
---
# <a name="how-to-monitor-cluster-availability-with-apache-ambari-in-azure-hdinsight"></a>如何在 Azure HDInsight 中通过 Apache Ambari 监视群集的可用性

HDInsight 群集包括 Apache Ambari，它提供了运行状况信息，并提供了预定义的警报。

本文介绍如何使用 Ambari 监视群集，并演练一些示例，以配置 Ambari 警报、监视节点可用性率，并创建在5小时内未从一个或多个节点接收到检测信号时触发的 Azure Monitor 警报。

## <a name="dashboard"></a>仪表板

可以通过在 Azure 门户的 HDInsight 概述的 "**群集仪表板**" 部分中选择**Ambari 主页**链接来访问 Ambari 仪表板，如下所示。 或者，可以通过在浏览器中导航`https://CLUSTERNAME.azurehdinsight.net`到，其中 CLUSTERNAME 是群集的名称。

![HDInsight 资源门户视图](media/hdinsight-cluster-availability/azure-portal-dashboard-ambari.png)

然后，系统将提示你输入群集登录用户名和密码。 请输入创建群集时选择的凭据。

然后，你将转到 Ambari 仪表板，其中包含的小组件显示了一些指标，可让你快速了解 HDInsight 群集的运行状况。 这些小组件显示活动的 DataNode（工作器节点）和 JournalNode（zookeeper 节点）数目、NameNode（头节点）运行时间等指标，以及特定群集类型的指标，例如 Spark 和 Hadoop 群集的 YARN ResourceManager 运行时间。

![Apache Ambari 使用仪表板显示](media/hdinsight-cluster-availability/apache-ambari-dashboard.png)

## <a name="hosts--view-individual-node-status"></a>主机 – 查看单个节点的状态

还可以查看单个节点的状态信息。 选择 "**主机**" 选项卡以查看群集中所有节点的列表，并查看有关每个节点的基本信息。 每个节点名称左侧的绿色勾选标记表示该节点上的所有组件已运行。 如果某个组件在节点上关闭，您将看到红色警报三角形，而不是绿色检查。

![HDInsight Apache Ambari 主机视图](media/hdinsight-cluster-availability/apache-ambari-hosts1.png)

然后，可以选择节点的**名称**以查看该特定节点的更详细的主机指标。 此视图显示每个组件的状态/可用性。

![Apache Ambari 托管单节点视图](media/hdinsight-cluster-availability/apache-ambari-hosts-node.png)

## <a name="ambari-alerts"></a>Ambari 警报

Ambari 还提供多个可配置的警报来针对特定的事件发出通知。 触发警报时，它们将显示在 Ambari 的左上角，其中包含警报数量的红色标记。 选择此徽章会显示当前警报的列表。

![Apache Ambari 当前警报计数](media/hdinsight-cluster-availability/apache-ambari-alerts.png)

若要查看警报定义及其状态的列表，请选择 "**警报**" 选项卡，如下所示。

![Ambari 警报定义视图](media/hdinsight-cluster-availability/ambari-alerts-definitions.png)

Ambari 提供许多与可用性相关的预定义警报，其中包括：

| 警报名称                        | 说明   |
|---|---|
| DataNode 运行状况摘要           | 如果存在不正常的 DataNode，则会触发此服务级别的警报|
| NameNode 高可用性运行状况 | 如果 Active NameNode 或待机 NameNode 未运行，则会触发此服务级别警报。|
| 可用 JournalNode 百分比    | 如果群集中已关闭的 JournalNode 数目大于配置的“严重”阈值，则会触发此警报。 此值聚合了 JournalNode 进程检查的结果。 |
| 可用 DataNode 百分比       | 如果群集中已关闭的 DataNode 数目大于配置的“严重”阈值，则会触发此警报。 此值聚合了 DataNode 进程检查的结果。|

可在[此处](https://docs.microsoft.com/azure/hdinsight/hdinsight-high-availability-linux#ambari-web-ui)找到帮助监视群集可用性的完整 Ambari 警报列表。

若要查看警报或修改条件的详细信息，请选择该警报的**名称**。 以“DataNode 运行状况摘要”为例。**** 你可以查看警报的说明，以及将触发 "警告" 或 "严重" 警报的特定条件，以及条件的检查间隔。 若要编辑配置，请选择 "配置" 框右上角的 "**编辑**" 按钮。

![Apache Ambari 警报配置](media/hdinsight-cluster-availability/ambari-alert-configuration.png)

在此处可以编辑说明，更重要的是，可以编辑警告或严重警报的检查间隔和阈值。

![Ambari 警报配置编辑视图](media/hdinsight-cluster-availability/ambari-alert-configuration-edit.png)

在此示例中，可以设置为存在 2 个不正常的 DataNode 时触发严重警报，存在 1 个不正常的 DataNode 时只触发警告。 完成编辑后，选择 "**保存**"。

## <a name="email-notifications"></a>电子邮件通知

还可以选择为 Ambari 警报配置电子邮件通知。 为此，请单击“警报”选项卡左上角的“操作”按钮，然后单击“管理通知”。************

![Ambari“管理通知”操作](media/hdinsight-cluster-availability/ambari-manage-notifications.png)

此时会打开一个用于管理警报通知的对话框。 **+** 选择对话框底部的，并填写必填字段，为 Ambari 提供电子邮件服务器详细信息，以便发送电子邮件。

> [!TIP]
> 设置 Ambari 电子邮件通知后，在管理多个 HDInsight 群集时，可以方便地在一个位置接收警报。

## <a name="next-steps"></a>后续步骤

- [HDInsight 中的 Apache Hadoop 群集的可用性和可靠性](hdinsight-high-availability-linux.md)
- [群集可用性 - Azure Monitor 日志](./cluster-availability-monitor-logs.md)
- [使用 Azure Monitor 日志](hdinsight-hadoop-oms-log-analytics-tutorial.md)
- [Apache Ambari 电子邮件通知](apache-ambari-email.md)
