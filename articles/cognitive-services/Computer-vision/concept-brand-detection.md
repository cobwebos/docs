---
title: 品牌检测 - 计算机视觉
titleSuffix: Azure Cognitive Services
description: 与使用计算机视觉 API 检测品牌/徽标相关的概念。
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 01/26/2019
ms.author: pafarley
ms.openlocfilehash: a8822eb773842df3ea6fd50cd2a6ba36c3c4cd88
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2019
ms.locfileid: "55569352"
---
# <a name="brand-detection"></a>品牌检测

品牌检测是[对象检测](concept-object-detection.md)的一种专用模式，使用包含全球数千个徽标的数据库来标识图像或视频中的商业品牌。 可以使用此功能来执行特定的操作，例如，发现哪些品牌在社交媒体上最受欢迎，或者哪些品牌在社交产品排名上最靠前。

计算机视觉服务检测给定图像中是否存在品牌徽标；如果存在，则返回品牌名称、置信度分数以及徽标周围边框的坐标。

内置徽标数据库涵盖消费类电子产品、服装等的流行品牌。 如果发现计算机视觉服务未检测到所寻找的品牌，则可能使用[自定义视觉](https://docs.microsoft.com/azure/cognitive-services/Custom-Vision-Service/)服务创建和训练自己的徽标检测程序的效果会更好。

## <a name="brand-detection-example"></a>品牌检测示例

以下 JSON 响应表明计算机视觉在示例图像中检测品牌时所返回的内容。

![上面有 Microsoft 标签和徽标的灰色运动衫](./Images/gray-shirt-logo.jpg)

```json
{
   "brands":[
      {
         "name":"Microsoft",
         "confidence":0.706,
         "rectangle":{
            "x":470,
            "y":862,
            "w":338,
            "h":327
         }
      }
   ],
   "requestId":"5fda6b40-3f60-4584-bf23-911a0042aa13",
   "metadata":{
      "width":2286,
      "height":1715,
      "format":"Jpeg"
   }
}
```
在某些情况下，品牌检测程序会挑选徽标图像和风格化品牌名称作为两个单独徽标。

![上面有 Microsoft 标签的徽标的红色衬衫](./Images/red-shirt-logo.jpg)

```json
{
   "brands":[
      {
         "name":"Microsoft",
         "confidence":0.657,
         "rectangle":{
            "x":436,
            "y":473,
            "w":568,
            "h":267
         }
      },
      {
         "name":"Microsoft",
         "confidence":0.85,
         "rectangle":{
            "x":101,
            "y":561,
            "w":273,
            "h":263
         }
      }
   ],
   "requestId":"10dcd2d6-0cf6-4a5e-9733-dc2e4b08ac8d",
   "metadata":{
      "width":1286,
      "height":1715,
      "format":"Jpeg"
   }
}
```

## <a name="use-the-api"></a>使用 API
品牌检测功能属于[分析图像](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) API。 可以通过本机 SDK 或 REST 调用来调用此 API。 获取完整 JSON 响应时，只需分析 `"brands"` 部分内容的字符串。

* [快速入门：分析图像 (.NET SDK)](./quickstarts-sdk/csharp-analyze-sdk.md)
* [快速入门：分析图像 (REST API)](./quickstarts/csharp-analyze.md)