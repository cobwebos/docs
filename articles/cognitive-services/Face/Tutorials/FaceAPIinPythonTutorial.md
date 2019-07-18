---
title: 快速入门：使用 Python SDK 检测和定格图像中的人脸
titleSuffix: Azure Cognitive Services
description: 在本快速入门中，你将创建一个 Python 脚本，它使用人脸 API 来检测和定格远程图像中的人脸。
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 07/15/2019
ms.author: sbowles
ms.openlocfilehash: 2f2245b4f6e4b38e0b071678ac0f3bddeb72f7ec
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2019
ms.locfileid: "68277518"
---
# <a name="quickstart-create-a-python-script-to-detect-and-frame-faces-in-an-image"></a>快速入门：创建一个用于检测和定格图像中人脸的 Python 脚本

在本快速入门中，你将创建一个 Python 脚本，它通过 Python SDK 使用 Azure 人脸 API 来检测远程图像中的人脸。 此应用程序显示一个选定的图像，然后围绕每个检测到的人脸绘制一个框。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 

## <a name="prerequisites"></a>先决条件

- 人脸 API 订阅密钥。 可以从[试用认知服务](https://azure.microsoft.com/try/cognitive-services/?api=face-api)获取免费试用的订阅密钥。 或者，按照[创建认知服务帐户](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)中的说明订阅人脸 API 服务并获取密钥。
- [Python 2.7+ 或 3.5+](https://www.python.org/downloads/)
- [pip](https://pip.pypa.io/en/stable/installing/) 工具

## <a name="get-the-face-sdk"></a>获取人脸 SDK

通过打开命令提示符并运行以下命令来安装人脸 Python SDK：

```shell
pip install cognitive_face
```

## <a name="detect-faces-in-an-image"></a>在图像中检测人脸

创建名为 FaceQuickstart.py 的新 Python 脚本并添加以下代码  。 此代码处理人脸检测的核心功能。 需将 `<Subscription Key>` 替换为密钥的值。 可能还需更改 `BASE_URL` 的值，以便使用密钥的正确区域标识符（如需包含所有区域终结点的列表，请参阅[人脸 API 文档](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)）。 免费试用订阅密钥在 **westus** 区域中生成。 （可选）将 `img_url` 设置为要使用的图像的 URL。

此脚本会通过调用 **cognitive_face.face.detect** 方法来检测人脸，该方法可包装[检测](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) REST API 并返回人脸的列表。

```python
import cognitive_face as CF

# Replace with a valid subscription key (keeping the quotes in place).
KEY = '<Subscription Key>'
CF.Key.set(KEY)

# Replace with your regional Base URL
BASE_URL = 'https://westus.api.cognitive.microsoft.com/face/v1.0/'
CF.BaseUrl.set(BASE_URL)

# You can use this example JPG or replace the URL below with your own URL to a JPEG image.
img_url = 'https://raw.githubusercontent.com/Microsoft/Cognitive-Face-Windows/master/Data/detection1.jpg'
faces = CF.face.detect(img_url)
print(faces)
```

### <a name="try-the-app"></a>试用应用

使用 `python FaceQuickstart.py` 命令运行此应用。 此时会在控制台窗口中出现文本响应，如下所示：

```console
[{'faceId': '26d8face-9714-4f3e-bfa1-f19a7a7aa240', 'faceRectangle': {'top': 124, 'left': 459, 'width': 227, 'height': 227}}]
```

输出表示检测到的人脸列表。 列表中的每项都是一个 **dict** 实例，其中 `faceId` 是检测到的人脸的唯一 ID，`faceRectangle` 说明检测到的人脸的位置。 

> [!NOTE]
> 人脸 ID 在 24 小时后过期；如需长期保存人脸数据，则需对其进行显式存储。

## <a name="draw-face-rectangles"></a>绘制人脸矩形

使用通过上一命令接收到的坐标，可在图像上绘制矩形，以可视方式呈现每张人脸。 需安装 Pillow (`pip install pillow`) 才能使用 Pillow 图像模块。 在 *FaceQuickstart.py* 顶部添加以下代码：

```python
import requests
from io import BytesIO
from PIL import Image, ImageDraw
```

然后，在脚本底部添加以下代码。 此代码会创建一个用于分析矩形坐标的简单函数，并使用 Pillow 在原始图像上绘制矩形。 然后，它会在默认的图像查看器中显示图像。

```python
# Convert width height to a point in a rectangle


def getRectangle(faceDictionary):
    rect = faceDictionary['faceRectangle']
    left = rect['left']
    top = rect['top']
    bottom = left + rect['height']
    right = top + rect['width']
    return ((left, top), (bottom, right))


# Download the image from the url
response = requests.get(img_url)
img = Image.open(BytesIO(response.content))

# For each face returned use the face rectangle and draw a red box.
draw = ImageDraw.Draw(img)
for face in faces:
    draw.rectangle(getRectangle(face), outline='red')

# Display the image in the users default image browser.
img.show()
```

## <a name="run-the-app"></a>运行应用

系统可能会提示你先选择默认的图像查看器。 然后，会看到如下图像。 还会在控制台窗口中看到输出的矩形数据。

![一位年轻的妇女，其脸部周围绘制了一个红色矩形](../images/face-rectangle-result.png)

## <a name="next-steps"></a>后续步骤

本快速入门介绍了人脸 API Python SDK 的基本使用过程，并创建了一个脚本来检测并定格图像中的人脸。 接下来，了解如何在更复杂的示例中使用 Python SDK。 请转到 GitHub 上的认知人脸 Python 示例，将其克隆到项目文件夹，然后按 _README.md_ 文件中的说明操作。

> [!div class="nextstepaction"]
> [认知人脸 Python 示例](https://github.com/Microsoft/Cognitive-Face-Python)
