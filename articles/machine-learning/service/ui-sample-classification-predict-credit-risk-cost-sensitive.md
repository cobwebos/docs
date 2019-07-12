---
title: 分类：预测信用风险 （成本敏感）
titleSuffix: Azure Machine Learning service
description: 本文介绍如何构建复杂的机器学习实验使用直观的界面。 您将了解如何实现自定义 Python 脚本和比较多个模型选择最佳选项。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: article
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/10/2019
ms.openlocfilehash: efed981b500ff14a66c2355a1d14bd762000622f
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/05/2019
ms.locfileid: "67606167"
---
# <a name="sample-4---classification-predict-credit-risk-cost-sensitive"></a>示例 4-分类：预测信用风险 （成本敏感）

本文介绍如何构建复杂的机器学习实验使用直观的界面。 您将了解如何使用 Python 脚本的自定义逻辑和比较多个模型选择最佳选项。

此示例训练分类器预测信用风险使用信用额度信用历史记录、 年龄、 和的信用卡号等的应用程序信息。 不过，您可以应用来解决自己的机器学习问题这篇文章中的概念。

如果你刚开始使用机器学习，可以看一看[基本分类器示例](ui-sample-classification-predict-credit-risk-basic.md)第一个。

下面是此实验的已完成关系图：

[![实验的关系图](media/ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png)](media/ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="prerequisites"></a>先决条件

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. 选择**打开**示例 4 实验的按钮：

    ![打开试验](media/ui-sample-classification-predict-credit-risk-cost-sensitive/open-sample4.png)

## <a name="data"></a>Data

我们从 UC Irvine 存储库使用德语信用卡数据集。 此数据集包含 1000 示例使用 20 个特征和 1 个标签。 每个示例表示一个人。 20 个特征包括数值和分类特征。 请参阅[UCI 网站](https://archive.ics.uci.edu/ml/datasets/Statlog+%28German+Credit+Data%29)有关数据集的详细信息。 最后一列是标签，它表示信用风险，并且有只有两个可能的值： 高信用风险 = 2 和低信用风险 = 1。

## <a name="experiment-summary"></a>实验摘要

在此实验中，我们将比较生成模型，若要解决此问题的两种不同方法：

- 使用原始数据集训练。
- 使用复制的数据集训练。

这两种方法，我们使用测试数据集进行复制，以确保结果符合成本函数评估模型。 我们测试这两种方法的两个分类器：**双类支持向量机**并**双类提升的决策树**。

为高的低风险示例错误分类成本为 1，并为低的高风险示例错误分类成本是 5。 我们使用**执行 Python 脚本**模块以解决此错误分类成本。

以下是实验的关系图：

[![实验的关系图](media/ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png)](media/ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="data-processing"></a>数据处理

我们首先使用**元数据编辑器**模块来添加列名称将默认列名称替换为更有意义的名称，获取从 UCI 网站上的数据集说明。 我们提供新的列名称作为值以逗号分隔**新列**的名称字段**元数据编辑器**。

接下来，我们生成定型和测试集用来开发风险预测模型。 我们将原始数据集拆分为大小相同的训练和测试集通过使用**拆分数据**模块。 若要创建大小相等的集，我们将设置**中第一个输出数据集的行部分**为 0.5 的选项。

### <a name="generate-the-new-dataset"></a>生成新的数据集

由于低估风险的成本较高，我们设置此类错误分类的成本：

- 高风险的情况下错误分类为低风险：5
- 低风险错误分类为高风险的情况下：第

以反映此成本函数，我们生成的新数据集。 在新数据集中，每个高风险示例会复制五次，但不会更改的低风险示例数。 我们将数据拆分为训练和测试数据集之前为防止同一行进行两个集内的复制。

若要复制的高风险数据，我们将此 Python 代码**执行 Python 脚本**模块：

```Python
import pandas as pd

def azureml_main(dataframe1 = None, dataframe2 = None):

    df_label_1 = dataframe1[dataframe1.iloc[:, 20] == 1]
    df_label_2 = dataframe1[dataframe1.iloc[:, 20] == 2]

    result = df_label_1.append([df_label_2] * 5, ignore_index=True)
    return result,
```

**执行 Python 脚本**模块将复制训练和测试数据集。

### <a name="feature-engineering"></a>特征工程

**双类支持向量机**算法要求规范化的数据。 因此，我们使用**规范化数据**模块来规范化所有数值功能使用的范围`tanh`转换。 一个`tanh`转换同时还能保留的值的总体分布将所有数值功能转换为 0 和 1 的范围内的值。

**双类支持向量机**模块处理字符串功能，将它们转换为分类特征，然后到二进制功能值为 0 或 1。 因此我们不需要对这些特征进行规范化。

## <a name="models"></a>Models

我们将应用两个分类器，因为**双类支持向量机**(SVM) 和**双类提升决策树**，并且还使用两个数据集，我们会生成总共四个模型：

- 使用原始数据训练 SVM。
- 使用训练的 SVM 复制的数据。
- 使用原始数据训练提升的决策树。
- 使用训练的提升的决策树复制的数据。

我们使用标准的试验性工作流创建、 定型和测试模型：

1. 初始化使用的学习算法**双类支持向量机**并**双类提升决策树**。
1. 使用**训练模型**将算法应用于数据并创建实际模型。
1. 使用**评分模型**以使用测试示例生成分数。

下图显示了此实验中，在该原始和已复制的定型集用于训练两个不同的 SVM 模型的一部分。 **为模型定型**连接到训练集，并**评分模型**连接到测试集。

![试验图](media/ui-sample-classification-predict-credit-risk-cost-sensitive/score-part.png)

在试验的评估阶段，我们计算每个四个模型的准确性。 对于此试验中，我们使用**评估模型**以比较具有相同的错误分类的示例成本。

**评估模型**模块可计算多达两个已评分模型的性能指标。 因此，我们使用的一个实例**评估模型**评估两个 SVM 模型和的另一个实例**评估模型**评估两个提升决策树模型。

请注意，已复制的测试数据集用作的输入**评分模型**。 换而言之，最终准确性分数包括用于获取标签错误成本。

## <a name="combine-multiple-results"></a>组合多个结果

**评估模型**模块将生成具有包含各种度量值的单个行的表。 若要创建一组准确性结果，我们首先使用**添加行**将结果合并到单个表。 然后，我们使用以下 Python 脚本**执行 Python 脚本**模块的结果表中添加的模型名称和每个行的培训方法：

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

若要查看试验的结果，可以右键单击的最后一个可视化输出**选择数据集中的列**模块。

![可视化输出](media/ui-sample-classification-predict-credit-risk-cost-sensitive/result.png)

第一列列出了机器学习算法用于生成模型。
第二列指示定型集的类型。
第三个列包含成本敏感准确度值。

根据这些结果，可以看到，通过使用创建的模型提供最高的准确性**双类支持向量机**和复制的训练数据集上训练。

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>后续步骤

了解可用于可视化界面的其他示例：

- [示例 1-回归：预测汽车的价格](ui-sample-regression-predict-automobile-price-basic.md)
- [示例 2-回归：比较进行汽车价格预测算法](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [示例 3-分类：预测信用风险](ui-sample-classification-predict-credit-risk-basic.md)
- [示例 5-分类：预测客户流失](ui-sample-classification-predict-churn.md)
- [示例 6-分类：预测航班延误](ui-sample-classification-predict-flight-delay.md)