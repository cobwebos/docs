---
title: 在 Azure HDInsight 中部署和管理 Apache Storm 拓扑
description: Learn how to deploy, monitor, and manage Apache Storm topologies using the Storm Dashboard on Linux-based HDInsight. 使用 Hadoop Tools for Visual Studio。
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/07/2019
ms.openlocfilehash: 82c5db4f75f131ebdc2434955108e7d50237d9ba
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74228938"
---
# <a name="deploy-and-manage-apache-storm-topologies-on-azure-hdinsight"></a>在 Azure HDInsight 中部署和管理 Apache Storm 拓扑 

本文档介绍有关如何在 HDInsight 群集上管理和监视 Storm 上运行的 [Apache Storm](https://storm.apache.org/) 拓扑的基本知识。

## <a name="prerequisites"></a>必备组件

* HDInsight 上的 Apache Storm 群集。 请参阅[使用 Azure 门户创建 Apache Hadoop 群集](../hdinsight-hadoop-create-linux-clusters-portal.md)，并选择 **Storm** 作为**群集类型**。

* (Optional) Familiarity with Secure Shell (SSH) and Secure Copy (SCP). 有关详细信息，请参阅[使用 SSH 连接到 HDInsight (Apache Hadoop)](../hdinsight-hadoop-linux-use-ssh-unix.md)。

* (Optional) Visual Studio, Azure SDK 2.5.1 or newer, and the Data Lake Tools for Visual Studio. For more information, see [Apache Hadoop & Visual Studio Data Lake Tools](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

## <a name="submit-a-topology-using-visual-studio"></a>Submit a topology using Visual Studio

You can use the Data Lake Tools for Visual Studio to submit C# or hybrid topologies to your Storm cluster. 以下步骤使用了一个示例应用程序。 For information about topology creation using the Data Lake Tools, see [Apache Storm topologies with Visual Studio and C#](apache-storm-develop-csharp-visual-studio-topology.md).

1. If you haven't already installed the latest version of the Data Lake tools for Visual Studio, see [Use Data Lake Tools for Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

    > [!NOTE]  
    > 针对 Visual Studio 的 Data Lake 工具以前称为 Visual Studio 的 HDInsight 工具。
    >
    > Data Lake Tools for Visual Studio are included in the **Azure Workload** for Visual Studio 2019.

2. 打开 Visual Studio。

3. In the **Start** window, select **Create a new project**.

4. In the **Create a new project** window, select the search box, and enter *Storm*. Then choose **Storm Sample** from the result list and select **Next**.

5. In the **Configure your new project** window, enter a **Project name**, and go to or create a **Location** to save the new project in. 然后选择“创建”。

    ![Configure your new project window, Visual Studio](./media/apache-storm-deploy-monitor-topology-linux/apache-storm-sample1.png)

6. In **Solution Explorer**, right-click the project, and choose **Submit to Storm on HDInsight**.

    > [!NOTE]  
    > 如果出现提示，请输入 Azure 订阅的登录凭据。 如果有多个订阅，请登录包含 Storm on HDInsight 群集的订阅。

7. In the **Submit Topology** dialog box, under the **Storm Cluster** drop-down list, choose your Storm on HDInsight cluster, and then select **Submit**. You can monitor whether the submission is successful by viewing the **Output** pane.

## <a name="submit-a-topology-using-ssh-and-the-storm-command"></a>Submit a topology using SSH and the Storm command

To submit a topology to Storm using SSH:

1. 使用 SSH 连接到 HDInsight 群集。 Replace `USERNAME` with the name of your SSH user name (such as *sshuser*). 将 `CLUSTERNAME` 替换为 HDInsight 群集名。

    ```shell
    ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    For more information on using SSH to connect to your HDInsight cluster, see [Connect to HDInsight (Apache Hadoop) using SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Use the following command to start the *WordCount* example topology:

    ```ssh
    storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-*.jar org.apache.storm.starter.WordCountTopology WordCount
    ```

    此命令启动群集上的示例 WordCount 拓扑。 此拓扑随机生成句子，并统计句子中每个单词的出现次数。

    > [!NOTE]  
    > When submitting topology to the cluster, you must first copy the .jar file containing the cluster before using the `storm` command. 要将文件复制到群集，可以使用 `scp` 命令。 例如，输入 `scp FILENAME.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.jar`。
    >
    > The *WordCount* example, and other storm starter examples, are already included on your cluster at `/usr/hdp/current/storm-client/contrib/storm-starter/`.

## <a name="submit-a-topology-programmatically"></a>Submit a topology programmatically

可以使用 Nimbus 服务以编程方式部署拓扑。 [https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology](https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology) 提供了 Java 应用程序示例，演示如何通过 Nimbus 服务部署和启动拓扑。

## <a name="monitor-and-manage-a-topology-in-visual-studio"></a>Monitor and manage a topology in Visual Studio

When you submit a topology using Visual Studio, the **Storm Topologies View** window appears. 从列表中选择拓扑，以查看有关正在运行的拓扑的信息。

![Monitor topology, Storm Topologies View window, Visual Studio](./media/apache-storm-deploy-monitor-topology-linux/visual-studio-monitor.png)

> [!NOTE]  
> 也可以从“服务器资源管理器”查看“Storm 拓扑”。 展开“Azure” > “HDInsight”，右键单击 Storm on HDInsight 群集，然后选择“查看 Storm 拓扑”。

选择 Spout 或 Bolt 的形状可查看有关这些组件的信息。 A tooltip with component information appears for the item selected.

### <a name="deactivate-and-reactivate-a-topology"></a>Deactivate and reactivate a topology

Deactivating a topology pauses it until the topology is killed or reactivated. To do these operations, use the **Deactivate** and **Reactivate** buttons in the **Actions** area at the top of the **Storm Topologies View** window.

### <a name="rebalance-a-topology"></a>Rebalance a topology

重新平衡拓扑可以让系统修改拓扑的并行度。 For example, if you've resized the cluster to add more notes, rebalancing allows a topology to see the new nodes.

To rebalance a topology, use the **Rebalance** button in the **Actions** area of the **Storm Topologies View** window.

> [!WARNING]  
> Rebalancing a topology deactivates the topology, redistributes workers evenly across the cluster, and then returns the topology to the state it was in before rebalancing occurred. If the topology was active, it becomes active again. If the topology was deactivated, it remains deactivated.

### <a name="kill-a-running-topology"></a>终止正在运行的拓扑

Storm topologies continue running until they're stopped or the cluster is deleted. To stop a topology, use the **Kill** button in the **Actions** area.

## <a name="monitor-and-manage-a-topology-using-ssh-and-the-storm-command"></a>Monitor and manage a topology using SSH and the Storm command

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

### <a name="deactivate-and-reactivate-a-topology"></a>Deactivate and reactivate a topology

Deactivating a topology pauses it until the topology is killed or reactivated. Use the following commands to deactivate or reactivate:

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

### <a name="rebalance-a-topology"></a>Rebalance a topology

重新平衡拓扑可以让系统修改拓扑的并行度。 For example, if you've resized the cluster to add more notes, rebalancing allows a topology to see the new nodes.

> [!WARNING]  
> Rebalancing a topology deactivates the topology, redistributes workers evenly across the cluster, and then returns the topology to the state it was in before rebalancing occurred. If the topology was active, it becomes active again. 如果它原本已停用，则将保持停用状态。

```shell
storm rebalance TOPOLOGYNAME
```

## <a name="monitor-and-manage-a-topology-using-the-storm-ui"></a>Monitor and manage a topology using the Storm UI

The Storm UI provides a web interface for working with running topologies, and it's included on your HDInsight cluster. To view the Storm UI, use a web browser to open `https://CLUSTERNAME.azurehdinsight.net/stormui`, where *CLUSTERNAME* is the name of your cluster.

> [!NOTE]  
> If you're asked to provide a user name and password, enter the cluster administrator username and password that you used when creating the cluster.

### <a name="storm-ui-main-page"></a>Storm UI main page

Storm UI 的主页面提供以下信息：

| 部分 | 描述 |
| --- | --- |
| **Cluster summary** | 有关 Storm 群集的基本信息。 |
| **Nimbus summary** | A list of basic Nimbus information. |
| **Topology summary** | 正在运行的拓扑的列表。 To view more information about a specific topology, select its link in the **Name** column. |
| **Supervisor summary** | 有关 Storm 监督器的信息。 To see the worker resources associated with a specific supervisor, select its link in the **Host** or **Id** column. |
| **Nimbus configuration** | 群集的 Nimbus 配置。 |

The Storm UI main page looks similar to this web page:

![Main page, Storm UI, Apache Storm topologies, Azure Insight](./media/apache-storm-deploy-monitor-topology-linux/apache-storm-web-ui-main-page.png)

#### <a name="topology-summary"></a>拓扑摘要

选择“拓扑摘要”部分中的链接会显示有关拓扑的以下信息：

| 部分 | 描述 |
| --- | --- |
| **Topology summary** | 有关拓扑的基本信息。 |
| **Topology actions** | Management actions that you can do for the topology. The available actions are described later in this section. |
| **Topology stats** | 有关拓扑的统计信息。 To set the time frame for an entry in this section, select its link in the **Window** column. |
| **Spouts** *(time frame)* | 拓扑使用的 Spout。 To view more information about a specific spout, select its link in the **Id** column. |
| **Bolts** *(time frame)* | 拓扑使用的 Bolt。 To view more information about a specific bolt, select its link in the **Id** column. |
| **Worker resources** | A list of worker resources. To view more information about a specific worker resource, select its link in the **Host** column. |
| **Topology visualization** | A **Show Visualization** button that displays a visualization of the topology. |
| **Topology configuration** | 所选拓扑的配置。 |

The Storm topology summary page looks similar to this web page:

![Topology summary page, Storm UI, Apache Storm, Azure Insight](./media/apache-storm-deploy-monitor-topology-linux/apache-storm-web-ui-topology-summary.png)

In the **Topology actions** section, you can select the following buttons to do an action:

| 按钮 | 描述 |
| --- | --- |
| **激活** | 继续处理已停用的拓扑。 |
| **Deactivate** | 暂停正在运行的拓扑。 |
| **Rebalance** | 调整拓扑的并行度。 You should rebalance running topologies after you've changed the number of nodes in the cluster. This operation allows the topology to adjust parallelism to compensate for the additional or reduced number of nodes in the cluster.<br/><br/>有关详细信息，请参阅<a href="https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html" target="_blank">了解 Apache Storm 拓扑的并行度</a>。
| **Kill** | 在经过指定的超时之后终止 Storm 拓扑。 |
| **调试** | Begins a debugging session for the running topology. |
| **Stop Debug** | Ends the debugging session for the running topology. |
| **Change Log Level** | Modifies the debugging log level. |

##### <a name="spout-and-bolt-summary"></a>Spout and bolt summary

从“Spout”或“Bolt”部分中选择 spout 会显示有关选定项的以下信息：

| 部分 | 描述 |
| --- | --- |
| **Component summary** | 有关 Spout 或 Bolt 的基本信息。 |
| **Component actions** | **Debug** and **Stop Debug** buttons. |
| **Spout stats** or **Bolt stats** | 有关 Spout 或 Bolt 的统计信息。 To set the time frame for an entry in this section, select its link in the **Window** column. |
| (Bolt-only)<br/>**Input stats** *(time frame)* | 有关 Bolt 使用的输入流的信息。 |
| **Output stats** *(time frame)* | 有关 Spout 或 Bolt 所发出的流的信息。 |
| **Profiling and debugging** | Controls for profiling and debugging the components on this page. You can set the **Status / Timeout (Minutes)** value, and you can select buttons for **JStack**, **Restart Worker**, and **Heap**. |
| **Executors** *(time frame)* | 有关 Spout 或 Bolt 实例的信息。 To view a log of diagnostic information produced for this instance, select the **Port** entry for a specific executor. You can also see the worker resources associated with a specific executor by selecting its link in the **Host** column. |
| **错误** | Spout 或 Bolt 的任何错误信息。 |

The Storm bolt summary page looks similar to this web page:

![Bolt summary page, Storm UI, Apache Storm, Azure Insight](./media/apache-storm-deploy-monitor-topology-linux/apache-storm-web-ui-bolt-summary.png)

## <a name="monitor-and-manage-the-topology-using-the-rest-api"></a>Monitor and manage the topology using the REST API

The Storm UI is built on top of the REST API, so you can do similar management and monitoring tasks by using the REST API. 使用 REST API 可以创建自定义工具来管理和监视 Storm 拓扑。

有关详细信息，请参阅 [Apache Storm UI REST API](https://storm.apache.org/releases/current/STORM-UI-REST-API.html)。 以下信息特定于将 REST API 与 Apache Storm on HDInsight 配合使用的情况。

> [!IMPORTANT]  
> The Storm REST API is not publicly available over the internet. It must be accessed using an SSH tunnel to the HDInsight cluster head node. For information on creating and using an SSH tunnel, see [Use SSH tunneling to access Azure HDInsight](../hdinsight-linux-ambari-ssh-tunnel.md).

### <a name="base-uri"></a>基本 URI

The base URI for the REST API on Linux-based HDInsight clusters is available at URL address `https://HEADNODEFQDN:8744/api/v1/`, where you replace *HEADNODEFQDN* with the head node. The domain name of the head node is generated during cluster creation and isn't static.

You can find the fully qualified domain name (FQDN) for the cluster head node in several ways:

| FQDN discovery method | 描述 |
| --- | --- |
| SSH session | Use the command `headnode -f` from an SSH session to the cluster. |
| Ambari Web | On the Ambari cluster web page (`https://CLUSTERNAME.azurehdinsight.net`), select **Services** from the top of the page, then select **Storm**. 在“摘要”选项卡中，选择“Storm UI 服务器”。 页面顶部会显示承载 Storm UI 和 REST API 的节点的 FQDN。 |
| Ambari REST API | Use the command `curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/STORM/components/STORM_UI_SERVER"` to retrieve information about the node that the Storm UI and REST API are running on. Replace the two instances of *CLUSTERNAME* with the cluster name. When you're prompted, enter the password for the user (admin) account. In the response, the "host_name" entry of the JSON output contains the FQDN of the node. |

### <a name="authentication"></a>Authentication

Requests to the REST API must use *basic authentication*, so you have to use the administrator name and password for the HDInsight cluster.

> [!NOTE]  
> 由于基本身份验证使用明文发送，因此*始终*应该使用 HTTPS 来保护与群集之间的通信。

### <a name="return-values"></a>返回值

从 REST API 返回的信息只能从该群集中使用。 For example, the fully qualified domain name (FQDN) returned for [Apache ZooKeeper](https://zookeeper.apache.org/) servers isn't accessible from the internet.

## <a name="next-steps"></a>后续步骤

了解如何[使用 Apache Maven 开发基于 Java 的拓扑](apache-storm-develop-java-topology.md)。

For a list of more example topologies, see [Example Apache Storm topologies in Azure HDInsight](apache-storm-example-topology.md).
