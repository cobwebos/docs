---
title: 设计器：预测信用风险的示例
titleSuffix: Azure Machine Learning
description: 使用 Azure 机器学习设计器生成分类器，并通过自定义 Python 脚本预测信用风险。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.date: 12/25/2019
ms.openlocfilehash: ed8ee9b1c711ee0056377154379b8df56e0785df
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/02/2020
ms.locfileid: "76964594"
---
# <a name="build-a-classifier--use-python-scripts-to-predict-credit-risk-using-azure-machine-learning-designer"></a>使用 Azure 机器学习设计器生成分类器并通过 Python 脚本预测信用风险

**设计器（预览版）示例 4**

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

本文介绍如何使用设计器（预览版）生成复杂的机器学习管道。 其中介绍了如何使用 Python 脚本实现自定义逻辑，并比较多个模型以选择最佳选项。

本示例将训练一个分类器，以使用信用历史记录、年龄和信用卡数目等信贷申请信息来预测信用风险。 但是，你也可以运用本文中的概念来解决自己的机器学习问题。

下面是此管道的已完成图形：

[![管道图形](./media/how-to-designer-sample-classification-credit-risk-cost-sensitive/graph.png)](./media/how-to-designer-sample-classification-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="prerequisites"></a>必备条件

[!INCLUDE [aml-ui-prereq](../../includes/aml-ui-prereq.md)]

4. 单击示例 4 将其打开。

## <a name="data"></a>数据

本示例使用 UC Irvine 存储库中的“德国信用卡”数据集。 其中包含 1,000 个样本，这些样本带有 20 个特征和 1 个标签。 每个样本代表一人。 20 个特征包括数字和分类特征。 有关该数据集的详细信息，请参阅 [UCI 网站](https://archive.ics.uci.edu/ml/datasets/Statlog+%28German+Credit+Data%29)。 最后一列是标签，它表示信用风险，只有两个可能的值：高信用风险 = 2，低信用风险 = 1。

## <a name="pipeline-summary"></a>管道摘要

在此管道中，你将比较两种不同的生成模型来解决此问题的方法：

- 使用原始数据集进行训练。
- 使用复制的数据集进行训练。

将通过这两种方法使用支持复制的测试数据集来评估模型，以确保结果与代价函数相符。 使用两种方法测试两个分类器：“双类支持向量机”和“双类提升决策树”。  

错误地将低风险示例分类为高的代价为 1，错误地将高风险示例分类为低的代价是 5。 我们使用“执行 Python 脚本”模块来分析此错误分类代价。 

下面是管道图形：

[![管道图形](./media/how-to-designer-sample-classification-credit-risk-cost-sensitive/graph.png)](./media/how-to-designer-sample-classification-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="data-processing"></a>数据处理

首先，使用“元数据编辑器”模块来添加列名，以使用更有意义的名称（从 UCI 网站上的数据集说明中获取）来替换默认列名。  在“元数据编辑器”的“新建列”名称字段中，以逗号分隔值的形式提供新列名。  

接下来，生成用于开发风险预测模型的训练集和测试集。 使用“拆分数据”模块将原始数据集拆分为相同大小的训练集和测试集。  若要创建相同大小的集，请将“第一个输出数据集中的行小数”选项设置为 0.7。 

### <a name="generate-the-new-dataset"></a>生成新数据集

由于低估风险的代价较高，因此，请按如下所示设置错误分类的代价：

- 对于错误分类为低风险的高风险案例：5
- 对于错误分类为高风险的低风险案例：1

若要反映此代价函数，请生成新数据集。 在新数据集中，每个高风险示例将复制五次，但低风险示例的数目不会更改。 在复制之前，请将数据拆分为训练和测试数据集，以防止两个集中出现相同的行。

若要复制高风险数据，请将此 Python 代码输入“执行 Python 脚本”模块： 

```Python
import pandas as pd

def azureml_main(dataframe1 = None, dataframe2 = None):

    df_label_1 = dataframe1[dataframe1.iloc[:, 20] == 1]
    df_label_2 = dataframe1[dataframe1.iloc[:, 20] == 2]

    result = df_label_1.append([df_label_2] * 5, ignore_index=True)
    return result,
```

“执行 Python 脚本”模块同时复制训练和测试数据集。 

### <a name="feature-engineering"></a>特性工程

“双类支持向量机”算法需要规范化的数据。  因此，请使用“规范化数据”模块通过 `tanh` 转换来规范化所有数字特征的范围。  `tanh` 转换将所有数字特征转换为 0 到 1 范围内的值，同时保留值的总体分布。

“双类支持向量机”模块处理字符串特征，依次将其转换为分类特征，以及值为 0 或 1 的二元特征。  因此，无需规范化这些特征。

## <a name="models"></a>Models

模型由于应用了两个分类器（“两类支持向量机”(SVM) 和“双类提升决策树”）和两个数据集，因此总共会生成四个模型：  

- 使用原始数据训练的 SVM。
- 使用复制的数据训练的 SVM。
- 使用原始数据训练的提升决策树。
- 使用复制的数据训练的提升决策树。

本示例使用标准数据科学工作流来创建、训练和测试模型：

1. 使用“双类支持向量机”和“双类提升决策树”初始化学习算法。  
1. 使用“训练模型”将算法应用于数据并创建实际模型。 
1. 使用“评分模型”通过测试示例生成评分。 

下图显示了此管道的一部分，其中使用了原始训练集和复制的训练集来训练两个不同的 SVM 模型。 “训练模型”连接到训练集，“评分模型”连接到测试集。  

![管道图形](./media/how-to-designer-sample-classification-credit-risk-cost-sensitive/score-part.png)

在管道的评估阶段，将计算每个模型（共四个）的准确度。 对于此管道，请使用“评估模型”来比较错误分类代价相同的示例。 

“评估模型”模块最多可以计算两个评分模型的性能指标。  因此，可以使用“评估模型”的一个实例来计算两个 SVM 模型，并使用“评估模型”的另一个实例来评估两个提升决策树模型。  

请注意，复制的测试数据集用作“评分模型”的输入。  换言之，最终的准确度评分包括用错标签的代价。

## <a name="combine-multiple-results"></a>合并多个结果

“评估模型”模块将生成一个表，其中有一行包含各项指标。  为了创建一组准确度结果，我们先使用“添加行”将结果合并到单个表中。  然后，在“执行 Python 脚本”模块中使用以下 Python 脚本为结果表中的每一行添加模型名称和训练方法： 

```Python
import pandas as pd

def azureml_main(dataframe1 = None, dataframe2 = None):

    new_cols = pd.DataFrame(
            columns=["Algorithm","Training"],
            data=[
                ["SVM", "weighted"],
                ["SVM", "unweighted"],
                ["Boosted Decision Tree","weighted"],
                ["Boosted Decision Tree","unweighted"]
            ])

    result = pd.concat([new_cols, dataframe1], axis=1)
    return result,
```

## <a name="results"></a>结果

若要查看管道的结果，可以右键单击最后一个“选择数据集中的列”模块的“可视化输出”。 

![可视化输出](media/how-to-designer-sample-classification-credit-risk-cost-sensitive/sample4-lastselect-1225.png)

第一列列出用于生成模型的机器学习算法。

第二列指示训练集的类型。

第三列包含代价敏感型准确度值。

从这些结果中可以看到，使用“双类支持向量机”创建的，并基于复制的训练数据集训练的模型所提供的准确度最高。 

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>后续步骤

浏览可用于设计器的其他示例：

- [示例 1 - 回归：预测汽车的价格](how-to-designer-sample-regression-automobile-price-basic.md)
- [示例 2 - 回归：比较汽车价格预测的算法](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [示例 3 - 通过特征选择进行分类：收入预测](how-to-designer-sample-classification-predict-income.md)
- [示例 5 - 分类：预测流失率](how-to-designer-sample-classification-churn.md)
- [示例 6 - 分类：预测航班延误](how-to-designer-sample-classification-flight-delay.md)
- [示例 7 - 文本分类：维基百科 SP 500 数据集](how-to-designer-sample-text-classification.md)
