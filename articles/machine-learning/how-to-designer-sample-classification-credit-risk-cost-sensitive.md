---
title: 设计器：预测信用风险示例
titleSuffix: Azure Machine Learning
description: 生成分类器并使用自定义 Python 脚本，使用 Azure 机器学习设计器预测信用风险。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.date: 12/25/2019
ms.openlocfilehash: 1430db34f9c31cbd9d9df921650c628d265bccc5
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2020
ms.locfileid: "76311081"
---
# <a name="build-a-classifier--use-python-scripts-to-predict-credit-risk-using-azure-machine-learning-designer"></a>使用 Azure 机器学习设计器生成分类器 & 使用 Python 脚本预测信用风险

**设计器示例4**

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

本文介绍如何使用设计器构建复杂的机器学习管道。 你将了解如何使用 Python 脚本实现自定义逻辑，并比较多个模型以选择最佳选项。

此示例训练一个分类器，以使用信用历史记录、年龄和信用卡号等信用额度预测信用风险。 不过，你可以应用本文中的概念，以解决你自己的机器学习问题。

下面是此管道的完成关系图：

[管道 ![图](./media/how-to-designer-sample-classification-credit-risk-cost-sensitive/graph.png)](./media/how-to-designer-sample-classification-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="prerequisites"></a>必备组件

[!INCLUDE [aml-ui-prereq](../../includes/aml-ui-prereq.md)]

4. 单击 "示例 4" 将其打开。

## <a name="data"></a>数据

此示例使用 UC Irvine 存储库中的德国信用卡数据集。 它包含1000个包含20个功能的样本和一个标签。 每个示例表示一个人。 20种功能包括数字和分类功能。 有关数据集的详细信息，请参阅[UCI 网站](https://archive.ics.uci.edu/ml/datasets/Statlog+%28German+Credit+Data%29)。 最后一列是标签，表示信用风险，只有两个可能的值：高信用风险 = 2 和低信用风险 = 1。

## <a name="pipeline-summary"></a>管道摘要

在此管道中，你比较两种不同的方法来生成模型，以解决此问题：

- 对原始数据集进行训练。
- 使用复制的数据集进行训练。

对于这两种方法，通过使用带有复制的测试数据集来评估模型，以确保结果与 cost 函数对齐。 用这两种方法测试两个分类器：**双类支持向量机**和**双类提升决策树**。

错误分类低风险示例的成本为1，而错误分类高风险示例的成本为5。 我们使用**执行 Python 脚本**模块来考虑此错误分类成本。

下面是管道的关系图：

[管道 ![图](./media/how-to-designer-sample-classification-credit-risk-cost-sensitive/graph.png)](./media/how-to-designer-sample-classification-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="data-processing"></a>数据处理

首先，使用**元数据编辑器**模块添加列名称，以使用更有意义的名称替换默认列名，该名称从 UCI 网站上的数据集说明获得。 在 "**元数据编辑器**" 的 "**新列名**" 字段中提供以逗号分隔的值的新列名称。

接下来，生成用于开发风险预测模型的定型集和测试集。 使用**拆分数据**模块将原始数据集拆分为相同大小的定型集和测试集。 若要创建相等大小的集，请将**第一个输出数据集选项中的行部分**设置为0.7。

### <a name="generate-the-new-dataset"></a>生成新的数据集

由于低估风险的代价很高，因此，请设置错误分类的成本，如下所示：

- 对于高风险的情况，分类不当为低风险：5
- 对于低风险情况，分类不当为高风险：1

若要反映此成本函数，请生成新的数据集。 在新数据集中，每个高风险示例复制了五次，但低风险示例的数目不会改变。 在复制前将数据拆分为定型和测试数据集，以防止在两个集中都有相同的行。

若要复制高风险数据，请将此 Python 代码置于**执行 Python 脚本**模块中：

```Python
import pandas as pd

def azureml_main(dataframe1 = None, dataframe2 = None):

    df_label_1 = dataframe1[dataframe1.iloc[:, 20] == 1]
    df_label_2 = dataframe1[dataframe1.iloc[:, 20] == 2]

    result = df_label_1.append([df_label_2] * 5, ignore_index=True)
    return result,
```

**执行 Python 脚本**模块会同时复制定型和测试数据集。

### <a name="feature-engineering"></a>特性工程

**双类支持向量机**算法需要规范化的数据。 因此，请使用**规范化数据**模块将所有数值特征的范围标准化 `tanh` 转换。 `tanh` 转换将所有数字功能转换为0到1范围内的值，同时保留值的总体分布。

**双类支持向量机**模块处理字符串功能，将它们转换为分类特征，然后转换为值为零或一的二元特征。 因此，您不需要对这些功能进行规范化。

## <a name="models"></a>模型

由于您应用了两个分类器、**双类支持向量机**（SVM）和**双类提升决策树**，以及两个数据集，因此总共生成四个模型：

- SVM 对原始数据进行定型。
- SVM 培训了复制的数据。
- 用原始数据训练的提升决策树。
- 针对复制的数据训练的提升决策树。

此示例使用标准数据科学工作流来创建、定型和测试模型：

1. 使用**双类支持向量机**和**双类提升决策树**初始化学习算法。
1. 使用**训练模型**将算法应用于数据并创建实际模型。
1. 使用**评分模型**，通过使用测试示例生成分数。

下图显示了此管道的一部分，在此管道中，原始和复制的定型集用于训练两种不同的 SVM 模型。 **训练模型**连接到定型集，**评分模型**连接到测试集。

![管道图形](./media/how-to-designer-sample-classification-credit-risk-cost-sensitive/score-part.png)

在管道的评估阶段，你计算四个模型中每个模型的准确性。 对于此管道，使用 "**评估模型**" 来比较具有相同错误分类成本的示例。

"**评估模型**" 模块可以计算多达两个评分模型的性能指标。 因此，您可以使用一个 "**评估模型**" 实例来计算两个 SVM 模型，并使用另一个 "**评估模型**" 实例来计算两个提升决策树模型。

请注意，复制的测试数据集用作**评分模型**的输入。 换句话说，最终的准确性分数包括使标签出现错误的成本。

## <a name="combine-multiple-results"></a>合并多个结果

"**评估模型**" 模块生成一个表，该表包含包含各种指标的单个行。 若要创建一组准确性结果，我们首先使用 "**添加行**" 将结果合并到单个表中。 然后，在 "**执行 Python 脚本**" 模块中使用以下 Python 脚本，为结果表中的每一行添加模型名称和定型方法：

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

若要查看管道的结果，可以右键单击 "数据集" 模块中最后一个 "**选择列**" 的 "可视化输出"。

![可视化输出](media/how-to-designer-sample-classification-credit-risk-cost-sensitive/sample4-lastselect-1225.png)

第一列列出用于生成模型的机器学习算法。

第二列指示定型集的类型。

第三列包含与成本相关的精度值。

根据这些结果，您可以看到，通过**双类支持向量机**创建并在复制的定型数据集上训练的模型提供了最佳准确性。

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>后续步骤

浏览可用于设计器的其他示例：

- [示例 1-回归：预测汽车的价格](how-to-designer-sample-regression-automobile-price-basic.md)
- [示例 2-回归：比较汽车价格预测的算法](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [示例 3-通过功能选择进行分类：收入预测](how-to-designer-sample-classification-predict-income.md)
- [示例 5-分类：预测改动](how-to-designer-sample-classification-churn.md)
- [示例 6-分类：预测航班延迟](how-to-designer-sample-classification-flight-delay.md)
- [示例 7-文本分类：维基百科 SP 500 数据集](how-to-designer-sample-text-classification.md)
