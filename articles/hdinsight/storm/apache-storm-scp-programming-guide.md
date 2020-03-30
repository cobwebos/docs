---
title: 适用于 Azure HDInsight 中 Storm 的 SCP.NET 编程指南
description: 了解如何通过 SCP.NET 创建可在 Azure HDInsight 中运行的 Storm 中使用的基于 .NET 的 Storm 拓扑。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/13/2020
ms.openlocfilehash: ddf69a75a39911293277a4a4189cf4e79256e09d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77186868"
---
# <a name="scp-programming-guide-for-apache-storm-in-azure-hdinsight"></a>Azure HDInsight 中 Apache Storm 的 SCP 编程指南

SCP 是构建实时、可靠、一致和高性能数据处理应用程序的平台。 它建立在[Apache风暴](https://storm.incubator.apache.org/)之上，这是一个由开源软件社区设计的流处理系统。 内森·马兹创造了风暴 它被推特作为开放源码出版。 风暴使用[阿帕奇动物园，](https://zookeeper.apache.org/)这是另一个Apache项目，使高度可靠的分布式协调和状态管理。

SCP 项目不仅在 Windows 上移植了 Storm，而且还移植了 Windows 环境的项目添加扩展和自定义。 扩展包括 .NET 开发人员体验和 .NET 库。 该自定义项包括基于 Windows 的部署。

使用扩展和自定义，您无需分叉开源软件项目。 您可以使用构建在 Storm 之上的派生环境。

## <a name="processing-model"></a>处理模型

SCP 中的数据以连续的元组流形式建模。 通常，元：

1. 流入队列。
1. 由在 Storm 拓扑中托管的业务逻辑拾取和转换。
1. 要么将其输出作为元/数传送到另一个 SCP 系统，要么被提交到分布式文件系统和 SQL Server 等存储中。

![队列将数据馈送到处理的关系图，该图反过来又为数据存储供电](./media/apache-storm-scp-programming-guide/queue-feeding-data-to-processing-to-data-store.png)

在 Storm 中，应用程序拓扑定义计算图。 拓扑中的每个节点都包含处理逻辑。 节点之间的链接指示数据流。

将输入数据注入拓扑的节点称为_spout。_ 您可以使用它们对数据进行排序。 输入数据可能来自文件日志、事务数据库或系统性能计数器等源。

同时具有输入和输出数据流的节点称为_螺栓_。 它们执行实际的数据筛选、选择和聚合。

SCP 支持尽力而为，至少一次，一次数据处理。

在分布式流处理应用程序中，数据处理期间可能会出现错误。 此类错误包括网络中断、计算机故障或代码中的错误。 至少一次处理通过在发生错误时自动重播相同的数据来确保至少处理一次所有数据。

至少一次处理简单可靠，并且适用于许多应用。 但是，当应用程序需要精确计数时，至少一次处理是不够的，因为相同的数据可以在应用程序拓扑中重播。 在这种情况下，即使多次重播和处理数据，也可确保结果正确。

SCP 允许 .NET 开发人员在使用 Java 虚拟机 （JVM） 时创建实时数据处理应用程序。 JVM 和 .NET 通过 TCP 本地套接字进行通信。 每个分出/螺栓都是 .NET/Java 进程对，其中用户逻辑作为插件在 .NET 进程中运行。

要在 SCP 之上构建数据处理应用程序，请按照以下步骤操作：

1. 设计和实现样出，以便从队列中提取数据。
1. 设计和实现处理输入数据的螺栓并将其保存到外部存储（如数据库）
1. 设计拓扑，然后提交并运行它。

拓扑定义顶点和它们之间流动的数据。 SCP 采用拓扑规范并将其部署到 Storm 群集上，其中每个顶点在一个逻辑节点上运行。 Storm 任务计划程序负责故障转移和缩放。

本文使用一些简单示例来演练如何使用 SCP 构建数据处理应用程序。

## <a name="scp-plug-in-interface"></a>SCP 插件接口

SCP 插件是独立应用程序。 它们可以在开发期间在 Visual Studio 中运行，并在生产部署后插入 Storm 管道。

编写 SCP 插件与编写任何其他 Windows 控制台应用程序相同。 SCP.NET平台声明一些接口用于喷口/螺栓。 您的插件代码实现这些接口。 此设计的主要目的是让您专注于业务逻辑，同时让SCP.NET平台处理其他事情。

您的插件代码实现了以下接口之一。 哪个接口取决于拓扑是事务性的还是非事务性的，以及组件是喷口还是螺栓。

* **ISCPSpout**
* **ISCPBolt**
* **ISCPTxSpout**
* **ISCPBatchBolt**

### <a name="iscpplugin"></a>ISCPPlugin

**ISCPPlugin**是许多插件的常见接口，目前，它是一个虚拟的界面。

```csharp
public interface ISCPPlugin
{
}
```

### <a name="iscpspout"></a>ISCPSpout

**ISCPSpout**是非事务性喷口的接口。

```csharp
public interface ISCPSpout : ISCPPlugin
{
    void NextTuple(Dictionary<string, Object> parms);
    void Ack(long seqId, Dictionary<string, Object> parms); 
    void Fail(long seqId, Dictionary<string, Object> parms);
}
```

当调用**NextTuple**时，您的 C# 代码可能会发出一个或多个元组。 如果没有什么可排放的，则此方法应该返回而不发出任何内容。

**NextTuple、Ack**和**Fail**方法都在 C# 进程的单个线程中的紧密循环中调用。 **Ack** 当没有要发出的元组时，让**NextTuple**睡眠短时间（如 10 毫秒）。 此睡眠有助于避免浪费 CPU 可用性。

仅当规范文件启用确认机制时，才调用**Ack**和**Fail**方法。 *seqId*参数标识已确认或已失败的元组。 如果在非事务性拓扑中启用了确认，则应在出点中使用以下**Emit**函数：

```csharp
public abstract void Emit(string streamId, List<object> values, long seqId);
```

如果非事务拓扑不支持确认，**则 Ack**和**Fail**可以保留为空函数。

这些函数中的*parms*输入参数指定一个空字典，并保留以供将来使用。

### <a name="iscpbolt"></a>ISCPBolt

**ISCPBolt**是非事务性螺栓的接口。

```csharp
public interface ISCPBolt : ISCPPlugin
{
void Execute(SCPTuple tuple);
}
```

当新的元组可用时，将调用**Execute**函数来处理它。

### <a name="iscptxspout"></a>ISCPTxSpout

**ISCPTxSpout**是事务喷口的接口。

```csharp
public interface ISCPTxSpout : ISCPPlugin
{
    void NextTx(out long seqId, Dictionary<string, Object> parms);  
    void Ack(long seqId, Dictionary<string, Object> parms);         
    void Fail(long seqId, Dictionary<string, Object> parms);        
}
```

就像他们的非事务性对应项一样 **，NextTx、Ack**和**Fail**都在 C# 进程的单个线程中的紧密循环中调用。 **Ack** 当没有要发出的元数时，让**NextTx**睡眠短时间（如 10 毫秒）。 此睡眠有助于避免浪费 CPU 可用性。

当**NextTx**调用以启动新事务时 *，seqId*输出参数标识事务。 该事务也用于**Ack**和**失败**。 **NextTx**方法可以向 Java 端发出数据。 然后，数据会被存储在 ZooKeeper 中，以支持重用。 由于 ZooKeeper 的容量有限，因此代码应仅发出元数据，而不应在事务喷口中发出批量数据。

由于 Storm 会自动重播失败的事务，因此通常不会调用**失败**。 但是，如果 SCP 可以检查事务喷口发出的元数据，则可以在元数据无效时调用**Fail。**

这些函数中的*parms*输入参数指定一个空字典，并保留以供将来使用。

### <a name="iscpbatchbolt"></a>ISCPBatchBolt

**ISCPBatchBolt**是事务螺栓的接口。

```csharp
public interface ISCPBatchBolt : ISCPPlugin
{
    void Execute(SCPTuple tuple);
    void FinishBatch(Dictionary<string, Object> parms);  
}
```

当新元组到达螺栓时，将调用**Execute**方法。 此事务结束时调用**FinishBatch**方法。 *parms*输入参数保留供将来使用。

对于事务拓扑 **，StormTx尝试**是一个重要的类。 它有两个成员 **：TxId**和**尝试 Id**。 **TxId**成员标识特定事务。 如果事务失败并重播，可能会尝试多次。

SCP.NET创建新的**ISCPBatchBolt**对象来处理每个**StormTx尝试**对象，就像 Storm 在 Java 中所做的一样。 此设计的目的是支持并行事务处理。 事务尝试完成后，将销毁相应的**ISCPBatchBolt**对象并收集垃圾。

## <a name="object-model"></a>对象模型

SCP.NET 还会提供可供开发人员用于编程的简单密钥对象集。 对象是**上下文**、**状态存储**和**SCPRuntime**。 本节将讨论它们。

### <a name="context"></a>上下文

**上下文**对象向应用程序提供正在运行的环境。 ISCPSpout、ISCPBolt、ISCPTxSpout 或**ISCPTxSpout** **ISCPBatchBolt**的每个**ISCPPlugin**实例都有相应的**上下文**实例。 **ISCPSpout** **ISCPBolt** **上下文**提供的功能分为以下两部分：

* 静态部件，可在整个 C# 流程中提供
* 动态部分，仅适用于特定**上下文**实例

### <a name="static-part"></a>静态部分

```csharp
public static ILogger Logger = null;
public static SCPPluginType pluginType;
public static Config Config { get; set; }
public static TopologyContext TopologyContext { get; set; }  
```

**记录器**对象是为日志记录目的提供的。

**插件类型**对象指示 C# 进程的插件类型。 如果进程在本地测试模式下运行，而不使用 Java，则插件类型**将SCP_NET_LOCAL**。

```csharp
public enum SCPPluginType 
{
    SCP_NET_LOCAL = 0,
    SCP_NET_SPOUT = 1,
    SCP_NET_BOLT = 2,
    SCP_NET_TX_SPOUT = 3,
    SCP_NET_BATCH_BOLT = 4  
    }
```

**Config**属性从 Java 端获取配置参数，在 C# 插件初始化时传递这些参数。 **配置**参数分为两部分：**风暴Conf**和**插件Conf。**

```csharp
public Dictionary<string, Object> stormConf { get; set; }  
public Dictionary<string, Object> pluginConf { get; set; }  
```

**风暴Conf**部分是由 Storm 定义的参数，**插件Conf**部分是 SCP 定义的参数。 下面是一个示例：

```csharp
public class Constants
{
    … …

    // constant string for pluginConf
    public static readonly String NONTRANSACTIONAL_ENABLE_ACK = "nontransactional.ack.enabled";  

    // constant string for stormConf
    public static readonly String STORM_ZOOKEEPER_SERVERS = "storm.zookeeper.servers";
    public static readonly String STORM_ZOOKEEPER_PORT = "storm.zookeeper.port";
}
```

**拓扑上下文**类型获取拓扑上下文。 它对于多个并行组件最有用。 下面是一个示例：

```csharp
//demo how to get TopologyContext info
if (Context.pluginType != SCPPluginType.SCP_NET_LOCAL)
{
    Context.Logger.Info("TopologyContext info:");
    TopologyContext topologyContext = Context.TopologyContext;
    Context.Logger.Info("taskId: {0}", topologyContext.GetThisTaskId());
    taskIndex = topologyContext.GetThisTaskIndex();
    Context.Logger.Info("taskIndex: {0}", taskIndex);
    string componentId = topologyContext.GetThisComponentId();
    Context.Logger.Info("componentId: {0}", componentId);
    List<int> componentTasks = topologyContext.GetComponentTasks(componentId);  
    Context.Logger.Info("taskNum: {0}", componentTasks.Count);
}
```

### <a name="dynamic-part"></a>动态部分

以下接口与特定**上下文**实例相关，该实例由SCP.NET平台创建并传递给代码：

```csharp
// Declare the Output and Input Stream Schemas

public void DeclareComponentSchema(ComponentStreamSchema schema);

// Emit tuple to default stream.
public abstract void Emit(List<object> values);

// Emit tuple to the specific stream.
public abstract void Emit(string streamId, List<object> values);  
```

对于支持确认的非事务式喷口，提供了以下方法：

```csharp
// for nontransactional spout that supports ack
public abstract void Emit(string streamId, List<object> values, long seqId);  
```

支持确认的非事务性螺栓应显式调用**Ack**或**失败**，并与其收到的元组进行连接。 发射新元组时，螺栓还必须指定元组锚。 提供了以下方法：

```csharp
public abstract void Emit(string streamId, IEnumerable<SCPTuple> anchors, List<object> values);
public abstract void Ack(SCPTuple tuple);
public abstract void Fail(SCPTuple tuple);
```

### <a name="statestore"></a>StateStore

**StateStore**对象提供元数据服务、单调序列生成和无等待协调。 您可以在**StateStore**上构建更高级别的分布式并发抽象。 这些抽象包括分布式锁、分布式队列、障碍和事务服务。

SCP 应用程序可以使用**状态**对象对[Apache ZooKeeper](https://zookeeper.apache.org/)中的信息进行序列化。 此功能对于事务拓扑尤其有价值。 如果事务喷口停止响应并重新启动，**状态**可以从 ZooKeeper 检索必要的信息并重新启动管道。

**StateStore**对象具有以下主要方法：

```csharp
/// <summary>
/// Static method to retrieve a state store of the given path and connStr 
/// </summary>
/// <param name="storePath">StateStore path</param>
/// <param name="connStr">StateStore address</param>
/// <returns>Instance of StateStore</returns>
public static StateStore Get(string storePath, string connStr);

/// <summary>
/// Create a new state object in this state store instance
/// </summary>
/// <returns>State from StateStore</returns>
public State Create();

/// <summary>
/// Retrieve all states that were previously uncommitted, excluding all exited states
/// </summary>
/// <returns>Uncommitted states</returns>
public IEnumerable<State> GetUnCommitted();

/// <summary>
/// Get all the states in the StateStore
/// </summary>
/// <returns>All the states</returns>
public IEnumerable<State> States();

/// <summary>
/// Get state or registry object
/// </summary>
/// <param name="info">Registry name (registry only)</param>
/// <typeparam name="T">Type, registry or state</typeparam>
/// <returns>Return registry or state</returns>
public T Get<T>(string info = null);

/// <summary>
/// List all the committed states
/// </summary>
/// <returns>Registries containing the committed state </returns>
public IEnumerable<Registry> Committed();

/// <summary>
/// List all the exited states in the StateStore
/// </summary>
/// <returns>Registries containing the exited states</returns>
public IEnumerable<Registry> Aborted();

/// <summary>
/// Retrieve an existing state object from this state store instance 
/// </summary>
/// <returns>State from StateStore</returns>
/// <typeparam name="T">stateId, id of the State</typeparam>
public State GetState(long stateId)
```

**状态**对象具有以下主要方法：

```csharp
/// <summary>
/// Set the status of the state object to commit
/// </summary>
public void Commit(bool simpleMode = true);

/// <summary>
/// Set the status of the state object to exit
/// </summary>
public void Abort();

/// <summary>
/// Put an attribute value under the given key
/// </summary>
/// <param name="key">Key</param>
/// <param name="attribute">State attribute</param>
    public void PutAttribute<T>(string key, T attribute);

/// <summary>
/// Get the attribute value associated with the given key
/// </summary>
/// <param name="key">Key</param>
/// <returns>State attribute</returns>
    public T GetAttribute<T>(string key);
```

当**simpleMode**设置为**true**时，**提交**方法将删除 ZooKeeper 中相应的 ZNode。 否则，该方法将删除当前 ZNode 并在"已提交路径"\_中添加新节点。

### <a name="scpruntime"></a>SCPRuntime

**SCPRuntime**类提供以下两种方法：

```csharp
public static void Initialize();

public static void LaunchPlugin(newSCPPlugin createDelegate);  
```

**初始化**方法初始化 SCP 运行时环境。 在此方法中，C# 进程连接到 Java 端以获取配置参数和拓扑上下文。

**LaunchPlugin**方法启动消息处理循环。 在此循环中，C# 插件接收来自 Java 端的消息。 这些消息包括元数和控制信号。 然后，插件处理消息，可能通过调用代码提供的接口方法。

**LaunchPlugin**的输入参数是委托。 该方法可以返回实现 ISCPSpout、ISCPBolt、ISCPTxSpout 或**ISCPBatchBolt**接口的对象。 **ISCPSpout** **ISCPBolt** **ISCPTxSpout**

```csharp
public delegate ISCPPlugin newSCPPlugin(Context ctx, Dictionary<string, Object> parms);
```

对于**ISCPBatchBolt，** 可以从*parms*参数获取**StormTxAit**对象，并用它来判断尝试是否是重播的尝试。 重播尝试的检查通常在提交螺栓处完成。 本文后面的 HelloWorldTx 示例演示了此检查。

SCP 插件通常可以在两种模式下运行：本地测试模式和常规模式。

#### <a name="local-test-mode"></a>本地测试模式

在此模式下，C# 代码中的 SCP 插件在开发阶段在 Visual Studio 内运行。 在此模式下可以使用**ILocalContext**接口。 该接口提供了将发出的元数序列化到本地文件并将其读回 RAM 的方法。

```csharp
public interface ILocalContext
{
    List<SCPTuple> RecvFromMsgQueue();
    void WriteMsgQueueToFile(string filepath, bool append = false);  
    void ReadFromFileToMsgQueue(string filepath);
}
```

#### <a name="regular-mode"></a>常规模式

在此模式下，Storm Java 进程运行 SCP 插件。下面是一个示例：

```csharp
namespace Scp.App.HelloWorld
{
public class Generator : ISCPSpout
{
    … …
    public static Generator Get(Context ctx, Dictionary<string, Object> parms)
    {
    return new Generator(ctx);
    }
}

class HelloWorld
{
    static void Main(string[] args)
    {
    /* Setting the environment variable here can change the log file name */
    System.Environment.SetEnvironmentVariable("microsoft.scp.logPrefix", "HelloWorld");

    SCPRuntime.Initialize();
    SCPRuntime.LaunchPlugin(new newSCPPlugin(Generator.Get));
    }
}
}
```

## <a name="topology-specification-language"></a>拓扑规范语言

SCP 拓扑规范是一种特定于域的语言 （DSL），用于描述和配置 SCP 拓扑。 它基于[风暴的Clojure DSL，](https://storm.incubator.apache.org/documentation/Clojure-DSL.html)由SCP扩展。

您可以直接将拓扑规范提交到 Storm 群集，以便通过**runSpec**命令执行。

SCP.NET添加了以下函数来定义事务拓扑：

| 新建函数 | 参数 | 描述 |
| --- | --- | --- |
| **tx-topolopy** |*topology-name*<br />*spout-map*<br />*bolt-map* |使用拓扑名称、样条线定义映射和螺栓定义映射定义事务拓扑。 |
| **scp-tx-spout** |*exec-name*<br />*阿格斯*<br />*字段* |定义事务喷口。 该函数运行由*exec-name*指定的应用程序，并使用*args*。<br /><br />*字段*参数指定出花口的输出字段。 |
| **scp-tx-batch-bolt** |*exec-name*<br />*阿格斯*<br />*字段* |定义事务批处理螺栓。 该函数运行由*exec-name*指定的应用程序并使用*args。*<br /><br />*字段*参数指定螺栓的输出字段。 |
| **scp-tx-commit-bolt** |*exec-name*<br />*阿格斯*<br />*字段* |定义事务提交螺栓。 该函数运行由*exec-name*指定的应用程序，并使用*args*。<br /><br />*字段*参数指定螺栓的输出字段。 |
| **非tx拓扑** |*topology-name*<br />*spout-map*<br />*bolt-map* |使用拓扑名称、喷口定义映射和螺栓定义映射定义非事务性拓扑。 |
| **scp-spout** |*exec-name*<br />*阿格斯*<br />*字段*<br />*参数* |定义非事务性喷口。 该函数运行由*exec-name*指定的应用程序，并使用*args*。<br /><br />*字段*参数指定出花口的输出字段。<br /><br />*参数参数*是可选的。 使用它指定参数，如"启用非事务性.ack"。 |
| **scp-bolt** |*exec-name*<br />*阿格斯*<br />*字段*<br />*参数* |定义非事务性螺栓。 该函数运行由*exec-name*指定的应用程序，并使用*args*。<br /><br />*字段*参数指定螺栓的输出字段<br /><br />*参数参数*是可选的。 使用它指定参数，如"启用非事务性.ack"。 |

SCP.NET定义以下关键字：

| 关键字 | 描述 |
| --- | --- |
| **：名称** |拓扑名称 |
| **：拓扑** |使用上表中的函数和内置函数的拓扑 |
| **:p** |每个出口或螺栓的并行提示 |
| **：配置** |是配置参数还是更新现有参数 |
| **：schema** |流的架构 |

SCP.NET还定义了这些常用参数：

| 参数 | 描述 |
| --- | --- |
| "plugin.name" |C# 插件的 .exe 文件名 |
| "plugin.args" |插件参数 |
| "output.schema" |输出架构 |
| "nontransactional.ack.enabled" |是否为非事务性拓扑启用确认 |

**runSpec**命令与位一起部署。 下面是命令用法：

```csharp
.\bin\runSpec.cmd
usage: runSpec [spec-file target-dir [resource-dir] [-cp classpath]]
ex: runSpec examples\HelloWorld\HelloWorld.spec specs examples\HelloWorld\Target
```

*资源-dir*参数是可选的。 在要插入 C# 应用程序时指定它。 指定的目录包含应用程序、依赖项和配置。

*classpath* 参数也是可选参数。 如果规范文件包含 Java 出点或螺栓，则指定 Java 类路径。

## <a name="miscellaneous-features"></a>其他功能

### <a name="input-and-output-schema-declarations"></a>输入和输出架构声明

您的 C# 进程可以发出 tup。 为此，平台将元数序列化为**字节*** 对象，并将对象传输到 Java 端。 风暴然后将这些元数转移到目标。

在下游组件中，C# 进程从 Java 端接收元组并将其转换为平台的原始类型。 所有这些操作都由平台隐藏。

为了支持序列化和反序列化，代码需要声明输入和输出的架构。 架构定义为字典。 流 ID 是字典密钥。 键值是列的类型。 组件可以声明多个流。

```csharp
public class ComponentStreamSchema
{
    public Dictionary<string, List<Type>> InputStreamSchema { get; set; }
    public Dictionary<string, List<Type>> OutputStreamSchema { get; set; }
    public ComponentStreamSchema(Dictionary<string, List<Type>> input, Dictionary<string, List<Type>> output)
    {
        InputStreamSchema = input;
        OutputStreamSchema = output;
    }
}
```

以下函数将添加到**上下文**对象：

```csharp
public void DeclareComponentSchema(ComponentStreamSchema schema)
```

开发人员必须确保发出的元数符合为流定义的架构。 否则，系统将引发运行时异常。

### <a name="multistream-support"></a>多流支持

SCP 允许您的代码同时向多个不同的流发出或接收。 **上下文**对象将此支持作为**Emit**方法的可选流 ID 参数反映。

添加了SCP.NET**上下文**对象中的两种方法。 它们向特定流发出一个或多个元数。 *streamId*参数是一个字符串。 其值在 C# 代码和拓扑定义规范中必须相同。

```csharp
/* Emit tuple to the specific stream. */
public abstract void Emit(string streamId, List<object> values);

/* for nontransactional spout only */
public abstract void Emit(string streamId, List<object> values, long seqId);
```

向不存在的流发出会导致运行时异常。

### <a name="fields-grouping"></a>字段分组

Storm 中的内置字段分组在SCP.NET中无法正常工作。 在 Java 代理端，所有字段的数据类型实际上是**字节***。 字段分组使用**字节*** 对象的哈希代码执行分组。 哈希代码是 RAM 中此对象的地址。 因此，对于共享相同内容但不相同地址的多字节对象的分组是错误的。

SCP.NET添加自定义分组方法，它使用**字节*** 对象的内容执行分组。 在规范文件中，语法如下所示：

```csharp
(bolt-spec
    {
        "spout_test" (scp-field-group :non-tx [0,1])
    }
    …
)
```

在前面的规范文件中：

* `scp-field-group`指定分组是由 SCP 实现的自定义字段分组。
* `:tx`或`:non-tx`指定拓扑是事务性的。 您需要此信息，因为起始索引在事务拓扑和非事务拓扑之间是不同的。
* `[0,1]`指定以零开头的哈希字段指示集。

### <a name="hybrid-topology"></a>混合拓扑

本机风暴代码用 Java 编写。 SCP.NET增强了 Storm，允许您编写 C# 代码来处理业务逻辑。 但是SCP.NET也支持混合拓扑，它不仅包含 C# 喷口/螺栓，还包含 Java 喷口/螺栓。

### <a name="specify-java-spoutbolt-in-a-specification-file"></a>在规范文件中指定 Java 出点/螺栓

您可以在规范文件中使用**scp-spout**和**scp-bolt**来指定 Java 样嘴和螺栓。 下面是一个示例：

```csharp
(spout-spec 
  (microsoft.scp.example.HybridTopology.Generator.)
  :p 1)
```

下面是`microsoft.scp.example.HybridTopology.Generator`Java 出口类的名称。

### <a name="specify-the-java-classpath-in-a-runspec-command"></a>在 runSpec 命令中指定 Java 类路径

如果要提交包含 Java 出口或螺栓的拓扑，首先编译它们以生成 JAR 文件。 然后指定提交拓扑时包含 JAR 文件的 java 类路径。 下面是一个示例：

```csharp
bin\runSpec.cmd examples\HybridTopology\HybridTopology.spec specs examples\HybridTopology\net\Target -cp examples\HybridTopology\java\target\*
```

`examples\HybridTopology\java\target\`此处是包含 Java 出样/螺栓 JAR 文件的文件夹。

### <a name="serialization-and-deserialization-between-java-and-c"></a>Java 和 C# 之间的序列化和反序列化

SCP 组件包括 Java 端和 C# 端。 要与本机 Java 语音/螺栓进行交互，必须在 Java 端和 C# 端之间进行序列化和反序列化，如下图所示：

![将 Java 组件发送到 SCP 组件的关系图，然后发送到其他 Java 组件](./media/apache-storm-scp-programming-guide/java-compent-sending-to-scp-component-sending-to-java-component.png)

#### <a name="serialization-in-the-java-side-and-deserialization-in-the-c-side"></a>Java 端的序列化和 C# 端的序列化

首先提供 Java 端序列化和 C# 端反序列化的默认实现。

在规范文件中指定 Java 端的序列化方法。

```csharp
(scp-bolt
    {
        "plugin.name" "HybridTopology.exe"
        "plugin.args" ["displayer"]
        "output.schema" {}
        "customized.java.serializer" ["microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer"]
    })
```

在 C# 代码中指定 C# 端的反序列化方法。

```csharp
Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
inputSchema.Add("default", new List<Type>() { typeof(Person) });
this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, null));
this.ctx.DeclareCustomizedDeserializer(new CustomizedInteropJSONDeserializer());
```  

如果数据类型不太复杂，则此默认实现应处理大多数情况。 以下是您可以插入自己的实现的情况：

* 对于默认实现来说，数据类型太复杂了。
* 默认实现的性能不符合您的要求。

Java 端的序列化接口定义为：

```csharp
public interface ICustomizedInteropJavaSerializer {
    public void prepare(String[] args);
    public List<ByteBuffer> serialize(List<Object> objectList);
}
```

C# 端中的反序列化接口定义为：

```csharp
public interface ICustomizedInteropCSharpDeserializer
{
    List<Object> Deserialize(List<byte[]> dataList, List<Type> targetTypes);
}
```

#### <a name="serialization-in-the-c-side-and-deserialization-in-the-java-side"></a>C# 端的序列化和 Java 端的反序列化

在 C# 代码中指定 C# 端的序列化方法。

```csharp
this.ctx.DeclareCustomizedSerializer(new CustomizedInteropJSONSerializer()); 
```

在规范文件中指定 Java 端的反序列化方法。

```csharp
(scp-spout
   {
     "plugin.name" "HybridTopology.exe"
     "plugin.args" ["generator"]
     "output.schema" {"default" ["person"]}
     "customized.java.deserializer" ["microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer" "microsoft.scp.example.HybridTopology.Person"]
   }
)
```

此处`"microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer"`是反序列化器的名称，是`"microsoft.scp.example.HybridTopology.Person"`数据反序列化的目标类。

您还可以插入自己的 C# 序列化器和 Java 反序列化器的实现。

此代码是 C# 序列化器的接口：

```csharp
public interface ICustomizedInteropCSharpSerializer
{
    List<byte[]> Serialize(List<object> dataList);
}
```

此代码是 Java 反序列化器的接口：

```csharp
public interface ICustomizedInteropJavaDeserializer {
    public void prepare(String[] targetClassNames);
    public List<Object> Deserialize(List<ByteBuffer> dataList);
}
```

## <a name="scp-host-mode"></a>SCP 主机模式

在此模式下，您可以将代码编译为 DLL，并使用 SCP 提供的 SCPHost.exe 提交拓扑。 规范文件如下所示：

```csharp
(scp-spout
  {
    "plugin.name" "SCPHost.exe"
    "plugin.args" ["HelloWorld.dll" "Scp.App.HelloWorld.Generator" "Get"]
    "output.schema" {"default" ["sentence"]}
  })
```

此处`"plugin.name"`，指定为`"SCPHost.exe"`SCP SDK 提供的 。 SCPHost.exe 接受以下顺序的三个参数：

1. DLL 名称，`"HelloWorld.dll"`在此示例中为。
1. 类名称，`"Scp.App.HelloWorld.Generator"`在此示例中。
1. 公共静态方法的名称，可以调用它来获取**ISCPPlugin**的实例。

在主机模式下，将代码编译为 SCP 平台调用的 DLL。 由于平台可以完全控制整个处理逻辑，因此我们建议您在 SCP 主机模式下提交拓扑。 这样做简化了开发体验。 它还为您带来更大的灵活性和更好的向后兼容性，适用于以后的版本。

## <a name="scp-programming-examples"></a>SCP 编程示例

### <a name="helloworld"></a>HelloWorld

下面的简单 HelloWorld 示例显示了SCP.NET的味道。 它使用非事务性拓扑，其喷口称为**生成器**，两个螺栓称为**拆分器**和**计数器**。 **生成器**喷口随机生成句子，并将这些句子发出给**拆分器**。 **分体**螺栓将句子分成单词，并将这些单词发出到**计数器**螺栓上。 **计数器**螺栓使用字典记录每个单词的匹配项。

此示例有两个规范文件：HelloWorld.spec 和\_HelloWorld 启用Ack.spec。C# 代码可以通过从 Java 端获取`pluginConf`对象来了解是否启用了确认。

```csharp
/* demo how to get pluginConf info */
if (Context.Config.pluginConf.ContainsKey(Constants.NONTRANSACTIONAL_ENABLE_ACK))
{
    enableAck = (bool)(Context.Config.pluginConf[Constants.NONTRANSACTIONAL_ENABLE_ACK]);
}
Context.Logger.Info("enableAck: {0}", enableAck);
```

如果在 spout 中启用了确认，则字典将缓存尚未确认的元数。 如果`Fail`调用，则重播失败元组。

```csharp
public void Fail(long seqId, Dictionary<string, Object> parms)
{
    Context.Logger.Info("Fail, seqId: {0}", seqId);
    if (cachedTuples.ContainsKey(seqId))
    {
        /* get the cached tuple */
        string sentence = cachedTuples[seqId];

        /* replay the failed tuple */
        Context.Logger.Info("Re-Emit: {0}, seqId: {1}", sentence, seqId);
        this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new Values(sentence), seqId);
    }
    else
    {
        Context.Logger.Warn("Fail(), can't find cached tuple for seqId {0}!", seqId);
    }
}
```

### <a name="helloworldtx"></a>HelloWorldTx

下面的 HelloWorldTx 示例演示如何实现事务拓扑。 该示例有一个称为**生成器**的喷口，一个称为**部分计数的**批处理螺栓，以及一个称为**计数和的**提交螺栓。 该示例还有三个现有的文本文件：DataSource0.txt、DataSource1.txt 和 DataSource2.txt。

在每个事务中，**生成器**喷口从现有三个文件中随机选择两个文件，并将两个文件名发射到**部分计数**螺栓。 **部分计数**螺栓：

1. 从接收的元组获取文件名。
1. 打开相应的文件。
1. 计算文件中的单词数。
1. 将单词计数发出到**计数和**螺栓。

count-sum Bolt 对总计数进行汇总****。

要精确实现一次语义，**计数和**提交螺栓需要判断它是否是重播事务。 在此示例中，它有以下静态成员变量：

```csharp
public static long lastCommittedTxId = -1; 
```

创建**ISCPBatchBolt**实例时，它将从输入参数中获取`txAttempt`对象的值。

```csharp
public static CountSum Get(Context ctx, Dictionary<string, Object> parms)
{
    /* for transactional topology, we can get txAttempt from the input parms */
    if (parms.ContainsKey(Constants.STORM_TX_ATTEMPT))
    {
        StormTxAttempt txAttempt = (StormTxAttempt)parms[Constants.STORM_TX_ATTEMPT];
        return new CountSum(ctx, txAttempt);
    }
    else
    {
        throw new Exception("null txAttempt");
    }
}
```

调用`FinishBatch`时，`lastCommittedTxId`如果它不是重播的事务，则更新该事务。

```csharp
public void FinishBatch(Dictionary<string, Object> parms)
{
    /* judge whether it is a replayed transaction */
    bool replay = (this.txAttempt.TxId <= lastCommittedTxId);

    if (!replay)
    {
        /* If it is not replayed, update the totalCount and lastCommittedTxId value */
        totalCount = totalCount + this.count;
        lastCommittedTxId = this.txAttempt.TxId;
    }
    … …
}
```

### <a name="hybridtopology"></a>HybridTopology

此拓扑包含 Java 喷口和 C# 螺栓。 它使用 SCP 平台提供的默认序列化和反序列化实现。 有关规范文件的详细信息，请参阅示例\\"混合拓扑.spec"中的文件混合拓扑。 有关如何指定 Java 类路径，请参阅提交拓扑.bat。

### <a name="scphostdemo"></a>SCPHostDemo

此示例本质上与 HelloWorld 相同。 唯一的区别是，您的代码编译为 DLL，拓扑是使用 SCPHost.exe 提交的。 有关更详细的说明，请参阅 SCP 主机模式部分。

## <a name="next-steps"></a>后续步骤

有关使用 SCP 创建的 Apache Storm 拓扑的示例，请参阅以下文章：

* [使用 Visual Studio 开发 Apache Storm on HDInsight 的 C# 拓扑](apache-storm-develop-csharp-visual-studio-topology.md)
* [使用 Apache Storm on HDInsight 从 Azure 事件中心处理事件](apache-storm-develop-csharp-event-hub-topology.md)
* [使用 Apache Storm on HDInsight 处理事件中心的车辆传感器数据](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/IotExample)
* [从 Azure 事件中心提取、转换和加载 （ETL） 到 Apache HBase](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample)
