---
title: 可视界面
titleSuffix: Azure Machine Learning
description: 了解构成 Azure 机器学习的可视化界面（预览）的术语、概念和工作流。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 05/15/2019
ms.openlocfilehash: f560887a48ce4754b26a54ef0e18093c5577af34
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/19/2019
ms.locfileid: "71128802"
---
# <a name="what-is-the-visual-interface-for-azure-machine-learning"></a>什么是用于 Azure 机器学习的视觉对象接口？ 

利用的视觉界面（预览 Azure 机器学习），可以在不编写代码的情况下准备数据、定型、测试、部署、管理和跟踪机器学习模型。

无需编程，您可以直观地连接[数据集](#dataset)和[模块](#module)来构造模型。

视觉对象接口使用 Azure 机器学习[工作区](concept-workspace.md)执行以下操作：

+ 将[试验](#experiment)的项目写入工作区。
+ 访问[数据集](#dataset)。
+ 使用工作区中的[计算资源](#compute)运行试验。 
+ 注册[模型](concept-azure-machine-learning-architecture.md#models)。
+ 将模型作为 web 服务[部署](#deployment)到工作区中的计算资源。

![视觉对象界面概述](media/ui-concept-visual-interface/overview.png)

## <a name="workflow"></a>工作流

视觉对象界面提供交互式的可视画布，可快速生成、测试和循环访问模型。 

+ 将[模块](#module)拖放到画布上。
+ 将模块连接在一起以形成[试验](#experiment)。
+ 使用机器学习服务工作区的计算资源运行试验。
+ 通过编辑试验并再次运行来循环访问模型设计。
+ 准备就绪后，将**训练实验**转换为**预测实验**。
+ 将预测实验[部署](#deployment)为 web 服务，以便其他人可以访问模型。

## <a name="experiment"></a>试验

从头开始创建试验，或使用现有的示例试验作为模板。  每次运行试验时，项目都存储在工作区中。

试验包含数据集和分析模块，它们连接在一起以构造模型。 具体而言，有效的试验有以下特征：

* 数据集只能连接到模块。
* 模块可以连接到数据集或其他模块。
* 模块的所有输入端口都必须连接到数据流。
* 必须设置每个模块的所有必需参数。


若要了解如何开始处理视觉对象界面，请参阅[教程：使用可视界面预测汽车价格](ui-tutorial-automobile-price-train-score.md)。

## <a name="dataset"></a>数据集

数据集是已上传到可视界面以在建模过程中使用的数据。 其中包含了许多示例数据集供您试验，可以根据需要上传更多的数据集。

## <a name="module"></a>模块

模块是可对数据执行的算法。 视觉对象接口具有多个模块，从数据入口函数到定型、评分和验证过程。

模块可能提供一组参数用于配置模块的内部算法。 选择画布上的模块时，模块的参数将显示在画布右侧的 "属性" 窗格中。 可以在该窗格中修改参数来调整模型。

![模块属性](media/ui-concept-visual-interface/properties.png)

有关在可用的机器学习算法库中导航的帮助，请参阅[算法 & 模块参考概述](../algorithm-module-reference/module-reference.md)

## <a name="compute"></a>计算资源

使用工作区中的计算资源运行试验，或将已部署的模型托管为 web 服务。 支持的计算目标为：


| 计算目标 | 培训 | 部署 |
| ---- |:----:|:----:|
| Azure 机器学习计算 | ✓ | |
| Azure Kubernetes 服务 | | ✓ |

计算目标会附加到机器学习[工作区](concept-workspace.md)。 可在工作区中的[Azure 门户](https://portal.azure.com)或[工作区登陆页面（预览版）](https://ml.azure.com)中管理计算目标。

## <a name="deployment"></a>部署

预测分析模型准备就绪后，可直接从可视界面将其部署为 web 服务。

Web 服务在应用程序和评分模型之间提供了一个接口。 外部应用程序可与评分模型实时通信。 对 web 服务的调用会将预测结果返回到外部应用程序。 若要调用 Web 服务，需要在传递部署 Web 服务时创建的 API 密钥。 Web 服务基于 REST，这是 web 编程项目的常用体系结构选择。

若要了解如何部署模型，请参阅[教程：使用可视界面](ui-tutorial-automobile-price-deploy.md)部署机器学习模型。

## <a name="next-steps"></a>后续步骤

* 通过[教程了解有关预测分析和机器学习的基本知识：使用可视界面预测汽车价格](ui-tutorial-automobile-price-train-score.md)
* 使用其中一个示例并进行修改以满足你的需求：
    * [示例 1-回归：预测价格](how-to-ui-sample-regression-predict-automobile-price-basic.md)
    * [示例 2-回归：预测价格和比较算法](how-to-ui-sample-regression-predict-automobile-price-compare-algorithms.md)
    * [示例 3-分类：预测信用风险](how-to-ui-sample-classification-predict-credit-risk-basic.md)
    * [示例 4-分类：预测信用风险（区分成本）](how-to-ui-sample-classification-predict-credit-risk-cost-sensitive.md)
    * [示例 5-分类：预测变动率、亲和力和向上销售](how-to-ui-sample-classification-predict-churn.md)
