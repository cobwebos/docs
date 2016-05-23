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
	ms.date="03/28/2016"
	wacn.date=""/>

#用于 HDInsight（预览版）上的 R Server 的计算上下文选项

HDInsight（预览版）上的 R Server 为使用HDFS（位于你的 [Azure Blob](/documentation/articles/storage-introduction "Azure Blob 存储") 存储帐户的容器中）中存储的数据进行基于 R 的分析提供最新功能。由于 R Server 基于开放源代码的 R 构建，因此你构建的基于 R 的应用程序可以利用超过 8000 个任意开放源代码 R 包，以及 [ScaleR](http://www.revolutionanalytics.com/revolution-r-enterprise-scaler "Revolution Analytics ScaleR")（R Server 附带的 Microsoft 的大数据分析包）的例程。高级群集的边缘节点为连接到群集和运行 R 脚本提供了方便的登录区域。使用边缘节点，你可以通过使用 ScaleR 的 Hadoop Map Reduce 计算上下文选择跨边缘节点服务器的各个核心或跨群集的各个节点运行 ScaleR 的并行化分布式函数。

## 边缘节点的计算上下文

一般情况下，在边缘节点上的 R Server 中执行的 R 脚本将在该节点上的 R 解释器中运行，调用 ScaleR 函数的步骤除外。ScaleR 调用将在计算环境中执行，而计算环境取决于你如何设置 ScaleR 计算上下文。从边缘节点运行 R 脚本时计算上下文的可能值包括本地顺序 ('local')、本地并行 ('localpar') 和 Map Reduce，其中：

| 计算上下文 | 设置方式 | 执行上下文 |
|------------------|---------------------------------|---------------------------------------------------------------------------------------|
| 本地顺序 | rxSetComputeContext('local') | 在边缘节点服务器上顺序（非并行）执行 |
| 本地并行 | rxSetComputeContext('localpar') | 跨边缘节点服务器的各个核心并行化执行 |
| Map Reduce | RxHadoopMR() | 通过 Map Reduce 跨 HDI 群集的各个节点并行化分布式执行 |


假设你希望并行化执行以改进性能，有三个选项可供选择。你的选择将基于分析工作的性质，以及数据的大小和位置。

## 确定计算上下文

目前，没有任何可填充的公式可以告诉你要使用哪一个计算上下文，但是有一些指导原则将引导你做出正确的选择，或至少可在需要最佳选择时帮助你在运行基准之前缩小选择范围。这些指导原则包括：

2.	如果数据在本地且为 XDF，则重复的分析将更加快捷 
3.	对于小型数据，从文本数据源进行流式处理；否则，在分析前将其转换为 XDF 
4.	对于超大型数据，将数据复制/流式处理到边缘节点以进行分析的开销将变得非常大 

鉴于这些原则，有一些用于选择计算上下文的常规经验规则：

### 本地并行

- 如果要分析的数据很小，并且不需要重复的分析，则直接将其流式处理为分析例程，并使用“localpar”。 
- 如果要分析的数据很小并且需要重复分析，或者大小适中，则将其复制到本地文件系统、导入到 XDF，并通过“localpar”分析。 

### Hadoop Map Reduce

- 如果要分析的数据非常大，则尝试通过“Map Reduce”分析。

## 关于 rxSetComputeContext 的内联帮助

有关详细信息和 ScaleR 计算上下文的示例，请参阅 R 中关于 rxSetComputeContext 方法的内联帮助，例如

    > ?rxSetComputeContext 

或参阅 [R Server MSDN](https://msdn.microsoft.com/zh-cn/library/mt674634.aspx "MSDN 上的 R Server") 库中提供的“ScaleR 分布式计算指南”。


## 后续步骤

现在，你已了解如何创建包括 R Server 的新 HDInsight 群集，以及从 SSH 会话使用 R 控制台的基础知识，请使用以下资源发现使用 HDInsight 上的 R Server 的其他方法。

- [Overview of R Server on Hadoop（Hadoop 上的 R Server 概述）](/documentation/articles/hdinsight-hadoop-r-server-overview)
- [Get started with R server on Hadoop（Hadoop 上的 R Server 入门）](/documentation/articles/hdinsight-hadoop-r-server-get-started)
- [Add RStudio Server to HDInsight premium（将 RStudio Server 添加到 HDInsight 高级版）](/documentation/articles/hdinsight-hadoop-r-server-install-r-studio)
- [Azure Storage options for R Server on HDInsight premium（用于 HDInsight 高级版上的 R Server 的 Azure 存储空间选项）](/documentation/articles/hdinsight-hadoop-r-server-storage)

<!---HONumber=Mooncake_0516_2016-->