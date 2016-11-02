<properties
	pageTitle="Apache Storm 教程：HDInsight 上基于 Linux 的 Storm 入门 | Microsoft Azure"
	description="开始在基于 Linux 的 HDInsight 上使用 Apache Storm 和 Storm 初学者示例进行大数据分析。了解如何使用 Storm 实时处理数据。"
	keywords="apache storm,apache storm 教程,大数据分析,storm 初学者"
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="java"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="08/16/2016"
   ms.author="larryfr"/>


# Apache Storm 教程：用于在 HDInsight 上进行大数据分析的 Storm 初学者示例入门

Apache Storm 是一个可扩展的、具有容错能力的分布式实时计算系统，用于处理数据流。使用 Azure HDInsight 上的 Storm，你可以创建一个基于云的、用于实时执行大数据分析的 Storm 群集。

> [AZURE.NOTE] 本文中的步骤将创建基于 Linux 的 HDInsight 群集。有关在 HDInsight 群集上创建基于 Windows 的 Storm 的步骤，请参阅 [Apache Storm 教程：开始在 HDInsight 上使用 Storm 初学者示例进行数据分析](hdinsight-apache-storm-tutorial-get-started.md)

## 开始之前

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

你必须具备以下条件才能成功完成本 Apache Storm 教程：

- **一个 Azure 订阅**。请参阅[获取 Azure 免费试用版](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。

- **熟悉 SSH 和 SCP**。有关如何在 HDInsight 中使用 SSH 和 SCP 的详细信息，请参阅以下文档：

    - **Linux、Unix 或 OS X 客户端**：请参阅[在 Linux、OS X 或 Unix 中的 HDInsight 上将 SSH 与基于 Linux 的 Hadoop 配合使用](hdinsight-hadoop-linux-use-ssh-unix.md)

	- **Windows 客户端**：请参阅[在 Windows 中的 HDInsight 上将 SSH 与基于 Linux 的 Hadoop 配合使用](hdinsight-hadoop-linux-use-ssh-windows.md)

## 创建 Storm 群集

在本部分中，使用 Azure Resource Manager 模板创建 HDInsight 3.2 版群集（Storm 0.9.3 版）。有关 HDInsight 版本及其 SLA 的信息，请参阅 [HDInsight 组件版本](hdinsight-component-versioning.md)。有关其他群集创建方法，请参阅 [Create HDInsight clusters](hdinsight-hadoop-provision-linux-clusters.md)（创建 HDInsight 群集）。

1. 单击下面的图像即可在 Azure 门户中打开该模板。

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-storm-cluster-in-hdinsight.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/zh-CN/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>
    
    该模板位于公共 blob 容器中，*https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-storm-cluster-in-hdinsight.json*。
   
2. 在“参数”边栏选项卡中，输入以下内容：

    - **ClusterName**：为将创建的 Hadoop 群集输入名称。
    - **群集登录名和密码**：默认登录名是 admin。
    - **SSH 用户名和密码**。
    
    请记下这些值。本教程后面的步骤中将会用到它们。

    > [AZURE.NOTE] 使用 SSH 可通过命令行远程访问 HDInsight 群集。在通过 SSH 连接群集时，将用到此处所用的用户名和密码。此外，SSH 用户名必须唯一，因为它将在所有 HDInsight 群集节点上创建一个用户帐户。下面是一些保留给群集上的服务使用的帐户名，不能将其用作 SSH 用户名：
    >
    > root、hdiuser、storm、hbase、ubuntu、zookeeper、hdfs、yarn、mapred、hbase、hive、oozie、falcon、sqoop、admin、tez、hcat、hdinsight-zookeeper。

	> 有关如何将 SSH 与 HDInsight 配合使用的详细信息，请参阅以下文章之一：

	> * [在 Linux、Unix 或 OS X 中的 HDInsight 上将 SSH 与基于 Linux 的 Hadoop 配合使用](hdinsight-hadoop-linux-use-ssh-unix.md)
	> * [在 Windows 中的 HDInsight 上将 SSH 与基于 Linux 的 Hadoop 配合使用](hdinsight-hadoop-linux-use-ssh-windows.md)

    
3\. 单击“确定”以保存参数。

4\. 在“自定义部署”边栏选项卡中，单击“资源组”下拉框，然后单击“新建”以创建新资源组。资源组是对群集、依赖存储帐户和其他链接资源进行分组的容器。

5\. 单击“法律条款”，然后单击“创建”。

6\. 单击“创建”。你会看到一个标题为“为模板部署提交部署”的新磁贴。创建群集和 SQL 数据库大约需要 20 分钟时间。


##在 HDInsight 上运行 Storm 初学者示例

[storm-starter](https://github.com/apache/storm/tree/master/examples/storm-starter) 示例包含在 HDInsight 群集中。在以下步骤中，你将运行 WordCount 示例。

1. 使用 SSH 连接到 HDInsight 群集：

		ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
		
	如果你使用了密码来保护 SSH 帐户，系统会提示你输入密码。如果你使用了公钥，则可能需要使用 `-i` 参数来指定匹配的私钥。例如，`ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`。
		
	有关将 SSH 与基于 Linux 的 HDInsight 配合使用的详细信息，请参阅以下文章：
	
	* [在 Linux、Unix 或 OS X 中的 HDInsight 上将 SSH 与基于 Linux 的 Hadoop 配合使用](hdinsight-hadoop-linux-use-ssh-unix.md)

	* [在 Windows 中的 HDInsight 上将 SSH 与基于 Linux 的 Hadoop 配合使用](hdinsight-hadoop-linux-use-ssh-windows)

2. 使用以下命令启动示例拓扑：

        storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-0.10.0.2.4.2.4-5.jar storm.starter.WordCountTopology wordcount
		
	> [AZURE.NOTE] 由于 HDinsight 将与 Storm 的新版本一起更新，因此文件名的 `0.10.0.2.4.2.4-5` 部分可能会更改。

    随后将在群集上启动具有友好名称“wordcount”的示例 WordCount 拓扑。该拓扑将随机生成句子，并统计句子中每个单词的出现次数。

    > [AZURE.NOTE] 将拓扑提交到群集时，必须先复制包含群集的 jar 文件，然后再使用 `storm` 命令。可以从文件所在的客户端使用 `scp` 命令实现此目的。例如：`scp FILENAME.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.jar`
    >
    > WordCount 示例和其他 Storm 初学者示例已经包含在群集中，其位置为 `/usr/hdp/current/storm-client/contrib/storm-starter/`。

##监视拓扑

Storm UI 提供一个 Web 界面用于处理正在运行的拓扑，你的 HDInsight 群集随附了此界面。

执行以下步骤以使用 Storm UI 来监视拓扑。

1. 将 Web 浏览器打开到 https://CLUSTERNAME.azurehdinsight.net/stormui，其中 __CLUSTERNAME__ 是群集的名称。此时将打开 Storm UI。

	> [AZURE.NOTE] 如果系统要求你提供用户名和密码，请输入创建群集时使用的群集管理员用户名 (admin) 和密码。

2. 在“拓扑摘要”下，选择“名称”列中的“Wordcount”条目。这将显示有关拓扑的详细信息。

	![包含 Storm 初学者项目 WordCount 拓扑信息的 Storm 仪表板。](./media/hdinsight-apache-storm-tutorial-get-started-linux/topology-summary.png)

	此页提供以下信息：

	* **拓扑统计信息** - 有关拓扑性能的基本信息，已组织到时间窗口中。

		> [AZURE.NOTE] 选择特定的时间窗口会更改页面其他部分中显示的信息的时间窗口。

	* **Spout** - 有关 spout 的基本信息，包括每个 spout 返回的最后一个错误。

	* **Bolt** - 有关 bolt 的基本信息。

	* **拓扑配置** - 有关拓扑配置的详细信息。

	此页还提供可对拓扑执行的操作：

	* **激活** - 继续处理已停用的拓扑。

	* **停用** - 暂停正在运行的拓扑。

	* **重新平衡** - 调整拓扑的并行度。更改群集中的节点数目之后，你应该重新平衡正在运行的拓扑。这可让拓扑调整并行度，以弥补群集中增加/减少的节点数目。有关详细信息，请参阅[了解 Storm 拓扑的并行度](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html)。

	* **终止** - 在经过指定的超时之后终止 Storm 拓扑。

3. 在此页中，从“Spout”或“Bolt”部分中选择一个条目。这将显示有关选定组件的信息。

	![包含有关选定组件信息的 Storm 仪表板。](./media/hdinsight-apache-storm-tutorial-get-started-linux/component-summary.png)

	此页显示以下信息：

	* **Spout/Bolt 统计信息** - 有关组件性能的基本信息，已组织到时间窗口中。

		> [AZURE.NOTE] 选择特定的时间窗口会更改页面其他部分中显示的信息的时间窗口。

	* **输入统计信息**（仅限 Bolt）- 有关生成 Bolt 所用数据的组件的信息。

	* **输出统计信息** - 对此 Bolt 发出的数据的信息。

	* **执行器** - 有关此组件的实例的信息。

	* **错误** - 此组件生成的错误。

4. 在查看 spout 或 bolt 的详细信息时，从“执行器”部分中的“端口”列中选择一个条目可以查看组件特定实例的详细信息。

		2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: split default ["with"]
		2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: split default ["nature"]
		2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [snow]
		2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [snow, 747293]
		2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [white]
		2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [white, 747293]
		2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [seven]
		2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [seven, 1493957]

	从这些数据中，你可以看到，单词 **seven** 出现了 1493957 次。就是自从启动此拓扑以来该单词出现的次数。

##停止拓扑

返回到单词计数拓扑的“拓扑摘要”页，然后从“拓扑操作”部分中选择“终止”按钮。出现提示时，输入停止拓扑之前要等待的秒数，即 10。超时期限过后，当你访问仪表板的“Storm UI”部分时，将不再显示该拓扑。

##删除群集

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

##<a id="next"></a>后续步骤

在本 Apache Storm 教程中，你已使用 Storm 初学者示例学习如何创建 Storm on HDInsight 群集，以及如何使用 Storm 仪表板来部署、监视和管理 Storm 拓扑。接下来，了解如何[使用 Maven 开发基于 Java 的拓扑](hdinsight-storm-develop-java-topology.md)。

如果已知道怎样开发基于 Java 的拓扑并想要将现有拓扑部署到 HDInsight，请参阅[在 HDInsight 上部署和管理 Apache Storm 拓扑](hdinsight-storm-deploy-monitor-topology-linux.md)。

[apachestorm]: https://storm.incubator.apache.org
[stormdocs]: http://storm.incubator.apache.org/documentation/Documentation.html
[stormstarter]: https://github.com/apache/storm/tree/master/examples/storm-starter
[stormjavadocs]: https://storm.incubator.apache.org/apidocs/
[azureportal]: https://manage.windowsazure.com/
[hdinsight-provision]: hdinsight-provision-clusters.md
[preview-portal]: https://portal.azure.com/

<!---HONumber=AcomDC_0921_2016-->