---
title: 什么是 Apache Hadoop 和 MapReduce - Azure HDInsight
description: 介绍 HDInsight，以及 Apache Hadoop 技术堆栈和组件。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: overview
ms.custom: hdinsightactive,hdiseo17may2017,mvc,seodec18
ms.date: 02/27/2020
ms.openlocfilehash: 7e8dd69b7c58e090c30ea1aa59feddab610dd3c5
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2020
ms.locfileid: "78244876"
---
# <a name="what-is-apache-hadoop-in-azure-hdinsight"></a>什么是 Azure HDInsight 中的 Apache Hadoop？

[Apache Hadoop](https://hadoop.apache.org/) 是原始的开源框架，适用于对群集上的大数据集进行分布式处理和分析。 Hadoop 生态系统包括相关的软件和实用程序，例如 Apache Hive、Apache HBase、Spark、Kafka 等等。

Azure HDInsight 是云中适用于企业的分析服务，具有完全托管、全面且开源的特点。 Azure HDInsight 中的 Apache Hadoop 群集类型可让你使用 HDFS、YARN 资源管理和简单的 MapReduce 编程模型来并行处理和分析批数据。

若要查看 HDInsight 上的可用 Hadoop 技术堆栈组件，请参阅[可以与 HDInsight 配合使用的组件和版本](../hdinsight-component-versioning.md)。 若要详细了解 HDInsight 中的 Hadoop，请参阅 [HDInsight 的 Azure 功能页](https://azure.microsoft.com/services/hdinsight/)。

## <a name="what-is-mapreduce"></a>什么是 MapReduce

Apache Hadoop MapReduce 是一个软件框架，用于编写处理海量数据的作业。 输入的数据将拆分为独立的区块。 每个区块跨群集中的节点并行进行处理。 MapReduce 作业包括两个函数：

* **映射器**：使用输入数据，对数据进行分析（通常使用筛选器和排序操作），并发出元组（键/值对）

* **化简器**：使用映射器发出的元组并执行汇总运算，以基于映射器数据创建更小的合并结果

下图演示了一个基本的单词计数 MapReduce 作业示例：

 ![HDI.WordCountDiagram](./media/apache-hadoop-introduction/hdi-word-count-diagram.gif)

此作业的输出是文本中每个单词出现次数的计数。

* 映射器将输入文本中的每行用作一个输入并将其拆分为多个单词。 每当文本中的单词后跟一个 1 时，映射器将发出一个键/值对。 输出在发送到化简器之前经过排序。
* 随后，化简器会计算每个单词的计数的和并发出一个键/值对（包含单词，后跟该单词的总出现次数）。

MapReduce 可使用多种语言实现。 Java 是最常见的实现，本文档中使用该语言进行演示。

## <a name="development-languages"></a>开发语言

基于 Java 和 Java 虚拟机的语言或框架可作为 MapReduce 作业直接运行。 在本文档中使用的示例是 Java MapReduce 应用程序。 C#、Python 等非 Java 语言或独立可执行文件必须使用 **Hadoop 流式处理**。

Hadoop 流式处理通过 STDIN 和 STDOUT 与映射器和化简器通信。 映射器和化简器从 STDIN 中一次读取一行数据，并将输出写入 STDOUT。 映射器和化简器读取或发出的每行必须采用制表符分隔的键/值对格式：

    [key]/t[value]

有关详细信息，请参阅 [Hadoop Streaming](https://hadoop.apache.org/docs/current/hadoop-streaming/HadoopStreaming.html)（Hadoop 流式处理）。

有关将 Hadoop 流式处理与 HDInsight 配合使用的示例，请参阅以下文档：

* [开发 C# MapReduce 作业](apache-hadoop-dotnet-csharp-mapreduce-streaming.md)

## <a name="next-steps"></a>后续步骤

* [在 HDInsight 中创建 Apache Hadoop 群集](apache-hadoop-linux-create-cluster-get-started-portal.md)
