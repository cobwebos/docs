---
title: 检测配色方案 - 计算机视觉
titleSuffix: Azure Cognitive Services
description: 使用计算机视觉 API 检测图像中的配色方案的相关概念。
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.openlocfilehash: 5d0cb6ca751c844846288e8fe26f6ae542e89831
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2018
ms.locfileid: "49339486"
---
# <a name="detecting-color-schemes"></a>检测配色方案

计算机视觉可从图像中提取颜色。 然后在三个不同的上下文中分析颜色：主导前景色、主导背景色和图像的整体主导色。 它们可分组为 12 种主导的主题色。 这些主题色为黑色、蓝色、棕色、灰色、绿色、橙色、粉红色、紫色、红色、青色、白色和黄色。 计算机视觉可分析从图像中提取的颜色，以通过主导色和饱和度的组合返回向观众呈现最鲜艳图像颜色的主题色。 根据图像中的颜色，可能会使用十六进制颜色代码返回简单的黑白或主题色。 计算机视觉还会返回一个布尔值，该值指示图像为黑色还是白色。

## <a name="color-scheme-detection-examples"></a>配色方案检测示例

下面的示例说明了检测示例图像的配色方案时，计算机视觉所返回的 JSON 响应。 在这种情况下，示例图不是黑色和白色的图像，但主导的前景色和背景色为黑色，并且图像的整体主导色为黑色和白色。

![户外山脉](./Images/mountain_vista.png)

```json
{
    "color": {
        "dominantColorForeground": "Black",
        "dominantColorBackground": "Black",
        "dominantColors": ["Black", "White"],
        "accentColor": "BB6D10",
        "isBwImg": false
    },
    "requestId": "0dc394bf-db50-4871-bdcc-13707d9405ea",
    "metadata": {
        "height": 202,
        "width": 300,
        "format": "Jpeg"
    }
}
```

### <a name="dominant-color-examples"></a>主导色示例

下表描述计算机视觉所返回的每个示例图像的主导前景色、主导背景色和图像颜色。

| 映像 | 主色 |
|-------|-----------------|
|![视觉分析花](./Images/flower.png)| 前景色：黑色<br/>背景色：白色<br/>颜色：黑色、白色、绿色|
![视觉分析火车站](./Images/train_station.png) | 前景色：黑色<br/>背景色：黑色<br/>颜色：黑色 |

### <a name="accent-color-examples"></a>主题色示例

 下表描述计算机视觉所返回的十六进制 HTML 值形式的每个示例图像的主题色。

| 映像 | 主题色 |
|-------|--------------|
|![户外山脉](./Images/mountain_vista.png) | #BB6D10 |
|![视觉分析花](./Images/flower.png) | #C6A205 |
|![视觉分析火车站](./Images/train_station.png) | #474A84 |

### <a name="black--white-detection-examples"></a>黑白检测示例

下表指示计算机视觉所返回的每个示例图像是为黑色还是白色。

| 映像 | 黑色还是白色？ |
|-------|----------------|
|![视觉分析建筑](./Images/bw_buildings.png) | true |
|![视觉分析住宅庭院](./Images/house_yard.png) | false |

## <a name="next-steps"></a>后续步骤

了解[检测映像类型](concept-detecting-image-types.md)的概念。