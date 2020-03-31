---
title: 使用 Scala 的 PySpark 分析 HDInsight Spark - Team Data Science Process
description: 团队数据科学流程的示例，这些流程在 Azure HDInsight Spark 上使用 PySpark 和 Scala。
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 91aac279a264d64ace5988d147c4caf8c52e9656
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75864139"
---
# <a name="hdinsight-spark-data-science-walkthroughs-using-pyspark-and-scala-on-azure"></a>在 Azure 上使用 PySpark 和 Scala 进行 HDInsight Spark 数据科学演练

这些演练在 Azure Spark 群集上使用 PySpark 和 Scala 执行预测分析。 它们遵循 Team Data Science Process 中所述的步骤。 有关 Team Data Science Process 的概述，请参阅[数据科学过程](overview.md)。 有关 Spark on HDInsight 的概述，请参阅 [Spark on HDInsight 简介](../../hdinsight/spark/apache-spark-overview.md)。

执行团队数据科学流程的其他数据科学演练按他们使用**的平台**分组。 有关这些示例的明细，请参阅[执行 Team Data Science Process 的演练](walkthroughs.md)。

## <a name="predict-taxi-tips-using-pyspark-on-azure-spark"></a>在 Azure Spark 上使用 PySpark 预测出租车小费

使用纽约的出租车数据[，Azure HDInsight 演练上的"使用 Spark"](spark-overview.md)可预测是否支付了小费以及预期金额的范围。 本示例使用团队数据科学流程，使用[Azure HDInsight Spark 群集](https://azure.microsoft.com/services/hdinsight/)来存储、浏览和功能工程师数据，这些数据来自公开可用的纽约市出租车旅行和票价数据集。 此概述主题使用 HDInsight Spark 群集和 Jupyter PySpark 笔记本。 这些 Notebook 演示如何浏览数据以及如何创建和使用模型。 高级数据浏览和建模笔记本介绍了如何包括交叉验证、超参数扫描和模型评估。

### <a name="data-exploration-and-modeling-with-spark"></a>使用 Spark 进行数据探索和建模 
通过完成[使用 Spark MLlib 工具包为数据创建二元分类和回归模型](spark-data-exploration-modeling.md)主题，探索数据集并创建、评分和评估机器学习模型。

### <a name="model-consumption"></a>使用模型
若要了解如何评分在本主题中创建的分类和回归模型，请参阅[评分和评估 Spark 生成的机器学习模型](spark-model-consumption.md)。

### <a name="cross-validation-and-hyperparameter-sweeping"></a>交叉验证和超参数扫描
有关如何使用交叉验证和超参数扫描对模型进行训练[的高级数据探索和建模](spark-advanced-data-exploration-modeling.md)。


## <a name="predict-taxi-tips-using-scala-on-azure-spark"></a>在 Azure Spark 上使用 Scala 预测出租车小费

在 Azure 演练[中使用 Spark](scala-walkthrough.md)会预测是否支付了小费以及预期支付的金额范围。 该演练介绍如何通过 Azure HDInsight Spark 群集上的 Spark 机器学习库 (MLlib) 和 SparkML 包，对受监督的机器学习任务使用 Scala。 它引导您完成构成[数据科学过程](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)的任务：数据引入和探索、可视化、功能工程、建模和模型消耗。 生成的模型包括逻辑和线性回归、随机林和梯度提升树。


## <a name="next-steps"></a>后续步骤

有关团队数据科学流程的概述，请参阅[团队数据科学流程概述](overview.md)。

有关团队数据科学流程生命周期的讨论，请参阅[团队数据科学流程生命周期](lifecycle.md)。 此生命周期概述了项目在执行时通常遵循的步骤（从开始到结束）。 

