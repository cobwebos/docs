---
title: 使用 Spark 中的 Python 库分析网站日志 - Azure
description: 此笔记本演示如何将自定义库与 Azure HDInsight 上的 Spark 配合使用来分析日志数据。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 12/27/2019
ms.openlocfilehash: c6bf26d8f3a73db6ee69b2aa0de73872911893bf
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/31/2019
ms.locfileid: "75552706"
---
# <a name="analyze-website-logs-using-a-custom-python-library-with-apache-spark-cluster-on-hdinsight"></a>将自定义 Python 库与 HDInsight 上的 Apache Spark 群集配合使用来分析网站日志

此笔记本演示如何将自定义库与 HDInsight 上的 Apache Spark 配合使用来分析日志数据。 我们使用的自定义库是一个名为 **iislogparser.py** 的 Python 库。

## <a name="prerequisites"></a>必备组件

HDInsight 上的 Apache Spark 群集。 有关说明，请参阅[在 Azure HDInsight 中创建 Apache Spark 群集](apache-spark-jupyter-spark-sql.md)。

## <a name="save-raw-data-as-an-rdd"></a>将原始数据另存为 RDD

在本部分中，将使用与 HDInsight 中的 Apache Spark 群集关联的 [Jupyter](https://jupyter.org) 笔记本来运行用于处理原始示例数据并将其保存为 Hive 表的作业。 示例数据是所有群集在默认情况下均会提供的 .csv 文件 (hvac.csv)。

将数据另存为 Apache Hive 表后，在下一部分中，我们将使用 BI 工具（如 Power BI 和 Tableau）连接到 Hive 表。

1. 在 web 浏览器中，导航到 `https://CLUSTERNAME.azurehdinsight.net/jupyter`，其中 `CLUSTERNAME` 是群集的名称。

1. 创建新的笔记本。 选择 "**新建**"，然后选择 " **PySpark**"。

    ![创建新的 Apache Jupyter 笔记本](./media/apache-spark-custom-library-website-log-analysis/hdinsight-create-jupyter-notebook.png "创建新的 Jupyter 笔记本")

1. 新笔记本随即已创建，并以 Untitled.pynb 名称打开。 选择顶部的笔记本名称，并输入一个友好名称。

    ![提供笔记本的名称](./media/apache-spark-custom-library-website-log-analysis/hdinsight-name-jupyter-notebook.png "提供笔记本的名称")

1. 由于已使用 PySpark 内核创建笔记本，因此不需要显式创建任何上下文。 运行第一个代码单元格时，系统会自动创建 Spark 和 Hive 上下文。 首先可以导入此方案所需的类型。 将以下代码片段粘贴到空白单元格中，然后按**Shift + enter**。

    ```pyspark
    from pyspark.sql import Row
    from pyspark.sql.types import *
    ```

1. 使用群集上已可用的示例日志数据创建 RDD。 可以在 `\HdiSamples\HdiSamples\WebsiteLogSampleData\SampleLog\909f2b.log`访问与群集关联的默认存储帐户中的数据。 执行以下代码：

    ```pyspark
    logs = sc.textFile('wasbs:///HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b.log')
    ```

1. 检索示例日志集以验证上一步是否成功完成。

    ```pyspark
    logs.take(5)
    ```

    应会看到类似于以下文本的输出：

    ```output
    [u'#Software: Microsoft Internet Information Services 8.0',
    u'#Fields: date time s-sitename cs-method cs-uri-stem cs-uri-query s-port cs-username c-ip cs(User-Agent) cs(Cookie) cs(Referer) cs-host sc-status sc-substatus sc-win32-status sc-bytes cs-bytes time-taken',
    u'2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step2.png X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 53175 871 46',
    u'2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step3.png X-ARR-LOG-ID=9eace870-2f49-4efd-b204-0d170da46b4a 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 51237 871 32',
    u'2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step4.png X-ARR-LOG-ID=4bea5b3d-8ac9-46c9-9b8c-ec3e9500cbea 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 72177 871 47']
    ```

## <a name="analyze-log-data-using-a-custom-python-library"></a>使用自定义 Python 库分析日志数据

1. 在上面的输出中，前几行包括标头信息，其余的每一行均与此标头中描述的架构相匹配。 分析此类日志可能很复杂。 因此，可使用自定义 Python 库 (**iislogparser.py**)，它能使分析这类日志变得容易得多。 默认情况下，此库随 HDInsight 上的 Spark 群集提供 `/HdiSamples/HdiSamples/WebsiteLogSampleData/iislogparser.py`。

    但是，此库不在 `PYTHONPATH` 中，因此无法使用 `import iislogparser`之类的 import 语句来使用它。 要使用此库，必须将其分发给所有从节点。 运行以下代码段。

    ```pyspark
    sc.addPyFile('wasbs:///HdiSamples/HdiSamples/WebsiteLogSampleData/iislogparser.py')
    ```

1. 如果日志行是标题行，并且在遇到日志行时返回 `LogLine` 类的实例，则 `iislogparser` 提供返回 `None` 的函数 `parse_log_line`。 使用 `LogLine` 类从 RDD 中仅提取日志行：

    ```pyspark
    def parse_line(l):
        import iislogparser
        return iislogparser.parse_log_line(l)
    logLines = logs.map(parse_line).filter(lambda p: p is not None).cache()
    ```

1. 检索一些提取的日志行，以验证该步骤是否成功完成。

    ```pyspark
    logLines.take(2)
    ```

   输出应类似于以下文本：

    ```output
    [2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step2.png X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 53175 871 46,
    2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step3.png X-ARR-LOG-ID=9eace870-2f49-4efd-b204-0d170da46b4a 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 51237 871 32]
    ```

1. 反过来，`LogLine` 类具有一些有用的方法，如 `is_error()`，可返回日志条目是否具有错误代码。 使用此类计算提取日志行中的错误数，然后将所有错误记录到另一个文件中。

    ```pyspark
    errors = logLines.filter(lambda p: p.is_error())
    numLines = logLines.count()
    numErrors = errors.count()
    print 'There are', numErrors, 'errors and', numLines, 'log entries'
    errors.map(lambda p: str(p)).saveAsTextFile('wasbs:///HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b-2.log')
    ```

    输出应 `There are 30 errors and 646 log entries`状态。

1. 还可以使用 **Matplotlib** 构造数据的可视化效果。 例如，如果要找出请求长时间运行的原因，可能需要查找平均执行时间最长的文件。 下面的代码段检索执行请求花费时间最长的前 25 个资源。

    ```pyspark
    def avgTimeTakenByKey(rdd):
        return rdd.combineByKey(lambda line: (line.time_taken, 1),
                                lambda x, line: (x[0] + line.time_taken, x[1] + 1),
                                lambda x, y: (x[0] + y[0], x[1] + y[1]))\
                    .map(lambda x: (x[0], float(x[1][0]) / float(x[1][1])))

    avgTimeTakenByKey(logLines.map(lambda p: (p.cs_uri_stem, p))).top(25, lambda x: x[1])
    ```

   应会看到类似于以下文本的输出：

    ```output
    [(u'/blogposts/mvc4/step13.png', 197.5),
    (u'/blogposts/mvc2/step10.jpg', 179.5),
    (u'/blogposts/extractusercontrol/step5.png', 170.0),
    (u'/blogposts/mvc4/step8.png', 159.0),
    (u'/blogposts/mvcrouting/step22.jpg', 155.0),
    (u'/blogposts/mvcrouting/step3.jpg', 152.0),
    (u'/blogposts/linqsproc1/step16.jpg', 138.75),
    (u'/blogposts/linqsproc1/step26.jpg', 137.33333333333334),
    (u'/blogposts/vs2008javascript/step10.jpg', 127.0),
    (u'/blogposts/nested/step2.jpg', 126.0),
    (u'/blogposts/adminpack/step1.png', 124.0),
    (u'/BlogPosts/datalistpaging/step2.png', 118.0),
    (u'/blogposts/mvc4/step35.png', 117.0),
    (u'/blogposts/mvcrouting/step2.jpg', 116.5),
    (u'/blogposts/aboutme/basketball.jpg', 109.0),
    (u'/blogposts/anonymoustypes/step11.jpg', 109.0),
    (u'/blogposts/mvc4/step12.png', 106.0),
    (u'/blogposts/linq8/step0.jpg', 105.5),
    (u'/blogposts/mvc2/step18.jpg', 104.0),
    (u'/blogposts/mvc2/step11.jpg', 104.0),
    (u'/blogposts/mvcrouting/step1.jpg', 104.0),
    (u'/blogposts/extractusercontrol/step1.png', 103.0),
    (u'/blogposts/sqlvideos/sqlvideos.jpg', 102.0),
    (u'/blogposts/mvcrouting/step21.jpg', 101.0),
    (u'/blogposts/mvc4/step1.png', 98.0)]
    ```

1. 还可以在此绘图窗体中显示此信息。 创建绘图的第一步是创建一个临时表 **AverageTime**。 该表按照时间对日志进行分组，以查看在任何特定时间是否有任何异常延迟峰值。

    ```pyspark
    avgTimeTakenByMinute = avgTimeTakenByKey(logLines.map(lambda p: (p.datetime.minute, p))).sortByKey()
    schema = StructType([StructField('Minutes', IntegerType(), True),
                        StructField('Time', FloatType(), True)])

    avgTimeTakenByMinuteDF = sqlContext.createDataFrame(avgTimeTakenByMinute, schema)
    avgTimeTakenByMinuteDF.registerTempTable('AverageTime')
    ```

1. 接下来可以运行以下 SQL 查询以获取 **AverageTime** 表中的所有记录。

    ```pyspark
    %%sql -o averagetime
    SELECT * FROM AverageTime
    ```

   后接 `-o averagetime` 的 `%%sql` magic 可确保查询输出本地保存在 Jupyter 服务器上（通常在群集的头结点）。 输出将作为 [Pandas](https://pandas.pydata.org/) 数据帧进行保存，指定名称为“averagetime”。

   应会看到如下图所示的输出：

   ![hdinsight jupyter sql 查询输出](./media/apache-spark-custom-library-website-log-analysis/hdinsight-jupyter-sql-qyery-output.png "SQL 查询输出")

   有关 `%%sql` magic 的详细信息，请参阅 [%%sql magic 支持的参数](apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic)。

1. 现在可以使用 Matplotlib（用于构造数据可视化的库）来创建绘图。 因为必须从本地保存的 **averagetime** 数据帧中创建绘图，所以代码片段必须以 `%%local` magic 开头。 这可确保代码在 Jupyter 服务器上本地运行。

    ```pyspark
    %%local
    %matplotlib inline
    import matplotlib.pyplot as plt

    plt.plot(averagetime['Minutes'], averagetime['Time'], marker='o', linestyle='--')
    plt.xlabel('Time (min)')
    plt.ylabel('Average time taken for request (ms)')
    ```

   应会看到如下图所示的输出：

   ![apache spark web 日志分析绘图](./media/apache-spark-custom-library-website-log-analysis/hdinsight-apache-spark-web-log-analysis-plot.png "Matplotlib 输出")

1. 运行完应用程序之后，应该关闭笔记本以释放资源。 为此，请在 Notebook 的“文件”菜单中选择“关闭并停止”。 此操作将关闭并关闭笔记本。

## <a name="next-steps"></a>后续步骤

浏览以下文章：

* [概述：Azure HDInsight 上的 Apache Spark](apache-spark-overview.md)
* [Use external packages with Jupyter notebooks（将外部包与 Jupyter 笔记本配合使用）](apache-spark-jupyter-notebook-use-external-packages.md)
* [Install Jupyter on your computer and connect to an HDInsight Spark cluster（在计算机上安装 Jupyter 并连接到 HDInsight Spark 群集）](apache-spark-jupyter-notebook-install-locally.md)