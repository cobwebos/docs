---
title: "在基于 Linux 的 HDInsight 上访问 Hadoop YARN 应用程序日志 — Azure | Microsoft Docs"
description: "了解如何使用命令行和 Web 浏览器在基于 Linux 的 HDInsight (Hadoop) 群集上访问 YARN 应用程序日志。"
services: hdinsight
documentationcenter: 
tags: azure-portal
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 3ec08d20-4f19-4a8e-ac86-639c04d2f12e
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2017
ms.author: larryfr
ms.openlocfilehash: fbbbddc47f24a46eac9bc64d4420ee8429ed4ad1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="access-yarn-application-logs-on-linux-based-hdinsight"></a>在基于 Linux 的 HDInsight 上访问 YARN 应用程序日志

了解如何访问 Azure HDInsight 中 Hadoop 群集上的 YARN (Yet Another Resource Negotiator) 应用程序日志。

> [!IMPORTANT]
> 本文档中的步骤需要使用 Linux 的 HDInsight 群集。 Linux 是 HDInsight 3.4 或更高版本上使用的唯一操作系统。 有关详细信息，请参阅 [HDInsight 组件版本控制](hdinsight-component-versioning.md#hdinsight-windows-retirement)。

## <a name="YARNTimelineServer"></a>YARN Timeline Server

[YARN Timeline Server](http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html) 通过两个不同的接口提供已完成应用程序的相关泛型信息，以及架构特定应用程序信息。 具体而言：

* 存储及检索 HDInsight 群集上泛型应用程序信息的功能已在版本 3.1.1.374 或更新版本上启用。
* Timeline Server 的架构特定应用程序信息组件当前在 HDInsight 群集上并未提供。

应用程序的相关一般信息包含以下类型的数据：

* 应用程序 ID（应用程序的唯一标识符）
* 启动应用程序的用户
* 为完成应用程序而进行的尝试的相关信息
* 任何给定应用程序尝试所用的容器

## <a name="YARNAppsAndLogs"></a>YARN 应用程序和日志

YARN 通过将资源管理与应用程序计划/监视相分离，来支持多种编程模型（MapReduce 就是其中之一）。 YARN 使用全局 *ResourceManager* (RM)、按辅助角色节点 *NodeManagers* (NM) 和按应用程序 *ApplicationMasters* (AM)。 按应用程序 AM 与 RM 协商用于运行应用程序的资源（CPU、内存、磁盘、网络）。 RM 与 NM 合作来授予这些资源（以容器的形式授予）。 AM 负责跟踪 RM 分配给它的容器的进度。 根据应用程序的性质，一个应用程序可能需要多个容器。

每个应用程序可能包含多个应用程序尝试。 如果应用程序失败，可能会重试作为新的尝试。 在容器中运行每次尝试。 在某种意义上，容器提供了由 YARN 应用程序执行的基本工作单位的上下文。 在分配了容器的单个辅助角色节点上执行容器上下文中完成的所有工作。 请参阅 [YARN 的概念][YARN-concepts]，以获取更多参考信息。

应用程序日志（和关联的容器日志）在对有问题的 Hadoop 应用程序进行调试上相当重要。 YARN 提供一个良好的框架，通过使用[日志聚合][log-aggregation]功能收集、聚合和储应用程序日志。 日志聚合功能使访问应用程序日志更具确定性。 它聚合辅助角色节点上所有容器的日志，并将其存储为一个聚合日志文件（每个辅助角色节点）。 应用程序完成后，日志存储在默认文件系统中。 应用程序可能使用数百或数千个容器，但在单个辅助角色节点上运行的所有容器的日志始终聚合成单个文件。 因此，在每个辅助角色节点上，应用程序只使用 1 个日志。 在 HDInsight 群集版本 3.0 和更高版本上，日志聚合默认已启用。 聚合日志位于群集的默认存储中。 下面的路径是日志的 HDFS 路径：

    /app-logs/<user>/logs/<applicationId>

在此路径中，`user` 是启动应用程序的用户的名称。 `applicationId` 是 YARN RM 分配给应用程序的唯一标识符。

无法直接阅读聚合日志，因为它们是以 [TFile][T-file]（由容器编制索引的[二进制格式][binary-format]）编写。 使用 YARN ResourceManager 日志或 CLI 工具以纯文本的形式查看感兴趣的应用程序或容器的日志。

## <a name="yarn-cli-tools"></a>YARN CLI 工具

若要使用 YARN CLI 工具，则必须首先使用 SSH 连接到 HDInsight 群集。 有关信息，请参阅[将 SSH 与 HDInsight 配合使用](hdinsight-hadoop-linux-use-ssh-unix.md)。

可通过运行下列命令之一以纯文本格式查看这些日志：

    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>

运行这些命令时，请指定 &lt;applicationId>、&lt;user-who-started-the-application>、&lt;containerId> 和 &lt;worker-node-address> 信息。

## <a name="yarn-resourcemanager-ui"></a>YARN ResourceManager UI

YARN ResourceManager UI 在群集头节点上运行。 可通过 Ambari web UI 访问它。 使用以下步骤查看 YARN 日志：

1. 在 Web 浏览器中，导航到 https://CLUSTERNAME.azurehdinsight.net。 将 CLUSTERNAME 替换为 HDInsight 群集的名称。
2. 从左侧的服务列表中选择 **YARN**。

    ![选中的 Yarn 服务](./media/hdinsight-hadoop-access-yarn-app-logs-linux/yarnservice.png)
3. 在“快速链接”的下拉列表中，选择其中一个群集头节点，并选择“ResourceManager 日志”。

    ![Yarn 快速链接](./media/hdinsight-hadoop-access-yarn-app-logs-linux/yarnquicklinks.png)

    将显示 YARN 日志的链接列表。

[YARN-timeline-server]:http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html
[log-aggregation]:http://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/
[T-file]:https://issues.apache.org/jira/secure/attachment/12396286/TFile%20Specification%2020081217.pdf
[binary-format]:https://issues.apache.org/jira/browse/HADOOP-3315
[YARN-concepts]:http://hortonworks.com/blog/apache-hadoop-yarn-concepts-and-applications/
