---
title: 与 Apache Hadoop-Azure HDInsight 的 MapReduce 和 SSH 连接
description: 了解如何使用 SSH 通过 HDInsight 上的 Apache Hadoop 运行 MapReduce 作业。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/10/2020
ms.openlocfilehash: 543bc29adc85bd767de9479607d067fadf7b0078
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/14/2020
ms.locfileid: "75934706"
---
# <a name="use-mapreduce-with-apache-hadoop-on-hdinsight-with-ssh"></a>通过 SSH 将 MapReduce 与 HDInsight 上的 Apache Hadoop 配合使用

[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

了解如何从安全外壳 (SSH) 将 MapReduce 作业提交到 HDInsight。

> [!NOTE]
> 如果已熟悉如何使用基于 Linux 的 Apache Hadoop 服务器，但刚接触 HDInsight，请参阅[基于 Linux 的 HDInsight 提示](../hdinsight-hadoop-linux-information.md)。

## <a name="prerequisites"></a>必备组件

HDInsight 中的 Apache Hadoop 群集。 请参阅[使用 Azure 门户创建 Apache Hadoop 群集](../hdinsight-hadoop-create-linux-clusters-portal.md)。

## <a name="use-hadoop-commands"></a>使用 Hadoop 命令

1. 使用[ssh 命令](../hdinsight-hadoop-linux-use-ssh-unix.md)连接到群集。 将 CLUSTERNAME 替换为群集名称，然后输入以下命令，以编辑以下命令：

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. 连接到 HDInsight 群集后，使用以下命令启动 MapReduce 作业：

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/WordCountOutput
    ```

    此命令启动 `hadoop-mapreduce-examples.jar` 文件中包含的 `wordcount` 类。 它使用 `/example/data/gutenberg/davinci.txt` 文档作为输入，并将输出存储在 `/example/data/WordCountOutput` 中。

    > [!NOTE]
    > 有关此 MapReduce 作业和示例数据的详细信息，请参阅[在 Apache Hadoop on HDInsight 中使用 MapReduce](hdinsight-use-mapreduce.md)。

    作业在处理时提供详细信息，并在完成时返回类似于以下文本的信息：

    ```output
    File Input Format Counters
    Bytes Read=1395666
    File Output Format Counters
    Bytes Written=337623
    ```

1. 作业完成后，使用以下命令列出输出文件：

    ```bash
    hdfs dfs -ls /example/data/WordCountOutput
    ```

    此命令显示两个文件（`_SUCCESS` 和 `part-r-00000`）。 `part-r-00000` 文件包含此作业的输出。

    > [!NOTE]  
    > 某些 MapReduce 作业可能会将结果拆分成多个 **part-r-#####** 文件。 如果是这样，请使用 ##### 后缀指示文件的顺序。

1. 若要查看输出，请使用以下命令：

    ```bash
    hdfs dfs -cat /example/data/WordCountOutput/part-r-00000
    ```

    此命令会显示 **wasbs://example/data/gutenberg/davinci.txt** 文件中包含的单词以及每个单词出现的次数的列表。 以下文本是文件中所含数据的示例：

    ```output
    wreathed        3
    wreathing       1
    wreaths         1
    wrecked         3
    wrenching       1
    wretched        6
    wriggling       1
    ```

## <a name="next-steps"></a>后续步骤

如你所见，Hadoop 命令提供简单的方法让你在 HDInsight 群集上运行 MapReduce 作业，并查看作业输出。 有关 HDInsight 上的 Hadoop 的其他使用方法的信息：

* [在 HDInsight Hadoop 上使用 MapReduce](hdinsight-use-mapreduce.md)
* [将 Apache Hive 与 Apache Hadoop on HDInsight 配合使用](hdinsight-use-hive.md)
