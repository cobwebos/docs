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
ms.reviewer: trbye
ms.date: 10/25/2019
ms.openlocfilehash: b68d2a72dc18f683f2203429908a536db1b5124a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063986"
---
# <a name="model-interpretability-in-azure-machine-learning"></a>Azure 机器学习中的模型可解释性
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

## <a name="overview-of-model-interpretability"></a>模型可解释性概述

可解释性对于数据科学家和业务决策者而言至关重要，它确保符合公司政策、行业标准和政府法规：
+ 数据科学家需要能够向主管和利益干系人解释其模型，使他们可以了解所发现的结果的价值和准确度 
+ 业务决策者需要能够从容地为最终用户提供透明度，以获得并保持他们的信任

在模型开发过程的以下两个主要阶段中，启用解释机器学习模型的功能非常重要：
+ 机器学习模型开发周期的训练阶段。 模型设计人员和评估人员可以使用模型的可解释性输出来验证假设条件，并与利益干系人建立信任关系。 他们还可以使用模型的见解进行调试，根据目标验证模型行为，并检查偏差或不重要的特征。
+ 在推断阶段，由于在部署的模型周围具有透明度，使管理人员能够了解"部署时"模型的工作原理，以及其决策在现实生活中如何对待和影响员工。 

## <a name="interpretability-with-azure-machine-learning"></a>Azure 机器学习的可解释性

本文介绍如何在 SDK 中实现模型可解释性的概念。

使用 SDK 中的类和方法可以获取：
+ 原始特征和工程特征的特征重要性值
+ 在训练和推理期间，基于真实数据集大规模实现的可解释性。
+ 交互式可视化效果，可帮助在训练时发现数据中的模式和解释


在机器学习中，“特征”是用于预测目标数据点的数据字段。**** 例如，若要预测信用风险，可以使用年龄、帐户大小和帐龄的数据字段。 在本例中，年龄、帐户大小和帐龄都是**特征**。 特征重要性告知每个数据字段如何影响模型的预测。 例如，年龄可能在预测中广泛使用，而帐户大小和帐龄不会显著影响预测准确度。 此过程可让数据科学家解释生成的预测，使利益干系人能够洞察模型中最重要的数据点。

借助这些工具，可以使用先进的技术，以方便且可缩放的方式**基于所有数据全局**解释或**基于特定的数据点在本地**解释机器学习模型。

可通过多个 SDK 包使用可解释性类。 了解如何[安装适用于 Azure 机器学习的 SDK 包](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)。

* 主包 `azureml.interpret`，包含 Microsoft 支持的功能。

* `azureml.contrib.interpret`，可以尝试的预览版和试验性功能。

* `azureml.train.automl.automlexplainer` 包，用于解释自动化机器学习模型。

> [!IMPORTANT]
> `contrib` 命名空间中的内容不完全受支持。 试验性功能变成熟后，会逐渐转移到主命名空间。

## <a name="how-to-interpret-your-model"></a>如何解释模型

您可以应用可解释性类和方法来了解模型的全局行为或特定预测。 前者称为全局解释，后者称为本地解释。

还可以根据方法是模型无关的还是模型特定的，来将这些方法分类。 某些方法面向特定类型的模型。 例如，SHAP 的树解释器仅适用于基于树的模型。 某些方法将模型视为黑盒，例如模拟解释器或 SHAP 的内核解释器。 `interpret` 包基于数据集、模型类型和用例利用这些不同的方法。

输出是有关给定的模型如何做出预测的一组信息，例如：
* 全局/局部相对特征重要性
* 全局/本地特征与预测的关系

### <a name="explainers"></a>解释器

此包使用 [Interpret-Community](https://github.com/interpretml/interpret-community/)（一个开源 Python 包，用于训练可解释的模型，并帮助解释黑盒 AI 系统）中开发的可解释性技术。 [Interpret-Community](https://github.com/interpretml/interpret-community/) 充当此 SDK 支持的解释器的主机，目前支持以下可解释性技术：

* **SHAP树解释器**[：SHAP](https://github.com/slundberg/shap)的树解释器，它侧重于多聚时间快速SHAP值估计算法，特定于树和树的合奏。
* **SHAP深度解释器**：基于[SHAP](https://github.com/slundberg/shap)的解释，深度解释器"是深度学习模型中SHAP值的高速近似算法，它建立在[SHAPNIPS论文](https://papers.nips.cc/paper/7062-a-unified-approach-to-interpreting-model-predictions)中描述的深度LIFT的连接之上。 支持使用 TensorFlow 后端的 TensorFlow 模型和 Keras 模型（并且初步支持 PyTorch）”。
* **SHAP 线性解释器**[：SHAP](https://github.com/slundberg/shap)的线性解释器计算线性模型的 SHAP 值，可以选择考虑特征间相关性。

* **SHAP 内核解释器**： [SHAP](https://github.com/slundberg/shap)的内核解释器使用特殊加权的局部线性回归来估计任何模型的 SHAP 值。
* **模仿解释器**：模仿解释器是基于训练[全球代理模型](https://christophm.github.io/interpretable-ml-book/global.html)来模拟黑盒模型的想法。 全局代理模型是内在地具有可解释性的模型，经训练后可以尽量准确地给出黑盒模型的预测近似值。 数据科学家可以解释代理模型，以得出有关黑盒模型的结论。 您可以将以下可解释模型之一用作代理模型：LightGBM （LGBM 可解释模型）、线性回归（线性可解释模型）、随机梯度下降可解释模型 （SGD 可解释模型）和决策树 （决策树可解释模型）。


* **排列特征重要性解释器**：排列特征重要性是一种技术，用于解释分类和回归模型，灵感来自[布雷曼的随机林论文](https://www.stat.berkeley.edu/~breiman/randomforest2001.pdf)（见第10节）。 从较高层面看，其工作原理是对整个数据集以每次一个特征的形式随机排布数据，并计算相关性能指标的变化程度。 变化越大，该特征越重要。

* **LIME解释器**`contrib`（）： 基于[LIME，](https://github.com/marcotcr/lime)LIME 解释器使用最先进的本地可解释模型无关解释 （LIME） 算法来创建本地代理模型. 与全局代理模型不同，LIME 侧重训练本地代理模型来解释各项预测。
* **HAN 文本解释器**（`contrib`）： HAN 文本解释器使用分层注意网络从给定黑盒文本模型的文本数据获取模型说明。 它基于给定黑盒模型的预测输出训练 HAN 代理模型。 全局训练整个文本集后，它会针对特定的文档添加一个微调步骤，以提高解释的准确度。 HAN 使用包含两个注意力层的双向 RNN 来关注句子和单词。 DNN 在黑盒模型中训练并针对特定的文档微调之后，用户可以从注意力层提取单词重要性。 经测试表明，在训练文本数据时，HAN 的准确度高于 LIME 或 SHAP，但训练时间造成的开销也更大。 此技术已得到改善，可让用户使用 GloVe 单词嵌入初始化网络，以缩短训练时间。 在远程 Azure GPU VM 上运行 HAN 可以显著改善训练时间。 [Hierarchical Attention Networks for Document Classification (Yang et al., 2016)](https://www.researchgate.net/publication/305334401_Hierarchical_Attention_Networks_for_Document_Classification)（用于文档分类的分层注意力网络（2016 年由“杨”等人合著））中介绍了 HAN 的实现。


* **表格解释器**：`TabularExplainer`使用以下逻辑调用直接[SHAP](https://github.com/slundberg/shap)解释器：

    1. 如果是基于树的模型，则应用 SHAP `TreeExplainer`，否则
    2. 如果是 DNN 模型，则应用 SHAP `DeepExplainer`，否则
    3. 如果是线性模型，则应用 SHAP `LinearExplainer`，否则
    3. 将模型视为黑盒模型，并应用 SHAP `KernelExplainer`


`TabularExplainer` 还在直接 SHAP 解释器的基础上做出了重大的功能和性能增强：

* **初始化数据集的汇总**。 如果解释速度极其重要，我们将汇总初始化数据集，并生成一个较小的代表性样本集，这可以加快全局和本地解释。
* **对评估数据集采样**。 如果用户传入较大的评估样本集，但实际上并不需要评估所有这些样本，则可将采样参数设置为 true，以加速全局解释。

下图显示了直接解释器和元解释器的当前结构。

[![机器学习可解释性体系结构](./media/how-to-machine-learning-interpretability/interpretability-architecture.png)](./media/how-to-machine-learning-interpretability/interpretability-architecture.png#lightbox)


### <a name="models-supported"></a>支持的模型

基于 Python `numpy.array`、`pandas.DataFrame`、`iml.datatypes.DenseData` 或 `scipy.sparse.csr_matrix` 格式的数据集训练的任何模型均受 SDK 的可解释性 `explain` 包的支持。

解释函数接受模型和管道作为输入。 如果提供某个模型，该模型必须实现符合 Scikit 约定的预测函数 `predict` 或 `predict_proba`。 如果提供某个管道（管道脚本的名称），解释函数将假设正在运行的管道脚本返回预测。 我们支持通过 PyTorch、TensorFlow 和 Keras 深度学习框架训练的模型。

### <a name="local-and-remote-compute-target"></a>本地和远程计算目标

`explain` 包旨在与本地和远程计算目标配合使用。 如果在本地运行，SDK 函数不会与任何 Azure 服务联系。 可以在 Azure 机器学习计算上远程运行解释，并将解释信息记录到 Azure 机器学习运行历史记录服务中。 记录此信息后，便可以在 Azure 机器学习工作区中提供来自解释的报告和可视化效果供用户分析。


## <a name="next-steps"></a>后续步骤

参阅[操作指南](how-to-machine-learning-interpretability-aml.md)，为本地的模型训练以及 Azure 机器学习远程计算资源上的模型训练启用可解释性。 参阅[示例笔记本](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model)了解更多方案。
