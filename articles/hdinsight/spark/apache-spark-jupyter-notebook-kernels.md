---
title: Azure HDInsight 中 Spark 群集上的 Jupyter 笔记本的内核
description: 了解 Azure HDInsight 上的 Spark 群集所提供的 Jupyter 笔记本的 PySpark、PySpark3 和 Spark 内核。
keywords: spark 中的 jupyter 笔记本,jupyter spark
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 05/27/2019
ms.author: hrasheed
ms.openlocfilehash: b2ae24c0449b009db6fcecdd8a1366ea5154629a
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/28/2019
ms.locfileid: "66257796"
---
# <a name="kernels-for-jupyter-notebook-on-apache-spark-clusters-in-azure-hdinsight"></a>Azure HDInsight 中 Apache Spark 群集上的 Jupyter notebook 的内核 

HDInsight Spark 群集提供可在 [Apache Spark](https://spark.apache.org/) 上的 Jupyter notebook 中用于测试应用程序的内核。 内核是可以运行和解释代码的程序。 三个内核如下：

- **PySpark** - 适用于以 Python2 编写的应用程序。
- **PySpark3** - 适用于以 Python3 编写的应用程序。
- **Spark** - 适用于以 Scala 编写的应用程序。

在本文中，学习如何使用这些内核以及使用它们的优势。

## <a name="prerequisites"></a>必备组件

HDInsight 中的 Apache Spark 群集。 有关说明，请参阅[在 Azure HDInsight 中创建 Apache Spark 群集](apache-spark-jupyter-spark-sql.md)。

## <a name="create-a-jupyter-notebook-on-spark-hdinsight"></a>在 Spark HDInsight 上创建 Jupyter 笔记本

1. 从[Azure 门户](https://portal.azure.com/)，选择你的 Spark 群集。  有关说明，请参阅[列出和显示群集](../hdinsight-administer-use-portal-linux.md#showClusters)。 **概述**视图打开。

2. 从**概述**视图中，在**群集仪表板**框中，选择**Jupyter notebook**。 出现提示时，请输入群集的管理员凭据。

    ![Spark 中的 Jupyter 笔记本](./media/apache-spark-jupyter-notebook-kernels/hdinsight-spark-open-jupyter-interactive-spark-sql-query.png "Spark 中的 Jupyter 笔记本") 
  
   > [!NOTE]  
   > 也可以在浏览器中打开以下 URL 来访问 Spark 群集中的 Jupyter 笔记本。 将 **CLUSTERNAME** 替换为群集的名称：
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

3. 选择**新建**，然后选择**Pyspark**， **PySpark3**，或者**Spark**创建笔记本。 使用适用于 Scala 应用程序的 Spark 内核，适用于 Python2 应用程序的 PySpark 内核，以及适用于 Python3 应用程序的 PySpark3 内核。
   
    ![Spark 中 Jupyter 笔记本的内核](./media/apache-spark-jupyter-notebook-kernels/kernel-jupyter-notebook-on-spark.png "Spark 中 Jupyter 笔记本的内核") 

4. 此时会打开使用所选内核的 Notebook。

## <a name="benefits-of-using-the-kernels"></a>使用这些内核的好处

以下是在 Spark HDInsight 群集中的 Jupyter 笔记本上使用新内核的几个好处。

- **预设上下文**。 使用 **PySpark**、**PySpark3** 或 **Spark** 内核时，无需首先显式设置 Spark 或 Hive 上下文，即可开始使用应用程序。 这些上下文默认已提供。 这些上下文包括：
   
  * **sc** - 表示 Spark 上下文
  * **sqlContext** - 表示 Hive 上下文
   
    因此，不需要运行如下语句来设置上下文：
   
         sc = SparkContext('yarn-client')
         sqlContext = HiveContext(sc)
   
    可以直接在应用程序中使用预设上下文。

- **单元 magic**。 PySpark 内核提供一些预定义的“magic”，这是可以结合 `%%` 调用的特殊命令（例如 `%%MAGIC` `<args>`）。 magic 命令必须是代码单元中的第一个字，并且允许多行内容。 magic 一字应该是单元中的第一个字。 在 magic 前面添加任何内容（即使是注释）会导致错误。     有关 magic 的详细信息，请参阅[此处](https://ipython.readthedocs.org/en/stable/interactive/magics.html)。
   
    下表列出可通过内核提供的不同 magic。

   | Magic | 示例 | 描述 |
   | --- | --- | --- |
   | help |`%%help` |生成所有可用 magic 的表，其中包含示例和说明 |
   | info |`%%info` |输出当前 Livy 终结点的会话信息 |
   | 配置 |`%%configure -f`<br>`{"executorMemory": "1000M"`,<br>`"executorCores": 4`} |配置用于创建会话的参数。 如果已创建会话，则必须指定 force 标志 (-f)，确保删除再重新创建该会话。 有关有效参数的列表，请查看 [Livy 的 POST /sessions 请求正文](https://github.com/cloudera/livy#request-body)。 参数必须以 JSON 字符串传入，并且必须位于 magic 后面的下一行，如示例列中所示。 |
   | sql |`%%sql -o <variable name>`<br> `SHOW TABLES` |针对 sqlContext 执行 Hive 查询。 如果传递了 `-o` 参数，则查询的结果以 [Pandas](https://pandas.pydata.org/) 数据帧的形式保存在 %%local Python 上下文中。 |
   | local |`%%local`<br>`a=1` |后续行中的所有代码在本地执行。 不管使用哪种内核，代码都必须是有效的 Python2 代码。 因此，即使所选**PySpark3**或**Spark**内核创建 notebook，如果你使用时`%%local`中单元格 magic，该单元也只能包含有效的 Python2 代码。 |
   | logs |`%%logs` |输出当前 Livy 会话的日志。 |
   | 删除 |`%%delete -f -s <session number>` |删除当前 Livy 终结点的特定会话。 无法删除针对内核本身启动的会话。 |
   | cleanup |`%%cleanup -f` |删除当前 Livy 终结点的所有会话，包括此笔记本的会话。 force 标志 -f 是必需的。 |

   > [!NOTE]  
   > 除了 PySpark 内核添加的 magic 以外，还可以使用[内置的 IPython magic](https://ipython.org/ipython-doc/3/interactive/magics.html#cell-magics)（包括 `%%sh`）。 可以使用 `%%sh` magic 在群集头节点上运行脚本和代码块。

- **自动可视化**。 Pyspark 内核自动可视化 Hive 和 SQL 查询的输出。 可以选择多种不同类型的视觉效果，包括表、饼图、折线图、分区图和条形图。

## <a name="parameters-supported-with-the-sql-magic"></a>%%sql magic 支持的参数
`%%sql` magic 支持不同的参数，可以使用这些参数控制运行查询时收到的输出类型。 下表列出了输出。

| 参数 | 示例 | 描述 |
| --- | --- | --- |
| -o |`-o <VARIABLE NAME>` |使用此参数将查询结果以 [Pandas](https://pandas.pydata.org/) 数据帧的形式保存在 %%local Python 上下文中。 数据帧变量的名称是指定的变量名称。 |
| -q |`-q` |使用此参数可关闭单元可视化。 如果不希望 autovisualize 到单元格的内容，并且只是想要作为数据帧捕获，然后使用`-q -o <VARIABLE>`。 如果想要关闭可视化而不捕获结果（例如，运行诸如 `CREATE TABLE` 语句的 SQL 查询），请使用不带 `-o` 参数的 `-q`。 |
| -m |`-m <METHOD>` |其中，**METHOD** 是 **take** 或 **sample**（默认为 **take**）。 如果方法为 **take**，内核将从 MAXROWS 指定的结果数据集顶部选择元素（如此表中稍后所述）。 如果方法为 **sample**，内核将根据 `-r` 参数进行数据集的元素随机采样，如此表中稍后所述。 |
| -r |`-r <FRACTION>` |此处的 **FRACTION** 是介于 0.0 与 1.0 之间的浮点数。 如果 SQL 查询的采样方法为 `sample`，则内核将针对结果集元素的指定分数随机采样。 例如，如果使用参数 `-m sample -r 0.01` 运行 SQL 查询，则会随机采样 1% 的结果行。 |
| -n |`-n <MAXROWS>` |**MAXROWS** 是整数值。 内核将输出行的数目限制为 **MAXROWS**。 如果 **MAXROWS** 是负数（例如 **-1**），结果集中的行数不受限制。 |

**示例：**

    %%sql -q -m sample -r 0.1 -n 500 -o query2
    SELECT * FROM hivesampletable

上述语句执行以下操作：

* 从 **hivesampletable** 中选择所有记录。
* 由于我们使用了-q，它会关闭 autovisualization。
* 由于使用了 `-m sample -r 0.1 -n 500`，因此将从 hivesampletable 的行中随机采样 10%，并将结果集的大小限制为 500 行。
* 最后，由于使用了 `-o query2`，因此也会将输出保存到名为 **query2** 的数据帧中。

## <a name="considerations-while-using-the-new-kernels"></a>使用新内核时的注意事项

不管使用哪种内核，使笔记本一直保持运行都会消耗群集资源。  使用这些内核时，由于上下文是预设的，仅退出笔记本并不会终止上下文，因此会继续占用群集资源。 合理的做法是在使用完笔记本后，使用笔记本“文件”菜单中的“关闭并停止”选项来终止上下文，并退出笔记本。  

## <a name="where-are-the-notebooks-stored"></a>笔记本存储在何处？

如果群集使用 Azure 存储作为默认存储帐户，Jupyter notebook 将保存到 **/HdiNotebooks** 文件夹下的存储帐户。  可以从存储帐户访问在 Jupyter 内部创建的笔记本、文本文件和文件夹。  例如，如果使用 Jupyter 创建文件夹 **myfolder** 和笔记本 **myfolder/mynotebook.ipynb**，可在存储帐户中通过 `/HdiNotebooks/myfolder/mynotebook.ipynb` 访问该笔记本。  反之亦然，如果直接将笔记本上传到 `/HdiNotebooks/mynotebook1.ipynb` 中的存储帐户，则可以从 Jupyter 查看该笔记本。  即使删除了群集，笔记本也仍会保留在存储帐户中。

> [!NOTE]  
> 将 Azure Data Lake Storage 作为默认存储的 HDInsight 群集不在关联的存储中存储笔记本。

将笔记本保存到存储帐户的方式与 [Apache Hadoop HDFS](https://hadoop.apache.org/docs/r1.2.1/hdfs_design.html) 兼容。 因此，如果通过 SSH 访问群集，可按以下代码片段中所示使用文件管理命令：

    hdfs dfs -ls /HdiNotebooks                            # List everything at the root directory – everything in this directory is visible to Jupyter from the home page
    hdfs dfs –copyToLocal /HdiNotebooks                   # Download the contents of the HdiNotebooks folder
    hdfs dfs –copyFromLocal example.ipynb /HdiNotebooks   # Upload a notebook example.ipynb to the root folder so it’s visible from Jupyter

不论群集是使用 Azure 存储还是 Azure Data Lake Storage 作为默认存储帐户，笔记本还会保存在群集头节点上的 `/var/lib/jupyter` 中。

## <a name="supported-browser"></a>支持的浏览器

Google Chrome 仅支持 Spark HDInsight 群集中的 Jupyter 笔记本。

## <a name="feedback"></a>反馈

新内核正处于发展阶段，一段时间后将变得成熟。 这可能也意味着，API 可随着这些内核的成熟而改变。 如果在使用这些新内核时有任何反馈，我们将不胜感激。 这对于内核最终版本的定调会很有帮助。 您可以保留在意见/反馈**反馈**这篇文章底部的部分。

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
* [使用适用于 IntelliJ IDEA 的 HDInsight 工具插件创建和提交 Spark Scala 应用程序](apache-spark-intellij-tool-plugin.md)
* [使用适用于 IntelliJ IDEA 的 HDInsight 工具插件远程调试 Apache Spark 应用程序](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [在 HDInsight 上的 Apache Spark 群集中使用 Apache Zeppelin 笔记本](apache-spark-zeppelin-notebook.md)
* [Use external packages with Jupyter notebooks（将外部包与 Jupyter 笔记本配合使用）](apache-spark-jupyter-notebook-use-external-packages.md)
* [Install Jupyter on your computer and connect to an HDInsight Spark cluster（在计算机上安装 Jupyter 并连接到 HDInsight Spark 群集）](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>管理资源
* [管理 Azure HDInsight 中 Apache Spark 群集的资源](apache-spark-resource-manager.md)
* [Track and debug jobs running on an Apache Spark cluster in HDInsight（跟踪和调试 HDInsight 中的 Apache Spark 群集上运行的作业）](apache-spark-job-debugging.md)
