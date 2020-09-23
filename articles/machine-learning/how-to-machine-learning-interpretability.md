---
title: '模型 interpretability Azure 机器学习 (预览版) '
titleSuffix: Azure Machine Learning
description: 了解如何使用 Azure 机器学习 SDK 解释模型做出预测的原因。 可以在训练和推理期间使用该 SDK 来了解模型做出预测的原因。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: mithigpe
author: minthigpen
ms.reviewer: Luis.Quintanilla
ms.date: 07/09/2020
ms.openlocfilehash: 757646a1630443f297d4b7c538721e9facdc130c
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90897438"
---
# <a name="model-interpretability-in-azure-machine-learning-preview"></a>模型 interpretability Azure 机器学习 (预览版) 


## <a name="overview-of-model-interpretability"></a>模型可解释性概述

可解释性对于数据科学家、审核人员和业务决策者确保符合公司政策、行业标准和政府法规而言，都同样至关重要：

+ 数据科学家需要能够向主管和利益干系人解释其模型，使他们可以了解所发现的结果的价值和准确度。 它们还需要利用可解释性来调试其模型，并做出有关如何改进模型的明智决策。 

+ 法定审核人员需要工具来验证与法规符合性相关的模型，并监视模型的决策对人的影响。 

+ 业务决策者需要能够为最终用户提供透明度来获得内心的宁静。 这使他们可以获得并保有他人的信任。


在模型开发过程的以下两个主要阶段中，启用解释机器学习模型的功能非常重要：
+ 在训练阶段。因为模型设计人员和评估人员可以使用模型的可解释性输出来验证假设条件，并与利益干系人建立信任关系。 他们还可以使用模型的见解进行调试，验证模型行为是否匹配目标，并检查是否存在模型不公平性或不重要的特征。

+ 在推理阶段。因为使部署的模型对外部透明可让主管了解模型在“部署后”的工作情况，及其决策如何对待和影响现实生活中的人们。 

## <a name="interpretability-with-azure-machine-learning"></a>Azure 机器学习的可解释性

可通过多个 SDK 包使用可解释性类：（了解如何[安装适用于 Azure 机器学习的 SDK 包](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true)）

* 主包 `azureml.interpret`，包含 Microsoft 支持的功能。

* `azureml.contrib.interpret`，可以尝试的预览版和试验性功能。

一般使用 `pip install azureml-interpret` 和 `pip install azureml-contrib-interpret`。


> [!IMPORTANT]
> `contrib` 命名空间中的内容不完全受支持。 试验性功能变成熟后，会逐渐转移到主命名空间。
上获取。



## <a name="how-to-interpret-your-model"></a>如何解释模型

使用 SDK 中的类和方法可以：
+ 通过为整个模型和/或单个数据点生成特征重要性值来解释模型预测。 
+ 在训练和推理期间，基于真实数据集大规模实现模型可解释性。
+ 使用交互式可视化仪表板在训练时发现数据中的模式和解释


在机器学习中，“特征”是用于预测目标数据点的数据字段。 例如，若要预测信用风险，可以使用年龄、帐户大小和帐龄的数据字段。 在本例中，年龄、帐户大小和帐龄都是**特征**。 特征重要性告知每个数据字段如何影响模型的预测。 例如，年龄可能在预测中广泛使用，而帐户大小和帐龄不会显著影响预测值。 此过程可让数据科学家解释生成的预测，使利益干系人能够洞察哪些特征在模型中最重要。

下面介绍支持的可解释性技术、支持的机器学习模型和支持的运行环境。


## <a name="supported-interpretability-techniques"></a>支持的可解释性技术

 `azureml-interpret` 使用 [Interpret-Community](https://github.com/interpretml/interpret-community/)（一个开源 Python 包，用于训练可解释的模型，并帮助解释黑盒 AI 系统）中开发的可解释性技术。 [Interpret-Community](https://github.com/interpretml/interpret-community/) 充当此 SDK 支持的解释器的主机，目前支持以下可解释性技术：

|可解释性技术|说明|类型|
|--|--|--------------------|
|SHAP 树解释器| [SHAP](https://github.com/slundberg/shap) 的树解释器，侧重于特定于**树和树系综**的多项式时间快速 SHAP 值估算算法。|特定于模型|
|SHAP 深度解释器| 深度解释器基于来自 SHAP 的解释，“是用于计算深度学习模型中 SHAP 值的高速近似算法，建立在与 [SHAP NIPS 论文](https://papers.nips.cc/paper/7062-a-unified-approach-to-interpreting-model-predictions)中所述 DeepLIFT 的关联的基础之上。 使用 TensorFlow 后端的 **TensorFlow** 模型和 **Keras** 模型受支持（还有对 PyTorch 的初步支持）”。|特定于模型|
|SHAP 线性解释器| SHAP 的线性解释器计算**线性模型**的 SHAP 值，有时还会解释特征间的关联。|特定于模型|
|SHAP 内核解释器| SHAP 的内核解释器使用特殊加权的本地线性回归来估算**任何模型**的 SHAP 值。|与模型无关|
|模拟解释器（全局代理）| 模拟解释器基于训练[全局代理模型](https://christophm.github.io/interpretable-ml-book/global.html)来模拟黑盒模型的思路。 全局代理模型是本质上可解释的模型，经训练后可以尽量准确地给出**任何黑盒模型**的预测近似值。 数据科学家可以解释代理模型，以得出有关黑盒模型的结论。 可以使用以下可解释模型之一作为代理模型：LightGBM (LGBMExplainableModel)、线性回归 (LinearExplainableModel)、随机梯度下降可解释模型 (SGDExplainableModel) 和决策树 (DecisionTreeExplainableModel)。|与模型无关|
|排列特征重要性解释器 (PFI)| 排列特征重要性是用于解释分类和回归模型的技术，该技术是受 [Breiman 的随机林论文](https://www.stat.berkeley.edu/~breiman/randomforest2001.pdf)（参阅第 10 部分）的启发开发出来的。 从较高层面看，其工作原理是对整个数据集以每次一个特征的形式随机排布数据，并计算相关性能指标的变化程度。 变化越大，该特征越重要。 PFI 可以解释**任何基础模型**的整体行为，但不会解释各个预测。 |与模型无关|




除了上面所述的可解释性技术，我们还支持另一种基于 SHAP 的解释器（称为 `TabularExplainer`）。 根据模型，`TabularExplainer` 会使用受支持的 SHAP 解释器之一：

* 用于所有基于树的模型的 TreeExplainer
* 用于 DNN 模型的 DeepExplainer
* 用于线性模型的 LinearExplainer
* 用于所有其他模型的 KernelExplainer

`TabularExplainer` 还在直接 SHAP 解释器的基础上做出了重大的功能和性能增强：

* **初始化数据集的汇总**。 如果解释速度最为重要，我们将汇总初始化数据集，并生成一个较小的代表性样本集，这可以加快生成整体和单个特征重要性值的速度。
* **对评估数据集采样**。 如果用户传入较大的评估样本集，但实际上并不需要评估所有这些样本，则可将采样参数设置为 true，以加快计算整体模型解释的速度。

下图显示了受支持解释器的当前结构。

[![机器学习可解释性体系结构](./media/how-to-machine-learning-interpretability/interpretability-architecture.png)](./media/how-to-machine-learning-interpretability/interpretability-architecture.png#lightbox)


## <a name="supported-machine-learning-models"></a>支持的机器学习模型

SDK 的 `azureml.interpret` 包支持使用以下数据集格式训练的模型：
- `numpy.array`
- `pandas.DataFrame`
- `iml.datatypes.DenseData`
- `scipy.sparse.csr_matrix`

解释函数接受模型和管道作为输入。 如果提供某个模型，该模型必须实现符合 Scikit 约定的预测函数 `predict` 或 `predict_proba`。 如果模型不支持此功能，则可以将模型包装到与 Scikit 中的 `predict` 或 `predict_proba` 生成相同结果的函数中，并将该包装器函数与所选解释器配合使用。 如果提供了某个管道，则解释函数将假设正在运行的管道脚本会返回预测。 使用此包装技术，`azureml.interpret` 可以支持通过 PyTorch、TensorFlow 和 Keras 深度学习框架训练的模型以及经典机器学习模型。

## <a name="local-and-remote-compute-target"></a>本地和远程计算目标

`azureml.interpret` 包旨在与本地和远程计算目标配合使用。 如果在本地运行，SDK 函数不会与任何 Azure 服务联系。 

可以在 Azure 机器学习计算上远程运行解释，并将解释信息记录到 Azure 机器学习运行历史记录服务中。 记录此信息后，Azure 机器学习工作室中便会提供来自该解释的报告和可视化效果，供用户分析。


## <a name="next-steps"></a>后续步骤

- 参阅[操作指南](how-to-machine-learning-interpretability-aml.md)，为本地的模型训练以及 Azure 机器学习远程计算资源上的模型训练启用可解释性。 
- 参阅[示例笔记本](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model)了解更多方案。 
- 如果对文本方案的可解释性感兴趣，请参阅 [Interpret-text](https://github.com/interpretml/interpret-text)（[Interpret-Community](https://github.com/interpretml/interpret-community/) 的相关开源存储库）来了解用于 NLP 的可解释性技术。 `azureml.interpret` 包目前不支持这些技术，但你可以从[文本分类上的示例笔记本](https://github.com/interpretml/interpret-text/blob/master/notebooks/text_classification/text_classification_classical_text_explainer.ipynb)入手。
