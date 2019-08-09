---
title: 分类预测信用风险 (区分成本)
titleSuffix: Azure Machine Learning service
description: 本文介绍如何使用可视界面构建复杂的机器学习试验。 你将了解如何实现自定义 Python 脚本并比较多个模型, 以选择最佳选项。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/10/2019
ms.openlocfilehash: 942d6fa6db7ee2fc07fd11d3448ac7ec96c3bd43
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2019
ms.locfileid: "68845977"
---
# <a name="sample-4---classification-predict-credit-risk-cost-sensitive"></a>示例 4-分类:预测信用风险 (区分成本)

本文介绍如何使用可视界面构建复杂的机器学习试验。 你将了解如何使用 Python 脚本实现自定义逻辑, 并比较多个模型以选择最佳选项。

此示例训练一个分类器, 以使用信用历史记录、年龄和信用卡号等信用额度预测信用风险。 不过, 你可以应用本文中的概念, 以解决你自己的机器学习问题。

如果只是开始使用机器学习, 可以先查看[基本分类器示例](ui-sample-classification-predict-credit-risk-basic.md)。

下面是此试验的完成关系图:

[![试验图](media/ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png)](media/ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="prerequisites"></a>先决条件

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. 选择示例4试验的 "**打开**" 按钮:

    ![打开试验](media/ui-sample-classification-predict-credit-risk-cost-sensitive/open-sample4.png)

## <a name="data"></a>Data

我们使用 UC Irvine 存储库中的德国信用卡数据集。 此数据集包含1000个包含20个功能的样本和1个标签。 每个示例表示一个人。 20种功能包括数字和分类功能。 有关数据集的详细信息, 请参阅[UCI 网站](https://archive.ics.uci.edu/ml/datasets/Statlog+%28German+Credit+Data%29)。 最后一列是标签, 表示信用风险, 只有两个可能的值: 高信用风险 = 2 和低信用风险 = 1。

## <a name="experiment-summary"></a>试验摘要

在此实验中, 我们比较两种不同的方法来生成模型, 以解决此问题:

- 对原始数据集进行训练。
- 使用复制的数据集进行训练。

对于这两种方法, 我们通过使用带有复制的测试数据集来评估模型, 以确保结果与 cost 函数对齐。 我们测试两个具有这两种方法的分类器:**双类支持向量机**和**双类提升决策树**。

错误分类低风险示例的成本为 1, 而错误分类高风险示例的成本为5。 我们使用**执行 Python 脚本**模块来考虑此错误分类成本。

下面是实验图:

[![试验图](media/ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png)](media/ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="data-processing"></a>数据处理

首先, 我们使用**元数据编辑器**模块来添加列名称, 以将默认列名替换为更有意义的名称 (从 UCI 网站上的数据集说明获得)。 我们在 "**元数据编辑器**" 的 "**新列名**" 字段中将新列名称作为逗号分隔值提供。

接下来, 我们将生成用于开发风险预测模型的定型集和测试集。 使用**拆分数据**模块将原始数据集拆分为相同大小的定型集和测试集。 若要创建相等大小的集, 请将**第一个输出数据集选项中的行部分**设置为0.5。

### <a name="generate-the-new-dataset"></a>生成新的数据集

由于低估风险的代价很高, 因此, 我们设置错误分类的成本, 如下所示:

- 对于高风险的情况, 分类不当为低风险:5
- 对于低风险的情况, 分类不当为高风险:1

为了反映此成本函数, 我们生成了一个新的数据集。 在新数据集中, 每个高风险示例复制了五次, 但低风险示例的数目不会改变。 我们会在复制前将数据拆分为定型和测试数据集, 以防止在两个集中都有相同的行。

为了复制高风险数据, 我们将此 Python 代码放入**执行 Python 脚本**模块中:

```Python
import pandas as pd

def azureml_main(dataframe1 = None, dataframe2 = None):

    df_label_1 = dataframe1[dataframe1.iloc[:, 20] == 1]
    df_label_2 = dataframe1[dataframe1.iloc[:, 20] == 2]

    result = df_label_1.append([df_label_2] * 5, ignore_index=True)
    return result,
```

**执行 Python 脚本**模块会同时复制定型和测试数据集。

### <a name="feature-engineering"></a>特征工程

**双类支持向量机**算法需要规范化的数据。 我们使用**规范化数据**模块将所有数值特征的范围与`tanh`转换进行标准化。 `tanh`转换将所有数字功能转换为0到1范围内的值, 同时保留值的总体分布。

**双类支持向量机**模块处理字符串功能, 将它们转换为分类特征, 然后转换为具有值0或1的二进制特征。 因此, 我们不需要将这些功能标准化。

## <a name="models"></a>Models

由于我们应用了两个分类器、**双类支持向量机**(SVM) 和**双类提升决策树**, 并且还使用了两个数据集, 因此我们总共生成四个模型:

- SVM 对原始数据进行定型。
- SVM 培训了复制的数据。
- 用原始数据训练的提升决策树。
- 针对复制的数据训练的提升决策树。

我们使用标准试验工作流来创建、定型和测试模型:

1. 使用**双类支持向量机**和**双类提升决策树**初始化学习算法。
1. 使用**训练模型**将算法应用于数据并创建实际模型。
1. 使用**评分模型**, 通过使用测试示例生成分数。

下图显示了此试验的一部分, 其中的原始和已复制定型集用于训练两种不同的 SVM 模型。 **训练模型**连接到定型集,**评分模型**连接到测试集。

![试验图](media/ui-sample-classification-predict-credit-risk-cost-sensitive/score-part.png)

在试验的评估阶段, 我们计算四个模型中每个模型的准确性。 对于此实验, 我们使用 "**评估模型**" 来比较具有相同错误分类成本的示例。

"**评估模型**" 模块可以计算多达两个评分模型的性能指标。 因此, 我们使用一个 "**评估模型**" 实例来计算两个 SVM 模型, 并使用另一个 "**评估模型**" 实例来计算两个提升决策树模型。

请注意, 复制的测试数据集用作**评分模型**的输入。 换句话说, 最终的准确性分数包括使标签出现错误的成本。

## <a name="combine-multiple-results"></a>合并多个结果

"**评估模型**" 模块生成一个表, 该表包含包含各种指标的单个行。 若要创建一组准确性结果, 我们首先使用 "**添加行**" 将结果合并到单个表中。 然后, 在 "**执行 Python 脚本**" 模块中使用以下 Python 脚本, 为结果表中的每一行添加模型名称和定型方法:

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

若要查看试验结果, 您可以右键单击 "数据集" 模块中最后一个 "**选择列**" 的 "可视化输出"。

![可视化输出](media/ui-sample-classification-predict-credit-risk-cost-sensitive/result.png)

第一列列出用于生成模型的机器学习算法。
第二列指示定型集的类型。
第三列包含与成本相关的精度值。

根据这些结果, 您可以看到, 通过**双类支持向量机**创建并在复制的定型数据集上训练的模型提供了最佳准确性。

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>后续步骤

浏览可用于可视界面的其他示例:

- [示例 1-回归:预测汽车的价格](ui-sample-regression-predict-automobile-price-basic.md)
- [示例 2-回归:比较汽车价格预测的算法](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [示例 3-分类:预测信用风险](ui-sample-classification-predict-credit-risk-basic.md)
- [示例 5-分类:预测改动](ui-sample-classification-predict-churn.md)
- [示例 6-分类:预测航班延迟](ui-sample-classification-predict-flight-delay.md)