---
title: 自动 ML 算法选择和优化
titleSuffix: Azure Machine Learning service
description: 了解 Azure 机器学习服务如何自动选取算法，以及如何通过它来生成模型，以便使用你提供的参数和条件为模型选择最佳算法，从而节省时间。
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
author: nacharya1
ms.author: nilesha
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 7e55304fb02078c71b91b2904475a4bee4bcc018
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/15/2018
ms.locfileid: "53436953"
---
# <a name="what-is-automated-machine-learning"></a>什么是自动化机器学习？

自动化机器学习是这样一个过程：获取带有定义的目标特征的训练数据，然后以迭代方式将算法和特征选择组合在一起，以便根据训练分数自动为数据选择最佳模型。 传统的机器学习模型开发过程是资源高度密集型的，需要大量的领域知识和时间投资来运行数十个模型并比较其结果。 自动化机器学习简化了此过程，其方法是基于你为试验定义的目标和约束（例如要运行的试验的时间或者要加入方块列表的模型）生成优化的模型。

## <a name="how-it-works"></a>工作原理

1. 你可以配置要尝试解决的机器学习问题的类型。 支持多个类别的监督式学习：
   + 分类
   + 回归
   + 预测

   自动化机器学习已推出正式版，不过，**预测功能仍以公共预览版提供。**

   请参阅 Azure 机器学习在培训时可以尝试的[模型列表](how-to-configure-auto-train.md#select-your-experiment-type)。

1. 指定培训数据的源和格式。 数据必须进行标记，并可存储在开发环境或 Azure Blob 存储中。 如果数据存储在开发环境中，则它必须与培训脚本位于同一目录中。 此目录将复制到为培训选择的计算目标。

    在培训脚本中，数据可以读取到 Numpy 数组或 Pandas 数据帧中。

    你可以配置拆分选项以选择培训和验证数据，也可以指定单独的培训和验证数据集。

1. 配置用于定型模型的[计算目标](how-to-set-up-training-targets.md)。

1. 配置自动化机器学习配置。 这可控制 Azure 机器学习循环访问不同模型时所使用的参数、超参数设置，以及确定最佳模型时要查看的指标 

1. 提交培训运行。

在培训期间，Azure 机器学习服务会创建多个尝试不同算法和参数的管道。 一旦它达到提供的迭代限制，或达到所指定指标的目标值时，它将停止。

[ ![自动化机器学习](./media/how-to-automated-ml/automated-machine-learning.png) ](./media/how-to-automated-ml/automated-machine-learning.png#lightbox)

你可以检查记录的运行信息，其中包含运行期间收集的指标。 训练运行还会生成一个包含模型和数据预处理的 Python 序列化对象（`.pkl` 文件）。

## <a name="model-explainability"></a>模型可说明性

自动化机器学习的常见问题是不能看到端到端过程。 可以通过 Azure 机器学习详细了解这些模型，更好地了解在后端运行的内容。 某些模型（例如线性回归）被认为是相当直观的模型，因此容易理解。 但是，随着添加的功能越来越多，使用的机器学习模型越来越复杂，理解它们也越来越困难。 了解机器学习时有两个关键的方面：

1. 了解机器学习管道以及所有相关的步骤，包括数据预处理/特征化和超参数值。
1. 了解输入变量（也称为“特征”）和模型输出之间的关系。  了解每个特征对预测值的影响大小和方向可以更好地了解和解释模型。 这称为特征重要性。

可以在训练后根据需要为所选管道启用全局特征重要性，也可以在自动化 ML 训练过程中为所有管道启用它。  这是一项预览版功能，我们会继续提供越来越丰富的信息，让你更好地了解 ML 模型。  

按此[示例笔记本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/model-explanation/auto-ml-model-explanation.ipynb)操作，在 Azure 机器学习中试验模型解释。

## <a name="next-steps"></a>后续步骤

查看示例并了解如何使用自动化机器学习构建模型：

+ [教程：使用 Azure 自动机器学习自动训练分类模型](tutorial-auto-train-models.md)

+ [为自动培训配置设置](how-to-configure-auto-train.md)

+ [在远程资源上使用自动训练](how-to-auto-train-remote.md) 
