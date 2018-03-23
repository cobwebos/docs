---
title: 在 Azure 机器学习工作室中创建文本分析模型 | Microsoft Docs
description: 如何使用文本预处理、N 元语法或特征哈希模块在 Azure 机器学习工作室中创建文本分析模型
services: machine-learning
documentationcenter: ''
author: rastala
manager: jhubbard
editor: ''
ms.assetid: 08cd6723-3ae6-4e99-a924-e650942e461b
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2018
ms.author: roastala
ms.openlocfilehash: 0c2dc3bad6973b7b00f0ed44231e78298f4422fb
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/16/2018
---
# <a name="create-text-analytics-models-in-azure-machine-learning-studio"></a>在 Azure 机器学习工作室中创建文本分析模型
可以使用 Azure 机器学习构建和实现文本分析模型的操作化。 例如，这些模型可以帮助你解决文档分类或情绪分析问题。

在文本分析实验中，通常需要：

1. 清除并预处理的文本数据集
2. 从预处理文本中提取数值特征向量
3. 定型分类或回归模型
4. 评分和验证模型
5. 将模型部署到生产环境

在本教程中，当我们使用“Amazon 书评数据集”逐步解说情绪分析模型时，可以学到这些步骤（请参阅研究报告 ”Biographies, Bollywood, Boom-boxes and Blenders: Domain Adaptation for Sentiment Classification”，作者：Association of Computational Linguistics (ACL) 的 John Blitzer、Mark Dredze 和 Fernando Pereira，2007 年）。此数据集是由评论分数（1-2 或 4-5）和自由格式文字组成。 目标是预测评论分数：低 (1-2) 或高 (4-5)。

可以在 Azure AI 库中找到本教程中涵盖的实验：

[预测书评](https://gallery.cortanaintelligence.com/Experiment/Predict-Book-Reviews-1)

[预测书评 - 预测实验](https://gallery.cortanaintelligence.com/Experiment/Predict-Book-Reviews-Predictive-Experiment-1)

## <a name="step-1-clean-and-preprocess-text-dataset"></a>步骤 1：清除并预处理的文本数据集
首先，我们将评论分数划分为低和高两类，这样可将问题公式化为双类别分类。 我们使用[编辑元数据](https://msdn.microsoft.com/library/azure/dn905986.aspx)和[分组分类值](https://msdn.microsoft.com/library/azure/dn906014.aspx)模块。

![创建标签](./media/text-analytics-module-tutorial/create-label.png)

然后，我们使用[预处理文本](https://msdn.microsoft.com/library/azure/mt762915.aspx)模块清理文本。 清理操作可减少数据集中的干扰信息，帮助你找到最重要的功能，并提高最终模型的准确性。 我们会删除非索引字，如“the”或“a”等常见字词，以及数字、特殊字符、重复的字符、电子邮件地址和 URL。 此外，我们还将文本转换为小写、按词形进行归并并检测句子边界，并在预处理文本中用“|||”符号表示。

![预处理文本](./media/text-analytics-module-tutorial/preprocess-text.png)

如果要使用非索引字的自定义列表怎样操作？ 可以将其作为可选输入传递。 还可以使用自定义 C# 语法正则表达式来替换子字符串，并按词性（名词、动词或形容词）删除字词。

预处理完毕后，我们将数据拆分为定型和测试集。

## <a name="step-2-extract-numeric-feature-vectors-from-pre-processed-text"></a>步骤2：从预处理文本中提取数值特征向量
要生成文本数据模型，通常需要将自由格式文本转换为数字特征向量。 在此示例中，我们使用[从文本提取 N 元语法功能](https://msdn.microsoft.com/library/azure/mt762916.aspx)模块，将文本数据转换为此类格式。 此模块使用一列以空格分隔的字词，并计算出现在数据集中的字典的字词，或 N 元语法的字词。 然后，它将计算每条记录中每个字词或 N 元语法出现的次数，并从这些计数创建特征向量。 在本教程中，我们将 N 元语法大小设置为 2，这样我们的特征向量可包含单个字词和两个后续字词的组合。

![提取 N 元语法](./media/text-analytics-module-tutorial/extract-ngrams.png)

我们会将 TF*IDF（词频逆文档频率）权重应用于 N 元语法计数。 此方法将添加单个记录中频繁出现的字词的权重，但极少针对整个数据集执行此操作。 其他选项包括二进制、TF 和图形权重。

此类文本功能通常具有较高的维度。 例如，假设语料库具有 100,000 个唯一字词，如果使用 N 元语法，则特征空间具有 100,000 维度或更多。 “提取 N 元语法特征”模块提供了一组用于减少维度的选项。 可以选择排除过短或过长的字词，或不太常见或太频繁而具有重要预测值的字词。 在本教程中，我们会排除出现在少于 5 个记录或超过 80% 的记录的 N 元语法。

此外，可使用特征选择来仅选择与预测目标最相关的特征。 我们使用卡方统计特征选择来选择 1,000 个特征。 单击“提取 N 元语法”模块右侧的输出，可查看所选字词或 N 元语法的词汇。

另一种方法是使用“提取 N 元语法特征”，就可以使用“特征哈希”模块。 但请注意，该[特征哈希](https://msdn.microsoft.com/library/azure/dn906018.aspx)不具有内置特征选择功能，或 TF*IDF 权重。

## <a name="step-3-train-classification-or-regression-model"></a>步骤 3：定型分类或回归模型
文本现已转换为数值特征列。 数据集仍包含上一阶段中的字符串列，因此我们使用“选择数据集中的列中”来将其排除。

然后，我们使用[二元逻辑回归](https://msdn.microsoft.com/library/azure/dn905994.aspx)来预测我们的目标：高或低评论分数。 在这种情况下，文本分析问题已转换为常规分类问题。 可以使用 Azure 机器学习中提供的工具来改进模型。 例如，可以实验不同的分类器，以了解如何它们给出结果的准确度，或使用超参数调整改进准确度。

![定型和评分](./media/text-analytics-module-tutorial/scoring-text.png)

## <a name="step-4-score-and-validate-the-model"></a>步骤 4：评分和验证模型
如何验证定型模型？ 我们对照测试数据集来评分并评估准确度。 但是，该模型已从定型数据集学习了 N 元语法及其权重的词汇。 因此，从测试数据提取特征时，我们应使用该词汇和这些权重，而不是重新创建词汇。 因此，我们将“提取 N 元语法特征”模块添加到此实验的评分分支、从定型分支连接输出词汇，并将词汇模式设置为只读。 我们还通过将最小值设置为 1 个实例，最大值设为 100% 来禁用依频率筛选 N 元语法，并关闭特征选择。

测试数据中的文本列转换为数值特征列后，我们会排除上一阶段（例如在定型分支中）中字符串列。 然后，使用“评分模型”模块来进行预测，使用“评估模型”模块来评估准确性。

## <a name="step-5-deploy-the-model-to-production"></a>步骤 5：将模型部署到生产环境
该模型几乎可立即部署到生产环境。 部署为 Web 服务时，它采用自由格式文本字符串作为输入，并返回“高”或“低”的预测。 它会使用学习到的 N 元语法词汇将文本转换为特征，并利用定型逻辑回归模型从这些特征进行预测。 

要设置预测实验，我们先将 N 元语法词汇保存为数据集，并使用来自实验定型分支的定型逻辑回归模型。 然后，我们使用“另存为”来保存试验，以创建预测试验的试验图。 我们从实验中删除“拆分数据”模块和定型分支。 然后，我们将上一阶段保存的 N 元语法词汇和模型分别连接到“提取 N 元语法特征”和“评分模型”模块。 此外，我们还删除“评估模型”模块。

我们将“选择数据集中的列”模块插入到“预处理文本”模块前面，以删除标签列，并取消选择“评分模块”中的“将评分列附加到数据集”选项。 这样一来，Web 服务不会请求正在尝试进行预测的标签，也不对响应中的输入特征作出回应。

![预测性实验](./media/text-analytics-module-tutorial/predictive-text.png)

现在我们有一个可以发布为 Web 服务的实验，并使用请求-响应或批处理执行 API 调用。

## <a name="next-steps"></a>后续步骤
从 [MSDN 文档](https://msdn.microsoft.com/library/azure/dn905886.aspx)了解文本分析模块。

