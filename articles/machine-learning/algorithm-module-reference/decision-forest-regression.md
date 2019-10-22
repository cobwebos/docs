---
title: 决策林回归：模块引用
titleSuffix: Azure Machine Learning service
description: 了解如何使用 Azure 机器学习服务中的 "决策林回归" 模块根据决策树的系综创建回归模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: 7b89d08f4621ecde77a60510b05d96decff0cfde
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693173"
---
# <a name="decision-forest-regression-module"></a>决策林回归模块

本文介绍了 Azure 机器学习服务的可视界面（预览）的模块。

使用此模块可以基于决策树的系综创建回归模型。

配置该模型后，必须使用标记的数据集和[训练模型](./train-model.md)模块来训练该模型。  然后，可以使用训练后的模型进行预测。 

## <a name="how-it-works"></a>如何运作

决策树是非参数模型，可对每个实例执行一系列简单测试，遍历二进制树数据结构，直到到达叶节点（决策）。

决策树具有以下优势：

- 在训练和预测期间，它们在计算和内存使用中都有效。

- 它们可以表示非线性决策边界。

- 它们执行集成的特征选择和分类，并在存在干扰性功能时提供弹性。

此回归模型由决策树的系综组成。 回归决策林中的每个树都将高斯分布输出为预测。 聚合是通过系综的树执行的，用于查找与模型中所有树的组合分布最接近的高斯分布。

有关此算法及其实现的理论框架的详细信息，请参阅此文章：[决策林：用于分类、回归、密度估算、复写体分段学习和半监督学习的统一框架](https://www.microsoft.com/en-us/research/publication/decision-forests-a-unified-framework-for-classification-regression-density-estimation-manifold-learning-and-semi-supervised-learning/?from=http%3A%2F%2Fresearch.microsoft.com%2Fapps%2Fpubs%2Fdefault.aspx%3Fid%3D158806#)

## <a name="how-to-configure-decision-forest-regression-model"></a>如何配置决策林回归模型

1. 将 "**决策林回归**" 模块添加到管道。 可以在接口中的 "**机器学习**"、"**初始化模型**" 和 "**回归**" 下找到该模块。

2. 打开 "模块属性"，为 "重新**采样" 方法**选择用于创建单个树的方法。  可以从**装袋**或**复制**中进行选择。

    - **装袋**：装袋也称为*启动聚合*。 回归决策林中的每个树都通过预测方式输出高斯分布。 聚合将查找其前两分钟与由单个树返回的所有 Gaussians 组合在一起的 Gaussians 混合时间的高斯。

         有关详细信息，请参阅用于[启动聚合](https://wikipedia.org/wiki/Bootstrap_aggregating)的维基百科条目。

    - **复制**：在复制中，每个树都在完全相同的输入数据上定型。 确定将哪个拆分谓词用于每个树节点会保持不变，并且树将是不同的。

         有关 "**复制**" 选项的训练过程的详细信息，请参阅[计算机视觉和医疗图像分析的决策林。Criminisi 和 Shotton。Springer link 2013.](https://research.microsoft.com/projects/decisionforests/)。

3. 通过设置 "**创建训练人员模式**" 选项，指定要如何定型模型。

    - **单个参数**

      如果你知道要如何配置模型，则可以提供一组特定的值作为参数。 您可能已通过试验或接收这些值作为指导来了解这些值。



4. 对于**决策树的数量**，指示要在系综中创建的决策树的总数。 通过创建更多决策树，你可能获得更好的覆盖范围，但训练时间会增加。

    > [!TIP]
    > 此值还控制在可视化定型模型时显示的树的数目。 如果要查看或打印单个树，可将值设置为 1;但是，这意味着将仅生成一个树（具有初始参数集的树），而不执行进一步的迭代。

5. 有关**决策树的最大深度**，请键入一个数字，以限制任何决策树的最大深度。 增加树的深度可能会提高精度，同时降低某些过度拟合和定型时间。

6. 对于**每个节点的随机拆分数**，请键入生成树的每个节点时要使用的拆分数。 *拆分*意味着将随机划分树的每个级别（节点）中的功能。

7. 对于**每个叶节点的最小样本数**，指示在树中创建任何终端节点（叶）所需的最小事例数。

     通过增加此值，可以增加用于创建新规则的阈值。 例如，默认值为1时，即使单个事例也可能导致创建新规则。 如果将该值增加到5，则定型数据必须至少包含五个满足相同条件的事例。


9. 连接标签的数据集，选择不包含两个以上结果的单个标签列，并连接到[定型模型](./train-model.md)。

    - 如果将 "**创建**训练人员模式" 选项设置为 "**单个参数**"，请使用 "[训练模型](./train-model.md)" 模块来训练该模型。

   

10. 运行管道。

### <a name="results"></a>结果

训练完成后：

+ 若要查看在每次迭代中创建的树，请右键单击定型模块的输出，并选择 "**可视化**"。

+ 若要查看每个节点的规则，请单击每个树并向下钻取拆分。

+ 若要保存训练的模型的快照，请右键单击定型模块的输出，并选择 "**另存为定型模型**"。 此模型副本不会在连续运行管道时进行更新。 

## <a name="next-steps"></a>后续步骤

查看可用于 Azure 机器学习服务[的模块集](module-reference.md)。 