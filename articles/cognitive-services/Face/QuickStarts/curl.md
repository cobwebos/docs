---
title: 快速入门：检测图像中的人脸 - 人脸 API cURL
titleSuffix: Azure Cognitive Services
description: 在本快速入门中，使用人脸 API 和 cURL 检测图像中的人脸。
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: quickstart
ms.date: 05/10/2018
ms.author: pafarley
ms.openlocfilehash: da18b7b3666863742f987b2ee0da297c0838d266
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2018
ms.locfileid: "49343055"
---
# <a name="quickstart-detect-faces-in-an-image-using-curl"></a>快速入门：使用 cURL 检测图像中的人脸

在本快速入门中，使用人脸 API 检测图像中的人脸。

## <a name="prerequisites"></a>先决条件

需要一个订阅密钥来运行此示例。 可以从[试用认知服务](https://azure.microsoft.com/try/cognitive-services/?api=face-api)获取免费试用的订阅密钥。

## <a name="detect-faces-in-an-image"></a>在图像中检测人脸

使用[人脸 - 检测](https://westcentralus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)方法检测图像中的人脸并返回人脸属性，包括：

* 人脸 ID：多个人脸 API 方案中使用的唯一 ID。
* 人脸矩形：左侧、顶部、宽度和高度，指示人脸在图像中的位置。
* 特征点：一系列（27 个点）人脸特征点，这些特征点指向人脸组件的重要位置。
* 人脸属性包括年龄、性别、笑容程度、头部姿态和面部毛发。

若要运行此示例，请执行以下步骤：

1. 打开命令提示符。
2. 将 `<Subscription Key>` 替换为有效订阅密钥。
3. 如果需要，将 URL (`https://westcentralus.api.cognitive.microsoft.com/face/v1.0/detect`) 更改为你在其中获取订阅密钥的位置。
4. （可选）更改要分析的图像 (`"{\"url\":...`)。
5. 在命令窗口中粘贴代码。
6. 运行命令。

### <a name="face---detect-request"></a>人脸 - 检测请求

> [!NOTE]
> 在 REST 调用中，必须使用用于获取订阅密钥的位置。 例如，如果你从美国西部获得订阅密钥，请将下面的 URL 中的“westcentralus”替换为“westus”。

```shell
curl -H "Ocp-Apim-Subscription-Key: <Subscription Key>" "https://westcentralus.api.cognitive.microsoft.com/face/v1.0/detect?returnFaceId=true&returnFaceLandmarks=false&returnFaceAttributes=age,gender,headPose,smile,facialHair,glasses,emotion,hair,makeup,occlusion,accessories,blur,exposure,noise" -H "Content-Type: application/json" --data-ascii "{\"url\":\"https://upload.wikimedia.org/wikipedia/commons/c/c3/RH_Louise_Lillian_Gish.jpg\"}"
```

### <a name="face---detect-response"></a>人脸 - 检测响应

成功的响应以 JSON 格式返回。

```json
[
  {
    "faceId": "49d55c17-e018-4a42-ba7b-8cbbdfae7c6f",
    "faceRectangle": {
      "top": 131,
      "left": 177,
      "width": 162,
      "height": 162
    },
    "faceAttributes": {
      "smile": 0,
      "headPose": {
        "pitch": 0,
        "roll": 0.1,
        "yaw": -32.9
      },
      "gender": "female",
      "age": 22.9,
      "facialHair": {
        "moustache": 0,
        "beard": 0,
        "sideburns": 0
      },
      "glasses": "NoGlasses",
      "emotion": {
        "anger": 0,
        "contempt": 0,
        "disgust": 0,
        "fear": 0,
        "happiness": 0,
        "neutral": 0.986,
        "sadness": 0.009,
        "surprise": 0.005
      },
      "blur": {
        "blurLevel": "low",
        "value": 0.06
      },
      "exposure": {
        "exposureLevel": "goodExposure",
        "value": 0.67
      },
      "noise": {
        "noiseLevel": "low",
        "value": 0
      },
      "makeup": {
        "eyeMakeup": true,
        "lipMakeup": true
      },
      "accessories": [],
      "occlusion": {
        "foreheadOccluded": false,
        "eyeOccluded": false,
        "mouthOccluded": false
      },
      "hair": {
        "bald": 0,
        "invisible": false,
        "hairColor": [
          {
            "color": "brown",
            "confidence": 1
          },
          {
            "color": "black",
            "confidence": 0.87
          },
          {
            "color": "other",
            "confidence": 0.51
          },
          {
            "color": "blond",
            "confidence": 0.08
          },
          {
            "color": "red",
            "confidence": 0.08
          },
          {
            "color": "gray",
            "confidence": 0.02
          }
        ]
      }
    }
  }
]
```

## <a name="next-steps"></a>后续步骤

探索用于检测图像中人脸的人脸 API，使用矩形划分人脸，并返回年龄和性别等属性。

> [!div class="nextstepaction"]
> [人脸 API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
