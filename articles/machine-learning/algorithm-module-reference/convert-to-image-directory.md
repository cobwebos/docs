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
ms.date: 09/28/2020
ms.openlocfilehash: 9f5f4b2b069ebc65430fba4bc31a9891ed61fedf
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91450114"
---
# <a name="convert-to-image-directory"></a>转换为图像目录

本文介绍如何使用 "转换为图像目录" 模块来帮助将图像数据集转换为 "Image Directory" 数据类型，该数据类型是图像相关任务中的标准化数据格式，如 Azure 机器学习设计器中的图像分类。

## <a name="how-to-use-convert-to-image-directory"></a>如何使用“转换为图像目录”模块  

1.  将 " **转换为图像目录** " 模块添加到画布中。 可以在模块列表的“计算机视觉/图像数据转换”类别中找到此模块。 

2.  **转换为图像目录**模块的输入必须是文件数据集。 [注册一个图像数据集](https://docs.microsoft.com/azure/machine-learning/how-to-create-register-datasets)，并将其连接到模块输入端口。 请确保输入数据集中有图像。 当前设计器不支持可视化图像数据集。
 
    支持以下数据集格式：

    - 具有以下扩展名的压缩文件：“.zip”、“.tar”、“.gz”、“.bz2”。
    - 包含图像的文件夹。 强烈建议先压缩此类文件夹，然后再使用压缩文件作为数据集。

    > [!WARNING]
    > 不能使用“导入数据”模块导入图像数据集，因为“导入数据”模块的输出类型是“DataFrame 目录”，它只包含文件路径字符串。
    

    > [!NOTE]
    > - 如果在监督式学习中使用映像数据集，则需要指定定型数据集的标签。
    > - 对于图像分类任务，如果此图像数据集以 Torchvision ImageFolder 格式进行组织，则可以在模块输出中将标签生成为图像“类别”。 否则，只保存不带标签的图像。 下面是一个示例，说明如何组织图像数据集以获取标签，并使用 image category 作为子文件夹名称。 
    > - 不需要在每个类别文件夹中上传相同数量的映像。
    > - 支持具有以下扩展名的图像 (采用小写) ： ".jpg"、"jpeg"、".png"、"ppm"、".bmp"、"pgm"、".tif"、"tiff"、"webp"。 您还可以在一个文件夹中包含多种类型的图像。    
    > - 有关详细信息，请参阅 [torchvision 数据集](https://pytorch.org/docs/stable/torchvision/datasets.html#imagefolder)。
    >
    > ```
    > Your_image_folder_name/Category_1/xxx.png
    > Your_image_folder_name/Category_1/xxy.jpg
    > Your_image_folder_name/Category_1/xxz.jpeg
    >
    > Your_image_folder_name/Category_2/123.png
    > Your_image_folder_name/Category_2/nsdf3.png
    > Your_image_folder_name/Category_2/asd932_.png
    > ```
    > - 如果使用映像数据集进行评分，则此模块的输入文件数据集应包含未分类的映像。
    
3.  提交管道。 此模块可在 GPU 或 CPU 上运行。

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
