---
title: "在 Azure HDInsight 上生成 Apache Spark 机器学习应用程序 | Microsoft Docs"
description: "有关如何使用 Jupyter 笔记本在 HDInsight Spark 群集中生成 Apache Spark 机器学习应用程序的分步说明"
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: f584ca5e-abee-4b7c-ae58-2e45dfc56bf4
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: nitinme
ms.openlocfilehash: ec69dfb1a0d43b73efbada654175c54bf315a1e5
ms.sourcegitcommit: cf42a5fc01e19c46d24b3206c09ba3b01348966f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/29/2017
---
# <a name="build-apache-spark-machine-learning-applications-on-azure-hdinsight"></a>在 Azure HDInsight 上生成 Apache Spark 机器学习应用程序

了解如何使用 HDInsight 中的 Spark 群集生成 Apache Spark 机器学习应用程序。 本文说明如何使用群集随附的 Jupyter 笔记本来生成和测试此应用程序。 应用程序使用所有群集默认提供的示例 HVAC.csv 数据。

**先决条件：**

必须满足以下条件：

* HDInsight 上的 Apache Spark 群集。 有关说明，请参阅[在 Azure HDInsight 中创建 Apache Spark 群集](apache-spark-jupyter-spark-sql.md)。 

## <a name="data"></a>了解数据集
在开始生成应用程序之前，我们先来了解要为其生成应用程序的数据的结构，以及要对数据执行的分析类型。 

本文使用与 HDInsight 群集关联的 Azure 存储帐户中提供的示例 **HVAC.csv** 数据文件。 该文件位于存储帐户中的 **\HdiSamples\HdiSamples\SensorSampleData\hvac** 位置。 下载并打开该 CSV 文件，以获取数据的快照。  

![用于 Spark 机器学习示例的数据的快照](./media/apache-spark-ipython-notebook-machine-learning/spark-machine-learning-understand-data.png "用于 Spark 机器学习示例的数据的快照")

该数据显示装有 HVAC 系统的建筑物的目标温度和实际温度。 我们假设 **System** 列代表系统 ID，**SystemAge** 列代表建筑物安装 HVAC 系统的年数。

在指定系统 ID 和系统年数的情况下，我们可以使用这些数据来预测建筑物的温度比目标温度高还是低。

## <a name="app"></a>使用 Spark MLlib 编写 Spark 机器学习应用程序
在此应用程序中，我们使用 Spark ML 管道来执行文档分类。 在管道中，我们将文档分割成单字、将单字转换成数字特征向量，最后使用特征向量和标签创建预测模型。 执行下列步骤创建应用程序。

1. 在 [Azure 门户](https://portal.azure.com/)上的启动板中，单击 Spark 群集的磁贴（如果已将它固定到启动板）。 也可以单击“全部浏览” > “HDInsight 群集”导航到群集。   
2. 在 Spark 群集边栏选项卡中单击“群集仪表板”，并单击“Jupyter 笔记本”。 出现提示时，请输入群集的管理员凭据。
   
   > [!NOTE]
   > 也可以在浏览器中打开以下 URL 来访问群集的 Jupyter 笔记本。 将 **CLUSTERNAME** 替换为群集的名称：
   > 
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
   > 
   > 
3. 创建新的笔记本。 单击“新建”，并单击“PySpark”。
   
    ![创建用于 Spark 机器学习示例的 Jupyter 笔记本](./media/apache-spark-ipython-notebook-machine-learning/spark-machine-learning-create-notebook.png "创建用于 Spark 机器学习示例的 Jupyter 笔记本")
4. 新笔记本随即已创建，并以 Untitled.pynb 名称打开。 在顶部单击笔记本名称，并输入一个友好名称。
   
    ![提供用于 Spark 机器学习示例的笔记本名称](./media/apache-spark-ipython-notebook-machine-learning/spark-machine-learning-notebook-name.png "提供用于 Spark 机器学习示例的笔记本名称")
5. 使用笔记本是使用 PySpark 内核创建的，因此不需要显式创建任何上下文。 运行第一个代码单元格时，系统会自动创建 Spark 和 Hive 上下文。 首先可以导入此方案所需的类型。 将以下代码段粘贴到空白单元格中，然后按 **Shift+Enter**。 
   
        from pyspark.ml import Pipeline
        from pyspark.ml.classification import LogisticRegression
        from pyspark.ml.feature import HashingTF, Tokenizer
        from pyspark.sql import Row
   
        import os
        import sys
        from pyspark.sql.types import *
   
        from pyspark.mllib.classification import LogisticRegressionWithSGD
        from pyspark.mllib.regression import LabeledPoint
        from numpy import array
6. 现在必须加载数据 (hvac.csv)，分析数据，并使用它来训练模型。 为此，需要定义检查建筑物实际温度是否高于目标温度的函数。 如果实际温度较高，则表示建筑物处于高温状态，以值 **1.0** 表示。 如果实际温度较低，则表示建筑物处于低温状态，以值 **0.0** 表示。 
   
    将以下代码段粘贴到空白单元格中，然后按 **Shift + Enter**。

        # List the structure of data for better understanding. Because the data will be
        # loaded as an array, this structure makes it easy to understand what each element
        # in the array corresponds to

        # 0 Date
        # 1 Time
        # 2 TargetTemp
        # 3 ActualTemp
        # 4 System
        # 5 SystemAge
        # 6 BuildingID

        LabeledDocument = Row("BuildingID", "SystemInfo", "label")

        # Define a function that parses the raw CSV file and returns an object of type LabeledDocument

        def parseDocument(line):
            values = [str(x) for x in line.split(',')]
            if (values[3] > values[2]):
                hot = 1.0
            else:
                hot = 0.0        

            textValue = str(values[4]) + " " + str(values[5])

            return LabeledDocument((values[6]), textValue, hot)

        # Load the raw HVAC.csv file, parse it using the function
        data = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

        documents = data.filter(lambda s: "Date" not in s).map(parseDocument)
        training = documents.toDF()


1. 设置包括三个阶段的 Spark 机器学习管道：tokenizer、hashingTF 和 lr。 有关管道介绍及其工作原理的详细信息，请参阅 <a href="http://spark.apache.org/docs/latest/ml-guide.html#how-it-works" target="_blank">Spark 机器学习管道</a>。
   
    将以下代码段粘贴到空白单元格中，然后按 **Shift + Enter**。
   
        tokenizer = Tokenizer(inputCol="SystemInfo", outputCol="words")
        hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
        lr = LogisticRegression(maxIter=10, regParam=0.01)
        pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])
2. 将管道拟合到培训文档中。 将以下代码段粘贴到空白单元格中，然后按 **Shift + Enter**。
   
        model = pipeline.fit(training)
3. 验证训练文档以根据应用程序的进度创建检查点。 将以下代码段粘贴到空白单元格中，然后按 **Shift + Enter**。
   
        training.show()
   
    此时应会出现与下面类似的输出：
   
        +----------+----------+-----+
        |BuildingID|SystemInfo|label|
        +----------+----------+-----+
        |         4|     13 20|  0.0|
        |        17|      3 20|  0.0|
        |        18|     17 20|  1.0|
        |        15|      2 23|  0.0|
        |         3|      16 9|  1.0|
        |         4|     13 28|  0.0|
        |         2|     12 24|  0.0|
        |        16|     20 26|  1.0|
        |         9|      16 9|  1.0|
        |        12|       6 5|  0.0|
        |        15|     10 17|  1.0|
        |         7|      2 11|  0.0|
        |        15|      14 2|  1.0|
        |         6|       3 2|  0.0|
        |        20|     19 22|  0.0|
        |         8|     19 11|  0.0|
        |         6|      15 7|  0.0|
        |        13|      12 5|  0.0|
        |         4|      8 22|  0.0|
        |         7|      17 5|  0.0|
        +----------+----------+-----+

    返回并根据原始 CSV 文件验证输出。 例如，CSV 文件中第一行包含此数据：

    ![Spark 机器学习示例的输出数据快照](./media/apache-spark-ipython-notebook-machine-learning/spark-machine-learning-output-data.png "Spark 机器学习示例的输出数据快照")

    请注意，实际温度比目标温度低的情况表示建筑物处于低温状态。 因此在训练输出中，第一行中的 **label** 值为 **0.0**，表示建筑物并非处于高温状态。

1. 准备要对其运行已训练模型的数据集。 为此，我们传递了系统 ID 和系统年数（以训练输出中的 **SystemInfo** 表示），模型将预测该系统 ID 和系统年数所代表的建筑物的温度是较高（以 1.0 表示）还是较低（以 0.0 表示）。
   
   将以下代码段粘贴到空白单元格中，然后按 **Shift + Enter**。
   
       # SystemInfo here is a combination of system ID followed by system age
       Document = Row("id", "SystemInfo")
       test = sc.parallelize([(1L, "20 25"),
                     (2L, "4 15"),
                     (3L, "16 9"),
                     (4L, "9 22"),
                     (5L, "17 10"),
                     (6L, "7 22")]) \
           .map(lambda x: Document(*x)).toDF() 
2. 最后，对测试数据进行预测。 将以下代码段粘贴到空白单元格中，然后按 **Shift + Enter**。
   
        # Make predictions on test documents and print columns of interest
        prediction = model.transform(test)
        selected = prediction.select("SystemInfo", "prediction", "probability")
        for row in selected.collect():
            print row
3. 应该会看到与下面类似的输出：
   
       Row(SystemInfo=u'20 25', prediction=1.0, probability=DenseVector([0.4999, 0.5001]))
       Row(SystemInfo=u'4 15', prediction=0.0, probability=DenseVector([0.5016, 0.4984]))
       Row(SystemInfo=u'16 9', prediction=1.0, probability=DenseVector([0.4785, 0.5215]))
       Row(SystemInfo=u'9 22', prediction=1.0, probability=DenseVector([0.4549, 0.5451]))
       Row(SystemInfo=u'17 10', prediction=1.0, probability=DenseVector([0.4925, 0.5075]))
       Row(SystemInfo=u'7 22', prediction=0.0, probability=DenseVector([0.5015, 0.4985]))
   
   从预测中的第一行可以看出，对于 ID 为 20 且系统年数为 25 的 HVAC 系统，建筑物处于高温状态 (**prediction=1.0**)。 DenseVector (0.49999) 的第一个值对应于预测 0.0，第二个值 (0.5001) 对应于预测 1.0。 在输出中，即使第二个值只稍高一点，模型也仍旧显示 **prediction=1.0**。
4. 完成运行应用程序之后，应该要关闭笔记本以释放资源。 为此，请在笔记本的“文件”菜单中，单击“关闭并停止”。 这会关闭笔记本。

## <a name="anaconda"></a>将 Anaconda scikit-learn 库用于 Spark 机器学习
HDInsight 上的 Apache Spark 群集包含 Anaconda 库， 其中包括适用于机器学习的 **scikit-learn** 库。 该库还包含用于直接从 Jupyter 笔记本生成示例应用程序的各种数据集。 有关使用 scikit-learn 库的示例，请参阅 [http://scikit-learn.org/stable/auto_examples/index.html](http://scikit-learn.org/stable/auto_examples/index.html)。

## <a name="seealso"></a>另请参阅
* [概述：Azure HDInsight 上的 Apache Spark](apache-spark-overview.md)

### <a name="scenarios"></a>方案
* [Spark 和 BI：使用 HDInsight 中的 Spark 和 BI 工具执行交互式数据分析](apache-spark-use-bi-tools.md)
* [Spark 和机器学习：使用 HDInsight 中的 Spark 预测食品检查结果](apache-spark-machine-learning-mllib-ipython.md)
* [Spark 流式处理：使用 HDInsight 中的 Spark 生成实时流式处理应用程序](apache-spark-eventhub-streaming.md)
* [使用 HDInsight 中的 Spark 分析网站日志](apache-spark-custom-library-website-log-analysis.md)

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

[hdinsight-weblogs-sample]:../hadoop/apache-hive-analyze-website-log.md
[hdinsight-sensor-data-sample]:../hadoop/apache-hive-analyze-sensor-data.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-create-storageaccount]:../../storage/common/storage-create-storage-account.md
