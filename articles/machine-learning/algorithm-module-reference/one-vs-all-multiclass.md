---
title: “一对多”多类分类
titleSuffix: Azure Machine Learning service
description: 了解如何使用 Azure 机器学习服务中的“一对多”多类分类模块，通过二元分类模型的系综创建多类分类模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/16/2019
ms.openlocfilehash: 9ded83dc5b8b8b98af66c8858214e8f82a4aa166
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73518159"
---
# <a name="one-vs-all-multiclass"></a>“一对多”多类分类

本文介绍如何使用 **“一对多”多类分类**module azure 机器学习设计器（预览）来创建可使用 "一种与全部" 方法预测多个类的分类模型。

当结果依赖于连续或分类预测器变量时，此模块可用于创建预测三个或更多可能的结果的模型。 此方法还允许对需要多个输出类的问题使用二进制分类方法。

### <a name="more-about-one-vs-all-models"></a>有关一对多模型的详细信息

尽管某些分类算法允许按设计使用两个以上的类，但其他一些则它们会将可能的结果限制为两个值中的一个（二进制或双类模型）。 但是，甚至可以使用各种策略来改编多类分类任务的二进制分类算法。 

此模块实现了*一种与 all 方法*，其中为每个输出类创建一个二进制模型。 针对各个类的每个二进制模型都针对其补数（模型中的所有其他类）进行评估，就像它是一个二元分类问题一样。 然后，通过运行这些二进制分类器并选择具有最高置信度分数的预测来执行预测。  

从本质上讲，将创建单个模型的系综，然后合并结果，以创建预测所有类的单个模型。 因此，任何二进制分类器均可用作一对一模型的基础。  

 例如，假设您配置一个[双类支持向量机](two-class-support-vector-machine.md)模型，并将其作为输入提供给 **“一对多”多类分类**模块。 该模块将为输出类的所有成员创建双类支持向量机模型，然后应用一种 vs-all 方法来合并所有类的结果。  

## <a name="how-to-configure-the-one-vs-all-classifier"></a>如何配置一对一分类器  

此模块创建系综的二元分类模型来分析多个类。 因此，若要使用此模块，需要先配置并训练**二元分类**模型。 

然后，将二进制模型连接到 **“一对多”多类分类**模块，并通过将[定型模型](train-model.md)与带标签的定型数据集结合使用来定型模型的系综。

当您组合模型时，即使定型数据集可能具有多个类值， **“一对多”多类分类**会创建多个二进制分类模型，为每个类优化算法，然后合并这些模型。

1. 在设计器中将 **“一对多”多类分类**添加到管道。 您可以在 "**分类**" 类别中机器学习初始化下找到此模块。

    **“一对多”多类分类**分类器没有自己的可配置参数。 所有自定义都必须在作为输入提供的二进制分类模型中完成。

2. 将二元分类模型添加到管道，并配置该模型。 例如，可以使用[双类支持向量机](two-class-support-vector-machine.md)或[双类提升决策树](two-class-boosted-decision-tree.md)。

3. 将[训练模型](train-model.md)模块添加到管道，并连接作为 **“一对多”多类分类**的输出的未训练的分类器。

4. 在[训练模型](train-model.md)的其他输入上，连接包含多个类值的标记定型数据集。

5. 运行管道。

## <a name="results"></a>结果

训练完成后，可以使用模型进行多类预测。

或者，您可以将未训练的分类器传递给[交叉验证模型](cross-validate-model.md)，以便对标记的验证数据集进行交叉验证。


## <a name="next-steps"></a>后续步骤

查看可用于 Azure 机器学习服务[的模块集](module-reference.md)。 
