---
title: "将 MapReduce 与 Hadoop on HDInsight 配合使用 | Microsoft Docs"
description: "学习如何在 Hadoop on HDInsight 群集中运行 MapReduce 作业。 你将运行一个实现为 Java MapReduce 作业的基本单词计数操作。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 7f321501-d62c-4ffc-b5d6-102ecba6dd76
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/12/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 279990a67ae260b09d056fd84a12160150eb4539
ms.openlocfilehash: ab40eb8b53e2e685be52d24ecf2c32b193b12b85


---
# <a name="use-mapreduce-in-hadoop-on-hdinsight"></a>在 Hadoop on HDInsight 中使用 MapReduce

[!INCLUDE [mapreduce-selector](../../includes/hdinsight-selector-use-mapreduce.md)]

本文介绍了如何在 HDInsight 群集中的 Hadoop 上运行 MapReduce 作业。 我们将运行一个实现为 Java MapReduce 作业的基本单词计数操作。

## <a name="a-idwhatisawhat-is-mapreduce"></a><a id="whatis"></a>什么是 MapReduce？

Hadoop MapReduce 是一个软件框架，用于编写处理海量数据的作业。 输入数据已拆分成独立的区块，这些区块将在群集中的节点之间并行处理。 MapReduce 作业包括两个函数：

* **映射器**：使用输入数据，对数据进行分析（通常使用筛选器和排序操作），然后发出元组（键/值对）

* **化简器**：使用映射器发出的元组并执行汇总运算，以基于映射器数据创建更小的合并结果

下图演示了一个基本的单词计数 MapReduce 作业示例：

![HDI.WordCountDiagram][image-hdi-wordcountdiagram]

此作业的输出是某个单词在所分析的文本中出现的次数。

* 映射器将输入文本中的每行用作一个输入并将其拆分为多个单词。 每当文本中的单词后跟一个 1 时，映射器将发出一个键/值对。 输出在发送到化简器之前经过排序。
* 随后，化简器会计算每个单词的计数的和并发出一个键/值对（包含单词，后跟该单词的总出现次数）。

可以使用多种语言实现 MapReduce。 Java 是最常见的实现，本文档中使用该语言进行演示。

### <a name="hadoop-streaming"></a>Hadoop 流式处理

与 Java 应用程序一样，基于 Java 和 Java 虚拟机（例如 Scalding 或 Cascading）的语言或框架可以直接作为 MapReduce 作业运行。 其他实体（例如 C# 或 Python）或独立的可执行文件必须使用 Hadoop 流式处理。

Hadoop 流式处理通过 STDIN 和 STDOUT 与映射器和化简器通信 - 映射器和化简器每次从 STDIN 读取一行数据，并将输出写入到 STDOUT。 读取的每行或者由映射器和化简器发出的每行必须采用制表符分隔的键/值对格式：

    [key]/t[value]

有关详细信息，请参阅 [Hadoop Streaming](http://hadoop.apache.org/docs/r1.2.1/streaming.html)（Hadoop 流式处理）。

有关将 Hadoop 流式处理与 hdinsight 配合使用的示例，请参阅：

* [开发 Python MapReduce 作业](hdinsight-hadoop-streaming-python.md)

## <a name="a-iddataaabout-the-sample-data"></a><a id="data"></a>关于示例数据

对于本示例中的示例数据，你将使用 HDInsight 群集中作为文本文档提供的 Leonardo Da Vinci 笔记本。

示例数据存储在 Azure Blob 存储中，HDInsight 可以将该存储用作 Hadoop 群集的默认文件系统。 HDInsight 可以使用 **wasb** 前缀访问 Blob 存储中存储的文件。 例如，若要访问 sample.log 文件，可使用以下语法：

    wasbs:///example/data/gutenberg/davinci.txt

由于 Azure Blob 存储是 HDInsight 的默认存储，因此也可以使用 **/example/data/gutenberg/davinci.txt** 访问该文件。

> [!NOTE]
> 在上述语法中，**wasbs:///** 可用来访问存储在 HDInsight 群集的默认存储容器中的文件。 如果你在设置群集时指定了其他存储帐户，并想要访问这些帐户中存储的文件，你可以指定容器名称和存储帐户地址来访问数据。 例如，**wasbs://mycontainer@mystorage.blob.core.windows.net/example/data/gutenberg/davinci.txt**。


## <a name="a-idjobaabout-the-example-mapreduce"></a><a id="job"></a>关于示例 MapReduce

本示例中使用的 MapReduce 作业位于随附 HDInsight 群集的 **wasbs://example/jars/hadoop-mapreduce-examples.jar** 中。 其中包含一个要针对 **davinci.txt** 运行的单词计数示例。

> [!NOTE]
> 在 HDInsight 2.1 群集上，该文件位于 **wasbs:///example/jars/hadoop-examples.jar**。

下面提供了单词计数 MapReduce 作业的 Java 代码供你参考：

```java
package org.apache.hadoop.examples;

import java.io.IOException;
import java.util.StringTokenizer;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.IntWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Job;
import org.apache.hadoop.mapreduce.Mapper;
import org.apache.hadoop.mapreduce.Reducer;
import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;
import org.apache.hadoop.util.GenericOptionsParser;

public class WordCount {

    public static class TokenizerMapper
        extends Mapper<Object, Text, Text, IntWritable>{

    private final static IntWritable one = new IntWritable(1);
    private Text word = new Text();

    public void map(Object key, Text value, Context context
                    ) throws IOException, InterruptedException {
        StringTokenizer itr = new StringTokenizer(value.toString());
        while (itr.hasMoreTokens()) {
        word.set(itr.nextToken());
        context.write(word, one);
        }
    }
    }

    public static class IntSumReducer
        extends Reducer<Text,IntWritable,Text,IntWritable> {
    private IntWritable result = new IntWritable();

    public void reduce(Text key, Iterable<IntWritable> values,
                        Context context
                        ) throws IOException, InterruptedException {
        int sum = 0;
        for (IntWritable val : values) {
        sum += val.get();
        }
        result.set(sum);
        context.write(key, result);
    }
    }

    public static void main(String[] args) throws Exception {
    Configuration conf = new Configuration();
    String[] otherArgs = new GenericOptionsParser(conf, args).getRemainingArgs();
    if (otherArgs.length != 2) {
        System.err.println("Usage: wordcount <in> <out>");
        System.exit(2);
    }
    Job job = new Job(conf, "word count");
    job.setJarByClass(WordCount.class);
    job.setMapperClass(TokenizerMapper.class);
    job.setCombinerClass(IntSumReducer.class);
    job.setReducerClass(IntSumReducer.class);
    job.setOutputKeyClass(Text.class);
    job.setOutputValueClass(IntWritable.class);
    FileInputFormat.addInputPath(job, new Path(otherArgs[0]));
    FileOutputFormat.setOutputPath(job, new Path(otherArgs[1]));
    System.exit(job.waitForCompletion(true) ? 0 : 1);
    }
}
```

有关编写自己的 MapReduce 作业的说明，请参阅[为 HDInsight 开发 Java MapReduce 程序](hdinsight-develop-deploy-java-mapreduce-linux.md)。

## <a name="a-idrunarun-the-mapreduce"></a><a id="run"></a>运行 MapReduce

HDInsight 可以使用各种方法运行 HiveQL 作业。 使用下表来确定哪种方法最适合你，然后按链接进行演练。

| **请使用以下方法**... | **...要执行以下操作** | ...使用此**群集操作系统** | ...从此**客户端操作系统** |
|:--- |:--- |:--- |:--- |
| [SSH](hdinsight-hadoop-use-mapreduce-ssh.md) |通过 **SSH** 使用 Hadoop 命令 |Linux |Linux、Unix、Mac OS X 或 Windows |
| [Curl](hdinsight-hadoop-use-mapreduce-curl.md) |使用 **REST** 远程提交作业 |Linux 或 Windows |Linux、Unix、Mac OS X 或 Windows |
| [Windows PowerShell](hdinsight-hadoop-use-mapreduce-powershell.md) |使用 **Windows PowerShell** 远程提交作业 |Linux 或 Windows |Windows |
| [远程桌面](hdinsight-hadoop-use-mapreduce-remote-desktop.md) |通过**远程桌面**使用 Hadoop 命令 |Windows |Windows |

> [!IMPORTANT]
> Linux 是 HDInsight 3.4 或更高版本上使用的唯一操作系统。 有关详细信息，请参阅 [HDInsight Deprecation on Windows](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date)（HDInsight 在 Windows 上即将弃用）。

## <a name="a-idnextstepsanext-steps"></a><a id="nextsteps"></a>后续步骤

虽然 MapReduce 提供了强大的诊断功能，但掌握起来可能会比较困难。 有多个基于 Java 的框架可让你更轻松地定义 MapReduce 应用程序，还有一些技术（例如 Pig 和 Hive）可让你更方便地在 HDInsight 中处理数据。 若要了解更多信息，请参阅下列文章：

* [为 HDInsight 开发 Java MapReduce 程序](hdinsight-develop-deploy-java-mapreduce-linux.md)
* [为 HDInsight 开发 Python 流式处理 MapReduce 程序](hdinsight-hadoop-streaming-python.md)
* [使用 Apache Hadoop on HDInsight 开发 Scalding MapReduce 作业](hdinsight-hadoop-mapreduce-scalding.md)
* [将 Hive 与 HDInsight 配合使用][hdinsight-use-hive]
* [将 Pig 与 HDInsight 配合使用][hdinsight-use-pig]
* [运行 HDInsight 示例][hdinsight-samples]

[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-develop-mapreduce-jobs]: hdinsight-develop-deploy-java-mapreduce-linux.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-samples]: hdinsight-run-samples.md
[hdinsight-provision]: hdinsight-provision-clusters.md

[powershell-install-configure]: /powershell/azureps-cmdlets-docs

[image-hdi-wordcountdiagram]: ./media/hdinsight-use-mapreduce/HDI.WordCountDiagram.gif



<!--HONumber=Jan17_HO3-->


