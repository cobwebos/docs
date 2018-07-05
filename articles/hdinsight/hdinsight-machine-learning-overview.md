---
title: 机器学习概述 - Azure HDInsight | Microsoft Docs
description: 介绍 HDInsight 中的机器学习选项。
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 01/19/2018
ms.author: nitinme
ms.openlocfilehash: 6ccdb1fff5ecdcb61beedf1b12904d3785d4921d
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2018
ms.locfileid: "37048493"
---
# <a name="machine-learning-on-hdinsight"></a>HDInsight 中的机器学习

可以使用 HDInsight 通过大数据进行机器学习，以便从大量（千万亿字节甚至百亿亿字节）结构化、非结构化和快速移动的数据中获得有价值的见解。 HDInsight 中有多个机器学习选项：SparkML 和 MLlib、R、Hive 以及 Microsoft 认知工具包。

## <a name="sparkml-and-mllib"></a>SparkML 和 MLlib

[HDInsight Spark](spark/apache-spark-overview.md) 是 Azure 托管的 [Spark](http://spark.apache.org/) 产品，它是统一的开源并行数据处理框架，支持使用内存中处理来大幅提升大数据分析性能。 Spark 处理引擎是专为速度、易用性和复杂分析打造的产品。 Spark 的内存中分布式计算功能使其成为机器学习和图形计算中使用的迭代算法的最佳选择。 有两个可缩放的机器学习库为此分布式环境带来了算法建模功能，这两个库是：MLlib 和 SparkML。 MLlib 包含构建在 RDD 基础之上的原始 API。 SparkML 是一个较新的包，提供构建在 DataFrames 基础之上的更高级 API，用于构造 ML 管道。 SparkML 目前尚不支持 MLlib 的所有功能，但正在替换 MLlib 的角色，即充当 Spark 的标准机器学习库。

[MMLSpark](https://github.com/Azure/mmlspark) 是适用于 Apache Spark 的 Microsoft 机器学习库。 该库旨在提升数据科学家在 Spark 上的生产力，它不仅可以提高试验成功率，而且还能在极大型数据集上利用前沿的机器学习技术，包括深度学习。 MMLSpark 在生成可缩放 ML 模型（例如编制字符串的索引、强制数据进入机器学习算法预期的布局中、组合特征矢量）时，可以在 SparkML 的低级别 API 基础上提供一个层。 MMLSpark 库简化了可在 PySpark 中生成模型的这些任务以及其他常见任务。

## <a name="r"></a>R

[R](https://www.r-project.org/) 目前是世界上最常用的统计编程语言。 它是一种开源数据可视化工具，其社区的用户超过 250 万，并且仍在增长。 R 拥有蓬勃增长的用户群，其用户贡献的程序包超过 8,000 个，是许多需要机器学习的公司的极佳选择。 可以使用 ML Services 创建随时可与大型数据集和模型配合使用的 HDInsight 群集。 这项功能为数据科学家和统计学家提供了可通过 HDInsight 按需缩放的熟悉 R 界面，并消除了群集设置和维护方面的开销。

![通过 R Server 进行预测训练](./media/hdinsight-machine-learning-overview/r-training.png)

群集的边缘节点为连接到群集和运行 R 脚本提供了便捷的位置。  还可以选择跨群集的各个节点运行 R 脚本，只需使用 ScaleR 的 Hadoop Map Reduce 或 Spark 计算上下文即可。

在带 Spark 的 HDInsight 上使用 ML Services 时，可以使用 Spark 计算上下文跨群集的节点进行并行训练。 可以根据需要直接在边缘节点上运行 R 脚本，并行使用所有可用的核心。 也可以在边缘节点中运行代码，开始执行分布在群集的所有节点上的处理任务。 使用带 Spark 的 HDInsight 上的 ML Services，还可以根据需要并行执行开源 R 包中的函数。

## <a name="azure-machine-learning-and-hive"></a>Azure 机器学习和 Hive

Azure 机器学习不仅提供预测分析建模工具，还提供完全托管的服务，可以通过此服务将预测模型部署为随时可用的 Web 服务。 Azure 机器学习是云中的完整预测分析解决方案，可以用来创建、测试、操作和管理预测模型。 可以从大型算法库中进行选择、使用基于 Web 的工作室来构建模型，然后将模型轻松部署为 Web 服务。

![通过 Microsoft Azure 机器学习使高级分析可供 Hadoop 访问](./media/hdinsight-machine-learning-overview/hadoop-azure-ml.png)

使用 [Hive 查询](../machine-learning/team-data-science-process/create-features-hive.md)，在 HDInsight Hadoop 群集中创建数据特征。 *特征工程*尝试通过从原始数据创建特征，简化学习过程，从而增加学习算法的预测能力。 可以使用[“导入数据”模块](../machine-learning/studio/import-data.md)从 Azure ML 运行 HiveQL 查询，以及访问在 Hive 中处理和在 Blob 存储中存储的数据。

## <a name="microsoft-cognitive-toolkit"></a>Microsoft 认知工具包

[深度学习](https://www.microsoft.com/en-us/research/group/dltc/)是机器学习的一个分支，使用神经网络是受人类大脑的生物学过程启发。 许多研究人员将深度学习视为有前景的可增强人工智能的方法。 深度学习的例子包括口译工具、图像识别系统和计算机推理。

为了推进自身在深度学习方面的工作，Microsoft 开发了免费、易用的开源 [Microsoft 认知工具包](https://www.microsoft.com/en-us/cognitive-toolkit/)。 各种 Microsoft 产品、世界各地需要大规模部署深度学习的公司，以及对最新算法和技术感兴趣的学生都在使用该工具包。 

## <a name="see-also"></a>另请参阅

### <a name="scenarios"></a>方案

* [Spark 和机器学习：使用 HDInsight 中的 Spark 对使用 HVAC 数据生成温度进行分析](spark/apache-spark-ipython-notebook-machine-learning.md)
* [Spark 和机器学习：使用 HDInsight 中的 Spark 预测食品检查结果](spark/apache-spark-machine-learning-mllib-ipython.md)
* [使用 Mahout 生成影片建议](hadoop/apache-hadoop-mahout-linux-mac.md)
* [Hive 和 Azure 机器学习](../machine-learning/team-data-science-process/create-features-hive.md)
* [Hive 和 Azure 机器学习端到端教程](../machine-learning/team-data-science-process/hive-walkthrough.md)
* [在 Spark on HDInsight 中使用机器学习](../machine-learning/team-data-science-process/spark-overview.md)

### <a name="deep-learning-resources"></a>深度学习资源

* [带 Spark 的深度学习工具包](https://blogs.technet.microsoft.com/machinelearning/2017/04/25/using-microsofts-deep-learning-toolkit-with-spark-on-azure-hdinsight-clusters/)
* [Embarrassingly parallel image classification with Cognitive toolkit + Tensorflow on Spark](https://blogs.technet.microsoft.com/machinelearning/2017/04/12/embarrassingly-parallel-image-classification-using-cognitive-toolkit-tensorflow-on-azure-hdinsight-spark/)（使用认知工具包和 Tensorflow on Spark 执行易并行图像分类）
