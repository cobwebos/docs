---
title: 快速入门：识别图像中人脸的情感 - 情感 API、Python
description: 获取信息和代码示例，以帮助你通过 Python 快速开始使用情感 API。
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: quickstart
ms.date: 02/05/2018
ms.author: anroth
ROBOTS: NOINDEX
ms.openlocfilehash: f0bcc88b60e0a9b93856aa32a10b9c0ad898ce95
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2018
ms.locfileid: "50240692"
---
# <a name="quickstart-build-an-app-to-recognize-emotions-on-faces-in-an-image"></a>快速入门：构建应用以识别图像中人脸的情感。

> [!IMPORTANT]
> 情感 API 将于 2019 年 2 月 15 日弃用。 情感识别功能现在已作为[人脸 API](https://docs.microsoft.com/azure/cognitive-services/face/) 的一部分正式发布。 

本演练中的信息和代码示例有助于用户快速开始结合使用[情感 API Recognize 方法](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa)和 Python，以识别图像中一人或多人的情感。

可以单击下面的“启动”/“活页夹”提示标记，在 [MyBinder](https://mybinder.org) 上以 Jupyter 笔记本形式运行此示例：[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=EmotionAPI.ipynb)


## <a name="prerequisite"></a>先决条件
单击[此处](https://azure.microsoft.com/try/cognitive-services/)获取免费订阅密钥

## <a name="running-the-walkthrough"></a>运行演练
若要继续本演练，请用前面获取的 API 密钥替换 `subscription_key`。


```python
subscription_key = None
assert subscription_key
```

接下来，验证服务 URL 是否对应于设置 API 密钥时使用的区域。 如果使用的是试用版密钥，无需执行任何更改。


```python
emotion_recognition_url = "https://westus.api.cognitive.microsoft.com/emotion/v1.0/recognize"
```

本演练使用在磁盘上存储的图像。 还可以使用可通过公开访问 URL 获取的图像。 有关详细信息，请参阅 [REST API 文档](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa)。

由于图像数据是作为请求正文的一部分进行传递，因此请注意必须将“`Content-Type`”请求头设置为“`application/octet-stream`”。 若要通过 URL 传入图像，请务必将请求头设置为：
```python
header = {'Ocp-Apim-Subscription-Key': subscription_key }
```
创建包含 URL 的字典：
```python
data = {'url': image_url}
```
运行下面的代码，将此类信息传递给 `requests` 库：
```python
requests.post(emotion_recognition_url, headers=headers, json=image_data)
```

首先，从[情感 API](https://azure.microsoft.com/services/cognitive-services/emotion/) 网站下载一些示例图像。


```bash
%%bash
mkdir -p images
curl -Ls https://aka.ms/csnb-emotion-1 -o images/emotion_1.jpg
curl -Ls https://aka.ms/csnb-emotion-2 -o images/emotion_2.jpg
```


```python
image_path = "images/emotion_1.jpg"
image_data = open(image_path, "rb").read()
```


```python
import requests
headers  = {'Ocp-Apim-Subscription-Key': subscription_key, "Content-Type": "application/octet-stream" }
response = requests.post(emotion_recognition_url, headers=headers, data=image_data)
response.raise_for_status()
analysis = response.json()
analysis
```




    [{'faceRectangle': {'height': 162, 'left': 130, 'top': 141, 'width': 162},
      'scores': {'anger': 9.29041e-06,
       'contempt': 0.000118981574,
       'disgust': 3.15619363e-05,
       'fear': 0.000589638,
       'happiness': 0.06630674,
       'neutral': 0.00555004273,
       'sadness': 7.44669524e-06,
       'surprise': 0.9273863}}]



返回的 JSON 对象包含已识别人脸的范围框，以及检测到的情感。 每种情感都与介于 0 和 1 之间的分数相关联，分数越高，就越能表明情感存在。

下面的代码行使用 `matplotlib` 库，检测到了图像中人脸上的情感。 为了保持整齐有序，仅显示前三种情感。


```python
%matplotlib inline
import matplotlib.pyplot as plt
from matplotlib.patches import Rectangle
from io import BytesIO
from PIL import Image

plt.figure(figsize=(5,5))

image  = Image.open(BytesIO(image_data))
ax     = plt.imshow(image, alpha=0.6)

for face in analysis:
    fr = face["faceRectangle"]
    em = face["scores"]
    origin = (fr["left"], fr["top"])
    p = Rectangle(origin, fr["width"], fr["height"], fill=False, linewidth=2, color='b')
    ax.axes.add_patch(p)
    ct = "\n".join(["{0:<10s}{1:>.4f}".format(k,v) for k, v in sorted(list(em.items()),key=lambda r: r[1], reverse=True)][:3])
    plt.text(origin[0], origin[1], ct, fontsize=20)    
_ = plt.axis("off")
```

接下来显示的 `annotate_image` 函数可用于在文件系统路径给定的图像文件的基础上叠加情感。 它依据的是，前面用于调用情感 API 的代码。


```python
def annotate_image(image_path):    
    image_data = open(image_path, "rb").read()
    headers  = {'Ocp-Apim-Subscription-Key': subscription_key, "Content-Type": "application/octet-stream" }
    response = requests.post(emotion_recognition_url, headers=headers, data=image_data)
    response.raise_for_status()
    analysis = response.json()

    plt.figure()

    image  = Image.open(image_path)
    ax     = plt.imshow(image, alpha=0.6)

    for face in analysis:
        fr = face["faceRectangle"]
        em = face["scores"]
        origin = (fr["left"], fr["top"])
        p = Rectangle(origin, fr["width"], fr["height"], fill=False, linewidth=2, color='b')
        ax.axes.add_patch(p)
        ct = "\n".join(["{0:<10s}{1:>.4f}".format(k,v) for k, v in sorted(list(em.items()),key=lambda r: r[1], reverse=True)][:3])
        plt.text(origin[0], origin[1], ct, fontsize=20, va="bottom")    
    _ = plt.axis("off")
```

最后，可以对图像文件调用 `annotate_image` 函数，如下面的代码行所示：


```python
annotate_image("images/emotion_2.jpg")
```
