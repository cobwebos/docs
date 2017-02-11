---
title: "在基于 Linux 的 HDInsight 上访问 Hadoop YARN 应用程序日志 | Microsoft Docs"
description: "了解如何使用命令行和 Web 浏览器在基于 Linux 的 HDInsight (Hadoop) 群集上访问 YARN 应用程序日志。"
services: hdinsight
documentationcenter: 
tags: azure-portal
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 3ec08d20-4f19-4a8e-ac86-639c04d2f12e
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/21/2016
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: f03c595977f098f19d396cc460c73e27163f070c


---
# <a name="access-yarn-application-logs-on-linux-based-hdinsight"></a>在基于 Linux 的 HDInsight 上访问 YARN 应用程序日志
本文档说明如何访问 Azure HDInsight 中的 Hadoop 群集上已完成的 YARN (Yet Another Resource Negotiator) 应用程序的日志。

> [!NOTE]
> 本文档中的信息针对基于 Linux 的 HDInsight 群集。 有关基于 Windows 的群集的信息 ，请参阅[在基于 Windows 的 HDInsight 上访问 YARN 应用程序日志](hdinsight-hadoop-access-yarn-app-logs.md)
> 
> 

## <a name="prerequisites"></a>先决条件
* 基于 Linux 的 HDInsight 群集。
* 必须首先[创建 SSH 隧道](hdinsight-linux-ambari-ssh-tunnel.md)，才能访问 ResourceManager 日志 Web UI。

## <a name="a-nameyarntimelineserverayarn-timeline-server"></a><a name="YARNTimelineServer"></a>YARN Timeline Server
[YARN Timeline Server](http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html) 通过两个不同的接口提供已完成之应用程序的相关泛型信息，以及架构特定应用程序信息。 具体而言：

* 存储及检索 HDInsight 群集上泛型应用程序信息的功能已在版本 3.1.1.374 或更新版本上启用。
* Timeline Server 的架构特定应用程序信息组件当前在 HDInsight 群集上并未提供。

应用程序的相关泛型信息包含以下类型的数据：

* 应用程序 ID（应用程序的唯一标识符）
* 启动应用程序的用户
* 为完成应用程序而进行的尝试的相关信息
* 任何给定应用程序尝试所用的容器

## <a name="a-nameyarnappsandlogsayarn-applications-and-logs"></a><a name="YARNAppsAndLogs"></a>YARN 应用程序和日志
YARN 通过将资源管理与应用程序计划/监视相分离，来支持多种编程模型（MapReduce 就是其中之一）。 这是通过全局 *ResourceManager* (RM)、按辅助角色节点 *NodeManagers* (NM) 和按应用程序 *ApplicationMasters* (AM) 来实现的。 按应用程序 AM 与 RM 协商用于运行应用程序的资源（CPU、内存、磁盘、网络）。 RM 与 NM 合作来授予这些资源（以容器的形式授予）。 AM 负责跟踪 RM 分配给它的容器的进度。 根据应用程序的性质，一个应用程序可能需要多个容器。

此外，每个应用程序可能包含多个应用程序尝试，为了在应用程序崩溃时，或因 AM 与 RM 之间通信中断时，完成应用程序。 因此，容器是授予应用程序的特定尝试。 在某种意义上，容器提供 YARN 应用程序所运行的基本工作单位的内容，而在容器的上下文中完成的所有工作都在分配给该容器的单个辅助节点上运行。 请参阅 [YARN 的概念][YARN-concepts]，以获取更多参考信息。

应用程序日志（和关联的容器日志）在对有问题的 Hadoop 应用程序进行调试上相当重要。 YARN 以[日志聚合][log-aggregation] 功能提供一个良好的框架，以收集、聚合及存储应用程序日志。 日志聚合功能让访问应用程序日志更具确定性，因为它会聚合辅助节点上所有容器的日志，并在应用程序完成之后，将它们按每个辅助节点一个聚合日志的方式存储在默认文件系统上。 应用程序可能使用数百或数千个容器，但在单个辅助节点上运行的所有容器的日志将一律聚合成单个文件，也就是为应用程序所用的每个辅助节点生成一个日志。 默认情况下，日志聚合已在 HDInsight 群集（3.0 和更高版本）上启用，在群集的默认容器中，可以找到聚合的日志，位置如下：

    wasbs:///app-logs/<user>/logs/<applicationId>

在该位置中，*user* 是启动应用程序的用户名，*applicationId* 是 YARN RM 分配的应用程序唯一标识符。

无法直接阅读聚合的日志，因为它们是以 [TFile][T-file]（由容器编制索引的[二进制格式][binary-format]）编写的。 必须使用 YARN ResourceManager 日志或 CLI 工具才能以纯文本的形式查看感兴趣的应用程序或容器的日志。 

## <a name="yarn-cli-tools"></a>YARN CLI 工具
若要使用 YARN CLI 工具，则必须首先使用 SSH 连接到 HDInsight 群集。 有关如何将 SSH 与 HDInsight 配合使用的详细信息，请参阅以下文档之一：

* [在 Linux、Unix 或 OS X 中的 HDInsight 上将 SSH 与基于 Linux 的 Hadoop 配合使用](hdinsight-hadoop-linux-use-ssh-unix.md)
* [在 Windows 中的 HDInsight 上将 SSH 与基于 Linux 的 Hadoop 配合使用](hdinsight-hadoop-linux-use-ssh-windows.md)

可通过运行下列命令之一以纯文本格式查看这些日志：

    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>

在运行这些命令时必须指定 &lt;applicationId>、&lt;user-who-started-the-application>、&lt;containerId> 和 &ltworker-node-address> 信息。

## <a name="yarn-resourcemanager-ui"></a>YARN ResourceManager UI
YARN ResourceManager UI 在群集头节点上运行，并可通过 Ambari Web UI 访问；但是，在访问 ResourceManager UI 之前必须先 [创建 SSH 隧道](hdinsight-linux-ambari-ssh-tunnel.md)。

创建 SSH 隧道后，请使用以下步骤查看 YARN 日志：

1. 在 Web 浏览器中，导航到 https://CLUSTERNAME.azurehdinsight.net。 将 CLUSTERNAME 替换为 HDInsight 群集的名称。
2. 从左侧的服务列表中选择 **YARN**。
   
    ![选中的 Yarn 服务](./media/hdinsight-hadoop-access-yarn-app-logs-linux/yarnservice.png)
3. 在“快速链接”的下拉列表中，选择其中一个群集头节点，然后选择“ResourceManager 日志”。
   
    ![Yarn 快速链接](./media/hdinsight-hadoop-access-yarn-app-logs-linux/yarnquicklinks.png)
   
    将显示 YARN 日志的链接列表。

[YARN-timeline-server]:http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html
[log-aggregation]:http://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/
[T-file]:https://issues.apache.org/jira/secure/attachment/12396286/TFile%20Specification%2020081217.pdf
[binary-format]:https://issues.apache.org/jira/browse/HADOOP-3315
[YARN-concepts]:http://hortonworks.com/blog/apache-hadoop-yarn-concepts-and-applications/



<!--HONumber=Nov16_HO3-->


