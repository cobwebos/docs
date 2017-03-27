---
title: "数据科学中的特征工程 | Microsoft Docs"
description: "解释特征工程的目的，并提供其在机器学习的数据增强过程中的作用的相关示例。"
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 3fde69e8-5e7b-49ad-b3fb-ab8ef6503a4d
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/09/2016
ms.author: zhangya;bradsev
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 4a2bb073360b372bb66281bceb2292d5d29f5625


---
# <a name="feature-engineering-in-data-science"></a>数据科学中的特征工程
本主题解释特征工程的目的，并提供其在机器学习的数据增强过程中的作用的相关示例。 用于说明此过程的示例来自 Azure 机器学习工作室。 

[!INCLUDE [cap-create-features-data-selector](../../includes/cap-create-features-selector.md)]

该**菜单**链接到介绍如何在各种环境中为数据创建特征的主题。 此任务是[团队数据科学过程 (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/) 中的一个步骤。

特征工程尝试通过从原始数据创建特征，帮助简化学习过程，从而增加学习算法的预测能力。 特征的工程设计和选择是 [Team Data Science Process 生命周期是什么？](data-science-process-overview.md)中描述的 TDSP 的一部分 特征工程和选择是 TDSP 的**开发特征**步骤的一部分。 

* **特征工程**：该过程尝试从数据中的现有原始特征创建其他相关特征，并提高学习算法的预测能力。
* **特征选择**：该过程选择原始数据特征的关键子集，试图降低定型问题的维度。

通常，首先应用**工程**生成其他特征，然后执行**特征选择**步骤，消除不相关、冗余或高度相关的特征。

机器学习中使用的定型数据通常可以通过从收集的原始数据中提取特征来增强。 在学习如何分类手写字符映像的上下文中，工程特征的一个示例是创建从原始位分布数据构建的位密度映射。 与仅直接使用原始分布相比，该映射可以更有效地定位字符的边缘。

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="creating-features-from-your-data---feature-engineering"></a>从数据创建特征 - 特征工程
定型数据包括由示例（存储在行中的记录或观察）组成的矩阵，每个示例具有一组特征（存储在列中的变量或字段）。 期望实验设计中指定的特征能显示数据中的模式特征。 虽然许多原始数据字段可以直接包括在用于定型模型的所选特征集中，但是通常情况下，需要从原始数据中的特征构建其他（工程）特征以生成增强的定型数据集。

应该创建何种特征以在定型模型时增强数据集？ 增强定型的工程特征提供了相关信息，可更好区分数据中的模式。 我们期望新特征可以提供原始或现有特征集中未清楚捕获或难以显示的附加信息。 但是，此过程是一件艺术。 做出正确可靠而又富有成效的决策通常需要一些领域专业知识。

开始 Azure 机器学习时，若要具体掌握此过程，最简单的方法是使用 Studio 中提供的示例。 这里介绍了两个示例：

* 一个回归示例，目标值已知的监督实验中的[预测自行车租赁数量](http://gallery.cortanaintelligence.com/Experiment/Regression-Demand-estimation-4)
* 一个使用[特征哈希](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/)的文本挖掘分类示例

## <a name="example-1-adding-temporal-features-for-regression-model"></a>示例1：为回归模型添加临时特征
使用 Azure 机器学习工作室中的“自行车需求预测”实验来演示如何为回归任务设计特征。 此实验的目的是预测自行车的需求，即在特定月/日/小时内的自行车租赁数量。 数据集“自行车租赁 UCI 数据集”用作原始输入数据。 此数据集基于 Capital Bikeshare 公司的实际数据，该公司维护着美国华盛顿特区的自行车租赁网络。 数据集表示 2011 年和 2012 年的某一天中的特定小时内的自行车租赁数量，其包含 17379 行和 17 列。 原始特征集包含天气条件（温度/湿度/风速）和日期类型（假日/工作日）。 要预测的字段是“cnt”，表示特定小时内的自行车租赁计数，范围是 1 到 977。

为了实现在定型数据中构建有效特征的目标，使用四个不同的定型数据集通过相同的算法构建四个回归模型。 四个数据集表示相同的原始输入数据，但是具有越来越多的特征集。 这些特征可分为以下四类：

1. A = 所预测的那天的天气 + 假日 + 工作日 + 周末特征
2. B = 过去 12 小时内每小时租用的自行车数量
3. C = 过去 12 天内每天同一小时租用的自行车数量
4. D = 过去 12 周内同一天同一小时租用的自行车数量

除了已经存在于最初的原始数据中的特征集 A 之外，其他三个特征集都需要通过特征工程过程创建。 特征集 B 捕获最近的自行车需求量。 特征集 C 捕获特定时间的自行车需求量。 特征集 D 捕获一周中特定日期特定小时的自行车需求量。 四个定型数据集分别包括特征集 A、A + B、A + B + C 和 A + B + C + D。

在 Azure 机器学习实验中，这四个定型数据集通过来自预处理输入数据集的四个分支形成。 除了最左边的分支之外，每个分支都包含[执行 R 脚本](https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/)模块，将在此模块中分别构建一组派生特征（特征集 B、C 和 D）并附加到导入的数据集。 下图演示了用于在左侧第二个分支中创建特征集 B 的 R 脚本。

![创建特征](./media/machine-learning-data-science-create-features/addFeature-Rscripts.png)

下表总结了四个模型的性能结果比较。 特征 A + B + C 显示出最佳结果。 注意，定型数据中包括附加特征集时，错误率会降低。 它验证了我们的假设，即特征集 B 和 C为回归任务提供附加的相关信息。 但添加 D 特征似乎并不有助于进一步降低错误率。

![结果比较](./media/machine-learning-data-science-create-features/result1.png)

## <a name="a-nameexample2a-example-2-creating-features-in-text-mining"></a><a name="example2"></a> 示例 2：在文本挖掘中创建特征
特征工程广泛应用于与文本挖掘相关的任务，如文档分类和情绪分析。 例如，想要将文档分成几个类别时，典型的假设是包含在一个文档类别中的单词/短语不太可能出现在另一个文档类别中。 换句话说，单词/短语分布的频率能够显示不同的文档类别的特征。 在文本挖掘应用程序中，由于文本内容的各个部分通常用作输入数据，因此需要特征工程过程来创建涉及单词/短语频率的特征。

为了完成这个任务，应用被称为**特征哈希**的技术将任意文本特征有效地转换为索引。 该方法通过将哈希函数应用于特征并将它们的哈希值直接用作索引来起作用，而不是将每个文本特征（单词/短语）与特定索引相关联。

在 Azure 机器学习中，有一个[特征哈希](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/)模块，可以方便地创建这些词/短语特征。 下图显示使用此模块的示例。 输入数据集包含两列：书籍评分（范围为 1 到 5），以及实际审核内容。 此[特征哈希](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/)模块的目标是检索一系列新特征，这些特征显示特定书籍审核中相应单词/短语的出现频率。 若要使用此模块，需要完成以下步骤：

* 首先，选择包含输入文本（此示例中的“Col2”）的列。
* 其次，将“Hashing bitesize”设置为 8，这意味着将创建 2 ^ 8 = 256 个特征。 所有文本中的单词/短语将哈希处理为 256 个索引。 参数“Hashing bitsize”的范围是 1 到 31。 如果将单词/短语设置为更大的数字，则其不太可能被哈希处理到相同的索引中。
* 然后，将参数“N 元语法”设置为 2。 这会从输入文本中获得单元语法（每个单词的特征）和二元语法（每对相邻单词的特征）的出现频率。 参数“N 元语法”的范围是 0 到 10 ，其指示要包括在特征中的连续单词的最大数目。  

![“特征哈希”模块](./media/machine-learning-data-science-create-features/feature-Hashing1.png)

下图显示了这些新特征的外观。

![“特征哈希”示例](./media/machine-learning-data-science-create-features/feature-Hashing2.png)

## <a name="conclusion"></a>结束语
设计和选择的特征提高了定型过程的效率，此过程尝试提取数据中包含的关键信息。 它们还提高了这些模型准确分类输入数据以及更加可靠地预测感兴趣的结果的能力。 特征工程和选择也可以结合起来使用，以使学习在计算上更易处理。 它通过增加然后减少校准或定型模型所需的特征数量来实现这一目标。 从数学上来说，所选的定型模型的特征是一组最小的独立变量，其解释数据中的模式，然后成功预测结果。

注意，并不总是必须执行特征工程或特征选择。 根据拥有的或收集的数据、选择的算法以及实验的目的决定是否需要。




<!--HONumber=Nov16_HO3-->


