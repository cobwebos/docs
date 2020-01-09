---
title: 在 Azure HDInsight 上安装并使用 Giraph
description: 了解如何使用脚本操作在 HDInsight 群集上安装 Giraph。 可以使用 Giraph 在 Azure 云中 Apache Hadoop 执行图形处理。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/26/2019
ms.openlocfilehash: 1f6fd88ec492f26f6819dff099ec8fe53364ba0b
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/31/2019
ms.locfileid: "75552247"
---
# <a name="install-apache-giraph-on-hdinsight-hadoop-clusters-and-use-giraph-to-process-large-scale-graphs"></a>在 HDInsight Hadoop 群集上安装 Apache Giraph 并使用 Giraph 处理大型图形

了解如何在 HDInsight 群集上安装 Apache Giraph。 HDInsight 的脚本操作功能允许通过运行 bash 脚本来自定义群集。 可以在创建群集期间或之后使用脚本来自定义群集。

## <a name="what-is-giraph"></a>什么是 Giraph

[Apache Giraph](https://giraph.apache.org/) 允许使用 Hadoop 执行图形处理，并可以在 Azure HDInsight 上使用。 Graphs 对各个对象之间的关系进行建模。 例如，大型网络（例如 Internet）上的路由器之间的连接，或者社交网络上人们之间的关系。 通过图形处理，可以推理图形中对象之间的关系，例如：

* 根据当前的关系识别潜在的朋友。

* 识别网络中两台计算机之间的最短路由。

* 计算网页的排名。

> [!WARNING]  
> 随 HDInsight 群集提供的组件享有完全支持权利 - Microsoft 支持部门将帮助找出并解决与这些组件相关的问题。
>
> 自定义组件（如 Giraph）可获得合理范围的支持，以帮助你进一步排查问题。 Microsoft 支持部门也许能够解决问题。 如果不能，必须去开源社区查阅资料，可以在那里找到关于该技术的深层专业知识。 有许多可以使用的社区站点，例如：[HDInsight 的 MSDN 论坛](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight)和 [https://stackoverflow.com](https://stackoverflow.com)。 此外，Apache 项目在[https://apache.org](https://apache.org)上有项目站点，例如[Hadoop](https://hadoop.apache.org/)。

## <a name="what-the-script-does"></a>脚本功能

此脚本可执行以下操作：

* 将 Giraph 安装到 `/usr/hdp/current/giraph`。

* 将 `giraph-examples.jar` 文件复制到群集的默认存储（WASB）： `/example/jars/giraph-examples.jar`。

## <a name="install-giraph-using-script-actions"></a>使用脚本操作安装 Giraph

`https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh` 提供了一个用于在 HDInsight 群集上安装 Giraph 的示例脚本

本部分说明了如何在通过 Azure 门户创建群集时使用示例脚本。

> [!NOTE]  
> 可以通过下列任一方法应用脚本操作：
> * Azure PowerShell
> * Azure CLI
> * HDInsight .NET SDK
> * Azure 资源管理器模板
> 
> 也可以将脚本操作应用于已在运行的群集。 有关详细信息，请参阅[使用脚本操作自定义 HDInsight 群集](hdinsight-hadoop-customize-cluster-linux.md)。

1. 使用[创建基于 Linux 的 HDInsight 群集](hdinsight-hadoop-create-linux-clusters-portal.md)中的步骤开始创建群集，但不要完成创建。 需要使用**经典创建体验**和**自定义（大小、设置、应用）** 。

1. 在 "**群集大小**" 部分中，确保在此示例中，"**工作节点数**" 至少为2。

1. 在 "**脚本操作**" 部分中，提供以下信息：

    |属性 |值 |
    |---|---|
    |脚本类型|- Custom|
    |名称|安装 Giraph|
    |Bash 脚本 URI|`https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh`|
    |节点类型|Head|
    |参数|留空|

    有关详细信息，请参阅[在群集创建过程中使用脚本操作](./hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation)。

1. 根据[创建基于 Linux 的 HDInsight 群集](hdinsight-hadoop-create-linux-clusters-portal.md)中所述继续创建群集。

## <a name="how-do-i-use-giraph-in-hdinsight"></a>如何在 HDInsight 中使用 Giraph？

创建群集后，便可执行以下步骤来运行 Giraph 附带的 SimpleShortestPathsComputation 示例。 此示例使用基本 [Pregel](https://people.apache.org/~edwardyoon/documents/pregel.pdf) 实现来查找图形中对象之间的最短路径。

1. 使用[ssh 命令](./hdinsight-hadoop-linux-use-ssh-unix.md)连接到群集。 将 CLUSTERNAME 替换为群集名称，然后输入以下命令，以编辑以下命令：

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. 使用以下命令创建一个名为 **tiny_graph.txt** 的文件：

    ```bash
    nano tiny_graph.txt
    ```

    将以下文本用作此文件的内容：

    ```text
    [0,0,[[1,1],[3,3]]]
    [1,0,[[0,1],[2,2],[3,1]]]
    [2,0,[[1,2],[4,4]]]
    [3,0,[[0,3],[1,1],[4,4]]]
    [4,0,[[3,4],[2,4]]]
    ```

    此数据使用 `[source_id, source_value,[[dest_id], [edge_value],...]]` 格式描述定向图形中对象之间的关系。 每一行代表 `source_id` 对象与一个或多个 `dest_id` 对象之间的关系。 可将 `edge_value` 视为 `source_id` 和 `dest\_id` 之间的连接的强度或距离。

    使用表示对象间距离的值（或权重）绘制图形后，上述数据看起来可能与下图类似：

    ![tiny_graph.txt 中的对象绘制为圆圈，线条表示对象之间的不同距离](./media/hdinsight-hadoop-giraph-install-linux/hdinsight-giraph-graph.png)

3. 要保存文件，请使用 **Ctrl+X**，然后输入“Y”，最后按 **Enter** 以接受文件名。

4. 使用以下命令将数据存储到 HDInsight 群集的主存储中：

    ```bash
    hdfs dfs -put tiny_graph.txt /example/data/tiny_graph.txt
    ```

5. 使用以下命令运行 SimpleShortestPathsComputation 示例：

    ```bash
    yarn jar /usr/hdp/current/giraph/giraph-examples.jar org.apache.giraph.GiraphRunner org.apache.giraph.examples.SimpleShortestPathsComputation -ca mapred.job.tracker=headnodehost:9010 -vif org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFormat -vip /example/data/tiny_graph.txt -vof org.apache.giraph.io.formats.IdWithValueTextOutputFormat -op /example/output/shortestpaths -w 2
    ```

    > [!IMPORTANT]
    > 传递给 `-w` 的值必须小于或等于实际的辅助角色节点数。

    下表介绍了与此命令搭配使用的参数：

   | 参数 | 它的作用 |
   | --- | --- |
   | .jar |包含示例的 jar 文件。 |
   | giraph. GiraphRunner |用于启动示例的类。 |
   | giraph. SimpleShortestPathsComputation。 |使用的示例。 在此示例中，它将计算图形中 ID 1 和所有其他 ID 之间的最短路径。 |
   | -ca mapred |群集的头节点。 |
   | -vif |用于输入数据的输入格式。 |
   | -vip |输入数据文件。 |
   | -vof |输出格式。 在此示例中，ID 和值是纯文本。 |
   | -op |输出位置。 |
   | -w 2 |要使用的辅助角色数目。 在此示例中为 2。 |

    有关这些参数以及与 Giraph 示例搭配使用的其他参数的详细信息，请参阅 [Giraph 快速入门](https://giraph.apache.org/quick_start.html)。

6. 完成该作业后，结果将存储在 **/example/output/shortestpaths**目录中。 输出文件的名称以第**m 部分**开头，并以数字开头，指示第一个、第二个和第二个文件。 使用以下命令查看输出：

    ```bash
    hdfs dfs -text /example/output/shortestpaths/*
    ```

    显示的输出类似于以下文本：

    ```output
    0    1.0
    4    5.0
    2    2.0
    1    0.0
    3    1.0
    ```

    SimpleShortestPathComputation 示例硬编码为从对象 ID 1 开始查找与其他对象间的最短路径。 输出采用 `destination_id` 和 `distance` 的格式。 `distance` 是对象 ID 1 与目标 ID 的边缘之间的行程值（或权重）。

    在可视化此数据的情况下，可以通过体验 ID 1 与所有其他对象之间的最短路径来验证结果。 ID 1 和 ID 4 之间的最短路径为 5。 此值是 ID 1 和3之间的总距离，然后是 ID 3 和4。

    ![将对象绘制为圆圈，并绘制对象之间的最短路径](./media/hdinsight-hadoop-giraph-install-linux/hdinsight-giraph-graph-out.png)

## <a name="next-steps"></a>后续步骤

[在 HDInsight 群集上安装并使用 Hue](hdinsight-hadoop-hue-linux.md)。
