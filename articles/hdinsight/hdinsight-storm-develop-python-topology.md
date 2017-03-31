---
title: "在 HDinsight 上的 Storm 拓扑中使用 Python 组件 | Microsoft Docs"
description: "了解如何在 HDinsight 上的 Apache Storm 中使用 Python 组件。 学习如何在基于 Java 和 Clojure 的 Storm 拓扑中使用 Python 组件。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: edd0ec4f-664d-4266-910c-6ecc94172ad8
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: python
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/12/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 8b32aa77e1dbe18076d73e10914b59be107c3588
ms.lasthandoff: 03/25/2017


---
# <a name="develop-apache-storm-topologies-using-python-on-hdinsight"></a>在 HDInsight 上使用 Python 开发 Apache Storm 拓扑

Apache Storm 支持多种语言，甚至允许将几种语言的组件组合到一个拓扑中。 在本文档中，将学习如何在 HDInsight 上基于 Java 和 Clojure 的 Storm 拓扑中使用 Python 组件。

> [!IMPORTANT]
> 本文档提供了使用基于 Windows 和基于 Linux 的 HDInsight 群集的步骤。 Linux 是 HDInsight 3.4 或更高版本上使用的唯一操作系统。 有关详细信息，请参阅 [HDInsight Deprecation on Windows](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date)（HDInsight 在 Windows 上即将弃用）。

## <a name="prerequisites"></a>先决条件

* Python 2.7 或更高版本
* Java JDK 1.7 或更高版本
* [Leiningen](http://leiningen.org/)

## <a name="storm-multi-language-support"></a>Storm 多语言支持

Storm 旨在与用任何编程语言编写的组件配合使用，但这需要这些组件了解如何处理 [Storm 的 Thrift 定义](https://github.com/apache/storm/blob/master/storm-core/src/storm.thrift)。 对于 Python，会以 Apache Storm 项目的一部分提供模块，让用户可以轻松与 Storm 进行交互。 可以在 [https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py](https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py) 上找到此模块。

由于 Apache Storm 是在 Java 虚拟机 (JVM) 上运行的 Java 进程，使用其他语言编写的组件将作为子进程执行。 在 JVM 中运行的 Storm 位使用通过 stdin/stdout 发送的 JSON 消息与这些子进程进行通信。 有关组件间通信的更多详细信息，请参阅 [Multi-lang Protocol](https://storm.apache.org/documentation/Multilang-protocol.html)（多语言协议）文档。

### <a name="the-storm-module"></a>Storm 模块
Storm 模块 (https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py) 提供了创建与 Storm 配合使用的 Python 组件所需的位。

这会提供 `storm.emit`（用于发出 tuple）和 `storm.logInfo`（用于写入日志）等。 建议阅读此文件，了解它提供的内容。

## <a name="challenges"></a>挑战
使用 **storm.py** 模块，可以创建使用数据的 Python Spout 和处理数据 Bolt，但实现组件间的通信的整体拓扑定义仍使用的是 Java 或 Clojure 进行编写。 此外，如果使用的是 Java，还必须创建充当 Python 组件接口的 Java 组件。

另外，由于 Storm 群集以分布方式运行，因此必须确保 Python 组件所需的所有模块在群集中的所有辅助角色节点上均可用。 对于多语言资源，Storm 并未提供任何简单的方法来实现此目的 - 要实现此目的，必须将所有依赖项作为拓扑 jar 文件的一部分包含在内，或在群集中的每个辅助角色节点上手动安装依赖项。

### <a name="java-vs-clojure-topology-definition"></a>Java 与Clojure 拓扑定义
在定义拓扑的两种方法中，Clojure 是迄今为止最简单/简洁的方法，可直接在拓扑结构定义中引用 python 组件。 有关基于 Java 的拓扑结构定义，还必须定义可处理多种操作的 Java 组件（例如，在 Python 组件返回的元组中声明字段）。

本文档以及示例项目中描述了这两种方法。

## <a name="python-components-with-a-java-topology"></a>使用 Java 拓扑的 Python 组件
> [!NOTE]
> 此示例位于 **JavaTopology** 目录中的 [https://github.com/Azure-Samples/hdinsight-python-storm-wordcount](https://github.com/Azure-Samples/hdinsight-python-storm-wordcount)。 这是一个基于 Maven 的项目。 如果不熟悉 Maven，请参阅[在 HDInsight 上使用 Apache Storm 开发基于 Java 的拓扑](hdinsight-storm-develop-java-topology.md)，获取有关为 Storm 拓扑创建 Maven 项目的详细信息。
> 
> 

使用 Python（或其他 JVM 语言组件）、基于 Java 的拓扑最初似乎使用的是 Java 组件：但如果查看每个 Java spout/bolt，就会发现类似于以下的代码：

    public SplitBolt() {
        super("python", "countbolt.py");
    }

Java 在此处调用 Python 并运行包含实际 bolt 逻辑的脚本。 Java spout/bolt（此示例中）只在将由基本 Python 组件发出的元组中声明字段。

本示例中，实际 Python 文件存储在 `/multilang/resources` 目录中。 在 **pom.xml** 中引用 `/multilang` 目录：

<resources>
    <resource>
        <!-- Where the Python bits are kept -->
        <directory>${basedir}/multilang</directory>
    </resource>
</resources>

这会将 `/multilang` 文件夹中的文件包含到将从此项目生成的 jar 内。

> [!IMPORTANT]
> 请注意，这仅指定 `/multilang` 目录而不指定 `/multilang/resources`。 Storm 预期非 JVM 资源位于 `resources` 目录下，因此已在内部进行了寻找。 将组件置于此文件夹中，允许用户在 Java 代码中直接以名称进行引用。 例如，`super("python", "countbolt.py");`。 另一种思考的方法是，Storm 在访问多语言资源时，会将 `resources` 目录视为根 (/)。
> 
> 对于此示例项目，`storm.py` 模块包含在 `/multilang/resources` 目录中。
> 
> 

### <a name="build-and-run-the-project"></a>生成并运行项目
若要本地运行此项目，只需使用以下 Maven 命令生成，并以本地模式下运行即可：

    mvn compile exec:java -Dstorm.topology=com.microsoft.example.WordCount

使用 ctrl+c 终止进程。

要将项目部署到运行 Apache Storm 的 HDInsight 群集，请使用以下步骤：

1. 生成 uber jar：
   
        mvn package
   
    这将在此项目的 `/target` 目录中创建一个名为 **WordCount--1.0-SNAPSHOT.jar** 的文件。
2. 使用以下方法之一将 jar 文件上传到 Hadoop 群集：
   
   * 对于**基于 Linux 的** HDInsight 群集：使用 `scp WordCount-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:WordCount-1.0-SNAPSHOT.jar` 将 jar 文件复制到群集中，将 USERNAME 替换为 SSH 用户名，CLUSTERNAME 替换为 HDInsight 群集名称。
     
       文件上传完后，连接到使用 SSH 的群集并启动使用 `storm jar WordCount-1.0-SNAPSHOT.jar com.microsoft.example.WordCount wordcount` 的拓扑
   * 对于**基于 Windows 的** HDInsight 群集：通过在浏览器中转至 HTTPS://CLUSTERNAME.azurehdinsight.net/，连接到 Storm 仪表板。 将 CLUSTERNAME 替换为 HDInsight 群集名称，出现提示时提供管理员名称和密码。
     
       使用窗体执行以下操作：
     
     * **Jar 文件**：选择“浏览”，然后选择 **WordCount-1.0-SNAPSHOT.jar** 文件
     * **类名**：输入 `com.microsoft.example.WordCount`
     * **其他参数**：输入友好名称（例如 `wordcount`）来标识拓扑
       
       最后，选择“提交”启动拓扑。

> [!NOTE]
> 启动后，Storm 拓扑会一直运行，直至停止（被终止）为止。若要停止拓扑，可从命令行（例如，Linux 群集的 SSH 会话）使用 `storm kill TOPOLOGYNAME` 命令，或使用 Storm UI 选择拓扑，然后选择“终止”按钮。
> 
> 

## <a name="python-components-with-a-clojure-topology"></a>使用 Clojure 拓扑的 Python 组件
> [!NOTE]
> 此示例位于 **ClojureTopology** 目录中的 [https://github.com/Azure-Samples/hdinsight-python-storm-wordcount](https://github.com/Azure-Samples/hdinsight-python-storm-wordcount)。
> 
> 

此拓扑由 [Leiningen](http://leiningen.org)创建，用于[新建 Clojure 项目](https://github.com/technomancy/leiningen/blob/stable/doc/TUTORIAL.md#creating-a-project)。 之后，对已搭建基架的项目进行了以下修改：

* **project.clj**：添加 Storm 的依赖项，并排除在部署到 HDInsight 服务器时可能会引起问题的项。
* **resources/resources**：Leiningen 创建默认的 `resources` 目录，但此处存储的文件似乎会添加至从此项目创建的 jar 文件的根中，而 Storm 希望这些文件位于名为 `resources` 的子目录中。 因此添加了一个子目录，且 Python 文件存储在 `resources/resources` 中。 在运行时，这将被视为访问 Python 组件的根 (/)。
* **src/wordcount/core.clj**：此文件包含拓扑定义，并且从 **project.clj** 文件引用。 有关使用 Clojure 定义 Storm 拓扑的详细信息，请参阅 [Clojure DSL](https://storm.apache.org/documentation/Clojure-DSL.html)。

### <a name="build-and-run-the-project"></a>生成并运行项目
**若要本地生成并运行项目**，使用以下命令：

    lein clean, run

若要停止拓扑，使用 **Ctrl+C**。

**若要生成 uberjar 并部署到 HDInsight**，使用以下步骤：

1. 创建包含拓扑和所需依赖项的 uberjar：
   
        lein uberjar
   
    这将在 `target\uberjar+uberjar` 目录中创建名为 `wordcount-1.0-SNAPSHOT.jar` 的新文件。
2. 使用以下方法之一，将拓扑部署至 HDInsight 群集运行：
   
   * **基于 Linux 的 HDInsight**
     
     1. 使用 `scp`.将文件复制到 HDInsight 群集。 例如：
        
             scp wordcount-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:wordcount-1.0-SNAPSHOT.jar
        
         将 USERNAME 替换为群集的 SSH 用户，并将 CLUSTERNAME 替换为 HDInsight 群集的名称。
     2. 将文件复制到群集中后，使用 SSH 连接到群集并提交作业。 有关详细信息，请参阅 [Use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)（对 HDInsight 使用 SSH）。
     
     3. 连接后，使用以下命令启动拓扑：
        
             storm jar wordcount-1.0-SNAPSHOT.jar wordcount.core wordcount
   * **基于 Windows 的 HDInsight**
     
     1. 通过在浏览器中转至 HTTPS://CLUSTERNAME.azurehdinsight.net/，连接到 Storm 仪表板。 将 CLUSTERNAME 替换为 HDInsight 群集名称，出现提示时提供管理员名称和密码。
     2. 使用窗体执行以下操作：
        
        * **Jar 文件**：选择“浏览”，然后选择 **wordcount-1.0-SNAPSHOT.jar** 文件
        * **类名**：输入 `wordcount.core`
        * **其他参数**：输入友好名称（例如 `wordcount`）来标识拓扑
          
          最后，选择“提交”启动拓扑。

> [!NOTE]
> 启动后，Storm 拓扑会一直运行，直至停止（被终止）为止。若要停止拓扑，从命令行（SSH 会话到 Linux 群集）使用 `storm kill TOPOLOGYNAME` 命令，或使用 Storm UI 选择拓扑，然后选择“终止”按钮。
> 
> 

## <a name="next-steps"></a>后续步骤
在本文档中，学习了如何从 Storm 拓扑使用 Python 组件。 请参阅以下文档，了解配合使用 Python 和 HDInsight 的其他方式：

* [如何使用 Python 流式处理 MapReduce 作业](hdinsight-hadoop-streaming-python.md)
* [如何在 Pig 和 Hive 中使用 Python 用户定义函数 (UDF) ](hdinsight-python.md)


