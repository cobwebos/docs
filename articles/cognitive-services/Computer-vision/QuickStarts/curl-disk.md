---
title: 计算机视觉 API cURL 快速入门 - 分析本地图像 | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: 在本快速入门中，你将在认知服务中使用计算机视觉和 cURL 分析本地图像。
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: v-deken
ms.openlocfilehash: 93ca3ea6eee3743dfd0c25c9514375ae63a531ee
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2018
ms.locfileid: "43769367"
---
# <a name="quickstart-analyze-a-local-image---rest-curl"></a>快速入门：分析本地图像 - REST、cURL

在本快速入门中，你将使用计算机视觉分析本地图像来提取视觉特征。 若要分析远程图像，请参阅[使用 cURL 分析远程图像](curl-analyze.md)。

## <a name="prerequisites"></a>先决条件

若要使用计算机视觉，需要一个订阅密钥；请参阅[获取订阅密钥](../Vision-API-How-to-Topics/HowToSubscribe.md)。

## <a name="analyze-a-local-image"></a>分析本地图像

此示例与[使用 cURL 分析远程图像](curl-analyze.md)类似，只是要分析的图像是在本地从磁盘读取的。 需要进行三处更改：

- 将 Content-Type 更改为 `"Content-Type: application/octet-stream"`。
- 将 `-d` 开关更改为 `--data-binary`。
- 使用以下语法指定要分析的图像：`@C:/Pictures/ImageToAnalyze.jpg`。

若要运行此示例，请执行以下步骤：

1. 将以下代码复制到编辑器中。
1. 将 `<Subscription Key>` 替换为有效订阅密钥。
1. 如有必要，请更改请求 URL (`https://westcentralus.api.cognitive.microsoft.com/vision/v2.0`) 以使用获得订阅密钥的位置。
1. 将 `<Image To Analyze>` 替换为要分析的本地图像。
1. （可选）更改响应语言 (`language=en`)。
1. 在安装了 cURL 的计算机上打开命令窗口。
1. 将代码粘贴到窗口中并运行命令。

>[!NOTE]
>在 REST 调用中，必须使用用于获取订阅密钥的位置。 例如，如果从 westus 获取了订阅密钥，请将下方 URL 中的“westcentralus”替换为“westus”。

```json
curl -H "Ocp-Apim-Subscription-Key: <Subscription Key>" -H "Content-Type: application/octet-stream" "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/analyze?visualFeatures=Categories,Description&details=Landmarks&language=en" --data-binary <Image To Analyze>
```

## <a name="analyze-image-response"></a>分析图像响应

成功的响应以 JSON 格式返回，例如：

```json
{
  "categories": [
    {
      "name": "outdoor_water",
      "score": 0.9921875,
      "detail": {
        "landmarks": []
      }
    }
  ],
  "description": {
    "tags": [
      "nature",
      "water",
      "waterfall",
      "outdoor",
      "rock",
      "mountain",
      "rocky",
      "grass",
      "hill",
      "covered",
      "hillside",
      "standing",
      "side",
      "group",
      "walking",
      "white",
      "man",
      "large",
      "snow",
      "grazing",
      "forest",
      "slope",
      "herd",
      "river",
      "giraffe",
      "field"
    ],
    "captions": [
      {
        "text": "a large waterfall over a rocky cliff",
        "confidence": 0.916458423253597
      }
    ]
  },
  "requestId": "b6e33879-abb2-43a0-a96e-02cb5ae0b795",
  "metadata": {
    "height": 959,
    "width": 1280,
    "format": "Jpeg"
  }
}
```

## <a name="next-steps"></a>后续步骤

探索用于分析图像、检测名人和地标，创建缩略图以及提取打印文本和手写文本的计算机视觉 API。 若要快速体验计算机视觉 API，请尝试使用 [Open API 测试控制台](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console)。

> [!div class="nextstepaction"]
> [探索计算机视觉 API](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
