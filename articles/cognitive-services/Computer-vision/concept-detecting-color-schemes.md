---
title: 配色方案检测-计算机视觉
titleSuffix: Azure Cognitive Services
description: 使用计算机视觉 API 检测图像中的配色方案的相关概念。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: af0c39ed8211ac2041d143112437ad5d6b384259
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "80244726"
---
# <a name="detect-color-schemes-in-images"></a>检测图像中的配色方案

计算机视觉分析图像中的颜色以便提供三种不同属性：主导前景色、主导背景色和图像的整体主导色的集。 返回色属于集：黑色、蓝色、棕色、灰色、绿色、橙色、粉红色、紫色、红色、青色、白色和黄色。 

计算机视觉还提取了代表图像中最鲜艳颜色的主题色，其基于主色和饱和度的组合。 主题色作为十六进制 HTML 颜色代码返回。 

计算机视觉还返回布尔值，该值指示图像为黑色或白色。

## <a name="color-scheme-detection-examples"></a>配色方案检测示例

下面的示例说明了检测示例图像的配色方案时，计算机视觉所返回的 JSON 响应。 在这种情况下，示例图不是黑色和白色的图像，但主导的前景色和背景色为黑色，并且图像的整体主导色为黑色和白色。

![日落时户外的大山以及人的轮廓](./Images/mountain_vista.png)

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

下表显示了针对每个示例图像返回的前景、背景和图像颜色。

| 映像 | 主色 |
|-------|-----------------|
|![具有绿色背景的白色花卉](./Images/flower.png)| 前景色：黑色<br/>背景色：白色<br/>颜色：黑色、白色、绿色|
![穿过车站的火车](./Images/train_station.png) | 前景色：黑色<br/>背景色：黑色<br/>颜色：黑色 |

### <a name="accent-color-examples"></a>主题色示例

 下表显示返回的十六进制 HTML 值形式的每个示例图像的主题色。

| 映像 | 主题色 |
|-------|--------------|
|![日落时站在山岩上的人](./Images/mountain_vista.png) | #BB6D10 |
|![具有绿色背景的白色花卉](./Images/flower.png) | #C6A205 |
|![穿过车站的火车](./Images/train_station.png) | #474A84 |

### <a name="black--white-detection-examples"></a>黑白检测示例

下表显示示例图像中的计算机视觉的黑色和白色计算。

| 映像 | 黑色还是白色？ |
|-------|----------------|
|![曼哈顿建筑的黑白照片](./Images/bw_buildings.png) | true |
|![一座蓝色的房子和前院](./Images/house_yard.png) | false |

## <a name="use-the-api"></a>使用 API

"配色方案检测" 功能是 " [分析映像](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) API" 的一部分。 可以通过本机 SDK 或 REST 调用来调用此 API。 将 `Color` 包括在 **visualFeatures** 查询参数中。 然后，在获取完整 JSON 响应时，就只需分析 `"color"` 部分内容的字符串。

* [快速入门：计算机视觉 .NET SDK](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
* [快速入门：分析图像 (REST API)](./quickstarts/csharp-analyze.md)
