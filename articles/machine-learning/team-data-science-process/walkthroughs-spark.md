---
title: 在 Azure 上使用 PySpark 和 Scala 进行 HDInsight Spark 演练 | Microsoft Docs
description: Team Data Science Process 的示例，演练如何在 Azure HDInsight Spark 上使用 PySpark 和 Scala 来执行预测分析。
services: machine-learning
documentationcenter: ''
author: deguhath
manager: jhubbard
editor: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/04/2017
ms.author: deguhath
ms.openlocfilehash: 06498f55b38be98441ddceb8f1667c89d257f8fd
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/03/2018
---
# <a name="hdinsight-spark-data-science-walkthroughs-using-pyspark-and-scala-on-azure"></a>在 Azure 上使用 PySpark 和 Scala 进行 HDInsight Spark 数据科学演练

这些演练在 Azure Spark 群集上使用 PySpark 和 Scala 执行预测分析。 它们遵循 Team Data Science Process 中所述的步骤。 有关 Team Data Science Process 的概述，请参阅[数据科学过程](overview.md)。 有关 Spark on HDInsight 的概述，请参阅 [Spark on HDInsight 简介](../../hdinsight/spark/apache-spark-overview.md)。

其他执行 Team Data Science Process 的数据科学演练按所使用的**平台**分组。 有关这些示例的明细，请参阅[执行 Team Data Science Process 的演练](walkthroughs.md)。

## <a name="predict-taxi-tips-using-pyspark-on-azure-spark"></a>在 Azure Spark 上使用 PySpark 预测出租车小费

[在 Azure HDInsight 上使用 Spark](spark-overview.md) 演练使用纽约出租车数据来预测是否会支付小费，以及预期支付的金额范围。 该演练在方案中使用 Team Data Science Process，以便使用 [Azure HDInsight Spark 群集](https://azure.microsoft.com/services/hdinsight/)存储、探索并特征化纽约市出租车行程与车费公用数据集中的工程数据。 本概述主题会设置 HDInsight Spark 群集和 Jupyter PySpark Notebook，以便在演练的余下内容中使用。 这些 Notebook 演示如何浏览数据以及如何创建和使用模型。 高级数据浏览和建模笔记本介绍了如何包括交叉验证、超参数扫描和模型评估。

### <a name="data-exploration-and-modeling-with-spark"></a>使用 Spark 进行数据探索和建模 
通过完成[使用 Spark MLlib 工具包为数据创建二元分类和回归模型](spark-data-exploration-modeling.md)主题，探索数据集并创建、评分和评估机器学习模型。

### <a name="model-consumption"></a>使用模型
若要了解如何评分在本主题中创建的分类和回归模型，请参阅[评分和评估 Spark 生成的机器学习模型](spark-model-consumption.md)。

### <a name="cross-validation-and-hyperparameter-sweeping"></a>交叉验证和超参数扫描
参阅[使用 Spark 进行高级数据探索和建模](spark-advanced-data-exploration-modeling.md)，了解如何使用交叉验证和超参数扫描训练模型。


## <a name="predict-taxi-tips-using-scala-on-azure-spark"></a>在 Azure Spark 上使用 Scala 预测出租车小费

[在 Azure 上将 Scala 与 Spark 配合使用](scala-walkthrough.md)演练使用纽约出租车数据来预测是否会支付小费，以及预期支付的金额范围。 该演练介绍如何通过 Azure HDInsight Spark 群集上的 Spark 机器学习库 (MLlib) 和 SparkML 包，对受监督的机器学习任务使用 Scala。 它指导完成构成[数据科学过程](http://aka.ms/datascienceprocess)的任务：数据引入和浏览、可视化、特征工程、建模和模型使用。 生成的模型包括逻辑和线性回归、随机林和梯度提升树。


## <a name="next-steps"></a>后续步骤

有关构成 Team Data Science Process 的关键组件的讨论，请参阅 [Team Data Science Process 概述](overview.md)。

有关可用于构建数据科学项目的 Team Data Science Process 生命周期的讨论，请参阅 [Team Data Science Process 生命周期](lifecycle.md)。 生命周期概述了执行项目时，其从开始到结束所遵循的步骤。 

