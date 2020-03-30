---
title: 基于 PCA 的异常检测：模块参考
titleSuffix: Azure Machine Learning
description: 了解如何使用基于 PCA 的异常检测模块创建基于主组件分析 （PCA） 的异常检测模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 0672b9769feae65c73a6f752a268968a7bad9e4b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502979"
---
# <a name="pca-based-anomaly-detection"></a>基于 PCA 的异常情况检测

本文介绍如何在 Azure 机器学习设计器（预览）中使用**基于 PCA 的异常检测**模块，以创建基于主组件分析 （PCA） 的异常检测模型。

此模块可帮助您在易于从一个类（如有效事务）获取训练数据但难以获取目标异常的足够样本的情况下构建模型。 

例如，要检测欺诈易，您通常没有足够的欺诈示例来培训，但有许多良好交易示例。 **基于 PCA 的异常检测**模块通过分析可用功能来确定什么构成"正常"类，并应用距离指标来识别表示异常的案例，从而解决了问题。 这允许您使用现有的不平衡数据训练模型。

## <a name="more-about-principal-component-analysis"></a>详细了解主成分分析

*主要组件分析*（通常缩写为PCA）是机器学习中的一种成熟技术。 PCA 常用于探索性数据分析，因为它可以显示数据的内部结构并解释数据差异。

PCA 的工作方式是通过分析包含多个变量的数据。 它查找变量之间的关联性，并确定最能捕捉结果差异的值的组合。 这些组合的特征值用于创建称为*主组件*的更紧凑的要素空间。

对于异常检测，对每个新输入进行分析，异常检测算法计算其对eigenvector的投影，以及一个规范化的重建误差。 规范化错误用作异常分数。 错误级别越高，实例越异常。

有关 PCA 的工作原理以及异常检测实现的详细信息，请参阅以下论文：

- [主体分量分析的随机算法](https://arxiv.org/abs/0809.2274)。 罗克林、斯兰和泰格特

- [查找具有随机性的结构：用于构造近似矩阵分解](http://users.cms.caltech.edu/~jtropp/papers/HMT11-Finding-Structure-SIREV.pdf)（PDF 下载）的概率算法。 哈尔科、马丁松和特罗普

## <a name="how-to-configure-pca-anomaly-detection"></a>如何配置 PCA 异常检测

1. 将**基于 PCA 的异常检测**模块添加到设计器中的管道中。 您可以在**异常检测**类别中找到此模块。

2. 在**基于 PCA 的异常检测**模块的右侧面板中，单击 **"训练模式"** 选项，并指示是使用一组特定的参数训练模型，还是使用参数扫描来查找最佳参数。

    - **单一参数**：如果您知道如何配置模型，并提供一组特定的值作为参数，请选择此选项。

3. **PCA 中要使用的组件数**：指定要输出的输出要素或组件数。

    决定要包括多少个组件是使用PCA进行实验设计的重要组成部分。 一般指导是，不应包含与存在变量相同的 PCA 组件数。 相反，您应该从一些较小的组件开始，然后增加它们，直到满足某些条件。

    当输出组件数**小于**数据集中可用的要素列数时，将获得最佳结果。

4. 指定在随机 PCA 培训期间要执行的过采样量。 在异常检测问题中，数据不平衡使得应用标准 PCA 技术变得困难。 通过指定一些过采样量，可以增加目标实例的数量。

    如果指定 1，则不执行过采样。 如果指定任何高于 1 的值，则将生成其他示例以用于训练模型。

    有两个选项，具体取决于是否使用参数扫描：

    - **随机 PCA 的过采样参数**：键入表示少数类超采样比正常类的单整数。 （使用**单参数**训练方法时可用。

    > [!NOTE]
    > 不能查看过采样数据集。 有关过度采样如何与 PCA 一起使用的详细信息，请参阅[技术说明](#technical-notes)。

5. **启用输入要素均值规范化**：选择此选项将所有输入要素规范化为零平均值。 通常建议 PCA 规范化或缩放为零，因为 PCA 的目标是最大化变量之间的方差。

     默认情况下选择此选项。 如果已使用不同的方法或比例对值进行规范化，则取消选择此选项。

6. 连接标记的培训数据集和其中一个训练模块：

    - 如果将 **"创建训练器模式"** 选项设置为 **"单一参数**"，请使用[训练异常检测模型](train-anomaly-detection-model.md)模块。

7. 提交管道。

## <a name="results"></a>结果

培训完成后，您可以保存已训练的模型，也可以将其连接到[分数模型](score-model.md)模块以预测异常分数。

评估异常检测模型的结果需要一些其他步骤：

1. 确保分数列在两个数据集中都可用

    如果您尝试评估异常检测模型并获取错误，"评分数据集中没有要比较的分数列"，则意味着您使用的是典型的评估数据集，该数据集包含标签列，但没有概率分数。 您需要选择与异常检测模型的架构输出匹配的数据集，其中包括**评分标签**和**评分概率**列。

2. 确保标记标签列

    有时，与标签列关联的元数据在管道图中被删除。 如果发生这种情况，当您使用[评估模型](evaluate-model.md)模块比较两个异常检测模型的结果时，您可能会得到错误，"评分数据集中没有标签列"或"评分数据集中没有要比较的标签列"。

    通过在[评估模型](evaluate-model.md)模块之前添加["编辑元数据"](edit-metadata.md)模块，可以避免此错误。 使用列选择器选择类列，在 **"字段**"下拉列表中选择 **"标签**"。

3. 使用[执行 Python 脚本](execute-python-script.md)将标签列类别调整为 1（正、正常）和 0（负、异常）。

    ````
    label_column_name = 'XXX'
    anomaly_label_category = YY
    dataframe1[label_column_name] = dataframe1[label_column_name].apply(lambda x: 0 if x == anomaly_label_category else 1)
    ````

    
## <a name="technical-notes"></a>技术说明

此算法使用 PCA 近似包含正常类的子空间。 子空间由与数据协方差矩阵的顶eigen值关联的 eigenvector 跨越。 对于每个新输入，异常检测器首先计算其在 eigenvector 上的投影，然后计算规范化重建误差。 此错误是异常分数。 错误越高，实例越异常。 有关如何计算正常空间的详细信息，请参阅维基百科：[主要组件分析](https://wikipedia.org/wiki/Principal_component_analysis) 


## <a name="next-steps"></a>后续步骤

参阅 Azure 机器学习[可用的模块集](module-reference.md)。 

有关特定于设计器模块的错误列表[，请参阅设计器的异常和错误代码（预览](designer-error-codes.md)）。