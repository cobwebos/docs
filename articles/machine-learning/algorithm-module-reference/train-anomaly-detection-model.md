---
title: 列车异常检测模型：模块参考
titleSuffix: Azure Machine Learning
description: 了解如何使用训练异常检测模型模块创建经过训练的异常检测模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 78ed2d85729cce94e8dfa579545f558d2cfe4651
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502966"
---
# <a name="train-anomaly-detection-model"></a>训练异常检测模型

本文介绍如何使用 Azure 机器学习设计器（预览）中的**训练异常检测模型**模块创建经过训练的异常检测模型。

该模块将异常检测模型和未标记数据集的一组模型参数作为输入。 它返回经过训练的异常检测模型以及一组训练数据的标签。  

有关设计器中提供的异常检测算法的详细信息，请参阅以下主题： 

+ [基于 PCA 的异常检测](pca-based-anomaly-detection.md)  

## <a name="how-to-configure-train-anomaly-detection-model"></a>如何配置列车异常检测模型 

1.  在设计器中将**训练异常检测模型**模块添加到管道中。 您可以在**异常检测**类别中找到此模块。

2. 连接专为异常检测而设计的模块之一，例如[基于 PCA 的异常检测](pca-based-anomaly-detection.md)

    不支持其他类型的模型;运行管道时，您会收到错误：所有模型必须具有相同的学员类型。  

3.  通过选择标签列并设置特定于算法的其他参数来配置异常检测模块。  

4.  将训练数据集附加到**列车异常检测模型**的右侧输入。  

5.  提交管道。  

## <a name="results"></a>结果

训练完成后：

+ 要查看模型的参数，请右键单击模块并选择 **"可视化**"。 

+ 要创建预测，请使用具有新输入数据[的分数模型](score-model.md)。

+ 要保存已训练的模型的快照，请选择模块，然后单击右侧面板中的 **"输出_logs"** 选项卡下的 **"注册"数据集**图标。   

 
## <a name="next-steps"></a>后续步骤

参阅 Azure 机器学习[可用的模块集](module-reference.md)。 

有关特定于设计器模块的错误列表[，请参阅设计器的异常和错误代码（预览](designer-error-codes.md)）。
'