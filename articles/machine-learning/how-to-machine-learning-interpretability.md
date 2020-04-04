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
ms.openlocfilehash: fcb837af85a54102e8c9eafc33249af9dba6b5ce
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631420"
---
# <a name="model-interpretability-in-azure-machine-learning"></a>Azure 机器学习中的模型可解释性
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

## <a name="overview-of-model-interpretability"></a>模型可解释性概述

可解释性对于数据科学家、审核员和业务决策者来说都至关重要，以确保符合公司政策、行业标准和政府法规：

+ 数据科学家需要能够向高管和利益相关者解释他们的模型，以便他们能够理解其发现的价值和准确性。 它们还需要可解释性来调试其模型，并就如何改进模型做出明智的决策。 

+ 法律审计师需要工具来验证与法规遵从性有关的模式，并监控模型的决策如何影响人类。 

+ 业务决策者需要通过有能力为最终用户提供透明度来平和。 这使他们能够赢得并保持信任。


在模型开发过程的以下两个主要阶段中，启用解释机器学习模型的功能非常重要：
+ 在培训阶段，作为模型设计人员和评估者，可以使用模型的解释性输出来验证假设并与利益相关者建立信任。 他们还使用对模型的见解进行调试，验证模型行为与其目标相匹配，并检查模型不公平或功能不重要。

+ 在推断阶段，由于在部署的模型周围具有透明度，使管理人员能够了解"部署时"模型的工作原理，以及其决策在现实生活中如何对待和影响员工。 

## <a name="interpretability-with-azure-machine-learning"></a>Azure 机器学习的可解释性

可解释性类可通过多个 SDK 包提供：（了解如何为[Azure 机器学习安装 SDK 包](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)）

* 主包 `azureml.interpret`，包含 Microsoft 支持的功能。

* `azureml.contrib.interpret`，可以尝试的预览版和试验性功能。

* `azureml.train.automl.automlexplainer` 包，用于解释自动化机器学习模型。

用于`pip install azureml-interpret`和`pip install azureml-interpret-contrib`用于一般用途，`pip install azureml-interpret-contrib`以及用于自动ML获取可解释性包。


> [!IMPORTANT]
> `contrib` 命名空间中的内容不完全受支持。 试验性功能变成熟后，会逐渐转移到主命名空间。
.



## <a name="how-to-interpret-your-model"></a>如何解释模型

使用 SDK 中的类和方法，可以：
+ 通过为整个模型和/或单个数据点生成要素重要性值来解释模型预测。 
+ 在培训和推理期间，在大规模实现实际数据集的模型可解释性。
+ 使用交互式可视化仪表板在培训时发现数据模式和说明


在机器学习中，“特征”是用于预测目标数据点的数据字段。**** 例如，若要预测信用风险，可以使用年龄、帐户大小和帐龄的数据字段。 在本例中，年龄、帐户大小和帐龄都是**特征**。 特征重要性告知每个数据字段如何影响模型的预测。 例如，在预测中可能大量使用年龄，而帐户大小和年龄不会对预测值产生重大影响。 此过程允许数据科学家解释结果预测，以便利益干系人能够了解模型中最重要的要素。

在此处了解受支持的可解释性技术、支持的机器学习模型和支持的运行环境。


## <a name="supported-interpretability-techniques"></a>支持的可解释性技术

 `azureml-interpret`使用[解释社区](https://github.com/interpretml/interpret-community/)开发的解释性技术，这是一个开源的python包，用于训练可解释的模型，并帮助解释黑盒AI系统。 [Interpret-Community](https://github.com/interpretml/interpret-community/) 充当此 SDK 支持的解释器的主机，目前支持以下可解释性技术：

|可解释性技术|说明|类型|
|--|--|--------------------|
|1. SHAP 树解释器| [SHAP](https://github.com/slundberg/shap)的树解释器，它侧重于多聚时间快速SHAP值估计算法特定于**树木和树的合奏**。|特定于型号|
|2. SHAP 深度解说器| 深度解释器基于 [SHAP](https://github.com/slundberg/shap) 的解释，“是针对深度学习模型中 SHAP 值的高速近似算法，建立在使用 [SHAP NIPS 论文](https://papers.nips.cc/paper/7062-a-unified-approach-to-interpreting-model-predictions)中所述 DeepLIFT 的连接的基础之上。 支持使用 TensorFlow 后端的**TensorFlow**模型和**Keras**模型（也有对 PyTorch 的初步支持）"。|特定于型号|
|3. SHAP 线性解释器| [SHAP](https://github.com/slundberg/shap)的线性解释器计算**线性模型**的 SHAP 值，可以选择考虑特征间相关性。|特定于型号|
|4. SHAP内核解释器| [SHAP](https://github.com/slundberg/shap)的内核解释器使用特殊加权的局部线性回归来估计**任何模型**的 SHAP 值。|模型无关|
|5. 模拟解释器（全球代理）| 模拟解释器基于训练[全局代理模型](https://christophm.github.io/interpretable-ml-book/global.html)来模拟黑盒模型的思路。 全局代理模型是一种内在可解释的模型，经过训练，可以尽可能准确地近似**任何黑盒模型**的预测。 数据科学家可以解释代理模型，以得出有关黑盒模型的结论。 您可以将以下可解释模型之一用作代理模型：LightGBM（LGBM 可解释模型）、线性回归（线性可解释模型）、随机梯度下降可解释模型（SGD 可解释模型）和决策树（决策树可解释模型）。|模型无关|
|6. 排列特征重要性解释器 （PFI）| 排列特征重要性是用于解释分类和回归模型的技术，该技术是受 [Breiman 的随机林论文](https://www.stat.berkeley.edu/~breiman/randomforest2001.pdf)（参阅第 10 部分）的启发开发出来的。 从较高层面看，其工作原理是对整个数据集以每次一个特征的形式随机排布数据，并计算相关性能指标的变化程度。 变化越大，该特征越重要。 PFI 可以解释**任何基础模型**的总体行为，但不解释单个预测。 |模型无关|




除了上述可解释性技术外，我们支持另一种[基于SHAP的解释器](https://github.com/slundberg/shap)，称为`TabularExplainer`。 根据模型，`TabularExplainer`使用支持的 SHAP 解释器之一：

* 所有基于树的模型的树解释器
* DNN 型号的深层解释器
* 线性模型的线性解释器
* 所有其他型号的内核解释器

`TabularExplainer` 还在直接 SHAP 解释器的基础上做出了重大的功能和性能增强：

* **初始化数据集的汇总**。 在解释速度最重要的情况下，我们将初始化数据集汇总并生成一小组具有代表性的样本，从而加快生成整体和单个要素重要性值。
* **对评估数据集采样**。 如果用户传递了一组大型评估样本，但实际上并不需要对其进行所有评估，则可以将采样参数设置为 true 以加快计算总体模型解释。

下图显示了受支持的解释器的当前结构。

[![机器学习可解释性体系结构](./media/how-to-machine-learning-interpretability/interpretability-architecture.png)](./media/how-to-machine-learning-interpretability/interpretability-architecture.png#lightbox)


## <a name="supported-machine-learning-models"></a>支持的机器学习模型

SDK`azureml.interpret`包支持使用以下数据集格式训练的模型：
- `numpy.array`
- `pandas.DataFrame`
- `iml.datatypes.DenseData`
- `scipy.sparse.csr_matrix`

解释函数接受模型和管道作为输入。 如果提供某个模型，该模型必须实现符合 Scikit 约定的预测函数 `predict` 或 `predict_proba`。 如果模型不支持此功能，则可以将模型包装在生成与`predict`Scikit 相同结果的函数中，`predict_proba`并将该包装函数与选定的解释器一起使用。 如果提供了管道，则解释函数假定正在运行的管道脚本返回预测。 使用此包装技术，`azureml.interpret`可以支持通过 PyTorch、TensorFlow 和 Keras 深度学习框架以及经典机器学习模型训练的模型。

## <a name="local-and-remote-compute-target"></a>本地和远程计算目标

`azureml.interpret` 包旨在与本地和远程计算目标配合使用。 如果在本地运行，SDK 函数不会与任何 Azure 服务联系。 

您可以在 Azure 机器学习计算上远程运行说明，并将说明信息记录到 Azure 机器学习运行历史记录服务中。 记录此信息后，在 Azure 机器学习工作室随时提供解释中的报表和可视化效果，以便进行用户分析。


## <a name="next-steps"></a>后续步骤

参阅[操作指南](how-to-machine-learning-interpretability-aml.md)，为本地的模型训练以及 Azure 机器学习远程计算资源上的模型训练启用可解释性。 参阅[示例笔记本](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model)了解更多方案。
