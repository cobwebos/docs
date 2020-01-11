---
title: Azure HDInsight 中 Apache Spark 的 OutOfMemoryError 异常
description: Azure HDInsight 中 Apache Spark 群集的各种 OutOfMemoryError 异常
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/15/2019
ms.openlocfilehash: 31cdef281b1cb26d01a4690c815e3d3621e2c053
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/11/2020
ms.locfileid: "75894313"
---
# <a name="outofmemoryerror-exceptions-for-apache-spark-in-azure-hdinsight"></a>Azure HDInsight 中 Apache Spark 的 OutOfMemoryError 异常

本文介绍在 Azure HDInsight 群集中使用 Apache Spark 组件时的故障排除步骤和可能的解决方法。

## <a name="scenario-outofmemoryerror-exception-for-apache-spark"></a>方案： Apache Spark 的 OutOfMemoryError 异常

### <a name="issue"></a>问题

Apache Spark 应用程序失败，并出现 OutOfMemoryError 未经处理的异常。 你可能会收到类似于以下内容的错误消息：

```error
ERROR Executor: Exception in task 7.0 in stage 6.0 (TID 439)

java.lang.OutOfMemoryError
    at java.io.ByteArrayOutputStream.hugeCapacity(Unknown Source)
    at java.io.ByteArrayOutputStream.grow(Unknown Source)
    at java.io.ByteArrayOutputStream.ensureCapacity(Unknown Source)
    at java.io.ByteArrayOutputStream.write(Unknown Source)
    at java.io.ObjectOutputStream$BlockDataOutputStream.drain(Unknown Source)
    at java.io.ObjectOutputStream$BlockDataOutputStream.setBlockDataMode(Unknown Source)
    at java.io.ObjectOutputStream.writeObject0(Unknown Source)
    at java.io.ObjectOutputStream.writeObject(Unknown Source)
    at org.apache.spark.serializer.JavaSerializationStream.writeObject(JavaSerializer.scala:44)
    at org.apache.spark.serializer.JavaSerializerInstance.serialize(JavaSerializer.scala:101)
    at org.apache.spark.executor.Executor$TaskRunner.run(Executor.scala:239)
    at java.util.concurrent.ThreadPoolExecutor.runWorker(Unknown Source)
    at java.util.concurrent.ThreadPoolExecutor$Worker.run(Unknown Source)
    at java.lang.Thread.run(Unknown Source)
```

```error
ERROR SparkUncaughtExceptionHandler: Uncaught exception in thread Thread[Executor task launch worker-0,5,main]

java.lang.OutOfMemoryError
    at java.io.ByteArrayOutputStream.hugeCapacity(Unknown Source)
    ...
```

### <a name="cause"></a>原因

此异常的最可能原因是未将足够的堆内存分配给 Java 虚拟机 (JVM)。 这些 Jvm 作为执行程序或驱动程序作为 Apache Spark 应用程序的一部分启动。

### <a name="resolution"></a>分辨率

1. 确定 Spark 应用程序将要处理的数据大小上限。 根据输入数据的最大大小、转换输入数据时生成的中间数据，以及进一步转换中间数据时生成的输出数据，对大小进行估计。 如果初始估计值不足，请略微增加大小，然后循环访问直到内存错误下降。

1. 请确保要使用的 HDInsight 群集具有足够的内存和核心资源，以便能够适应 Spark 应用程序。 为此，可以查看群集的 YARN UI 的 "群集指标" 部分，了解所**使用的内存**的值与**内存总量**以及使用的**vcore**与**vcore 总数**。

    ![yarn 核心内存视图](./media/apache-spark-ts-outofmemory/yarn-core-memory-view.png)

1. 将以下 Spark 配置设置为合适的值。 将应用程序要求与群集中的可用资源进行平衡。 这些值不应超过 YARN 查看的可用内存和核心数90%，并且还应满足 Spark 应用程序的最低内存要求：

    ```
    spark.executor.instances (Example: 8 for 8 executor count)
    spark.executor.memory (Example: 4g for 4 GB)
    spark.yarn.executor.memoryOverhead (Example: 384m for 384 MB)
    spark.executor.cores (Example: 2 for 2 cores per executor)
    spark.driver.memory (Example: 8g for 8GB)
    spark.driver.cores (Example: 4 for 4 cores)
    spark.yarn.driver.memoryOverhead (Example: 384m for 384MB)
    ```

    所有执行程序使用的内存总量 =

    ```
    spark.executor.instances * (spark.executor.memory + spark.yarn.executor.memoryOverhead) 
    ```

    驱动程序使用的内存总量 =

    ```
    spark.driver.memory + spark.yarn.driver.memoryOverhead
    ```

---

## <a name="scenario-java-heap-space-error-when-trying-to-open-apache-spark-history-server"></a>方案：尝试打开 Apache Spark history 服务器时出现 Java 堆空间错误

### <a name="issue"></a>问题

在 Spark 历史记录服务器中打开事件时，会收到以下错误：

```
scala.MatchError: java.lang.OutOfMemoryError: Java heap space (of class java.lang.OutOfMemoryError)
```

### <a name="cause"></a>原因

此问题通常是由于打开大型 spark 事件文件时资源不足引起的。 默认情况下，Spark 堆大小设置为 1 GB，但较大的 Spark 事件文件可能需要超过此大小。

如果你想要验证尝试加载的文件的大小，则可以执行以下命令：

```bash
hadoop fs -du -s -h wasb:///hdp/spark2-events/application_1503957839788_0274_1/
**576.5 M**  wasb:///hdp/spark2-events/application_1503957839788_0274_1

hadoop fs -du -s -h wasb:///hdp/spark2-events/application_1503957839788_0264_1/
**2.1 G**  wasb:///hdp/spark2-events/application_1503957839788_0264_1
```

### <a name="resolution"></a>分辨率

可以通过在 Spark 配置中编辑 `SPARK_DAEMON_MEMORY` 属性并重新启动所有服务，来增加 Spark 历史记录服务器内存。

可以通过选择 Custom-spark2-defaults/Config/Advanced custom-spark2-defaults 节，在 Ambari 浏览器 UI 中执行此操作。

![Advanced custom-spark2-defaults-env 部分](./media/apache-spark-ts-outofmemory-heap-space/apache-spark-image01.png)

添加以下属性，将 Spark 历史记录服务器内存从1g 更改为4g： `SPARK_DAEMON_MEMORY=4g`。

![Spark 属性](./media/apache-spark-ts-outofmemory-heap-space/apache-spark-image02.png)

请确保从 Ambari 重新启动所有受影响的服务。

---

## <a name="scenario-livy-server-fails-to-start-on-apache-spark-cluster"></a>方案： Livy Server 未能在 Apache Spark 群集上启动

### <a name="issue"></a>问题

无法在 Apache Spark [（在 Linux 上 Spark 2.1 （HDI 3.6）] 上启动 Livy 服务器。 尝试从 Livy 日志重新启动以下错误堆栈中的结果：

```log
17/07/27 17:52:50 INFO CuratorFrameworkImpl: Starting
17/07/27 17:52:50 INFO ZooKeeper: Client environment:zookeeper.version=3.4.6-29--1, built on 05/15/2017 17:55 GMT
17/07/27 17:52:50 INFO ZooKeeper: Client environment:host.name=10.0.0.66
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.version=1.8.0_131
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.vendor=Oracle Corporation
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.home=/usr/lib/jvm/java-8-openjdk-amd64/jre
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.class.path= <DELETED>
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.library.path= <DELETED>
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.io.tmpdir=/tmp
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.compiler=<NA>
17/07/27 17:52:50 INFO ZooKeeper: Client environment:os.name=Linux
17/07/27 17:52:50 INFO ZooKeeper: Client environment:os.arch=amd64
17/07/27 17:52:50 INFO ZooKeeper: Client environment:os.version=4.4.0-81-generic
17/07/27 17:52:50 INFO ZooKeeper: Client environment:user.name=livy
17/07/27 17:52:50 INFO ZooKeeper: Client environment:user.home=/home/livy
17/07/27 17:52:50 INFO ZooKeeper: Client environment:user.dir=/home/livy
17/07/27 17:52:50 INFO ZooKeeper: Initiating client connection, connectString=zk2-kcspark.cxtzifsbseee1genzixf44zzga.gx.internal.cloudapp.net:2181,zk3-kcspark.cxtzifsbseee1genzixf44zzga.gx.internal.cloudapp.net:2181,zk6-kcspark.cxtzifsbseee1genzixf44zzga.gx.internal.cloudapp.net:2181 sessionTimeout=60000 watcher=org.apache.curator.ConnectionState@25fb8912
17/07/27 17:52:50 INFO StateStore$: Using ZooKeeperStateStore for recovery.
17/07/27 17:52:50 INFO ClientCnxn: Opening socket connection to server 10.0.0.61/10.0.0.61:2181. Will not attempt to authenticate using SASL (unknown error)
17/07/27 17:52:50 INFO ClientCnxn: Socket connection established to 10.0.0.61/10.0.0.61:2181, initiating session
17/07/27 17:52:50 INFO ClientCnxn: Session establishment complete on server 10.0.0.61/10.0.0.61:2181, sessionid = 0x25d666f311d00b3, negotiated timeout = 60000
17/07/27 17:52:50 INFO ConnectionStateManager: State change: CONNECTED
17/07/27 17:52:50 WARN NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
17/07/27 17:52:50 INFO AHSProxy: Connecting to Application History server at headnodehost/10.0.0.67:10200
Exception in thread "main" java.lang.OutOfMemoryError: unable to create new native thread
  at java.lang.Thread.start0(Native Method)
  at java.lang.Thread.start(Thread.java:717)
  at com.cloudera.livy.Utils$.startDaemonThread(Utils.scala:98)
  at com.cloudera.livy.utils.SparkYarnApp.<init>(SparkYarnApp.scala:232)
  at com.cloudera.livy.utils.SparkApp$.create(SparkApp.scala:93)
  at com.cloudera.livy.server.batch.BatchSession$$anonfun$recover$2$$anonfun$apply$4.apply(BatchSession.scala:117)
  at com.cloudera.livy.server.batch.BatchSession$$anonfun$recover$2$$anonfun$apply$4.apply(BatchSession.scala:116)
  at com.cloudera.livy.server.batch.BatchSession.<init>(BatchSession.scala:137)
  at com.cloudera.livy.server.batch.BatchSession$.recover(BatchSession.scala:108)
  at com.cloudera.livy.sessions.BatchSessionManager$$anonfun$$init$$1.apply(SessionManager.scala:47)
  at com.cloudera.livy.sessions.BatchSessionManager$$anonfun$$init$$1.apply(SessionManager.scala:47)
  at scala.collection.TraversableLike$$anonfun$map$1.apply(TraversableLike.scala:244)
  at scala.collection.TraversableLike$$anonfun$map$1.apply(TraversableLike.scala:244)
  at scala.collection.mutable.ResizableArray$class.foreach(ResizableArray.scala:59)
  at scala.collection.mutable.ArrayBuffer.foreach(ArrayBuffer.scala:47)
  at scala.collection.TraversableLike$class.map(TraversableLike.scala:244)
  at scala.collection.AbstractTraversable.map(Traversable.scala:105)
  at com.cloudera.livy.sessions.SessionManager.com$cloudera$livy$sessions$SessionManager$$recover(SessionManager.scala:150)
  at com.cloudera.livy.sessions.SessionManager$$anonfun$1.apply(SessionManager.scala:82)
  at com.cloudera.livy.sessions.SessionManager$$anonfun$1.apply(SessionManager.scala:82)
  at scala.Option.getOrElse(Option.scala:120)
  at com.cloudera.livy.sessions.SessionManager.<init>(SessionManager.scala:82)
  at com.cloudera.livy.sessions.BatchSessionManager.<init>(SessionManager.scala:42)
  at com.cloudera.livy.server.LivyServer.start(LivyServer.scala:99)
  at com.cloudera.livy.server.LivyServer$.main(LivyServer.scala:302)
  at com.cloudera.livy.server.LivyServer.main(LivyServer.scala)
  
  ## using "vmstat" found  we had enough free memory
```

### <a name="cause"></a>原因

`java.lang.OutOfMemoryError: unable to create new native thread` 突出显示，操作系统无法将更多的本机线程分配给 Jvm。 已确认此异常是由于违反每进程线程计数限制导致的。

当 Livy 服务器意外终止时，与 Spark 群集的所有连接也会终止，这意味着所有作业和相关数据都将丢失。 引入 HDP 2.6 会话恢复机制后，Livy 将会话详细信息存储在 Zookeeper 中，以便在 Livy 服务器恢复后恢复。

当大量作业通过 Livy 提交时，作为 Livy 服务器的高可用性的一部分，将这些会话状态存储在 ZK 中（在 HDInsight 群集上），并在 Livy 服务重新启动时恢复这些会话。 在意外终止后重新启动时，Livy 将为每个会话创建一个线程，并累计一定数量的待恢复会话，从而导致创建的线程太多。

### <a name="resolution"></a>分辨率

使用以下详细步骤删除所有条目。

1. 使用获取 zookeeper 节点的 IP 地址

    ```bash
    grep -R zk /etc/hadoop/conf  
    ```

1. 上述命令列出了群集的所有 zookeeper

    ```bash
    /etc/hadoop/conf/core-site.xml:      <value>zk1-hwxspa.lnuwp5akw5ie1j2gi2amtuuimc.dx.internal.cloudapp.net:2181,zk2-      hwxspa.lnuwp5akw5ie1j2gi2amtuuimc.dx.internal.cloudapp.net:2181,zk4-hwxspa.lnuwp5akw5ie1j2gi2amtuuimc.dx.internal.cloudapp.net:2181</value>
    ```

1. 使用 ping 获取 zookeeper 节点的所有 IP 地址，也可以使用 zk name 从头节点连接到 zookeeper

    ```bash
    /usr/hdp/current/zookeeper-client/bin/zkCli.sh -server zk2-hwxspa:2181
    ```

1. 连接到 zookeeper 后，请执行以下命令以列出尝试重新启动的所有会话。

    1. 大多数情况下，这可能是超过8000个会话的列表####

        ```bash
        ls /livy/v1/batch
        ```

    1. 以下命令将删除所有恢复到的会话。 #####

        ```bash
        rmr /livy/v1/batch
        ```

1. 等待以上命令完成，并使光标返回提示符，然后从 Ambari 重新启动 Livy service，这应该会成功。

> [!NOTE]
> 在 livy 会话完成执行后 `DELETE`。 Spark 应用完成后，将不会自动删除 Livy 批处理会话（这是设计完成的）。 Livy 会话是由针对 Livy Rest 服务器的 POST 请求创建的实体。 需要 `DELETE` 调用才能删除该实体。 或者我们应该等待 GC 开始。

---

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* [Spark 内存管理概述](https://spark.apache.org/docs/latest/tuning.html#memory-management-overview)。

* [调试 HDInsight 群集上的 Spark 应用程序](https://blogs.msdn.microsoft.com/azuredatalake/2016/12/19/spark-debugging-101/)。

* 通过[Azure 社区支持](https://azure.microsoft.com/support/community/)获得 azure 专家的解答。

* 连接[@AzureSupport](https://twitter.com/azuresupport) -用于改善客户体验的官方 Microsoft Azure 帐户。 将 Azure 社区连接到正确的资源：答案、支持和专家。

* 如果需要更多帮助，可以从[Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择 "**支持**" 或打开 "**帮助 + 支持**中心"。 有关更多详细信息，请参阅[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 Microsoft Azure 订阅中包含对订阅管理和计费支持的访问权限，并且通过一个[Azure 支持计划](https://azure.microsoft.com/support/plans/)提供技术支持。
