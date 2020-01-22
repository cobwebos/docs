---
title: 决策林回归：模块引用
titleSuffix: Azure Machine Learning
description: 了解如何在 Azure 机器学习中使用双类平均感知器模块来基于平均感知器算法创建机器学习模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: b4180fced60c4a9f2402253c7c3b3d3ff97fe1e1
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2020
ms.locfileid: "76314600"
---
# <a name="two-class-averaged-perceptron-module"></a>双类平均感知器模块

本文介绍 Azure 机器学习设计器中的模块。

使用此模块可以基于平均感知器算法创建机器学习模型。  
  
此分类算法是一种监督的学习方法，需要一个*标记的数据集*，其中包括标签列。 您可以通过提供模型和标记的数据集作为[训练模型](./train-model.md)的输入来对模型进行定型。 然后，可以使用训练的模型来预测新输入示例的值。  

### <a name="about-averaged-perceptron-models"></a>关于平均感知器模型

*平均感知器方法*是神经网络的早期且简单的版本。 在此方法中，根据线性函数将输入分类为多个可能的输出，然后将其与派生自特征向量的一组权重相结合，因此名称为 "感知器"。

更简单的感知器模型适合用于线性学习可分离模式，而神经网络（尤其是深层神经网络）可为更复杂的类边界建模。 但是，感知器的速度更快，由于它们会连续处理用例，因此可用于连续训练。

## <a name="how-to-configure-two-class-averaged-perceptron"></a>如何配置双类平均感知器

1.  将**双类平均感知器**模块添加到管道。  

2.  通过设置 "**创建训练人员模式**" 选项，指定要如何定型模型。  
  
    -   **单个参数**：如果你知道要如何配置模型，请提供一组特定值作为参数。
  
3.  对于 "**学习速率**"，请指定*学习速率*值。 学习速率值控制每次测试和更正模型时在随机梯度下降中使用的步骤的大小。
  
     通过使速率更小，可以更频繁地测试模型，但可能会陷入本地达到平稳。 步长越大，聚合速度较快，而且可能会超过实际最小值。
  
4.  对于 "**最大迭代数**"，键入希望算法检查定型数据的次数。  
  
     早期停止通常会获得更好的正则化性能。 增加迭代次数可改善拟合，但会导致过度拟合风险。
  
5.  对于**随机数种子**，可选择键入要用作种子的整数值。 如果要确保可再现性在运行期间的管道，则建议使用种子。  
  
1.  连接定型数据集和训练模块之一：
  
    -   如果将 "**创建**训练人员模式" 设置为 "**单个参数**"，请使用 "[训练模型](train-model.md)" 模块。




## <a name="next-steps"></a>后续步骤

查看可用于 Azure 机器学习[的模块集](module-reference.md)。 