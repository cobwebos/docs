---
title: 通过 Python 组件 Apache Storm-Azure HDInsight
description: 了解如何创建在 Azure HDInsight 中使用 Python 组件的 Apache Storm 拓扑
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 12/16/2019
ms.openlocfilehash: ba632a98c21926ec28606def128cc068abf47f53
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/03/2020
ms.locfileid: "75646619"
---
# <a name="develop-apache-storm-topologies-using-python-on-hdinsight"></a>在 HDInsight 上使用 Python 开发 Apache Storm 拓扑

了解如何创建使用 Python 组件的 [Apache Storm](https://storm.apache.org/) 拓扑。 Apache Storm 支持多种语言，甚至允许将几种语言的组件组合到一个拓扑中。 借助 [Flux](https://storm.apache.org/releases/current/flux.html) 框架（通过 Storm 0.10.0 引入），可以轻松地创建使用 Python 组件的解决方案。

> [!IMPORTANT]  
> 本文档中的信息已使用 Storm on HDInsight 3.6 进行测试。

## <a name="prerequisites"></a>必备组件

* HDInsight 上的 Apache Storm 群集。 请参阅[使用 Azure 门户创建 Apache Hadoop 群集](../hdinsight-hadoop-create-linux-clusters-portal.md)，并选择 **Storm** 作为**群集类型**。

* 本地风暴开发环境（可选）。 仅当想要在本地运行拓扑时，才需要本地 Storm 环境。 有关详细信息，请参阅[设置开发环境](http://storm.apache.org/releases/current/Setting-up-development-environment.html)。

* [Python 2.7 或更高版本](https://www.python.org/downloads/)。

* [Java 开发人员工具包（JDK）版本 8](https://aka.ms/azure-jdks)。

* 根据 Apache 要求正确[安装](https://maven.apache.org/install.html)的 [Apache Maven](https://maven.apache.org/download.cgi)。  Maven 是 Java 项目的项目生成系统。

## <a name="storm-multi-language-support"></a>Storm 多语言支持

Apache Storm 设计为与使用任何编程语言编写的组件配合使用。 组件必须了解如何处理风暴的 Thrift 定义。 对于 Python，会以 Apache Storm 项目的一部分提供模块，让用户可以轻松与 Storm 进行交互。 可以在 [https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py](https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py) 上找到此模块。

Storm 是在 Java 虚拟机 (JVM) 上运行的 Java 进程。 使用其他语言编写的组件作为子进程执行。 Storm 使用通过 stdin/stdout 发送的 JSON 消息与这些子进程通信。 有关组件间通信的更多详细信息，请参阅 [Multi-lang Protocol](https://storm.apache.org/documentation/Multilang-protocol.html)（多语言协议）文档。

## <a name="python-with-the-flux-framework"></a>使用 Flux 框架的 Python

借助 Flux 框架，可独立于组件定义 Storm 拓扑。 Flux 框架使用 YAML 定义 Storm 拓扑。 下面的文本举例说明如何在 YAML 文档中引用 Python 组件：

```yaml
# Spout definitions
spouts:
  - id: "sentence-spout"
    className: "org.apache.storm.flux.wrappers.spouts.FluxShellSpout"
    constructorArgs:
      # Command line
      - ["python", "sentencespout.py"]
      # Output field(s)
      - ["sentence"]
    # parallelism hint
    parallelism: 1
```

类 `FluxShellSpout` 用于启动实现 spout 的 `sentencespout.py` 脚本。

Flux 需要 Python 脚本位于包含拓扑的 jar 文件内的 `/resources` 目录中。 因此，此示例将 Python 脚本存储在 `/multilang/resources` 目录中。 `pom.xml` 使用以下 XML 包含此文件：

```xml
<!-- include the Python components -->
<resource>
    <directory>${basedir}/multilang</directory>
    <filtering>false</filtering>
</resource>
```

如前文所述，有一个 `storm.py` 文件，用于实现风暴的 Thrift 定义。 Flux 框架在生成项目时自动包含 `storm.py`，无需额外执行操作。

## <a name="build-the-project"></a>生成项目

1. 从 [https://github.com/Azure-Samples/hdinsight-python-storm-wordcount](https://github.com/Azure-Samples/hdinsight-python-storm-wordcount) 下载项目。

1. 打开命令提示符并导航到项目 root： `hdinsight-python-storm-wordcount-master`。 输入以下命令：

    ```cmd
    mvn clean compile package
    ```

    此命令可创建 `target/WordCount-1.0-SNAPSHOT.jar` 文件，其中包含已编译的拓扑。

## <a name="run-the-storm-topology-on-hdinsight"></a>在 HDInsight 上运行 Storm 拓扑

1. 使用[ssh 命令](../hdinsight-hadoop-linux-use-ssh-unix.md)将 `WordCount-1.0-SNAPSHOT.jar` 文件复制到你的 HDInsight 群集上的风暴。 将 CLUSTERNAME 替换为群集名称，然后输入以下命令，以编辑以下命令：

    ```cmd
    scp target/WordCount-1.0-SNAPSHOT.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:
    ```

1. 文件上传后，使用 SSH 连接到群集：

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. 在 SSH 会话中，使用以下命令在群集上启动拓扑：

    ```bash
    storm jar WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux -r -R /topology.yaml
    ```

    启动后，Storm 拓扑会一直运行，直到被停止。

1. 使用风暴 UI 查看群集上的拓扑。 Storm UI 位于 `https://CLUSTERNAME.azurehdinsight.net/stormui`。 将 `CLUSTERNAME` 替换为群集名称。

1. 停止风暴拓扑。 使用以下命令停止群集上的拓扑：

    ```bash
    storm kill wordcount
    ```

    或者，可以使用 "风暴" UI。 在拓扑的**拓扑操作**下，选择 "**终止**"。

## <a name="run-the-topology-locally"></a>在本地运行拓扑

若要在本地运行拓扑，请使用以下命令：

```bash
storm jar WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux -l -R /topology.yaml
```

> [!NOTE]  
> 此命令要求提供本地 Storm 开发环境。 有关详细信息，请参阅[设置开发环境](https://storm.apache.org/releases/current/Setting-up-development-environment.html)。

拓扑启动后，它会向本地控制台发出类似如下文本的信息：

```output
24302 [Thread-25-sentence-spout-executor[4 4]] INFO  o.a.s.s.ShellSpout - ShellLog pid:2436, name:sentence-spout Emiting the cow jumped over the moon
24302 [Thread-30] INFO  o.a.s.t.ShellBolt - ShellLog pid:2438, name:splitter-bolt Emitting the
24302 [Thread-28] INFO  o.a.s.t.ShellBolt - ShellLog pid:2437, name:counter-bolt Emitting years:160
24302 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=the, count=599}
24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=seven, count=302}
24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=dwarfs, count=143}
24303 [Thread-25-sentence-spout-executor[4 4]] INFO  o.a.s.s.ShellSpout - ShellLog pid:2436, name:sentence-spout Emiting the cow jumped over the moon
24303 [Thread-30] INFO  o.a.s.t.ShellBolt - ShellLog pid:2438, name:splitter-bolt Emitting cow
24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=four, count=160}
```

若要停止拓扑，请使用 __Ctrl + C__。

## <a name="next-steps"></a>后续步骤

有关将 Python 与 HDInsight 配合使用的其他方式，请参阅以下文档：[如何在 Apache Pig 和 Apache Hive 中使用 Python 用户定义的函数（UDF）](../hadoop/python-udf-hdinsight.md)。
