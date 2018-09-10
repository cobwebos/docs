---
title: 快速入门：生成缩略图 - REST、Python - 计算机视觉
titleSuffix: Azure Cognitive Services
description: 在本快速入门中，你将在认知服务中使用计算机视觉和 Python 基于图像生成缩略图。
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: v-deken
ms.openlocfilehash: a558ceb078a81879cd0043e5d573a1f4bd580a25
ms.sourcegitcommit: 3d0295a939c07bf9f0b38ebd37ac8461af8d461f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2018
ms.locfileid: "43841517"
---
# <a name="quickstart-generate-a-thumbnail---rest-python---computer-vision"></a>快速入门：生成缩略图 - REST、Python - 计算机视觉

在本快速入门中，你将使用计算机视觉基于图像生成缩略图。

可以在 [MyBinder](https://mybinder.org) 上使用 Jupyter 笔记本以分步方式运行此快速入门。 若要启动活页夹，请选择以下按钮：

[![活页夹](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=VisionAPI.ipynb)

## <a name="prerequisites"></a>先决条件

若要使用计算机视觉，需要一个订阅密钥；请参阅[获取订阅密钥](../Vision-API-How-to-Topics/HowToSubscribe.md)。

## <a name="intelligently-generate-a-thumbnail"></a>智能生成缩略图

使用 [Get Thumbnail 方法](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb)，可以生成图像的缩略图。 可以指定高度和宽度，可以与输入图像的纵横比不同。 计算机视觉使用智能裁剪来智能识别感兴趣的区域并基于该区域生成裁剪坐标。

若要运行此示例，请执行以下步骤：

1. 将以下代码复制到新的 Python 脚本文件。
1. 将 `<Subscription Key>` 替换为有效订阅密钥。
1. 如有必要，将 `vision_base_url` 值更改为你获得订阅密钥的位置。
1. （可选）将 `image_url` 值更改为另一个图像。
1. 运行该脚本。

以下代码使用 Python `requests` 库调用计算机视觉分析图像 API。 API 密钥是通过 `headers` 字典传入的。 通过传入的缩略图大小`params`字典。 缩略图在响应中以字节数组形式返回。

## <a name="get-thumbnail-request"></a>获取缩略图请求

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

thumbnail_url = vision_base_url + "generateThumbnail"

# Set image_url to the URL of an image that you want to analyze.
image_url = "https://upload.wikimedia.org/wikipedia/commons/9/94/Bloodhound_Puppy.jpg"

headers = {'Ocp-Apim-Subscription-Key': subscription_key}
params  = {'width': '50', 'height': '50', 'smartCropping': 'true'}
data    = {'url': image_url}
response = requests.post(thumbnail_url, headers=headers, params=params, json=data)
response.raise_for_status()

thumbnail = Image.open(BytesIO(response.content))

# Display the thumbnail.
plt.imshow(thumbnail)
plt.axis("off")

# Verify the thumbnail size.
print("Thumbnail is {0}-by-{1}".format(*thumbnail.size))
```

## <a name="next-steps"></a>后续步骤

浏览一款 Python 应用程序，该应用程序使用计算机视觉执行光学字符识别 (OCR)、创建智能裁剪缩略图，并对图像中的视觉特征（包括人脸）进行检测、分类、标记和描述。 若要快速体验计算机视觉 API，请尝试使用 [Open API 测试控制台](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console)。

> [!div class="nextstepaction"]
> [计算机视觉 API Python 教程](../Tutorials/PythonTutorial.md)
