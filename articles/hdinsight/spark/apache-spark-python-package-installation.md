---
title: 脚本操作 - 在 Azure HDInsight 上使用 Jupyter 安装 Python 包
description: 逐步说明如何使用脚本操作配置可在 HDInsight Spark 群集中使用的 Jupyter 笔记本，以使用外部 python 包。
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: hrasheed
ms.openlocfilehash: 093a5eb7ee366abfdbc4c5dba68739544b438ff2
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/15/2018
ms.locfileid: "51684522"
---
# <a name="use-script-action-to-install-external-python-packages-for-jupyter-notebooks-in-apache-spark-clusters-on-hdinsight"></a>使用脚本操作在 HDInsight 上的 Apache Spark 群集中安装 Jupyter 笔记本的外部 Python 包
> [!div class="op_single_selector"]
> * [使用单元格 magic](apache-spark-jupyter-notebook-use-external-packages.md)
> * [使用脚本操作](apache-spark-python-package-installation.md)
>
>

了解如何使用脚本操作在 HDInsight (Linux) 上配置 Apache Spark 群集，以使用未现成包含在群集中的、由社区贡献的 **python** 外部包。

> [!NOTE]
> 还可使用 `%%configure` magic 配置 Jupyter 笔记本以便使用外部包。 有关说明，请参阅[在 HDInsight 上的 Apache Spark 群集中将外部包与 Jupyter 笔记本配合使用](apache-spark-jupyter-notebook-use-external-packages.md)。
> 
> 

可以在[包索引](https://pypi.python.org/pypi)中搜索可用包的完整列表。 也可以从其他源获取可用包的列表。 例如，可以安装通过 [Anaconda](https://docs.continuum.io/anaconda/pkg-docs) 或 [conda-forge](https://conda-forge.org/feedstocks/) 提供的包。

本文介绍如何使用脚本操作在群集上安装 [TensorFlow](https://www.tensorflow.org/) 包并通过 Jupyter Notebook 使用它作为示例。

## <a name="prerequisites"></a>先决条件
必须满足以下条件：

* Azure 订阅。 请参阅[获取 Azure 免费试用版](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。
* HDInsight 上的 Apache Spark 群集。 有关说明，请参阅[在 Azure HDInsight 中创建 Apache Spark 群集](apache-spark-jupyter-spark-sql.md)。

   > [!NOTE]
   > 如果 HDInsight Linux 上还没有 Spark 群集，则可以在群集创建过程中运行脚本操作。 访问有关[如何使用自定义脚本操作](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux)的文档。
   > 
   > 
   
   ## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>支持 HDInsight 群集上使用的开放源代码软件

Microsoft Azure HDInsight 服务使用围绕 Hadoop 构建的开源技术生态系统。 Microsoft Azure 为开源技术提供常规级别的支持。 有关详细信息，请参阅 [Azure 支持常见问题解答网站](https://azure.microsoft.com/support/faq/)的“支持范围”部分。 HDInsight 服务为内置组件提供附加的支持级别。

HDInsight 服务中有两种类型的开放源代码组件：

* **内置组件** - 这些组件预先安装在 HDInsight 群集上，并提供在群集的核心功能。 例如，Yarn ResourceManager、Hive 查询语言 (HiveQL) 及 Mahout 库均属于此类别。 [HDInsight 提供的 Hadoop 群集版本有哪些新功能](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning)中提供了群集组件的完整列表。
* **自定义组件** - 作为群集用户，可以安装，或者在工作负荷中使用由社区提供的或自己创建的任何组件。

> [!WARNING]
> HDInsight 群集提供的组件受到完全支持。 Microsoft 支持部门可帮助找出并解决与这些组件相关的问题。
>
> 自定义组件可获得合理范围的支持，以帮助你进一步排查问题。 Microsoft 支持部门也许能够解决问题，也可能要求你参与可用的开放源代码技术渠道，获取该技术的深入专业知识。 有许多可以使用的社区站点，例如：[HDInsight 的 MSDN 论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight)和 [http://stackoverflow.com](http://stackoverflow.com)。 此外，Apache 项目在 [http://apache.org](http://apache.org) 上提供了项目站点，例如 [Hadoop](http://hadoop.apache.org/)。


## <a name="use-external-packages-with-jupyter-notebooks"></a>将外部包与 Jupyter 笔记本配合使用

1. 在 [Azure 门户](https://portal.azure.com/)上的启动板中，单击 Spark 群集的磁贴（如果已将它固定到启动板）。 也可以单击“全部浏览” > “HDInsight 群集”导航到群集。   

2. 在 Spark 群集边栏选项卡中，单击左窗格中的“脚本操作”。 使用脚本类型“自定义”并输入脚本操作的友好名称。 在**头节点和工作节点**上运行脚本，并将参数字段留空。 可以从 https://hdiconfigactions.blob.core.windows.net/linuxtensorflow/tensorflowinstall.sh 参考 bash 脚本；访问有关[如何使用自定义脚本操作](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux)的文档。

   > [!NOTE]
   > 群集中有两个 python 安装。 Spark 将使用位于 `/usr/bin/anaconda/bin` 中的 Anaconda python 安装。 通过 `/usr/bin/anaconda/bin/pip` 和 `/usr/bin/anaconda/bin/conda` 在自定义操作中引用该安装。
   > 
   > 

3. 打开 PySpark Jupyter 笔记本

    ![创建新的 Jupyter 笔记本](./media/apache-spark-python-package-installation/hdinsight-spark-create-notebook.png "创建新的 Jupyter 笔记本")

4. 新笔记本随即已创建，并以 Untitled.pynb 名称打开。 在顶部单击笔记本名称，并输入一个友好名称。

    ![提供笔记本的名称](./media/apache-spark-python-package-installation/hdinsight-spark-name-notebook.png "提供笔记本的名称")

5. 现在将`import tensorflow` 并运行 hello world 示例。 

    要复制的代码：

        import tensorflow as tf
        hello = tf.constant('Hello, TensorFlow!')
        sess = tf.Session()
        print(sess.run(hello))

    结果如下所示：
    
    ![执行 TensorFlow 代码](./media/apache-spark-python-package-installation/execution.png "执行 TensorFlow 代码")

## <a name="seealso"></a>另请参阅
* [概述：Azure HDInsight 上的 Apache Spark](apache-spark-overview.md)

### <a name="scenarios"></a>方案
* [Spark 和 BI：使用 HDInsight 中的 Spark 和 BI 工具执行交互式数据分析](apache-spark-use-bi-tools.md)
* [Spark 和机器学习：使用 HDInsight 中的 Spark 对使用 HVAC 数据生成温度进行分析](apache-spark-ipython-notebook-machine-learning.md)
* [Spark 和机器学习：使用 HDInsight 中的 Spark 预测食品检查结果](apache-spark-machine-learning-mllib-ipython.md)
* [使用 HDInsight 中的 Spark 分析网站日志](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>创建和运行应用程序
* [使用 Scala 创建独立的应用程序](apache-spark-create-standalone-application.md)
* [使用 Livy 在 Spark 群集中远程运行作业](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>工具和扩展
* [在 HDInsight 上的 Apache Spark 群集中将外部包与 Jupyter 笔记本配合使用](apache-spark-jupyter-notebook-use-external-packages.md)
* [使用适用于 IntelliJ IDEA 的 HDInsight 工具插件创建和提交 Spark Scala 应用程序](apache-spark-intellij-tool-plugin.md)
* [Use HDInsight Tools Plugin for IntelliJ IDEA to debug Spark applications remotely（使用 IntelliJ IDEA 的 HDInsight 工具插件远程调试 Spark 应用程序）](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [在 HDInsight 上的 Spark 群集中使用 Zeppelin 笔记本](apache-spark-zeppelin-notebook.md)
* [在 HDInsight 的 Spark 群集中可用于 Jupyter 笔记本的内核](apache-spark-jupyter-notebook-kernels.md)
* [Install Jupyter on your computer and connect to an HDInsight Spark cluster（在计算机上安装 Jupyter 并连接到 HDInsight Spark 群集）](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>管理资源
* [管理 Azure HDInsight 中 Apache Spark 群集的资源](apache-spark-resource-manager.md)
* [Track and debug jobs running on an Apache Spark cluster in HDInsight（跟踪和调试 HDInsight 中的 Apache Spark 群集上运行的作业）](apache-spark-job-debugging.md)
