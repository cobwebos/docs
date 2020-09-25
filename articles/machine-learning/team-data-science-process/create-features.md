---
title: 数据科学中的特征工程 - Team Data Science Process
description: 了解特征工程及其在机器学习数据增强过程中的作用。
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: conceptual
ms.date: 05/14/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath, contperfq4
ms.openlocfilehash: 5e84a3930d350ec45cef7119342e3e4d2d5daaee
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91250651"
---
# <a name="feature-engineering-in-data-science"></a>数据科学中的特征工程

在本文中，你将了解特征工程及其在机器学习增强数据中的作用。 从在 [Azure 机器学习工作室（经典）](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)试验中提取的说明性示例中学习。 

* **特征工程**：基于原始数据中创建新特征来提高学习算法的预测能力的过程。 工程特征应捕获原始特征集中不易发现的附加信息。
* **特征选择**：选择部分关键特征来降低训练问题的维度的过程。

通常，先应用特征工程来生成其他特征，然后执行特征选择来消除不相关、冗余或高度相关的特征 。

特征工程和特征选择在 Team Data Science Process (TDSP) 的[建模阶段](lifecycle-modeling.md)完成。 要详细了解 TDSP 和数据科学生命周期，请参阅[什么是 TDSP？](overview.md)

## <a name="what-is-feature-engineering"></a>什么是特征工程？

训练数据由一个矩阵组成，该矩阵由行和列构成。 矩阵中的每一行都是一个观察或记录。 每行的列是描述每条记录的特征。 实验设计中指定的特征应显示数据中的模式特征。

尽管许多原始数据字段可直接用于训练模型，但通常需要为增强的训练数据集创建其他（工程）特征。

用于增强训练的工程特征提供了能更好区分数据中的模式的信息。 但是，此过程是一件艺术。 要做出正确可靠而又富有成效的决策，通常需要领域专业知识。

## <a name="example-1-add-temporal-features-for-a-regression-model"></a>示例 1：为回归模型添加临时特征

使用 Azure 机器学习工作室（经典）中的[自行车租赁需求预测](https://gallery.azure.ai/Experiment/Regression-Demand-estimation-4)实验来演示如何为回归任务设计特征。 此实验的目的是预测在特定月/日/小时内自行车的租赁需求。

### <a name="bike-rental-dataset"></a>自行车租赁数据集

[自行车租赁 UCI 数据集](http://archive.ics.uci.edu/ml/datasets/Bike+Sharing+Dataset/)基于美国自行车共享公司的真实数据。 它表示 2011 年和 2012 年某一天中特定小时内的自行车租赁数量。 它包含 17,379 行和 17 列。

原始特征集包含天气条件（温度/湿度/风速）和日期类型（假日/工作日）。 要预测的字段是计数，表示特定小时内的自行车租赁计数。 计数范围从 1 到 977。

### <a name="create-a-feature-engineering-experiment"></a>创建特征工程实验

为了实现在训练数据中构建有效特征的目标，使用四个不同的训练数据集通过相同的算法构建四个回归模型。 四个数据集表示相同的原始输入数据，但是具有越来越多的特征集。 这些特征可分为以下四类：

1. A = 所预测的那天的天气 + 假日 + 工作日 + 周末特征
2. B = 过去 12 小时内每小时租用的自行车数量
3. C = 过去 12 天内每天同一小时租用的自行车数量
4. D = 过去 12 周内同一天同一小时租用的自行车数量

除了已经存在于最初的原始数据中的特征集 A 之外，其他三个特征集都需要通过特征工程过程创建。 特征集 B 捕获近期的自行车需求量。 特征集 C 捕获特定时间的自行车需求量。 特征集 D 捕获一周中特定日期特定小时的自行车需求量。 四个训练数据集分别包括特征集 A、A + B、A + B + C 和 A + B + C + D。

### <a name="feature-engineering-using-studio-classic"></a>使用工作室（经典）的特征工程

在工作室（经典）中，下面 4 个训练数据集通过预处理的输入数据集的 4 个分支形成。 除了最左边的分支外，每个分支都包含[执行 R 脚本](https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/)模块，会在此模块中构建派生特征（特征集 B、C 和 D）并附加到导入的数据集。

下图演示了用于在左侧第二个分支中创建特征集 B 的 R 脚本。

![创建特征](./media/create-features/addFeature-Rscripts.png)

### <a name="results"></a>结果

下表总结了四个模型的性能结果比较： 

![结果比较](./media/create-features/result1.png)

特征 A + B + C 显示出最佳结果。 训练数据中包括附加特征集时，错误率会降低。 它验证了一个假设，即特征集 B 和 C为回归任务提供附加的相关信息。 但添加 D 特征似乎并不有助于进一步降低错误率。

## <a name="example-2-create-features-for-text-mining"></a><a name="example2"></a> 示例 2：创建用于文本挖掘的特征

特征工程广泛应用于与文本挖掘相关的任务，例如文档分类和情绪分析。 由于原始文本的各个部分通常用作输入数据，因此需要特征工程过程来创建涉及单词/短语频率的特征。

### <a name="feature-hashing"></a>特征哈希

为了完成这个任务，应用被称为[特征哈希](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/feature-hashing)的技术将任意文本特征有效地转换为索引。 该方法将哈希函数应用于特征并将它们的哈希值直接用作索引，而不是将每个文本特征（单词/短语）与特定索引相关联。

在工作室（经典）中，有一个[特征哈希](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/feature-hashing)模块，你可用它来轻松创建单词/短语特征。 下图显示使用此模块的示例。 输入数据集包含两列：书籍评分（范围为 1 到 5），以及实际审核内容。 此模块的目标是检索一系列新特征，这些特征显示特定书评中相应单词/短语出现的频率。 若要使用此模块，完成以下步骤：

* 首先，选择包含输入文本（此示例中的“Col2”）的列。
* 其次，将“Hashing bitsize”设置为 8，这意味着将创建“2^8=256”个特征。 所有文本中的单词/短语将哈希处理为 256 个索引。 参数“Hashing bitsize”的范围是 1 到 31。 如果将单词/短语设置为更大的数字，则其不太可能被哈希处理到相同的索引中。
* 最后，将参数“N-grams”设置为 2。 此值会从输入文本中获得单元语法（每个单词的特征）和二元语法（每对相邻单词的特征）的出现频率。 参数“N 元语法”的范围是 0 到 10 ，其指示要包括在特征中的连续单词的最大数目。  

![“特征哈希”模块](./media/create-features/feature-Hashing1.png)

下图显示了这些新特征的外观。

![“特征哈希”示例](./media/create-features/feature-Hashing2.png)

## <a name="conclusion"></a>结束语
设计和选择的特征提高了训练过程的效率，此过程尝试提取数据中包含的关键信息。 它们还提高了这些模型准确分类输入数据以及更加可靠地预测感兴趣的结果的能力。

特征工程和选择也可以结合起来使用，以使学习在计算上更易处理。 它通过增加然后减少校准或训练模型所需的特征数量来实现这一目标。 从数学上来说，所选特征是一组最小的独立变量，它们解释数据中的模式，并成功预测结果。

并不总是必须执行特征工程或特征选择。 这取决于数据、选择的算法以及实验的目的。

## <a name="next-steps"></a>后续步骤

若要在特定环境中创建数据功能，请参阅以下文章：

* [在 SQL Server 中为数据创建功能](create-features-sql-server.md)
* [使用 Hive 查询创建用于 Hadoop 群集中数据的功能](create-features-hive.md)