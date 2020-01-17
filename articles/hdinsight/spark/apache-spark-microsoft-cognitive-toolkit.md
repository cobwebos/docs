---
title: 与 Apache Spark Microsoft Cognitive Toolkit-Azure HDInsight
description: 了解如何在 Azure HDInsight Spark 群集中使用 Spark Python API 将定型的 Microsoft 认知工具包深入学习模型应用于数据集。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/14/2020
ms.openlocfilehash: 587de168a17c407abf3c2a7797969df013760a9f
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2020
ms.locfileid: "76156635"
---
# <a name="use-microsoft-cognitive-toolkit-deep-learning-model-with-azure-hdinsight-spark-cluster"></a>将 Microsoft 认知工具包深入学习模型与 Azure HDInsight Spark 群集配合使用

在本文中，将执行以下步骤。

1. 运行自定义脚本，以在 Azure HDInsight Spark 群集上安装 [Microsoft Cognitive Toolkit](https://docs.microsoft.com/cognitive-toolkit/)。

2. 将 [Jupyter Notebook](https://jupyter.org/) 上传到 [Apache Spark](https://spark.apache.org/) 群集，以了解如何使用 [Spark Python API (PySpark)](https://spark.apache.org/docs/latest/api/python/index.html) 将定型的 Microsoft Cognitive Toolkit 深度学习模型应用于 Azure Blob 存储帐户中的文件

## <a name="prerequisites"></a>必备组件

* HDInsight 上的 Apache Spark 群集。 请参阅[创建 Apache Spark 群集](./apache-spark-jupyter-spark-sql-use-portal.md)。

* 熟悉 Jupyter Notebook 和 Spark on HDInsight 的结合使用。 有关详细信息，请参阅[使用 Apache Spark on HDInsight 加载数据并运行查询](./apache-spark-load-data-run-query.md)。

## <a name="how-does-this-solution-flow"></a>此解决方案的流程如何？

此解决方案分为本文和您在本文中上传的 Jupyter 笔记本。 在本文中，完成以下步骤：

* 在 HDInsight Spark 群集上运行脚本操作，安装 Microsoft 认知工具包和 Python 包。
* 将运行解决方案的 Jupyter 笔记本上传到 HDInsight Spark 群集中。

以下其余步骤涵盖在 Jupyter 笔记本中。

* 将示例图像加载到 Spark 弹性分布式数据集或 RDD 中。
  * 加载模块并定义预设。
  * 将数据集下载到本地 Spark 群集上。
  * 将数据集格式转换为 RDD。
* 使用定型的 Cognitive Toolkit 模型对图像评分。
  * 将定型的 Cognitive Toolkit 模型下载到 Spark 群集。
  * 定义由辅助角色节点使用的函数。
  * 对辅助角色节点上的图像评分。
  * 评估模型准确性。

## <a name="install-microsoft-cognitive-toolkit"></a>安装 Microsoft 认知工具包

可以使用脚本操作在 Spark 群集上安装 Microsoft 认知工具包。 脚本操作使用自定义脚本在群集上安装默认情况下不可用的组件。 可以通过使用 HDInsight .NET SDK 或使用 Azure PowerShell 从 Azure 门户中使用自定义脚本。 还可以在创建群集过程中或者在群集已启动并运行之后使用脚本安装工具包。

在本文中，我们在群集创建完成后使用门户安装该工具包。 有关运行自定义脚本的其他方式，请参阅[使用脚本操作自定义 HDInsight 群集](../hdinsight-hadoop-customize-cluster-linux.md)。

### <a name="using-the-azure-portal"></a>使用 Azure 门户

有关如何使用 Azure 门户运行脚本操作的说明，请参阅[使用脚本操作自定义 HDInsight 群集](../hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation)。 确保提供以下输入，以便安装 Microsoft 认知工具包。 对于脚本操作，请使用以下值：

|属性 |值 |
|---|---|
|脚本类型|- Custom|
|名称| 安装 MCT|
|Bash 脚本 URI|`https://raw.githubusercontent.com/Azure-Samples/hdinsight-pyspark-cntk-integration/master/cntk-install.sh`|
|节点类型：|Head，辅助角色|
|参数|无|

## <a name="upload-the-jupyter-notebook-to-azure-hdinsight-spark-cluster"></a>将 Jupyter 笔记本上传到 Azure HDInsight Spark 群集

要将 Microsoft 认知工具包与 Azure HDInsight Spark 群集配合使用，必须将 Jupyter 笔记本 **CNTK_model_scoring_on_Spark_walkthrough.ipynb** 加载到 Azure HDInsight Spark 群集中。 GitHub 上的 [https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration](https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration) 位置提供了此 Notebook。

1. 下载并解压缩[https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration](https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration)。

1. 在 web 浏览器中，导航到 `https://CLUSTERNAME.azurehdinsight.net/jupyter`，其中 `CLUSTERNAME` 是群集的名称。

1. 在 Jupyter 笔记本中，选择右上角的 "**上传**"，并导航到 "下载" 并选择 "文件" `CNTK_model_scoring_on_Spark_walkthrough.ipynb`。

    ![将 Jupyter 笔记本上传到 Azure HDInsight Spark 群集](./media/apache-spark-microsoft-cognitive-toolkit/hdinsight-microsoft-cognitive-toolkit-load-jupyter-notebook.png "将 Jupyter 笔记本上传到 Azure HDInsight Spark 群集")

1. 再次选择 "**上传**"。

1. 上传笔记本后，单击笔记本的名称，并按照笔记本中有关如何加载数据集和执行此文章的说明进行操作。

## <a name="see-also"></a>另请参阅

* [概述：Azure HDInsight 上的 Apache Spark](apache-spark-overview.md)

### <a name="scenarios"></a>方案

* [Apache Spark 和 BI：使用 HDInsight 中的 Spark 和 BI 工具执行交互式数据分析](apache-spark-use-bi-tools.md)
* [Apache Spark 和机器学习：使用 HDInsight 中的 Spark 结合 HVAC 数据分析建筑物温度](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark 和机器学习：使用 HDInsight 中的 Spark 预测食品检查结果](apache-spark-machine-learning-mllib-ipython.md)
* [使用 HDInsight 中的 Apache Spark 分析网站日志](apache-spark-custom-library-website-log-analysis.md)
* [使用 HDInsight 中的 Apache Spark 执行 Application Insight 遥测数据分析](apache-spark-analyze-application-insight-logs.md)

### <a name="create-and-run-applications"></a>创建和运行应用程序

* [使用 Scala 创建独立的应用程序](apache-spark-create-standalone-application.md)
* [使用 Apache Livy 在 Apache Spark 群集中远程运行作业](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>工具和扩展

* [使用适用于 IntelliJ IDEA 的 HDInsight 工具插件创建和提交 Spark Scala 应用程序](apache-spark-intellij-tool-plugin.md)
* [使用适用于 IntelliJ IDEA 的 HDInsight 工具插件远程调试 Apache Spark 应用程序](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [在 HDInsight 上的 Apache Spark 群集中使用 Apache Zeppelin 笔记本](apache-spark-zeppelin-notebook.md)
* [在 HDInsight 的 Apache Spark 群集中可用于 Jupyter Notebook 的内核](apache-spark-jupyter-notebook-kernels.md)
* [Use external packages with Jupyter notebooks（将外部包与 Jupyter 笔记本配合使用）](apache-spark-jupyter-notebook-use-external-packages.md)
* [Install Jupyter on your computer and connect to an HDInsight Spark cluster（在计算机上安装 Jupyter 并连接到 HDInsight Spark 群集）](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>管理资源

* [管理 Azure HDInsight 中 Apache Spark 群集的资源](apache-spark-resource-manager.md)
* [Track and debug jobs running on an Apache Spark cluster in HDInsight（跟踪和调试 HDInsight 中的 Apache Spark 群集上运行的作业）](apache-spark-job-debugging.md)
