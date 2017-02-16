---
title: "有关在 Azure HDInsight 上使用 Spark 展开数据科学的概述 | Microsoft Docs"
description: "Spark MLlib 工具包向分布式 HDInsight 环境引入了大量机器学习建模功能。"
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: a4e1de99-a554-4240-9647-2c6d669593c8
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/07/2016
ms.author: deguhath;bradsev;gokuma
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 091a753c8a183975c7aa340fa2f089adf4fe5574



---
# <a name="overview-of-data-science-using-spark-on-azure-hdinsight"></a>有关在 Azure HDInsight 上使用 Spark 展开数据科研的概述
[!INCLUDE [machine-learning-spark-modeling](../../includes/machine-learning-spark-modeling.md)]

这一系列主题显示了如何使用 HDInsight Spark 完成常见的数据科学任务，例如数据引入、特征工程、建模和模型评估。 所使用的数据是 2013 年 NYC 出租车行程和费用数据集样本。 生成的模型包括逻辑和线性回归、随机林和梯度提升树。 本主题还介绍了如何在 Azure Blob 存储 (WASB) 中存储这些模型，以及如何对其预测性能进行评分和评估。 更高级的主题介绍了如何使用交叉验证和超参数扫描训练模型。 本概述主题还介绍了如何设置 Spark 群集，需要完成所提供的三个演练中的步骤。 

[Spark](http://spark.apache.org/) 是一种开放源代码并行处理框架，支持内存中处理，以提升大数据分析应用程序的性能。 Spark 处理引擎是专为速度、易用性和复杂分析打造的产品。 Spark 的内存中分布式计算功能使其成为机器学习和图形计算中的迭代算法的最佳选择。 [MLlib](http://spark.apache.org/mllib/) 是 Spark 的可缩放机器学习库，向此分布式环境引入建模功能。 

[HDInsight Spark](../hdinsight/hdinsight-apache-spark-overview.md) 是 Azure 托管的开放源代码 Spark 产品。 它还包括对 Spark 群集上 **Jupyter PySpark 笔记本**的支持，这些笔记本可以运行 Spark SQL 交互式查询，用于对存储在 Azure Blob (WASB) 中的数据进行转换、筛选和可视化。 PySpark 是用于 Spark 的 Python API。 提供解决方案并显示相关图表以可视化此处的数据的代码片段在 Spark 群集上安装的 Jupyter 笔记本中运行。 这些主题中的建模步骤包含代码，显示每种类型模型的训练、评估、保存和使用方式。 

本演练中提供的设置步骤和代码适用于 HDInsight 3.4 Spark 1.6。 但是，此处位于笔记本中的代码是泛型代码，应适用于任何 Spark 群集。 如果不使用 HDInsight Spark，群集设置和管理步骤可能与此处所示内容稍有不同。

## <a name="prerequisites"></a>先决条件
1. 必须具有 Azure 订阅。 如果还没有 Azure 订阅，请参阅[获取 Azure 免费试用版](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。

2. 需要使用 HDInsight 3.4 Spark 1.6 群集完成本演练。 若要创建群集，请参阅[入门：在 Azure HDInsight 上创建 Apache Spark](../hdinsight/hdinsight-apache-spark-jupyter-spark-sql.md) 中提供的说明。 从“选择群集类型”菜单中指定群集类型和版本。 

![配置群集](./media/machine-learning-data-science-spark-overview/spark-cluster-on-portal.png)

<!-- -->

> [!NOTE]
> 有关演示如何使用 Scala 而非 Python 完成端到端数据科学过程任务的主题，请参阅[在 Azure 上使用 Scala 与 Spark 开展数据科学](machine-learning-data-science-process-scala-walkthrough.md)。
> 
> 

<!-- -->

> [!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]
> 
> 

## <a name="the-nyc-2013-taxi-data"></a>NYC 2013 年出租车数据
NYC 出租车行程数据是大约 20 GB（未压缩时约为 48 GB）的压缩逗号分隔值 (CSV) 文件，其中包含超过 1.73 亿个单独行程及每个行程支付的费用。 每个行程记录都包括上车和下车的位置和时间、匿名的出租车司机驾驶证编号和牌照（出租车的唯一 ID）编号。 数据涵盖 2013 年的所有行程，并每月在以下两个数据集中提供：

1. “trip_data”CSV 文件包含行程的详细信息，例如乘客数、上车和下车地点、行程持续时间和行程距离。 下面是一些示例记录：
   
        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868
2. “trip_fare”CSV 文件包含每个行程支付费用的详细信息，例如付款类型、费用金额、附加费和税金、小费和通行费以及支付的总金额。 下面是一些示例记录：
   
        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

我们已对这些文件进行 0.1% 采样，并将 trip\_data 和 trip\_fare CVS 文件联接到单个数据集中，以用作本演练的输入数据集。 联接 trip\_data 和 trip\_fare 的唯一键由以下字段组成：medallion、hack\_licence 和 pickup\_datetime。 每个数据集记录包含以下属性，用于表示 NYC 出租车行程：

| 字段 | 简要说明 |
| --- | --- |
| medallion |匿名的出租车牌照（出租车的唯一 ID） |
| hack_license |匿名的出租车司机驾驶证编号 |
| vendor_id |出租车供应商 ID |
| rate_code |NYC 出租车费率 |
| store_and_fwd_flag |停靠和行进标志 |
| pickup_datetime |上车日期和时间 |
| dropoff_datetime |下车日期和时间 |
| pickup_hour |上车小时 |
| pickup_week |上车星期（一年中的某一周） |
| weekday |工作日（周一到周日） |
| passenger_count |出租车行程中的乘客数 |
| trip_time_in_secs |行程时间（以秒为单位） |
| trip_distance |行程距离（以英里为单位） |
| pickup_longitude |上车经度 |
| pickup_latitude |上车纬度 |
| dropoff_longitude |下车经度 |
| dropoff_latitude |下车纬度 |
| direct_distance |上车位置和下车位置之间的直线距离 |
| payment_type |付款类型（现金、信用卡等） |
| fare_amount |费用金额 |
| surcharge |附加费 |
| mta_tax |MTA 税金 |
| tip_amount |小费金额 |
| tolls_amount |通行费金额 |
| total_amount |总金额 |
| tipped |是否有小费（0 表示没有，1 表示有） |
| tip_class |小费等级（0：0 美元，1：0-5 美元，2：6-10 美元，3：11-20 美元，4：大于 20 美元） |

## <a name="execute-code-from-a-jupyter-notebook-on-the-spark-cluster"></a>从 Spark 群集上的 Jupyter 笔记本执行代码
可以从 Azure 门户启动 Jupyter 笔记本。 在仪表板上找到 Spark 群集，然后单击进入群集管理页面。 若要打开与 Spark 群集相关联的笔记本，请依次单击“群集仪表板” -> “Jupyter 笔记本”。

![群集仪表板](./media/machine-learning-data-science-spark-overview/spark-jupyter-on-portal.png)

还可以浏览到 ***https://CLUSTERNAME.azurehdinsight.net/jupyter*** 访问 Jupyter 笔记本。 将此 URL 的 CLUSTERNAME 部分替换为你自己的群集名称。 需要使用管理员帐户密码才能访问笔记本。

![浏览 Jupyter 笔记本](./media/machine-learning-data-science-spark-overview/spark-jupyter-notebook.png)

选择 PySpark 查看包含几个使用 PySpark API 的预打包笔记本示例的目录。包含这一系列 Spark 主题的代码示例的笔记本可在 [Github](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/pySpark) 上获取

可以将笔记本直接从 GitHub 上传到 Spark 群集上的 Jupyter 笔记本服务器。 在 Jupyter 的主页上，单击屏幕右侧的“上传”按钮。 它将打开文件资源管理器。 你可以在此处粘贴笔记本的 Github（原始内容）URL，然后单击“打开”。 PySpark 笔记本可在以下 URL 中获取：

1. [pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb)
2. [pySpark-machine-learning-data-science-spark-model-consumption.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/pySpark-machine-learning-data-science-spark-model-consumption.ipynb)
3. [pySpark-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/pySpark-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb)

你将再次看到文件名显示在 Jupyter 文件列表上，并带有“上传”按钮。 单击此“上传”按钮。 现在你已导入笔记本。 重复上述步骤，上传本演练中的其他笔记本。

> [!TIP]
> 你可以右键单击浏览器上的链接，然后选择“复制链接”获取 GitHub 原始内容 URL。 可将此 URL 粘贴到“Jupyter 上传文件资源管理器”对话框中。
> 
> 

现在你可以：

* 通过单击笔记本查看代码。
* 通过按 **SHIFT-ENTER** 执行每个单元格。
* 通过依次单击“单元格” -> “运行”运行整个笔记本。
* 使用查询的自动可视化。

> [!TIP]
> PySpark 内核自动将 SQL (HiveQL) 查询的输出可视化。 可通过使用笔记本中的“类型”菜单按钮从多个不同类型的可视化（表、饼图、行、区域或栏）中选择：
> 
> 

![泛型方法的逻辑回归 ROC 曲线](./media/machine-learning-data-science-spark-overview/pyspark-jupyter-autovisualization.png)

## <a name="whats-next"></a>后续步骤
现在，已完成 HDInsight Spark 群集设置并且上传了 Jupyter 笔记本，你可以随时完成对应于这三个 PySpark 笔记本的主题。 它们介绍了如何浏览你的数据以及如何创建和使用模型。 高级数据浏览和建模笔记本介绍了如何包括交叉验证、超参数扫描和模型评估。 

**使用 Spark 进行数据浏览和建模：**通过完成[使用 Spark MLlib 工具包为数据创建二元分类和回归模型](machine-learning-data-science-spark-data-exploration-modeling.md)主题，浏览数据集并创建、评分和评估机器学习模型。

**模型使用：**若要了解如何评分在本主题中创建的分类和回归模型，请参阅[评分和评估 Spark 生成的机器学习模型](machine-learning-data-science-spark-model-consumption.md)。

**交叉验证和超参数扫描**：请参阅[使用 Spark 进行高级数据浏览和建模](machine-learning-data-science-spark-advanced-data-exploration-modeling.md)，了解如何使用交叉验证和超参数扫描训练模型




<!--HONumber=Dec16_HO2-->


