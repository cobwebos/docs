---
title: 在 Azure HDInsight 中部署和管理 Apache Storm 拓扑
description: 了解如何在基于 Linux 的 HDInsight 上使用风暴仪表板部署、监视和管理 Apache Storm 拓扑。 使用 Hadoop Tools for Visual Studio。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/18/2019
ms.openlocfilehash: e890289230b3215bd102d8c5a78dca4f1b7b90f8
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79271897"
---
# <a name="deploy-and-manage-apache-storm-topologies-on-azure-hdinsight"></a>在 Azure HDInsight 中部署和管理 Apache Storm 拓扑

本文档介绍有关如何在 HDInsight 群集上管理和监视 Storm 上运行的 [Apache Storm](https://storm.apache.org/) 拓扑的基本知识。

## <a name="prerequisites"></a>必备条件

* HDInsight 上的 Apache Storm 群集。 请参阅[使用 Azure 门户创建 Apache Hadoop 群集](../hdinsight-hadoop-create-linux-clusters-portal.md)，并选择 **Storm** 作为**群集类型**。

* 可有可无熟悉安全外壳（SSH）和安全复制（SCP）。 有关详细信息，请参阅[使用 SSH 连接到 HDInsight (Apache Hadoop)](../hdinsight-hadoop-linux-use-ssh-unix.md)。

* 可有可无Visual Studio、Azure SDK 2.5.1 或更高版本，以及适用于 Visual Studio 的 Data Lake 工具。 有关详细信息，请参阅[Apache Hadoop & Visual Studio Data Lake 工具](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)。

## <a name="submit-a-topology-using-visual-studio"></a>使用 Visual Studio 提交拓扑

可以使用适用于 Visual Studio 的 Data Lake 工具将拓扑C#提交到风暴群集或混合拓扑。 以下步骤使用了一个示例应用程序。 有关使用 Data Lake 工具创建拓扑的信息，请参阅[使用 Visual Studio 和C#Apache Storm 拓扑](apache-storm-develop-csharp-visual-studio-topology.md)。

1. 如果尚未安装最新版本的适用于 Visual Studio 的 Data Lake 工具，请参阅[使用 Visual studio Data Lake 工具](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)。

    > [!NOTE]  
    > Azure Data Lake 和流分析工具以前称为用于 Visual Studio 的 HDInsight 工具。
    >
    > 适用于 Visual Studio 的 Azure Data Lake 和流分析工具包含在 Visual Studio 2019 的**Azure 开发**工作负荷中。

1. 启动 Visual Studio。

1. 在“开始”窗口中，选择“创建新项目”。

1. 在 "**新建项目**" 窗口中，选择 "搜索" 框，然后输入 `Storm`。 然后从结果列表中选择 "**风暴示例**"，然后选择 "**下一步**"。

1. 在 "**配置新项目**" 窗口中，输入**项目名称**，然后前往或创建保存新项目的**位置**。 然后选择“创建”。

    ![配置新的项目窗口 Visual Studio](./media/apache-storm-deploy-monitor-topology-linux/apache-storm-sample1.png)

1. 在**服务器资源管理器**中，右键单击 " **Azure** "，并选择 "**连接到 Microsoft Azure 订阅 ...** "，然后完成登录过程。

1. 在**解决方案资源管理器**中，右键单击该项目，然后选择 "**在 HDInsight 上提交到风暴"** 。

    > [!NOTE]  
    > 如果出现提示，请输入 Azure 订阅的登录凭据。 如果有多个订阅，请登录包含 Storm on HDInsight 群集的订阅。

1. 在 "**提交拓扑**" 对话框中，在 "**风暴群集**" 下拉列表中，选择 "风暴 on HDInsight 群集"，然后选择 "**提交**"。 可以通过查看 "**输出**" 窗格监视提交是否成功。

## <a name="submit-a-topology-using-ssh-and-the-storm-command"></a>使用 SSH 和风暴命令提交拓扑

1. 使用[ssh 命令](../hdinsight-hadoop-linux-use-ssh-unix.md)连接到群集。 将 CLUSTERNAME 替换为群集名称，然后输入以下命令，以编辑以下命令：

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. 在 ssh 会话中，使用以下命令启动**WordCount**示例拓扑：

    ```bash
    storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-*.jar org.apache.storm.starter.WordCountTopology WordCount
    ```

    此命令启动群集上的示例 WordCount 拓扑。 此拓扑随机生成句子，并统计句子中每个单词的出现次数。

    > [!NOTE]  
    > 将拓扑提交到群集时，必须先复制包含群集的 jar 文件，然后才能使用 `storm` 命令。 要将文件复制到群集，可以使用 `scp` 命令。 例如，输入 `scp FILENAME.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.jar`。
    >
    > `/usr/hdp/current/storm-client/contrib/storm-starter/`上已包含了群集中的*WordCount*示例和其他风暴入门示例。

## <a name="submit-a-topology-programmatically"></a>以编程方式提交拓扑

可以使用 Nimbus 服务以编程方式部署拓扑。 [https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology](https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology) 提供了 Java 应用程序示例，演示如何通过 Nimbus 服务部署和启动拓扑。

## <a name="monitor-and-manage-a-topology-in-visual-studio"></a>在 Visual Studio 中监视和管理拓扑

使用 Visual Studio 提交拓扑时，将显示 "**风暴拓扑视图**" 窗口。 从列表中选择拓扑，以查看有关正在运行的拓扑的信息。

![监视拓扑，风暴拓扑视图窗口，Visual Studio](./media/apache-storm-deploy-monitor-topology-linux/visual-studio-monitor.png)

> [!NOTE]  
> 也可以从“服务器资源管理器”查看“Storm 拓扑”。 展开“Azure” > “HDInsight”，右键单击 Storm on HDInsight 群集，然后选择“查看 Storm 拓扑”。

选择 Spout 或 Bolt 的形状可查看有关这些组件的信息。 此时会显示一个工具提示，其中包含所选项的组件信息。

### <a name="deactivate-and-reactivate-a-topology"></a>停用和重新激活拓扑

停用拓扑将暂停，直到终止或重新激活拓扑。 若要执行这些操作，请使用 "**风暴拓扑视图**" 窗口顶部的 "**操作**" 区域中的 "**停用**和**重新激活**" 按钮。

### <a name="rebalance-a-topology"></a>重新平衡拓扑

重新平衡拓扑可以让系统修改拓扑的并行度。 例如，如果调整了群集的大小以添加更多说明，则重新平衡允许拓扑查看新节点。

若要重新平衡拓扑，请使用 "**风暴拓扑视图**" 窗口的 "**操作**" 区域中的 "重新**平衡**" 按钮。

> [!WARNING]  
> 重新平衡拓扑将停用拓扑，将辅助角色均匀地分布到群集，然后将拓扑返回到发生重新平衡之前的状态。 如果拓扑处于活动状态，则它会再次变为活动状态。 如果拓扑被停用，则它将保持停用状态。

### <a name="kill-a-running-topology"></a>终止正在运行的拓扑

风暴拓扑将继续运行，直到它们停止或群集被删除。 若要停止拓扑，请使用 "**操作**" 区域中的 "**终止**" 按钮。

## <a name="monitor-and-manage-a-topology-using-ssh-and-the-storm-command"></a>使用 SSH 和风暴命令监视和管理拓扑

通过 `storm` 实用工具，可以从命令行使用正在运行的拓扑。 使用 `storm -h` 可以获取完整的命令行列表。

### <a name="list-topologies"></a>列出拓扑

使用以下命令可以列出所有正在运行的拓扑：

```shell
storm list
```

此命令返回类似于以下文本的信息：

```shell
Topology_name        Status     Num_tasks  Num_workers  Uptime_secs
-------------------------------------------------------------------
WordCount            ACTIVE     29         2            263
```

### <a name="deactivate-and-reactivate-a-topology"></a>停用和重新激活拓扑

停用拓扑将暂停，直到终止或重新激活拓扑。 使用以下命令来停用或重新激活：

```shell
storm Deactivate TOPOLOGYNAME
```

```shell
storm Activate TOPOLOGYNAME
```

### <a name="kill-a-running-topology"></a>终止正在运行的拓扑

Storm 拓扑在启动后，会不断运行，直到将其停止。 若要停止拓扑，请使用以下命令：

```shell
storm kill TOPOLOGYNAME
```

### <a name="rebalance-a-topology"></a>重新平衡拓扑

重新平衡拓扑可以让系统修改拓扑的并行度。 例如，如果调整了群集的大小以添加更多说明，则重新平衡允许拓扑查看新节点。

> [!WARNING]  
> 重新平衡拓扑将停用拓扑，将辅助角色均匀地分布到群集，然后将拓扑返回到发生重新平衡之前的状态。 如果拓扑处于活动状态，则它会再次变为活动状态。 如果它原本已停用，则将保持停用状态。

```shell
storm rebalance TOPOLOGYNAME
```

## <a name="monitor-and-manage-a-topology-using-the-storm-ui"></a>使用风暴 UI 监视和管理拓扑

风暴 UI 提供一个 web 界面用于处理正在运行的拓扑，并包括在 HDInsight 群集上。 若要查看风暴 UI，请使用 web 浏览器打开 `https://CLUSTERNAME.azurehdinsight.net/stormui`，其中*CLUSTERNAME*是群集的名称。

> [!NOTE]  
> 如果系统要求提供用户名和密码，请输入创建群集时使用的群集管理员用户名和密码。

### <a name="storm-ui-main-page"></a>风暴 UI 主页

Storm UI 的主页面提供以下信息：

| 部分 | 说明 |
| --- | --- |
| 群集摘要| 有关 Storm 群集的基本信息。 |
| Nimbus 摘要 | 基本 Nimbus 信息的列表。 |
| 拓扑摘要 | 正在运行的拓扑的列表。 若要查看有关特定拓扑的详细信息，请在 "**名称**" 列中选择其链接。 |
| 主管摘要 | 有关 Storm 监督器的信息。 若要查看与特定监控器关联的辅助角色资源，请在 "**主机**" 或 " **Id** " 列中选择其链接。 |
| Nimbus 配置 | 群集的 Nimbus 配置。 |

风暴 UI 主页类似于以下网页：

![主页，风暴 UI，Apache Storm 拓扑，Azure](./media/apache-storm-deploy-monitor-topology-linux/apache-storm-web-ui-main-page.png)

#### <a name="topology-summary"></a>拓扑摘要

选择“拓扑摘要”部分中的链接会显示有关拓扑的以下信息：

| 部分 | 说明 |
| --- | --- |
| 拓扑摘要 | 有关拓扑的基本信息。 |
| 拓扑操作| 可对拓扑执行的管理操作。 本部分稍后将介绍可用的操作。 |
| 拓扑统计信息 | 有关拓扑的统计信息。 若要为此部分中的条目设置时间范围，请在 "**窗口**" 列中选择其链接。 |
| Spout *（时间范围）* | 拓扑使用的 Spout。 若要查看有关特定 spout 的详细信息，请在 " **Id** " 列中选择其链接。 |
| 螺栓 *（时间范围）* | 拓扑使用的 Bolt。 若要查看有关特定螺栓的详细信息，请在 " **Id** " 列中选择其链接。 |
| 辅助角色资源 | 辅助角色的列表。 若要查看有关特定辅助角色资源的详细信息，请在 "**主机**" 列中选择其链接。 |
| 拓扑可视化 | 显示拓扑可视化的 "**显示可视化效果**" 按钮。 |
| 拓扑配置 | 所选拓扑的配置。 |

"风暴拓扑摘要" 页看起来与此网页类似：

![拓扑摘要页，风暴 UI，Apache Storm，Azure](./media/apache-storm-deploy-monitor-topology-linux/apache-storm-web-ui-topology-summary.png)

在 "**拓扑操作**" 部分中，可以选择以下按钮执行操作：

| 按钮 | 说明 |
| --- | --- |
| 激活 | 继续处理已停用的拓扑。 |
| 停用 | 暂停正在运行的拓扑。 |
| 重新平衡 | 调整拓扑的并行度。 更改群集中的节点数后，应该重新平衡正在运行的拓扑。 此操作可让拓扑调整并行度，以弥补群集中增加或减少的节点数。<br/><br/>有关详细信息，请参阅<a href="https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html" target="_blank">了解 Apache Storm 拓扑的并行度</a>。
| 终止 | 在经过指定的超时之后终止 Storm 拓扑。 |
| 调试 | 为正在运行的拓扑启动调试会话。 |
| 停止调试 | 结束正在运行的拓扑的调试会话。 |
| 更改日志级别 | 修改调试日志级别。 |

##### <a name="spout-and-bolt-summary"></a>Spout 和螺栓摘要

从“Spout”或“Bolt”部分中选择 spout 会显示有关选定项的以下信息：

| 部分 | 说明 |
| --- | --- |
| 组件摘要 | 有关 Spout 或 Bolt 的基本信息。 |
| 组件操作 | **调试**和**停止调试**按钮。 |
| Spout 统计信息或螺栓统计 | 有关 Spout 或 Bolt 的统计信息。 若要为此部分中的条目设置时间范围，请在 "**窗口**" 列中选择其链接。 |
| （仅限螺栓）<br/>输入统计信息 *（时间范围）* | 有关 Bolt 使用的输入流的信息。 |
| 输出统计信息 *（时间范围）* | 有关 Spout 或 Bolt 所发出的流的信息。 |
| 分析和调试 | 用于分析和调试此页上的组件的控件。 可以设置 "**状态/超时（分钟）** " 值，还可以选择 " **JStack**"、"**重新启动辅助角色**" 和 "**堆**" 按钮。 |
| 执行器 *（时间范围）* | 有关 Spout 或 Bolt 实例的信息。 若要查看为此实例生成的诊断信息的日志，请选择特定执行器的**端口**条目。 还可以通过在 "**主机**" 列中选择与特定执行器关联的辅助角色来查看该执行器。 |
| 错误 | Spout 或 Bolt 的任何错误信息。 |

"风暴螺栓摘要" 页看起来与此网页类似：

![螺栓摘要页面，风暴 UI，Apache Storm，Azure](./media/apache-storm-deploy-monitor-topology-linux/apache-storm-web-ui-bolt-summary.png)

## <a name="monitor-and-manage-the-topology-using-the-rest-api"></a>使用 REST API 监视和管理拓扑

风暴 UI 建立在 REST API 的基础之上，因此你可以通过使用 REST API 执行类似的管理和监视任务。 使用 REST API 可以创建自定义工具来管理和监视 Storm 拓扑。

有关详细信息，请参阅 [Apache Storm UI REST API](https://storm.apache.org/releases/current/STORM-UI-REST-API.html)。 以下信息特定于将 REST API 与 Apache Storm on HDInsight 配合使用的情况。

> [!IMPORTANT]  
> 风暴 REST API 不能通过 internet 公开提供。 必须使用与 HDInsight 群集头节点的 SSH 隧道访问该节点。 有关创建和使用 SSH 隧道的信息，请参阅[使用 ssh 隧道访问 Azure HDInsight](../hdinsight-linux-ambari-ssh-tunnel.md)。

### <a name="base-uri"></a>基本 URI

基于 Linux 的 HDInsight 群集上 REST API 的基本 URI 位于 URL 地址 `https://HEADNODEFQDN:8744/api/v1/`上，你可以在其中将*HEADNODEFQDN*替换为头节点。 头节点的域名在群集创建过程中生成，并且不是静态的。

可以通过多种方式找到群集头节点的完全限定的域名（FQDN）：

| FQDN 发现方法 | 说明 |
| --- | --- |
| SSH 会话 | 通过与群集建立的 SSH 会话使用 `headnode -f` 命令。 |
| Ambari Web | 在 Ambari 群集网页（`https://CLUSTERNAME.azurehdinsight.net`）上，从页面顶部选择 "**服务**"，然后选择 "**风暴**"。 在“摘要”选项卡中，选择“Storm UI 服务器”。 页面顶部会显示承载 Storm UI 和 REST API 的节点的 FQDN。 |
| Ambari REST API | 使用 `curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/STORM/components/STORM_UI_SERVER"` 命令来检索有关 Storm UI 和 REST API 正在其上运行的节点的信息。 将*CLUSTERNAME*的两个实例替换为群集名称。 出现提示时，输入用户（管理员）帐户的密码。 在响应中，JSON 输出的 "host_name" 条目包含节点的 FQDN。 |

### <a name="authentication"></a>Authentication

对 REST API 的请求必须使用*基本身份验证*，因此你必须使用 HDInsight 群集的管理员名称和密码。

> [!NOTE]  
> 由于基本身份验证使用明文发送，因此*始终*应该使用 HTTPS 来保护与群集之间的通信。

### <a name="return-values"></a>返回值

从 REST API 返回的信息只能从该群集中使用。 例如，无法从 internet 访问为[Apache ZooKeeper](https://zookeeper.apache.org/)服务器返回的完全限定的域名（FQDN）。

## <a name="next-steps"></a>后续步骤

了解如何[使用 Apache Maven 开发基于 Java 的拓扑](apache-storm-develop-java-topology.md)。

有关更多示例拓扑的列表，请参阅[Azure HDInsight 中的示例 Apache Storm 拓扑](apache-storm-example-topology.md)。
