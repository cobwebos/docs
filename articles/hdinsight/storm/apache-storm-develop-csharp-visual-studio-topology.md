---
title: 使用 Visual Studio 和 C# 的 Apache Storm 拓扑 — Azure HDInsight | Microsoft Docs
description: 了解如何用 C# 创建 Storm 拓扑。 在 Visual Studio 中使用针对 Visual Studio 的 Hadoop 工具创建简单的字数统计拓扑。
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 380d804f-a8c5-4b20-9762-593ec4da5a0d
ms.service: hdinsight
ms.custom: ''
ms.devlang: java
ms.topic: conceptual
ms.date: 11/27/2017
ms.author: larryfr
ROBOTS: NOINDEX
ms.openlocfilehash: 1e35d795fb65c837f7a4152920f701da5bf8f506
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2018
---
# <a name="develop-c-topologies-for-apache-storm-by-using-the-data-lake-tools-for-visual-studio"></a>使用针对 Visual Studio 的 Data Lake 工具开发 Apache Storm 的 C# 拓扑

了解如何使用针对 Visual Studio 的 Azure Data Lake (Hadoop) 工具创建 C# Storm 拓扑。 本文档逐步说明在 Visual Studio 中创建 Storm 项目，在本地测试该项目，然后将它部署到Apache Storm on Azure HDInsight 群集的过程。

还可了解如何创建使用 C# 和 Java 组件的混合拓扑。

> [!NOTE]
> 虽然本文档中的步骤依赖于具有 Visual Studio 的 Windows 开发环境，但编译后的项目可以提交到基于 Linux 或基于 Windows 的 HDInsight 群集。 仅 2016 年 10 月 28 日之后创建的基于 Linux 的群集支持 SCP.NET 拓扑。

若要将 C# 拓扑与基于 Linux 的群集配合使用，必须将项目使用的 Microsoft.SCP.Net.SDK NuGet 包更新到版本 0.10.0.6 或更高版本。 包的版本还必须与安装在 HDInsight 上的 Storm 主版本相匹配。

| HDInsight 版本 | Storm 版本 | SCP.NET 版本 | 默认 Mono 版本 |
|:-----------------:|:-------------:|:---------------:|:--------------------:|
| 3.3 |0.10.x |0.10.x.x</br>（仅在基于 Windows 的 HDInsight 上） | 不可用 |
| 3.4 | 0.10.0.x | 0.10.0.x | 3.2.8 |
| 3.5 | 1.0.2.x | 1.0.0.x | 4.2.1 |
| 3.6 | 1.1.0.x | 1.0.0.x | 4.2.8 |

> [!IMPORTANT]
> 基于 Linux 的群集上的 C# 拓扑必须使用 .NET 4.5，并使用 Mono 在 HDInsight 群集上运行。 查看 [Mono 兼容性](http://www.mono-project.com/docs/about-mono/compatibility/)了解可能的不兼容问题。

## <a name="install-visual-studio"></a>安装 Visual Studio

通过安装以下 Visual Studio 版本之一，可以使用 SCP.NET 开发 C# 拓扑：

* Visual Studio 2012 [Update 4](http://www.microsoft.com/download/details.aspx?id=39305)

* Visual Studio 2013 [Update 4](http://www.microsoft.com/download/details.aspx?id=44921) 或 [Visual Studio 2013 Community](http://go.microsoft.com/fwlink/?LinkId=517284)

* Visual Studio 2015 或 [Visual Studio 2015 Community](https://go.microsoft.com/fwlink/?LinkId=532606)

* Visual Studio 2017（任何版本）

## <a name="install-data-lake-tools-for-visual-studio"></a>安装针对 Visual Studio 的 Data Lake 工具

若要安装针对 Visual Studio 的 Data Lake 工具，请按照[开始使用针对 Visual Studio 的 Data Lake 工具](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)中的步骤操作。

## <a name="install-java"></a>安装 Java

从 Visual Studio 提交 Storm 拓扑时，SCP.NET 生成一个包含拓扑和依赖关系的 zip 文件。 这些 zip 文件用 Java 创建，因为该语言使用的格式与基于 Linux 的群集更兼容。

1. 在开发环境中安装 Java 开发人员工具包 (JDK) 7 或更高版本。 可以从 [Oracle](http://www.oracle.com/technetwork/java/javase/downloads/index.html) 获取 Oracle JDK。 也可以使用[其他 Java 分发](http://openjdk.java.net/)。

2. `JAVA_HOME` 环境变量必须指向包含 Java 的目录。

3. `PATH` 环境变量必须包括 `%JAVA_HOME%\bin` 目录。

可以使用以下 C# 控制台应用程序验证是否正确安装了 Java 和 JDK：

```csharp
using System;
using System.IO;
namespace ConsoleApplication2
{
   class Program
   {
       static void Main(string[] args)
       {
           string javaHome = Environment.GetEnvironmentVariable(“JAVA_HOME”);
           if (!string.IsNullOrEmpty(javaHome))
           {
               string jarExe = Path.Combine(javaHome + @”\bin”, “jar.exe”);
               if (File.Exists(jarExe))
               {
                   Console.WriteLine(“JAVA Is Installed properly”);
                    return;
               }
               else
               {
                   Console.WriteLine(“A valid JAVA JDK is not found. Looks like JRE is installed instead of JDK.”);
               }
           }
           else
           {
             Console.WriteLine(“A valid JAVA JDK is not found. JAVA_HOME environment variable is not set.”);
           }
       }  
   }
}
```

## <a name="storm-templates"></a>Storm 模板

针对 Visual Studio 的 Data Lake 工具提供以下模板：

| 项目类型 | 演示 |
| --- | --- |
| Storm 应用程序 |空的 Storm 拓扑项目。 |
| Storm Azure SQL 写入器示例 |如何写入 Azure SQL 数据库。 |
| Storm Azure Cosmos DB 读取器示例 |如何从 Azure Cosmos DB 读取。 |
| Storm Azure Cosmos DB 写入器示例 |如何写入 Azure Cosmos DB。 |
| Storm EventHub 读取器示例 |如何从 Azure 事件中心读取。 |
| Storm EventHub 写入器示例 |如何写入 Azure 事件中心。 |
| Storm HBase 读取器示例 |如何从 HDInsight 群集上的 HBase 读取。 |
| Storm HBase 写入器示例 |如何写入 HDInsight 群集上的 HBase。 |
| Storm 混合示例 |如何使用 Java 组件。 |
| Storm 示例 |基本的字数统计拓扑。 |

> [!WARNING]
> 并非所有模板都可用于基于 Linux 的 HDInsight。 模板使用的 NuGet 程序包可能与 Mono 不兼容。 查看 [Mono 兼容性](http://www.mono-project.com/docs/about-mono/compatibility/)文档并使用 [.NET Portability Analyzer](../hdinsight-hadoop-migrate-dotnet-to-linux.md#automated-portability-analysis) 确定潜在问题。

在本文档的步骤中，使用基本 Storm 应用程序项目类型创建拓扑。

### <a name="hbase-templates-notes"></a>HBase 模板说明

HBase 读取器和写入器模板使用 HBase REST API（而不是 HBase Java API）与 HDInsight 群集上的 HBase 通信。

### <a name="eventhub-templates-notes"></a>EventHub 模板说明

> [!IMPORTANT]
> EventHub 读取器模板中包含的基于 Java 的 EventHub Spout 组件可能不适用于 Storm on HDInsight 3.5 版或更高版本。 [GitHub](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/HDI3.5/lib) 提供此组件的更新版本。

有关使用此组件并适用于 Storm on HDInsight 3.5 的示例拓扑，请参阅 [GitHub](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub)。

## <a name="create-a-c-topology"></a>创建 C# 拓扑

1. 打开 Visual Studio，选择“文件” > “新建”，然后选择“项目”。

2. 在“新建项目”窗口中，展开“已安装” > “模板”，然后选择“Azure Data Lake”。 从模板列表中，选择“Storm 应用程序”。 在屏幕底部，输入 **WordCount** 作为应用程序名称。

    ![“新建项目”窗口的屏幕截图](./media/apache-storm-develop-csharp-visual-studio-topology/new-project.png)

3. 创建项目后，应有以下文件：

   * **Program.cs**：此文件定义项目的拓扑。 默认情况下会创建包含一个 Spout 和一个 Bolt 的默认拓扑。

   * **Spout.cs**：发出随机数的示例 Spout。

   * **Bolt.cs**：保留 Spout 所发出数字计数的示例 Bolt。

     创建项目时，NuGet 会下载最新的 [SCP.NET 包](https://www.nuget.org/packages/Microsoft.SCP.Net.SDK/)。

     [!INCLUDE [scp.net version important](../../../includes/hdinsight-storm-scpdotnet-version.md)]

### <a name="implement-the-spout"></a>实现 Spout

1. 打开 **Spout.cs**。 Spout 用于将外部源中的数据读入拓扑。 Spout 的主要组件如下：

   * **NextTuple**：允许 Spout 发出新的元组时由 Storm 调用。

   * **Ack**（仅限事务拓扑）：针对从此 Spout 发送的元组，处理拓扑中其他组件所发起的确认。 确认元组可让 Spout 知了解下游组件已成功处理元组。

   * **失败**（仅限事务拓扑）：处理无法处理拓扑中其他组件的元组。 实现失败方法可以重新发出元组，以便可以再次处理。

2. 将 **Spout** 类的内容替换为以下文本：此 Spout 将句子随机发送到拓扑。

    ```csharp
    private Context ctx;
    private Random r = new Random();
    string[] sentences = new string[] {
        "the cow jumped over the moon",
        "an apple a day keeps the doctor away",
        "four score and seven years ago",
        "snow white and the seven dwarfs",
        "i am at two with nature"
    };

    public Spout(Context ctx)
    {
        // Set the instance context
        this.ctx = ctx;

        Context.Logger.Info("Generator constructor called");

        // Declare Output schema
        Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
        // The schema for the default output stream is
        // a tuple that contains a string field
        outputSchema.Add("default", new List<Type>() { typeof(string) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(null, outputSchema));
    }

    // Get an instance of the spout
    public static Spout Get(Context ctx, Dictionary<string, Object> parms)
    {
        return new Spout(ctx);
    }

    public void NextTuple(Dictionary<string, Object> parms)
    {
        Context.Logger.Info("NextTuple enter");
        // The sentence to be emitted
        string sentence;

        // Get a random sentence
        sentence = sentences[r.Next(0, sentences.Length - 1)];
        Context.Logger.Info("Emit: {0}", sentence);
        // Emit it
        this.ctx.Emit(new Values(sentence));

        Context.Logger.Info("NextTuple exit");
    }

    public void Ack(long seqId, Dictionary<string, Object> parms)
    {
        // Only used for transactional topologies
    }

    public void Fail(long seqId, Dictionary<string, Object> parms)
    {
        // Only used for transactional topologies
    }
    ```

### <a name="implement-the-bolts"></a>实现 Bolt

1. 删除项目中的现有 **Bolt.cs** 文件。

2. 在“解决方案资源管理器”中，右键单击项目，然后选择“添加” > “新建项”。 从列表中选择“Storm Bolt”，并输入 **Splitter.cs** 作为名称。 重复此过程，以创建名为 **Counter.cs** 的另一个 Bolt。

   * **Splitter.cs**：实现 Bolt，以将句子分割成不同的单词并发出一串新单词。

   * **Counter.cs**：实现 Bolt，以统计每个单词的数目，并发出一串新单词和每个单词的计数。

     > [!NOTE]
     > 这些 Bolt 读取和写入流，但是你也可以使用 Bolt 来与数据库或服务等源进行通信。

3. 打开 **Splitter.cs**。 默认情况下它只包含一个方法：**Execute**。 在 Bolt 收到要处理的元组时会调用 Execute 方法。 此时，可读取和处理传入元组，以及发出传出元组。

4. 将 **Splitter** 类的内容替换为以下代码：

    ```csharp
    private Context ctx;

    // Constructor
    public Splitter(Context ctx)
    {
        Context.Logger.Info("Splitter constructor called");
        this.ctx = ctx;

        // Declare Input and Output schemas
        Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
        // Input contains a tuple with a string field (the sentence)
        inputSchema.Add("default", new List<Type>() { typeof(string) });
        Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
        // Outbound contains a tuple with a string field (the word)
        outputSchema.Add("default", new List<Type>() { typeof(string) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, outputSchema));
    }

    // Get a new instance of the bolt
    public static Splitter Get(Context ctx, Dictionary<string, Object> parms)
    {
        return new Splitter(ctx);
    }

    // Called when a new tuple is available
    public void Execute(SCPTuple tuple)
    {
        Context.Logger.Info("Execute enter");

        // Get the sentence from the tuple
        string sentence = tuple.GetString(0);
        // Split at space characters
        foreach (string word in sentence.Split(' '))
        {
            Context.Logger.Info("Emit: {0}", word);
            //Emit each word
            this.ctx.Emit(new Values(word));
        }

        Context.Logger.Info("Execute exit");
    }
    ```

5. 打开 **Counter.cs** 并将类内容替换为以下代码：

    ```csharp
    private Context ctx;

    // Dictionary for holding words and counts
    private Dictionary<string, int> counts = new Dictionary<string, int>();

    // Constructor
    public Counter(Context ctx)
    {
        Context.Logger.Info("Counter constructor called");
        // Set instance context
        this.ctx = ctx;

        // Declare Input and Output schemas
        Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
        // A tuple containing a string field - the word
        inputSchema.Add("default", new List<Type>() { typeof(string) });

        Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
        // A tuple containing a string and integer field - the word and the word count
        outputSchema.Add("default", new List<Type>() { typeof(string), typeof(int) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, outputSchema));
    }

    // Get a new instance
    public static Counter Get(Context ctx, Dictionary<string, Object> parms)
    {
        return new Counter(ctx);
    }

    // Called when a new tuple is available
    public void Execute(SCPTuple tuple)
    {
        Context.Logger.Info("Execute enter");

        // Get the word from the tuple
        string word = tuple.GetString(0);
        // Do we already have an entry for the word in the dictionary?
        // If no, create one with a count of 0
        int count = counts.ContainsKey(word) ? counts[word] : 0;
        // Increment the count
        count++;
        // Update the count in the dictionary
        counts[word] = count;

        Context.Logger.Info("Emit: {0}, count: {1}", word, count);
        // Emit the word and count information
        this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new List<SCPTuple> { tuple }, new Values(word, count));
        Context.Logger.Info("Execute exit");
    }
    ```

### <a name="define-the-topology"></a>定义拓扑

Spout 和 Bolt 以图形方式排列，用于定义数据在组件之间的流动方式。 此拓扑的图形如下：

![组件的排列方式关系图](./media/apache-storm-develop-csharp-visual-studio-topology/wordcount-topology.png)

句子从 Spout 发出，分布到 Splitter Bolt 的实例。 Splitter Bolt 将句子分割成多个单词，并将这些单词分布到 Counter Bolt。

因为字数会本地保留在 Counter 实例中，所以想要确保特定单词流向相同的 Counter Bolt 实例。 每个实例均跟踪特定单词。 由于 Splitter Bolt 不维护任何状态，因此哪个拆分器实例收到哪个句子并不重要。

打开 **Program.cs**。 重要的方法是 **GetTopologyBuilder**，它用于定义提交到 Storm 的拓扑。 将 **GetTopologyBuilder** 的内容替换为以下代码，以实现上面所述的拓扑：

```csharp
// Create a new topology named 'WordCount'
TopologyBuilder topologyBuilder = new TopologyBuilder("WordCount" + DateTime.Now.ToString("yyyyMMddHHmmss"));

// Add the spout to the topology.
// Name the component 'sentences'
// Name the field that is emitted as 'sentence'
topologyBuilder.SetSpout(
    "sentences",
    Spout.Get,
    new Dictionary<string, List<string>>()
    {
        {Constants.DEFAULT_STREAM_ID, new List<string>(){"sentence"}}
    },
    1);
// Add the splitter bolt to the topology.
// Name the component 'splitter'
// Name the field that is emitted 'word'
// Use suffleGrouping to distribute incoming tuples
//   from the 'sentences' spout across instances
//   of the splitter
topologyBuilder.SetBolt(
    "splitter",
    Splitter.Get,
    new Dictionary<string, List<string>>()
    {
        {Constants.DEFAULT_STREAM_ID, new List<string>(){"word"}}
    },
    1).shuffleGrouping("sentences");

// Add the counter bolt to the topology.
// Name the component 'counter'
// Name the fields that are emitted 'word' and 'count'
// Use fieldsGrouping to ensure that tuples are routed
//   to counter instances based on the contents of field
//   position 0 (the word). This could also have been
//   List<string>(){"word"}.
//   This ensures that the word 'jumped', for example, will always
//   go to the same instance
topologyBuilder.SetBolt(
    "counter",
    Counter.Get,
    new Dictionary<string, List<string>>()
    {
        {Constants.DEFAULT_STREAM_ID, new List<string>(){"word", "count"}}
    },
    1).fieldsGrouping("splitter", new List<int>() { 0 });

// Add topology config
topologyBuilder.SetTopologyConfig(new Dictionary<string, string>()
{
    {"topology.kryo.register","[\"[B\"]"}
});

return topologyBuilder;
```

## <a name="submit-the-topology"></a>提交拓扑

1. 在“解决方案资源管理器”中，右键单击项目，并选择“提交到 Storm on HDInsight”。

   > [!NOTE]
   > 如果出现提示，请输入 Azure 订阅的凭据。 如果有多个订阅，请登录包含 Storm on HDInsight 群集的订阅。

2. 从“Storm 群集”下拉列表中选择 Storm on HDInsight 群集，并选择“提交”。 可使用“输出”窗口监视提交是否成功。

3. 成功提交拓扑之后，应该会出现群集的“Storm 拓扑”。 从列表中选择“WordCount”拓扑，以查看有关正在运行的拓扑的信息。

   > [!NOTE]
   > 也可以从“服务器资源管理器”查看“Storm 拓扑”。 展开“Azure” > “HDInsight”，右键单击 Storm on HDInsight 群集，然后选择“查看 Storm 拓扑”。

    若要在拓扑中查看有关组件的信息，请双击关系图中的组件。

4. 从“拓扑摘要”视图中，单击“终止”以停止拓扑。

   > [!NOTE]
   > Storm 拓扑会一直运行，直到它被停用，或者群集被删除。

## <a name="transactional-topology"></a>事务拓扑

前面的拓扑是非事务性的 拓扑中的组件不会实现重播消息的功能。 对于事务拓扑示例，请创建项目，然后选择“Storm 示例”作为项目类型。

事务拓扑会实现以下项来支持重播数据：

* **元数据缓存**：Spout 必须存储所发出数据的相关元数据，只有这样，才能在失败时重新检索和发出数据。 此示例所发出的数据较少，因此将每个元组的原始数据存储在字典中以便重播。

* **确认**：拓扑中的每个 Bolt 都可以调用 `this.ctx.Ack(tuple)` 来确认它已成功处理元组。 所有 Bolt 都已确认 Tuple 之后，即会调用 Spout 的 `Ack` 方法。 `Ack` 方法可让 Spout 删除为重播缓存的数据。

* **失败**：每个 Bolt 都可以调用 `this.ctx.Fail(tuple)`，指出元组的处理失败。 这项失败会传播到 Spout 的 `Fail` 方法，在其中，可以使用缓存的元数据来重放 Tuple。

* **序列 ID**：发出元组时，可以指定唯一序列 ID。 此值标识要进行重播（确认和失败）处理的元组。 例如，发出数据时，**Storm 示例项目**中的 Spout 会使用以下项：

        this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new Values(sentence), lastSeqId);

    此代码会发出包含默认数据流的句子的元组，以及 **lastSeqId** 中所含的序列 ID 值。 在此示例中，**lastSeqId** 针对发出的每个元组递增。

如 **Storm 示例**项目中所示，在运行时，可以根据配置来设置组件是否为事务性。

## <a name="hybrid-topology-with-c-and-java"></a>使用 C# 和 Java 的混合拓扑

还可以使用针对 Visual Studio 的 Data Lake 工具创建混合拓扑，其中某些组件用 C# 编写，其他用 Java 编写。

对于混合拓扑示例，请创建项目，然后选择“Storm 混合示例”。 此示例类型演示以下概念：

* **Java Spout** 和 **C# Bolt**：在 **HybridTopology_javaSpout_csharpBolt** 中定义。

    * 事务版本在 **HybridTopologyTx_javaSpout_csharpBolt** 中定义。

* **C# Spout** 和 **Java Bolt**：在 **HybridTopology_csharpSpout_javaBolt** 中定义。

    * 事务版本在 **HybridTopologyTx_csharpSpout_javaBolt** 中定义。

  > [!NOTE]
  > 此版本还演示了如何使用文本文件中的 clojure 代码作为 Java 组件。


若要切换在提交项目时使用的拓扑，请将 `[Active(true)]` 语句移到要在提交给群集之前使用的拓扑。

> [!NOTE]
> 在 **JavaDependency** 文件夹中，所需的所有 Java 文件都会提供为此项目的一部分。

创建和提交混合拓扑时，需注意以下事项：

* 使用 **JavaComponentConstructor** 创建 Spout 或 Bolt 的 Java 类实例。

* 使用 **microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer** 将传入或传出 Java 组件的数据从 Java 对象序列化为 JSON。

* 将拓扑提交到服务器时，必须使用“其他配置”选项指定 **Java 文件路径**。 指定的路径应该是包含 JAR 文件的目录，而 JAR 文件包含 Java 类

### <a name="azure-event-hubs"></a>Azure 事件中心

SCP.NET 版本 0.9.4.203 引入了专用于事件中心 Spout（从事件中心读取的 Java Spout）的新类和方法。 创建使用事件中心 Spout 的拓扑时，请使用以下方法：

* **EventHubSpoutConfig** 类：创建一个对象，其中包含 Spout 组件的配置。

* **TopologyBuilder.SetEventHubSpout** 方法：将事件中心 Spout 组件添加到拓扑。

> [!NOTE]
> 仍必须使用 **CustomizedInteropJSONSerializer** 序列化 Spout 生成的数据。

## <a id="configurationmanager"></a>使用 ConfigurationManager

请勿使用 **ConfigurationManager** 从 Bolt 和 Spout 组件检索配置值。 这样做会导致空指针异常。 而项目的配置将作为拓扑上下文中的键值对传递到 Storm 拓扑中。 对于依赖于配置值的每个组件，必须在初始化期间从上下文中检索它们。

下面的代码演示了如何检索这些值：

```csharp
public class MyComponent : ISCPBolt
{
    // To hold configuration information loaded from context
    Configuration configuration;
    ...
    public MyComponent(Context ctx, Dictionary<string, Object> parms)
    {
        // Save a copy of the context for this component instance
        this.ctx = ctx;
        // If it exists, load the configuration for the component
        if(parms.ContainsKey(Constants.USER_CONFIG))
        {
            this.configuration = parms[Constants.USER_CONFIG] as System.Configuration.Configuration;
        }
        // Retrieve the value of "Foo" from configuration
        var foo = this.configuration.AppSettings.Settings["Foo"].Value;
    }
    ...
}
```

如果使用 `Get` 方法返回组件的实例，则必须确保它将 `Context` 和 `Dictionary<string, Object>` 参数都传递给构造函数。 以下示例是正确传递这些值的基本 `Get` 方法：

```csharp
public static MyComponent Get(Context ctx, Dictionary<string, Object> parms)
{
    return new MyComponent(ctx, parms);
}
```

## <a name="how-to-update-scpnet"></a>如何更新 SCP.NET

最新版 SCP.NET 支持通过 NuGet 进行包升级。 有新的更新可用时，会收到升级通知。 若要手动检查升级，请执行以下步骤：

1. 在“解决方案资源管理器”中，右键单击项目，然后选择“管理 NuGet 包”。

2. 从包管理器中选择“更新”。 有可用更新时会列出。 单击“更新”让包安装更新。

> [!IMPORTANT]
> 如果项目是通过未使用 NuGet 的旧版 SCP.NET 创建的，则必须执行以下步骤以更新到更新版本：
>
> 1. 在“解决方案资源管理器”中，右键单击项目，然后选择“管理 NuGet 包”。
> 2. 使用“搜索”字段搜索 **Microsoft.SCP.Net.SDK**，然后将其添加到项目中。

## <a name="troubleshoot-common-issues-with-topologies"></a>排查使用拓扑的常见问题

### <a name="null-pointer-exceptions"></a>空指针异常

在基于 Linux 的 HDInsight 集群中使用 C# 拓扑时，使用 **ConfigurationManager** 在运行时读取配置设置的 Bolt 和 Spout 组件可能会返回空指针异常。

项目的配置将作为拓扑上下文中的键值对传递到 Storm 拓扑中。 初始化组件时，可以从传递到组件的字典对象检索该配置。

有关详细信息，请参阅本文档的 [ConfigurationManager](#configurationmanager) 部分。

### <a name="systemtypeloadexception"></a>System.TypeLoadException

在基于 Linux 的 HDInsight 群集中使用 C# 拓扑时，可能会遇到以下错误：

    System.TypeLoadException: Failure has occurred while loading a type.

在使用与 Mono 支持的 .NET 版本不兼容的二进制文件时，会发生此错误。

对于基于 Linux 的 HDInsight 集群，应确保项目使用的是为 .NET 4.5 编译的二进制文件。

### <a name="test-a-topology-locally"></a>在本地测试拓扑

虽然很容易就可以将拓扑部署到群集，但是，在某些情况下，可能需要在本地测试拓扑。 使用以下步骤，在开发环境上本地执行和测试本教程中的示例拓扑。

> [!WARNING]
> 本地测试只适用于仅限 C# 的基本拓扑。 不能将本地测试用于混合拓扑或使用多个流的拓扑。

1. 在“解决方案资源管理器”中，右键单击项目，并选择“属性”。 在项目属性中，将“输出类型”更改为“控制台应用程序”。

    ![突出显示“输出类型”的项目属性屏幕截图](./media/apache-storm-develop-csharp-visual-studio-topology/outputtype.png)

   > [!NOTE]
   > 将拓扑部署到群集之前，请记得将“输出类型”更改回“类库”。

2. 在“解决方案资源管理器”中，右键单击项目，然后选择“添加” > “新建项”。 选择“类”，并输入 **LocalTest.cs** 作为类名。 最后，单击“添加”。

3. 打开 **LocalTest.cs**，在顶部添加以下 **using** 语句：

    ```csharp
    using Microsoft.SCP;
    ```

4. 使用以下代码作为 **LocalTest** 类的内容：

    ```csharp
    // Drives the topology components
    public void RunTestCase()
    {
        // An empty dictionary for use when creating components
        Dictionary<string, Object> emptyDictionary = new Dictionary<string, object>();

        #region Test the spout
        {
            Console.WriteLine("Starting spout");
            // LocalContext is a local-mode context that can be used to initialize
            // components in the development environment.
            LocalContext spoutCtx = LocalContext.Get();
            // Get a new instance of the spout, using the local context
            Spout sentences = Spout.Get(spoutCtx, emptyDictionary);

            // Emit 10 tuples
            for (int i = 0; i < 10; i++)
            {
                sentences.NextTuple(emptyDictionary);
            }
            // Use LocalContext to persist the data stream to file
            spoutCtx.WriteMsgQueueToFile("sentences.txt");
            Console.WriteLine("Spout finished");
        }
        #endregion

        #region Test the splitter bolt
        {
            Console.WriteLine("Starting splitter bolt");
            // LocalContext is a local-mode context that can be used to initialize
            // components in the development environment.
            LocalContext splitterCtx = LocalContext.Get();
            // Get a new instance of the bolt
            Splitter splitter = Splitter.Get(splitterCtx, emptyDictionary);

            // Set the data stream to the data created by the spout
            splitterCtx.ReadFromFileToMsgQueue("sentences.txt");
            // Get a batch of tuples from the stream
            List<SCPTuple> batch = splitterCtx.RecvFromMsgQueue();
            // Process each tuple in the batch
            foreach (SCPTuple tuple in batch)
            {
                splitter.Execute(tuple);
            }
            // Use LocalContext to persist the data stream to file
            splitterCtx.WriteMsgQueueToFile("splitter.txt");
            Console.WriteLine("Splitter bolt finished");
        }
        #endregion

        #region Test the counter bolt
        {
            Console.WriteLine("Starting counter bolt");
            // LocalContext is a local-mode context that can be used to initialize
            // components in the development environment.
            LocalContext counterCtx = LocalContext.Get();
            // Get a new instance of the bolt
            Counter counter = Counter.Get(counterCtx, emptyDictionary);

            // Set the data stream to the data created by splitter bolt
            counterCtx.ReadFromFileToMsgQueue("splitter.txt");
            // Get a batch of tuples from the stream
            List<SCPTuple> batch = counterCtx.RecvFromMsgQueue();
            // Process each tuple in the batch
            foreach (SCPTuple tuple in batch)
            {
                counter.Execute(tuple);
            }
            // Use LocalContext to persist the data stream to file
            counterCtx.WriteMsgQueueToFile("counter.txt");
            Console.WriteLine("Counter bolt finished");
        }
        #endregion
    }
    ```

    花费片刻时间通读代码注释。 此代码使用 **LocalContext** 在开发环境中运行组件，并将组件之间的数据流保存到本地磁盘驱动器上的文本文件中。

1. 打开 **Program.cs**，将以下代码添加到 **Main** 方法中：

    ```csharp
    Console.WriteLine("Starting tests");
    System.Environment.SetEnvironmentVariable("microsoft.scp.logPrefix", "WordCount-LocalTest");
    // Initialize the runtime
    SCPRuntime.Initialize();

    //If we are not running under the local context, throw an error
    if (Context.pluginType != SCPPluginType.SCP_NET_LOCAL)
    {
        throw new Exception(string.Format("unexpected pluginType: {0}", Context.pluginType));
    }
    // Create test instance
    LocalTest tests = new LocalTest();
    // Run tests
    tests.RunTestCase();
    Console.WriteLine("Tests finished");
    Console.ReadKey();
    ```

2. 保存更改，然后单击“F5”，或选择“调试” > “开始调试”以启动项目。 随后应会出现一个控制台窗口，并记录测试进行的状态。 出现“测试已完成”后，请按任意键关闭窗口。

3. 使用“Windows 资源管理器”找到包含项目的目录。 例如：**C:\Users\<your_user_name>\Documents\Visual Studio 2013\Projects\WordCount\WordCount**。 在此目录中打开 **Bin**，并单击“调试”。 应可看到运行测试时生成的文本文件：sentences.txt、counter.txt 和 splitter.txt。 打开每个文本文件并检查数据。

   > [!NOTE]
   > 字符串数据保存为这些文件中的十进制值数组。 例如，**splitter.txt** 文件中的 \[[97,103,111]] 是单词 *and*。

> [!NOTE]
> 在部署到 Storm on HDInsight 群集之前，请记得将“项目类型”设置回“类库”。

### <a name="log-information"></a>记录信息

可以使用 `Context.Logger` 轻松记录拓扑组件中的信息。 例如，以下命令会创建一个信息日志条目：

```csharp
Context.Logger.Info("Component started");
```

可从“服务器资源管理器”中的“Hadoop 服务日志”查看记录的信息。 展开 Storm on HDInsight 群集对应的项，然后展开“Hadoop 服务日志”。 最后，选择要查看的日志文件。

> [!NOTE]
> 日志存储在群集使用的 Azure 存储帐户中。 若要查看 Visual Studio 中的日志，必须登录到拥有存储帐户的 Azure 订阅。

### <a name="view-error-information"></a>查看错误信息

若要查看运行中拓扑中所发生的错误，请使用以下步骤：

1. 在“服务器资源管理器”中，右键单击 Storm on HDInsight 群集，并选择“查看 Storm 拓扑”。

2. 对于 **Spout** 和 **Bolt**，“上一错误”列包含有关上次发生的错误的信息。

3. 选择发生错误的组件的“Spout ID”或“Bolt ID”。 在显示的详细信息页面上，页面底部的“错误”部分中列出了其他错误信息。

4. 若要获取详细信息，请从页面的“执行程序”部分中选择“端口”，查看最后几分钟的 Storm 工作进程日志。

### <a name="errors-submitting-topologies"></a>提交拓扑时出现错误

如果将拓扑提交到 HDInsight 时遇到错误，可在 HDInsight 群集上找到处理拓扑提交的服务器端组件的日志。 若要检索这些日志，请从命令行运行以下命令：

    scp sshuser@clustername-ssh.azurehdinsight.net:/var/log/hdinsight-scpwebapi/hdinsight-scpwebapi.out .

将 __sshuser__ 替换为群集的 SSH 用户帐户。 将 __clustername__ 替换为 HDInsight 群集的名称。 有关将 `scp` 和 `ssh` 与 HDInsight 配合使用的详细信息，请参阅[将 SSH 与 HDInsight 配合使用](../hdinsight-hadoop-linux-use-ssh-unix.md)。

以下几个原因可能导致提交失败：

* JDK 未安装或不在相应路径中。
* 提交中未包含必需的 Java 依赖项。
* 依赖项不兼容。
* 拓扑名称重复。

如果 `hdinsight-scpwebapi.out` 日志包含 `FileNotFoundException`，可能是由以下情况导致的：

* JDK 不在开发环境的相应路径中。 确保在开发环境中安装了 JDK，并且 `%JAVA_HOME%/bin` 位于相应路径中。
* 缺少 Java 依赖项。 确保提交中包含所有必需的 .jar 文件。

## <a name="next-steps"></a>后续步骤

有关处理事件中心数据的示例，请参阅[使用 Storm on HDInsight 处理 Azure 事件中心的事件](apache-storm-develop-csharp-event-hub-topology.md)。

有关将流数据拆分为多个流的 C# 拓扑示例，请参阅 [C# Storm 示例](https://github.com/Blackmist/csharp-storm-example)。

若要了解有关创建 C# 拓扑的详细信息，请参阅 [GitHub](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/SCPNet-GettingStarted.md)。

有关 HDInsight 的更多用法和更多 Storm on HDinsight 示例，请参阅以下文档：

**Microsoft SCP.NET**

* [SCP 编程指南](apache-storm-scp-programming-guide.md)

**Apache Storm on HDInsight**

* [使用 Apache Storm on HDInsight 部署和监视拓扑](apache-storm-deploy-monitor-topology.md)
* [Storm on HDInsight 的示例拓扑](apache-storm-example-topology.md)

**Apache Hadoop on HDInsight**

* [将 Hive 与 Hadoop on HDInsight 配合使用](../hadoop/hdinsight-use-hive.md)
* [将 Pig 与 Hadoop on HDInsight 配合使用](../hadoop/hdinsight-use-pig.md)
* [将 MapReduce 与 HDInsight 上的 Hadoop 配合使用](../hadoop/hdinsight-use-mapreduce.md)

**Apache HBase on HDInsight**

* [HBase on HDInsight 入门](../hbase/apache-hbase-tutorial-get-started-linux.md)
