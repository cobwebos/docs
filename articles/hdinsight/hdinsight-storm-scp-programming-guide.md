---
title: "SCP.NET 编程指南 |Microsoft 文档"
description: "了解如何使用 SCP.NET 创建。使用 Storm on HDInsight 使用 NET 基于 Storm 的拓扑。"
services: hdinsight
documentationcenter: 
author: raviperi
manager: jhubbard
editor: cgronlun
ms.assetid: 34192ed0-b1d1-4cf7-a3d4-5466301cf307
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/16/2016
ms.author: raviperi
ms.openlocfilehash: 3d76aebd2a1fd729c8e0639e6afcbde4c3fb752b
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="scp-programming-guide"></a>SCP 编程指南
SCP 是一个平台，可以构建实时、 可靠、 一致和高性能数据处理应用程序。 它基础上构建[Apache Storm](http://storm.incubator.apache.org/) -流处理系统，设计由 OSS 社区。 Storm 旨在由 Nathan Marz 并打开由 Twitter 指明其出处。 它利用[Apache ZooKeeper](http://zookeeper.apache.org/)，另一个 Apache 项目，以使高度可靠的分布式协调和状态管理。 

不仅 SCP 项目移植 Windows 上的 Storm，而且还增加了扩展和自定义项的 Windows 生态系统。 扩展包括.NET 开发人员经验和库、 自定义包括基于 Windows 的部署。 

我们不需派生 OSS 项目，我们还可以利用在 Storm 上构建的派生生态系统，在这种方式中完成的扩展和自定义项。

## <a name="processing-model"></a>处理模型
SCP 中的数据建模为连续的元组流。 通常将元组流入某个队列第一次，然后被提取，再通过托管在 Storm 拓扑，最后输出无法作为到其他 SCP 系统，元组传送或提交到如分布式的文件系统的存储或类似于 SQL Server 数据库的业务逻辑转换。

![将数据传送到处理，源数据存储队列的关系图](media/hdinsight-storm-scp-programming-guide/queue-feeding-data-to-processing-to-data-store.png)

在 Storm 中，应用程序拓扑定义计算的图形。 拓扑中的每个节点包含处理逻辑，节点之间的链接指明数据流。 要将输入的数据注入到拓扑中的节点称为 Spout，可用于序列化数据。 输入的数据可驻留在文件日志、 事务性数据库、 系统性能计数器等。具有两个输入和输出的数据流的节点称为 Bolt，执行此操作的实际数据过滤、 选择和汇总。

SCP 支持最大努力，至少一次和完全-一次数据处理。 在分布式流处理应用程序，数据在处理期间，例如网络中断、 机器故障、 或用户代码错误等等的发生可能各种错误。至少一次处理可确保所有数据至少一次进行都处理，通过自动重播相同的数据时，会发生错误。 至少一次处理简单且可靠，适用于许多应用程序。 但是，当应用程序需要确切计算时，例如，在-至少一次处理是不够的由于相同的数据可能无法在应用程序拓扑中播放。 在这种情况下，完全-甚至当数据可以重播并处理多次，处理设计以确保结果之后是否正确。

通过 SCP，.NET 开发人员能够利用 Java 虚拟机 (JVM) 的基于 Storm 的同时开发实时数据处理应用程序。 .NET 和 JVM 通过 TCP 本地套接字进行通信。 基本上每个 Spout/Bolt 是一个.Net/Java 进程对，用户逻辑作为插件的.Net 进程中运行的位置。

若要生成在 SCP 上的数据处理应用程序，需要几个步骤：

* 设计并实施 Spout，以在队列中数据的请求。
* 设计和实施 Bolt 以处理输入的数据，以及将数据保存到数据库之类的外部存储区。
* 设计拓扑，然后提交并运行拓扑。 拓扑定义顶点以及顶点之间的数据流。 SCP 会采用拓扑规范并将其部署在 Storm 群集，其中每个顶点在一个逻辑节点运行。 故障转移和缩放将会通过 Storm 任务计划程序处理。

本文档将使用一些简单的示例演练如何构建使用 SCP 的数据处理应用程序。

## <a name="scp-plugin-interface"></a>SCP 插件接口
SCP 插件 （或应用程序） 是独立的 Exe，可以同时运行在 Visual Studio 内的开发阶段，并在生产环境中部署后插入到 Storm 管道中。 编写 SCP 插件是一样的编写任何其他标准 Windows 控制台应用程序。 SCP.NET 平台声明用于 spout/bolt，一些接口和用户插件代码应实现这些接口。 这种设计的主要用途是用户可以专注于自己的业务逻辑，和其他事情则由 SCP.NET 平台处理。

用户插件代码应实现以下接口之一，取决于拓扑是事务性还是非事务性和组件是 spout 还是 bolt。

* ISCPSpout
* ISCPBolt
* ISCPTxSpout
* ISCPBatchBolt

### <a name="iscpplugin"></a>ISCPPlugin
ISCPPlugin 是各种插件的公共接口。 目前，它是一个虚拟接口。

    public interface ISCPPlugin 
    {
    }

### <a name="iscpspout"></a>ISCPSpout
ISCPSpout 是适用于非事务性 spout 的接口。

     public interface ISCPSpout : ISCPPlugin                    
     {
         void NextTuple(Dictionary<string, Object> parms);         
         void Ack(long seqId, Dictionary<string, Object> parms);   
         void Fail(long seqId, Dictionary<string, Object> parms);  
     }

当`NextTuple()`调用时，C\#用户代码可以发出一个或多个元组。 如果没有任何要发出，此方法应返回而不发送任何内容。 应注意的是， `NextTuple()`， `Ack()`，和`Fail()`所有在 C 中的单一线程出现紧凑循环中调用\#过程。 如果有要发出的元组，最好短暂的时间 （例如 10 毫秒） 具有 nexttuple 置于休眠状态，以免浪费太多 CPU。

`Ack()`和`Fail()`仅当在规范文件中启用了确认机制时将调用。 `seqId`用于标识已确认或失败的元组。 因此，如果在非事务性拓扑中启用了确认，则应在 Spout 中使用以下 emit 函数：

    public abstract void Emit(string streamId, List<object> values, long seqId); 

如果在非事务性拓扑中，不支持 ack`Ack()`和`Fail()`可以保留为空函数。

`parms`这些函数中的输入的参数仅仅是空字典，它们是保留供将来使用。

### <a name="iscpbolt"></a>ISCPBolt
ISCPBolt 是适用于非事务性 bolt 的接口。

    public interface ISCPBolt : ISCPPlugin 
    {
    void Execute(SCPTuple tuple);           
    }

当新的元组不可用时，`Execute()`将调用函数，以对其进行处理。

### <a name="iscptxspout"></a>ISCPTxSpout
ISCPTxSpout 是适用于事务性 spout 的接口。

    public interface ISCPTxSpout : ISCPPlugin
    {
        void NextTx(out long seqId, Dictionary<string, Object> parms);  
        void Ack(long seqId, Dictionary<string, Object> parms);         
        void Fail(long seqId, Dictionary<string, Object> parms);        
    }

就像自己的非事务性这种部分， `NextTx()`， `Ack()`，和`Fail()`所有在 C 中的单一线程出现紧凑循环中调用\#过程。 当存在要发送的数据时，最好地具有`NextTx`休眠一小段时间 （10 毫秒），以免浪费太多 CPU。

`NextTx()`调用以启动新事务，输出参数`seqId`用于标识事务，这还用`Ack()`和`Fail()`。 在`NextTx()`，用户可以将数据发出到 Java 端。 数据将存储在 ZooKeeper 中，以支持重播。 由于 ZooKeeper 的容量都是非常有限，用户应仅生成一些元数据，大容量事务性 spout 中的数据。

Storm 将自动地重播事务，如果失败，因此`Fail()`不应在正常情况下调用。 但如果 SCP 可以检查事务性 spout 发送的元数据，它可以调用`Fail()`元数据无效。

`parms`这些函数中的输入的参数仅仅是空字典，它们是保留供将来使用。

### <a name="iscpbatchbolt"></a>ISCPBatchBolt
ISCPBatchBolt 是适用于事务性 bolt 的接口。

    public interface ISCPBatchBolt : ISCPPlugin           
    {
        void Execute(SCPTuple tuple);
        void FinishBatch(Dictionary<string, Object> parms);  
    }

`Execute()`新的元组到达 bolt 时调用。 `FinishBatch()`事务结束时调用。 `parms`输入的参数保留供将来使用。

对于事务性拓扑，没有一个重要概念 – `StormTxAttempt`。 它具有两个字段，`TxId`和`AttemptId`。 `TxId`用于标识特定的事务，并且对于给定的事务，可能有多个尝试如果事务失败并且重播。 SCP.NET 会新建不同的 ISCPBatchBolt 对象来处理每个`StormTxAttempt`，就像在 Java 端 Storm 什么。 此设计的目的是支持并行事务处理。 用户应将其保存在注意如果事务尝试结束后，将销毁相应的 ISCPBatchBolt 对象和垃圾回收中。

## <a name="object-model"></a>对象模型
SCP.NET 还提供了一组简单的面向开发人员使用进行编程的键对象。 它们是**上下文**， **StateStore**，和**SCPRuntime**。 在本部分的其余部分将讨论它们。

### <a name="context"></a>上下文
上下文提供给应用程序正在运行的环境。 每个 ISCPPlugin 实例 (ISCPSpout/ISCPBolt/ISCPTxSpout/ISCPBatchBolt) 都有相应的上下文实例。 提供上下文的功能可以分为两个部分: （1） 静态部分，可用于整个 C\#处理，（2） 动态部分，仅可用于特定上下文实例。

### <a name="static-part"></a>静态部分
    public static ILogger Logger = null;
    public static SCPPluginType pluginType;                      
    public static Config Config { get; set; }                    
    public static TopologyContext TopologyContext { get; set; }  

`Logger`提供用于日志记录。

`pluginType`用于指示 C 的插件类型\#过程。 如果 C\#进程运行在本地测试模式下 （没有 Java)，则插件类型是`SCP_NET_LOCAL`。

    public enum SCPPluginType 
    {
        SCP_NET_LOCAL = 0,       
        SCP_NET_SPOUT = 1,       
        SCP_NET_BOLT = 2,        
        SCP_NET_TX_SPOUT = 3,   
        SCP_NET_BATCH_BOLT = 4  
    }

`Config`可用于从 Java 端获取配置参数。 从 Java 端传递的参数时 C\#插件初始化。 `Config`参数分为两个部分：`stormConf`和`pluginConf`。

    public Dictionary<string, Object> stormConf { get; set; }  
    public Dictionary<string, Object> pluginConf { get; set; }  

`stormConf`是由 Storm 定义的参数和`pluginConf`是 SCP 定义的参数。 例如：

    public class Constants
    {
        … …

        // constant string for pluginConf
        public static readonly String NONTRANSACTIONAL_ENABLE_ACK = "nontransactional.ack.enabled";  

        // constant string for stormConf
        public static readonly String STORM_ZOOKEEPER_SERVERS = "storm.zookeeper.servers";           
        public static readonly String STORM_ZOOKEEPER_PORT = "storm.zookeeper.port";                 
    }

`TopologyContext`提供以获取拓扑上下文，是最适用于组件具有多种并行。 下面是一个示例：

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

### <a name="dynamic-part"></a>动态部分
下列接口是与某个上下文实例相关。 上下文实例由 SCP.NET 平台创建并传递到用户代码：

    // Declare the Output and Input Stream Schemas

    public void DeclareComponentSchema(ComponentStreamSchema schema);   

    // Emit tuple to default stream.
    public abstract void Emit(List<object> values);                   

    // Emit tuple to the specific stream.
    public abstract void Emit(string streamId, List<object> values);  

对于支持确认的非事务性 spout，提供了以下方法：

    // for non-transactional Spout which supports ack
    public abstract void Emit(string streamId, List<object> values, long seqId);  

对于支持确认的非事务性 bolt，它应显式`Ack()`或`Fail()`它收到的元组。 和时发出新的元组，它还必须指定新的元组的锚。 提供了以下方法。

    public abstract void Emit(string streamId, IEnumerable<SCPTuple> anchors, List<object> values); 
    public abstract void Ack(SCPTuple tuple);
    public abstract void Fail(SCPTuple tuple);

### <a name="statestore"></a>StateStore
`StateStore`提供元数据服务、 单调序列生成和无等待协调。 可基于高级分布式的并发抽象`StateStore`，包括分布式的锁、 分布式的队列、 屏障和事务服务。

Scp 可使用`State`对象持久保留中 ZooKeeper，尤其是对于事务性拓扑的一些信息。 这样做，如果事务性 spout 崩溃并重新启动，它可以从 ZooKeeper 检索必要的信息并重新启动管道。

`StateStore`对象主要提供以下方法：

    /// <summary>
    /// Static method to retrieve a state store of the given path and connStr 
    /// </summary>
    /// <param name="storePath">StateStore Path</param>
    /// <param name="connStr">StateStore Address</param>
    /// <returns>Instance of StateStore</returns>
    public static StateStore Get(string storePath, string connStr);

    /// <summary>
    /// Create a new state object in this state store instance
    /// </summary>
    /// <returns>State from StateStore</returns>
    public State Create();

    /// <summary>
    /// Retrieve all states that were previously uncommitted, excluding all aborted states 
    /// </summary>
    /// <returns>Uncommited States</returns>
    public IEnumerable<State> GetUnCommitted();

    /// <summary>
    /// Get all the States in the StateStore
    /// </summary>
    /// <returns>All the States</returns>
    public IEnumerable<State> States();

    /// <summary>
    /// Get state or registry object
    /// </summary>
    /// <param name="info">Registry Name(Registry only)</param>
    /// <typeparam name="T">Type, Registry or State</typeparam>
    /// <returns>Return Registry or State</returns>
    public T Get<T>(string info = null);

    /// <summary>
    /// List all the committed states
    /// </summary>
    /// <returns>Registries contain the Committed State </returns> 
    public IEnumerable<Registry> Commited();

    /// <summary>
    /// List all the Aborted State in the StateStore
    /// </summary>
    /// <returns>Registries contain the Aborted State</returns>
    public IEnumerable<Registry> Aborted();

    /// <summary>
    /// Retrieve an existing state object from this state store instance 
    /// </summary>
    /// <returns>State from StateStore</returns>
    /// <typeparam name="T">stateId, id of the State</typeparam>
    public State GetState(long stateId)

`State`对象主要提供以下方法：

    /// <summary>
    /// Set the status of the state object to commit 
    /// </summary>
    public void Commit(bool simpleMode = true); 

    /// <summary>
    /// Set the status of the state object to abort 
    /// </summary>
    public void Abort();

    /// <summary>
    /// Put an attribute value under the give key 
    /// </summary>
    /// <param name="key">Key</param> 
    /// <param name="attribute">State Attribute</param> 
    public void PutAttribute<T>(string key, T attribute); 

    /// <summary>
    /// Get the attribute value associated with the given key 
    /// </summary>
    /// <param name="key">Key</param> 
    /// <returns>State Attribute</returns>               
    public T GetAttribute<T>(string key);                    

有关`Commit()`方法，如果 simpleMode 设置 true，它将只需删除 ZooKeeper 中相应的 ZNode。 否则，它将删除当前的 ZNode 并中已提交的添加新节点\_路径。

### <a name="scpruntime"></a>SCPRuntime
SCPRuntime 提供以下两种方法。

    public static void Initialize();

    public static void LaunchPlugin(newSCPPlugin createDelegate);  

`Initialize()`用于初始化 SCP 运行时环境。 在此方法中，C\#进程将连接到 Java 端获取配置参数和拓扑上下文。

`LaunchPlugin()`用于启动消息处理循环。 在此循环，C\#插件将收到消息窗体 Java 端 （包括元组和控制信号），然后处理的消息，可能调用接口方法提供了由用户代码。 方法的输入的参数`LaunchPlugin()`是一个委托，可返回实施 ISCPSpout/IScpBolt/ISCPTxSpout/ISCPBatchBolt 接口的对象。

    public delegate ISCPPlugin newSCPPlugin(Context ctx, Dictionary\<string, Object\> parms); 

对于 ISCPBatchBolt，我们可以获取`StormTxAttempt`从`parms`，并使用它来判断事务是否是重复的尝试。 这通常是在 commit bolt，它进行了演示`HelloWorldTx`示例。

一般来说，SCP 插件可能会在以下两种模式下运行：

1. 本地测试模式： 在此模式下，SCP 插件 (C\#用户代码) 的开发阶段在 Visual Studio 内运行。 `LocalContext`可在此模式下，提供的方法来序列化到本地文件，将发出元组以及读取回内存。
   
        public interface ILocalContext
        {
            List\<SCPTuple\> RecvFromMsgQueue();
            void WriteMsgQueueToFile(string filepath, bool append = false);  
            void ReadFromFileToMsgQueue(string filepath);                    
        }
2. 常规模式： 在此模式下，SCP 插件启动由 storm java 进程。
   
    下面是 SCP 插件启动示例：
   
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

## <a name="topology-specification-language"></a>拓扑规范语言
SCP 拓扑规范是用于描述和配置 SCP 拓扑的域特定语言。 它基于 Storm 的 Clojure DSL (<http://storm.incubator.apache.org/documentation/Clojure-DSL.html>) 和通过 SCP 进行扩展。

拓扑规范可以直接提交到 storm 群集进行执行通过***runspec***命令。

SCP.NET 添加了以下函数来定义事务性拓扑：

| **新的函数** | **参数** | **描述** |
| --- | --- | --- |
| **tx topolopy** |拓扑名称<br />spout 映射<br />bolt 映射 |定义事务性拓扑与拓扑名称、 &nbsp;spout 定义图和 bolts 定义图 |
| **scp tx spout** |exec 名称<br />参数<br />字段 |定义事务性 spout。 它将运行的应用程序***exec 名称***使用***args***。<br /><br />***字段***是用于 spout 的输出字段 |
| **scp tx 批处理 bolt** |exec 名称<br />参数<br />字段 |定义事务性批处理 Bolt。 它将运行的应用程序***exec 名称***使用***参数。***<br /><br />字段是用于 bolt 的输出字段。 |
| **scp tx 提交 bolt** |exec 名称<br />参数<br />字段 |定义事务性 Committer Bolt。 它将运行的应用程序***exec 名称***使用***args***。<br /><br />***字段***是用于 bolt 的输出字段 |
| **nontx topolopy** |拓扑名称<br />spout 映射<br />bolt 映射 |定义非事务性拓扑与拓扑名称、&nbsp; spout 定义图和 bolts 定义图 |
| **scp spout** |exec 名称<br />参数<br />字段<br />参数 |定义非事务性 spout。 它将运行的应用程序***exec 名称***使用***args***。<br /><br />***字段***是用于 spout 的输出字段<br /><br />***参数***是可选的用于指定某些参数，如"nontransactional.ack.enabled"。 |
| **scp bolt** |exec 名称<br />参数<br />字段<br />参数 |定义非事务性 Bolt。 它将运行的应用程序***exec 名称***使用***args***。<br /><br />***字段***是用于 bolt 的输出字段<br /><br />***参数***是可选的用于指定某些参数，如"nontransactional.ack.enabled"。 |

Scp.net 关键词定义：

| **关键字** | **描述** |
| --- | --- |
| **： 名称** |定义拓扑名称 |
| **： 拓扑** |定义拓扑使用上述函数和内置。 |
| **: p** |定义每个 spout 或 bolt 的并行度提示。 |
| **： 配置** |定义配置参数或更新现有的 |
| **： 架构** |定义流的架构。 |

和常用参数：

| **参数** | **描述** |
| --- | --- |
| **"plugin.name"** |C# 插件的 exe 文件名称 |
| **"plugin.args"** |插件参数 |
| **"output.schema"** |输出架构 |
| **"nontransactional.ack.enabled"** |非事务性拓扑时是否已启用确认 |

Runspec 命令将与 bits 一起部署，其用法类似：

    .\bin\runSpec.cmd
    usage: runSpec [spec-file target-dir [resource-dir] [-cp classpath]]
    ex: runSpec examples\HelloWorld\HelloWorld.spec specs examples\HelloWorld\Target

***资源 dir***参数是可选的你需要时您想要启动 C 将它指定\#应用程序，并且此目录将包含应用程序、 依赖关系和配置。

***Classpath***参数也是可选的。 它用于指定 Java classpath，如果规范文件包含 Java Spout 或 Bolt。

## <a name="miscellaneous-features"></a>其他功能
### <a name="input-and-output-schema-declaration"></a>输入和输出架构声明
用户可以发出元组中 C\#过程中，平台需要需要此元组序列化为 byte []，将转移到 Java 端和 Storm 会将此元组传输到目标。 同时在下游组件中，C\#过程将接收从 java 端返回的元组并将其转换为原始类型由平台，所有这些操作隐藏的平台。

若要支持的序列化和反序列化，用户代码需要声明输入和输出的架构。

输入/输出流架构被定义为一个字典，密钥是 StreamId，值为列的类型。 组件可以声明多个数据流。

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


在上下文对象中，我们已添加了以下 API:

    public void DeclareComponentSchema(ComponentStreamSchema schema)

用户代码必须确保发送的元组遵循该事件流中定义的架构或系统可能会引发运行时异常。

### <a name="multi-stream-support"></a>多个数据流支持
SCP 支持用户代码发出或同时接收来自多个不同数据流。 如 Emit 方法采用一个可选的流 ID 参数，支持将反映在上下文对象中。

添加了 SCP.NET 上下文对象中的两种方法。 它们用于发出元组或元组，以指定 StreamId。 StreamId 是字符串，它必须是在这两个 C 中保持一致\#和拓扑定义规范。

        /* Emit tuple to the specific stream. */
        public abstract void Emit(string streamId, List<object> values);

        /* for non-transactional Spout only */
        public abstract void Emit(string streamId, List<object> values, long seqId);

发出到不存在流将导致运行时异常。

### <a name="fields-grouping"></a>字段分组
在 Strom 生成中字段分组在 SCP.NET 中的工作不正常。 在 Java 代理端，所有字段数据类型都都实际 byte []、 和字段分组会使用字节 [] 对象哈希代码来进行分组。 字节 [] 对象哈希代码是此对象在内存中的地址。 因此，分组将会错误两个 byte [] 对象共享相同的内容但地址不相同。

SCP.NET 添加自定义的分组方法，并且它将使用 byte [] 的内容来进行分组。 在**规范**语法如下所示文件：

    (bolt-spec
        {
            "spout_test" (scp-field-group :non-tx [0,1])
        }
        …
    )


此处，

1. "scp 字段组"意味着"SCP 实现的自定义字段分组"。
2. ": tx"： 非 tx"表示是否是事务性拓扑。 由于是不同 tx 在与非事务性拓扑的起始索引，我们需要此信息。
3. [0，1] 表示哈希集或字段 id，从 0 开始。

### <a name="hybrid-topology"></a>混合拓扑
本机 Storm 用 Java 编写。 和 SCP.Net 已增强它，以使我们的客户可以编写 C\#代码来处理其业务逻辑。 但我们也支持混合拓扑，其中包含不仅 C\# spout/bolt，但还 Java Spout/Bolt。

### <a name="specify-java-spoutbolt-in-spec-file"></a>在规范文件中指定 Java Spout/Bolt
在规范文件中，"scp spout"和"scp bolt"还可指定 Java Spout 和 Bolt，下面是一个示例：

    (spout-spec 
      (microsoft.scp.example.HybridTopology.Generator.)           
      :p 1)

此处`microsoft.scp.example.HybridTopology.Generator`是 Java Spout 类的名称。

### <a name="specify-java-classpath-in-runspec-command"></a>在 runSpec 命令中指定 Java Classpath
如果你想要提交包含 Java Spout 或 Bolt 的拓扑，你需要首先编译 Java Spout 或 Bolt 并获取 Jar 文件。 然后，你应指定 java classpath 提交拓扑时包含 Jar 文件。 下面是一个示例：

    bin\runSpec.cmd examples\HybridTopology\HybridTopology.spec specs examples\HybridTopology\net\Target -cp examples\HybridTopology\java\target\*

此处**示例\\HybridTopology\\java\\目标\\**是包含 Java Spout/Bolt Jar 文件的文件夹。

### <a name="serialization-and-deserialization-between-java-and-c"></a>序列化和反序列化 Java 和 C\ 之间
我们 SCP 组件包括 Java 端和 C\#端。 以交互方式使用本机 Java Spout/Bolt，序列化/反序列化都必须执行 Java 端和 C 之间\#端，如以下关系图中所示。

![发送到发送到 Java 组件的 SCP 组件的 java 组件的图示](media/hdinsight-storm-scp-programming-guide/java-compent-sending-to-scp-component-sending-to-java-component.png)

1. **在 Java 端和在 C 中的反序列化的序列化\#端**
   
   首先，我们提供有关在 Java 端的序列化和反序列化在 C 中的默认实现\#端。 可以在规范文件中指定 Java 端的序列化方法：
   
       (scp-bolt
           {
               "plugin.name" "HybridTopology.exe"
               "plugin.args" ["displayer"]
               "output.schema" {}
               "customized.java.serializer" ["microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer"]
           })
   
   在 C 中的反序列化方法\#端应在 C 中指定\#用户代码：
   
       Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
       inputSchema.Add("default", new List<Type>() { typeof(Person) });
       this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, null));
       this.ctx.DeclareCustomizedDeserializer(new CustomizedInteropJSONDeserializer());            
   
   此默认实现应处理大多数情况下，如果数据类型不是太复杂。 对于某些情况，或者是因为用户数据类型太过复杂，我们的默认实现的性能并不满足用户要求，用户可能会自己的实现。
   
   在 java 端的序列化接口定义为：
   
       public interface ICustomizedInteropJavaSerializer {
           public void prepare(String[] args);
           public List<ByteBuffer> serialize(List<Object> objectList);
       }
   
   在 C 中的反序列化接口\#端定义为：
   
   公共接口 ICustomizedInteropCSharpDeserializer
   
       public interface ICustomizedInteropCSharpDeserializer
       {
           List<Object> Deserialize(List<byte[]> dataList, List<Type> targetTypes);
       }
2. **C 中的序列化\#端和在 Java 端端的反序列化**
   
   在 C 中的序列化方法\#端应在 C 中指定\#用户代码：
   
       this.ctx.DeclareCustomizedSerializer(new CustomizedInteropJSONSerializer()); 
   
   应在规范文件中指定 Java 端中的反序列化方法：
   
     (scp spout
   
       {
         "plugin.name" "HybridTopology.exe"
         "plugin.args" ["generator"]
         "output.schema" {"default" ["person"]}
         "customized.java.deserializer" ["microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer" "microsoft.scp.example.HybridTopology.Person"]
       })
   
   此处"microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer"的反序列化程序，名称，并且"microsoft.scp.example.HybridTopology.Person"目标类的数据进行反序列化到。
   
   用户可以还插件 C 自己实现\#序列化程序和 Java 反序列化程序。 这是用于 c 语言的接口\#序列化程序：
   
       public interface ICustomizedInteropCSharpSerializer
       {
           List<byte[]> Serialize(List<object> dataList);
       }
   
   这是 Java 反序列化程序的接口：
   
       public interface ICustomizedInteropJavaDeserializer {
           public void prepare(String[] targetClassNames);
           public List<Object> Deserialize(List<ByteBuffer> dataList);
       }

## <a name="scp-host-mode"></a>SCP 主机模式
在此模式下，用户可以编译为 DLL，其代码，以及使用 SCP 提供的 SCPHost.exe 提交拓扑。 规范文件如下所示：

    (scp-spout
      {
        "plugin.name" "SCPHost.exe"
        "plugin.args" ["HelloWorld.dll" "Scp.App.HelloWorld.Generator" "Get"]
        "output.schema" {"default" ["sentence"]}
      })

在这里，`plugin.name`指定为`SCPHost.exe`SCP SDK 提供的。 SCPHost.exe 仅接受三个参数：

1. 第一个是 DLL 名称，这是`"HelloWorld.dll"`在此示例中。
2. 第二个是类名称，这是`"Scp.App.HelloWorld.Generator"`在此示例中。
3. 第三个是公共的静态方法，可以调用以获取 ISCPPlugin 的实例的名称。

在主机模式下，用户代码被编译为 DLL，并由 SCP 平台调用。 因此，SCP 平台可以获得完全控制整个处理逻辑。 因此，我们建议客户提交拓扑在 SCP 主机模式下，因为它可以简化开发体验，获得更多灵活性和更好地向后兼容性以及更高版本。

## <a name="scp-programming-examples"></a>SCP 编程示例
### <a name="helloworld"></a>HelloWorld
**HelloWorld**是一个非常简单的示例，以显示的 SCP.Net。 它调用的 spout 中使用非事务性拓扑，**生成器**，和调用的两个 bolt**拆分器**和**计数器**。 Spout**生成器**将随机生成一些句子，并发送到**拆分器**。 Bolt**拆分器**将拆分为字词的句子，并发送到**计数器**bolt。 Bolt"counter"使用字典记录出现的每个单词的次数。

有两个规范文件， **HelloWorld.spec**和**HelloWorld\_EnableAck.spec**对于此示例。 在 C 中\#代码，它可以找出是否已通过从 Java 端获取 pluginConf 来启用确认。

    /* demo how to get pluginConf info */
    if (Context.Config.pluginConf.ContainsKey(Constants.NONTRANSACTIONAL_ENABLE_ACK))
    {
        enableAck = (bool)(Context.Config.pluginConf[Constants.NONTRANSACTIONAL_ENABLE_ACK]);
    }
    Context.Logger.Info("enableAck: {0}", enableAck);

在 spout 中，如果启用了确认，字典用于缓存未确认的元组。 如果调用 fail （），则将重播失败的元组：

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

### <a name="helloworldtx"></a>HelloWorldTx
**HelloWorldTx**示例演示如何实施事务性拓扑。 它具有一个 spout 调用**生成器**、 批处理 bolt 调用**部分计数**，并且提交 bolt 调用**计数总和**。 此外还有三个预先创建的 txt 文件： **DataSource0.txt**， **DataSource1.txt**和**DataSource2.txt**。

在每个事务中，spout**生成器**将随机从预先创建的三个文件，选择两个文件，然后发出两个文件名传递给**部分计数**bolt。 Bolt**部分计数**将首先从接收到的元组，获取的文件名称，然后打开文件和此文件中单词的计数以及最后发出到的单词数**计数总和**bolt。 **计数总和**bolt 将汇总的总计数。

若要实现**一次性**语义，commit bolt**计数总和**需要判断事务是否是重复处理的事务。 在此示例中，它具有静态成员变量：

    public static long lastCommittedTxId = -1; 

创建了 ISCPBatchBolt 实例后，它将获取`txAttempt`从输入参数：

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

当`FinishBatch()`调用时，`lastCommittedTxId`如果它不是重复处理的事务，将会更新。

    public void FinishBatch(Dictionary<string, Object> parms)
    {
        /* judge whether it is a replayed transaction? */
        bool replay = (this.txAttempt.TxId <= lastCommittedTxId);

        if (!replay)
        {
            /* If it is not replayed, update the toalCount and lastCommittedTxId vaule */
            totalCount = totalCount + this.count;
            lastCommittedTxId = this.txAttempt.TxId;
        }
        … …
    }


### <a name="hybridtopology"></a>HybridTopology
此拓扑包含 Java Spout 和 C\# Bolt。 它使用 SCP 平台提供的默认序列化和反序列化实现。 请 ref**参阅**中**示例\\HybridTopology**规范文件的详细信息，文件夹和**SubmitTopology.bat**有关如何指定 Java classpath。

### <a name="scphostdemo"></a>SCPHostDemo
此示例是实质上与 HelloWorld 相同。 唯一的区别是，用户代码被编译为 DLL 和通过使用 SCPHost.exe 提交拓扑。 请 ref 有关更多详细说明的"SCP 主机模式"一节。

## <a name="next-steps"></a>后续步骤
使用 SCP 创建 Storm 拓扑的示例，请参阅以下资源：

* [为 Apache Storm on HDInsight 使用 Visual Studio 开发 C# 拓扑](hdinsight-storm-develop-csharp-visual-studio-topology.md)
* [从 Azure 事件中心使用 Storm on HDInsight 处理事件](hdinsight-storm-develop-csharp-event-hub-topology.md)
* [在 C# Storm 拓朴中创建多个数据流](hdinsight-storm-twitter-trending.md)
* [使用 Power Bi 从 Storm 拓扑可视化数据](hdinsight-storm-power-bi-topology.md)
* [处理从事件中心使用 Storm on HDInsight 的车辆传感器数据](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/IotExample)
* [提取、 转换和到 HBase 中从 Azure 事件中心加载 (ETL)](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample)
* [将使用 Storm 和 HBase on HDInsight 的事件相关联](hdinsight-storm-correlation-topology.md)

