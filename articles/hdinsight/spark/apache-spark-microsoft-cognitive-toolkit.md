---
title: "用于进行深度学习的包含 Azure HDInsight Spark 的 Microsoft 认知工具包 | Microsoft Docs"
description: "了解如何在 Azure HDInsight Spark 群集中使用 Spark Python API 将定型的 Microsoft 认知工具包深入学习模型应用于数据集。"
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: nitinme
ms.openlocfilehash: e4bf0b176817daf3c128f26f407cde2ce1adba4c
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/29/2017
---
# <a name="use-microsoft-cognitive-toolkit-deep-learning-model-with-azure-hdinsight-spark-cluster"></a>将 Microsoft 认知工具包深入学习模型与 Azure HDInsight Spark 群集配合使用

在本文中，将执行以下步骤。

1. 运行自定义脚本，在 Azure HDInsight Spark 群集上安装 Microsoft 认知工具包。

2. 将 Jupyter 笔记本上传到 Spark 群集，以了解如何使用 [Spark Python API (PySpark)](https://spark.apache.org/docs/0.9.0/python-programming-guide.html) 将定型的 Microsoft 认知工具包深入学习模型应用于 Azure Blob 存储帐户中的文件

## <a name="prerequisites"></a>先决条件

* **一个 Azure 订阅**。 在开始学习本教程之前，必须有一个 Azure 订阅。 请参阅[立即创建免费 Azure 帐户](https://azure.microsoft.com/free)。

* **Azure HDInsight Spark 群集**。 在本文中，将创建 Spark 2.0 群集。 有关说明，请参阅[在 Azure HDInsight 中创建 Apache Spark 群集](apache-spark-jupyter-spark-sql.md)。

## <a name="how-does-this-solution-flow"></a>此解决方案的流程如何？

此解决方案分为两部分，即本文和作为本教程的一部分上传的 Jupyter 笔记本。 在本文中，完成以下步骤：

* 在 HDInsight Spark 群集上运行脚本操作，安装 Microsoft 认知工具包和 Python 包。
* 将运行解决方案的 Jupyter 笔记本上传到 HDInsight Spark 群集中。

以下其余步骤涵盖在 Jupyter 笔记本中。

- 将示例图像加载到 Spark 弹性分布式数据集或 RDD 中
   - 加载模块并定义预设
   - 将数据集下载到本地 Spark 群集上
   - 将数据集格转换为 RDD
- 使用定型的认知工具包模型对图像评分
   - 将定型的认知工具包模型下载到 Spark 群集
   - 定义由辅助角色节点使用的函数
   - 对辅助角色节点上的图像评分
   - 评估模型准确性


## <a name="install-microsoft-cognitive-toolkit"></a>安装 Microsoft 认知工具包

可以使用脚本操作在 Spark 群集上安装 Microsoft 认知工具包。 脚本操作使用自定义脚本在群集上安装默认情况下未提供的组件。 可以从 Azure 门户、通过使用 HDInsight .NET SDK 或 Azure PowerShell，来使用自定义脚本。 还可以在创建群集过程中或者在群集已启动并运行之后使用脚本安装工具包。 

在本文中，我们在群集创建完成后使用门户安装该工具包。 有关运行自定义脚本的其他方式，请参阅[使用脚本操作自定义 HDInsight 群集](../hdinsight-hadoop-customize-cluster-linux.md)。

### <a name="using-the-azure-portal"></a>使用 Azure 门户

有关如何使用 Azure 门户运行脚本操作的说明，请参阅[使用脚本操作自定义 HDInsight 群集](../hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation)。 确保提供以下输入，以便安装 Microsoft 认知工具包。

* 提供脚本操作名称的值。

* 对于 **Bash 脚本 URI**，输入 `https://raw.githubusercontent.com/Azure-Samples/hdinsight-pyspark-cntk-integration/master/cntk-install.sh`。

* 请确保仅在头节点和工作节点上运行脚本并清除其他所有复选框。

* 单击“创建” 。

## <a name="upload-the-jupyter-notebook-to-azure-hdinsight-spark-cluster"></a>将 Jupyter 笔记本上传到 Azure HDInsight Spark 群集

要将 Microsoft 认知工具包与 Azure HDInsight Spark 群集配合使用，必须将 Jupyter 笔记本 **CNTK_model_scoring_on_Spark_walkthrough.ipynb** 加载到 Azure HDInsight Spark 群集中。 此笔记本可在 GitHub 上通过以下地址获取：[https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration](https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration)。

1. 克隆 GitHub 存储库 [https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration](https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration)。 有关克隆的说明，请参阅 [Cloning a repository](https://help.github.com/articles/cloning-a-repository/)（克隆存储库）。

2. 从 Azure 门户中，打开已预配的“Spark 群集”边栏选项卡，依次单击“群集仪表板”和“Jupyter 笔记本”。

    也可以通过转到 URL `https://<clustername>.azurehdinsight.net/jupyter/` 来启动 Jupyter 笔记本。 将 \<clustername> 替换为 HDInsight 群集名。

3. 从 Jupyter 笔记本中，单击右上角的“上传”，并导航至克隆 GitHub 存储库的位置。

    ![将 Jupyter 笔记本上传到 Azure HDInsight Spark 群集](./media/apache-spark-microsoft-cognitive-toolkit/hdinsight-microsoft-cognitive-toolkit-load-jupyter-notebook.png "Upload Jupyter notebook to Azure HDInsight Spark cluster")

4. 再次单击“上传”。

5. 笔记本上传后，单击笔记本的名称，并按照笔记本中有关如何加载数据集和执行教程的说明进行操作。

## <a name="see-also"></a>另请参阅
* [概述：Azure HDInsight 上的 Apache Spark](apache-spark-overview.md)

### <a name="scenarios"></a>方案
* [Spark 和 BI：使用 HDInsight 中的 Spark 和 BI 工具执行交互式数据分析](apache-spark-use-bi-tools.md)
* [Spark 和机器学习：使用 HDInsight 中的 Spark 对使用 HVAC 数据生成温度进行分析](apache-spark-ipython-notebook-machine-learning.md)
* [Spark 和机器学习：使用 HDInsight 中的 Spark 预测食品检查结果](apache-spark-machine-learning-mllib-ipython.md)
* [Spark 流式处理：使用 HDInsight 中的 Spark 生成实时流式处理应用程序](apache-spark-eventhub-streaming.md)
* [使用 HDInsight 中的 Spark 分析网站日志](apache-spark-custom-library-website-log-analysis.md)
* [Application Insight telemetry data analysis using Spark in HDInsight（使用 HDInsight 中的 Spark 执行 Application Insight 遥测数据分析）](apache-spark-analyze-application-insight-logs.md)

### <a name="create-and-run-applications"></a>创建和运行应用程序
* [使用 Scala 创建独立的应用程序](apache-spark-create-standalone-application.md)
* [使用 Livy 在 Spark 群集中远程运行作业](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>工具和扩展
* [使用适用于 IntelliJ IDEA 的 HDInsight 工具插件创建和提交 Spark Scala 应用程序](apache-spark-intellij-tool-plugin.md)
* [Use HDInsight Tools Plugin for IntelliJ IDEA to debug Spark applications remotely（使用 IntelliJ IDEA 的 HDInsight 工具插件远程调试 Spark 应用程序）](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [在 HDInsight 上的 Spark 群集中使用 Zeppelin 笔记本](apache-spark-zeppelin-notebook.md)
* [在 HDInsight 的 Spark 群集中可用于 Jupyter 笔记本的内核](apache-spark-jupyter-notebook-kernels.md)
* [Use external packages with Jupyter notebooks（将外部包与 Jupyter 笔记本配合使用）](apache-spark-jupyter-notebook-use-external-packages.md)
* [Install Jupyter on your computer and connect to an HDInsight Spark cluster（在计算机上安装 Jupyter 并连接到 HDInsight Spark 群集）](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>管理资源
* [管理 Azure HDInsight 中 Apache Spark 群集的资源](apache-spark-resource-manager.md)
* [Track and debug jobs running on an Apache Spark cluster in HDInsight（跟踪和调试 HDInsight 中的 Apache Spark 群集上运行的作业）](apache-spark-job-debugging.md)

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-create-storageaccount]:../../storage/common/storage-create-storage-account.md
