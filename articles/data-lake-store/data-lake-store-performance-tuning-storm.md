---
title: "Azure Data Lake Store Storm 性能优化指南 | Microsoft 文档"
description: "Azure Data Lake Store Storm 性能优化指南"
services: data-lake-store
documentationcenter: 
author: stewu
manager: amitkul
editor: stewu
ms.assetid: ebde7b9f-2e51-4d43-b7ab-566417221335
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/19/2016
ms.author: stewu
translationtype: Human Translation
ms.sourcegitcommit: ebf876f946eceddce9c8c990d8b28fcb969bec23
ms.openlocfilehash: 112226028c053cc91f9fb2bc0e5978f7cb2343ed


---
# <a name="performance-tuning-guidance-for-storm-on-hdinsight-and-azure-data-lake-store"></a>Storm on HDInsight 和 Azure Data Lake Store 性能优化指南

优化 Storm 拓扑的性能时，需要考虑一些因素。  必须了解 Spout 和 Bolt 的工作特征（这种工作是 I/O 密集型还是内存密集型的）。

## <a name="prerequisites"></a>先决条件 

* **一个 Azure 订阅**。 请参阅 [获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。 
* **Azure Data Lake Store 帐户**。 有关如何创建帐户的说明，请参阅 [Azure Data Lake Store 入门](data-lake-store-get-started-portal.md) 
* 具有 Data Lake Store 帐户访问权限的**Azure HDInsight 群集**。 请参阅[创建包含 Data Lake Store 的 HDInsight 群集](data-lake-store-hdinsight-hadoop-use-portal.md)。 请确保对该群集启用远程桌面。 
* **在 Azure Data Lake Store 中运行 Storm 群集**。  有关详细信息，请参阅 [Storm on HDInsight](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-storm-overview) 
* **ADLS 性能优化指南**。  有关一般的性能概念，请参阅 [Data Lake Store 性能优化指南](https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-performance-tuning-guidance)  

**优化拓扑的并行度**

增大传入和传出 Azure Data Lake Store 的 I/O 的并发性可以提高性能。  
Storm 拓扑提供一组配置来确定并行度：
* 工作进程数：工作进程均匀分散在 VM 之间。
* Spout 执行器实例数
* Bolt 执行器实例数
* Spout 任务数
* Bolt 任务数

例如，在包含 4 个 VM 和 4 个工作进程、32 个 Spout 执行器和 32 个 Spout 任务、256 个 Bolt 执行器和 512 个 Bolt 任务的群集上：

每个监督器（一个辅助节点）有一个 JVM 工作进程，该进程管理 4 个 Spout 线程和 64 个 Bolt 线程。 在每个线程中，任务按顺序执行。 使用以上配置时，每个 Spout 线程有 1 个任务，每个 Bolt 线程有 2 个任务。

在 Storm 中，这种配置涉及到多个组件，下面描述了这些组件对并行度的影响：
* 头节点（在 Storm 中称为 Nimbus）用于提交和管理作业。 这些节点不会影响并行度。
* 监督器节点 – 在 Azure HDInsight 中，对应于辅助节点 Azure VM。
* 辅助任务是 VM 中运行的 Storm 进程。 每个辅助任务对应于一个 Java JVM 实例。 Storm 会尽可能均匀地将指定的工作进程数分配到辅助节点。
* Spout 和 Bolt 执行器实例：每个执行器实例对应于辅助角色 (JVM) 中运行的一个线程
* Storm 任务：其中每个线程运行的逻辑任务。 这不会更改并行度，因此，你应该评估是否需要为每个执行器提供多个任务。

## <a name="guidance"></a>指南

使用 Azure Data Lake 时，如果采取以下措施，则可以获得最佳性能：
* 将小规模的追加操作联合成更大的大小（理想的大小为 4MB）
* 尽可能地发出大量并发请求。 由于每个 Bolt 线程执行阻塞读取，因此，最好地将每个核心的线程数限制在 8-12 的范围内，使 NIC 和 CPU 得到充分的利用。  更大的 VM 支持更多的并发请求。  

## <a name="example"></a>示例

假设某个群集包含 8 个辅助节点，每个节点为 D13v2 Azure VM。  D13v2 VM 有 8 个核心，因此 8 个辅助节点总共有 64 个核心。

假设每个核心有 8 个 Bolt 线程。 由于有 64 个核心，总共可以配置 512 个 Bolt 执行器实例（即线程）。 在这种情况下，假设我们一开始在每个 VM 安装一个 JVM，主要使用 JVM 中的线程并发性来实现并发性。 这意味着，我们需要 8 个辅助任务（每个 Azure VM 一个）和 512 个 Bolt 执行器。 在这种配置下，Storm 会尝试在辅助节点（即监督器节点）之间均匀分配辅助角色，为每个辅助节点提供一个 JVM。 在监督器中，Storm 会尝试在监督器之间均匀分配执行器，为每个监督器（即 JVM）提供 8 个线程。

## <a name="further-tuning"></a>进一步优化
创建基本拓扑后，可以考虑是否要调整以下任何参数：
* **每个辅助节点的 JVM 数：**如果在内存中托管一个大型数据结构（例如查找表），则每个 JVM 都需要一个单独的副本，安装更少的 JVM 就能通过多个线程使用该结构。 对于 Bolt 的 I/O 而言，JVM 数目不会造成这么大的差异，因为线程数是在这些 JVM 之间增加的。 为方便起见，我们建议为每个辅助角色创建一个 JVM，但根据 Bolt 的作用或者所需的应用程序处理功能，可能需要评估是否要调整此数字。
* **Spout 执行器数：**由于本示例使用 Bolt 向 Azure Data Lake 写入数据，因此 Spout 的数目与 Bolt 性能没有直接的关系。 但是，根据 Spout 中发生的处理或 I/O 工作量，需要优化 Spout 以获得最佳性能。 至少需要确保提供足够的 Spout 来让 Bolt 处于繁忙状态 – 也就是说，Spout 的输出速率应与 Bolt 的吞吐量相符。 实际配置取决于 Spout，这超出了本文的讨论范畴。
* **任务数：**每个 Bolt 以单个线程的形式运行。 增加每个 Bolt 的任务并不能进一步提高并发性。 仅当确认元组的进程占用了大部分 Bolt 执行时间时，增加任务才能发挥作用。 在发送来自 Bolt 的确认之前，我们建议将多个元组分组成一个较大的追加操作。在大多数情况下，分配多个任务并不能带来任何额外的好处。
* **本地或随机分组：**如果启用此设置，元组将发送到同一工作进程中的 Bolt。 这会减少进程间通信和网络调用。 建议在大多数拓扑中采用此设置。

一开始，我们建议先从基本方案着手并使用自己的数据进行测试，同时调整上述参数以获得最佳性能。

## <a name="tuning-the-spout"></a>优化 Spout

**元组超时**

topology.message.timeout.secs – 此设置确定在完成发送消息之后，在多长时间内如果未收到确认，则将消息处理视为失败。

**每个工作进程的最大内存**

Worker.childopts - 此设置用于指定 Java 辅助角色的附加命令行参数。 此处最常用的设置是 XmX，它确定分配给 JVM 堆的最大内存。

**最大 Spout 挂起时间**

Topology.max.spout.pending - 此配置确定任意时间点每个 Spout 线程的进行中元组数（尚未在拓扑中的所有节点上确认）。

一种不错的计算方式是评估每个元组的大小。 然后算出一个 Spout 线程具有的内存量。 将分配给线程的总内存量除以此值，即可得出最大 Spout 挂起时间参数的上限。

## <a name="tuning-the-bolt"></a>优化 Bolt
向 ADLS 写入数据时，建议将大小同步策略（客户端的缓冲区）设置为 4 MiB。  仅当缓冲区大小达到上述值时，才执行刷新或 hsync()。  除非用户显式执行 hsync()，否则辅助角色 VM 上的 ADLS 驱动程序会自动执行这种缓冲。

默认的 ADLS Storm Bolt 提供了一个可用于优化此参数的大小同步策略参数 (fileBufferSize)。

在 I/O 密集型拓扑中，建议让每个 Bolt 线程将数据写入其自身的文件，并设置文件轮转策略 (fileRotationSize)。  当文件达到特定的大小时，系统会自动刷新流并向其写入新文件。  用于轮转的建议文件大小为 1GB。

**确认时机：**在 Storm 中，Spout 不断将数据保存到元组，直到该元组被 Bolt 显式确认。  如果元组已由 Bolt 读取但尚未确认，则无法保证 Bolt 会持久保存在 Azure Data Lake Store 后端。  确认元组后，可以保证 Spout 持久保存在 Bolt 中，因此，随后可以从 Bolt 读取的任何源中删除源数据。  

**在 ADLS 中获得最佳性能：**建议为元组数据提供 4MB 的 Bolt 缓冲区，然后以一个 4MB 写入的方式将数据写入 ADLS 后端。 成功将数据写入存储（通过调用 hflush()）后，Bolt 可以向 Spout 确认数据。 这就是此处提供的示例 Bolt 的作用。 在发出 hflush() 调用和确认元组之前，还接受保存更大数量的元组。 但是，这会增加 Spout 需要保存的进行中元组数量，因此也会增加每个 JVM 所需的内存量。

出于其他与性能无关的原因，应用程序可能要求更频繁地确认元组（以小于 4MB 的数据大小）。 但是，这可能会影响存储后端的 I/O 吞吐量，因此客户应该针对 Bolt 的 I/O 性能，认真权衡这种利弊。

如果元组的传入速率不够高，使得 4MB 缓冲区需要很长时间才能填满，则你可能需要考虑通过多种方式来缓解这种情况：
* 减少 Bolt 数量，从而减少要填充的 4MB 缓冲区
* 使用基于时间或基于计数的策略，每隔 x 次刷新或每隔 y 毫秒触发 hflush() 一次，并确认到目前为止累积的元组。

请注意，在这种情况下，吞吐量会降低，但在事件速率较慢的情况下，最大吞吐量不再是最主要的目标。  采取上述这些缓解措施可以减少元组流入存储所花费的端到端时间。如果即使在面临事件速率较低的情况下也要维持一个实时传输管道，这些做法可能会产生作用。  另请注意，如果元组传入速率较低，则还需要调整 topology.message.timeout_secs 参数，使元组在缓冲或处理期间不会超时。

## <a name="interpreting-storm-ui"></a>解释 Storm UI  
运行拓扑时，可在 storm UI 中对它进行监视。 浏览 UI 时，可以看到以下主要参数：

* **进程执行延迟总计** – 一个元组由 Spout 发出、由 Bolt 处理并确认所花费的平均时间。

* **Bolt 进程延迟总计** – Bolt 中的元组在收到确认之前花费的平均时间。

* **Bolt 执行延迟总计** – execute 方法中的 Bolt 花费的平均时间。

* **失败数** – 在超时之前无法完全处理的元组数目。

* **容量** – 系统繁忙程度的度量值。 如果此数字等于 1，则表示 Bolt 以最快的速度工作。 如果小于 1，应提高并行度。 如果大于&1;，应降低并行度。

## <a name="common-troubleshooting-scenarios"></a>常见故障排除方案
* **大量元组超时** – 检查拓扑中的每个节点，确定瓶颈所在。 此问题的最常见原因是 Bolt 跟不上 Spout，从而导致元组在等待处理时阻塞内部缓冲区。 请考虑增大超时值，或减小最大 Spout 挂起时间。

* **进程执行延迟总计较高，但 Bolt 进程延迟总计较低** – 如果出现这种情况，表示元组的确认速度不够快。 请检查是否有足够数量的确认器。 另一种可能是元组在队列中等待 Bolt 处理的时间太长。 请减小最大 Spout 挂起时间。

* **Bolt 执行延迟较高** – 这表示 Bolt 的 execute() 方法花费的时间太长。 请优化代码，或检查写入大小/刷新行为。

## <a name="limitation"></a>限制
ADLS 限制：如果达到 ADLS 所提供的带宽限制，将开始出现任务失败。 这可以通过观察任务日志中的限制错误来确定。  可以通过增加容器大小来降低并行度。  如果作业需要更大的并发性，请与我们联系。   
若要查看是否受到限制，需要在客户端上启用调试日志记录。 下面是执行该操作的方法：

1. 在“Ambari”>“Storm”>“配置”>“高级 storm-worker-log4j”中更改以下设置。  将“root level="info"”更改为“root level="debug"”。&lt;&gt;&lt;&gt;  重新启动所有节点/服务使配置生效。
2. 监视辅助节点上的 Storm 拓扑日志（在 /var/log/storm/worker-artifacts/&lt;TopologyName&gt;/&lt;port&gt;/worker.log 下面），确定是否发生 ADLS 限制异常。

## <a name="additional-tuning"></a>其他优化方法
有关 Storm 的其他性能优化方法，请参阅这篇[博客](https://blogs.msdn.microsoft.com/shanyu/2015/05/14/performance-tuning-for-hdinsight-storm-and-microsoft-azure-eventhubs/)。

## <a name="examples-to-run"></a>运行的示例
请尝试运行 [github](https://github.com/hdinsight/storm-performance-automation) 上的这个示例。



<!--HONumber=Jan17_HO2-->


