---
title: 以编程方式访问 Hadoop YARN 应用程序日志 - Azure
description: 以编程方式访问 HDInsight 中 Hadoop 群集上的应用程序日志。
services: hdinsight
author: jasonwhowell
editor: jasonwhowell
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/25/2017
ms.author: jasonh
ROBOTS: NOINDEX
ms.openlocfilehash: 42484f2a93ab5effdcafca0f0769c3fb4cdbb926
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/07/2018
ms.locfileid: "39600177"
---
# <a name="access-yarn-application-logs-on-windows-based-hdinsight"></a>在基于 Windows 的 HDInsight 上访问 YARN 应用程序日志
本文档介绍了如何访问 Azure HDInsight 中基于 Windows 的 Hadoop 群集上已完成的 YARN 应用程序的日志。

> [!IMPORTANT]
> 本文档中的信息仅适用于基于 Windows 的 HDInsight 群集。 Linux 是 HDInsight 3.4 或更高版本上使用的唯一操作系统。 有关详细信息，请参阅 [HDInsight 在 Windows 上停用](hdinsight-component-versioning.md#hdinsight-windows-retirement)。 有关在基于 Linux 的 HDInsight 群集上访问 YARN 日志的信息，请参阅[在 HDInsight 上基于 Linux 的 Hadoop 中访问 YARN 应用程序日志](hdinsight-hadoop-access-yarn-app-logs-linux.md)
>


### <a name="prerequisites"></a>先决条件
* 基于 Windows 的 HDInsight 群集。  请参阅[在 HDInsight 中创建基于 Windows 的 Hadoop 群集](hdinsight-hadoop-provision-linux-clusters.md)。

## <a name="yarn-timeline-server"></a>YARN Timeline Server
<a href="http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html" target="_blank">YARN Timeline Server</a> 通过两个不同的接口提供已完成之应用程序的相关泛型信息，以及架构特定应用程序信息。 具体而言：

* 存储及检索 HDInsight 群集上泛型应用程序信息的功能已在版本 3.1.1.374 或更新版本上启用。
* Timeline Server 的架构特定应用程序信息组件当前在 HDInsight 群集上并未提供。

应用程序的相关泛型信息包含以下类型的数据：

* 应用程序 ID（应用程序的唯一标识符）
* 启动应用程序的用户
* 为完成应用程序而进行的尝试的相关信息
* 任何给定应用程序尝试所用的容器

在 HDInsight 群集中，此信息是由 Azure 资源管理器存储的。 信息将保存到群集的默认存储中的历史记录存储区。 可以通过 REST API 检索有关已完成的应用程序的此类泛型数据：

    GET on https://<cluster-dns-name>.azurehdinsight.net/ws/v1/applicationhistory/apps


## <a name="yarn-applications-and-logs"></a>YARN 应用程序和日志
YARN 通过将资源管理与应用程序计划/监视相分离，来支持多种编程模型。 YARN 使用全局 *ResourceManager* (RM)、按辅助角色节点 *NodeManagers* (NM) 和按应用程序 *ApplicationMasters* (AM)。 按应用程序 AM 与 RM 协商用于运行应用程序的资源（CPU、内存、磁盘、网络）。 RM 与 NM 合作来授予这些资源（以容器的形式授予）。 AM 负责跟踪 RM 分配给它的容器的进度。 根据应用程序的性质，一个应用程序可能需要多个容器。

* 每个应用程序可能包含多个应用程序尝试。 
* 容器授予给应用程序的特定尝试。 
* 容器为基本工作单元提供了上下文。 
* 在容器的上下文中执行的工作是在容器分配到的单个工作节点上执行的。 

有关详细信息，请参阅 [YARN 概念][YARN-concepts]。

应用程序日志（和关联的容器日志）在对有问题的 Hadoop 应用程序进行调试上相当重要。 YARN 提供一个良好的框架，通过使用[日志聚合][log-aggregation]功能收集、聚合和存储应用程序日志。 日志聚合功能让访问应用程序日志更具确定性，因为它会聚合辅助节点上所有容器的日志，并在应用程序完成之后，将它们按每个辅助节点一个聚合日志的方式存储在默认文件系统上。 应用程序可能使用数百或数千个容器，但在单个工作节点上运行的所有容器的日志将聚合成单个文件，也就是为应用程序所用的每个工作节点生成一个文件。 默认情况下，日志聚合已在 HDInsight 群集（3.0 和更高版本）上启用，在群集的默认容器中，可以找到聚合的日志，位置如下：

    wasb:///app-logs/<user>/logs/<applicationId>

在该位置中，*user* 是启动应用程序的用户名，*applicationId* 是 YARN RM 分配的应用程序唯一标识符。

无法直接阅读聚合日志，因为它们是以 [TFile][T-file]（由容器编制索引的[二进制格式][binary-format]）编写。 YARN 提供 CLI 工具，可针对你感兴趣的应用程序或容器，将这些日志转储成纯文本。 可以直接在群集节点上（通过 RDP 连接到节点之后）运行以下 YARN 命令之一，以纯文本格式查看这些日志：

    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>


## <a name="yarn-resourcemanager-ui"></a>YARN ResourceManager UI
YARN ResourceManager UI 在群集头节点上运行，且可以通过 Azure 门户仪表板进行访问：

1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 在左侧菜单中，依次单击“浏览”、“HDInsight 群集”和要访问 YARN 应用程序日志的基于 Windows 的群集。
3. 在顶部菜单中单击“仪表板”。 将会看到在新的浏览器选项卡上打开名为“HDInsight 查询控制台”页面。
4. 在“HDInsight 查询控制台”中单击“Yarn UI”。

[YARN-timeline-server]:http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html
[log-aggregation]:http://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/
[T-file]:https://issues.apache.org/jira/secure/attachment/12396286/TFile%20Specification%2020081217.pdf
[binary-format]:https://issues.apache.org/jira/browse/HADOOP-3315
[YARN-concepts]:http://hortonworks.com/blog/apache-hadoop-yarn-concepts-and-applications/
