---
title: Azure 机器学习中的模型可解释性
titleSuffix: Azure Machine Learning
description: 了解如何使用 Azure 机器学习 SDK 解释模型做出预测的原因。 可以在训练和推理期间使用该 SDK 来了解模型做出预测的原因。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: Luis.Quintanilla
ms.date: 04/02/2020
ms.openlocfilehash: f4210352a9d8cd3cd9cb9afda7d9a4798d96f44b
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2020
ms.locfileid: "82982881"
---
# <a name="model-interpretability-in-azure-machine-learning"></a>Azure 机器学习中的模型可解释性
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

## <a name="overview-of-model-interpretability"></a>模型可解释性概述

Interpretability 对于数据科学家、审计员和业务决策者非常重要，可确保符合公司政策、行业标准和政府法规：

+ 数据科学家需要能够向管理人员和利益干系人解释他们的模型，因此他们可以了解他们发现的价值和准确性。 它们还需要 interpretability 来调试其模型，并做出有关如何改进它们的明智决策。 

+ 法律审计员需要工具来验证与法规遵从性相关的模型，并监视模型的决策对人的影响。 

+ 业务决策者需要能够为最终用户提供透明性，无需担心。 这样，他们就可以获得并维持信任。


在模型开发过程的以下两个主要阶段中，启用解释机器学习模型的功能非常重要：
+ 在训练阶段，模型设计人员和计算器可以使用 interpretability 的模型输出来验证假设并与利益干系人建立信任。 它们还使用该模型进行调试，验证模型行为与它们的目标，并检查模型 unfairness 或不重要的特征。

+ 在推断阶段，在已部署的模型周围具有透明性，使高级管理人员能够了解模型的工作方式，以及如何在现实生活中对人员的决策和影响。 

## <a name="interpretability-with-azure-machine-learning"></a>Azure 机器学习的可解释性

可以通过多个 SDK 包提供 interpretability 类：（了解如何[为 Azure 机器学习安装 SDK 包](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)）

* 主包 `azureml.interpret`，包含 Microsoft 支持的功能。

* `azureml.contrib.interpret`，可以尝试的预览版和试验性功能。

* `azureml.train.automl.automlexplainer` 包，用于解释自动化机器学习模型。

使用`pip install azureml-interpret`和`pip install azureml-interpret-contrib`进行一般使用， `pip install azureml-interpret-contrib`使用 AutoML 可获取 interpretability 包。


> [!IMPORTANT]
> `contrib` 命名空间中的内容不完全受支持。 试验性功能变成熟后，会逐渐转移到主命名空间。
.



## <a name="how-to-interpret-your-model"></a>如何解释模型

使用 SDK 中的类和方法，你可以：
+ 通过为整个模型和/或单个数据点生成特征重要性值来说明模型预测。 
+ 在定型和推理期间，按比例实现实际数据集的模型 interpretability。
+ 使用交互式可视化面板来发现数据中的模式和在定型时的说明


在机器学习中，“特征”是用于预测目标数据点的数据字段。**** 例如，若要预测信用风险，可以使用年龄、帐户大小和帐龄的数据字段。 在本例中，年龄、帐户大小和帐龄都是**特征**。 特征重要性告知每个数据字段如何影响模型的预测。 例如，在预测中，年龄可能会很高，而 "帐户大小" 和 "年龄" 不会显著影响预测值。 此过程允许数据科学家解释所产生的预测，使利益干系人能够查看模型中最重要的功能。

在此处了解支持的 interpretability 技术、支持的机器学习模型和支持的运行环境。


## <a name="supported-interpretability-techniques"></a>支持的 interpretability 技术

 `azureml-interpret`使用[解释社区](https://github.com/interpretml/interpret-community/)中开发的 interpretability 技术，这是一种用于定型可解释模型的开源 python 包，有助于解释黑盒 AI 系统。 [Interpret-Community](https://github.com/interpretml/interpret-community/) 充当此 SDK 支持的解释器的主机，目前支持以下可解释性技术：

|Interpretability 方法|说明|类型|
|--|--|--------------------|
|SHAP 树说明| [SHAP](https://github.com/slundberg/shap)的树说明，侧重于多项式时间快速 SHAP 值估算算法，特定于树**和整体的**树。|特定于模型|
|SHAP Deep 说明| 根据 SHAP 中的说明，Deep 说明 "是一种高速近似算法，适用于深度学习模型中的 SHAP 值，这些值建立在与[SHAP NIPS 纸张](https://papers.nips.cc/paper/7062-a-unified-approach-to-interpreting-model-predictions)中所述 DeepLIFT 的连接上。 支持使用 TensorFlow 后端的**TensorFlow**模型和**Keras**模型（还支持 PyTorch 的初步支持） "。|特定于模型|
|SHAP 线性说明| SHAP 的线性说明计算**线性模型**的 SHAP 值，还可以选择对功能间相关进行记帐。|特定于模型|
|SHAP 内核说明| SHAP 的内核说明使用特殊的加权本地线性回归来估算**任何模型**的 SHAP 值。|模型-不可知|
|模拟说明（全局代理项）| 模拟解释器基于训练[全局代理模型](https://christophm.github.io/interpretable-ml-book/global.html)来模拟黑盒模型的思路。 全局代理项模型是一种固有的可解释模型，经过训练，可尽可能准确地估计**任何黑色框模型**的预测。 数据科学家可以解释代理项模型，以绘制关于黑色框模型的结论。 您可以使用以下可解释模型之一作为代理项模型： LightGBM （LGBMExplainableModel）、线性回归（LinearExplainableModel）、随机梯度下降 explainable 模型（SGDExplainableModel）和决策树（DecisionTreeExplainableModel）。|模型-不可知|
|排列特征重要性说明（PFI）| 排列特征重要性是用于解释分类和回归模型的技术，该技术是受 [Breiman 的随机林论文](https://www.stat.berkeley.edu/~breiman/randomforest2001.pdf)（参阅第 10 部分）的启发开发出来的。 从较高层面看，其工作原理是对整个数据集以每次一个特征的形式随机排布数据，并计算相关性能指标的变化程度。 变化越大，该特征越重要。 PFI 可以解释**任何基础模型**的总体行为，但不会说明各个预测。 |模型-不可知|




除了上面所述的 interpretability 方法之外，我们还支持另一种基于`TabularExplainer`SHAP 的说明，称为。 根据模型， `TabularExplainer`使用受支持的 SHAP explainers 之一：

* 所有基于树的模型的 TreeExplainer
* DeepExplainer for DNN 模型
* 线性模型的 LinearExplainer
* 针对所有其他模型的 KernelExplainer

`TabularExplainer` 还在直接 SHAP 解释器的基础上做出了重大的功能和性能增强：

* **初始化数据集的汇总**。 在解释速度最重要的情况下，我们汇总了初始化数据集，并生成了一小部分代表性示例，从而加速了整体功能和各个特征重要性值的生成。
* **对评估数据集采样**。 如果用户传入一组大量的计算示例，但实际上并不需要计算所有这些示例，则可以将采样参数设置为 true，以加快总体模型说明的计算速度。

下图显示了受支持的 explainers 的当前结构。

[![机器学习 Interpretability 体系结构](./media/how-to-machine-learning-interpretability/interpretability-architecture.png)](./media/how-to-machine-learning-interpretability/interpretability-architecture.png#lightbox)


## <a name="supported-machine-learning-models"></a>支持的机器学习模型

SDK `azureml.interpret`包支持通过以下数据集格式进行定型的模型：
- `numpy.array`
- `pandas.DataFrame`
- `iml.datatypes.DenseData`
- `scipy.sparse.csr_matrix`

解释函数接受模型和管道作为输入。 如果提供某个模型，该模型必须实现符合 Scikit 约定的预测函数 `predict` 或 `predict_proba`。 如果您的模型不支持这种情况，则可以在生成与 Scikit-learn 相同的结果`predict` `predict_proba`的函数中包装模型，然后将该包装函数与所选说明一起使用。 如果提供了管道，则解释函数假设正在运行的管道脚本返回一个预测。 使用此包装技术， `azureml.interpret`可支持通过 PyTorch、TensorFlow 和 Keras 深度学习框架以及经典机器学习模型进行训练的模型。

## <a name="local-and-remote-compute-target"></a>本地和远程计算目标

`azureml.interpret` 包旨在与本地和远程计算目标配合使用。 如果在本地运行，SDK 函数不会与任何 Azure 服务联系。 

可以在 Azure 机器学习计算上远程运行说明，并将说明信息记录到 Azure 机器学习运行历史记录服务中。 记录此信息后，可在 Azure 机器学习 studio 中轻松获取有关说明的报表和可视化效果，以便进行用户分析。


## <a name="next-steps"></a>后续步骤

- 参阅[操作指南](how-to-machine-learning-interpretability-aml.md)，为本地的模型训练以及 Azure 机器学习远程计算资源上的模型训练启用可解释性。 
- 参阅[示例笔记本](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model)了解更多方案。 
- 如果对用于文本方案的 interpretability 感兴趣，请[参阅解释](https://github.com/interpretml/interpret-text) [-社区](https://github.com/interpretml/interpret-community/)的相关开源存储库，用于 NLP 的 interpretability 技术。 `azureml.interpret`包目前不支持这些技术，但可以开始使用[文本分类的示例笔记本](https://github.com/interpretml/interpret-text/blob/master/notebooks/text_classification/text_classification_classical_text_explainer.ipynb)。
