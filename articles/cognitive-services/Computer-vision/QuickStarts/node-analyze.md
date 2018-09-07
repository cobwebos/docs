---
title: 计算机视觉 API Node.js 快速入门 - 分析图像 | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: 本快速入门将在认知服务中使用计算机视觉和 Node.js 分析图像。
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: v-deken
ms.openlocfilehash: dab6547e08b1b01a9090a817d728c86359c680f2
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2018
ms.locfileid: "43769296"
---
# <a name="quickstart-analyze-a-remote-image---rest-nodejs"></a>快速入门：分析远程图像 - REST、Node.js

本快速入门将使用计算机视觉分析图像以提取视觉特征。

## <a name="prerequisites"></a>先决条件

若要使用计算机视觉，需要订阅密钥；请参阅[获取订阅密钥](../Vision-API-How-to-Topics/HowToSubscribe.md)。

## <a name="analyze-image-request"></a>Analyze Image 请求

使用 [Analyze Image 方法](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa)，可以根据图像内容提取视觉特征。 可以上传图像或指定图像 URL 并选择要返回的特征，包括：

* 与图像内容相关的标记的详细列表。
* 用完整句子描述图像内容。
* 图像中包含的任何人脸的坐标、性别和年龄。
* 图像类型（剪贴画或线条图）。
* 主色、主题色或图像是否为黑白。
* 此[分类](../Category-Taxonomy.md)中定义的类别。
* 图像是否包含成人内容或性暗示内容？

若要运行此示例，请执行以下步骤：

1. 将以下代码复制到编辑器中。
1. 将 `<Subscription Key>` 替换为有效订阅密钥。
1. 如有必要，将 `uriBase` 值更改为你获得订阅密钥的位置。
1. （可选）将 `imageUrl` 值更改为要分析的图像。
1. （可选）更改响应语言 (`'language': 'en'`)。
1. 使用 `.js` 扩展名保存文件。
1. 打开 Node.js 命令提示符并运行该文件，例如：`node myfile.js`。

此示例使用 npm [请求](https://www.npmjs.com/package/request)包。

```nodejs
'use strict';

const request = require('request');

// Replace <Subscription Key> with your valid subscription key.
const subscriptionKey = '<Subscription Key>';

// You must use the same location in your REST call as you used to get your
// subscription keys. For example, if you got your subscription keys from
// westus, replace "westcentralus" in the URL below with "westus".
const uriBase =
    'https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/analyze';

const imageUrl =
    'http://upload.wikimedia.org/wikipedia/commons/3/3c/Shaki_waterfall.jpg';

// Request parameters.
const params = {
    'visualFeatures': 'Categories,Description,Color',
    'details': '',
    'language': 'en'
};

const options = {
    uri: uriBase,
    qs: params,
    body: '{"url": ' + '"' + imageUrl + '"}',
    headers: {
        'Content-Type': 'application/json',
        'Ocp-Apim-Subscription-Key' : subscriptionKey
    }
};

request.post(options, (error, response, body) => {
  if (error) {
    console.log('Error: ', error);
    return;
  }
  let jsonResponse = JSON.stringify(JSON.parse(body), null, '  ');
  console.log('JSON Response\n');
  console.log(jsonResponse);
});
```

## <a name="analyze-image-response"></a>Analyze Image 响应

JSON 中返回了成功的响应，例如：

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
  "color": {
    "dominantColorForeground": "Grey",
    "dominantColorBackground": "Green",
    "dominantColors": [
      "Grey",
      "Green"
    ],
    "accentColor": "4D5E2F",
    "isBwImg": false
  },
  "requestId": "81b4e400-e3c1-41f1-9020-e6871ad9f0ed",
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
