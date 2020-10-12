---
title: 应用图像转换
titleSuffix: Azure Machine Learning
description: 了解如何使用“应用图像转换”模块将图像转换应用到图像目录。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/26/2020
ms.openlocfilehash: a64d5cebfd8e70e2f54a66193a7041c47887c54a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90898908"
---
# <a name="apply-image-transformation"></a>应用图像转换 

本文介绍如何使用 Azure 机器学习设计器中的 "应用图像转换" 模块，根据以前指定的图像转换修改输入图像目录。  

需要连接 [初始化图像转换](init-image-transformation.md)模块来指定转换，然后可以将此类转换应用于“应用图像转换”模块的输入图像目录。

## <a name="how-to-use-apply-image-transformation"></a>如何使用“应用图像转换”  

1. 将“应用图像转换”模块添加到管道。 可以在“计算机视觉/图像数据转换”类别下找到此模块。 

2. 将“初始化图像转换”的输出连接到“应用图像转换”的左侧输入。

     > [!NOTE]
     > 此模块仅接受 [初始化图像转换](init-image-transformation.md)模块生成的图像转换。 对于其他类型的转换，请将其连接到 [应用转换](apply-transformation.md)，否则会引发“InvalidTransformationDirectoryError”。


3. 连接要转换的图像目录。

4. 对于“模式”，请指定你使用输入转换的目的：“用于训练”或“用于推理”。 

   如果选择“用于训练”，则会应用你在“初始化图像转换”中指定的所有转换。

   如果选择“用于推理”，则会在应用之前排除“随机创建新样本”这样的转换。 这是因为在训练过程中，随机创建新样本的转换操作（如“随机水平翻转”）用于数据补充，而在推理过程中，由于需要修复推理样本以进行准确的预测和评估，则应将此类转换删除。

   > [!NOTE]
   > 将在 **用于推理** 模式中排除的转换为：随机重设裁剪大小、随机裁剪、随机水平翻转、随机垂直翻转、随机旋转、随机仿射、随机灰度、随机透视、随机擦除。

5. 若要将图像转换应用于新的图像目录，请提交管道。  

### <a name="module-parameters"></a>模块参数

| 名称 | 范围 | 类型 | 默认                   | 说明                              |
| ---- | ----- | ---- | ------------------------- | ---------------------------------------- |
| Mode | 任意   | Mode | （要求用户指定） | 出于什么目的使用输入转换。 应在推理中排除“随机”转换操作，但在训练中保留这些操作 |

### <a name="expected-inputs"></a>预期输入  

| 名称                       | 类型                    | 说明                       |
| -------------------------- | ----------------------- | --------------------------------- |
| 输入图像转换 | TransformationDirectory | 输入图像转换        |
| 输入图像目录      | ImageDirectory          | 要转换的图像目录 |

### <a name="outputs"></a>Outputs  

| 名称                   | 类型           | 说明            |
| ---------------------- | -------------- | ---------------------- |
| 输出图像目录 | ImageDirectory | 输出图像目录 |

## <a name="next-steps"></a>后续步骤

请参阅 Azure 机器学习的[可用模块集](module-reference.md)。 
