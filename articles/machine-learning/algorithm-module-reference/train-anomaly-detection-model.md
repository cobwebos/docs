---
title: 训练异常情况检测模型：模块参考
titleSuffix: Azure Machine Learning
description: 了解如何使用“训练异常情况检测模型”模块创建已训练的异常情况检测模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: edf35fada4233fbe43bc7f859c2414bfb8130714
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90905731"
---
# <a name="train-anomaly-detection-model-module"></a>“训练异常情况检测模型”模块

本文介绍如何使用 Azure 机器学习设计器中的 "训练异常检测模型" 模块来创建定型的异常情况检测模型。

该模块将异常情况检测模型和未标记数据集的一组参数作为输入。 它将返回已训练的异常情况检测模型，以及训练数据的一组标签。  

有关设计器中提供的异常情况检测算法的详细信息，请参阅[基于 PCA 的异常情况检测](pca-based-anomaly-detection.md)。  

## <a name="how-to-configure-train-anomaly-detection-model"></a>如何配置“训练异常情况检测模型”模块 

1.  在设计器中向管道添加“训练异常情况检测模型”模块****。 可以在“异常情况检测”类别中找到此模块****。

2. 连接设计用于异常情况检测的模块之一，例如[基于 PCA 的异常情况检测](pca-based-anomaly-detection.md)。

    不支持其他类型的模型。 运行管道时，会出现“所有模型都必须具有相同的学习器类型”错误。  

3.  通过选择标签列并设置特定于算法的其他参数来配置异常情况检测模块。  

4.  将训练数据集附加到“训练异常情况检测模型”**** 的右侧输入。  

5.  提交管道。  

## <a name="results"></a>结果

在训练完成后：

+ 若要查看模型的参数，请右键单击模块，并选择“可视化”。**** 

+ 若要创建预测，请对新输入数据使用[评分模型](score-model.md)模块。

+ 若要保存已训练模型的快照，请选择该模块。 然后，在右侧面板中选择“输出 + 日志”**** 选项卡下的“注册数据集”**** 图标。   

 
## <a name="next-steps"></a>后续步骤

请参阅 Azure 机器学习的[可用模块集](module-reference.md)。 

有关特定于设计器模块的错误列表，请参阅 [设计器的异常和错误代码](designer-error-codes.md) 。
'