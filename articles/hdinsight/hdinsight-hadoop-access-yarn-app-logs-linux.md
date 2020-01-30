---
title: 访问 Apache Hadoop YARN 应用程序日志-Azure HDInsight
description: 了解如何使用命令行和 Web 浏览器在基于 Linux 的 HDInsight (Apache Hadoop) 群集上访问 YARN 应用程序日志。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/23/2020
ms.openlocfilehash: 2a7d71c6d751d4a48ec93f020e657a4d43114cfc
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2020
ms.locfileid: "76764386"
---
# <a name="access-apache-hadoop-yarn-application-logs-on-linux-based-hdinsight"></a>在基于 Linux 的 HDInsight 上访问 Apache Hadoop YARN 应用程序日志

了解如何在 Azure HDInsight 中的 [Apache Hadoop](https://hadoop.apache.org/) 群集上访问 [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) (Yet Another Resource Negotiator) 应用程序日志。

## <a name="what-is-apache-yarn"></a>什么是 Apache YARN？

YARN 通过将资源管理与应用程序计划/监视相分离，来支持多种编程模型（[Apache Hadoop MapReduce](https://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html) 就是其中之一）。 YARN 使用全局 *ResourceManager* (RM)、按辅助角色节点 *NodeManagers* (NM) 和按应用程序 *ApplicationMasters* (AM)。 按应用程序 AM 与 RM 协商用于运行应用程序的资源（CPU、内存、磁盘、网络）。 RM 与 NM 合作来授予这些资源（以容器的形式授予）。 AM 负责跟踪 RM 分配给它的容器的进度。 根据应用程序的性质，一个应用程序可能需要多个容器。

每个应用程序可能包含多个应用程序尝试。 如果应用程序失败，可能会重试作为新的尝试。 在容器中运行每次尝试。 在某种意义上，容器提供了由 YARN 应用程序执行的基本工作单位的上下文。 在分配了容器的单个辅助角色节点上执行容器上下文中完成的所有工作。 请参阅[Hadoop：编写 YARN 应用程序](https://hadoop.apache.org/docs/r2.7.4/hadoop-yarn/hadoop-yarn-site/WritingYarnApplications.html)，或[Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html)获取进一步引用。

若要缩放群集以支持更高的处理吞吐量，可以使用[几种不同的语言，手动](hdinsight-scaling-best-practices.md#utilities-to-scale-clusters)使用[自动缩放](hdinsight-autoscale-clusters.md)或扩展群集。

## <a name="yarn-timeline-server"></a>YARN Timeline Server

[Apache Hadoop YARN Timeline Server](https://hadoop.apache.org/docs/r2.7.3/hadoop-yarn/hadoop-yarn-site/TimelineServer.html) 提供有关已完成应用程序的一般信息

YARN Timeline Server 包括以下类型的数据：

* 应用程序 ID（应用程序的唯一标识符）
* 启动应用程序的用户
* 为完成应用程序而进行的尝试的相关信息
* 任何给定应用程序尝试所用的容器

## <a name="yarn-applications-and-logs"></a>YARN 应用程序和日志

YARN 通过将资源管理与应用程序计划/监视相分离，来支持多种编程模型（[Apache Hadoop MapReduce](https://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html) 就是其中之一）。 YARN 使用全局 *ResourceManager* (RM)、按辅助角色节点 *NodeManagers* (NM) 和按应用程序 *ApplicationMasters* (AM)。 按应用程序 AM 与 RM 协商用于运行应用程序的资源（CPU、内存、磁盘、网络）。 RM 与 NM 合作来授予这些资源（以容器的形式授予）。 AM 负责跟踪 RM 分配给它的容器的进度。 根据应用程序的性质，一个应用程序可能需要多个容器。

每个应用程序可能包含多个应用程序尝试。 如果应用程序失败，可能会重试作为新的尝试。 在容器中运行每次尝试。 在某种意义上，容器提供了由 YARN 应用程序执行的基本工作单位的上下文。 在分配了容器的单个辅助角色节点上执行容器上下文中完成的所有工作。 有关进一步参考，请参阅[APACHE HADOOP YARN 的概念](https://hadoop.apache.org/docs/r2.7.4/hadoop-yarn/hadoop-yarn-site/WritingYarnApplications.html)。

应用程序日志（和关联的容器日志）在对有问题的 Hadoop 应用程序进行调试上相当重要。 YARN 提供了一个很好的框架，用于使用[日志聚合](https://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/)功能收集、聚合和存储应用程序日志。 日志聚合功能使访问应用程序日志更具确定性。 它聚合工作器节点上所有容器的日志，并将其存储为一个聚合日志文件（每个工作器节点）。 应用程序完成后，日志存储在默认文件系统中。 应用程序可能使用数百或数千个容器，但在单个工作器节点上运行的所有容器的日志始终聚合成单个文件。 因此，应用程序使用的每个辅助角色节点只有1个日志。 在 HDInsight 群集版本 3.0 和更高版本上，日志聚合默认已启用。 聚合日志位于群集的默认存储中。 下面的路径是日志的 HDFS 路径：

```
/app-logs/<user>/logs/<applicationId>
```

在此路径中，`user` 是启动应用程序的用户的名称。 `applicationId` 是 YARN RM 分配给应用程序的唯一标识符。

聚合日志不能直接读取，因为它们是以[tfile (](https://issues.apache.org/jira/secure/attachment/12396286/TFile%20Specification%2020081217.pdf)（由容器索引的[二进制格式](https://issues.apache.org/jira/browse/HADOOP-3315)）编写的。 使用 YARN ResourceManager 日志或 CLI 工具以纯文本的形式查看感兴趣的应用程序或容器的这些日志。

## <a name="yarn-logs-in-an-esp-cluster"></a>在 ESP 群集中 Yarn 日志

必须将两个配置添加到 Ambari 中的自定义 `mapred-site`。

1. 在 web 浏览器中，导航到 `https://CLUSTERNAME.azurehdinsight.net`，其中 `CLUSTERNAME` 是群集的名称。

1. 在 Ambari UI 中，导航到 " **MapReduce2** " ** >  > 配置**"**高级** > **自定义 mapred**"。

1. 添加以下属性集*之一*：

    **集1**

    ```
    mapred.acls.enabled=true
    mapreduce.job.acl-view-job=*
    ```

    **设置2**

    ```
    mapreduce.job.acl-view-job=<user1>,<user2>,<user3>
    ```

1. 保存更改并重新启动所有受影响的服务。

## <a name="yarn-cli-tools"></a>YARN CLI 工具

1. 使用[ssh 命令](./hdinsight-hadoop-linux-use-ssh-unix.md)连接到群集。 将 CLUSTERNAME 替换为群集名称，然后输入以下命令，以编辑以下命令：

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. 列出当前正在运行的 Yarn 应用程序的所有应用程序 id，并提供以下命令：

    ```bash
    yarn top
    ```

    记下要下载其日志的 `APPLICATIONID` 列中的应用程序 id。

    ```output
    YARN top - 18:00:07, up 19d, 0:14, 0 active users, queue(s): root
    NodeManager(s): 4 total, 4 active, 0 unhealthy, 0 decommissioned, 0 lost, 0 rebooted
    Queue(s) Applications: 2 running, 10 submitted, 0 pending, 8 completed, 0 killed, 0 failed
    Queue(s) Mem(GB): 97 available, 3 allocated, 0 pending, 0 reserved
    Queue(s) VCores: 58 available, 2 allocated, 0 pending, 0 reserved
    Queue(s) Containers: 2 allocated, 0 pending, 0 reserved
    
                      APPLICATIONID USER             TYPE      QUEUE   #CONT  #RCONT  VCORES RVCORES     MEM    RMEM  VCORESECS    MEMSECS %PROGR       TIME NAME
     application_1490377567345_0007 hive            spark  thriftsvr       1       0       1       0      1G      0G    1628407    2442611  10.00   18:20:20 Thrift JDBC/ODBC Server
     application_1490377567345_0006 hive            spark  thriftsvr       1       0       1       0      1G      0G    1628430    2442645  10.00   18:20:20 Thrift JDBC/ODBC Server
    ```

1. 可通过运行下列命令之一以纯文本格式查看这些日志：

    ```bash
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>
    ```

    运行这些命令时，请指定 &lt;applicationId>、&lt;user-who-started-the-application>、&lt;containerId> 和 &lt;worker-node-address> 信息。

### <a name="other-sample-commands"></a>其他示例命令

1. 通过以下命令下载所有应用程序主机的 Yarn 容器日志。 这会以文本格式创建名为 `amlogs.txt` 的日志文件。

    ```bash
    yarn logs -applicationId <application_id> -am ALL > amlogs.txt
    ```

1. 使用以下命令只会下载最新应用程序主机的 Yarn 容器日志：

    ```bash
    yarn logs -applicationId <application_id> -am -1 > latestamlogs.txt
    ```

1. 使用以下命令下载前两个应用程序主机的 YARN 容器日志：

    ```bash
    yarn logs -applicationId <application_id> -am 1,2 > first2amlogs.txt
    ```

1. 使用以下命令下载所有 Yarn 容器日志：

    ```bash
    yarn logs -applicationId <application_id> > logs.txt
    ```

1. 使用以下命令下载特定容器的 Yarn 容器日志：

    ```bash
    yarn logs -applicationId <application_id> -containerId <container_id> > containerlogs.txt
    ```

## <a name="yarn-resourcemanager-ui"></a>YARN ResourceManager UI

YARN ResourceManager UI 在群集头节点上运行。 可通过 Ambari web UI 访问它。 使用以下步骤查看 YARN 日志：

1. 在 Web 浏览器中导航到 `https://CLUSTERNAME.azurehdinsight.net`。 将 CLUSTERNAME 替换为 HDInsight 群集的名称。

2. 从左侧的服务列表中选择 **YARN**。

    ![选择的 Apache Ambari Yarn 服务](./media/hdinsight-hadoop-access-yarn-app-logs-linux/yarn-service-selected.png)

3. 在“快速链接”的下拉列表中，选择其中一个群集头节点，并选择“ResourceManager 日志”。

    ![Apache Ambari Yarn 快速链接](./media/hdinsight-hadoop-access-yarn-app-logs-linux/hdi-yarn-quick-links.png)

    将显示一个链接列表，其中包含指向 YARN 日志的链接。
