---
title: 设计器：分类书籍评论的示例
titleSuffix: Azure Machine Learning
description: 使用 Azure 机器学习设计器生成一个多类逻辑回归分类器，以通过维基百科 SP 500 数据集预测公司类别。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: xiaoharper
ms.author: zhanxia
ms.reviewer: peterlu
ms.date: 11/04/2019
ms.openlocfilehash: 4d22fd39eae5d5cf207d6d44819f0ce7ab2eceb5
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/02/2020
ms.locfileid: "76963235"
---
# <a name="build-a-classifier-to-predict-company-category-using-azure-machine-learning-designer"></a>使用 Azure 机器学习设计器生成分类器来预测公司类别。

**设计器（预览版）示例 7**

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

本示例演示如何在 Azure 机器学习设计器（预览版）中使用文本分析模块生成文本分类管道。

文本分类的目标是将某段文本分配到一个或多个预定义的类或类别。 该文本片段可以是文档、新闻稿、搜索查询、电子邮件、推文、支持票证、客户反馈、用户产品评论等。文本分类的应用包括将报刊文章和新闻专刊内容分类成主题、将网页组织成分层类别、筛选垃圾邮件、情绪分析、从搜索查询预测用户意向、路由支持票证，以及分析客户反馈。 

此管道将训练一个**多类逻辑回归分类器**，以通过**派生自维基百科的维基百科 SP 500 数据集**预测公司类别。  

使用文本数据训练机器学习模型的基本步骤如下：

1. 获取数据

1. 预处理文本数据

1. 特征工程

   使用特征提取模块（例如特征哈希处理）将文本特征转换为数字功能，从文本数据中提取 n 元语法特征。

1. 定型模型

1. 为数据集评分

1. 评估模型

下面是我们要处理的管道在完成后的最终图形。 我们将提供所有模块的基本原理，使你可以自行做出类似的决策。

[![管道图形](./media/how-to-designer-sample-text-classification/nlp-modules-overall.png)](./media/how-to-designer-sample-text-classification/nlp-modules-overall.png#lightbox)

## <a name="data"></a>数据

在此管道中，我们将使用**维基百科 SP 500** 数据集。 该数据集派生自维基百科 (https://www.wikipedia.org/) ，基于每个标准普尔 500 强公司的商品。 在上传到 Azure 机器学习设计器之前，该数据集已按如下所述进行处理：

- 提取每个特定公司的文本内容
- 去除维基百科的格式设置
- 去除非字母数字字符
- 将所有文本都转换为小写
- 添加了已知的公司类别

可能找不到某些公司的商品，因此记录数小于 500。

## <a name="pre-process-the-text-data"></a>预处理文本数据

我们将使用“预处理文本”模块对文本数据进行预处理，包括检测句子、切分句子，等等。  可以在[**预处理文本**](algorithm-module-reference/preprocess-text.md)一文中找到所有支持的选项。 预处理文本数据后，我们将使用“拆分数据”模块来随机分割输入数据，使训练数据集包含 50% 的原始数据，使测试数据集包含 50% 的原始数据。 

## <a name="feature-engineering"></a>特征工程
在此示例中，我们将使用两种方法来执行特征工程。

### <a name="feature-hashing"></a>特征哈希
我们使用了[**特征哈希处理**](algorithm-module-reference/feature-hashing.md)模块将商品的纯文本转换为整数，并使用了整数值作为模型的输入特征。 

可以使用“特征哈希处理”模块，通过 Vowpal Wabbit 库提供的 32 位 murmurhash v3 哈希方法，将长度可变的文本文档转换为长度相等的数字特征向量。  使用特征哈希处理的目标是化简维度；此外，在分类时，特征哈希处理可以加速特征权重的查找，因为它使用哈希值比较而不是字符串比较。

在示例管道中，我们将哈希位数设置为 14，将 n 元语法数设置为 2。 使用这些设置，哈希表可以保存 2^14 个条目，其中每个哈希特征表示一个或多个 n 元语法特征，其值表示该 n 元语法在文本实例中出现的频率。 对于许多问题而言，这种大小的哈希表已完全足够，但在某些情况下，可能需要更大的空间来避免冲突。 使用不同的位数来评估机器学习解决方案的性能。 

### <a name="extract-n-gram-feature-from-text"></a>从文本中提取 N 元语法特征

n 元语法是给定文本序列中 n 个字词的连续序列。 大小为 1 的 n 元语法称为单元语法；大小为 2 的 n 元语法称为双元语法；大小为 3 的 n 元语法称为三元语法。 值较大的 n 元语法有时根据 n 值来命名，例如，“四元语法”、“五元语法”，等等。

我们使用了[**从文本中提取 N 元语法特征**](algorithm-module-reference/extract-n-gram-features-from-text.md)模块作为特征工程的另一种解决方案。 此模块先提取 n 元语法集。除了 n 元语法以外，还会统计每个 n 元语法在其文本中出现的文档数 (DF)。 在此示例中，TF-IDF 指标用于计算特征值。 然后，此模块将非结构化文本数据转换为长度相等的数字特征向量，其中的每个特征表示文本实例中某个 n 元语法的 TF-IDF。

将文本数据转换为数字特征向量后，将使用“选择列”模块从数据集中删除该文本数据。  

## <a name="train-the-model"></a>定型模型

选择哪个算法通常取决于用例的要求。 由于此管道的目标是预测公司的类别，因此多类分类器模型是适当的选择。 考虑到特征数目较大且这些特征是稀疏的，我们对此管道使用了“多类逻辑回归”模型。 

## <a name="test-evaluate-and-compare"></a>测试、评估和比较

 我们将拆分数据集并使用不同的数据集来训练和测试模型，使模型的评估更加客观。

在对模型进行训练后，我们使用“评分模型”和“评估模型”模块来生成预测结果并评估模型。   但是，在使用“评分模型”模块之前，需要执行特征工程，就像在训练过程中所做的那样。  

对于“特征哈希处理”模块，可以像采用训练流那样，采用评分流轻松执行特征工程。  使用“特征哈希处理”模块直接处理输入文本数据。 

对于“从文本中提取 N 元语法特征”模块，我们将训练数据流的“结果词汇”输出连接到评分数据流中的“输入词汇”，并将“词汇模式”参数设置为“ReadOnly”。     
[![N 元语法评分图形](./media/how-to-designer-sample-text-classification/n-gram.png)](./media/how-to-designer-sample-text-classification/n-gram.png)

完成工程步骤后，可以使用“评分模型”通过训练的模型为测试数据集生成预测。  若要检查结果，请选择“评分模型”的输出端口，并选择“可视化”。  

然后，将评分传递给“评估模型”模块以生成评估指标。  “评估模型”有两个输入端口，因此，我们可以评估和比较通过不同方法生成的评分数据集。  在本示例中，我们将比较通过特征哈希处理方法和 n 元语法方法生成的结果的性能。
若要检查结果，请选择“评估模型”的输出端口，然后选择“可视化”。  

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>后续步骤

浏览可用于设计器的其他示例：
- [示例 1 - 回归：预测汽车的价格](how-to-designer-sample-regression-automobile-price-basic.md)
- [示例 2 - 回归：比较汽车价格预测的算法](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [示例 3 - 通过特征选择进行分类：收入预测](how-to-designer-sample-classification-predict-income.md)
- [示例 4 - 分类：预测信用风险（代价敏感）](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [示例 5 - 分类：预测流失率](how-to-designer-sample-classification-churn.md)
- [示例 6 - 分类：预测航班延误](how-to-designer-sample-classification-flight-delay.md)
