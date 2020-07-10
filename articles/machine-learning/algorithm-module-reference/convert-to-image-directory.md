---
title: 转换为图像目录
titleSuffix: Azure Machine Learning
description: 了解如何使用“转换为图像目录”模块将数据集转换为图像目录格式。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/26/2020
ms.openlocfilehash: b29b5fa1beb19bc055f94c56b064ae2c0ae175b5
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2020
ms.locfileid: "86171136"
---
# <a name="convert-to-image-directory"></a>转换为图像目录

本文介绍如何使用 "转换为映像目录" 模块来帮助将图像数据集转换为 "Image Directory" 数据类型，该数据类型是图像相关任务中的标准化数据格式，如 Azure 机器学习设计器中的图像分类 (预览版) 。

## <a name="how-to-use-convert-to-image-directory"></a>如何使用“转换为图像目录”模块  

1.  将“转换为图像目录”**** 模块添加到试验中。 可以在模块列表的“计算机视觉/图像数据转换”类别中找到此模块。 

2.  [注册图像数据集](https://docs.microsoft.com/azure/machine-learning/how-to-create-register-datasets)，并将其连接到模块输入端口。 请确保输入数据集中有图像。 
    支持以下数据集格式：

    - 具有以下扩展名的压缩文件：“.zip”、“.tar”、“.gz”、“.bz2”。
    - 包含图像的文件夹。 **强烈建议先压缩此类文件夹，然后使用压缩文件作为数据集**。

    > [!WARNING]
    > 您**无法**使用 "**导入数据**" 模块导入图像数据集，因为**导入数据**模块的输出类型为数据帧 Directory，其中仅包含文件路径字符串。
    

    > [!NOTE]
    > 如果在监督式学习中使用映像数据集，则需要标签。
    > 对于图像分类任务，如果此图像数据集以 torchvision ImageFolder 格式进行组织，则可以在模块输出中生成图像 "category" 标签。 否则，仅保存图像而不保存标签。 下面是一个示例，说明如何组织图像数据集以获取标签，并使用 image category 作为子文件夹名称。 有关详细信息，请参阅[torchvision 数据集](https://pytorch.org/docs/stable/torchvision/datasets.html#imagefolder)。
    >
    > ```
    > root/dog/xxx.png
    > root/dog/xxy.png
    > root/dog/xxz.png
    >
    > root/cat/123.png
    > root/cat/nsdf3.png
    > root/cat/asd932_.png
    > ```

3.  提交管道。

## <a name="results"></a>结果

**转换为图像目录**模块的输出为图像目录格式，可连接到其他与图像相关的模块，其中输入端口格式也是图像目录。

## <a name="technical-notes"></a>技术说明 

###  <a name="expected-inputs"></a>预期输入  

| 名称          | 类型                  | 说明   |
| ------------- | --------------------- | ------------- |
| 输入数据集 | AnyDirectory、ZipFile | 输入数据集 |

###  <a name="output"></a>输出  

| 名称                   | 类型           | 说明            |
| ---------------------- | -------------- | ---------------------- |
| 输出图像目录 | ImageDirectory | 输出图像目录 |

## <a name="next-steps"></a>后续步骤

请参阅 Azure 机器学习的[可用模块集](module-reference.md)。 
