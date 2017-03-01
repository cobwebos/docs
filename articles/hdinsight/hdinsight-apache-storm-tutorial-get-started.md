---
title: "Apache Storm 教程：Storm 入门 | Microsoft Docs"
description: "开始在 HDInsight 上使用 Apache Storm 和 Storm 初学者示例进行大数据分析。 了解如何使用 Storm 实时处理数据。"
keywords: "apache storm,apache storm 教程,大数据分析,storm 初学者"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 6f93f7ff-0736-4708-80ef-4289dae532a9
ms.service: hdinsight
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/17/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: ccd1dffda19718a434fc09bb74a536714799740a
ms.openlocfilehash: 4b70f30bf8d5a0545bb85a791cb63f514be0ba74


---
# <a name="apache-storm-tutorial-get-started-with-the-storm-starter-samples-for-big-data-analytics-on-hdinsight"></a>Apache Storm 教程：用于在 HDInsight 上进行大数据分析的 Storm 初学者示例入门
Apache Storm 是一个可扩展的、具有容错能力的分布式实时计算系统，用于处理数据流。 使用 Microsoft Azure HDInsight 上的 Storm，可创建一个基于云的、用于实时执行大数据分析的 Storm 群集。 

> [!IMPORTANT]
> 本文中的步骤将创建基于 Windows 的 HDInsight 群集。 Windows 上仅可使用低于 HDInsight 3.4 版本的 HDInsight。 Linux 是 HDInsight 3.4 或更高版本上使用的唯一操作系统。 有关详细信息，请参阅 [HDInsight 在 Windows 上即将弃用](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date)。
>
> 有关在 HDInsight 群集上创建基于 Linux 的 Storm 的步骤，请参阅 [Apache Storm 教程：开始在 HDInsight 上使用 Storm 初学者示例进行数据分析](hdinsight-apache-storm-tutorial-get-started-linux.md)

## <a name="prerequisites"></a>先决条件
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

你必须具备以下条件才能成功完成本 Apache Storm 教程：

* **一个 Azure 订阅**。 请参阅 [获取 Azure 免费试用版](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。

### <a name="access-control-requirements"></a>访问控制要求
[!INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-a-storm-cluster"></a>创建 Storm 群集
Storm on HDInsight 使用 Azure Blob 存储来存储提交到群集的日志文件和拓扑。 使用以下步骤来创建用于群集的 Azure 存储帐户：

1. 登录到 [Azure 门户][preview-portal]。
2. 依次选择“新建”、“数据分析”和“HDInsight”。
   
    ![在 Azure 门户中创建新群集](./media/hdinsight-apache-storm-tutorial-get-started/new-cluster.png)
3. 输入**群集名称**。 如果该**群集名称**可用，则名称旁边会出现绿色的复选标记。
4. 如果你有多个订阅，请选择“订阅”条目来选择要用于群集的 Azure 订阅。
5. 使用“选择群集类型”来选择 **Storm** 群集。 选择 Windows **操作系统**。 选择标准**群集层**。 最后使用“选择”按钮保存这些设置。
   
   ![群集名称、群集类型和操作系统类型](./media/hdinsight-apache-storm-tutorial-get-started/clustertype.png)
6. 对于**资源组**，可使用下拉列表查看现有资源组的列表，然后选择要在其中创建群集的资源组。 或者，可选择“新建”，然后输入新资源组的名称。 出现绿色复选标记表示可以使用这个新的组名称。
7. 选择“凭据”，然后输入“群集登录用户名”和“群集登录密码”。 最后，使用“选择”来设置凭据。 本文档中不会使用远程桌面，因此可将其禁用。
   
    ![群集凭据边栏选项卡](./media/hdinsight-apache-storm-tutorial-get-started/clustercredentials.png)
8. 对于**数据源**，可选择该条目以选择现有数据源，或创建一个新的数据源。
   
    ![数据源边栏选项卡](./media/hdinsight-apache-storm-tutorial-get-started/datasource.png)
   
    目前可选择 Azure 存储帐户作为 HDInsight 群集数据源。 通过以下信息来了解“数据源”边栏选项卡上的条目。
   
   * **选择方法**：将此项设置为“来自所有订阅”，以便能够浏览订阅中的存储帐户。 如果想要输入现有存储帐户的“存储名称”和“访问密钥”，请设置为“访问密钥”。
   * **新建**：用于创建新的存储帐户。 使用出现的字段输入存储帐户名称。 如果该名称可用，将出现绿色复选标记。
   * **选择默认容器**：使用此选项输入要用于该群集的默认容器名称。 尽管你可以输入任何名称，但我们建议使用与群集相同的名称，以方便辨别用于这个特定群集的容器。
   * **位置**：存储帐户所在的地理区域，或要在其中创建存储帐户的地理区域。
     
     > [!IMPORTANT]
     > 选择默认数据源位置的同时会设置 HDInsight 群集的位置。 群集和默认数据源必须位于同一区域。
     > 
     > 
   * **选择**：用于保存数据源配置。
9. 选择“节点定价层”显示针对此群集创建的节点的相关信息。 默认情况下，辅助角色节点数设置为 **4**。 将其设置为 **1**，因为这对本教程来说已足够且可以降低群集成本。 群集的预计成本显示在此边栏选项卡底部。
   
    ![节点定价层边栏选项卡](./media/hdinsight-apache-storm-tutorial-get-started/nodepricingtiers.png)
   
    使用“选择”保存“节点定价层”信息。
10. 选择“可选配置”。 此边栏选项卡允许你选择群集版本，并配置其他可选设置，如加入**虚拟网络**。
    
     ![可选配置边栏选项卡](./media/hdinsight-apache-storm-tutorial-get-started/optionalconfiguration.png)
11. 确保选中“固定到启动板”，然后选择“创建”。 这将会创建群集，并将该群集的磁贴添加到 Azure 门户的启动板。 该图标指示群集正在预配，完成预配后，将改为显示 HDInsight 图标。
    
    | 预配时 | 预配完成 |
    | --- | --- |
    | ![启动板上的预配指示器](./media/hdinsight-apache-storm-tutorial-get-started/provisioning.png) |![已预配群集磁贴](./media/hdinsight-apache-storm-tutorial-get-started/provisioned.png) |
    
    > [!NOTE]
    > 创建群集需要一些时间，通常约 15 分钟左右。 使用启动板上的磁贴或页面左侧的“通知”条目检查预配进程。
    > 
    > 

## <a name="run-a-storm-starter-sample-on-hdinsight"></a>在 HDInsight 上运行 Storm 初学者示例
本 Apache Storm 教程将介绍如何在 GitHub 上使用 Storm 初学者示例执行大型数据分析。

每个 Storm on HDInsight 群集都附带了一个 Storm 仪表板，可用于上载并在群集上运行 Storm 拓扑。 每个群集还附带了可直接从 Storm 仪表板运行的示例拓扑。

### <a name="a-idconnectaconnect-to-the-dashboard"></a><a id="connect"></a>连接到仪表板
仪表板位于 **https://&lt;clustername>.azurehdinsight.net//** 上，其中 **clustername** 是群集的名称。 此外，还可通过从启动板选择群集并在边栏选项卡顶部选择“仪表板”链接来查找仪表板的链接。

![包含 Storm“仪表板”链接的 Azure 门户](./media/hdinsight-apache-storm-tutorial-get-started/dashboard.png)

> [!NOTE]
> 在连接到仪表板时，系统将提示你输入用户名和密码。 这是创建群集时所用的管理员名称 (**admin**) 和密码。
> 
> 

加载 Storm 仪表板后，将看到“提交拓扑”窗体。

![使用 Storm 仪表板提交 Storm 初学者拓扑。](./media/hdinsight-apache-storm-tutorial-get-started/submit.png)

“提交拓扑”窗体可用于上传和运行包含 Storm 拓扑的 .jar 文件。 它还包括群集随附的几个基本示例。

### <a name="a-idrunarun-the-word-count-sample-from-the-storm-starter-project-in-github"></a><a id="run"></a>从 GitHub 中的 Storm 初学者项目运行单词计数示例
群集随附的示例包括单词计数拓扑的多种变体。 这些示例包括随机生成句子的 **spout**，和将每个句子分解成不同的单词，然后统计每个单词出现次数的 **bolt**。 这些示例来自 Apache Storm 中包含的 [Storm 初学者示例](https://github.com/apache/storm/tree/master/examples/storm-starter)。

执行以下步骤以运行 Storm 初学者示例：

1. 从“Jar 文件”下拉列表中选择“StormStarter - WordCount”。 这会在“类名”和“其他参数”字段中填充此示例的参数。
   
    ![已在 Storm 仪表板上选择 Storm 初学者项目 WordCount。](./media/hdinsight-apache-storm-tutorial-get-started/submit.png)
   
   * **类名** - 提交拓扑的 .jar 文件中的类。
   * **其他参数** - 拓扑所需的任何参数。 在此示例中，该字段用于提供已提交的拓扑的友好名称。
2. 单击“提交”。 片刻之后，“结果”字段将显示用于提交该作业的命令以及命令的结果。 “错误”字段将显示提交拓扑时出现的任何错误。
   
    ![“提交”按钮和 Storm 初学者项目 WordCount 的结果。](./media/hdinsight-apache-storm-tutorial-get-started/submit-results.png)
   
   > [!NOTE]
   > 结果不会指示已完成拓扑 - **Storm 拓扑在启动后将一直运行，直到你停止它**。 单词计数拓扑将会生成随机的句子，并会一直计算所遇到的每个单词的出现次数，直到你将其停止。
   > 
   > 

### <a name="a-idmonitoramonitor-the-topology"></a><a id="monitor"></a>监视拓扑
Storm UI 可以用于监视拓扑。

1. 从 Storm 仪表板顶部选择“Storm UI”。 这将显示群集和所有正在运行的拓扑的摘要信息。
   
    ![显示 Storm 初学者项目 WordCount 拓扑摘要的 Storm 仪表板。](./media/hdinsight-apache-storm-tutorial-get-started/stormui.png)
   
    在上述页面中，你可以看到拓扑处于活动状态的时间，以及工作线程、执行器和正在使用的任务数。
   
   > [!NOTE]
   > “名称”列包含前面通过“其他参数”字段提供的友好名称。
   > 
   > 
2. 在“拓扑摘要”下，选择“名称”列中的“Wordcount”条目。 这将显示有关拓扑的详细信息。
   
    ![包含 Storm 初学者项目 WordCount 拓扑信息的 Storm 仪表板。](./media/hdinsight-apache-storm-tutorial-get-started/topology-summary.png)
   
    此页提供以下信息：
   
   * **拓扑统计信息** - 有关拓扑性能的基本信息，已组织到时间窗口中。
     
     > [!NOTE]
     > 选择特定的时间窗口会更改页面其他部分中显示的信息的时间窗口。
     > 
     > 
   * **Spout** - 有关 spout 的基本信息，包括每个 spout 返回的最后一个错误。
   * **Bolt** - 有关 bolt 的基本信息。
   * **拓扑配置** - 有关拓扑配置的详细信息。
     
     此页还提供可对拓扑执行的操作：
   * **激活** - 继续处理已停用的拓扑。
   * **停用** - 暂停正在运行的拓扑。
   * **重新平衡** - 调整拓扑的并行度。 更改群集中的节点数目之后，你应该重新平衡正在运行的拓扑。 这可让拓扑调整并行度，以弥补群集中增加/减少的节点数目。 有关详细信息，请参阅 [了解 Storm 拓扑的并行度](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html)。
   * **终止** - 在经过指定的超时之后终止 Storm 拓扑。
3. 在此页中，从“Spout”或“Bolt”部分中选择一个条目。 这将显示有关选定组件的信息。
   
    ![包含有关选定组件的信息的 Storm 仪表板。](./media/hdinsight-apache-storm-tutorial-get-started/component-summary.png)
   
    此页显示以下信息：
   
   * **Spout/Bolt 统计信息** - 有关组件性能的基本信息，已组织到时间窗口中。
     
     > [!NOTE]
     > 选择特定的时间窗口会更改页面其他部分中显示的信息的时间窗口。
     > 
     > 
   * **输入统计信息** （仅限 Bolt）- 有关生成 Bolt 所用数据的组件的信息。
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
   
    从这些数据中，可看到，单词 **seven** 出现了 1,493,957 次。 就是自从启动此拓扑以来该单词出现的次数。

### <a name="stop-the-topology"></a>停止拓扑
返回到单词计数拓扑的“拓扑摘要”页，然后从“拓扑操作”部分中选择“终止”。 出现提示时，输入停止拓扑之前要等待的秒数，即 10。 在超时期限之后访问仪表板的“Storm UI”部分，将不再显示该拓扑。

## <a name="delete-the-cluster"></a>删除群集
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="summary"></a>摘要
在本 Apache Storm 教程中，你已使用 Storm 初学者示例学习如何创建 Storm on HDInsight 群集，以及如何使用 Storm 仪表板来部署、监视和管理 Storm 拓扑。

## <a name="a-idnextanext-steps"></a><a id="next"></a>后续步骤
* **Visual Studio 的 HDInsight 工具** - HDInsight 工具允许你使用 Visual Studio 提交、监视和管理 Storm 拓扑，就像在前面提到的 Storm 仪表板中一样。 HDInsight Tools 可让你创建 C# Storm 拓扑，并包含可在群集上部署和运行的示例拓扑。
  
    有关详细信息，请参阅 [用于 Visual Studio 的 HDInsight 工具入门](hdinsight-hadoop-visual-studio-tools-get-started.md)。
* **示例文件** - HDInsight Storm 群集在 **%STORM_HOME%\contrib** 目录中提供了多个示例。 每个示例都会包含以下内容：
  
  * 源代码 - 例如，storm-starter-0.9.1.2.1.5.0-2057-sources.jar
  * Java 文档 - 例如，storm-starter-0.9.1.2.1.5.0-2057-javadoc.jar
  * 示例 - 例如，storm-starter-0.9.1.2.1.5.0-2057-jar-with-dependencies.jar
    
    可以使用“jar”命令提取源代码或 Java 文档。 例如，“jar -xvf storm-starter-0.9.1.2.1.5.0.2057-javadoc.jar”。
    
    > [!NOTE]
    > Java 文档包含网页。 提取后，可使用浏览器来查看 **index.html** 文件。
    > 
    > 
    
    若要访问这些示例，必须为 Storm on HDInsight 群集启用远程桌面，然后复制 **%STORM_HOME%\contrib** 中的文件。
* 以下文档包含可与 Storm on HDInsight 配合使用的其他示例的列表：
  
  * [Storm on HDInsight 的示例拓扑](hdinsight-storm-example-topology.md)

[apachestorm]: https://storm.incubator.apache.org
[stormdocs]: http://storm.incubator.apache.org/documentation/Documentation.html
[stormstarter]: https://github.com/apache/storm/tree/master/examples/storm-starter
[stormjavadocs]: https://storm.incubator.apache.org/apidocs/
[azureportal]: https://manage.windowsazure.com/
[hdinsight-provision]: hdinsight-provision-clusters.md
[preview-portal]: https://portal.azure.com/



<!--HONumber=Jan17_HO3-->


