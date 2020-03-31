---
title: 决策林回归：模块参考
titleSuffix: Azure Machine Learning
description: 了解如何在 Azure 机器学习中使用双类平均感知器模块，以便根据平均感知器算法创建机器学习模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 73e23dd7d350ea63e9fd8b933a525a9d8aad9e3e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77920767"
---
# <a name="two-class-averaged-perceptron-module"></a>双类平均感知器模块

本文介绍 Azure 机器学习设计器（预览版）中的一个模块。

使用此模块，可以根据平均感知器算法创建机器学习模型。  
  
此分类算法是一种监督式学习方法，需要一个标记的数据集（其中包含标签列）。** 可以对模型进行训练，只需提供模型和标记的数据集作为[训练模型](./train-model.md)的输入， 然后即可使用训练的模型来预测新输入示例的值。  

### <a name="about-averaged-perceptron-models"></a>关于平均感知器模型

平均感知器方法是神经网络的早期简单版本。** 在此方法中，我们根据一个线性函数将输入分类为多个可能的输出，然后将其与一组派生自特征矢量的权重相结合，这也是“感知器”这一名称的由来。

较简单的感知器模型适用于学习线性可分模式，而神经网络（尤其是深度神经网络）则可对较复杂的类边界建模。 但是，感知器速度更快，并且由于它们是按顺序处理案例的，因此可以将它们用于连续训练。

## <a name="how-to-configure-two-class-averaged-perceptron"></a>如何配置双类平均感知器

1.  向管道添加“双类平均感知器”**** 模块。  

2.  通过设置“创建训练器模式”选项来指定如何训练模型。****  
  
    -   **单一参数**：如果您知道如何配置模型，请提供一组特定的值作为参数。

    -   **参数范围**：如果不确定最佳参数，并且想要运行参数扫描，请选择此选项。 选择要迭代的值范围，[调谐模型 Hyper 参数](tune-model-hyperparameters.md)迭代您提供的所有可能组合设置，以确定生成最佳结果的超参数。  
  
3.  对于“学习速率”，请指定一个值作为“学习速率”。****** 学习速率值控制每次对模型进行测试和纠正时用于随机梯度下降法的梯度的大小。
  
     降低该速率可以加快模型测试频率，但风险是可能会在局部出现拟合效果改进缓慢的情况。 加大梯度可以加快聚合速度，但风险是可能会错过真正的最小值。
  
4.  对于“最大迭代次数”，请键入你希望算法检查训练数据的次数。****  
  
     早停止通常提供更好的通用化。 提高迭代次数可以改进拟合情况，风险是过度拟合。
  
5.  对于“随机数种子”，可以选择键入一个整数值，将其用作种子。**** 如果需要跨运行确保管道的可再现性，建议使用种子。  
  
1.  连接一个训练数据集以及训练模块之一：
  
    -   如果将“创建训练程序模式”设置为“单个参数”，请使用[训练模型](train-model.md)模块。********




## <a name="next-steps"></a>后续步骤

参阅 Azure 机器学习[可用的模块集](module-reference.md)。 