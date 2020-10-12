---
title: 基于 PCA 的异常情况检测：模块参考
titleSuffix: Azure Machine Learning
description: 了解如何使用“基于 PCA 的异常情况检测”模块基于主体组件分析 (PCA) 创建异常情况检测模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: fa90fcb9ebc17be9a658b08873234eada98b0fba
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90898453"
---
# <a name="pca-based-anomaly-detection-module"></a>“基于 PCA 的异常情况检测”模块

本文介绍如何使用 Azure 机器学习设计器中的 PCA-Based 异常情况检测模块，根据主体组件分析 (PCA) 创建异常情况检测模型。

此模块在以下场景中帮助你构建模型：很容易从一个类（例如有效交易）中获得训练数据，但很难获得目标异常情况的足够样本。 

例如，在检测欺诈性交易时，你通常没有足够的可用于训练的欺诈示例。 但是，你可能有许多良好交易的示例。 “基于 PCA 的异常情况检测”模块通过分析可用的特征来确定什么构成“正常”类，从而解决了这个问题。 然后，此模块将应用距离指标来识别表示异常情况的事例。 此方法允许你使用现有的不均衡数据来训练模型。

## <a name="more-about-principal-component-analysis"></a>有关主体组件分析的详细信息

PCA 是机器学习中的一项成熟技术。 它经常用于探索性数据分析，因为它揭示了数据的内部结构并解释了数据差异。

PCA 会分析包含多个变量的数据。 它将查找变量间的相关性，并确定最能捕获结果差异的值的组合。 这些组合的特征值用来创建一个更精简的特征空间，称为“主体组件”。

为了进行异常情况检测，将分析每个新输入。 异常情况检测算法会计算其在特征向量上的投影以及规范化的重构误差。 此规范化误差用作异常情况得分。 错误级别越高，实例越异常。

若要详细了解 PCA 如何工作以及异常情况检测的实现，请参阅以下文章：

- [A randomized algorithm for principal component analysis](https://arxiv.org/abs/0809.2274)（主体组件分析的随机算法），作者：Rokhlin、Szlan 和 Tygert

- [Finding Structure with Randomness:Probabilistic Algorithms for Constructing Approximate Matrix Decompositions](http://users.cms.caltech.edu/~jtropp/papers/HMT11-Finding-Structure-SIREV.pdf)（随机查找结构：用于构造近似矩阵分解的概率算法）（PDF 下载），作者：Halko、Martinsson 和 Tropp

## <a name="how-to-configure-pca-based-anomaly-detection"></a>如何配置“基于 PCA 的异常情况检测”

1. 在设计器中向管道添加“基于 PCA 的异常情况检测”模块。 可以在“异常情况检测”类别中找到此模块。

2. 在模块的右面板中，选择“训练模式”选项。 指示是要使用特定的一组参数来训练模型，还是使用参数扫描来查找最佳参数。

    如果你知道自己想要如何配置模型，请选择“单一参数”选项并提供特定的一组值作为参数。

3. 对于“要在 PCA 中使用的组件数”，请指定所需的输出特征或组件的数目。

    在使用 PCA 的试验设计中，决定要包含的组件数是一个重要环节。 一般指导原则是，不应包含与变量数相同的 PCA 组件数， 而应从较小的组件数开始，不断增加组件，直至满足某个条件。

    当输出组件的数量小于数据集中提供的特征列的数量时，将获得最佳结果。

4. 指定在随机 PCA 训练期间要执行的过度抽样的量。 在异常情况检测问题中，数据不均衡导致难以应用标准 PCA 技术。 通过指定一定数量的过度抽样，你可以增加目标实例的数目。

    如果指定 **1**，则不会执行任何过度抽样。 如果指定了高于 **1** 的任何值，则会生成更多用于训练模型的示例。

    有两个选项可用，具体取决于你是否使用参数扫描：

    - **随机 PCA 的过度抽样参数**：键入一个整数，用以表示少数类与正常类相比的过度抽样比率。 （当你使用**单一参数**训练方法时，此选项可用。）

    > [!NOTE]
    > 你无法查看过度采样的数据集。 若要详细了解如何将过度抽样与 PCA 一起使用，请参阅[技术说明](#technical-notes)。

5. 选择“启用输入特征平均值规范化”选项，将所有输入特征规范化为平均值零。 对于 PCA，通常建议规范化或缩放为零，因为 PCA 的目标是最大程度地提高变量之间的差异。

    默认情况下选择此选项。 如果已通过不同的方法或缩放对值进行了规范化，请取消选择此选项。

6. 连接一个带标记的训练数据集和一个训练模块。

   如果将“创建训练器模式”选项设置为“单一参数”，请使用[训练异常情况检测模型](train-anomaly-detection-model.md)模块。 

7. 提交管道。

## <a name="results"></a>结果

训练完成后，你可以保存训练后的模型。 也可以将其连接到[评分模型](score-model.md)模块来预测异常情况得分。

若要评估异常情况检测模型的结果，请执行以下操作：

1. 确保两个数据集中都有分数列。

    如果你尝试评估某个异常情况检测模型，但出现“评分数据集中没有要比较的分数列”错误，则表明你使用的是包含标签列但未包含概率分数的典型评估数据集。 请选择与异常情况检测模型的架构输出匹配的数据集，其中包括“评分标签”列和“评分概率”列。

2. 确保标签列已标记。

    有时，与标签列关联的元数据在管道图中被删除。 如果发生这种情况，当使用[评估模型](evaluate-model.md)模块来比较两个异常情况检测模型的结果时，可能会出现“评分数据集中没有标签列”错误。 或者，可能会出现“评分数据集中没有要比较的标签列”错误消息。

    可以通过在[评估模型](evaluate-model.md)模块之前添加[编辑元数据](edit-metadata.md)模块来避免这些错误。 请使用列选择器来选择类列，然后在“字段”列表中选择“标签”。

3. 使用[执行 Python 脚本](execute-python-script.md)模块将标签列类别调整为 **1(positive, normal)** 和 **0(negative, abnormal)** 。

    ````
    label_column_name = 'XXX'
    anomaly_label_category = YY
    dataframe1[label_column_name] = dataframe1[label_column_name].apply(lambda x: 0 if x == anomaly_label_category else 1)
    ````

    
## <a name="technical-notes"></a>技术说明

此算法使用 PCA 来近似计算包含正常类的子空间。 与数据协方差矩阵的顶部特征值相关联的特征向量横跨该子空间。 

对于每个新输入，异常情况探测器首先计算其在特征向量上的投影，然后计算规范化的重构误差。 此误差是异常情况得分。 误差越大，实例越异常。 有关如何计算正常空间的详细信息，请查看维基百科：[Principal component analysis](https://wikipedia.org/wiki/Principal_component_analysis)（主体组件分析）。 


## <a name="next-steps"></a>后续步骤

请参阅 Azure 机器学习的[可用模块集](module-reference.md)。 

有关特定于设计器模块的错误列表，请参阅 [设计器的异常和错误代码](designer-error-codes.md) 。