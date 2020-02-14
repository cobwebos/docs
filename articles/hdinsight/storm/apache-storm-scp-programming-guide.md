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
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2020
ms.locfileid: "77186868"
---
# <a name="scp-programming-guide-for-apache-storm-in-azure-hdinsight"></a>Azure HDInsight 中 Apache Storm 的 SCP 编程指南

SCP 是一个用于构建实时、可靠、一致和高性能的数据处理应用程序的平台。 它在[Apache Storm](https://storm.incubator.apache.org/)的基础上构建，后者是由开源软件社区设计的流处理系统。 Nathan Marz 创建了风暴。 它已由 Twitter 作为开放源代码发布。 风暴使用[Apache ZooKeeper](https://zookeeper.apache.org/)，这是启用高度可靠的分布式协调和状态管理的另一个 Apache 项目。

SCP 项目不仅在 Windows 上移植了风暴，还在 Windows 环境中具有项目添加的扩展和自定义功能。 扩展包括 .NET 开发人员体验和 .NET 库。 自定义包括基于 Windows 的部署。

通过扩展和自定义，你无需分叉开源软件项目。 可以使用在风暴上构建的派生环境。

## <a name="processing-model"></a>处理模型

SCP 中的数据以连续的元组流形式建模。 通常，元组：

1. 流入队列。
1. 由在风暴拓扑中托管的业务逻辑选取和转换。
1. 将其输出管道传递给另一个 SCP 系统，或者提交到存储（如 SQL Server 的分布式文件系统和数据库）。

![队列，用于向要处理的数据馈送，并将数据馈送到数据存储中](./media/apache-storm-scp-programming-guide/queue-feeding-data-to-processing-to-data-store.png)

在风暴中，应用程序拓扑定义计算图。 拓扑中的每个节点都包含处理逻辑。 节点之间的链接指示数据流。

将输入数据注入到拓扑中的节点称为_spout_。 您可以使用它们来对数据进行排序。 输入数据可能来自文件日志、事务性数据库或系统性能计数器等源。

具有输入和输出数据流的节点称为_螺栓_。 它们执行实际的数据筛选、选择和聚合。

SCP 支持最大努力、至少一次和一次数据处理。

在分布式流处理应用程序中，数据处理过程中可能会发生错误。 此类错误包括网络故障、计算机故障或代码错误。 "至少一次" 处理可确保在发生错误时自动重播相同的数据，从而至少处理一次所有的数据。

"至少一次" 处理简单且可靠，并且适用于许多应用程序。 但当应用程序需要精确计数时，一次至少一次处理就会不足，因为在应用程序拓扑中可能会重播相同的数据。 在这种情况下，只需进行一次处理，就可以确保即使在多次重播并处理数据时，结果也是正确的。

通过 SCP，.NET 开发人员可以在使用带有风暴的 Java 虚拟机（JVM）时创建实时数据处理应用程序。 JVM 和 .NET 通过 TCP 本地套接字进行通信。 每个 spout/螺栓都是 .NET/Java 进程对，其中用户逻辑作为插件在 .NET 进程中运行。

若要在 SCP 上构建数据处理应用程序，请执行以下步骤：

1. 设计并实施 spout，以从队列中提取数据。
1. 设计和实现用于处理输入数据并将其保存到外部存储（如数据库）的螺栓。
1. 设计拓扑，并提交并运行它。

拓扑定义顶点以及在它们之间流动的数据。 SCP 采用拓扑规范，并将其部署在风暴群集上，其中每个顶点在一个逻辑节点上运行。 风暴任务计划程序负责故障转移和缩放。

本文使用一些简单的示例来演练如何使用 SCP 构建数据处理应用程序。

## <a name="scp-plug-in-interface"></a>SCP 插件接口

SCP 插件是独立的应用程序。 它们可以在开发过程中在 Visual Studio 内部运行，并在生产部署后插入到风暴管道。

编写 SCP 插件就像编写任何其他 Windows 控制台应用程序一样。 SCP.NET 平台为 spout/螺栓声明了某些接口。 插件代码实现这些接口。 这种设计的主要目的是让你将精力集中在业务逻辑上，同时让 SCP.NET 平台处理其他东西。

插件代码实现以下接口之一。 哪个接口取决于拓扑是事务性还是非事务性，以及组件是 spout 还是螺栓。

* **ISCPSpout**
* **ISCPBolt**
* **ISCPTxSpout**
* **ISCPBatchBolt**

### <a name="iscpplugin"></a>ISCPPlugin

**ISCPPlugin**是许多插件的通用接口。目前，它是一个虚拟接口。

```csharp
public interface ISCPPlugin
{
}
```

### <a name="iscpspout"></a>ISCPSpout

**ISCPSpout**是用于非事务性 spout 的接口。

```csharp
public interface ISCPSpout : ISCPPlugin
{
    void NextTuple(Dictionary<string, Object> parms);
    void Ack(long seqId, Dictionary<string, Object> parms); 
    void Fail(long seqId, Dictionary<string, Object> parms);
}
```

调用**NextTuple**时，你的C#代码可能会发出一个或多个元组。 如果没有要发出的内容，则此方法应返回而不发出任何内容。

**NextTuple**、 **Ack**和**Fail**方法都是在C#进程的单个线程中严格循环中调用的。 如果没有要发出的元组，请使用**NextTuple**休眠一小段时间，如10毫秒。 此睡眠有助于避免浪费 CPU 可用性。

仅当规范文件启用确认机制时，才会调用**确认**和**失败**方法。 *SeqId*参数标识已确认或已失败的元组。 如果在非事务性拓扑中启用了确认，则应在 spout 中使用以下**发出**函数：

```csharp
public abstract void Emit(string streamId, List<object> values, long seqId);
```

如果非事务性拓扑不支持确认，则可将**确认**和**失败**保留为空函数。

这些函数中的*parms*输入参数指定一个空字典，保留供将来使用。

### <a name="iscpbolt"></a>ISCPBolt

**ISCPBolt**是非事务性螺栓的接口。

```csharp
public interface ISCPBolt : ISCPPlugin
{
void Execute(SCPTuple tuple);
}
```

新的元组可用时，将调用**Execute**函数来处理该元组。

### <a name="iscptxspout"></a>ISCPTxSpout

**ISCPTxSpout**是事务性 spout 的接口。

```csharp
public interface ISCPTxSpout : ISCPPlugin
{
    void NextTx(out long seqId, Dictionary<string, Object> parms);  
    void Ack(long seqId, Dictionary<string, Object> parms);         
    void Fail(long seqId, Dictionary<string, Object> parms);        
}
```

与非事务性对应项一样， **NextTx**、 **Ack**和**Fail**都是在C#进程的单个线程中严格循环中调用的。 如果没有要发出的元组，请使用**NextTx**休眠一小段时间，如10毫秒。 此睡眠有助于避免浪费 CPU 可用性。

当调用**NextTx**来启动一个新事务时， *seqId* output 参数会标识该事务。 该事务还用于**Ack** ，并**失败**。 **NextTx**方法可将数据发送到 Java 端。 然后，数据会被存储在 ZooKeeper 中，以支持重用。 由于 ZooKeeper 的容量有限，因此，你的代码只应发送元数据，而不会在事务性 spout 中发出大容量数据。

由于风暴会自动重播失败的事务，因此通常不会调用**失败**。 但是，如果 SCP 可以检查事务性 spout 发出的元数据，则在元数据无效时，它可以调用**Fail** 。

这些函数中的*parms*输入参数指定一个空字典，保留供将来使用。

### <a name="iscpbatchbolt"></a>ISCPBatchBolt

**ISCPBatchBolt**是事务螺栓的接口。

```csharp
public interface ISCPBatchBolt : ISCPPlugin
{
    void Execute(SCPTuple tuple);
    void FinishBatch(Dictionary<string, Object> parms);  
}
```

当新的元组到达螺栓时，将调用**Execute**方法。 此事务结束时，将调用**FinishBatch**方法。 *Parms*输入参数保留供将来使用。

对于事务拓扑， **StormTxAttempt**是一个重要的类。 它有两个成员： **TxId**和**AttemptId**。 **TxId**成员标识特定事务。 如果事务失败并被重播，则可能会尝试多次。

SCP.NET 会创建一个新的**ISCPBatchBolt**对象来处理每个**StormTxAttempt**对象，就像它在 Java 中所执行的操作一样。 此设计的目的是支持并行事务处理。 事务尝试完成后，会销毁相应的**ISCPBatchBolt**对象并对其进行垃圾回收。

## <a name="object-model"></a>对象模型

SCP.NET 还会提供可供开发人员用于编程的简单密钥对象集。 这些对象是**Context**、 **StateStore**和**SCPRuntime**。 本部分将对此进行讨论。

### <a name="context"></a>上下文

**上下文**对象为应用程序提供运行环境。 **ISCPSpout**、 **ISCPBolt**、 **ISCPTxSpout**或**ISCPBatchBolt**的每个**ISCPPlugin**实例都有相应的**上下文**实例。 **上下文**提供的功能分为以下两部分：

* 静态部分，可用于整个C#进程
* 动态部分，仅可用于特定**上下文**实例

### <a name="static-part"></a>静态部分

```csharp
public static ILogger Logger = null;
public static SCPPluginType pluginType;
public static Config Config { get; set; }
public static TopologyContext TopologyContext { get; set; }  
```

提供日志记录的**记录器**对象。

**PluginType**对象指示C#进程的插件类型。 如果进程在本地测试模式下运行（没有 Java），则插件类型是**SCP_NET_LOCAL**。

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

**Config**属性从 Java 端获取配置参数，并在初始化C#插件时传递这些参数。 **Config**参数分为两部分： **stormConf**和**pluginconf 来确定**。

```csharp
public Dictionary<string, Object> stormConf { get; set; }  
public Dictionary<string, Object> pluginConf { get; set; }  
```

**StormConf**部分是由风暴定义的参数，而**PLUGINCONF 来确定**部分是 SCP 定义的参数。 下面是一个示例：

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

**TopologyContext**类型获取拓扑上下文。 它最适用于多个并行组件。 下面是一个示例：

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

以下接口与某个**上下文**实例相关，该实例由 SCP.NET 平台创建并传递给你的代码：

```csharp
// Declare the Output and Input Stream Schemas

public void DeclareComponentSchema(ComponentStreamSchema schema);

// Emit tuple to default stream.
public abstract void Emit(List<object> values);

// Emit tuple to the specific stream.
public abstract void Emit(string streamId, List<object> values);  
```

对于支持确认的非事务性 spout，提供了以下方法：

```csharp
// for nontransactional spout that supports ack
public abstract void Emit(string streamId, List<object> values, long seqId);  
```

支持确认的非事务性螺栓应使用收到的元组显式调用**确认**或**失败**。 发出新的元组时，螺栓还必须指定元组的定位点。 提供了以下方法：

```csharp
public abstract void Emit(string streamId, IEnumerable<SCPTuple> anchors, List<object> values);
public abstract void Ack(SCPTuple tuple);
public abstract void Fail(SCPTuple tuple);
```

### <a name="statestore"></a>StateStore

**StateStore**对象提供元数据服务、单调序列生成和无等待协调。 可以在**StateStore**上生成较高级别的分布式并发抽象。 这些抽象包括分布式锁、分布式队列、屏障和事务服务。

SCP 应用程序可以使用**状态**对象在[Apache ZooKeeper](https://zookeeper.apache.org/)中序列化信息。 此功能对于事务拓扑特别有用。 如果事务性 spout 停止响应并重启，则**状态**可从 ZooKeeper 检索必要信息并重新启动管道。

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

当**simpleMode**设置为**True**时， **Commit**方法会删除 ZooKeeper 中相应的 ZNode。 否则，该方法将删除当前 ZNode，并在提交的\_路径中添加一个新节点。

### <a name="scpruntime"></a>SCPRuntime

**SCPRuntime**类提供了以下两种方法：

```csharp
public static void Initialize();

public static void LaunchPlugin(newSCPPlugin createDelegate);  
```

**Initialize**方法用于初始化 SCP 运行时环境。 在此方法中， C#进程连接到 Java 端以获取配置参数和拓扑上下文。

**LaunchPlugin**方法启动消息处理循环。 在此循环中， C#该插件接收来自 Java 端的消息。 这些消息包括元组和控制信号。 然后，该插件会通过调用代码提供的接口方法来处理这些消息。

**LaunchPlugin**的输入参数为委托。 方法可以返回实现**ISCPSpout**、 **ISCPBolt**、 **ISCPTxSpout**或**ISCPBatchBolt**接口的对象。

```csharp
public delegate ISCPPlugin newSCPPlugin(Context ctx, Dictionary<string, Object> parms);
```

对于**ISCPBatchBolt**，你可以从*Parms*参数获取**StormTxAttempt**对象，并使用它来判断尝试是否是重播尝试。 检查尝试是否经常在提交螺栓上完成。 本文后面的 HelloWorldTx 示例将演示此项检查。

SCP 插件通常可在两种模式下运行：本地测试模式和常规模式。

#### <a name="local-test-mode"></a>本地测试模式

在此模式下， C#代码中的 SCP 插件在开发阶段在 Visual Studio 中运行。 可以在此模式下使用**ILocalContext**接口。 接口提供将发出的元组序列化为本地文件并将其重新读入 RAM 的方法。

```csharp
public interface ILocalContext
{
    List<SCPTuple> RecvFromMsgQueue();
    void WriteMsgQueueToFile(string filepath, bool append = false);  
    void ReadFromFileToMsgQueue(string filepath);
}
```

#### <a name="regular-mode"></a>常规模式

在此模式下，风暴 Java 进程将运行 SCP 插件。下面是一个示例：

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

SCP 拓扑规范是一种特定于域的语言（DSL），用于描述和配置 SCP 拓扑。 它基于[风暴的 CLOJURE DSL](https://storm.incubator.apache.org/documentation/Clojure-DSL.html) ，并由 SCP 扩展。

可以通过**runSpec**命令将拓扑规范直接提交给风暴群集，以执行该操作。

SCP.NET 添加了以下函数来定义事务性拓扑：

| 新建函数 | 参数 | 说明 |
| --- | --- | --- |
| **tx-topolopy** |*拓扑-名称*<br />*spout-映射*<br />*螺栓-地图* |使用拓扑名称、spout 定义图和螺栓定义地图定义事务拓扑。 |
| **scp-tx-spout** |*exec-名称*<br />*args*<br />*fields* |定义事务性 spout。 函数运行按*exec 名称*指定的应用程序，并使用*参数*。<br /><br />*Fields*参数指定 spout 的输出字段。 |
| **scp-tx-batch-bolt** |*exec-名称*<br />*args*<br />*fields* |定义事务性批处理螺栓。 函数运行按*exec 名称*指定的应用程序，并使用*参数。*<br /><br />*Fields*参数指定螺栓的输出字段。 |
| **scp-tx-commit-bolt** |*exec-名称*<br />*args*<br />*fields* |定义事务提交螺栓。 函数运行按*exec 名称*指定的应用程序，并使用*参数*。<br /><br />*Fields*参数指定螺栓的输出字段。 |
| **nontx-拓扑** |*拓扑-名称*<br />*spout-映射*<br />*螺栓-地图* |使用拓扑名称、spout 定义图和螺栓定义地图定义非事务性拓扑。 |
| **scp-spout** |*exec-名称*<br />*args*<br />*fields*<br />*参数* |定义非事务性 spout。 函数运行按*exec 名称*指定的应用程序，并使用*参数*。<br /><br />*Fields*参数指定 spout 的输出字段。<br /><br />*Parameters*参数是可选的。 使用它来指定参数，如 "已启用的"。 |
| **scp-bolt** |*exec-名称*<br />*args*<br />*fields*<br />*参数* |定义非事务性螺栓。 函数运行按*exec 名称*指定的应用程序，并使用*参数*。<br /><br />*Fields*参数指定螺栓的输出字段<br /><br />*Parameters*参数是可选的。 使用它来指定参数，如 "已启用的"。 |

SCP.NET 定义以下关键字：

| 关键字 | 说明 |
| --- | --- |
| **:name** |拓扑名称 |
| **:topology** |使用上表中的函数和内置函数的拓扑 |
| **:p** |每个 spout 或螺栓的并行度提示 |
| **:config** |是配置参数还是更新现有参数 |
| **:schema** |流的架构 |

SCP.NET 还定义了这些常用参数：

| 参数 | 说明 |
| --- | --- |
| "plugin.name" |C#插件的 .exe 文件名 |
| "插件" |插件参数 |
| "output. schema" |输出架构 |
| "已启用" |是否为非事务性拓扑启用确认 |

**RunSpec**命令与 bits 一起部署。 命令用法如下：

```csharp
.\bin\runSpec.cmd
usage: runSpec [spec-file target-dir [resource-dir] [-cp classpath]]
ex: runSpec examples\HelloWorld\HelloWorld.spec specs examples\HelloWorld\Target
```

*Resource-dir*参数是可选的。 如果要插入C#应用程序，请指定此项。 指定的目录包含应用程序、依赖项和配置。

*classpath* 参数也是可选参数。 如果规范文件包含 Java spout 或螺栓，它将指定 Java 类路径。

## <a name="miscellaneous-features"></a>其他功能

### <a name="input-and-output-schema-declarations"></a>输入和输出架构声明

C#进程可以发出元组。 为此，平台会将元组序列化为**byte []** 对象，并将对象传输到 Java 端。 然后，风暴将这些元组传输到目标。

在下游组件中C# ，进程从 Java 端接收元组，并将其转换为平台的原始类型。 所有这些操作都被平台隐藏。

若要支持序列化和反序列化，你的代码需要声明输入和输出的架构。 架构定义为字典。 流 ID 是字典键。 键值是列的类型。 组件可以声明多个流。

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

将以下函数添加到**上下文**对象：

```csharp
public void DeclareComponentSchema(ComponentStreamSchema schema)
```

开发人员必须确保发出的元组遵循为流定义的架构。 否则，系统会引发运行时异常。

### <a name="multistream-support"></a>Multistream 支持

SCP 允许你的代码同时在多个不同的流中发出或接收。 **上下文**对象将此支持反映为**发出**方法的可选流 ID 参数。

在 SCP.NET**上下文**对象中添加了两个方法。 它们将一个或多个元组发送到特定流。 *StreamId*参数是一个字符串。 在C#代码和拓扑定义规范中，其值必须相同。

```csharp
/* Emit tuple to the specific stream. */
public abstract void Emit(string streamId, List<object> values);

/* for nontransactional spout only */
public abstract void Emit(string streamId, List<object> values, long seqId);
```

发出到不存在的流将导致运行时异常。

### <a name="fields-grouping"></a>字段分组

SCP.NET 中的内置字段分组不能正常工作。 在 Java 代理端，所有字段的数据类型实际上都是**byte []** 。 字段分组使用**byte []** 对象的哈希代码进行分组。 哈希代码是该对象在内存中的地址。 因此，对于共享相同内容但地址不相同的多字节对象，分组将会出错。

SCP.NET 添加了一个自定义的分组方法，并使用**byte []** 对象的内容进行分组。 在规范文件中，语法如下例所示：

```csharp
(bolt-spec
    {
        "spout_test" (scp-field-group :non-tx [0,1])
    }
    …
)
```

在上述规范文件中：

* `scp-field-group` 指定分组是 SCP 实现的自定义字段分组。
* `:tx` 或 `:non-tx` 指定拓扑是否为事务性的。 你需要此信息，因为起始索引在事务性和非事务性拓扑之间是不同的。
* `[0,1]` 指定以零开头的字段 Id 的哈希集。

### <a name="hybrid-topology"></a>混合拓扑

本机风暴代码以 Java 编写。 SCP.NET 增强了风暴，使你能够C#编写代码来处理业务逻辑。 但 SCP.NET 还支持混合拓扑，这种拓扑不仅C#包含 spout/螺栓，还包含 Java spout/螺栓。

### <a name="specify-java-spoutbolt-in-a-specification-file"></a>在规范文件中指定 Java spout/螺栓

可以在规范文件中使用**spout**和**Scp**来指定 Java spout 和螺栓。 下面是一个示例：

```csharp
(spout-spec 
  (microsoft.scp.example.HybridTopology.Generator.)
  :p 1)
```

此处 `microsoft.scp.example.HybridTopology.Generator` 是 Java spout 类的名称。

### <a name="specify-the-java-classpath-in-a-runspec-command"></a>在 runSpec 命令中指定 Java 类路径

如果要提交包含 Java spout 或螺栓的拓扑，请先将其编译为生成 JAR 文件。 然后，在提交拓扑时指定包含 JAR 文件的 java 类路径。 下面是一个示例：

```csharp
bin\runSpec.cmd examples\HybridTopology\HybridTopology.spec specs examples\HybridTopology\net\Target -cp examples\HybridTopology\java\target\*
```

此处 `examples\HybridTopology\java\target\` 是包含 Java spout/螺栓 JAR 文件的文件夹。

### <a name="serialization-and-deserialization-between-java-and-c"></a>Java 和 C# 之间的序列化和反序列化

SCP 组件包括 Java 端和C#端。 若要与本机 Java spout/螺栓交互，序列化和反序列化必须在 Java 端C#和端之间发生，如下图所示：

![Java 组件的关系图，发送到 SCP 组件，然后将其发送到不同的 Java 组件](./media/apache-storm-scp-programming-guide/java-compent-sending-to-scp-component-sending-to-java-component.png)

#### <a name="serialization-in-the-java-side-and-deserialization-in-the-c-side"></a>Java 端的序列化和C#端反序列化

首先提供 Java 端的序列化的默认实现，并在一C#侧进行反序列化。

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

在C#代码C#中指定侧的反序列化方法。

```csharp
Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
inputSchema.Add("default", new List<Type>() { typeof(Person) });
this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, null));
this.ctx.DeclareCustomizedDeserializer(new CustomizedInteropJSONDeserializer());
```  

如果数据类型不太复杂，则此默认实现应处理大多数情况。 下面是可以插入自己的实现的情况：

* 对于默认实现，数据类型太复杂。
* 默认实现的性能不符合要求。

Java 端的序列化接口定义为：

```csharp
public interface ICustomizedInteropJavaSerializer {
    public void prepare(String[] args);
    public List<ByteBuffer> serialize(List<Object> objectList);
}
```

C#端中的反序列化接口定义为：

```csharp
public interface ICustomizedInteropCSharpDeserializer
{
    List<Object> Deserialize(List<byte[]> dataList, List<Type> targetTypes);
}
```

#### <a name="serialization-in-the-c-side-and-deserialization-in-the-java-side"></a>端中的C#序列化和 Java 端的反序列化

在C#代码C#中指定端的序列化方法。

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

此处，`"microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer"` 是反序列化程序的名称，`"microsoft.scp.example.HybridTopology.Person"` 是数据反序列化到的目标类。

还可以插入自己的C#序列化程序实现和 Java 反序列化程序。

此代码是C#序列化程序的接口：

```csharp
public interface ICustomizedInteropCSharpSerializer
{
    List<byte[]> Serialize(List<object> dataList);
}
```

此代码是 Java 反序列化程序的接口：

```csharp
public interface ICustomizedInteropJavaDeserializer {
    public void prepare(String[] targetClassNames);
    public List<Object> Deserialize(List<ByteBuffer> dataList);
}
```

## <a name="scp-host-mode"></a>SCP 主机模式

在此模式下，可以将代码编译为 DLL，并使用 SCP 提供的 Scphost.exe 来提交拓扑。 规范文件如下所示：

```csharp
(scp-spout
  {
    "plugin.name" "SCPHost.exe"
    "plugin.args" ["HelloWorld.dll" "Scp.App.HelloWorld.Generator" "Get"]
    "output.schema" {"default" ["sentence"]}
  })
```

此处，`"plugin.name"` 指定为 `"SCPHost.exe"`，这是 SCP SDK 提供的。 Scphost.exe 按以下顺序接受三个参数：

1. 在此示例中 `"HelloWorld.dll"` 的 DLL 名称。
1. 类名，在此示例中 `"Scp.App.HelloWorld.Generator"`。
1. 公共静态方法的名称，可调用此方法获取**ISCPPlugin**的实例。

在 "主机" 模式下，将代码编译为 DLL，以供 SCP 平台调用。 由于平台可以对整个处理逻辑进行完全控制，因此，我们建议你在 SCP 主机模式下提交拓扑。 这样做可以简化开发体验。 它还使您能够更灵活、更好地向后兼容。

## <a name="scp-programming-examples"></a>SCP 编程示例

### <a name="helloworld"></a>HelloWorld

以下简单的 HelloWorld 示例展示了 SCP.NET 的感受。 它使用名为 spout 的非事务性拓扑**和两个称为** **拆分器**和**计数器**的螺栓。 **生成器**spout 随机生成句子并将这些句子发出到**拆分器**。 **拆分器**螺栓会将句子拆分为单词，并将这些单词发出到**计数器**螺栓。 **计数器**螺栓使用字典记录每个词的匹配项。

此示例包含两个规范文件： HelloWorld 和 HelloWorld\_Enableack.spec。C#代码可以通过从 Java 端获取 `pluginConf` 对象来确定是否已启用确认。

```csharp
/* demo how to get pluginConf info */
if (Context.Config.pluginConf.ContainsKey(Constants.NONTRANSACTIONAL_ENABLE_ACK))
{
    enableAck = (bool)(Context.Config.pluginConf[Constants.NONTRANSACTIONAL_ENABLE_ACK]);
}
Context.Logger.Info("enableAck: {0}", enableAck);
```

如果在 spout 中启用了确认，则字典将缓存尚未确认的元组。 如果调用 `Fail`，则会重播失败的元组。

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

下面的 HelloWorldTx 示例演示如何实现事务性拓扑。 该示例有一个名为 "**生成器**" 的 spout、一个名为 "**部分计数**" 的批处理螺栓和一个名为**count sum**的提交螺栓。 该示例还包含三个现有的文本文件： Datasource0.txt、datasource1.txt、DataSource1 和 DataSource2。

在每个事务中，**生成器**都从现有的三个文件中随机选择两个文件，并向**部分计数**螺栓发出两个文件名。 **部分计数**螺栓：

1. 获取收到的元组中的文件名。
1. 打开相应的文件。
1. 计算文件中的单词数。
1. 将单词计数发出到**计数总和**螺栓。

count-sum Bolt 对总计数进行汇总。

若要仅实现一次语义，则**计数总和**提交螺栓需要判断它是否是重播事务。 在此示例中，它具有以下静态成员变量：

```csharp
public static long lastCommittedTxId = -1; 
```

在创建**ISCPBatchBolt**实例时，它将从输入参数获取 `txAttempt` 对象的值。

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

调用 `FinishBatch` 时，如果不是重播事务，则 `lastCommittedTxId` 会更新。

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

此拓扑包含一个 Java spout 和一个C#螺栓。 它使用 SCP 平台提供的默认序列化和反序列化实现。 请参阅\\示例中的文件 HybridTopology，以获取规范文件详细信息。 有关如何指定 Java 类路径的详细说明，请参阅 Submittopology.bat。

### <a name="scphostdemo"></a>SCPHostDemo

此示例与 HelloWorld 相同。 唯一的区别在于，你的代码编译为 DLL，并使用 Scphost.exe 提交拓扑。 有关更详细的说明，请参阅 SCP 主机模式部分。

## <a name="next-steps"></a>后续步骤

有关使用 SCP 创建的 Apache Storm 拓扑的示例，请参阅以下文章：

* [使用 Visual Studio 开发 Apache Storm on HDInsight 的 C# 拓扑](apache-storm-develop-csharp-visual-studio-topology.md)
* [使用 Apache Storm on HDInsight 从 Azure 事件中心处理事件](apache-storm-develop-csharp-event-hub-topology.md)
* [使用 Apache Storm on HDInsight 处理事件中心的车辆传感器数据](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/IotExample)
* [从 Azure 事件中心提取、转换和加载（ETL）到 Apache HBase](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample)
