---
title: 决策林回归：模块参考
titleSuffix: Azure Machine Learning
description: 了解如何使用 Azure 机器学习中的“决策林回归”模块基于决策树的集成创建回归模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 63d90a5239e6bf350d8a6b66f35157e4c7d15aee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456533"
---
# <a name="decision-forest-regression-module"></a>“决策林回归”模块

本文介绍 Azure 机器学习设计器（预览版）中的一个模块。

使用此模块，可以基于决策树的集成创建回归模型。

配置模型后，必须使用带标签的数据集和[训练模型](./train-model.md)模块来训练模型。 然后，可以使用训练后的模型进行预测。 

## <a name="how-it-works"></a>工作原理

决策树是非参数模型，可对每个实例执行一系列简单测试，遍历二元树数据结构，直至到达叶节点（决策）。

决策树具有以下优点：

- 在训练和预测期间的计算和内存使用效率都很高。

- 可以表示非线性决策边界。

- 它们执行集成的特征选择和分类，并且在存在干扰性特征时具有弹性。

此回归模型由决策树的集成组成。 回归决策林中的每个树都将高斯分布输出为预测。 聚合是通过树的集成执行的，用于查找与模型中所有树的组合分布最接近的高斯分布。

有关此算法的理论框架及其实现的详细信息，请参阅本文：[决策林：分类、回归、密度估计、多管理系统学习和半监督学习的统一框架](https://www.microsoft.com/en-us/research/publication/decision-forests-a-unified-framework-for-classification-regression-density-estimation-manifold-learning-and-semi-supervised-learning/?from=http%3A%2F%2Fresearch.microsoft.com%2Fapps%2Fpubs%2Fdefault.aspx%3Fid%3D158806#)

## <a name="how-to-configure-decision-forest-regression-model"></a>如何配置决策林回归模型

1. 将**决策林回归**模块添加到管道。 可以在设计器中在“机器学习”****、“初始化模型”**** 和“回归”**** 下找到此模块。

2. 打开模块属性，对于“重新采样方法”，请选择用于创建各个树的方法****。  你可以选择“装袋”或“复制”。********

    - **装袋**： 装袋也称为*引导*聚合。 回归决策林中的每个树都将高斯分布输出为预测。 聚合的目的是找到这样一个高斯分布：其前两个矩与通过组合各个树返回的所有分布来形成的混合高斯分布的矩相匹配。

         有关详细信息，请参阅[Bootstrap 聚合](https://wikipedia.org/wiki/Bootstrap_aggregating)的维基百科条目。

    - **复制**：在复制中，每个树都接受完全相同的输入数据的培训。 确定每个树节点使用哪个拆分谓词仍然是随机的，并且树将多种多样。

         有关使用**复制**选项的培训过程的详细信息，请参阅[计算机视觉和医疗图像分析的决策林。克里米尼西和J.肖特顿斯普林格2013.](https://research.microsoft.com/projects/decisionforests/).

3. 通过设置“创建训练器模式”选项来指定如何训练模型。****

    - **单一参数**

      如果知道自己想要如何配置模型，可以提供一组特定的值作为参数。 你可能已通过试验了解了这些值，或者已采用它们作为指南。

    - **参数范围**：如果不确定最佳参数，并且想要运行参数扫描，请选择此选项。 选择要迭代的值范围，[调谐模型 Hyper 参数](tune-model-hyperparameters.md)迭代您提供的所有可能组合设置，以确定生成最佳结果的超参数。 



4. 对于“决策树数目”****，请指定要在集成中创建的决策树的总数。 通过创建更多决策树，你可能会获得更好的覆盖范围，但训练时间将会增加。

    > [!TIP]
    > 此值还控制对训练后的模型进行可视化时显示的树的数量。 如果希望查看或打印单个树，可将该值设置为 1；但是，这意味着只会生成一个树（带有初始参数集的树），而不会执行进一步的迭代。

5. 对于“决策树的最大深度”****，请键入一个数字，以限制任何决策树的最大深度。 增加树的深度可能会提高精度，但存在过度拟合和训练时间增加的风险。

6. 对于“每个节点的随机拆分数”****，键入在生成树的每个节点时要使用的拆分数。 拆分意味着对树的每个级别（节点）的要素进行随机划分**。

7. 对于“每个叶节点的最少样本数”****，指定在树中创建任何终端节点（叶）所需的最小事例数。

     通过增加此值，可以增加创建新规则的阈值。 例如，使用默认值 1 时，即使是单个案例也可以导致创建新规则。 如果将值增加到 5，则训练数据将必须包含至少五个满足相同条件的事例。


9. 连接一个带标签的数据集，选择包含不超过两个结果的单个标签列，并连接到[训练模型](./train-model.md)。

    - 如果将“创建训练程序模式”选项设置为“单个参数”，请使用[训练模型](./train-model.md)模块来训练模型********。

   

10. 提交管道。

### <a name="results"></a>结果

训练完成后：

+ 要保存已定型模型的快照，请选择训练模块，然后切换到右侧面板中的 **"输出"** 选项卡。 单击图标**注册模型**。  您可以在模块树中找到已保存的模型作为模块。 

## <a name="next-steps"></a>后续步骤

参阅 Azure 机器学习[可用的模块集](module-reference.md)。 