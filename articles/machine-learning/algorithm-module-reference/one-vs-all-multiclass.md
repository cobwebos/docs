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
ms.openlocfilehash: 5c59f2865e7ebf768cdd8b80e59d69359f8607c6
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/07/2019
ms.locfileid: "73717194"
---
# <a name="one-vs-all-multiclass"></a>“一对多”多类分类

本文介绍如何使用 Azure 机器学习设计器（预览版）中的“一对多”多类分类模块。 目标是使用*一种与所有*方法创建可预测多个类的分类模型。

当结果依赖于连续或分类预测器变量时，此模块可用于创建预测三个或更多可能的结果的模型。 此方法还允许对需要多个输出类的问题使用二进制分类方法。

### <a name="more-about-one-versus-all-models"></a>有关一对多模型的详细信息

某些分类算法允许在设计中使用两个以上的类。 其他人将可能的结果限制为两个值中的一个（二进制或双类模型）。 但甚至可以通过各种策略来改编多类分类任务的二进制分类算法。 

此模块实现了一种与全部方法，在此方法中，为每个输出类创建一个二进制模型。 该模块针对各个类的补码（模型中的所有其他类）评估每个这些二进制模型，就像它是一个二元分类问题一样。 然后，该模块运行这些二进制分类器并选择具有最高置信分数的预测，从而执行预测。  

从本质上讲，该模块创建了单独的模型的系综，然后合并了结果，以创建预测所有类的单个模型。 任何二元分类器均可用作一对一模型的基础。  

例如，假设您配置一个[双类支持向量机](two-class-support-vector-machine.md)模型，并将其作为输入提供给“一对多”多类分类模块。 该模块将为输出类的所有成员创建双类支持向量机模型。 然后，它将应用一种与全部方法，以合并所有类的结果。  

## <a name="how-to-configure-the-one-vs-all-multiclass-classifier"></a>如何配置“一对多”多类分类分类器  

此模块创建系综的二元分类模型来分析多个类。 若要使用此模块，需要先配置并训练*二元分类*模型。 

将二进制模型连接到“一对多”多类分类模块。 然后，通过将[定型模型](train-model.md)与带标签的定型数据集结合使用来定型模型的系综。

组合模型时，“一对多”多类分类会创建多个二进制分类模型，为每个类优化算法，然后合并模型。 即使定型数据集可以有多个类值，模块也会执行这些任务。

1. 在设计器中将“一对多”多类分类模块添加到管道。 您可以在 "**分类**" 类别中**机器学习初始化**下找到此模块。

   “一对多”多类分类分类器没有自己的可配置参数。 所有自定义项都必须在作为输入提供的二进制分类模型中完成。

2. 将二元分类模型添加到管道，并配置该模型。 例如，您可以使用[双类支持向量机](two-class-support-vector-machine.md)或[双类提升决策树](two-class-boosted-decision-tree.md)。

3. 将[训练模型](train-model.md)模块添加到管道。 连接作为“一对多”多类分类的输出的未训练的分类器。

4. 在[训练模型](train-model.md)的其他输入上，连接包含多个类值的标记定型数据集。

5. 运行管道。

## <a name="results"></a>结果

训练完成后，可以使用模型进行多类预测。

另外，还可以将未训练的分类器传递给[交叉验证模型](cross-validate-model.md)，以针对标记的验证数据集进行交叉验证。


## <a name="next-steps"></a>后续步骤

查看可用于 Azure 机器学习服务[的模块集](module-reference.md)。 
