---
title: 适用于 Azure HDInsight 中 Storm 的 SCP.NET 编程指南
description: 了解如何通过 SCP.NET 创建可在 Azure HDInsight 中运行的 Storm 中使用的基于 .NET 的 Storm 拓扑。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-csharp
ms.date: 01/13/2020
ms.openlocfilehash: d54a06c457451fc5323ae37b34b53411cdd6abda
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89000135"
---
# <a name="scp-programming-guide-for-apache-storm-in-azure-hdinsight"></a>Azure HDInsight 中 Apache Storm 的 SCP 编程指南

SCP 是一个用于构建实时、可靠、一致且高性能的数据处理应用程序的平台。 它在 [Apache Storm](https://storm.incubator.apache.org/)（开源软件社区设计的一个流处理系统）的基础上构建而成。 Storm 由 Nathan Marz 设计， 在 Twitter 上以开放源代码形式发布。 Storm 使用 [Apache ZooKeeper](https://zookeeper.apache.org/)（另一个 Apache 项目）来实现高可靠性的分布式协调和状态管理。

SCP 项目不仅已移植到 Windows 的 Storm 中，还为 Windows 环境增加了扩展和自定义。 扩展包括 .NET 开发人员体验和 .NET 库。 自定义包括基于 Windows 的部署。

借助扩展和自定义，无需创建开源软件项目的分支。 可以使用在 Storm 顶层构建的派生环境。

## <a name="processing-model"></a>处理模型

SCP 中的数据以连续的元组流形式建模。 通常，元组：

1. 流入队列。
1. 由 Storm 拓扑中托管的业务逻辑提取和转换。
1. 其输出将作为元组传送到其他 SCP 系统，或者提交到存储（例如分布式文件系统）和数据库（例如 SQL Server）。

![馈送待处理数据的队列（继而馈送数据存储）示意图](./media/apache-storm-scp-programming-guide/queue-feeding-data-to-processing-to-data-store.png)

在 Storm 中，应用程序拓扑定义计算图。 拓扑中的每个节点包含处理逻辑。 节点之间的链接指示数据流。

用于将输入数据注入到拓扑中的节点称为 _Spout_。 可以使用 Spout 来为数据定序。 输入数据可能来自文件日志、事务数据库或系统性能计数器等源。

具有输入和输出数据流的节点称为 _Bolt_。 Bolt 执行实际数据筛选、选择和聚合。

SCP 支持“尽力而为”、“至少一次”和“恰好一次”数据处理。

在分布式流处理应用程序中，数据处理过程中可能会发生错误。 此类错误包括网络中断、计算机故障或代码错误。 “至少一次”处理会在出现错误时自动重放原来的数据，从而确保所有数据均至少被处理一次。

“至少一次”处理简单且可靠，适用于许多应用程序。 但是，当应用程序需要确切计数时，进行“至少一次”处理是不够的，因为相同的数据有可能用于应用程序拓扑中。 在此情况下，“恰好一次”处理可确保即使数据被多次重放和处理，结果也是正确的。

通过 SCP，.NET 开发人员可以创建实时数据处理应用程序，同时在 Storm 中使用 Java 虚拟机 (JVM)。 JVM 和 .NET 通过 TCP 本地套接字通信。 每个 Spout/Bolt 都是一个 .NET/Java 进程对，其中，用户逻辑作为插件在 .NET 进程中运行。

若要在 SCP 的顶层构建数据处理应用程序，请执行以下步骤：

1. 设计并实现 Spout，以从队列中提取数据。
1. 设计并实现 Bolt 用于处理输入数据，并将数据保存到数据库等外部存储中。
1. 设计拓扑，然后提交并运行拓扑。

拓扑定义顶点以及在它们之间流动的数据。 SCP 采用拓扑规范并将拓扑部署在 Storm 群集上。在 Storm 群集中，每个顶点在一个逻辑节点上运行。 Storm 任务计划程序处理故障转移和缩放。

本文使用一些简单示例来介绍如何使用 SCP 构建数据处理应用程序。

## <a name="scp-plug-in-interface"></a>SCP 插件接口

SCP 插件是独立的应用程序。 它们可以在开发期间在 Visual Studio 中运行，也可以在完成生产部署后插入到 Storm 管道中。

编写 SCP 插件就像编写任何其他 Windows 控制台应用程序一样。 SCP.NET 平台声明 Spout/Bolt 的接口。 插件代码实现这些接口。 这种设计的主要目的是使你可以专注于自己的业务逻辑，同时让 SCP.NET 平台处理其他事情。

插件代码实现以下接口之一。 实现哪个接口取决于拓扑是事务性还是非事务性，以及组件是 Spout 还是 Bolt。

* **ISCPSpout**
* **ISCPBolt**
* **ISCPTxSpout**
* **ISCPBatchBolt**

### <a name="iscpplugin"></a>ISCPPlugin

**ISCPPlugin** 是许多插件的公用接口。目前，该接口是一个虚拟接口。

```csharp
public interface ISCPPlugin
{
}
```

### <a name="iscpspout"></a>ISCPSpout

**ISCPSpout** 是非事务性 Spout 的接口。

```csharp
public interface ISCPSpout : ISCPPlugin
{
    void NextTuple(Dictionary<string, Object> parms);
    void Ack(long seqId, Dictionary<string, Object> parms); 
    void Fail(long seqId, Dictionary<string, Object> parms);
}
```

调用 **NextTuple** 时，C# 代码可以发出一个或多个元组。 如果没有要发出的数据，此方法应返回而不发出任何内容。

**NextTuple**、**Ack** 和 **Fail** 方法都在 C# 进程的单一线程的紧凑循环中调用。 如果没有要发出的元组，请短暂地将 **NextTuple** 置于休眠状态（例如 10 毫秒）。 这种休眠有助于避免浪费 CPU 的使用。

仅当规范文件启用了确认机制时，才会调用 **Ack** 和 **Fail** 方法。 *seqId* 参数标识已确认或失败的元组。 如果在非事务性拓扑中启用了确认，则应在 Spout 中使用以下 **Emit** 函数：

```csharp
public abstract void Emit(string streamId, List<object> values, long seqId);
```

如果非事务性拓扑不支持确认，可将 **Ack** 和 **Fail** 保留为空函数。

这些函数中的 *parms* 输入参数指定空字典，保留供以后使用。

### <a name="iscpbolt"></a>ISCPBolt

**ISCPBolt** 是非事务性 Bolt 的接口。

```csharp
public interface ISCPBolt : ISCPPlugin
{
void Execute(SCPTuple tuple);
}
```

有新的元组可用时，将调用 **Execute** 函数来处理该元组。

### <a name="iscptxspout"></a>ISCPTxSpout

**ISCPTxSpout** 是事务性 Spout 的接口。

```csharp
public interface ISCPTxSpout : ISCPPlugin
{
    void NextTx(out long seqId, Dictionary<string, Object> parms);  
    void Ack(long seqId, Dictionary<string, Object> parms);         
    void Fail(long seqId, Dictionary<string, Object> parms);        
}
```

与对应的非事务性方法一样，**NextTx**、**Ack** 和 **Fail** 也都在 C# 进程的单一线程的紧凑循环中调用。 如果没有要发出的元组，请短暂地将 **NextTx** 置于休眠状态（例如 10 毫秒）。 这种休眠有助于避免浪费 CPU 的使用。

调用 **NextTx** 启动新的事务时，*seqId* 输出参数将标识事务。 该事务也在 **Ack** 和 **Fail** 中使用。 **NextTx** 方法可将数据发出到 Java 端。 然后，数据会被存储在 ZooKeeper 中，以支持重用。 由于 ZooKeeper 的容量有限，因此，代码应该只发出元数据，而不在事务性 Spout 中发出批量数据。

由于 Storm 自动重放失败的事务，因此，通常不会调用 **Fail**。 但是，如果 SCP 可以检查事务性 Spout 发出的元数据，当元数据无效时，SCP 可以调用 **Fail**。

这些函数中的 *parms* 输入参数指定空字典，保留供以后使用。

### <a name="iscpbatchbolt"></a>ISCPBatchBolt

**ISCPBatchBolt** 是事务性 Bolt 的接口。

```csharp
public interface ISCPBatchBolt : ISCPPlugin
{
    void Execute(SCPTuple tuple);
    void FinishBatch(Dictionary<string, Object> parms);  
}
```

当有新的元组抵达 Bolt 时，将调用 **Execute** 方法。 当此事务结束时，将调用 **FinishBatch** 方法。 *parms* 输入参数保留供以后使用。

对于事务性拓扑，**StormTxAttempt** 是重要的类。 它包含两个成员：**TxId** 和 **AttemptId**。 **TxId** 成员标识特定的事务。 如果某个事务失败并重放，可以多次尝试该事务。

SCP.NET 新建一个 **ISCPBatchBolt** 对象来处理每个 **StormTxAttempt** 对象，就像 Storm 在 Java 中所做的一样。 这种设计的目的是支持并行事务处理。 事务尝试完成后，将销毁相应的 **ISCPBatchBolt** 对象并回收垃圾。

## <a name="object-model"></a>对象模型

SCP.NET 还会提供可供开发人员用于编程的简单密钥对象集。 对象为 **Context**、**StateStore** 和 **SCPRuntime**。 本部分将介绍这些对象。

### <a name="context"></a>上下文

**Context** 对象为应用程序提供运行环境。 **ISCPSpout**、**ISCPBolt**、**ISCPTxSpout** 或 **ISCPBatchBolt** 的每个 **ISCPPlugin** 实例具有相应的 **Context** 实例。 **Context** 提供的功能可划分为以下两个部分：

* 静态部分，可在整个 C# 进程中使用
* 动态部分，仅可用于特定的 **Context** 实例。

### <a name="static-part"></a>静态部分

```csharp
public static ILogger Logger = null;
public static SCPPluginType pluginType;
public static Config Config { get; set; }
public static TopologyContext TopologyContext { get; set; }  
```

提供 **Logger** 对象用于日志记录。

**pluginType** 对象指示 C# 进程的插件类型。 如果该进程在本地测试模式下运行（没有 Java），则插件类型为 **SCP_NET_LOCAL**。

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

**Config** 属性从 Java 端获取配置参数，初始化 C# 插件时，Java 端会传递这些参数。 **Config** 参数划分为两个部分：**stormConf** 和 **pluginConf**。

```csharp
public Dictionary<string, Object> stormConf { get; set; }  
public Dictionary<string, Object> pluginConf { get; set; }  
```

**stormConf** 部分是 Storm 定义的参数，**pluginConf** 部分是 SCP 定义的参数。 下面是一个示例：

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

**TopologyContext** 类型获取拓扑上下文。 它非常适合用于多个并行组件。 下面是一个示例：

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

以下接口与特定的 **Context** 实例相关，该实例由 SCP.NET 平台创建，并传递给代码：

```csharp
// Declare the Output and Input Stream Schemas

public void DeclareComponentSchema(ComponentStreamSchema schema);

// Emit tuple to default stream.
public abstract void Emit(List<object> values);

// Emit tuple to the specific stream.
public abstract void Emit(string streamId, List<object> values);  
```

对于支持确认的非事务性 Spout，提供了以下方法：

```csharp
// for nontransactional spout that supports ack
public abstract void Emit(string streamId, List<object> values, long seqId);  
```

支持确认的非事务性 Bolt 应对其接收到的元组显式调用 **Ack** 或 **Fail**。 发出新元组时，Bolt 还必须指定该元组的定位点。 提供了以下方法：

```csharp
public abstract void Emit(string streamId, IEnumerable<SCPTuple> anchors, List<object> values);
public abstract void Ack(SCPTuple tuple);
public abstract void Fail(SCPTuple tuple);
```

### <a name="statestore"></a>StateStore

**StateStore** 对象提供元数据服务、单调序列生成和无等待协调。 可以在 **StateStore** 中构建更高级别的分布式并发抽象。 这些抽象包括分布式锁、分布式队列、屏障和事务服务。

SCP 应用程序可以使用 **State** 对象序列化 [Apache ZooKeeper](https://zookeeper.apache.org/) 中的信息。 此功能对于事务性拓扑特别有用。 如果某个事务性 Spout 停止响应并重启，**State** 可以从 ZooKeeper 检索所需信息并重启管道。

**StateStore** 对象具有以下主体方法：

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

**State** 对象具有以下主体方法：

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

当 **simpleMode** 设置为 **true** 时，**Commit** 方法将删除 ZooKeeper 中相应的 ZNode。 否则，该方法将删除当前 ZNode，并在 COMMITTED\_PATH 中添加新节点。

### <a name="scpruntime"></a>SCPRuntime

**SCPRuntime** 类提供以下两个方法：

```csharp
public static void Initialize();

public static void LaunchPlugin(newSCPPlugin createDelegate);  
```

**Initialize** 方法初始化 SCP 运行时环境。 在此方法中，C# 进程连接到 Java 端，以获取配置参数和拓扑上下文。

**LaunchPlugin** 方法启动消息处理循环。 在此循环中，C# 插件从 Java 端接收消息。 这些消息包括元组和控制信号。 然后，该插件处理消息（也许是通过调用代码提供的接口方法进行处理）。

**LaunchPlugin** 的输入参数是一个委托。 该方法可返回实现 **ISCPSpout**、**ISCPBolt**、**ISCPTxSpout** 或 **ISCPBatchBolt** 接口的对象。

```csharp
public delegate ISCPPlugin newSCPPlugin(Context ctx, Dictionary<string, Object> parms);
```

对于 **ISCPBatchBolt**，可以从 *parms* 参数获取 **StormTxAttempt** 对象，并使用该对象来判断尝试是否为重放的尝试。 检查重放尝试通常在提交 Bolt 中完成。 本文稍后的 HelloWorldTx 示例将演示此项检查。

SCP 插件通常可在两种模式下运行：本地测试模式和常规模式。

#### <a name="local-test-mode"></a>本地测试模式

在此模式下，C# 代码中的 SCP 插件在开发阶段将在 Visual Studio 中运行。 在此模式下可以使用 **ILocalContext** 接口。 该接口提供所需的方法用于在本地文件中序列化发出的元组，并将其读回到 RAM 中。

```csharp
public interface ILocalContext
{
    List<SCPTuple> RecvFromMsgQueue();
    void WriteMsgQueueToFile(string filepath, bool append = false);  
    void ReadFromFileToMsgQueue(string filepath);
}
```

#### <a name="regular-mode"></a>常规模式

在此模式下，Storm Java 进程将运行 SCP 插件。下面是一个示例：

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

SCP 拓扑规范是一种特定于域的语言 (DSL)，用于描述和配置 SCP 拓扑。 它基于 [Storm 的 Clojure DSL](https://storm.incubator.apache.org/documentation/Clojure-DSL.html) 并通过 SCP 进行扩展。

可将拓扑规范直接提交到 Storm 群集，以通过 **runSpec** 命令执行。

SCP.NET 添加了以下函数来定义事务性拓扑：

| 新建函数 | parameters | 说明 |
| --- | --- | --- |
| **tx-topolopy** |*topology-name*<br />*spout-map*<br />*bolt-map* |使用拓扑名称、Spout 定义图和 Bolt 定义图来定义事务性拓扑。 |
| **scp-tx-spout** |*exec-name*<br />*args*<br />*fields* |定义事务性 Spout。 该函数运行 *exec-name* 指定的应用程序，并使用 *args*。<br /><br />*fields* 参数指定 Spout 的输出字段。 |
| **scp-tx-batch-bolt** |*exec-name*<br />*args*<br />*fields* |定义事务性批处理 Bolt。 该函数运行 *exec-name* 指定的应用程序，并使用 *args*。<br /><br />*fields* 参数指定 Bolt 的输出字段。 |
| **scp-tx-commit-bolt** |*exec-name*<br />*args*<br />*fields* |定义事务性提交 Bolt。 该函数运行 *exec-name* 指定的应用程序，并使用 *args*。<br /><br />*fields* 参数指定 Bolt 的输出字段。 |
| **nontx-topology** |*topology-name*<br />*spout-map*<br />*bolt-map* |使用拓扑名称、Spout 定义图和 Bolt 定义图来定义非事务性拓扑。 |
| **scp-spout** |*exec-name*<br />*args*<br />*fields*<br />*parameters* |定义非事务性 Spout。 该函数运行 *exec-name* 指定的应用程序，并使用 *args*。<br /><br />*fields* 参数指定 Spout 的输出字段。<br /><br />*parameters* 参数是可选的。 使用它可以指定参数，例如“nontransactional.ack.enabled”。 |
| **scp-bolt** |*exec-name*<br />*args*<br />*fields*<br />*parameters* |定义非事务性 Bolt。 该函数运行 *exec-name* 指定的应用程序，并使用 *args*。<br /><br />*fields* 参数指定 Bolt 的输出字段<br /><br />*parameters* 参数是可选的。 使用它可以指定参数，例如“nontransactional.ack.enabled”。 |

SCP.NET 定义以下关键字：

| 关键字 | 说明 |
| --- | --- |
| **:name** |拓扑名称 |
| **:topology** |使用上表中的函数和内置函数的拓扑 |
| **:p** |每个 Spout 或 Bolt 的并行度提示 |
| **:config** |是要配置参数还是更新现有的参数 |
| **:schema** |流的架构 |

SCP.NET 还定义以下常用参数：

| 参数 | 说明 |
| --- | --- |
| "plugin.name" |C# 插件的 .exe 文件名 |
| "plugin.args" |插件参数 |
| "output.schema" |输出架构 |
| "nontransactional.ack.enabled" |是否为非事务性拓扑启用了确认 |

**runSpec** 命令连同位一起部署。 该命令的用法如下：

```csharp
.\bin\runSpec.cmd
usage: runSpec [spec-file target-dir [resource-dir] [-cp classpath]]
ex: runSpec examples\HelloWorld\HelloWorld.spec specs examples\HelloWorld\Target
```

*resource-dir* 参数是可选的。 若要插入 C# 应用程序，请指定此参数。 指定的目录包含应用程序、依赖项和配置。

classpath 参数也是可选参数  。 如果规范文件包含 Java Spout 或 Bolt，此参数将指定 Java 类路径。

## <a name="miscellaneous-features"></a>其他功能

### <a name="input-and-output-schema-declarations"></a>输入和输出架构声明

C# 进程可以发出元组。 为此，平台会将元组序列化为 **byte[]** 对象，并将这些对象传输到 Java 端。 然后，Storm 将这些元组传输到目标。

在下游组件中，C# 进程从 Java 端取回元组，并将这些元组转换为平台的原始类型。 所有这些操作都在平台的后台进行。

若要支持序列化和反序列化，代码需要声明输入和输出的架构。 架构定义为字典。 流 ID 是字典键。 键值为列的类型。 一个组件可以声明多个流。

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

以下函数添加到 **Context** 对象：

```csharp
public void DeclareComponentSchema(ComponentStreamSchema schema)
```

开发人员必须确保发出的元组遵从为流定义的架构。 否则，系统会引发运行时异常。

### <a name="multistream-support"></a>多流支持

SCP 允许代码同时向/从多个不同的流发出/接收数据。 **Context** 对象以 **Emit** 方法的可选流 ID 参数形式反映了此项支持。

在 SCP.NET **Context** 对象中添加了两个方法。 这些方法将一个或多个元组发出到特定的流。 *streamId* 参数是一个字符串。 在 C# 代码和拓扑定义规范中，其值必须相同。

```csharp
/* Emit tuple to the specific stream. */
public abstract void Emit(string streamId, List<object> values);

/* for nontransactional spout only */
public abstract void Emit(string streamId, List<object> values, long seqId);
```

向不存在的流发出元组会导致运行时异常。

### <a name="fields-grouping"></a>字段分组

Storm 中内置的字段分组在 SCP.NET 中无法正常进行。 在 Java 代理端，所有字段的数据类型实际上都是 **byte[]** 。 字段分组使用 **byte[]** 对象的哈希代码来执行分组。 哈希代码是此对象在 RAM 中的地址。 因此，如果两个多字节对象共享相同的内容但地址不相同，分组将不正确。

SCP.NET 添加了一个自定义的分组方法，该方法使用 **byte[]** 对象的内容来执行分组。 在规范文件中，语法如以下示例所示：

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
* `:tx` 或 `:non-tx` 指定拓扑是否为事务性。 之所以需要此信息，因为事务性拓扑和非事务性拓扑的起始索引不同。
* `[0,1]` 指定从零开始的字段 ID 的哈希集。

### <a name="hybrid-topology"></a>混合拓扑

本机 Storm 代码是以 Java 编写的。 SCP.NET 增强了 Storm，使你可以编写 C# 代码来处理业务逻辑。 但是，SCP.NET 还支持混合拓扑，这种拓扑不仅包含 C# Spout/Bolt，还包含 Java Spout/Bolt。

### <a name="specify-java-spoutbolt-in-a-specification-file"></a>在规范文件中指定 Java Spout/Bolt

可以在规范文件中使用 **scp-spout** 和 **scp-bolt** 来指定 Java Spout 和 Bolt。 下面是一个示例：

```csharp
(spout-spec 
  (microsoft.scp.example.HybridTopology.Generator.)
  :p 1)
```

此处，`microsoft.scp.example.HybridTopology.Generator` 是 Java Spout 类的名称。

### <a name="specify-the-java-classpath-in-a-runspec-command"></a>在 runSpec 命令中指定 Java 类路径

若要提交包含 Java Spout 或 Bolt 的拓扑，请先编译这些 Spout 或 Bolt 以生成 JAR 文件。 然后，在提交拓扑时指定包含 JAR 文件的 Java 类路径。 下面是一个示例：

```csharp
bin\runSpec.cmd examples\HybridTopology\HybridTopology.spec specs examples\HybridTopology\net\Target -cp examples\HybridTopology\java\target\*
```

此处，`examples\HybridTopology\java\target\` 是包含 Java Spout/Bolt JAR 文件的文件夹。

### <a name="serialization-and-deserialization-between-java-and-c"></a>Java 与 C# 之间的序列化和反序列化

SCP 组件包括 Java 端和 C# 端。 若要与本机 Java Spout/Bolt 交互，必须在 Java 端与 C# 端之间执行序列化/反序列化，如下图所示：

![Java 组件向 SCP 组件发送数据，然后发送到不同 Java 组件的示意图](./media/apache-storm-scp-programming-guide/java-compent-sending-to-scp-component-sending-to-java-component.png)

#### <a name="serialization-in-the-java-side-and-deserialization-in-the-c-side"></a>Java 端的序列化和 C# 端的反序列化

首先，为 Java 端的序列化和 C# 端的反序列化提供默认实现。

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

如果数据类型不是太复杂，此默认实现应该能够处理大多数情况。 下面是可以插入自己的实现的情况：

* 数据类型过于复杂，默认实现无法对其进行处理。
* 默认实现的性能不符合要求。

Java 端的序列化接口定义为：

```csharp
public interface ICustomizedInteropJavaSerializer {
    public void prepare(String[] args);
    public List<ByteBuffer> serialize(List<Object> objectList);
}
```

C# 端的反序列化接口定义为：

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

此处，`"microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer"` 是反序列化程序的名称，`"microsoft.scp.example.HybridTopology.Person"` 是数据反序列化成的目标类。

你也可以插入自己的 C# 序列化程序和 Java 反序列化程序的实现。

以下代码是 C# 序列化程序的接口：

```csharp
public interface ICustomizedInteropCSharpSerializer
{
    List<byte[]> Serialize(List<object> dataList);
}
```

以下代码是 Java 反序列化程序的接口：

```csharp
public interface ICustomizedInteropJavaDeserializer {
    public void prepare(String[] targetClassNames);
    public List<Object> Deserialize(List<ByteBuffer> dataList);
}
```

## <a name="scp-host-mode"></a>SCP 宿主模式

在此模式下，可将代码编译为 DLL，并使用 SCP 提供的 SCPHost.exe 来提交拓扑。 规范文件如以下代码所示：

```csharp
(scp-spout
  {
    "plugin.name" "SCPHost.exe"
    "plugin.args" ["HelloWorld.dll" "Scp.App.HelloWorld.Generator" "Get"]
    "output.schema" {"default" ["sentence"]}
  })
```

此处，`"plugin.name"` 指定为 SCP SDK 提供的 `"SCPHost.exe"`。 SCPHost.exe 按以下顺序接受三个参数：

1. DLL 名称，在本示例中为 `"HelloWorld.dll"`。
1. 类名，在本示例中为 `"Scp.App.HelloWorld.Generator"`。
1. 公共静态方法的名称，调用此方法可获取 **ISCPPlugin** 的实例。

在宿主模式下，请将代码编译为 DLL，供 SCP 平台调用。 由于平台随后可以全面控制整个处理逻辑，因此我们建议客户在 SCP 宿主模式下提交拓扑。 这样可以简化开发体验。 此外，可以获得更大的灵活性，并改善与将来版本的后向兼容性。

## <a name="scp-programming-examples"></a>SCP 编程示例

### <a name="helloworld"></a>HelloWorld

以下简单 HelloWorld 示例演示了 SCP.NET 的风格。 此示例使用一个非事务性拓扑，该拓扑包括一个名为 **generator** 的 Spout，以及分别名为 **splitter** 和 **counter** 的两个 Bolt。 **generator** Spout 随机生成句子，并将这些句子发出到 **splitter**。 **splitter** Bolt 将句子拆分为字词，并将这些字词发出到 **counter** Bolt。 **counter** Bolt 使用字典来记录每个字词出现的次数。

此示例具有两个规范文件：HelloWorld.spec 和 HelloWorld\_EnableAck.spec。C# 代码可以通过从 Java 端获取 `pluginConf` 对象来确定是否已启用确认。

```csharp
/* demo how to get pluginConf info */
if (Context.Config.pluginConf.ContainsKey(Constants.NONTRANSACTIONAL_ENABLE_ACK))
{
    enableAck = (bool)(Context.Config.pluginConf[Constants.NONTRANSACTIONAL_ENABLE_ACK]);
}
Context.Logger.Info("enableAck: {0}", enableAck);
```

如果在 Spout 中启用了确认，字典将缓存未确认的元组。 如果调用 `Fail`，则会重放失败的元组。

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

以下 HelloWorldTx 示例演示如何实现事务性拓扑。 该示例包含一个名为 **generator** 的 Spout、一个名为 **partial-count** 的批处理 Bolt，以及一个名为 **count-sum** 的提交 Bolt。 该示例还包含三个现有的文本文件：DataSource0.txt、DataSource1.txt 和 DataSource2.txt。

在每个事务中，**generator** Spout 从现有的三个文件中随机选择两个文件，并将这两个文件的名称发出到 **partial-count** Bolt。 **partial-count** Bolt：

1. 从收到的元组中获取文件名。
1. 打开相应的文件。
1. 统计该文件中的字词数。
1. 将单词计数发出到 **count-sum** Bolt。

count-sum Bolt 对总计数进行汇总  。

若要实现“恰好一次”语义，**count-sum** 提交 Bolt 需要判断事务是否为重放的事务。 在本示例中，此 Bolt 包含以下静态成员变量：

```csharp
public static long lastCommittedTxId = -1; 
```

创建 **ISCPBatchBolt** 实例后，它将从输入参数获取 `txAttempt` 对象的值。

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

调用 `FinishBatch` 时，将会更新 `lastCommittedTxId`（如果不是重放的事务）。

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

此拓扑包含一个 Java Spout 和一个 C# Bolt。 它使用 SCP 平台提供的默认序列化和反序列化实现。 有关规范文件的详细信息，请参阅 examples\\HybridTopology 文件夹中的 HybridTopology.spec 文件。 有关如何指定 Java 类路径，另请参阅“SubmitTopology.bat”。

### <a name="scphostdemo"></a>SCPHostDemo

本示例在本质上与 HelloWorld 相同。 唯一不同之处在于，在本示例中，代码编译为 DLL，而拓扑是使用 SCPHost.exe 提交的。 有关更详细的说明，请参阅“SCP 宿主模式”部分。

## <a name="next-steps"></a>后续步骤

有关使用 SCP 创建的 Apache Storm 拓扑示例，请参阅以下文章：

* [使用 Visual Studio 开发 Apache Storm on HDInsight 的 C# 拓扑](apache-storm-develop-csharp-visual-studio-topology.md)
* [使用 Apache Storm on HDInsight 从 Azure 事件中心处理事件](apache-storm-develop-csharp-event-hub-topology.md)
* [使用 Apache Storm on HDInsight 处理事件中心的车辆传感器数据](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/IotExample)
* [从 Azure 事件中心提取、转换和加载 (ETL) 到 Apache HBase](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample)
