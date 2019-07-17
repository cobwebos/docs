---
title: 快速入门：生成缩略图 - REST、Python
titleSuffix: Azure Cognitive Services
description: 在本快速入门中，你将使用计算机视觉 API 和 Python 基于图像生成缩略图。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 07/03/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 5203467eadadf1180eabf7b5a51b113ae273ee7c
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/05/2019
ms.locfileid: "67603537"
---
# <a name="quickstart-generate-a-thumbnail-using-the-computer-vision-rest-api-and-python"></a>快速入门：使用计算机视觉 REST API 和 Python 生成缩略图

在本快速入门中，你将使用计算机视觉的 REST API 基于图像生成缩略图。 使用[获取缩略图](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb)方法，可以指定所需的高度和宽度，计算机视觉使用智能裁剪来智能识别感兴趣的区域并基于该区域生成裁剪坐标。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/try/cognitive-services/)。

## <a name="prerequisites"></a>先决条件

- 必须具有计算机视觉的订阅密钥。 可以从[试用认知服务](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision)获取免费试用密钥。 或者，按照[创建认知服务帐户](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)中的说明订阅计算机视觉并获取密钥。
- 代码编辑器，如 [Visual Studio Code](https://code.visualstudio.com/download)

## <a name="create-and-run-the-sample"></a>创建并运行示例

若要创建和运行示例，请将以下代码复制到代码编辑器中。 

```python
import requests
# If you are using a Jupyter notebook, uncomment the following line.
# %matplotlib inline
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
# Free trial subscription keys are generated in the "westcentralus" region.
# If you use a free trial subscription key, you shouldn't need to change
# this region.
vision_base_url = "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/"

thumbnail_url = vision_base_url + "generateThumbnail"

# Set image_url to the URL of an image that you want to analyze.
image_url = "https://upload.wikimedia.org/wikipedia/commons/9/94/Bloodhound_Puppy.jpg"

headers = {'Ocp-Apim-Subscription-Key': subscription_key}
params = {'width': '50', 'height': '50', 'smartCropping': 'true'}
data = {'url': image_url}
response = requests.post(thumbnail_url, headers=headers,
                         params=params, json=data)
response.raise_for_status()

thumbnail = Image.open(BytesIO(response.content))

# Display the thumbnail.
plt.imshow(thumbnail)
plt.axis("off")

# Verify the thumbnail size.
print("Thumbnail is {0}-by-{1}".format(*thumbnail.size))
```

接下来，请执行以下操作：
1. 将 `subscription_key` 的值替换为你的订阅密钥。
1. 如有必要，请将 `vision_base_url` 的值替换为获取的订阅密钥所在的 Azure 区域中的计算机视觉资源的终结点 URL。
1. （可选）将 `image_url` 的值替换为你要为其生成缩略图的另一图像的 URL。
1. 将代码保存为以 `.py` 为扩展名的文件。 例如，`get-thumbnail.py`。
1. 打开命令提示符窗口。
1. 在提示符处，使用 `python` 命令运行示例。 例如，`python get-thumbnail.py` 。

## <a name="examine-the-response"></a>检查响应

成功的响应将以二进制数据形式返回，这些数据表示缩略图的图像数据。 该示例应显示此图像。 如果请求失败，则响应将显示在命令提示符窗口中，并且应包含错误代码。

## <a name="run-in-jupyter-optional"></a>在 Jupyter 中运行（可选）

可以选择在 [MyBinder](https://mybinder.org) 上使用 Jupyter 笔记本以分步方式运行此快速入门。 要启动活页夹，请选择以下按钮：

[![活页夹](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=VisionAPI.ipynb)

## <a name="next-steps"></a>后续步骤

接下来，了解有关生成缩略图功能的更多详细信息。

> [!div class="nextstepaction"]
> [生成缩略图](../concept-generating-thumbnails.md)
