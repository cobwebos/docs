---
title: 可视界面
titleSuffix: Azure Machine Learning service
description: 了解的术语、 概念和组成 Azure 机器学习服务的可视界面 （预览版） 的工作流。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 05/15/2019
ms.openlocfilehash: 0b158a1d713e0de8f3d1b2672aed442fce66e724
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2019
ms.locfileid: "65917155"
---
# <a name="what-is-the-visual-interface-for-azure-machine-learning-service"></a>什么是 Azure 机器学习服务的可视界面？ 

Azure 机器学习服务的可视界面 （预览版），可准备数据、 训练、 测试、 部署、 管理和跟踪机器学习模型，而无需编写代码。

不需要编程，以可视方式连接[数据集](#dataset)并[模块](#module)来构造您的模型。

可视界面使用你的 Azure 机器学习服务[工作区](concept-workspace.md)到：

+ 编写的项目[试验](#experiment)运行到工作区。
+ 访问[数据集](#dataset)。
+ 使用[计算资源](#compute)要运行此试验的工作区中。 
+ 注册[模型](concept-azure-machine-learning-architecture.md#model)。
+ [部署](#deployment)模型作为 web 服务上的计算的工作区中的资源。

![可视化界面的概述](media/ui-concept-visual-interface/overview.png)

## <a name="workflow"></a>工作流

可视界面提供交互式的可视画布，以快速构建、 测试和循环访问模型。 

+ 您拖放[模块](#module)拖动到画布上。
+ 将模块连接在一起窗体[试验](#experiment)。
+ 运行试验使用机器学习服务工作区的计算资源。
+ 循环访问模型设计通过编辑实验并再次运行。
+ 当准备好后时，将转换你**训练实验**到**预测实验**。
+ [部署](#deployment)将预测实验作为 web 服务，使您的模型可以其他人访问。

## <a name="experiment"></a>试验

从零开始，创建一个试验或使用现有的示例试验作为模板。  每次运行试验，项目存储在工作区中。

试验由数据集和分析模块，你将连接在一起以构造模型组成。 具体而言，有效的试验有以下特征：

* 可能仅对模块连接的数据集。
* 模块可能连接到数据集或其他模块。
* 模块的所有输入的端口必须具有一些连接到数据流。
* 所有必需的每个模块的参数必须设置。

一个简单的实验的示例，请参阅[快速入门：准备和可视化数据而无需在 Azure 机器学习中编写代码](ui-quickstart-run-experiment.md)。

有关预测分析解决方案的更完整演练，请参阅[教程：预测汽车价格与可视界面](ui-tutorial-automobile-price-train-score.md)。

## <a name="dataset"></a>数据集

数据集是已上载至可视化界面，在建模过程中使用的数据。 许多示例数据集都包含为您要试验，并且可以上传多个数据集根据你的需要。

## <a name="module"></a>模块

模块是可对数据执行的算法。 可视界面具有多个模块从数据引入函数范围到培训、 评分和验证过程。

模块可能提供一组参数用于配置模块的内部算法。 如果选择在画布上的模块，模块的参数会显示在画布右侧的属性窗格中。 可以在该窗格中修改参数来调整模型。

![模块属性](media/ui-concept-visual-interface/properties.png)

有关某些帮助通过浏览可用的机器学习算法库，请参阅[算法和模块参考概述](../algorithm-module-reference/module-reference.md)

## <a name="compute"></a> 计算资源

使用计算，运行实验或主机作为 web 服务已部署的模型工作区中的资源。 支持的计算目标为：


| 计算目标 | 培训 | 部署 |
| ---- |:----:|:----:|
| Azure 机器学习计算 | ✓ | |
| Azure Kubernetes 服务 | | ✓ |

计算目标附加到机器学习[工作区](concept-workspace.md)。 工作区中管理计算目标[Azure 门户](https://portal.azure.com)。

## <a name="deployment"></a>部署

预测分析模型准备就绪后，您可以将其部署为 web 服务直接从可视界面。

Web 服务提供应用程序和评分模型之间的接口。 外部应用程序可以与评分模型实时通信。 对 web 服务的调用将预测结果返回到外部应用程序。 若要调用 Web 服务，需要在传递部署 Web 服务时创建的 API 密钥。 Web 服务基于 REST，流行体系结构的 web 编程项目。

若要了解如何将模型部署，请参阅[教程：部署机器学习模型与可视界面](ui-tutorial-automobile-price-deploy.md)。

## <a name="next-steps"></a>后续步骤

* 了解基础知识的预测分析和机器学习与[快速入门：准备和可视化数据而无需在 Azure 机器学习中编写代码](ui-quickstart-run-experiment.md)。
* 使用其中一个示例，并修改套件为你的需求：
    * [示例 1-回归：预测价格](ui-sample-regression-predict-automobile-price-basic.md)
    * [示例 2-回归：预测价格和比较算法](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
    * [示例 3-分类：预测信用风险](ui-sample-classification-predict-credit-risk-basic.md)
    * [示例 4-分类：预测信用风险 （成本敏感）](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
    * [示例 5-分类：预测流失情况、 亲和力，和向上销售](ui-sample-classification-predict-churn.md)
