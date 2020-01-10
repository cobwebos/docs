---
title: 设计器：对书籍评审示例进行分类
titleSuffix: Azure Machine Learning
description: 使用 Azure 机器学习设计器生成一个多类逻辑回归分类器，以预测带有维基百科 SP 500 数据集的公司类别。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.reviewer: peterlu
ms.date: 11/04/2019
ms.openlocfilehash: 06a88ba1e477054aa6080baa9c9644d4da396442
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75771421"
---
# <a name="build-a-classifier-to-predict-company-category-using-azure-machine-learning-designer"></a>使用 Azure 机器学习设计器生成分类器以预测公司类别。

**设计器（预览）示例7**

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

此示例演示如何使用文本分析模块在 Azure 机器学习设计器（预览版）中构建文本分类管道。

文本分类的目标是将一些文本分配给一个或多个预定义的类或类别。 文本片段可以是文档、新闻文章、搜索查询、电子邮件、推文、支持票证、客户反馈、用户产品评论等。文本分类应用程序包括将报纸文章和资讯内容分类到主题，将网页组织成层次结构类别，筛选垃圾邮件，情绪分析，根据搜索查询预测用户意向，路由支持票证和分析客户反馈。 

此管道培训**多类逻辑回归分类器**，以预测**从维基百科派生的维基百科 SP 500 数据集**的公司类别。  

训练机器学习模型中包含文本数据的基本步骤如下：

1. 获取数据

1. 预处理文本数据

1. 特征工程

   通过功能提取模块（例如功能哈希）将文本功能转换为数值功能，从文本数据中提取 n 元语法功能。

1. 定型模型

1. 评分数据集

1. 评估模型

下面是我们将处理的管道的最终完成关系图。 我们将提供所有模块的基本原理，以便您可以自行做出类似的决策。

[管道 ![图](./media/how-to-designer-sample-text-classification/nlp-modules-overall.png)](./media/how-to-designer-sample-text-classification/nlp-modules-overall.png#lightbox)

## <a name="data"></a>数据

在此管道中，我们使用**维基百科 SP 500**数据集。 数据集派生自维基百科（ https://www.wikipedia.org/) 基于每个 & P 500 公司的文章。 上载到 Azure 机器学习设计器之前，数据集的处理方式如下：

- 提取每个特定公司的文本内容
- 去除维基百科的格式设置
- 去除非字母数字字符
- 将所有文本都转换为小写
- 添加了已知的公司类别

对于某些公司，找不到项目，因此记录数小于500。

## <a name="pre-process-the-text-data"></a>预处理文本数据

我们使用 "**预处理文本**" 模块对文本数据进行预处理，包括检测句子、标记的句子等。 您可以在[**预处理文本**](algorithm-module-reference/preprocess-text.md)一文中找到所有支持的选项。 预处理 tex 数据后，我们使用**拆分数据**模块随机划分输入数据，以便定型数据集包含50% 的原始数据，并且测试数据集包含原始数据的50%。

## <a name="feature-engineering"></a>特征工程
在此示例中，我们将使用两个执行特征工程的方法。

### <a name="feature-hashing"></a>特征哈希
我们使用了 "[**特征哈希**](algorithm-module-reference/feature-hashing.md)" 模块将文章的纯文本转换为整数，并使用整数值作为模型的输入功能。 

**功能哈希**模块可用于将可变长度的文本文档转换为相等长度的数字特征向量，使用 Vowpal Wabbit 库提供的32位 murmurhash v3 哈希方法。 使用特征哈希的目标是维度缩减;此外，特征哈希使得功能权重的查找在分类时间更快，因为它使用哈希值比较而不是字符串比较。

在示例管道中，我们将哈希位数设置为14，并将 n 元语法数设置为2。 在这些设置中，哈希表可以保存 2 ^ 14 个条目，其中每个哈希特征表示一个或多个 n 元语法特征，其值表示文本实例中 n 语法的出现频率。 对于许多问题，此大小的哈希表已足够，但在某些情况下，可能需要更多的空间来避免冲突。 使用不同位数评估机器学习解决方案的性能。 

### <a name="extract-n-gram-feature-from-text"></a>从文本提取 N 元语法特征

N 语法是给定文本序列中的 n 个字词的连续序列。 大小为1的 n 语法称为 unigram;大小为2的 n 语法为 bigram;大小为3的 n 语法为三线形。 N-如果值较大，则值通常由 n 的值引用，例如 "四语法"、"5 语法" 等。

我们使用了 "[**从文本中提取 N 元语法" 功能**](algorithm-module-reference/extract-n-gram-features-from-text.md)作为功能设计的另一个解决方案。 此模块首先提取 n 语法集，此外还会提取 n 语法，其中每个 n 元语法在文本中出现的文档数（DF）。 在此示例中，使用了 TF-IDF 指标来计算特征值。 然后，它将非结构化文本数据转换为等长数值特征向量，其中每个特征表示文本实例中 n 语法的 TF-IDF。

将文本数据转换为数字特征向量后，使用 "**选择列**" 模块从数据集中删除文本数据。 

## <a name="train-the-model"></a>定型模型

选择的算法通常取决于用例的要求。 由于此管道的目标是预测公司的类别，因此，多类分类器模型是一个不错的选择。 考虑到功能很大，这些功能是稀疏的，我们将**多类逻辑回归**模型用于此管道。

## <a name="test-evaluate-and-compare"></a>测试、评估和比较

 我们拆分数据集并使用不同的数据集来定型和测试模型，以使模型的评估更具目标。

在对模型进行定型后，我们将使用**评分模型**并**评估模型**模块以生成预测结果并对模型进行评估。 但是，在使用 "**评分模型**" 模块之前，需要执行培训过程中所做的工作。 

对于**特征哈希**模块，可以轻松地在评分流上执行功能工程师。 直接使用**特征哈希**模块来处理输入文本数据。

对于 "**从文本中提取 N 元语法特征**" 模块，我们会将定型数据流的**结果词汇输出**连接到计分数据流上的**输入词汇**，并将**词汇模式**参数设置为**ReadOnly**。
[n 语法分数 ![图形](./media/how-to-designer-sample-text-classification/n-gram.png)](./media/how-to-designer-sample-text-classification/n-gram.png)

完成工程步骤后，可以使用**评分模型**通过训练的模型生成测试数据集的预测。 若要检查结果，请选择 "**评分模型**" 的输出端口，然后选择 "**可视化**"。

然后，将评分传递到 "**评估模型**" 模块以生成评估指标。 **评估模型**有两个输入端口，因此，我们可以评估和比较用不同方法生成的评分的数据集。 在此示例中，我们比较了用功能哈希方法和 n 语法方法生成的结果的性能。
若要检查结果，请选择 "**评估模型**" 的输出端口，然后选择 "**可视化**"。

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>后续步骤

浏览可用于设计器的其他示例：
- [示例 1-回归：预测汽车的价格](how-to-designer-sample-regression-automobile-price-basic.md)
- [示例 2-回归：比较汽车价格预测的算法](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [示例 3-通过功能选择进行分类：收入预测](how-to-designer-sample-classification-predict-income.md)
- [示例 4-分类：预测信用风险（区分成本）](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [示例 5-分类：预测改动](how-to-designer-sample-classification-churn.md)
- [示例 6-分类：预测航班延迟](how-to-designer-sample-classification-flight-delay.md)
