---
title: 训练异常检测模型：模块引用
titleSuffix: Azure Machine Learning
description: 了解如何使用 "训练异常检测模型" 模块来创建定型的异常情况检测模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 55c5b89eb23581543d57187ee4d7899d97eafe66
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2020
ms.locfileid: "77925951"
---
# <a name="train-anomaly-detection-model"></a>训练异常检测模型

本文介绍如何使用 Azure 机器学习设计器（预览版）中的 "**训练异常检测模型**" 模块来创建定型的异常情况检测模型。

模块将异常检测模型和未标记的数据集的一组模型参数作为输入。 它将返回训练的异常检测模型，以及定型数据的一组标签。  

有关设计器中提供的异常情况检测算法的详细信息，请参阅以下主题： 

+ [基于 PCA 的异常情况检测](pca-based-anomaly-detection.md)  

## <a name="how-to-configure-train-anomaly-detection-model"></a>如何配置训练异常检测模型 

1.  在设计器中将 "**训练异常检测模型**" 模块添加到管道。 可以在**异常情况检测**类别中找到此模块。

2. 连接设计用于异常检测的模块之一，例如[基于 PCA 的异常情况检测](pca-based-anomaly-detection.md)

    不支持其他类型的模型;运行管道时，会出现错误：所有模型必须具有相同的学习器类型。  

3.  通过选择 "标签" 列并设置特定于算法的其他参数，配置异常情况检测模块。  

4.  将定型数据集附加到**训练异常检测模型**的右侧输入。  

5.  运行管道。  

## <a name="results"></a>结果

训练完成后：

+ 若要查看模型的参数，请右键单击模块并选择 "**可视化**"。 

+ 若要创建预测，请将[评分模型](score-model.md)用于新的输入数据。

+ 若要保存训练的模型的快照，请选择该模块，然后单击右侧面板中 "**输出 + 日志**" 选项卡下的 "**注册数据集**" 图标。   

 
## <a name="next-steps"></a>后续步骤

查看可用于 Azure 机器学习[的模块集](module-reference.md)。 

有关特定于设计器模块的错误列表，请参阅[设计器（预览）的异常和错误代码](designer-error-codes.md)。
