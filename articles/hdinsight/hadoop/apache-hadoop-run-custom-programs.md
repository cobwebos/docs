---
title: 运行自定义 MapReduce 程序 - Azure HDInsight
description: 何时以及如何在 HDInsight 中运行自定义的 MapReduce 程序。
services: hdinsight
author: ashishthaps
editor: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 12/04/2017
ms.author: ashishth
ms.openlocfilehash: 87eb4c8380c0a542d52ffb4a77bcc317407ea545
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/07/2018
ms.locfileid: "39594468"
---
# <a name="run-custom-mapreduce-programs"></a>运行自定义 MapReduce 程序

基于 Hadoop 的大数据系统（例如 HDInsight）支持使用各种工具和技术来处理数据。 下表描述了每种工具和技术的主要优势与注意事项。

| 查询机制 | 优点 | 注意事项 |
| --- | --- | --- |
| **使用 HiveQL 的 Hive** | <ul><li>这是用于批处理和分析大量不可变数据、数据汇总和按需查询的优异解决方案。 此工具使用流行的 SQL 式语法。</li><li>使用此工具能够生成可轻松分区和编制索引的持久性表。</li><li>可以基于相同的数据创建多个外部表和视图。</li><li>此工具支持简单的数据仓库实现，该实现提供大规模横向扩展和容错功能进行数据存储和处理。</li></ul> | <ul><li>此工具要求源数据至少采用某种可识别的结构。</li><li>此工具不适用于实时查询和行级更新。 此工具最适合用于基于大型数据集的批处理作业。</li><li>此工具可能无法执行某些类型的复杂处理任务。</li></ul> |
| **使用 Pig Latin 的 Pig** | <ul><li>这是用于以集的方式处理数据、合并和筛选数据集、将函数应用到记录或记录组，以及通过定义列、将值分组或将列转换为行来重建数据结构的优异解决方案。</li><li>此工具可以使用基于工作流的方法作为数据操作序列。</li></ul> | <ul><li>SQL 用户可能对 Pig Latin 的语法感到陌生，因此觉得比 HiveQL 更难使用。</li><li>默认的输出通常是一个文本文件，因此，更难与可视化工具（例如 Excel）配合使用。 通常，我们会在输出上添加一个 Hive 表层。</li></ul> |
| **自定义映射/化简** | <ul><li>此工具针对映射和化简阶段与执行提供完全控制。</li><li>此工具允许优化查询以实现群集的最大性能，或者将服务器和网络上的负载降到最低。</li><li>可以使用一系列流行语言来编写组件。</li></ul> | <ul><li>它的用法比 Pig 或 Hive 更难，因为我们必须创建自己的映射和化简组件。</li><li>需要联接数据集的进程较难以实现。</li><li>尽管有可用的测试框架，但调试代码比普通的应用程序更复杂，因为代码以批处理作业的形式在 Hadoop 作业计划程序的控制下运行。</li></ul> |
| **HCatalog** | <ul><li>此工具将存储的路径详细信息抽象化，因而简化了管理，并且用户无需知道数据的存储位置。</li><li>此工具支持事件通知（例如数据可用性方面），允许其他工具（例如 Oozie）检测操作的发生时间。</li><li>此工具公开数据的关系视图（包括根据键分区），使数据变得易于访问。</li></ul> | <ul><li>此工具默认支持 RCFile、CSV 文本、JSON 文本、SequenceFile 和 ORC 文件格式，但可能需要编写自定义的 SerDe 才能支持其他格式。</li><li>HCatalog 不是线程安全的。</li><li>在 Pig 脚本中使用 HCatalog 加载器时，列的数据类型存在一些限制。 有关详细信息，请参阅 Apache HCatalog 文档中的 [HCatLoader 数据类型](http://cwiki.apache.org/confluence/display/Hive/HCatalog%20LoadStore#HCatalogLoadStore-HCatLoaderDataTypes)。</li></ul> |

通常，我们可以使用上述最简单的方法来获取所需的。 例如，只需使用 Hive 也许就能获取此类结果，但对于较复杂的方案，可能需要使用 Pig，甚至编写自己的映射和化简组件。 在体验 Hive 或 Pig 之后，还可以确定是否能够通过自定义映射和化简组件来微调和优化处理，从而提高性能。

## <a name="custom-mapreduce-components"></a>自定义映射/化简组件

映射/化简代码由实现为**映射**和**化简**组件的两个独立函数组成。 **映射**组件在多个群集节点上并行运行，其中每个节点将映射应用到该节点自身的数据子集。 **化简**组件对来自所有映射函数的结果进行整理和汇总。 有关这两个组件的详细信息，请参阅[在 HDInsight 上的 Hadoop 中使用 MapReduce](hdinsight-use-mapreduce.md)。

在大多数 HDInsight 处理方案中，使用更高级别的抽象（例如 Pig 或 Hive）会更简单且更有效。 也可以创建要在 Hive 脚本中使用的自定义映射和化简组件，用于执行更复杂的处理。

自定义映射/化简组件通常以 Java 编写。 Hadoop 提供一个流式处理接口，借助该接口可以使用以其他语言（例如 C#、F#、Visual Basic、Python 及 JavaScript）开发的组件。

* 有关开发自定义 Java MapReduce 程序的演练，请参阅[开发适用于 HDInsight 上的 Hadoop 的 Java MapReduce 程序](apache-hadoop-develop-deploy-java-mapreduce-linux.md)。
* 若要查看使用 Python 的示例，请参阅[开发适用于 HDInsight 的 Python 流式处理 MapReduce 程序](apache-hadoop-streaming-python.md)。

对于以下情况，请考虑创建自己的映射和化简组件：

* 需要分析数据并使用自定义逻辑从该数据获取结构化信息，以处理完全非结构化的数据。
* 想要执行在不创建 UDF 的情况下很难（或无法）以 Pig 或 Hive 表达的复杂任务。 例如，可能需要使用外部地理编码服务，将源数据中的纬度和经度坐标或 IP 地址转换为地理位置名称。
* 想要使用 Hadoop 流式处理接口，在 Map/Reduce 组件中重复使用现有的 .NET、Python 或 JavaScript 代码。

## <a name="upload-and-run-your-custom-mapreduce-program"></a>上传和运行自定义 MapReduce 程序

最常见的 MapReduce 程序以 Java 编写，并会编译成 jar 文件。

1. 开发、编译和测试 MapReduce 程序之后，请使用 `scp` 命令将 jar 文件上传到头节点。

    ```bash
    scp mycustomprogram.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    将 **USERNAME** 替换为群集的 SSH 用户帐户。 将 **CLUSTERNAME** 替换为群集名称。 如果使用密码保护 SSH 帐户，系统会提示输入该密码。 如果使用了证书，则可能需要使用 `-i` 参数指定私钥文件。

2. 使用 [SSH](../hdinsight-hadoop-linux-use-ssh-unix.md) 连接到群集。

    ```bash
    ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
    ```

3. 在 SSH 会话中，通过 YARN 执行 MapReduce 程序。

    ```bash
    yarn jar mycustomprogram.jar mynamespace.myclass /example/data/sample.log /example/data/logoutput
    ```

    此命令将 MapReduce 作业提交到 YARN。 输入文件是 `/example/data/sample.log`，输出目录是 `/example/data/logoutput`。 输入文件和所有输出文件将存储到群集的默认存储中。

## <a name="next-steps"></a>后续步骤

* [在 HDInsight 中的 Hadoop 上将 C# 与 MapReduce 流式处理配合使用](apache-hadoop-dotnet-csharp-mapreduce-streaming.md)
* [为 HDInsight 上的 Hadoop 开发 Java MapReduce 程序](apache-hadoop-develop-deploy-java-mapreduce-linux.md)
* [为 HDInsight 开发 Python 流式处理 MapReduce 程序](apache-hadoop-streaming-python.md)
* [使用用于 Eclipse 的 Azure 工具包为 HDInsight 群集创建 Spark 应用程序](../spark/apache-spark-eclipse-tool-plugin.md)
* [在 HDInsight 中通过 Hive 和 Pig 使用 Python 用户定义的函数 (UDF)](python-udf-hdinsight.md)
