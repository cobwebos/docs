---
title: "HDInsight 上 Spark MLlib 的机器学习示例 — Azure | Microsoft Docs"
description: "了解如何使用 Spark MLlib 创建机器学习应用，以通过逻辑回归使用分类对数据集进行分析。"
keywords: "spark 机器学习, spark 机器学习示例"
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: c0fd4baa-946d-4e03-ad2c-a03491bd90c8
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/11/2017
ms.author: jgao
ms.openlocfilehash: 0e1d7b46aeaf8f21fdf2942f986643746dad3313
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2018
---
# <a name="use-spark-mllib-to-build-a-machine-learning-application-and-analyze-a-dataset"></a>使用 Spark MLlib 生成机器学习应用程序并分析数据集

了解如何使用 Spark **MLlib** 创建机器学习应用程序，以便对打开的数据集执行简单预测分析。 在 Spark 的内置机器学习库中，本示例通过逻辑回归使用*分类*。 

> [!TIP]
> 本示例也适用于在 HDInsight 中创建的 Spark (Linux) 群集上的 Jupyter 笔记本。 笔记本体验可让你从笔记本本身运行 Python 代码片段。 若要从笔记本内部执行本教程，请创建 Spark 群集，启动 Jupyter 笔记本 (`https://CLUSTERNAME.azurehdinsight.net/jupyter`)。 然后运行 **Python** 文件夹中的笔记本“机器学习：使用 MLlib.ipynb 对食品检测数据进行预测分析”。
>
>

MLlib 是一个核心 Spark 库，提供许多可用于机器学习任务的实用工具，其中包括适用于以下任务的实用工具：

* 分类
* 回归
* 群集功能
* 主题建模
* 单值分解 (SVD) 和主体组件分析 (PCA)
* 假设测试和计算示例统计信息

## <a name="what-are-classification-and-logistic-regression"></a>什么是分类和逻辑回归？
*分类*是一种常见的机器学习任务，是将输入数据按类别排序的过程。 它是一种分类算法的作业，旨在算出如何将“标签”分配到提供的输入数据。 例如，可以联想机器学习算法，该算法接受股票信息作为输入并将股票划分为两个类别：应该卖出的股票和应该保留的股票。

逻辑回归是用于分类的算法。 Spark 的逻辑回归 API 可用于 *二元分类*，或将输入数据归类到两组中的一组。 有关逻辑回归的详细信息，请参阅[维基百科](https://en.wikipedia.org/wiki/Logistic_regression)。

总之，逻辑回归的过程会产生 *逻辑函数*，可用于预测输入向量属于一个组或另一个组的概率。  

## <a name="predictive-analysis-example-on-food-inspection-data"></a>食品检测数据的预测分析示例
本示例使用 Spark 对食品检测数据 (**Food_Inspections1.csv**) 执行一些预测分析，这些数据通过 [City of Chicago data portal](https://data.cityofchicago.org/)（芝加哥市数据门户）获取。 此数据集包含有关在芝加哥执行的食品企业检测的信息，包括每家企业的相关信息、发现的违规行为（若有）以及检测结果。 CSV 数据文件在与群集（位于 **/HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections1.csv**）关联的存储帐户中可用。

在以下步骤中，将开发一个模型以查看如何通过食物检测或为何失败。

## <a name="start-building-a-spark-mmlib-machine-learning-app"></a>开始生成 Spark MMLib 机器学习应用
1. 在 [Azure 门户](https://portal.azure.com/)上的启动板中，单击 Spark 群集的磁贴（如果已将它固定到启动板）。 也可以单击“全部浏览” > “HDInsight 群集”导航到群集。   
1. 在 Spark 群集边栏选项卡中单击“群集仪表板”，并单击“Jupyter 笔记本”。 出现提示时，请输入群集的管理员凭据。

   > [!NOTE]
   > 也可以在浏览器中打开以下 URL 来访问群集的 Jupyter 笔记本。 将 **CLUSTERNAME** 替换为群集的名称：
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
   >
   >
1. 创建笔记本。 单击“新建”，并单击“PySpark”。

    ![创建 Jupyter 笔记本](./media/apache-spark-machine-learning-mllib-ipython/spark-machine-learning-create-jupyter.png "创建新的 Jupyter 笔记本")
1. 新笔记本随即已创建，并以 Untitled.pynb 名称打开。 在顶部单击笔记本名称，并输入一个友好名称。

    ![提供笔记本的名称](./media/apache-spark-machine-learning-mllib-ipython/spark-machine-learning-name-jupyter.png "提供笔记本的名称")
1. 使用笔记本是使用 PySpark 内核创建的，因此不需要显式创建任何上下文。 运行第一个代码单元格时，系统会自动创建 Spark 和 Hive 上下文。 可以通过导入此方案需要的类型来创建机器学习应用程序。 为此，请将光标放在单元格中，然后按“SHIFT+ENTER”。

        from pyspark.ml import Pipeline
        from pyspark.ml.classification import LogisticRegression
        from pyspark.ml.feature import HashingTF, Tokenizer
        from pyspark.sql import Row
        from pyspark.sql.functions import UserDefinedFunction
        from pyspark.sql.types import *

## <a name="construct-an-input-dataframe"></a>构造输入数据帧
可使用 `sqlContext` 来转换结构化数据。 第一个任务是将示例数据 ((**Food_Inspections1.csv**)) 加载到 Spark SQL *数据帧*中。

1. 由于原始数据是 CSV 格式，因此需使用 Spark 上下文将文件的每一行拉取到内存中作为非结构化文本，并使用 Python 的 CSV 库单独分析每一行。

        def csvParse(s):
            import csv
            from StringIO import StringIO
            sio = StringIO(s)
            value = csv.reader(sio).next()
            sio.close()
            return value

        inspections = sc.textFile('wasb:///HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections1.csv')\
                        .map(csvParse)
1. 现将 CSV 文件作为 RDD。  从 RDD 中检索一行以了解数据的构架。

        inspections.take(1)

    应该看到如下输出：

        # -----------------
        # THIS IS AN OUTPUT
        # -----------------

        [['413707',
          'LUNA PARK INC',
          'LUNA PARK  DAY CARE',
          '2049789',
          "Children's Services Facility",
          'Risk 1 (High)',
          '3250 W FOSTER AVE ',
          'CHICAGO',
          'IL',
          '60625',
          '09/21/2010',
          'License-Task Force',
          'Fail',
          '24. DISH WASHING FACILITIES: PROPERLY DESIGNED, CONSTRUCTED, MAINTAINED, INSTALLED, LOCATED AND OPERATED - Comments: All dishwashing machines must be of a type that complies with all requirements of the plumbing section of the Municipal Code of Chicago and Rules and Regulation of the Board of Health. OBSEVERD THE 3 COMPARTMENT SINK BACKING UP INTO THE 1ST AND 2ND COMPARTMENT WITH CLEAR WATER AND SLOWLY DRAINING OUT. INST NEED HAVE IT REPAIR. CITATION ISSUED, SERIOUS VIOLATION 7-38-030 H000062369-10 COURT DATE 10-28-10 TIME 1 P.M. ROOM 107 400 W. SURPERIOR. | 36. LIGHTING: REQUIRED MINIMUM FOOT-CANDLES OF LIGHT PROVIDED, FIXTURES SHIELDED - Comments: Shielding to protect against broken glass falling into food shall be provided for all artificial lighting sources in preparation, service, and display facilities. LIGHT SHIELD ARE MISSING UNDER HOOD OF  COOKING EQUIPMENT AND NEED TO REPLACE LIGHT UNDER UNIT. 4 LIGHTS ARE OUT IN THE REAR CHILDREN AREA,IN THE KINDERGARDEN CLASS ROOM. 2 LIGHT ARE OUT EAST REAR, LIGHT FRONT WEST ROOM. NEED TO REPLACE ALL LIGHT THAT ARE NOT WORKING. | 35. WALLS, CEILINGS, ATTACHED EQUIPMENT CONSTRUCTED PER CODE: GOOD REPAIR, SURFACES CLEAN AND DUST-LESS CLEANING METHODS - Comments: The walls and ceilings shall be in good repair and easily cleaned. MISSING CEILING TILES WITH STAINS IN WEST,EAST, IN FRONT AREA WEST, AND BY THE 15MOS AREA. NEED TO BE REPLACED. | 32. FOOD AND NON-FOOD CONTACT SURFACES PROPERLY DESIGNED, CONSTRUCTED AND MAINTAINED - Comments: All food and non-food contact equipment and utensils shall be smooth, easily cleanable, and durable, and shall be in good repair. SPLASH GUARDED ARE NEEDED BY THE EXPOSED HAND SINK IN THE KITCHEN AREA | 34. FLOORS: CONSTRUCTED PER CODE, CLEANED, GOOD REPAIR, COVING INSTALLED, DUST-LESS CLEANING METHODS USED - Comments: The floors shall be constructed per code, be smooth and easily cleaned, and be kept clean and in good repair. INST NEED TO ELEVATE ALL FOOD ITEMS 6INCH OFF THE FLOOR 6 INCH AWAY FORM WALL.  ',
          '41.97583445690982',
          '-87.7107455232781',
          '(41.97583445690982, -87.7107455232781)']]
1. 通过上面的输出可了解输入文件的构架。 它包括每家企业的名称、企业类型、地址、检测数据、位置，以及其他事项。 选择可用于预测分析的几个列，将结果分组为数据帧，然后用该数据帧创建临时表。

        schema = StructType([
        StructField("id", IntegerType(), False),
        StructField("name", StringType(), False),
        StructField("results", StringType(), False),
        StructField("violations", StringType(), True)])

        df = sqlContext.createDataFrame(inspections.map(lambda l: (int(l[0]), l[1], l[12], l[13])) , schema)
        df.registerTempTable('CountResults')
1. 现在便有了一个可在其上执行分析的数据帧 `df`。 还有一个名为 CountResults 的临时表。 数据帧中已包含四个所需的列：“ID”、“名称”、“结果”和“违规行为”。

    现在来获取一个小的数据示例：

        df.show(5)

    应该看到如下输出：

        # -----------------
        # THIS IS AN OUTPUT
        # -----------------

        +------+--------------------+-------+--------------------+
        |    id|                name|results|          violations|
        +------+--------------------+-------+--------------------+
        |413707|       LUNA PARK INC|   Fail|24. DISH WASHING ...|
        |391234|       CAFE SELMARIE|   Fail|2. FACILITIES TO ...|
        |413751|          MANCHU WOK|   Pass|33. FOOD AND NON-...|
        |413708|BENCHMARK HOSPITA...|   Pass|                    |
        |413722|           JJ BURGER|   Pass|                    |
        +------+--------------------+-------+--------------------+

## <a name="understand-the-data"></a>了解数据
1. 开始了解数据集包含的内容。 例如，**结果**列中有哪些不同的值？

        df.select('results').distinct().show()

    应该看到如下输出：

        # -----------------
        # THIS IS AN OUTPUT
        # -----------------

        +--------------------+
        |             results|
        +--------------------+
        |                Fail|
        |Business Not Located|
        |                Pass|
        |  Pass w/ Conditions|
        |     Out of Business|
        +--------------------+
1. 快速可视化有助于解释这些结果的分布。 临时表 CountResults 中已存在数据。 可以针对此表运行以下 SQL 查询以便更好地理解结果分布的方式。

        %%sql -o countResultsdf
        SELECT results, COUNT(results) AS cnt FROM CountResults GROUP BY results

    后接 `-o countResultsdf` 的 `%%sql` magic 可确保查询输出本地保存在 Jupyter 服务器上（通常在群集的头结点）。 输出将作为 [Pandas](http://pandas.pydata.org/) 数据帧进行保存，指定名称为 **countResultsdf**。

    应该看到如下输出：

    ![SQL 查询输出](./media/apache-spark-machine-learning-mllib-ipython/spark-machine-learning-query-output.png "SQL 查询输出")

    有关 `%%sql` magic 以及可在 PySpark 内核中使用的其他 magic 的详细信息，请参阅[包含 Spark HDInsight 群集的 Jupyter Notebook 上可用的内核](apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic)。
1. 也可以使用 Matplotlib（用于构造数据可视化的库）进行绘图。 因为必须从本地保存的 **countResultsdf** 数据帧中创建绘图，所以代码片段必须以 `%%local` magic 开头。 这可确保代码在 Jupyter 服务器上本地运行。

        %%local
        %matplotlib inline
        import matplotlib.pyplot as plt

        labels = countResultsdf['results']
        sizes = countResultsdf['cnt']
        colors = ['turquoise', 'seagreen', 'mediumslateblue', 'palegreen', 'coral']
        plt.pie(sizes, labels=labels, autopct='%1.1f%%', colors=colors)
        plt.axis('equal')

    应该看到如下输出：

    ![Spark 机器学习应用程序输出：包含五个不同检测结果的饼图](./media/apache-spark-machine-learning-mllib-ipython/spark-machine-learning-result-output-1.png "Spark 机器学习结果输出")
1. 可以看到一个检测可以有 5 个不同结果：

   * 未找到企业
   * 失败
   * 通过
   * 通过但有情况
   * 停止经营

     我们根据违规行为开发一个可以猜测食物检测结果的模型。 由于逻辑回归是二元分类方法，因此有必要将数据分为两个类别：“失败”和“通过”。 “通过但有条件”仍然是“通过”，因此在训练模型时，会将这两个结果视为等效。 得到其他结果（“未找到企业”或“停止经营”）的数据没有用，所以将其从训练集中删除。 这应该没有问题，因为这两个类别只占结果中非常小的百分比。
1. 继续操作并将现有数据帧 (`df`) 转换为新的数据帧，其中每个检测以“违规行为标签对”表示。 在本例中，`0.0` 标签表示失败，`1.0` 标签表示成功，`-1.0` 标签表示除了这两个以外的结果。 计算新的数据帧时，将筛选出这些其他结果。

        def labelForResults(s):
            if s == 'Fail':
                return 0.0
            elif s == 'Pass w/ Conditions' or s == 'Pass':
                return 1.0
            else:
                return -1.0
        label = UserDefinedFunction(labelForResults, DoubleType())
        labeledData = df.select(label(df.results).alias('label'), df.violations).where('label >= 0')

    若要查看标签数据的外观，可从中检索一行。

        labeledData.take(1)

    应该看到如下输出：

        # -----------------
        # THIS IS AN OUTPUT
        # -----------------

        [Row(label=0.0, violations=u"41. PREMISES MAINTAINED FREE OF LITTER, UNNECESSARY ARTICLES, CLEANING  EQUIPMENT PROPERLY STORED - Comments: All parts of the food establishment and all parts of the property used in connection with the operation of the establishment shall be kept neat and clean and should not produce any offensive odors.  REMOVE MATTRESS FROM SMALL DUMPSTER. | 35. WALLS, CEILINGS, ATTACHED EQUIPMENT CONSTRUCTED PER CODE: GOOD REPAIR, SURFACES CLEAN AND DUST-LESS CLEANING METHODS - Comments: The walls and ceilings shall be in good repair and easily cleaned.  REPAIR MISALIGNED DOORS AND DOOR NEAR ELEVATOR.  DETAIL CLEAN BLACK MOLD LIKE SUBSTANCE FROM WALLS BY BOTH DISH MACHINES.  REPAIR OR REMOVE BASEBOARD UNDER DISH MACHINE (LEFT REAR KITCHEN). SEAL ALL GAPS.  REPLACE MILK CRATES USED IN WALK IN COOLERS AND STORAGE AREAS WITH PROPER SHELVING AT LEAST 6' OFF THE FLOOR.  | 38. VENTILATION: ROOMS AND EQUIPMENT VENTED AS REQUIRED: PLUMBING: INSTALLED AND MAINTAINED - Comments: The flow of air discharged from kitchen fans shall always be through a duct to a point above the roofline.  REPAIR BROKEN VENTILATION IN MEN'S AND WOMEN'S WASHROOMS NEXT TO DINING AREA. | 32. FOOD AND NON-FOOD CONTACT SURFACES PROPERLY DESIGNED, CONSTRUCTED AND MAINTAINED - Comments: All food and non-food contact equipment and utensils shall be smooth, easily cleanable, and durable, and shall be in good repair.  REPAIR DAMAGED PLUG ON LEFT SIDE OF 2 COMPARTMENT SINK.  REPAIR SELF CLOSER ON BOTTOM LEFT DOOR OF 4 DOOR PREP UNIT NEXT TO OFFICE.")]

## <a name="create-a-logistic-regression-model-from-the-input-dataframe"></a>从输入数据帧创建逻辑回归模型
最后一项任务是将标签数据转换为逻辑回归可分析的格式。 逻辑回归算法的输入应是一组*标签功能向量对*，其中“功能向量”是表示输入点的数字向量。 因此，需要将“违规行为”列（半结构化，并且包含许多任意文本格式的注释）转换为计算机能轻松理解的实数组。

处理自然语言的一种标准机器学习方法是为每个不同单词分配一个“索引”，并将一个向量传递到机器学习算法，以便每个索引值包含文本字符串中该单词的相对频率。

MLlib 提供一种简单方法来执行此操作。 首先，“标记”每个违规行为字符串以获取每个字符串中的单个单词。 然后使用 `HashingTF` 将每组令牌转换为功能向量，随后可将向量传递到逻辑回归算法以构造模型。 利用“管道”按序列执行上述所有步骤。

    tokenizer = Tokenizer(inputCol="violations", outputCol="words")
    hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
    lr = LogisticRegression(maxIter=10, regParam=0.01)
    pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])

    model = pipeline.fit(labeledData)

## <a name="evaluate-the-model-on-a-separate-test-dataset"></a>评估单独测试数据集上的模型
可使用先前创建的模型基于所观察到的违规行为来预测后续检查将产生哪些结果。 通过数据集 Food_Inspections1.csv 来训练此模型。 使用第二个数据集 **Food_Inspections2.csv** 来*评估*此模型对新数据的功能性。 第二个数据集 (**Food_Inspections2.csv**) 应已存在于与群集关联的默认存储容器中。

1. 以下代码片段将创建新的数据帧 **predictionsDf**，其中包含由模型生成的预测。 该代码片段还根据数据帧创建一个名为 **Predictions** 的临时表。

        testData = sc.textFile('wasb:///HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections2.csv')\
                 .map(csvParse) \
                 .map(lambda l: (int(l[0]), l[1], l[12], l[13]))
        testDf = sqlContext.createDataFrame(testData, schema).where("results = 'Fail' OR results = 'Pass' OR results = 'Pass w/ Conditions'")
        predictionsDf = model.transform(testDf)
        predictionsDf.registerTempTable('Predictions')
        predictionsDf.columns

    应该看到如下输出：

        # -----------------
        # THIS IS AN OUTPUT
        # -----------------

        ['id',
         'name',
         'results',
         'violations',
         'words',
         'features',
         'rawPrediction',
         'probability',
         'prediction']
1. 查看其中一个预测。 运行此代码段：

        predictionsDf.take(1)

   将显示针对测试数据集中第一项的预测。
1. `model.transform()` 方法会将相同转换应用于任何具有相同构架的新数据，并成功预测如何对数据进行分类。 可执行一些简单的统计，了解预测的准确度如何：

        numSuccesses = predictionsDf.where("""(prediction = 0 AND results = 'Fail') OR
                                              (prediction = 1 AND (results = 'Pass' OR
                                                                   results = 'Pass w/ Conditions'))""").count()
        numInspections = predictionsDf.count()

        print "There were", numInspections, "inspections and there were", numSuccesses, "successful predictions"
        print "This is a", str((float(numSuccesses) / float(numInspections)) * 100) + "%", "success rate"

    输出如下所示：

        # -----------------
        # THIS IS AN OUTPUT
        # -----------------

        There were 9315 inspections and there were 8087 successful predictions
        This is a 86.8169618894% success rate

    将逻辑回归与 Spark 配合使用可得到关于违规行为（中文）描述和给定企业是通过还是未通过食物检测之间关系的准确模型。

## <a name="create-a-visual-representation-of-the-prediction"></a>创建预测的直观表示形式
现可够着一个最终可视化，帮助我们推论出此测试的结果。

1. 首先，提取之前创建的“Predictions”临时表中的不同预测和结果。 以下查询将输出分为 *true_positive*、*false_positive*、*true_negative* 和 *false_negative*。 在以下查询中，使用 `-q` 关闭可视化，同时使用 `-o` 将输出保存为随后可用于 `%%local` 幻数的数据帧。

        %%sql -q -o true_positive
        SELECT count(*) AS cnt FROM Predictions WHERE prediction = 0 AND results = 'Fail'

        %%sql -q -o false_positive
        SELECT count(*) AS cnt FROM Predictions WHERE prediction = 0 AND (results = 'Pass' OR results = 'Pass w/ Conditions')

        %%sql -q -o true_negative
        SELECT count(*) AS cnt FROM Predictions WHERE prediction = 1 AND results = 'Fail'

        %%sql -q -o false_negative
        SELECT count(*) AS cnt FROM Predictions WHERE prediction = 1 AND (results = 'Pass' OR results = 'Pass w/ Conditions')
1. 最后，使用下面的代码段通过 **Matplotlib**生成绘图。

        %%local
        %matplotlib inline
        import matplotlib.pyplot as plt

        labels = ['True positive', 'False positive', 'True negative', 'False negative']
        sizes = [true_positive['cnt'], false_positive['cnt'], false_negative['cnt'], true_negative['cnt']]
        colors = ['turquoise', 'seagreen', 'mediumslateblue', 'palegreen', 'coral']
        plt.pie(sizes, labels=labels, autopct='%1.1f%%', colors=colors)
        plt.axis('equal')

    应该会看到以下输出：

    ![Spark 机器学习应用程序输出：失败食品检测百分比饼图。](./media/apache-spark-machine-learning-mllib-ipython/spark-machine-learning-result-output-2.png "Spark 机器学习结果输出")

    在此图中，“positive”结果是指未通过食品检测，而“negative”结果表示已通过检测。

## <a name="shut-down-the-notebook"></a>关闭笔记本
运行完应用程序之后，应该关闭笔记本以释放资源。 为此，请在笔记本的“文件”菜单中，单击“关闭并停止”。 这会关闭笔记本。

## <a name="seealso"></a>另请参阅
* [概述：Azure HDInsight 上的 Apache Spark](apache-spark-overview.md)

### <a name="scenarios"></a>方案
* [Spark 和 BI：使用 HDInsight 中的 Spark 和 BI 工具执行交互式数据分析](apache-spark-use-bi-tools.md)
* [Spark 和机器学习：使用 HDInsight 中的 Spark 对使用 HVAC 数据生成温度进行分析](apache-spark-ipython-notebook-machine-learning.md)
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
