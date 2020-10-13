---
title: 使用 Visual Studio 和 C# 的 Apache Storm 拓扑 - Azure HDInsight
description: 了解如何在 C# 中创建 Storm 拓扑。 在 Visual Studio 中使用适用于 Visual Studio 的 Hadoop 工具创建字数统计拓扑。
ROBOTS: NOINDEX
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.date: 12/31/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 271f62625433a6651ba0e3230a62be51e5147f3e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89000186"
---
# <a name="develop-c-topologies-for-apache-storm-by-using-the-data-lake-tools-for-visual-studio"></a>使用针对 Visual Studio 的 Data Lake 工具开发 Apache Storm 的 C# 拓扑

了解如何使用针对 Visual Studio 的 Azure Data Lake (Apache Hadoop) 工具创建 C# Apache Storm 拓扑。 本文档逐步说明在 Visual Studio 中创建 Storm 项目、在本地测试该项目，然后将它部署到 Apache Storm on Azure HDInsight 群集的过程。

同时还说明了如何创建使用 C# 和 Java 组件的混合拓扑。

C# 拓扑使用 .NET 4.5，并使用 Mono 在 HDInsight 群集上运行。 有关潜在不兼容性的信息，请参阅 [Mono 兼容性](https://www.mono-project.com/docs/about-mono/compatibility/)。 若要使用 C# 拓扑，必须将项目使用的 `Microsoft.SCP.Net.SDK` NuGet 包更新到 0.10.0.6 或更高版本。 包的版本还必须与 HDInsight 上安装的 Storm 的主要版本相符。

| HDInsight 版本 | Apache Storm 版本 | SCP.NET 版本 | 默认 Mono 版本 |
|:-----------------:|:-------------:|:---------------:|:--------------------:|
| 3.4 | 0.10.0.x | 0.10.0.x | 3.2.8 |
| 3.5 | 1.0.2.x | 1.0.0.x | 4.2.1 |
| 3.6 | 1.1.0.x | 1.0.0.x | 4.2.8 |

## <a name="prerequisite"></a>先决条件

HDInsight 上的 Apache Storm 群集。 请参阅[使用 Azure 门户创建 Apache Hadoop 群集](../hdinsight-hadoop-create-linux-clusters-portal.md)，并选择 **Storm** 作为**群集类型**。

## <a name="install-visual-studio"></a>安装 Visual Studio

可以使用 [Visual Studio](https://visualstudio.microsoft.com/downloads/) 通过 SCP.NET 开发 C# 拓扑。 本文中的说明使用 Visual Studio 2019，但你也可以使用更低版本的 Visual Studio。

## <a name="install-data-lake-tools-for-visual-studio"></a>安装针对 Visual Studio 的 Data Lake 工具

若要安装针对 Visual Studio 的 Data Lake 工具，请执行[针对 Visual Studio 的 Data Lake 工具使用入门](../hadoop/apache-hadoop-visual-studio-tools-get-started.md#install-data-lake-tools-for-visual-studio)中的步骤。

## <a name="install-java"></a>安装 Java

从 Visual Studio 提交 Storm 拓扑时，SCP.NET 会生成一个包含拓扑和依赖项的 zip 文件。 使用 Java 来创建这些 zip 文件是因为其采用的格式更兼容基于 Linux 的群集。

1. 在开发环境中安装 Java 开发人员工具包 (JDK) 7 或更高版本。 可以从 [Oracle](https://openjdk.java.net/) 获取 Oracle JDK。 也可使用[其他 Java 发行版](/java/azure/jdk/)。

2. 将 `JAVA_HOME` 环境变量设置为包含 Java 的目录。

3. 设置 `PATH` 环境变量以包含 `%JAVA_HOME%\bin` 目录。

可以生成并运行以下 C# 控制台应用程序来验证 Java 和 JDK 是否已正确安装：

```csharp
using System;
using System.IO;
namespace ConsoleApplication2
{
   class Program
   {
       static void Main(string[] args)
       {
           string javaHome = Environment.GetEnvironmentVariable("JAVA_HOME");
           if (!string.IsNullOrEmpty(javaHome))
           {
               string jarExe = Path.Combine(javaHome + @"\bin", "jar.exe");
               if (File.Exists(jarExe))
               {
                   Console.WriteLine("JAVA Is Installed properly");
                    return;
               }
               else
               {
                   Console.WriteLine("A valid JAVA JDK is not found. Looks like JRE is installed instead of JDK.");
               }
           }
           else
           {
             Console.WriteLine("A valid JAVA JDK is not found. JAVA_HOME environment variable is not set.");
           }
       }  
   }
}
```

## <a name="apache-storm-templates"></a>Apache Storm 模板

针对 Visual Studio 的 Data Lake 工具提供以下模板：

| 项目类型 | 演示 |
| --- | --- |
| Storm 应用程序 |空的 Storm 拓扑项目。 |
| Storm Azure SQL 写入器示例 |如何写入 Azure SQL 数据库。 |
| Storm Azure Cosmos DB 读取器示例 |如何从 Azure Cosmos DB 读取。 |
| Storm Azure Cosmos DB 写入器示例 |如何写入 Azure Cosmos DB。 |
| Storm EventHub 读取器示例 |如何从 Azure 事件中心读取。 |
| Storm EventHub 写入器示例 |如何写入 Azure 事件中心。 |
| Storm HBase 读取器示例 |如何从 HBase on HDInsight 群集读取。 |
| Storm HBase 写入器示例 |如何写入 HBase on HDInsight 群集。 |
| Storm 混合示例 |如何使用 Java 组件。 |
| Storm 示例 |基本的字数统计拓扑。 |

> [!WARNING]  
> 并非所有模板都可用于基于 Linux 的 HDInsight。 模板使用的 NuGet 程序包可能与 Mono 不兼容。 若要识别潜在的问题，请查看 [Mono 兼容性](https://www.mono-project.com/docs/about-mono/compatibility/)并使用 [.NET Portability Analyzer](../hdinsight-hadoop-migrate-dotnet-to-linux.md#automated-portability-analysis)。

在本文档的步骤中，将使用基本 Storm 应用程序项目类型来创建拓扑。

### <a name="apache-hbase-templates"></a>Apache HBase 模板

HBase 读取器和写入器模板使用 HBase REST API（而不是 HBase Java API）与 HBase on HDInsight 群集通信。

### <a name="eventhub-templates"></a>EventHub 模板

> [!IMPORTANT]  
> EventHub 读取器模板随附的基于 Java 的 EventHub Spout 组件不适用于 Storm on HDInsight 3.5 或更高版本。 [GitHub](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/HDI3.5/lib) 上提供此组件的更新版本。

如需使用此组件且适用于 Storm on HDInsight 3.5 的示例拓扑，请参阅 [GitHub](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub)。

## <a name="create-a-c-topology"></a>创建 C# 拓扑

在 Visual Studio 中创建 C# 拓扑项目：

1. 打开 Visual Studio。

1. 在“开始”窗口中，选择“创建新项目”。  

1. 在“创建新项目”窗口中，滚动并选择“Storm 应用程序”，然后选择“下一步”。   

1. 在“配置新项目”窗口中，输入 *WordCount* 作为**项目名称**，转到或创建该项目的**位置**目录路径，然后选择“创建”。  

    ![Storm 应用程序，“配置新项目”对话框，Visual Studio](./media/apache-storm-develop-csharp-visual-studio-topology/apache-storm-new-project.png)

创建项目后，应有以下文件：

* Program.cs  ：项目的拓扑定义。 默认情况下会创建包含一个 Spout 和一个 Bolt 的默认拓扑。

* *Spout.cs*：发出随机数的示例 Spout。

* *Bolt.cs*：保留 Spout 所发出数字计数的示例 Bolt。

创建项目时，NuGet 会下载最新的 [SCP.NET 包](https://www.nuget.org/packages/Microsoft.SCP.Net.SDK/)。

### <a name="implement-the-spout"></a>实现 Spout

接下来，添加 spout 的代码，用于从外部源读取拓扑中的数据。 此 Spout 会随机将语句发出到拓扑中。

1. 打开 *Spout.cs*。 Spout 的主要组件如下：

   * `NextTuple`：允许 Spout 发出新的 Tuple 时由 Storm 调用。

   * `Ack`（仅限事务拓扑）：针对从此 Spout 发送的 Tuple，处理拓扑中其他组件启动的确认。 确认元组可让 Spout 知了解下游组件已成功处理元组。

   * `Fail`（仅限事务拓扑）：处理那些无法处理拓扑中其他组件的元组。 实现 `Fail` 方法可以重新发出 Tuple，以便对其再次处理。

2. 将 `Spout` 类的内容替换为以下文本：

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

现在，请在本示例中创建两个 Storm Bolt：

1. 删除项目中的现有 *Bolt.cs* 文件。

2. 在“解决方案资源管理器”  中，右键单击该项目，然后选择“添加”   > “新建项”  。 从列表中选择“Storm Bolt”  ，然后输入 Splitter.cs  作为名称。 在新文件的代码中，将命名空间名称更改为 `WordCount`。 然后重复此过程，以创建名为 *Counter.cs* 的另一个 Bolt。

   * *Splitter.cs*：实现 Bolt，以将句子分割成不同的单词并发出一串新单词。

   *  Counter.cs：实现 Bolt，对每个单词计数，并发出一串新单词和每个单词的计数。

     > [!NOTE]  
     > 这些 Bolt 读取和写入流，但是你也可以使用 Bolt 来与数据库或服务等源进行通信。

3. 打开 *Splitter.cs*。 默认情况下它只包含一个方法：`Execute`。 在 Bolt 收到要处理的 Tuple 时将调用 `Execute` 方法。 此时，可读取和处理传入元组，以及发出传出元组。

4. 将 `Splitter` 类的内容替换为以下代码：

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

5. 打开 *Counter.cs* 并将类内容替换为以下代码：

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

![Spout 和 Bolt 组件排列图，Storm 拓扑](./media/apache-storm-develop-csharp-visual-studio-topology/word-count-topology1.png)

Spout 发出句子，这些句子将分布到 Splitter Bolt 的实例。 Splitter Bolt 将句子分割成多个单词，并将这些单词分布到 Counter Bolt。

由于 Counter 实例在本地保留字数，因此需确保特定单词流向相同的 Counter Bolt 实例。 每个实例都会跟踪特定的单词。 由于 Splitter Bolt 不保留任何状态，因此哪个 Splitter 实例接收哪个语句无关紧要。

打开 *Program.cs*。 重要的方法是 `GetTopologyBuilder`，用于定义提交到 Storm 的拓扑。 将 `GetTopologyBuilder` 的内容替换为以下代码，以实现上面所述的拓扑：

```csharp
// Create a new topology named 'WordCount'
TopologyBuilder topologyBuilder = new TopologyBuilder(
    "WordCount" + DateTime.Now.ToString("yyyyMMddHHmmss"));

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

你现在已准备好将拓扑提交到 HDInsight 群集。

1. 导航到“视图” > “服务器资源管理器”。  

1. 右键单击“Azure”并选择“连接到 Microsoft Azure 订阅...”，然后完成登录过程。  

1. 在“解决方案资源管理器”中右键单击项目，然后选择“提交到 Storm on HDInsight”。  

1. 从“提交拓扑”对话框中的“Storm 群集”下拉列表内，选择你的 Storm on HDInsight 群集，然后选择“提交”。    可以查看“输出”窗口来检查提交是否成功。 

    成功提交拓扑后，应会出现群集的“Storm 拓扑视图”窗口。  从列表中选择“WordCount”拓扑，查看正在运行的拓扑的信息。 

    ![Storm 拓扑视图窗口，HDInsight 群集，Visual Studio](./media/apache-storm-develop-csharp-visual-studio-topology/storm-topology-view.png)

    > [!NOTE]  
    > 也可在“服务器资源管理器”中查看“Storm 拓扑”。   展开“Azure” > “HDInsight”，右键单击 Storm on HDInsight 群集，然后选择“查看 Storm 拓扑”。   

    若要查看拓扑中组件的信息，请选择图中的组件。

1. 在“拓扑摘要”部分，选择“终止”以停止拓扑   。

    > [!NOTE]  
    > Storm 拓扑会一直运行，直到它被停用，或者群集被删除。

## <a name="transactional-topology"></a>事务拓扑

前面的拓扑是非事务性的拓扑中的组件不实现重播消息的功能。 拓扑中的组件不实现重播消息的功能。 如需事务拓扑的示例，请创建一个项目，然后选择“Storm 示例”  作为项目类型。

事务拓扑会实现以下项来支持重播数据：

*  元数据缓存：Spout 必须存储所发出数据的元数据，这样，在失败时，就可以再次检索和发出数据。 此示例所发出的数据较少，因此将每个元组的原始数据存储在字典中以便重播。

*  Ack：拓扑中的每个 Bolt 都可以调用 `this.ctx.Ack(tuple)` 来确认它已成功处理元组。 所有 Bolt 都已确认 Tuple 之后，即会调用 Spout 的 `Ack` 方法。 `Ack` 方法允许 Spout 删除为重放而缓存的数据。

*  失败：每个 Bolt 都可以调用 `this.ctx.Fail(tuple)`，指出元组的处理失败。 这项失败会传播到 Spout 的 `Fail` 方法，在其中，可以使用缓存的元数据来重放 Tuple。

* **序列 ID**：发出元组时，可以指定唯一的序列 ID。 此值标识要进行重播（`Ack` 和 `Fail`）处理的 Tuple。 例如，发出数据时，“Storm 示例”项目中的 Spout 会使用以下方法调用： 

  `this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new Values(sentence), lastSeqId);`

  此代码使用中包含的序列 ID 值向默认流发出包含句子的元组 `lastSeqId` 。 在此示例中，为 `lastSeqId` 发出的每个元组递增。

如“Storm 示例”  项目所示，在运行时，可以根据配置来设置组件是否为事务性。

## <a name="hybrid-topology-with-c-and-java"></a>使用 C# 和 Java 的混合拓扑

还可使用针对 Visual Studio 的 Data Lake 工具来创建混合拓扑，其中的某些组件采用 C# 编写，另一些组件采用 Java 编写。

如需混合拓扑的示例，请创建一个项目，然后选择“Storm 混合示例”  。 此示例类型演示以下概念：

*  Java Spout 和  C# Bolt：在 `HybridTopology_javaSpout_csharpBolt` 类中定义。

  事务版本在 `HybridTopologyTx_javaSpout_csharpBolt` 类中定义。

*  C# Spout 和  Java Bolt：在 `HybridTopology_csharpSpout_javaBolt` 类中定义。

  事务版本在 `HybridTopologyTx_csharpSpout_javaBolt` 类中定义。

  > [!NOTE]  
  > 此版本还演示了如何使用文本文件中的 clojure 代码作为 Java 组件。

若要切换在提交项目时使用的拓扑，请将 `[Active(true)]` 语句移到要在提交给群集之前使用的拓扑。

> [!NOTE]  
> 在 *JavaDependency* 文件夹中，所需的所有 Java 文件都会提供为此项目的一部分。

创建和提交混合拓扑时，需注意以下事项：

* 使用 `JavaComponentConstructor` 创建 Spout 或 Bolt 的 Java 类实例。

* 使用 `microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer` 将传入或传出 Java 组件的数据从 Java 对象序列化为 JSON。

* 将拓扑提交到服务器时，必须使用“其他配置”  选项指定 Java 文件路径  。 指定的路径应该是包含 JAR 文件的目录，而 JAR 文件包含 Java 类。

### <a name="azure-event-hubs"></a>Azure 事件中心

SCP.NET 版本 0.9.4.203 引入了专用于事件中心 Spout（从事件中心读取数据的 Java Spout）的新类和方法。 创建采用事件中心 Spout 的拓扑（例如，使用“Storm EventHub 读取器示例”模板）时，请使用以下 API： 

* `EventHubSpoutConfig` 类：创建一个对象，其中包含 Spout 组件的配置。

* `TopologyBuilder.SetEventHubSpout` 方法：将事件中心 Spout 组件添加到拓扑。

> [!NOTE]  
> 仍然必须使用 `CustomizedInteropJSONSerializer` 来序列化 Spout 生成的数据。

## <a name="use-configurationmanager"></a>使用 ConfigurationManager

请勿使用 ConfigurationManager 从 Bolt 和 Spout 组件检索配置值。  这样做可能导致空指针异常。 项目的配置将作为拓扑上下文中的键值对传递到 Storm 拓扑中。 每个依赖于配置值的组件都必须在初始化过程中从上下文检索这些值。

下面的代码演示如何检索这些值：

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
            this.configuration = parms[Constants.USER_CONFIG] 
                as System.Configuration.Configuration;
        }
        // Retrieve the value of "Foo" from configuration
        var foo = this.configuration.AppSettings.Settings["Foo"].Value;
    }
    ...
}
```

如果使用 `Get` 方法返回组件的实例，则必须确保它将 `Context` 和 `Dictionary<string, Object>` 参数都传递给构造函数。 以下示例是一个基本的 `Get` 方法，用于正确传递这些值：

```csharp
public static MyComponent Get(Context ctx, Dictionary<string, Object> parms)
{
    return new MyComponent(ctx, parms);
}
```

## <a name="how-to-update-scpnet"></a>如何更新 SCP.NET

最新版 SCP.NET 支持通过 NuGet 进行包升级。 有新的更新可用时，将收到升级通知。 若要手动检查升级，请执行以下步骤：

1. 在“解决方案资源管理器”  中，右键单击项目，然后选择“管理 NuGet 包”  。

2. 从包管理器中选择“更新”  。 有适用于 SCP.NET 支持包的更新时，会列出此更新。 选择该包对应的“更新”，然后在“预览更改”对话框中，选择“确定”以安装更新。   

> [!IMPORTANT]  
> 如果项目是通过未使用 NuGet 的旧版 SCP.NET 创建的，则必须执行以下步骤以更新到新版本：
>
> 1. 在“解决方案资源管理器”  中，右键单击项目，然后选择“管理 NuGet 包”  。
> 2. 使用 **搜索** 字段，搜索并添加 `Microsoft.SCP.Net.SDK` 到项目。

## <a name="troubleshoot-common-issues-with-topologies"></a>排查拓扑常见问题

### <a name="null-pointer-exceptions"></a>空指针异常

在将 c # 拓扑与基于 Linux 的 HDInsight 群集配合使用时，使用 **ConfigurationManager** 在运行时读取配置设置的 spout 组件可能会返回空指针异常。

项目的配置将作为拓扑上下文中的键值对传递到 Storm 拓扑中。 它可以从在初始化时传递给组件的字典对象中进行检索。

有关详细信息，请参阅本文档的[使用 ConfigurationManager](#use-configurationmanager) 部分。

### <a name="systemtypeloadexception"></a>System.TypeLoadException

在基于 Linux 的 HDInsight 群集中使用 C# 拓扑时，可能会遇到以下错误：

`System.TypeLoadException: Failure has occurred while loading a type.`

如果使用二进制文件，而该文件不兼容 Mono 支持的 .NET 版本，通常会发生此错误。

对于基于 Linux 的 HDInsight 群集，请确保项目使用的二进制文件是针对 .NET 4.5 编译的。

### <a name="test-a-topology-locally"></a>在本地测试拓扑

尽管可以轻松地将拓扑部署到群集，但在某些情况下，可能需要在本地测试拓扑。 使用以下步骤，在开发环境上本地运行和测试本文中的示例拓扑。

> [!WARNING]  
> 本地测试只适用于仅限 C# 的基本拓扑。 不能将本地测试用于混合拓扑或用于使用多个流的拓扑。

1. 在“解决方案资源管理器”  中，右键单击项目，然后选择“属性”  。 在项目属性中， 将“输出类型”更改为“控制台应用程序”。  

   ![HDInsight Storm 应用程序，项目属性，输出类型](./media/apache-storm-develop-csharp-visual-studio-topology/hdi-output-type-window.png)

   > [!NOTE]
   > 将拓扑部署到群集之前，请记得将“输出类型”  改回“类库”  。

1. 在“解决方案资源管理器”  中，右键单击项目，然后选择“添加”   > “新建项”  。 选择“类”  ，并输入  LocalTest.cs 作为类名称。 最后选择“添加”  。

1. 打开 *LocalTest.cs*，在顶部添加以下 `using` 语句：

    ```csharp
    using Microsoft.SCP;
    ```

1. 将以下代码用作 `LocalTest` 类的内容：

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

    花费片刻时间通读代码注释。 此代码使用 `LocalContext` 在开发环境中运行组件。 它将组件之间的数据流保存到本地磁盘驱动器上的文本文件中。

1. 打开 *Program.cs*，将以下代码添加到 `Main` 方法中：

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

1. 保存更改，然后按 **F5**，或选择“调试”   > “开始调试”  以启动项目。 此时会出现一个控制台窗口，该窗口会在测试进行过程中记录状态。 显示 `Tests finished` 时，请按任意键以关闭窗口。

1. 使用 Windows 资源管理器  找到包含项目的目录。 （例如：*C:\\Users\\\<your_user_name>\\source\\repos\\WordCount\\WordCount*)在此目录中打开 *Bin*，然后选择“调试”。 应可看到运行测试时生成的文本文件：*sentences.txt*、*counter.txt* 和 *splitter.txt*。 打开每个文本文件并检查数据。

   > [!NOTE]  
   > 字符串数据在这些文件中持久保存为十进制值数组。 例如，**splitter.txt** 文件中的 `[[97,103,111]]` 代表单词 *ago*。

> [!NOTE]  
> 在部署到 Storm on HDInsight 群集之前，请确保在项目属性中将“项目类型”  设置回到“类库”  。

### <a name="log-information"></a>记录信息

可以使用 `Context.Logger`轻松记录拓扑组件中的信息。 例如，以下命令会创建一个信息日志条目：

`Context.Logger.Info("Component started");`

可从“服务器资源管理器”  中的“Hadoop 服务日志”  查看记录的信息。  展开 Storm on HDInsight 群集的条目，然后展开“Hadoop 服务日志”。 最后，选择要查看的日志文件。

> [!NOTE]  
> 日志存储在群集使用的 Azure 存储帐户中。 若要查看 Visual Studio 中的日志，必须登录到拥有存储帐户的 Azure 订阅。

### <a name="view-error-information"></a>查看错误信息

若要查看运行中拓扑中所发生的错误，请使用以下步骤：

1. 在“服务器资源管理器”  中，右键单击 Storm on HDInsight 群集，然后选择“查看 Storm 拓扑”  。

   对于 Spout  和 Bolt  ，“上一错误”  列包含有关上次错误的信息。

2. 选择列出了错误的组件的“Spout ID”  或“Bolt ID”  。 详细信息页面底部的“错误”  部分会列出其他错误信息。

3. 若要获取详细信息，请从页面的“执行器”  部分中选择“端口”  ，以查看最后几分钟的 Storm 工作进程日志。

### <a name="errors-submitting-topologies"></a>提交拓扑时出错

如果将拓扑提交到 HDInsight 时遇到错误，可在 HDInsight 群集上找到处理拓扑提交的服务器端组件的日志。 若要下载这些日志，请从命令行运行以下命令：

```cmd
scp sshuser@clustername-ssh.azurehdinsight.net:/var/log/hdinsight-scpwebapi/hdinsight-scpwebapi.out .
```

将 *sshuser* 替换为群集的 SSH 用户帐户。 将 *clustername* 替换为 HDInsight 群集的名称。 有关将 `scp` 和 `ssh` 与 HDInsight 配合使用的详细信息，请参阅[将 SSH 与 HDInsight 配合使用](../hdinsight-hadoop-linux-use-ssh-unix.md)。

提交失败可能有多个原因：

* JDK 未安装或不在路径中。
* 未在提交内容中包含所需的 Java 依赖项。
* 依赖项不兼容。
* 拓扑名称重复。

如果 *hdinsight-scpwebapi.out* 日志文件包含 `FileNotFoundException`，该异常可能是由以下情况导致的：

* JDK 不在开发环境的路径中。 请验证 JDK 是否安装在开发环境中，以及 `%JAVA_HOME%/bin` 是否在路径中。
* 缺少 Java 依赖项。 请确保提交内容包含全部所需的 .jar 文件。

## <a name="next-steps"></a>后续步骤

如需处理事件中心数据的示例，请参阅[使用 Storm on HDInsight 处理 Azure 事件中心的事件](apache-storm-develop-csharp-event-hub-topology.md)。

如需将流数据拆分为多个流的 C# 拓扑示例，请参阅 [C# Storm 示例](https://github.com/Blackmist/csharp-storm-example)。

若要详细了解如何创建 C# 拓扑，请查看 [GitHub](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/SCPNet-GettingStarted.md)。

有关 HDInsight 的其他用法和其他 Storm on HDinsight 示例，请参阅以下文档：

**Microsoft SCP.NET**

* [Azure HDInsight 中 Apache Storm 的 SCP 编程指南](apache-storm-scp-programming-guide.md)

**Apache Storm on HDInsight**

* [在 Azure HDInsight 中部署和管理 Apache Storm 拓扑](apache-storm-deploy-monitor-topology-linux.md)
* [Azure HDInsight 中的示例 Apache Storm 拓扑](apache-storm-example-topology.md)

**Apache HDInsight 上的 Hadoop**

* [什么是 Azure HDInsight 中的 Apache Hive 和 HiveQL？](../hadoop/hdinsight-use-hive.md)
* [在 Apache Hadoop on HDInsight 中使用 MapReduce](../hadoop/hdinsight-use-mapreduce.md)

**Apache HBase on HDInsight**

* [在 Azure HDInsight 中使用 Apache HBase](../hbase/apache-hbase-tutorial-get-started-linux.md)
