---
title: 生成缩略图 - 计算机视觉
titleSuffix: Azure Cognitive Services
description: 使用计算机视觉 API 生成图像的缩略图的相关概念。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 03/11/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 8bbc86f5c6fe0f30968a1ba5bd5fa28160ef6963
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60372810"
---
# <a name="generating-smart-cropped-thumbnails-with-computer-vision"></a>使用计算机视觉生成智能裁剪的缩略图

缩略图是图像的缩减表示形式。 缩略图用于以更加经济且更适合布局的方式表示图像及其他数据。 计算机视觉 API 利用智能裁剪及图像大小调整，来创建给定图像的直观缩略图。

计算机视觉缩略图生成算法的工作原理如下：

1. 从图像中删除让人分散注意力的元素并识别  感兴趣区域&mdash;显示主要对象的图像区域。
1. 基于所识别的感兴趣区域裁剪图像。 
1. 更改纵横比以适应目标缩略图尺寸。

## <a name="area-of-interest"></a>感兴趣区域

上传图像时，计算机视觉 API 将对图像进行分析，以确定感兴趣区域。  然后它可使用该区域来确定如何裁剪图像。 但是，如果已指定所需的纵横比，则裁剪操作始终会与之匹配。

此外，还可改为调用 areaOfInterest API 来获取同一个感兴趣区域的原始边界框坐标。   然后可以使用此信息并根据需要来修改原始图像。

## <a name="examples"></a>示例

生成的缩略图可能会根据指定的高度、宽度和智能裁剪的不同而有很大差异，如下图所示。

![各种裁剪配置旁边的一个山脉图像](./Images/thumbnail-demo.png)

下表说明了计算机视觉为示例图像生成的典型缩略图。 生成缩略图的指定目标高度和宽度为 50 像素，并且启用了智能裁剪。

| Image | 缩略图 |
|-------|-----------|
|![日落时户外的大山以及人的轮廓](./Images/mountain_vista.png) | ![日落时户外的大山以及人的轮廓的缩略图](./Images/mountain_vista_thumbnail.png) |
|![具有绿色背景的白色花卉](./Images/flower.png) | ![视觉分析花缩略图](./Images/flower_thumbnail.png) |
|![在公寓楼顶上的一个女人](./Images/woman_roof.png) | ![在公寓楼顶上的一个女人的缩略图](./Images/woman_roof_thumbnail.png) |

## <a name="next-steps"></a>后续步骤

了解[标记图像](concept-tagging-images.md)和[对图像进行分类](concept-categorizing-images.md)。
