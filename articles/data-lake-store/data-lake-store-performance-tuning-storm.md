---
title: 性能优化-带有 Azure Data Lake Storage Gen1 的风暴
description: 了解 Azure Data Lake Storage Gen1 上的风暴群集的性能优化指南。
author: stewu
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 12/19/2016
ms.author: stewu
ms.openlocfilehash: 85a38a4da65d1b4a669a41eba902b39508e9216c
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2020
ms.locfileid: "82691644"
---
# <a name="performance-tuning-guidance-for-storm-on-hdinsight-and-azure-data-lake-storage-gen1"></a>Storm on HDInsight 和 Azure Data Lake Storage Gen1 性能优化指南

了解在优化 Azure Storm 拓扑的性能时应该考虑的因素。 例如，必须了解 Spout 和 Bolt 的工作特征（这种工作是 I/O 密集型还是内存密集型的）。 本文介绍一系列性能优化指南，包括如何排查常见问题。

## <a name="prerequisites"></a>先决条件

* **一个 Azure 订阅**。 请参阅[获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。
* **Azure Data Lake Storage Gen1 帐户**。 有关如何创建帐户的说明，请参阅 [Azure Data Lake Storage Gen1 入门](data-lake-store-get-started-portal.md)。
* 具有 Data Lake Storage Gen1 帐户访问权限的 Azure HDInsight 群集****。 请参阅[创建包含 Data Lake Storage Gen1 的 HDInsight 群集](data-lake-store-hdinsight-hadoop-use-portal.md)。 请确保对该群集启用远程桌面。
* **在 Data Lake Storage Gen1 中运行 Storm 群集**。 有关详细信息，请参阅[HDInsight 上的风暴](https://docs.microsoft.com/azure/hdinsight/hdinsight-storm-overview)。
* **Data Lake Storage Gen1 的性能优化指南**。  有关一般的性能概念，请参阅 [Data Lake Storage Gen1 性能优化指南](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-performance-tuning-guidance)。  

## <a name="tune-the-parallelism-of-the-topology"></a>优化拓扑的并行度

可以通过增大传入和传出 Data Lake Storage Gen1 的 I/O 的并发性来提高性能。 Storm 拓扑提供一组配置来确定并行度：
* 工作进程数（工作进程均匀分散在 VM 之间）。
* Spout 执行器实例数。
* Bolt 执行器实例数。
* Spout 任务数。
* Bolt 任务数。

例如，在包含 4 个 VM 和 4 个工作进程、32 个 Spout 执行器和 32 个 Spout 任务、256 个 Bolt 执行器和 512 个 Bolt 任务的群集上，请考虑以下因素：

每个监督器（一个辅助节点）有一个 Java 虚拟机 (JVM) 工作进程。 此 JVM 进程管理 4 个 Spout 线程和 64 个 Bolt 线程。 在每个线程中，任务按顺序运行。 对于前面的配置，每个 spout 线程都有一个任务，每个螺栓线程有两个任务。

在 Storm 中，这种配置涉及到多个组件，下面描述了这些组件对并行度的影响：
* 头节点（在 Storm 中称为 Nimbus）用于提交和管理作业。 这些节点不会影响并行度。
* 监督器节点。 在 HDInsight 中，对应于辅助节点 Azure VM。
* 辅助任务是 VM 中运行的 Storm 进程。 每个辅助任务对应于一个 JVM 实例。 Storm 会尽可能均匀地将指定的工作进程数分配到辅助节点。
* Spout 和 Bolt 执行器实例。 每个执行器实例对应于辅助角色 (JVM) 中运行的一个线程。
* Storm 任务。 其中每个线程运行的逻辑任务。 这不会更改并行度，因此，应该评估是否需要为每个执行器提供多个任务。

### <a name="get-the-best-performance-from-data-lake-storage-gen1"></a>从 Data Lake Storage Gen1 获得最佳性能

使用 Data Lake Storage Gen1 时，如果采取以下措施，可以获得最佳性能：
* 将小规模的追加操作联合成更大的大小（理想的大小为 4 MB）。
* 尽可能地发出大量并发请求。 由于每个 Bolt 线程执行阻塞读取，因此，最好是将每个核心的线程数限制在 8-12 的范围内。 这可以让 NIC 和 CPU 得到充分利用。 更大的 VM 支持更多的并发请求。  

### <a name="example-topology"></a>示例拓扑

假设你有一个包含 D13v2 Azure VM 的8个工作节点群集。 此 VM 有8个核心，因此在八个辅助角色节点中，共有64个核心。

假设我们每个内核都有8个螺栓线程。 由于有 64 个核心，总共可以配置 512 个 Bolt 执行器实例（即线程）。 在这种情况下，假设我们一开始在每个 VM 安装一个 JVM，主要使用 JVM 中的线程并发性来实现并发性。 这意味着，我们需要8个工作任务（每个 Azure VM 一个）和512螺栓执行器。 根据此配置，风暴会尝试在辅助节点（也称为监督器节点）之间平均分配工作线程，并为每个辅助角色节点提供一个 JVM。 现在，在监察员内，风暴尝试在监察员之间平均分配执行器，为每个监督器（即 JVM）提供8个线程。

## <a name="tune-additional-parameters"></a>优化其他参数
创建基本拓扑后，可以考虑是否要调整以下任何参数：
* **每个辅助节点的 JVM 数。** 如果在内存中托管一个大型数据结构（例如查找表），则每个 JVM 都需要一个单独的副本。 或者，如果安装更少的 JVM，就能通过许多线程使用该数据结构。 对于 Bolt 的 I/O 而言，JVM 数目不会造成这么大的差异，因为线程数是在这些 JVM 之间增加的。 为方便起见，最好是为每个辅助角色创建一个 JVM。 根据 Bolt 的作用或者所需的应用程序处理功能，可能需要评估是否要更改此数字。
* **Spout 执行器数。** 由于上面的示例使用 Bolt 向 Data Lake Storage Gen1 写入数据，因此 Spout 的数目与 Bolt 性能没有直接的关系。 但是，根据 Spout 中发生的处理或 I/O 工作量，最好是优化 Spout 以获得最佳性能。 确保提供足够的 Spout 来让 Bolt 保持繁忙状态。 Spout 的输出速率应与 Bolt 的吞吐量相符。 实际配置取决于 Spout。
* **任务数。** 每个 Bolt 以单个线程的形式运行。 增加每个 Bolt 的任务并不能进一步提高并发性。 仅当确认元组的进程占用了大部分 Bolt 执行时间时，增加任务才能发挥作用。 在从螺栓发送确认之前，最好将多个元组分组为更大的附加。 因此，在大多数情况下，分配多个任务并不能带来任何额外的好处。
* **本地或随机分组。** 如果启用此设置，元组将发送到同一工作进程中的 Bolt。 这会减少进程间通信和网络调用。 建议在大多数拓扑中采用此设置。

这种基本方案是个不错的起点。 可以使用自己的数据进行测试，同时调整上述参数来获得最佳性能。

## <a name="tune-the-spout"></a>优化 Spout

可通过修改以下设置来优化 Spout。

- **元组超时：topology.message.timeout.secs**。 此设置确定消息在被视为失败之前需要完成并接收确认的时间。

- **每个工作进程的最大内存：worker.childopts**。 此设置用于指定 Java 辅助角色的附加命令行参数。 此处最常用的设置是 XmX，它确定分配给 JVM 堆的最大内存。

- **最大 Spout 挂起时间：topology.max.spout.pending**。 此设置确定任意时间每个 Spout 线程的进行中元组数（尚未在拓扑中的所有节点上确认）。

  一种不错的计算方式是评估每个元组的大小。 然后算出一个 Spout 线程具有的内存量。 将分配给线程的总内存量除以此值，即可得出最大 Spout 挂起时间参数的上限。

## <a name="tune-the-bolt"></a>优化 Bolt
向 Data Lake Storage Gen1 写入数据时，请将大小同步策略（客户端的缓冲区）设置为 4 MB。 仅当缓冲区大小为此值时，才执行刷新或 hsync （）。 除非显式执行 hsync()，否则辅助角色 VM 上的 Data Lake Storage Gen1 驱动程序会自动执行这种缓冲。

默认的 Data Lake Storage Gen1 Storm Bolt 提供了一个可用于优化此参数的大小同步策略参数 (fileBufferSize)。

在 I/O 密集型拓扑中，最好是让每个 Bolt 线程将数据写入其自身的文件，并设置文件轮转策略 (fileRotationSize)。 当文件达到特定的大小时，系统会自动刷新流并向其写入新文件。 用于轮转的建议文件大小为 1 GB。

### <a name="handle-tuple-data"></a>处理元组数据

在 Storm 中，Spout 不断将数据保存到元组，直到该元组被 Bolt 显式确认。 如果元组已由 Bolt 读取但尚未确认，Spout 可能无法持久保存在 Data Lake Storage Gen1 后端。 确认元组后，可以保证 Spout 持久保存在 Bolt 中，随后可从 Bolt 读取的任何源中删除源数据。  

若要在 Data Lake Storage Gen1 中获得最佳性能，可为元组数据提供 4 MB 的 Bolt 缓冲区。 然后，将写入到 Data Lake Storage Gen1 的后端。 成功将数据写入存储（通过调用 hflush()）后，Bolt 可以向 Spout 确认数据。 这就是此处提供的示例 Bolt 的作用。 在发出 hflush() 调用和确认元组之前，还接受保存更大数量的元组。 但是，这会增加 Spout 需要保存的进行中元组数量，因此也会增加每个 JVM 所需的内存量。

> [!NOTE]
> 出于其他与性能无关的原因，应用程序可能要求更频繁地确认元组（以小于 4 MB 的数据大小）。 但是，这可能会影响存储后端的 I/O 吞吐量。 应该针对 Bolt 的 I/O 性能认真权衡这种利弊。

如果元组的传入速率不高，使得 4-MB 缓冲区需要很长时间才能填满，请考虑以下缓解措施：
* 减少 Bolt 数量，从而减少要填充的缓冲区。
* 使用基于时间或基于计数的策略，每隔 x 次刷新或每隔 y 毫秒触发 hflush() 一次，并确认到目前为止累积的元组。

在这种情况下，吞吐量较低，但事件速率较低，最大吞吐量并不是最大的目标。 采取上述这些缓解措施可以减少元组流过存储所花费的总时间。 如果即使事件速率较低也想要维持一个实时管道，这些做法可能会有作用。 另请注意，如果元组传入速率较低，应调整 topology.message.timeout_secs 参数，使元组在缓冲或处理期间不会超时。

## <a name="monitor-your-topology-in-storm"></a>在 Storm 中监视拓扑  
运行拓扑时，可在 Storm 用户界面中对它进行监视。 下面是要查看的主要参数：

* **进程执行延迟总计。** 一个元组由 Spout 发出、由 Bolt 处理并确认所花费的平均时间。

* **Bolt 进程延迟总计。** 这是按下的元组在收到确认之前花费的平均时间。

* **Bolt 进程执行总计。** execute 方法中的 Bolt 花费的平均时间。

* **失败数。** 在超时之前无法完全处理的元组数目。

* **功能.** 系统繁忙程度的度量值。 如果此数字等于 1，则表示 Bolt 以最快的速度工作。 如果小于 1，应提高并行度。 如果大于 1，应降低并行度。

## <a name="troubleshoot-common-problems"></a>排查常见问题
下面是一些常见的故障排除方案。
* **许多元组超时。** 查看拓扑中的每个节点以确定瓶颈的位置。 此问题的最常见原因是 Bolt 跟不上 Spout， 从而导致元组在等待处理时阻塞内部缓冲区。 请考虑增大超时值，或减小最大 Spout 挂起时间。

* **进程执行延迟总计较高，但 Bolt 进程延迟较低。** 此情况下，可能不会快速确认元组。 请检查是否有足够数量的确认器。 另一种可能是元组在队列中等待 Bolt 处理的时间太长。 请减小最大 Spout 挂起时间。

* **Bolt 执行延迟较高。** 这表示 Bolt 的 execute() 方法花费的时间太长。 请优化代码，或检查写入大小并刷新行为。

### <a name="data-lake-storage-gen1-throttling"></a>Data Lake Storage Gen1 限制
如果达到 Data Lake Storage Gen1 所提供的带宽限制，将开始出现任务失败。 请检查任务日志中的限制错误。 可以通过增加容器大小来降低并行度。    

若要查看是否受到限制，请在客户端上启用调试日志记录：

1. 在**Ambari** > **风暴** > **Config**Config > **Advanced 风暴-log4j**中，将** &lt;root level = "info"&gt; **改为** &lt;root level = "debug"&gt;**。 重新启动所有节点/服务使配置生效。
2. 监视工作器节点上的 Storm 拓扑日志（在 /var/log/storm/worker-artifacts/&lt;TopologyName&gt;/&lt;port&gt;/worker.log 下面），确定是否发生 Data Lake Storage Gen1 限制异常。

## <a name="next-steps"></a>后续步骤
有关风暴的其他性能调整，请参阅[此博客](https://blogs.msdn.microsoft.com/shanyu/2015/05/14/performance-tuning-for-hdinsight-storm-and-microsoft-azure-eventhubs/)。

有关可运行的其他示例，请参阅 [GitHub 上的这篇文章](https://github.com/hdinsight/storm-performance-automation)。
