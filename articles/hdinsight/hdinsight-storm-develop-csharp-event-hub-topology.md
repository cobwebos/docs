---
title: "使用 Storm on HDInsight 从事件中心处理事件 | Microsoft Docs"
description: "了解如何使用在 Visual Studio 中通过 HDInsight Tools for Visual Studio 创建的 C# Storm 拓扑处理事件中心数据。"
services: hdinsight,notification hubs
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 67f9d08c-eea0-401b-952b-db765655dad0
ms.service: hdinsight
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/27/2016
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 77d0dfe7e09baab9b923b3c49af0cb7c28cd8625


---
# <a name="process-events-from-azure-event-hubs-with-storm-on-hdinsight-c"></a>使用 Storm on HDInsight 从 Azure 事件中心处理事件 (C#)
Azure 事件中心可让你处理网站、应用程序和设备中的大量数据。 借助事件中心 Spout，你可以轻松使用 Apache Storm on HDInsight 实时分析这些数据。 你还可以使用事件中心 Bolt 从 Storm 向事件中心写入数据。

在本教程中，你将学习如何使用连同 HDInsight Tools for Visual Studio 一起安装的 Visual Studio 模板，来创建两个可以配合 Azure 事件中心运行的拓扑。

* **EventHubWriter**：随机生成数据，并将其写入事件中心
* **EventHubReader**：从事件中心读取数据并将数据记录到 Storm 日志

> [!NOTE]
> 虽然本文档中的步骤依赖于具有 Visual Studio 的 Windows 开发环境，但编译后的项目可以提交到基于 Linux 或基于 Windows 的 HDInsight 群集。 仅创建于 2016/10/28 之后的基于 Linux 的群集支持 SCP.NET 拓扑。
> 
> 若要将 C# 拓扑与基于 Linux 的群集结合使用，则必须将项目使用的 Microsoft.SCP.Net.SDK NuGet 包更新到版本 0.10.0.6 或更高版本。 包的版本还必须与安装在 HDInsight 上的 Storm 主版本相匹配。 例如，Storm on HDInsight 版本 3.3 和 3.4 使用 Storm 版本 0.10.x，而 HDInsight 3.5 使用 Storm 1.0.x。
> 
> 基于 Linux 的群集上的 C# 拓扑必须使用 .NET 4.5，并使用 Mono 在 HDInsight 群集上运行。 大多数情况下会正常工作，但还是应查看[Mono 兼容性](http://www.mono-project.com/docs/about-mono/compatibility/)文档了解可能的不兼容性。
> 
> 对于此项目的 Java 版本（此版本还适用于基于 Linux 或基于 Windows 的群集），请参阅[使用 Storm on HDInsight 从 Azure 事件中心处理事件 (Java)](hdinsight-storm-develop-java-event-hub-topology.md)。
> 
> 

## <a name="prerequisites"></a>先决条件
* 一个 [Apache Storm on HDInsight 群集](hdinsight-apache-storm-tutorial-get-started.md)
* 一个 [Azure 事件中心](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
* [Azure .NET SDK](http://azure.microsoft.com/downloads/)
* [Visual Studio 的 HDInsight 工具](hdinsight-hadoop-visual-studio-tools-get-started.md)

## <a name="completed-project"></a>已完成的项目
可以从 GitHub 下载本教程中所创建的项目的完整版本：[eventhub-storm-hybrid](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub)。 不过，你仍然必须根据本教程中的步骤提供配置设置。

## <a name="event-hubs-spout-and-bolt"></a>事件中心 Spout 和 Bolt
事件中心 Spout 和 Bolt 是可让你轻松从 Apache Storm 使用事件中心的 Java 组件。 虽然这些组件是用 Java 语言编写的，但 HDInsight Tools for Visual Studio 允许你创建混用 C# 和 Java 组件的混合拓扑。

以名为 **eventhubs-storm-spout-#.#-jar-with-dependencies.jar** 的单个 Java 存档 (.jar) 文件的形式分发 Spout 和 Bolt，其中 #.# 为文件的版本。

### <a name="download-the-jar-file"></a>下载 .jar 文件
[HDInsight Storm 示例](https://github.com/hdinsight/hdinsight-storm-examples)项目中的 **lib/eventhubs** 文件夹下包含了最新版本的 jar 文件。 若要下载该文件，请使用以下方法之一。

> [!NOTE]
> 已提交 Spout 和 Bolt 以包含在 Apache Storm 项目中。 有关详细信息，请参阅 GitHub 中的 [STORM-583：Storm 事件中心的初始签入](https://github.com/apache/storm/pull/336/files)。
> 
> 

* **下载 ZIP 文件**：在 [HDInsight Storm 示例](https://github.com/hdinsight/hdinsight-storm-examples)站点中，选择右窗格中的“下载 ZIP”来下载包含项目的 .zip 文件。
  
    ![下载 zip 按钮](./media/hdinsight-storm-develop-csharp-event-hub-topology/download.png)
  
    下载文件后，可以解压缩存档，该文件将位于 **lib** 目录中。
* **克隆项目**：如果已安装 [Git](http://git-scm.com/)，请使用以下命令在本地克隆存储库，然后在 **lib** 目录中查找文件。
  
        git clone https://github.com/hdinsight/hdinsight-storm-examples

## <a name="configure-event-hubs"></a>配置事件中心
事件中心是此示例的数据源。 使用[事件中心入门](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)文档中**创建事件中心**部分的信息。

1. 创建事件中心后，在 Azure 门户中查看 EventHub 边栏选项卡，选择“共享访问策略”。 使用“+ 添加”项添加以下策略：
   
   | Name | 权限 |
   | --- | --- |
   | writer |发送 |
   | reader |侦听 |
   
    ![策略](./media/hdinsight-storm-develop-csharp-event-hub-topology/sas.png)
2. 选择“读取器”和“写入器”策略。 复制并保存两个策略的**主密钥**值，因为稍后将使用它们。

## <a name="configure-the-eventhubwriter"></a>配置 EventHubWriter
1. 如果你尚未安装最新版本的 HDInsight Tools for Visual Studio，请参阅[开始使用 HDInsight Tools for Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md)。
2. 从 [eventhub-storm-hybrid](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub) 下载解决方案。 打开解决方案，花一些时间仔细查看 **EventHubWriter** 项目的代码。
3. 在 **EventHubWriter** 项目中，打开 **App.config** 文件。 使用之前配置的事件中心中的信息填写以下键的值：
   
   | 键 | 值 |
   | --- | --- |
   | EventHubPolicyName |写入器（如果对具有“发送”权限的策略使用不同名称，则改用它。） |
   | EventHubPolicyKey |写入器策略的密钥 |
   | EventHubNamespace |包含事件中心的命名空间 |
   | EventHubName |事件中心名称 |
   | EventHubPartitionCount |事件中心内的分区数 |
4. 保存并关闭 **App.config** 文件。

## <a name="configure-the-eventhubreader"></a>配置 EventHubReader
1. 打开 **EventHubReader** 项目并花一些时间仔细查看代码。
2. 打开 **EventHubWriter** 的 **App.config**。 使用之前配置的事件中心中的信息填写以下键的值：
   
   | 键 | 值 |
   | --- | --- |
   | EventHubPolicyName |读取器（如果对具有“侦听”权限的策略使用不同名称，则改用它。） |
   | EventHubPolicyKey |读取器策略的密钥 |
   | EventHubNamespace |包含事件中心的命名空间 |
   | EventHubName |事件中心名称 |
   | EventHubPartitionCount |事件中心内的分区数 |
3. 保存并关闭 **App.config** 文件。

## <a name="deploy-the-topologies"></a>部署拓扑
1. 在“解决方案资源管理器”中，右键单击 **EventHubReader** 项目，然后选择“提交到 Storm on HDInsight”。
   
    ![提交到 Storm](./media/hdinsight-storm-develop-csharp-event-hub-topology/submittostorm.png)
2. 在“提交拓扑”屏幕上，选择“Storm 群集”。 展开“其他配置”，选择“Java 文件路径”，接着选择“...”，然后选择前面下载的 **eventhubs-storm-spout-0.9-jar-with-dependencies.jar** 文件所在的目录。 最后，单击“提交”。
   
    ![提交对话框的图像](./media/hdinsight-storm-develop-csharp-event-hub-topology/submit.png)
3. 提交拓扑之后，将会出现“Storm 拓扑查看器”。 在左窗格中选择“EventHubReader”拓扑，以查看该拓扑的统计信息。 当前应该不会发生任何情况，因为尚未将任何事件写入事件中心。
   
    ![示例存储视图](./media/hdinsight-storm-develop-csharp-event-hub-topology/topologyviewer.png)
4. 在“解决方案资源管理器”中，右键单击 **EventHubWriter** 项目，然后选择“提交到 Storm on HDInsight”。
5. 在“提交拓扑”屏幕上，选择“Storm 群集”。 展开“其他配置”，选择“Java 文件路径”，接着选择“...”，然后选择前面下载的 **eventhubs-storm-spout-0.9-jar-with-dependencies.jar** 文件所在的目录。 最后，单击“提交”。
6. 提交拓扑之后，在“Storm 拓扑查看器”中刷新拓扑列表以验证这两个拓扑是否正在群集上运行。
7. 在“Storm 拓扑查看器”中，选择 **EventHubReader** 拓扑。
8. 在“图形”视图中，双击 **LogBolt** 组件。 这将打开 Bolt 的“组件摘要”页面。
9. 在“执行器”部分，选择“端口”列中的链接之一。 这将显示由组件记录的信息。 记录的信息类似于以下内容：
   
        2016-10-20 13:26:44.186 m.s.s.b.ScpNetBolt [INFO] Processing tuple: source: com.microsoft.eventhubs.spout.EventHubSpout:7, stream: default, id: {5769732396213255808=520853934697489134}, [{"deviceId":3,"deviceValue":1379915540}]
        2016-10-20 13:26:44.234 m.s.s.b.ScpNetBolt [INFO] Processing tuple: source: com.microsoft.eventhubs.spout.EventHubSpout:7, stream: default, id: {7154038361491319965=4543766486572976404}, [{"deviceId":3,"deviceValue":459399321}]
        2016-10-20 13:26:44.335 m.s.s.b.ScpNetBolt [INFO] Processing tuple: source: com.microsoft.eventhubs.spout.EventHubSpout:6, stream: default, id: {513308780877039680=-7571211415704099042}, [{"deviceId":5,"deviceValue":845561159}]
        2016-10-20 13:26:44.445 m.s.s.b.ScpNetBolt [INFO] Processing tuple: source: com.microsoft.eventhubs.spout.EventHubSpout:7, stream: default, id: {-2409895457033895206=5479027861202203517}, [{"deviceId":8,"deviceValue":2105860655}]

## <a name="stop-the-topologies"></a>停止拓扑
若要停止拓扑，请在“Storm 拓扑查看器”中选择每个拓扑，然后单击“终止”。

![终止拓扑的图像](./media/hdinsight-storm-develop-csharp-event-hub-topology/killtopology.png)

## <a name="delete-your-cluster"></a>删除群集
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="notes"></a>说明
### <a name="checkpointing"></a>检查点
EventHubSpout 定期检查点其状态为 Zookeeper 节点，将保存当前的偏移量的消息从队列中读取。 这样，要开始在以下情况下接收已保存的偏移量处的消息的组件：

* 组件实例失败，并已重新启动。
* 通过添加或删除节点扩大或收缩群集。
* 拓扑已终止并已**使用相同的名称**重新启动。

你还可以将持久性检查点导入和导出到 WASB（HDInsight 群集使用的 Azure 存储。）用于执行此操作的脚本位于 Storm on HDInsight 群集上的 **c:\apps\dist\storm-0.9.3.2.2.1.0-2340\zkdatatool-1.0\bin** 中。

> [!NOTE]
> 路径中的版本号可能不同，因为群集上安装的 Storm 版本将来可能会更改。
> 
> 

此目录中的脚本是：

* **stormmeta_import.cmd**：将所有 Storm 元数据从群集默认存储容器导入 Zookeeper。
* **stormmeta_export.cmd**：将所有 Storm 元数据从 Zookeeper 导出到群集默认存储容器。
* **stormmeta_delete.cmd**：从 Zookeeper 中删除所有 Storm 元数据。

当你需要删除群集，但在将新群集重新联机的情况下想要从中心的当前偏移量恢复处理时，可以使用导出和导入来保存检查点数据。

> [!NOTE]
> 由于数据将保存到默认的存储容器，因此新群集**必须**使用前一群集所用的同一个存储帐户和容器。
> 
> 

## <a name="next-steps"></a>后续步骤
在本文档中，你已学习如何使用 C# 拓扑中的 Java 事件中心 Spout 和 Bolt 处理 Azure 事件中心内的数据。 若要了解有关创建 C# 拓扑的详细信息，请参阅以下主题。

* [使用 Visual Studio 开发 Apache Storm on HDInsight 的 C# 拓扑](hdinsight-storm-develop-csharp-visual-studio-topology.md)
* [SCP 编程指南](hdinsight-storm-scp-programming-guide.md)
* [Storm on HDInsight 的示例拓扑](hdinsight-storm-example-topology.md)




<!--HONumber=Nov16_HO3-->


