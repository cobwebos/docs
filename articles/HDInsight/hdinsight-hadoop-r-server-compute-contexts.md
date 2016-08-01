<!-- not suitable for Mooncake -->

<properties
   pageTitle="用于 HDInsight（预览版）上的 R Server 的计算上下文选项 | Azure"
   description="了解用户可用于 HDInsight（预览版）上的 R Server 的不同计算上下文选项"
   services="HDInsight"
   documentationCenter=""
   authors="jeffstokes72"
   manager="paulettem"
   editor="cgronlun"
/>

<tags
	ms.service="HDInsight"
	ms.date="07/07/2016"
	wacn.date=""/>

# 适用于 HDInsight（预览版）上的 R Server 计算上下文选项

Azure HDInsight（预览版）上的 Microsoft R Server 提供最新的基于 R 的分析功能。它在 [Azure Blob](/documentation/articles/storage-introduction/ "Azure Blob 存储") 存储帐户或本地 Linux 文件系统中使用存储在容器的 HDFS 中的数据。由于 R Server 基于开放源代码的 R 构建，因此你构建的基于 R 的应用程序可以利用超过 8000 个任意开放源代码 R 包。这些应用程序还可以利用 [ScaleR](http://www.revolutionanalytics.com/revolution-r-enterprise-scaler "Revolution Analytics ScaleR")（R Server 附带的 Microsoft 的大数据分析包）中的例程。

高级群集的边缘节点为连接到群集和运行 R 脚本提供了便捷的位置。使用边缘节点，你可以选择跨边缘节点服务器的各个核心运行 ScaleR 的并行化分布式函数。你还可以选择通过使用 ScaleR 的 Hadoop Map Reduce 或 Spark 计算上下文跨群集的各个节点运行这些函数。

## 边缘节点的计算上下文

一般而言，在边缘节点上的 R Server 中运行的 R 脚本将在该节点上的 R 解释程序内运行。但是，调用 ScaleR 函数的步骤例外。ScaleR 调用将在计算环境中运行，而计算环境取决于你如何设置 ScaleR 计算上下文。从边缘节点运行 R 脚本时，计算上下文的可能值包括本地顺序 ('local')、本地并行 ('localpar')、Map Reduce 和 Spark，如下所示：

| 计算上下文 | 设置方式 | 执行上下文 |
|------------------|---------------------------------|---------------------------------------------------------------------------------------|
| 本地顺序 | rxSetComputeContext('local') | 在边缘节点服务器上顺序（非并行）执行 |
| 本地并行 | rxSetComputeContext('localpar') | 跨边缘节点服务器的各个核心并行化执行 |
| Spark | RxSpark() | 通过 Spark 跨 HDI 群集的各个节点并行化分布式执行 |
| Map Reduce | RxHadoopMR() | 通过 Map Reduce 跨 HDI 群集的各个节点并行化分布式执行 |


假设你希望并行化执行以改进性能，有三个选项可供选择。要选择哪个选项取决于分析工作的性质，以及数据的大小和位置。

## 有关确定计算上下文的指导原则

目前，没有哪个公式可以告诉你要使用哪个计算上下文。但是，有些指导原则可帮助你做出正确的选择，或至少可以帮助你在运行基准测试之前缩小选择范围。这些指导原则包括：

1.	本地 Linux 文件系统比 HDFS 更快。
2.	如果数据在本地且采用 XDF，则重复分析的速度更快。
3.	最好是从文本数据源流式传输少量数据；如果数据量较大，可将其转换为 XDF 格式再进行分析。
4.	对于极大量的数据，可将数据复制或流式传输到边缘节点进行分析所造成的开销将变得难以控制。
5.	在 Hadoop 中进行分析时，Spark 比 Map Reduce 更快。

鉴于这些原则，有一些用于选择计算上下文的常规经验规则：

### 本地并行

- 如果要分析的数据量较小，并且不需要重复的分析，则直接将其流式处理为分析例程，并使用“localpar”。
- 如果要分析的数据量较小或者大小适中并且需要重复分析，可将其复制到本地文件系统、导入到 XDF，并通过“localpar”分析。

### Hadoop Spark

- 如果要分析的数据量较大，可将它导入到 HDFS 中的 XDF（除非存储有问题），然后通过“Spark”进行分析。

### Hadoop Map Reduce

- 仅当使用 Spark 计算上下文遇到无法解决的问题时才使用这种方式，因为它的速度通常较慢。

## 关于 rxSetComputeContext 的内联帮助

有关详细信息和 ScaleR 计算上下文的示例，请参阅 R 中关于 rxSetComputeContext 方法的内联帮助，例如：

    > ?rxSetComputeContext

也可以参考 [R Server MSDN](https://msdn.microsoft.com/zh-cn/library/mt674634.aspx "MSDN 上的 R Server") 库中提供的“ScaleR Distributed Computing Guide”（ScaleR 分布式计算指南）。


## 后续步骤

在本文中，你已了解如何创建包含 R Server 的新 HDInsight 群集。此外，你还了解了有关从 SSH 会话使用 R 控制台的基本知识。接下来，你可以阅读以下文章，探索在 HDInsight 上使用 R Server 的其他方法：

- [Overview of R Server for Hadoop（Hadoop 的 R Server 概述）](/documentation/articles/hdinsight-hadoop-r-server-overview/)
- [Get started with R server for Hadoop（Hadoop 的 R Server 入门）](/documentation/articles/hdinsight-hadoop-r-server-get-started/)
- [Add RStudio Server to HDInsight Premium（将 RStudio Server 添加到 HDInsight 高级版）](/documentation/articles/hdinsight-hadoop-r-server-install-r-studio/)
- [Azure Storage options for R Server on HDInsight Premium（适用于 HDInsight 高级版上的 R Server 的 Azure 存储空间选项）](/documentation/articles/hdinsight-hadoop-r-server-storage/)

<!---HONumber=Mooncake_0725_2016-->