---
title: "使用 HDInsight 上的 Storm 和 HBase 按时间对事件进行关联"
description: "了解如何使用 HDInsight 上的 Storm 和 HBase 将不同时间到达的事件关联起来。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 17d11479-2d02-4790-8d29-05fb38351479
ms.service: hdinsight
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/07/2017
ms.author: larryfr
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: 06630096383601e48e8f69f8553314cee42f5f3e
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2017
---
# <a name="correlate-events-that-arrive-at-different-times-using-storm-and-hbase"></a>使用 Storm 和 HBase 关联在不同时间到达的事件

通过使用 Apache Storm 的持久数据存储，可以将不同时间到达的数据条目关联起来。 例如，将用户会话的登录事件和注销事件关联起来即可计算该会话的持续时间。

本文档介绍如何创建基本的 C# Storm 拓扑，该拓扑用于跟踪用户会话的登录事件和注销事件并计算会话的持续时间。 拓扑使用 HBase 作为永久性数据存储。 HBase 还允许对历史数据执行批查询以生成其他见解。 例如，在特定时间段内已启动或已结束多少用户会话。

## <a name="prerequisites"></a>先决条件

* Visual Studio 和用于 Visual Studio 的 HDInsight 工具。 有关详细信息，请参阅[用于 Visual Studio 的 HDInsight 工具入门](hdinsight-hadoop-visual-studio-tools-get-started.md)。

* Apache Storm on HDInsight 群集（基于 Windows）。

  > [!WARNING]
  > 尽管在 2016/10/28 之后创建的基于 Linux 的 Storm 群集支持 SCP.NET 拓扑，但是在 2016/10/28 之后可用的 HBase SDK for .NET 包在基于 Linux 的 HDInsight 上无法正常工作

* HDInsight 群集上的 Apache HBase（基于 Linux 或 Windows）。

  > [!IMPORTANT]
  > Linux 是 HDInsight 3.4 或更高版本上使用的唯一操作系统。 有关详细信息，请参阅 [HDInsight 在 Windows 上停用](hdinsight-component-versioning.md#hdinsight-windows-retirement)。

* 开发环境中采用 [Java](https://java.com) 1.7 或更高版本。 将拓扑提交到 HDInsight 群集时，Java 用于打包拓扑。

  * **JAVA_HOME** 环境变量必须指向包含 Java 的目录。
  * 路径中必须包含 **%JAVA_HOME%/bin** 目录

## <a name="architecture"></a>体系结构

![拓扑中的数据流程图](./media/hdinsight-storm-correlation-topology/correlationtopology.png)

对事件进行关联时，需要一个事件源的通用标识符。 例如，用户 ID、会话 ID 或其他数据片段，其特点是：a) 唯一；b) 包括在发送到 Storm 的所有数据中。 此示例使用 GUID 值来表示会话 ID。

此示例包含两个 HDInsight 群集：

* HBase：历史数据的永久数据存储
* Storm：用于摄取传入数据

数据通过 Storm 拓扑随机生成，包含以下项：

* 会话 ID：一个 GUID，可唯一地标识每个会话
* 事件：开始或结束事件。 就此示例来说，开始事件始终发生在结束事件之前
* 时间：事件的时间。

此数据在 HBase 中处理和存储。

### <a name="storm-topology"></a>Storm 拓扑

会话启动时，**开始**事件由拓扑接收，并记录到 HBase。 在收到**结束**事件时，拓扑会检索**开始**事件并计算两个事件之间的时间。 然后会将此持续时间值存储到 HBase 中，同时存储的还有结束事件信息。

> [!IMPORTANT]
> 虽然此拓扑演示了基本的模式，但生产型解决方案需要针对以下情况进行设计：
>
> * 事件在到达时混乱无序
> * 重复的事件
> * 删除的事件

示例拓扑由以下组件组成：

* Session.cs：通过创建随机会话 ID、开始时间和会话持续时间来模拟用户会话。

* Spout.cs：创建 100 个会话，发出一个开始事件，等待每个会话随机超时，并发出一个结束事件。 然后回收结束的会话，以便生成新的会话。

* HBaseLookupBolt.cs：使用的会话 ID 来查找 HBase 中的会话信息。 处理结束事件时，它会查找相应的开始事件，并计算会话的持续时间。

* HBaseBolt.cs：将信息存储到 HBase。

* TypeHelper.cs：通过 HBase 执行读/写操作时，帮助进行类型转换。

### <a name="hbase-schema"></a>HBase 架构

在 HBase 中，数据存储在具有以下架构/设置的表中：

* 行键：会话 ID 用作此表中行的键。

* 列系列：系列名称为“cf”。 存储在此系列中的列包括：

  * 事件：开始或结束。

  * 时间：事件发生的时间（以毫秒为单位）。

  * 持续时间：开始事件和结束事件之间的时长。

* 版本：“cf”系列设置为每行保留 5 个版本。

  > [!NOTE]
  > 可以使用版本来记录以前为特定行键存储的值。 默认情况下，HBase 只返回行的最新版本的值。 在这种情况下，同一行将用于所有事件（开始、结束）。每个版本的行通过时间戳值来标识。 使用版本可通过历史视图来查看针对特定 ID 记录的事件。

## <a name="download-the-project"></a>下载项目

可以从 [https://github.com/Azure-Samples/hdinsight-storm-dotnet-event-correlation](https://github.com/Azure-Samples/hdinsight-storm-dotnet-event-correlation) 下载示例项目。

此下载包含以下 C# 项目：

* CorrelationTopology：C# Storm 拓扑，可针对用户会话随机发出开始事件和结束事件。 每个会话的持续时间为 1 到 5 分钟。

* SessionInfo：C# 控制台应用程序，用于创建 HBase 表，以及提供可返回已存储会话数据相关信息的示例查询。

## <a name="create-the-table"></a>创建表

1. 打开 Visual Studio 中的 **SessionInfo** 项目。

2. 在“解决方案资源管理器”中，右键单击 **SessionInfo** 项目，并选择“属性”。

    ![属性已选定的菜单的屏幕快照](./media/hdinsight-storm-correlation-topology/selectproperties.png)

3. 选择“设置”，并设置以下值：

   * HBaseClusterURL：到 HBase 群集的 URL。 例如，https://myhbasecluster.azurehdinsight.net。

   * HBaseClusterUserName：群集的管理员/HTTP 用户帐户

   * HBaseClusterPassword：管理员/HTTP 用户帐户的密码

   * HBaseTableName：用于此示例的表的名称

   * HBaseTableColumnFamily：列系列名称

     ![设置对话框的图像](./media/hdinsight-storm-correlation-topology/settings.png)

4. 运行解决方案。 出现提示时，选择可在 HBase 群集上创建表的“c”键。

## <a name="build-and-deploy-the-storm-topology"></a>生成和部署 Storm 拓扑

1. 在 Visual Studio 中打开 **CorrelationTopology** 解决方案。

2. 在“解决方案资源管理器”中，右键单击 CorrelationTopology 项目，然后选择属性。

3. 在属性窗口中，选择“设置”，然后输入此项目的配置值。 前 5 个属性的值与 **SessionInfo** 项目所使用的值相同：

   * HBaseClusterURL：到 HBase 群集的 URL。 例如，https://myhbasecluster.azurehdinsight.net。

   * HBaseClusterUserName：群集的管理员/HTTP 用户帐户。

   * HBaseClusterPassword：管理员/HTTP 用户帐户的密码。

   * HBaseTableName：用于此示例的表的名称。 此值是与 SessionInfo 项目中使用的相同的表名。

   * HBaseTableColumnFamily：列系列名称。 此值是与 SessionInfo 项目中使用的相同的列系列名称。

   > [!IMPORTANT]
   > 请勿更改 HBaseTableColumnNames，因为其默认值是 **SessionInfo** 用来检索数据的名称。

4. 保存属性，并生成项目。

5. 在“解决方案资源管理器”中，右键单击项目，然后选择“提交到 Storm on HDInsight”。 如果出现提示，请输入 Azure 订阅的凭据。

   ![提交到 storm 菜单项的图像](./media/hdinsight-storm-correlation-topology/submittostorm.png)

6. 在“提交拓扑”对话框中，选择要将此拓扑部署到的 Storm 群集。

   > [!NOTE]
   > 第一次提交拓扑时，可能需要几秒钟来检索 HDInsight 群集名称。

7. 将拓扑上传并提交到该群集后，“Storm 拓扑视图”将打开并显示正在运行的拓扑。 若要刷新数据，请选择“CorrelationTopology”，然后使用页面右上角的“刷新”按钮。

   ![拓扑视图的图像](./media/hdinsight-storm-correlation-topology/topologyview.png)

   拓扑开始生成数据时，“已发出”列的值将递增。

   > [!NOTE]
   > 如果“Storm 拓扑视图”不会自动打开，可使用以下步骤将其打开：
   >
   > 1. 在“解决方案资源管理器”中，展开“Azure”，然后展开“HDInsight”。
   > 2. 右键单击运行拓扑的 Storm 群集，然后选择“查看 Storm 拓扑”

## <a name="query-the-data"></a>查询数据

一旦已发出数据，则可使用以下步骤来查询数据。

1. 返回到 **SessionInfo** 项目。 如果该项目未运行，可启动一个新实例。

2. 出现提示时，选择 **s** 即可搜索开始事件。 系统会提示输入开始时间和结束时间，以便定义一个时间范围 - 将只返回这两个时间之间的事件。

    输入开始和结束时间时使用以下格式：HH:MM 以及“am”或“pm”。 例如，11:20pm。

    若要返回记录的事件，可使用部署 Storm 拓扑之前的某个时间作为开始时间，而使用现在的时间作为结束时间。 返回的数据包含类似于以下文本的条目：

        Session e6992b3e-79be-4991-afcf-5cb47dd1c81c started at 6/5/2015 6:10:15 PM. Timestamp = 1433527820737

搜索结束事件与搜索开始事件在原理上是相同的。 不过，结束事件是在开始事件之后 1 到 5 分钟随机生成的。 可能需要尝试数个时间范围才能找到结束事件。 结束事件还会包含会话持续时间 - 开始事件时间与结束事件时间之差。 下面是结束事件数据的一个示例：

    Session fc9fa8e6-6892-4073-93b3-a587040d892e lasted 2 minutes, and ended at 6/5/2015 6:12:15 PM

> [!NOTE]
> 虽然输入的时间值为本地时间，但从查询返回的时间采用 UTC 格式。

## <a name="stop-the-topology"></a>停止拓扑

准备停止拓扑时，请返回到 Visual Studio 中的 **CorrelationTopology** 项目。 在“Storm 拓扑视图”中，选择该拓扑，并使用拓扑视图顶部的“终止”按钮。

## <a name="delete-your-cluster"></a>删除群集

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>后续步骤

如需更多的 Storm 示例，请参阅 [Storm on HDInsight 的示例拓扑](hdinsight-storm-example-topology.md)。
