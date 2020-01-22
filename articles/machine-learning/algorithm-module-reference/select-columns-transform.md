---
title: 选择列转换：模块引用
titleSuffix: Azure Machine Learning
description: 了解如何使用 Azure 机器学习中的 "选择列" 转换模块来创建一个转换，该转换选择与给定数据集中相同的列子集。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: c8d58180b11c12afb256dc888406c7c0d58fb119
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2020
ms.locfileid: "76314311"
---
# <a name="select-columns-transform"></a>选择列转换

本文介绍如何使用 Azure 机器学习设计器中的 "选择列转换" 模块。 "选择列" 转换模块的用途是确保下游机器学习操作中使用可预测、一致的一组列。

此模块对于需要特定列的任务（如评分）非常有用。 可用列中的更改可能会中断管道或更改结果。

使用 "选择列" 转换创建并保存一组列。 然后，使用 "[应用转换](apply-transformation.md)" 模块将这些选择应用于新数据。

## <a name="how-to-use-select-columns-transform"></a>如何使用 "选择列" 转换

此方案假设您要使用特征选择来生成将用于定型模型的动态列集。 若要确保列选择对于计分过程是相同的，可以使用 "选择列" 转换模块来捕获列选择，并将其应用于管道中的其他位置。

1. 在设计器中将输入数据集添加到管道。

2. 添加[基于筛选器的功能选择](filter-based-feature-selection.md)的实例。

3. 连接模块并配置功能选择模块，以便在输入数据集中自动查找许多最佳功能。

4. 添加[定型模型](train-model.md)的实例，并使用[基于筛选器的功能选择](filter-based-feature-selection.md)的输出作为定型输入。

    > [!IMPORTANT]
    > 由于特征重要性基于列中的值，因此您无法事先知道哪些列可能可用于[训练模型](train-model.md)。  
5. 附加 "选择列" 转换模块的实例。 

    此步骤将列选择作为转换生成，可将其保存或应用到其他数据集。 此步骤可确保保存在功能选择中标识的列，以便重复使用其他模块。

6. 添加 "[评分模型](score-model.md)" 模块。 

   *请勿连接输入数据集。* 相反，请添加 "[应用转换](apply-transformation.md)" 模块，并连接功能选择转换的输出。

   > [!IMPORTANT]
   > 不能将[基于筛选器的功能选择](filter-based-feature-selection.md)应用于计分数据集并获得相同的结果。 由于功能选择基于值，因此它可能会选择一组不同的列，这将导致评分操作失败。
7. 运行管道。

此保存和应用列选择的过程可确保相同的数据架构可用于定型和评分。


## <a name="next-steps"></a>后续步骤

查看可用于 Azure 机器学习[的模块集](module-reference.md)。 
