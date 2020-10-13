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
ms.date: 10/09/2020
ms.openlocfilehash: 2e597299c9b157d79a5317c97550fc30820636d6
ms.sourcegitcommit: 541bb46e38ce21829a056da880c1619954678586
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2020
ms.locfileid: "91940340"
---
# <a name="convert-to-image-directory"></a>转换为图像目录

本文介绍如何使用转换到映像目录模块来帮助将图像数据集转换为 *图像目录* 数据类型，该数据类型是图像相关任务中的标准化数据格式，如 Azure 机器学习设计器中的图像分类。

## <a name="how-to-use-convert-to-image-directory"></a>如何使用“转换为图像目录”模块  

1. 首先准备映像数据集。 

    对于监督式学习，需要指定定型数据集的标签。 图像数据集文件应采用以下结构：
    
    ```
    Your_image_folder_name/Category_1/xxx.png
    Your_image_folder_name/Category_1/xxy.jpg
    Your_image_folder_name/Category_1/xxz.jpeg
    
    Your_image_folder_name/Category_2/123.png
    Your_image_folder_name/Category_2/nsdf3.png
    Your_image_folder_name/Category_2/asd932_.png
    ```
    
    在 "映像数据集" 文件夹中，有多个子文件夹。 每个子文件夹分别包含一个类别的图像。 子文件夹的名称被视为图像分类等任务的标签。 有关详细信息，请参阅 [torchvision 数据集](https://pytorch.org/docs/stable/torchvision/datasets.html#imagefolder) 。

    > [!WARNING]
    > 设计器不支持从数据标签导出的当前标记的数据集。

    支持具有以下扩展名的图像 (采用小写) ： ".jpg"、"jpeg"、".png"、"ppm"、".bmp"、"pgm"、".tif"、"tiff"、"webp"。 您还可以在一个文件夹中包含多种类型的图像。 不需要在每个类别文件夹中包含相同数量的图像。

    可以使用扩展名为 ".zip"、"tar"、"gz" 和 ". bz2" 的文件夹或压缩文件。 **建议使用压缩文件来提高性能。** 
    
    ![映像示例数据集](./media/module/image-sample-dataset.png)

    为评分，image dataset 文件夹只需要包含未分类的图像。

1. 将[图像数据集注册为](https://docs.microsoft.com/azure/machine-learning/how-to-create-register-datasets)工作区中的文件数据集，因为转换为图像目录模块的输入必须是**文件数据集**。

1. 将已注册的图像数据集添加到画布。 可以在画布左侧的模块列表中的 " **数据集** " 类别中找到已注册的数据集。 当前设计器不支持可视化图像数据集。

    > [!WARNING]
    > 不能使用“导入数据”模块导入图像数据集，因为“导入数据”模块的输出类型是“DataFrame 目录”，它只包含文件路径字符串。

1. 将 " **转换为图像目录** " 模块添加到画布中。 可以在模块列表的“计算机视觉/图像数据转换”类别中找到此模块。 将其连接到图像数据集。
    
3.  提交管道。 此模块可在 GPU 或 CPU 上运行。

## <a name="results"></a>结果

**转换为图像目录**模块的输出为**图像目录**格式，可连接到其他与图像相关的模块，其中输入端口格式也是图像目录。

![转换为图像目录输出](./media/module/convert-to-image-directory-output.png)

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
