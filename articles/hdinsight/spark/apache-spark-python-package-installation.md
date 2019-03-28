---
title: 脚本操作 - 在 Azure HDInsight 上安装包含 Jupyter 的 Python 包
description: 逐步说明如何使用脚本操作配置 HDInsight Spark 群集随附的 Jupyter notebook，以使用外部 python 包。
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: hrasheed
ms.openlocfilehash: 8bc44949d804349de37796a2695edbdc64693edf
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2019
ms.locfileid: "58518671"
---
# <a name="use-script-action-to-install-external-python-packages-for-jupyter-notebooks-in-apache-spark-clusters-on-hdinsight"></a>在 HDInsight 上的 Apache Spark 群集中使用建本操作安装适用于 Jupyter 笔记本的外部 Python 包
> [!div class="op_single_selector"]
> * [使用单元格 magic](apache-spark-jupyter-notebook-use-external-packages.md)
> * [使用脚本操作](apache-spark-python-package-installation.md)

了解如何使用脚本操作来配置[Apache Spark](https://spark.apache.org/)上使用外部、 由社区贡献的 HDInsight 群集**python**包不包含在群集中的框。

> [!NOTE]  
> 还可使用 `%%configure` magic 配置 Jupyter notebook 以使用外部包。 有关说明，请参阅[在 HDInsight 上的 Apache Spark 群集中将外部包与 Jupyter notebook 配合使用](apache-spark-jupyter-notebook-use-external-packages.md)。

可以在 [包索引](https://pypi.python.org/pypi) 中搜索可用包的完整列表。 也可以从其他源获取可用包的列表。 例如，可以安装通过 [conda-forge](https://conda-forge.org/feedstocks/) 提供的包。

本文介绍如何使用脚本操作在群集上安装 [TensorFlow](https://www.tensorflow.org/) 包并通过 Jupyter Notebook 使用它作为示例。

## <a name="prerequisites"></a>必备组件
必须满足以下条件：

* Azure 订阅。 请参阅 [获取 Azure 免费试用版](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。
* HDInsight 上的 Apache Spark 群集。 有关说明，请参阅[在 Azure HDInsight 中创建 Apache Spark 群集](apache-spark-jupyter-spark-sql.md)。

   > [!NOTE]  
   > 如果 HDInsight Linux 上还没有 Spark 群集，则可以在群集创建过程中运行脚本操作。 访问有关[如何使用自定义脚本操作](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux)的文档。
   
## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>支持 HDInsight 群集上使用的开源软件

Microsoft Azure HDInsight 服务使用围绕 Apache Hadoop 构建的开源技术生态系统。 Microsoft Azure 为开源技术提供常规级别的支持。 有关详细信息，请参阅 [Azure 支持常见问题解答网站](https://azure.microsoft.com/support/faq/)上的**支持范围**部分。 HDInsight 服务为内置组件提供附加的支持级别。

HDInsight 服务中有两种类型的开放源代码组件：

* **内置组件** - 这些组件预先安装在 HDInsight 群集上，并提供在群集的核心功能。 例如，Apache Hadoop YARN ResourceManager、Apache Hive 查询语言 (HiveQL) 及 Mahout 库均属于此类别。 [HDInsight 提供的 Apache Hadoop 群集版本的新增功能](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning)中提供了群集组件的完整列表。
* **自定义组件** - 作为群集用户，可以安装，或者在工作负荷中使用由社区提供的或自己创建的任何组件。

> [!IMPORTANT]   
> HDInsight 群集提供的组件受到完全支持。 Microsoft 支持部门可帮助找出并解决与这些组件相关的问题。
>
> 自定义组件可获得合理范围的支持，有助于进一步解决问题。 Microsoft 支持部门也许能够解决问题，也可能要求你参与可用的开放源代码技术渠道，获取该技术的深入专业知识。 例如，有许多可以使用的社区站点，例如：[面向 HDInsight 的 MSDN 论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight)、[https://stackoverflow.com](https://stackoverflow.com)。 此外，Apache 项目在 [https://apache.org](https://apache.org) 上有项目站点，例如：[Hadoop](https://hadoop.apache.org/)。


## <a name="use-external-packages-with-jupyter-notebooks"></a>将外部包与 Jupyter 笔记本配合使用

1. 从[Azure 门户](https://portal.azure.com/)，导航到群集。  

2. 与你的群集，左窗格中，从下面选择**设置**，选择**脚本操作**。

3. 选择 **+ 提交新**。

4. 输入以下值：**提交脚本操作**窗口：  


    |参数 | 值 |
    |---|---|
    |脚本类型 | 从下拉列表中选择“- 自定义”。|
    |名称 |在文本框中输入 `tensorflow`。|
    |Bash 脚本 URI |在文本框中输入 `https://hdiconfigactions.blob.core.windows.net/linuxtensorflow/tensorflowinstall.sh`。 |
    |节点类型 | 选择**Head**，并**辅助**复选框。 |

    `tensorflowinstall.sh` 包含以下命令：

    ```bash
    #!/usr/bin/env bash
    /usr/bin/anaconda/bin/conda install -c conda-forge tensorflow
    ```

5. 选择“创建” 。  访问有关[如何使用自定义脚本操作](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux)的文档。

6. 等待脚本来完成。  **脚本操作**窗格将状态**在当前的群集操作完成后，可以提交新脚本操作**执行脚本时。  可以从 Ambari UI 中查看进度栏**后台操作**窗口。

7. 打开 PySpark Jupyter 笔记本。  请参阅[创建 Jupyter notebook 在 Spark HDInsight 上](./apache-spark-jupyter-notebook-kernels.md#create-a-jupyter-notebook-on-spark-hdinsight)的步骤。

    ![创建新的 Jupyter 笔记本](./media/apache-spark-python-package-installation/hdinsight-spark-create-notebook.png "创建新的 Jupyter 笔记本")

8. 现在将 `import tensorflow` 并运行 hello world 示例。 输入以下代码：

    ```
    import tensorflow as tf
    hello = tf.constant('Hello, TensorFlow!')
    sess = tf.Session()
    print(sess.run(hello))
    ```

    结果如下所示：
    
    ![TensorFlow 代码执行](./media/apache-spark-python-package-installation/execution.png "执行 TensorFlow 代码")

> [!NOTE]  
> 群集中有两个 python 安装。 Spark 将使用位于 `/usr/bin/anaconda/bin` 的 Anaconda python 安装并将默认为 Python 2.7 环境。 若要使用 Python 3.x 并将包安装到 PySpark3 内核中，请使用指向适用于该环境的 `conda` 可执行文件的路径，并使用 `-n` 参数来指定环境。 例如，`/usr/bin/anaconda/envs/py35/bin/conda install -c conda-forge ggplot -n py35` 命令使用 `conda-forge` 通道将 `ggplot` 包安装到 Python 3.5 环境。

## <a name="seealso"></a>另请参阅
* [概述：Azure HDInsight 上的 Apache Spark](apache-spark-overview.md)

### <a name="scenarios"></a>方案
* [Apache Spark 与 BI：将 HDInsight 中的 Spark 与 BI 工具配合使用来执行交互式数据分析](apache-spark-use-bi-tools.md)
* [Apache Spark 与机器学习：使用 HDInsight 中的 Spark 来通过 HVAC 数据分析建筑物温度](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark 与机器学习：使用 HDInsight 中的 Spark 预测食品检验结果](apache-spark-machine-learning-mllib-ipython.md)
* [使用 HDInsight 中的 Apache Spark 分析网站日志](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>创建和运行应用程序
* [使用 Scala 创建独立的应用程序](apache-spark-create-standalone-application.md)
* [使用 Apache Livy 在 Apache Spark 群集中远程运行作业](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>工具和扩展
* [在 HDInsight 上的 Apache Spark 群集中将外部包与 Jupyter notebook 配合使用](apache-spark-jupyter-notebook-use-external-packages.md)
* [使用适用于 IntelliJ IDEA 的 HDInsight 工具插件创建和提交 Spark Scala 应用程序](apache-spark-intellij-tool-plugin.md)
* [使用适用于 IntelliJ IDEA 的 HDInsight 工具插件远程调试 Apache Spark 应用程序](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [在 HDInsight 上的 Apache Spark 群集中使用 Apache Zeppelin 笔记本](apache-spark-zeppelin-notebook.md)
* [在 HDInsight 的 Apache Spark 群集中可用于 Jupyter Notebook 的内核](apache-spark-jupyter-notebook-kernels.md)
* [Install Jupyter on your computer and connect to an HDInsight Spark cluster（在计算机上安装 Jupyter 并连接到 HDInsight Spark 群集）](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>管理资源
* [管理 Azure HDInsight 中 Apache Spark 群集的资源](apache-spark-resource-manager.md)
* [Track and debug jobs running on an Apache Spark cluster in HDInsight（跟踪和调试 HDInsight 中的 Apache Spark 群集上运行的作业）](apache-spark-job-debugging.md)
