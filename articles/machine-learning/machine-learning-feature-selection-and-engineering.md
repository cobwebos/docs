---
title: "Azure 机器学习中的特征工程和选择 | Microsoft Docs"
description: "解释特征选择和特征工程的目的，并提供其在机器学习的数据增强过程中的作用的相关示例。"
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 9ceb524d-842e-4f77-9eae-a18e599442d6
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2016
ms.author: zhangya;bradsev
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 1d29f416fe7d5ef3f74cf64122a84acc4a4535da


---
# <a name="feature-engineering-and-selection-in-azure-machine-learning"></a>Azure 机器学习中的特征工程和选择
本主题介绍特征工程和特征选择在机器学习的数据增强过程中的作用。 它通过使用 Azure 机器学习工作室提供的示例说明这些过程所涉及的内容。

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

机器学习中使用的定型数据通常可以通过从收集的原始数据中选择或提取特征来增强。 在学习如何分类手写字符映像的上下文中，工程特征的一个示例是从原始位分布数据构建的位密度映射。 与原始分布相比，该映射可以更有效地定位字符的边缘。

设计和选择的特征提高了定型过程的效率，此过程尝试提取数据中包含的关键信息。 它们还提高了这些模型准确分类输入数据以及更加可靠地预测感兴趣的结果的能力。 特征工程和选择也可以结合起来使用，以使学习在计算上更易处理。 它通过增加然后减少校准或定型模型所需的特征数量来实现这一目标。 从数学上来说，所选的定型模型的特征是一组最小的独立变量，其解释数据中的模式，然后成功预测结果。

特征的工程和选择是较大过程中的一部分，通常由以下四个步骤组成：

* 数据收集
* 数据增强
* 模型构造
* 后处理

工程和选择构成了机器学习的数据增强步骤。 此过程的三个方面可能因我们的目的而有所不同：

* **数据预处理**：此过程尝试确保收集的数据干净且一致。 它包括诸如集成多个数据集、处理缺失数据、处理不一致数据和转换数据类型等任务。
* **特征工程**：该过程尝试从数据中的现有原始特征创建其他相关特征，并提高学习算法的预测能力。
* **特征选择**：该过程选择原始数据特征的关键子集，以降低定型问题的维度。

本主题仅介绍数据增强过程的特征工程和特征选择方面。 有关数据预处理步骤的详细信息，请参阅[在 Azure 机器学习工作室中预处理数据](https://azure.microsoft.com/documentation/videos/preprocessing-data-in-azure-ml-studio/)。

## <a name="creating-features-from-your-data--feature-engineering"></a>从数据创建特征 - 特征工程
定型数据包括由示例（存储在行中的记录或观察）组成的矩阵，每个示例具有一组特征（存储在列中的变量或字段）。 期望实验设计中指定的特征能显示数据中的模式特征。 虽然许多原始数据字段可以直接包括在用于训练模型的所选特征集中，经常需要从原始数据中的特征构建其他工程特征以生成增强的定型数据集。

应该创建何种特征以在训练模型时增强数据集？ 增强定型的工程特征提供了相关信息，可更好区分数据中的模式。 我们期望新特征可以提供原始或现有特征集中未清楚捕获或难以显示的附加信息，但此过程是一件艺术。 做出正确可靠而又富有成效的决策通常需要一些领域专业知识。

开始 Azure 机器学习时，通过使用机器学习工作室中提供的示例具体掌握此过程最为简单。 这里介绍了两个示例：

* 目标值已知的监督实验中的回归示例（[预测自行车租赁数量](http://gallery.cortanaintelligence.com/Experiment/Regression-Demand-estimation-4)）
* 使用[特征哈希][feature-hashing]的文本挖掘分类示例

### <a name="example-1-adding-temporal-features-for-a-regression-model"></a>示例1：为回归模型添加临时特征
若要演示如何为回归任务设计特征，请在 Azure 机器学习工作室中使用“自行车需求预测”实验。 此实验的目的是预测自行车的需求，即在特定月、日或小时内的自行车租赁数量。 数据集**自行车租赁 UCI 数据集**用作原始输入数据。

此数据集基于 Capital Bikeshare 公司的实际数据，该公司维护着美国华盛顿特区的自行车租赁网络。 数据集表示 2011 年到 2012 年的某一天中的特定小时内的自行车租赁数量，包含 17379 行和 17 列。 原始特征集包含天气条件（温度、湿度、风速）和日期类型（假日或工作日）。 要预测的字段是 **cnt**，表示特定小时内自行车租赁的计数，范围从 1 到 977。

若要基于定型数据构建有效特征，通过使用相同的算法，但不同的四个定型数据集即可构建四个回归模型。 四个数据集表示相同的原始输入数据，但是具有越来越多的特征集。 这些特征可分为以下四类：

1. A = 所预测的那天的天气 + 假日 + 工作日 + 周末特征
2. B = 过去 12 小时内每小时租用的自行车数量
3. C = 过去 12 天内每天同一小时租用的自行车数量
4. D = 过去 12 周内同一天同一小时租用的自行车数量

除了已经存在于最初的原始数据中的特征集 A 之外，其他三个特征集都需要通过特征工程过程创建。 特征集 B 捕获最近的自行车需求量。 特征集 C 捕获特定时间的自行车需求量。 特征集 D 捕获一周中特定日期特定小时的自行车需求量。 四个定型数据集中的每一个都分别包括特征集 A、A+B、A+B+C 和 A+B+C+D。

在 Azure 机器学习实验中，这四个定型数据集通过来自预处理输入数据集的四个分支形成。 除了最左边的分支之外，每个分支都包含[执行 R 脚本][execute-r-script]模块，将在此模块中分别构建一组派生特征（特征集 B、C 和 D）并附加到导入的数据集。 下图演示了用于在左侧第二个分支中创建特征集 B 的 R 脚本。

![创建特征集](./media/machine-learning-feature-selection-and-engineering/addFeature-Rscripts.png)

下表总结了四个模型的性能结果比较。 特征 A + B + C 显示出最佳结果。 注意，定型数据中包括附加特征集时，错误率会降低。 这将验证我们的假设，即特征集 B 和 C 会为回归任务提供附加的相关信息。 添加 D 特征集似乎并未有助于进一步降低错误率。

![比较性能结果](./media/machine-learning-feature-selection-and-engineering/result1.png)

### <a name="a-nameexample2a-example-2-creating-features-in-text-mining"></a><a name="example2"></a> 示例 2：使用文本挖掘创建特征
特征工程广泛应用于与文本挖掘相关的任务，如文档分类和情绪分析。 例如，想要将文档分成几个类别时，典型的假设是包含在一个文档类别中的单词或短语不太可能出现在另一个文档类别中。 换句话说，单词或短语分布的频率能够描绘不同文档类别的特征。 在文本挖掘应用程序中，需要特征工程过程来创建涉及单词或短语频率的特征，因为文本内容的各个部分通常用作输入数据。

为了完成这个任务，应用被称为*特征哈希*的技术将任意文本特征有效地转换为索引。 该方法通过将哈希函数应用于特征并将它们的哈希值直接用作索引来起作用，而不是将每个文本特征（单词或短语）与特定索引相关联。

在 Azure 机器学习中，有一个[特征哈希][feature-hashing]模块，可以创建这些词或短语特征。 下图显示使用此模块的示例。 输入数据集包含两列：书籍评分（范围为 1 到 5），以及实际评论内容。 此[特征哈希][feature-hashing]模块的目标是检索新特征，这些特征显示特定书籍评论中相应单词或短语的出现频率。 若要使用此模块，需要完成以下步骤：

1. 选择包含输入文本的列（此示例中的 **Col2**）。
2. 将 *Hashing bitesize* 设置为 8，这意味着将创建 2^8 = 256 个特征。 文本中的单词或短语将哈希处理为 256 个索引。 参数 *Hashing bitsize* 的范围是 1 到 31。 如果该参数设置为较大数字，单词或短语不太可能被哈希处理到相同的索引中。
3. 将参数 *N-grams* 设置为 2。 这会从输入文本中检索一元语法（每个单词的特征）和二元语法（每对相邻单词的特征）的出现频率。 参数 *N-grams* 的范围是 0 到 10，其指示要包括在特征中的连续单词的最大数目。  

![特征哈希模块](./media/machine-learning-feature-selection-and-engineering/feature-Hashing1.png)

下图显示了这些新特征的外观。

![特征哈希示例](./media/machine-learning-feature-selection-and-engineering/feature-Hashing2.png)

## <a name="filtering-features-from-your-data--feature-selection"></a>从数据中筛选特征 - 特征选择
*特征选择*是一个过程，通常适用于为预测建模任务（如分类或回归任务）构建定型数据集。 目标是从原始数据集中选择特征子集，即通过使用最少特征集来表示数据中最大方差量以减少其维度。 此特征子集仅包含要包括用于训练模型的特征。 特征选择有两个主要目的：

* 特征选择通常通过消除不相关、冗余或高度相关的特征，来提高分类准确度。
* 特征选择会减少特征数，这使模型定型过程更高效。 这对于训练昂贵的学习者（例如支持向量机）尤其重要。

虽然特征选择试图减少数据集中用于训练模型的特征数，但通常不会将它称为*降维*（术语）。 功能选择方法提取数据中原始特征的子集，而不会改变它们。  降维方法采用可以转换原始特征并可以对它们进行修改的工程特征。 降维方法示例包括主成分分析、典型相关分析和奇异值分解。

特征选择方法在监管上下文中的一个广泛应用类别是基于筛选器的特征选择。 通过评估每个特征和目标属性之间的相关性，这些方法应用统计测量来为每个特征分配分数。 将依据分数对这些特征排名，可以基于该排名为保留还是消除特定特征设置阈值。 这些方法中使用的统计测量示例包括皮尔逊相关、互信息和卡方检验。

Azure 机器学习工作室提供了适用于特征选择的模块。 如下图中所示，这些模块包括[基于筛选器的特征选择][filter-based-feature-selection]和[费舍尔线性判别分析][fisher-linear-discriminant-analysis]。

![特征选择示例](./media/machine-learning-feature-selection-and-engineering/feature-Selection.png)

例如，将[基于筛选器的特征选择][filter-based-feature-selection]模块与前面所述的文本挖掘示例结合使用。 假设在通过[特征哈希][feature-hashing]创建了一组 256 个特征后想要构建回归模型，并且响应变量为 **Col1**，表示范围为 1 到 5 的书籍审核评分。 将“特征评分方法”设置为“皮尔逊相关”、“目标列”设置为“Col1”以及“所需特征数”设置为“50”。 [基于筛选器的特征选择][filter-based-feature-selection]模块将生成一个包含 50 个特征以及目标属性为 **Col1** 的数据集。 下图显示了此实验的流程以及输入参数。

![特征选择示例](./media/machine-learning-feature-selection-and-engineering/feature-Selection1.png)

下图显示了生成的数据集。 基于特征本身和目标属性 **Col1** 之间的皮尔逊相关对每个特征进行评分。 将保留分数最高的特征。

![基于筛选器的特征选择数据集](./media/machine-learning-feature-selection-and-engineering/feature-Selection2.png)

下图显示了所选特征的相应分数。

![所选特征的分数](./media/machine-learning-feature-selection-and-engineering/feature-Selection3.png)

通过应用此[基于筛选器的特征选择][filter-based-feature-selection]模块，将选出 256 个特征中的 50 个，因为它们基于评分方法**皮尔逊相关**具有与目标变量 **Col1** 最相关的特征。

## <a name="conclusion"></a>结束语
特征工程和特征选择是在生成机器学习模型时，为准备定型数据通常执行的两个步骤。 通常，首先应用特征工程生成其他特征，然后执行特征选择步骤，消除不相关、冗余或高度相关的特征。

并不总是必须执行特征工程或特征选择。 是否需要执行，具体取决于拥有的或收集的数据、选择的算法以及实验的目的。

<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[feature-hashing]: https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[fisher-linear-discriminant-analysis]: https://msdn.microsoft.com/library/azure/dcaab0b2-59ca-4bec-bb66-79fd23540080/



<!--HONumber=Nov16_HO3-->


