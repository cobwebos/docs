---
title: 快速入门：分析远程图像 - REST、Python - 计算机视觉
titleSuffix: Azure Cognitive Services
description: 在本快速入门中，你将在认知服务中使用计算机视觉和 Python 分析远程图像。
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: v-deken
ms.openlocfilehash: 628b1b35a990052e49f80a4a65a8a80972dde909
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/07/2018
ms.locfileid: "44048984"
---
# <a name="quickstart-analyze-a-remote-image---rest-python---computer-vision"></a>快速入门：分析远程图像 - REST、Python - 计算机视觉

在本快速入门中，你将使用计算机视觉分析远程图像。 若要分析本地图像，请参阅[使用 Python 分析本地图像](python-disk.md)。

可以在 [MyBinder](https://mybinder.org) 上使用 Jupyter 笔记本以分步方式运行此快速入门。 若要启动活页夹，请选择以下按钮：

[![活页夹](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=VisionAPI.ipynb)

## <a name="prerequisites"></a>先决条件

若要使用计算机视觉，需要一个订阅密钥；请参阅[获取订阅密钥](../Vision-API-How-to-Topics/HowToSubscribe.md)。

## <a name="analyze-a-remote-image"></a>分析远程图像

使用 [Analyze Image 方法](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa)，可以根据图像内容提取视觉特征。 可以上传图像或指定图像 URL 并选择要返回的特征，包括：

* 与图像内容相关的标记的详细列表。
* 用完整句子描述的图像内容。
* 图像中包含的任何人脸的坐标、性别和年龄。
* 图像类型（剪贴画或线条图）。
* 主色、主题色或图像是否为黑白。
* 此[分类](../Category-Taxonomy.md)中定义的类别。
* 图像是否包含成人内容或性暗示内容？

若要运行此示例，请执行以下步骤：

1. 将以下代码复制到新的 Python 脚本文件。
1. 将 `<Subscription Key>` 替换为有效订阅密钥。
1. 如有必要，将 `vision_base_url` 值更改为你获得订阅密钥的位置。
1. （可选）将 `image_url` 值更改为另一个图像。
1. 运行该脚本。

以下代码使用 Python `requests` 库调用计算机视觉分析图像 API。 它将结果作为 JSON 对象返回。 API 密钥是通过 `headers` 字典传入的。 要识别的功能的类型是通过 `params` 字典传入的。

## <a name="analyze-image-request"></a>分析图像请求

```python
import requests
# If you are using a Jupyter notebook, uncomment the following line.
#%matplotlib inline
import matplotlib.pyplot as plt
from PIL import Image
from io import BytesIO

# Replace <Subscription Key> with your valid subscription key.
subscription_key = "<Subscription Key>"
assert subscription_key

# You must use the same region in your REST call as you used to get your
# subscription keys. For example, if you got your subscription keys from
# westus, replace "westcentralus" in the URI below with "westus".
#
# Free trial subscription keys are generated in the westcentralus region.
# If you use a free trial subscription key, you shouldn't need to change
# this region.
vision_base_url = "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/"

analyze_url = vision_base_url + "analyze"

# Set image_url to the URL of an image that you want to analyze.
image_url = "https://upload.wikimedia.org/wikipedia/commons/thumb/1/12/" + \
    "Broadway_and_Times_Square_by_night.jpg/450px-Broadway_and_Times_Square_by_night.jpg"

headers = {'Ocp-Apim-Subscription-Key': subscription_key }
params  = {'visualFeatures': 'Categories,Description,Color'}
data    = {'url': image_url}
response = requests.post(analyze_url, headers=headers, params=params, json=data)
response.raise_for_status()

# The 'analysis' object contains various fields that describe the image. The most
# relevant caption for the image is obtained from the 'description' property.
analysis = response.json()
print(json.dumps(response.json()))
image_caption = analysis["description"]["captions"][0]["text"].capitalize()

# Display the image and overlay it with the caption.
image = Image.open(BytesIO(requests.get(image_url).content))
plt.imshow(image)
plt.axis("off")
_ = plt.title(image_caption, size="x-large", y=-0.1)
plt.show()
```

## <a name="analyze-image-response"></a>分析图像响应

成功的响应以 JSON 格式返回，例如：

```json
{
  "categories": [
    {
      "name": "outdoor_",
      "score": 0.00390625,
      "detail": {
        "landmarks": []
      }
    },
    {
      "name": "outdoor_street",
      "score": 0.33984375,
      "detail": {
        "landmarks": []
      }
    }
  ],
  "description": {
    "tags": [
      "building",
      "outdoor",
      "street",
      "city",
      "people",
      "busy",
      "table",
      "walking",
      "traffic",
      "filled",
      "large",
      "many",
      "group",
      "night",
      "light",
      "crowded",
      "bunch",
      "standing",
      "man",
      "sign",
      "crowd",
      "umbrella",
      "riding",
      "tall",
      "woman",
      "bus"
    ],
    "captions": [
      {
        "text": "a group of people on a city street at night",
        "confidence": 0.9122243847383961
      }
    ]
  },
  "color": {
    "dominantColorForeground": "Brown",
    "dominantColorBackground": "Brown",
    "dominantColors": [
      "Brown"
    ],
    "accentColor": "B54316",
    "isBwImg": false
  },
  "requestId": "c11894eb-de3e-451b-9257-7c8b168073d1",
  "metadata": {
    "height": 600,
    "width": 450,
    "format": "Jpeg"
  }
}
```

## <a name="next-steps"></a>后续步骤

浏览一款 Python 应用程序，该应用程序使用计算机视觉执行光学字符识别 (OCR)、创建智能裁剪缩略图，并对图像中的视觉特征（包括人脸）进行检测、分类、标记和描述。 若要快速体验计算机视觉 API，请尝试使用 [Open API 测试控制台](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console)。

> [!div class="nextstepaction"]
> [计算机视觉 API Python 教程](../Tutorials/PythonTutorial.md)
