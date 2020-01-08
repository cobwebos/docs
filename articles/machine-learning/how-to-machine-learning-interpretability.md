---
title: Azure 机器学习中的模型 interpretability
titleSuffix: Azure Machine Learning
description: 了解如何使用 Azure 机器学习 SDK 解释模型进行预测的原因。 可以在定型和推理期间使用它来了解模型如何进行预测。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: trbye
ms.date: 10/25/2019
ms.openlocfilehash: 35623be4f0e4495388dc58b1e4d4f6c4663a93fd
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/28/2019
ms.locfileid: "75537261"
---
# <a name="model-interpretability-in-azure-machine-learning"></a>Azure 机器学习中的模型 interpretability
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

## <a name="overview-of-model-interpretability"></a>模型 interpretability 概述

Interpretability 对于数据科学家和业务决策者非常重要，可确保符合公司政策、行业标准和政府法规：
+ 数据科学家需要能够向管理人员和利益干系人解释其模型，以便他们可以了解他们的发现的价值和准确性 
+ 业务决策者需要能够为最终用户提供透明性以获得和维护其信任的能力

在模型开发的两个主要阶段，启用对机器学习模型进行解释的功能非常重要：
+ 在机器学习模型开发周期的培训阶段。 模型设计器和计算器可以使用模型的 interpretability 输出来验证假设并与利益干系人建立信任关系。 它们还使用该模型进行调试，验证模型行为与它们的目标，并检查偏向或不重要的特征。
+ 在推断阶段，在已部署的模型周围具有透明性，使高级管理人员能够了解模型的工作方式，以及如何在现实生活中对人员的决策和影响。 

## <a name="interpretability-with-azure-machine-learning"></a>Interpretability 与 Azure 机器学习

本文介绍如何在 SDK 中实现模型 interpretability 概念。

使用 SDK 中的类和方法，可以获取：
+ 原始和工程功能的功能重要性值
+ 在定型和推理期间，大规模 Interpretability 实际数据集。
+ 交互式可视化效果，可帮助发现数据中的模式以及定型时间的说明


在机器学习中，**功能**是用于预测目标数据点的数据字段。 例如，要预测信用风险，可以使用 "年龄"、"帐户大小" 和 "帐户年龄" 数据字段。 在这种情况下，年龄、帐户大小和帐户期限都是**功能**。 功能重要性告诉您每个数据字段对模型预测的影响。 例如，在预测中，年龄可能会很高，而 "帐户大小" 和 "年龄" 不会显著影响预测准确性。 此过程允许数据科学家解释所产生的预测，使利益干系人能够查看模型中最重要的数据点。

使用这些工具，您可以使用一种易于使用且可缩放的方式，以**全局方式在所有数据上**或以**本地方式在特定数据点上**对机器学习模型进行说明。

Interpretability 类通过多个 SDK 包提供。 了解如何[为 Azure 机器学习安装 SDK 包](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)。

* `azureml.interpret`（主要包），其中包含 Microsoft 支持的功能。

* 可以尝试 `azureml.contrib.interpret`、预览和实验性功能。

* 用于解释自动机器学习模型的 `azureml.train.automl.automlexplainer` 包。

> [!IMPORTANT]
> 不完全支持 `contrib` 命名空间中的内容。 当实验性功能变得成熟时，它们会逐渐移到主命名空间。

## <a name="how-to-interpret-your-model"></a>如何解释模型

您可以应用 interpretability 类和方法来了解模型的全局行为或特定的预测。 前者称为全局说明，后者称为本地说明。

还可以根据方法是与模型无关还是特定于模型来分类这些方法。 某些方法面向特定类型的模型。 例如，SHAP 的树说明仅适用于基于树的模型。 某些方法将模型视为黑色框，如模仿说明或 SHAP 的内核说明。 `interpret` 包基于数据集、模型类型和用例利用这些不同的方法。

输出是有关给定模型如何进行预测的一组信息，如：
* 全局/局部相对功能重要性
* 全局/本地功能和预测关系

### <a name="explainers"></a>Explainers

此包使用[解释社区](https://github.com/interpretml/interpret-community/)中开发的 interpretability 技术，这是一种用于定型可解释模型的开源 python 包，有助于解释黑盒 AI 系统。 [解释社区](https://github.com/interpretml/interpret-community/)充当此 SDK 支持的 explainers 的主机，目前支持以下 interpretability 技术：

* **SHAP 树说明**： [SHAP](https://github.com/slundberg/shap)的树说明，侧重于多项式时间快速 SHAP 值估算算法，特定于树的树和整体。
* **SHAP Deep 说明**：根据[SHAP](https://github.com/slundberg/shap)的说明，deep 说明 "是一种高速近似算法，用于在与[SHAP DeepLIFT 白皮书](https://papers.nips.cc/paper/7062-a-unified-approach-to-interpreting-model-predictions)中所述的 SHAP 建立连接的深度学习模型中建立 NIPS 值。 支持使用 TensorFlow 后端的 TensorFlow 模型和 Keras 模型（还支持 PyTorch 的初步支持） "。
* **SHAP 线性说明**： [SHAP](https://github.com/slundberg/shap)的线性说明计算线性模型的 SHAP 值，还可以选择对功能间相关进行记帐。

* **SHAP 内核说明**： [SHAP](https://github.com/slundberg/shap)的内核说明使用专门加权的本地线性回归来估算任何模型的 SHAP 值。
* **模拟说明**：模拟说明是基于定型[全局代理项模型](https://christophm.github.io/interpretable-ml-book/global.html)来模拟黑盒模型的理念。 全局代理项模型是一种固有的可解释模型，经过训练，可尽可能准确地估计黑色框模型的预测。 数据科研人员可以解释代理项模型，以绘制关于黑色框模型的结论。 您可以使用以下可解释模型之一作为代理模型： LightGBM （LGBMExplainableModel）、线性回归（LinearExplainableModel）、随机梯度下降 explainable 模型（SGDExplainableModel）和决策树（DecisionTreeExplainableModel).


* **排列特征重要性说明**：排列特征重要性是一种技术，用于说明由[Breiman 的随机林纸](https://www.stat.berkeley.edu/%7Ebreiman/randomforest2001.pdf)（请参阅第10部分）激发的分类和回归模型。 从较高层次来看，它的工作方式是对整个数据集随机混排一项功能，并计算出相关性能指标的变化程度。 变化越大，特征就越重要。

* **酸橙色说明**（`contrib`）：基于[酸橙色](https://github.com/marcotcr/lime)，酸橙色说明使用先进的本地可解释模式说明（酸橙色）算法来创建本地代理项模型。 与全局代理项模型不同，酸橙色侧重于定型本地代理项模型来解释各个预测。
* **中文文本说明**（`contrib`）：汉语文本说明使用分层网络，从给定的黑色框文本模型的文本数据获取模型说明。 它在给定的黑盒模型的预测输出上定型了汉语代理模型。 在整个文本语料库中进行全局定型后，它会为特定文档添加一个微调步骤，以便提高说明的准确性。 汉语使用双向 RNN，其中包含两个注意层，用于句子和单词。 DNN 在黑盒模型上定型并对特定文档进行微调后，用户就可以从 "注意" 层中提取单词 "importances"。 对于文本数据来说，中文的显示效果比酸橙色或 SHAP 更精确，但对于培训时间而言，其开销更大。 已进行了改进，使用户能够使用手套 word 嵌入初始化网络以缩短定型时间。 通过在远程 Azure GPU VM 上运行汉语，可以显著提高训练时间。 ["文档分类的分层注意事项网络" （"阳 et al，2016）"](https://www.researchgate.net/publication/305334401_Hierarchical_Attention_Networks_for_Document_Classification)中介绍了汉语的实现。


* **表格说明**： `TabularExplainer` 采用以下逻辑调用直接[SHAP](https://github.com/slundberg/shap) Explainers：

    1. 如果它是基于树的模型，请应用 SHAP `TreeExplainer`，否则
    2. 如果是 DNN 模型，则应用 SHAP `DeepExplainer`
    3. 如果它是线性模型，则应用 SHAP `LinearExplainer`
    3. 将其视为一个黑白模型，并应用 SHAP `KernelExplainer`


通过直接 SHAP Explainers，`TabularExplainer` 还实现了重大功能和性能增强：

* **初始化数据集的摘要**。 在最重要的解释速度的情况下，我们汇总了初始化数据集，并生成了一小部分代表性示例，这将加快全局和本地说明。
* **采样计算数据集**。 如果用户传入一组大量的评估样本，但实际上并不需要计算全部，则可以将采样参数设置为 true，以加快全局说明的速度。

下图显示了 direct 和元 explainers 的当前结构。

[![机器学习 Interpretability 体系结构](./media/how-to-machine-learning-interpretability/interpretability-architecture.png)](./media/how-to-machine-learning-interpretability/interpretability-architecture.png#lightbox)


### <a name="models-supported"></a>支持的模型

SDK 的 interpretability `explain` 包支持在 Python `numpy.array`、`pandas.DataFrame`、`iml.datatypes.DenseData`或 `scipy.sparse.csr_matrix` 格式的数据集上定型的任何模型。

解释函数接受模型和管道作为输入。 如果提供了模型，则该模型必须实现符合 Scikit-learn 约定 `predict` 或 `predict_proba` 的预测函数。 如果提供了管道（管道脚本的名称），则说明函数假设正在运行的管道脚本返回一个预测。 我们支持通过 PyTorch、TensorFlow 和 Keras 深度学习框架训练的模型。

### <a name="local-and-remote-compute-target"></a>本地和远程计算目标

`explain` 包旨在与本地和远程计算目标一起使用。 如果在本地运行，SDK 函数将不会与任何 Azure 服务联系。 可以在 Azure 机器学习计算上远程运行说明，并将说明信息记录到 Azure 机器学习运行历史记录服务中。 记录此信息后，来自说明的报表和可视化效果随时可用于用户分析 Azure 机器学习工作区。


## <a name="next-steps"></a>后续步骤

请参阅[如何](service/how-to-machine-learning-interpretability-aml.md)在本地和 Azure 机器学习远程计算资源中启用模型定型培训的操作方法。 有关其他方案，请参阅[示例笔记本](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model)。
