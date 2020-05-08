---
title: HDInsight 上 Spark MLlib 的机器学习示例 - Azure
description: 了解如何使用 Spark MLlib 创建机器学习应用，用于通过逻辑回归使用分类分析数据集。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017,seoapr2020
ms.date: 04/27/2020
ms.openlocfilehash: 48bd53160c3d2e76dccd1f22723c30c2c7e00d7a
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82559940"
---
# <a name="use-apache-spark-mllib-to-build-a-machine-learning-application-and-analyze-a-dataset"></a>使用 Apache Spark MLlib 生成机器学习应用程序并分析数据集

了解如何使用 Apache Spark MLlib 创建机器学习应用程序。 应用程序将对打开的数据集执行预测分析。 本示例摘自 Spark 的内置机器学习库，它通过逻辑回归使用分类。 

MLlib 是一个核心 Spark 库，它提供了许多可用于机器学习任务的实用程序，例如：

* 分类
* 回归
* 群集功能
* 建模
* 单值分解 (SVD) 和主体组件分析 (PCA)
* 假设测试和计算示例统计信息

## <a name="understand-classification-and-logistic-regression"></a>了解分类和逻辑回归

“分类”是一种很常见的机器学习任务，是将输入数据归入各类别的过程。  分类算法的工作就是确定如何将 "标签" 分配到提供的输入数据。 例如，可以考虑将股票信息作为输入接受的机器学习算法。 然后将股票分为两类：应该销售的股票和应该保留的股票。

逻辑回归是用于分类的算法。 Spark 的逻辑回归 API 可用于 *二元分类*，或将输入数据归类到两组中的一组。 有关逻辑回归的详细信息，请参阅[维基百科](https://en.wikipedia.org/wiki/Logistic_regression)。

总之，逻辑回归过程产生了*逻辑函数*。 使用函数可预测输入向量属于一个组或另一个组的概率。  

## <a name="predictive-analysis-example-on-food-inspection-data"></a>食品检测数据的预测分析示例

在此示例中，使用 Spark 对食品检测数据（**Food_Inspections1 .csv**）执行一些预测分析。 通过[芝加哥数据门户的城市获取的](https://data.cityofchicago.org/)数据。 此数据集包含有关在芝加哥进行的食品建立检查的信息。 包括有关每个建立的信息、发现的违规（如果有）以及检测的结果。 CSV 数据文件在与群集（位于 **/HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections1.csv**）关联的存储帐户中可用。

在以下步骤中，将开发一个模型以查看如何通过食物检测或为何失败。

## <a name="create-an-apache-spark-mllib-machine-learning-app"></a>创建 Apache Spark MLlib 机器学习应用

1. 使用 PySpark 内核创建 Jupyter Notebook。 有关说明，请参阅[创建 Jupyter Notebook](./apache-spark-jupyter-spark-sql.md#create-a-jupyter-notebook)。

2. 导入此应用程序所需的类型。 将以下代码复制并粘贴到空白单元格中，然后按 **SHIFT + ENTER**。

    ```PySpark
    from pyspark.ml import Pipeline
    from pyspark.ml.classification import LogisticRegression
    from pyspark.ml.feature import HashingTF, Tokenizer
    from pyspark.sql import Row
    from pyspark.sql.functions import UserDefinedFunction
    from pyspark.sql.types import *
    ```

    由于 PySpark 内核，不需要显式创建任何上下文。 当你运行第一个代码单元格时，将自动创建 Spark 和 Hive 上下文。

## <a name="construct-the-input-dataframe"></a>构造输入数据帧

使用 Spark 上下文将原始 CSV 数据作为非结构化文本提取到内存。 然后，使用 Python 的 CSV 库分析数据的每一行。

1. 运行以下命令行，通过导入并分析输入数据来创建弹性分布式数据集 (RDD)。

    ```PySpark
    def csvParse(s):
        import csv
        from StringIO import StringIO
        sio = StringIO(s)
        value = csv.reader(sio).next()
        sio.close()
        return value

    inspections = sc.textFile('/HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections1.csv')\
                    .map(csvParse)
    ```

2. 运行以下代码检索 RDD 中的一行，以便可以查看数据架构：

    ```PySpark
    inspections.take(1)
    ```

    输出为：

    ```
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
    ```

    通过这些输出可以了解输入文件的架构。 它包括每个建立的名称，以及建立类型。 此外，还包括地址、检查的数据和位置，等等。

3. 运行以下代码创建数据帧 (*df*) 和临时表 (*CountResults*)，其中包含一些可用于预测分析的列。 `sqlContext`用于对结构化数据执行转换。

    ```PySpark
    schema = StructType([
    StructField("id", IntegerType(), False),
    StructField("name", StringType(), False),
    StructField("results", StringType(), False),
    StructField("violations", StringType(), True)])

    df = spark.createDataFrame(inspections.map(lambda l: (int(l[0]), l[1], l[12], l[13])) , schema)
    df.registerTempTable('CountResults')
    ```

    数据帧中的四个相关列是**ID**、**名称**、**结果**和**冲突**。

4. 运行以下代码获取数据小样本：

    ```PySpark
    df.show(5)
    ```

    输出为：

    ```
    +------+--------------------+-------+--------------------+
    |    id|                name|results|          violations|
    +------+--------------------+-------+--------------------+
    |413707|       LUNA PARK INC|   Fail|24. DISH WASHING ...|
    |391234|       CAFE SELMARIE|   Fail|2. FACILITIES TO ...|
    |413751|          MANCHU WOK|   Pass|33. FOOD AND NON-...|
    |413708|BENCHMARK HOSPITA...|   Pass|                    |
    |413722|           JJ BURGER|   Pass|                    |
    +------+--------------------+-------+--------------------+
    ```

## <a name="understand-the-data"></a>了解数据

让我们开始了解数据集包含的内容。 

1. 运行以下代码，显示 **results** 列中的非重复值：

    ```PySpark
    df.select('results').distinct().show()
    ```

    输出为：

    ```
    +--------------------+
    |             results|
    +--------------------+
    |                Fail|
    |Business Not Located|
    |                Pass|
    |  Pass w/ Conditions|
    |     Out of Business|
    +--------------------+
    ```

2. 运行以下代码来可视化这些结果的分布：

    ```PySpark
    %%sql -o countResultsdf
    SELECT COUNT(results) AS cnt, results FROM CountResults GROUP BY results
    ```

    后接 `-o countResultsdf` 的 `%%sql` magic 可确保查询输出本地保存在 Jupyter 服务器上（通常在群集的头结点）。 输出将作为 [Pandas](https://pandas.pydata.org/) 数据帧进行保存，指定名称为 **countResultsdf**。 有关 `%%sql` magic 以及可在 PySpark 内核中使用的其他 magic 的详细信息，请参阅[包含 Apache Spark HDInsight 群集的 Jupyter Notebook 上可用的内核](apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic)。

    输出为：

    ![SQL 查询输出](./media/apache-spark-machine-learning-mllib-ipython/spark-machine-learning-query-output.png "SQL 查询输出")

3. 也可以使用 Matplotlib（用于构造数据可视化的库）进行绘图。 因为必须从本地保存的 **countResultsdf** 数据帧中创建绘图，所以代码片段必须以 `%%local` magic 开头。 此操作可确保代码在 Jupyter 服务器上本地运行。

    ```PySpark
    %%local
    %matplotlib inline
    import matplotlib.pyplot as plt

    labels = countResultsdf['results']
    sizes = countResultsdf['cnt']
    colors = ['turquoise', 'seagreen', 'mediumslateblue', 'palegreen', 'coral']
    plt.pie(sizes, labels=labels, autopct='%1.1f%%', colors=colors)
    plt.axis('equal')
    ```

    若要预测食物检测结果，需要基于违规行为开发一个模型。 由于逻辑回归是二元分类方法，因此有必要将结果数据分为两个类别：“失败”**** 和“通过”****。

   - 通过
       - 通过
       - 有条件通过
   - 失败
       - 失败
   - 弃用
       - 未找到企业
       - 停止经营

     具有其他结果（"未找到企业" 或 "业务不足"）的数据不起作用，因此，它们会导致一小部分结果。

4. 运行以下代码，将现有数据帧 (`df`) 转换为新的数据帧，其中每个检测以“违规行为标签对”表示。 在本例中，标签`0.0`表示失败，标签`1.0`表示成功，标签`-1.0`表示除这两个结果之外的某些结果。

    ```PySpark
    def labelForResults(s):
        if s == 'Fail':
            return 0.0
        elif s == 'Pass w/ Conditions' or s == 'Pass':
            return 1.0
        else:
            return -1.0
    label = UserDefinedFunction(labelForResults, DoubleType())
    labeledData = df.select(label(df.results).alias('label'), df.violations).where('label >= 0')
    ```

5. 运行以下代码显示一行带有标签的数据：

    ```PySpark
    labeledData.take(1)
    ```

    输出为：

    ```
    [Row(label=0.0, violations=u"41. PREMISES MAINTAINED FREE OF LITTER, UNNECESSARY ARTICLES, CLEANING  EQUIPMENT PROPERLY STORED - Comments: All parts of the food establishment and all parts of the property used in connection with the operation of the establishment shall be kept neat and clean and should not produce any offensive odors.  REMOVE MATTRESS FROM SMALL DUMPSTER. | 35. WALLS, CEILINGS, ATTACHED EQUIPMENT CONSTRUCTED PER CODE: GOOD REPAIR, SURFACES CLEAN AND DUST-LESS CLEANING METHODS - Comments: The walls and ceilings shall be in good repair and easily cleaned.  REPAIR MISALIGNED DOORS AND DOOR NEAR ELEVATOR.  DETAIL CLEAN BLACK MOLD LIKE SUBSTANCE FROM WALLS BY BOTH DISH MACHINES.  REPAIR OR REMOVE BASEBOARD UNDER DISH MACHINE (LEFT REAR KITCHEN). SEAL ALL GAPS.  REPLACE MILK CRATES USED IN WALK IN COOLERS AND STORAGE AREAS WITH PROPER SHELVING AT LEAST 6' OFF THE FLOOR.  | 38. VENTILATION: ROOMS AND EQUIPMENT VENTED AS REQUIRED: PLUMBING: INSTALLED AND MAINTAINED - Comments: The flow of air discharged from kitchen fans shall always be through a duct to a point above the roofline.  REPAIR BROKEN VENTILATION IN MEN'S AND WOMEN'S WASHROOMS NEXT TO DINING AREA. | 32. FOOD AND NON-FOOD CONTACT SURFACES PROPERLY DESIGNED, CONSTRUCTED AND MAINTAINED - Comments: All food and non-food contact equipment and utensils shall be smooth, easily cleanable, and durable, and shall be in good repair.  REPAIR DAMAGED PLUG ON LEFT SIDE OF 2 COMPARTMENT SINK.  REPAIR SELF CLOSER ON BOTTOM LEFT DOOR OF 4 DOOR PREP UNIT NEXT TO OFFICE.")]
    ```

## <a name="create-a-logistic-regression-model-from-the-input-dataframe"></a>从输入数据帧创建逻辑回归模型

最终任务是转换标记的数据。 将数据转换为可通过逻辑回归进行分析的格式。 逻辑回归算法的输入需要一组*标签功能向量对*。 其中 "特征向量" 是表示输入点的数字向量。 因此，您需要将 "违规" 列转换为半结构化的，并且在自由文本中包含了许多注释。 将列转换为计算机可以轻松理解的实数数组。

处理自然语言的一种标准机器学习方法是为每个不同的单词分配一个 "索引"。 然后，将一个向量传递到机器学习算法。 这样，每个索引的值都包含文本字符串中该单词的相对频率。

MLlib 提供了一种简单的方法来执行此操作。 首先，“标记”每个违规行为字符串以获取每个字符串中的单个单词。 然后使用 `HashingTF` 将每组令牌转换为功能向量，随后可将向量传递到逻辑回归算法以构造模型。 利用“管道”按序列执行上述所有步骤。

```PySpark
tokenizer = Tokenizer(inputCol="violations", outputCol="words")
hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
lr = LogisticRegression(maxIter=10, regParam=0.01)
pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])

model = pipeline.fit(labeledData)
```

## <a name="evaluate-the-model-using-another-dataset"></a>使用另一个数据集评估模型

您可以使用之前创建的模型来*预测*新检查的结果。 预测基于所观察到的违规。 通过数据集 Food_Inspections1.csv 来训练此模型****。 可以使用另一个数据集 **Food_Inspections2.csv** 来评估此模型对新数据的功能性。** 第二个数据集 (**Food_Inspections2.csv**) 位于与群集关联的默认存储容器中。

1. 运行以下代码创建新的数据帧 **predictionsDf**，其中包含由模型生成的预测。 该代码片段还根据数据帧创建一个名为 **Predictions** 的临时表。

    ```PySpark
    testData = sc.textFile('wasbs:///HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections2.csv')\
                .map(csvParse) \
                .map(lambda l: (int(l[0]), l[1], l[12], l[13]))
    testDf = spark.createDataFrame(testData, schema).where("results = 'Fail' OR results = 'Pass' OR results = 'Pass w/ Conditions'")
    predictionsDf = model.transform(testDf)
    predictionsDf.registerTempTable('Predictions')
    predictionsDf.columns
    ```

    应会看到类似于以下文本的输出：

    ```
    ['id',
        'name',
        'results',
        'violations',
        'words',
        'features',
        'rawPrediction',
        'probability',
        'prediction']
    ```

1. 查看其中一个预测。 运行此代码段：

    ```PySpark
    predictionsDf.take(1)
    ```

   测试数据集中的第一项有一个预测。

1. `model.transform()` 方法会将相同转换应用于任何具有相同构架的新数据，并成功预测如何对数据进行分类。 您可以执行一些统计信息，以了解预测的方式：

    ```PySpark
    numSuccesses = predictionsDf.where("""(prediction = 0 AND results = 'Fail') OR
                                            (prediction = 1 AND (results = 'Pass' OR
                                                                results = 'Pass w/ Conditions'))""").count()
    numInspections = predictionsDf.count()

    print "There were", numInspections, "inspections and there were", numSuccesses, "successful predictions"
    print "This is a", str((float(numSuccesses) / float(numInspections)) * 100) + "%", "success rate"
    ```

    输出类似于以下文本：

    ```
    There were 9315 inspections and there were 8087 successful predictions
    This is a 86.8169618894% success rate
    ```

    将逻辑回归与 Spark 结合使用，可为你提供英语冲突说明之间关系的模型。 某个给定企业是通过还是未通过食物检测。

## <a name="create-a-visual-representation-of-the-prediction"></a>创建预测的直观表示形式

现在可以构造一个最终可视化效果，以帮助推理此测试的结果。

1. 首先，提取之前创建的“Predictions”临时表中的不同预测和结果****。 以下查询将输出分为 *true_positive*、*false_positive*、*true_negative* 和 *false_negative*。 在以下查询中，使用 `-q` 关闭可视化，同时使用 `-o` 将输出保存为随后可用于 `%%local` 幻数的数据帧。

    ```PySpark
    %%sql -q -o true_positive
    SELECT count(*) AS cnt FROM Predictions WHERE prediction = 0 AND results = 'Fail'
    ```

    ```PySpark
    %%sql -q -o false_positive
    SELECT count(*) AS cnt FROM Predictions WHERE prediction = 0 AND (results = 'Pass' OR results = 'Pass w/ Conditions')
    ```

    ```PySpark
    %%sql -q -o true_negative
    SELECT count(*) AS cnt FROM Predictions WHERE prediction = 1 AND results = 'Fail'
    ```

    ```PySpark
    %%sql -q -o false_negative
    SELECT count(*) AS cnt FROM Predictions WHERE prediction = 1 AND (results = 'Pass' OR results = 'Pass w/ Conditions')
    ```

1. 最后，使用下面的代码段通过 **Matplotlib**生成绘图。

    ```PySpark
    %%local
    %matplotlib inline
    import matplotlib.pyplot as plt

    labels = ['True positive', 'False positive', 'True negative', 'False negative']
    sizes = [true_positive['cnt'], false_positive['cnt'], false_negative['cnt'], true_negative['cnt']]
    colors = ['turquoise', 'seagreen', 'mediumslateblue', 'palegreen', 'coral']
    plt.pie(sizes, labels=labels, autopct='%1.1f%%', colors=colors)
    plt.axis('equal')
    ```

    应会看到以下输出：

    ![Spark 机器学习应用程序输出-饼图检查失败食物的百分比。](./media/apache-spark-machine-learning-mllib-ipython/spark-machine-learning-result-output-2.png "Spark 机器学习结果输出")

    在此图中，“positive”结果是指未通过食品检测，而“negative”结果表示已通过检测。

## <a name="shut-down-the-notebook"></a>关闭笔记本

运行完应用程序之后，应该关闭笔记本以释放资源。 为此，请在 Notebook 的“文件”菜单中选择“关闭并停止”   。 此操作会关闭 Notebook。

## <a name="next-steps"></a>后续步骤

* [概述：Azure HDInsight 上的 Apache Spark](apache-spark-overview.md)
* [使用 HDInsight 中的 Apache Spark 分析网站日志](apache-spark-custom-library-website-log-analysis.md)
* [通过 Azure HDInsight Microsoft Cognitive Toolkit 深度学习模型](apache-spark-microsoft-cognitive-toolkit.md)
