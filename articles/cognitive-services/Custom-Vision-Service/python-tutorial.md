---
title: 快速入门：使用适用于 Python 的自定义视觉 SDK 创建图像分类项目
titlesuffix: Azure Cognitive Services
description: 使用 Python SDK 创建项目、添加标记、上传图像、训练项目以及进行预测。
services: cognitive-services
author: areddish
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 11/2/2018
ms.author: areddish
ms.openlocfilehash: 551d713d6cc5c6ae8024d4784dbc3a8cca79b401
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/07/2019
ms.locfileid: "55854823"
---
# <a name="quickstart-create-an-image-classification-project-with-the-custom-vision-python-sdk"></a>快速入门：使用自定义视觉 Python SDK 创建图像分类项目

本文提供信息和示例代码，以帮助你开始通过 Python 使用自定义视觉 SDK 来构建图像分类模型。 创建该项目后，可以添加标记、上传图像、定型项目、获取项目的默认预测终结点 URL 并使用终结点以编程方式测试图像。 使用此示例作为构建自己的 Python 应用程序的模板。 若要在不使用代码的情况下了解生成和使用分类模型的过程，请改为查看[基于浏览器的指南](getting-started-build-a-classifier.md)。

## <a name="prerequisites"></a>先决条件

- [Python 2.7+ 或 3.5+](https://www.python.org/downloads/)
- [pip](https://pip.pypa.io/en/stable/installing/) 工具

## <a name="install-the-custom-vision-sdk"></a>安装自定义视觉 SDK

若要安装适用于 Python 的自定义视觉服务 SDK，请在 PowerShell 中运行以下命令：

```PowerShell
pip install azure-cognitiveservices-vision-customvision
```

[!INCLUDE [get-keys](includes/get-keys.md)]

[!INCLUDE [python-get-images](includes/python-get-images.md)]


## <a name="add-the-code"></a>添加代码

在首选项目目录中创建名为 *sample.py* 的新文件。

### <a name="create-the-custom-vision-service-project"></a>创建自定义视觉服务项目

将以下代码添加到脚本中以创建新的自定义视觉服务项目。 在适当的定义中插入订阅密钥。

```Python
from azure.cognitiveservices.vision.customvision.training import CustomVisionTrainingClient
from azure.cognitiveservices.vision.customvision.training.models import ImageUrlCreateEntry

ENDPOINT = "https://southcentralus.api.cognitive.microsoft.com"

# Replace with a valid key
training_key = "<your training key>"
prediction_key = "<your prediction key>"

trainer = CustomVisionTrainingClient(training_key, endpoint=ENDPOINT)

# Create a new project
print ("Creating project...")
project = trainer.create_project("My New Project")
```

### <a name="create-tags-in-the-project"></a>在项目中创建标记

若要在项目中创建分类标记，请将以下代码添加到 *sample.py* 末尾：

```Python
# Make two tags in the new project
hemlock_tag = trainer.create_tag(project.id, "Hemlock")
cherry_tag = trainer.create_tag(project.id, "Japanese Cherry")
```

### <a name="upload-and-tag-images"></a>上传和标记图像

要将示例图像添加到项目，请在创建标记后插入以下代码。 此代码会上传具有相应标记的每个图像。 需根据下载认知服务 Python SDK 示例项目的位置输入基本的图像 URL 路径。

> [!NOTE]
> 需根据此前下载认知服务 Python SDK 示例项目的位置更改图像的路径。

```Python
base_image_url = "<path to project>"

print ("Adding images...")
for image_num in range(1,10):
    image_url = base_image_url + "Images/Hemlock/hemlock_{}.jpg".format(image_num)
    trainer.create_images_from_urls(project.id, [ ImageUrlCreateEntry(url=image_url, tag_ids=[ hemlock_tag.id ] ) ])

for image_num in range(1,10):
    image_url = base_image_url + "Images/Japanese Cherry/japanese_cherry_{}.jpg".format(image_num)
    trainer.create_images_from_urls(project.id, [ ImageUrlCreateEntry(url=image_url, tag_ids=[ cherry_tag.id ] ) ])
```

### <a name="train-the-classifier"></a>训练分类器

此代码在项目中创建第一个迭代，并将其标记为默认迭代。 默认迭代反映了将响应预测请求的模型版本。 每次重新训练模型时都应更新此项。

```Python
import time

print ("Training...")
iteration = trainer.train_project(project.id)
while (iteration.status != "Completed"):
    iteration = trainer.get_iteration(project.id, iteration.id)
    print ("Training status: " + iteration.status)
    time.sleep(1)

# The iteration is now trained. Make it the default project endpoint
trainer.update_iteration(project.id, iteration.id, is_default=True)
print ("Done!")
```

### <a name="get-and-use-the-default-prediction-endpoint"></a>获取并使用默认预测终结点

若要将图像发送到预测终结点并检索预测，请将以下代码添加到文件末尾：

```python
from azure.cognitiveservices.vision.customvision.prediction import CustomVisionPredictionClient

# Now there is a trained endpoint that can be used to make a prediction

predictor = CustomVisionPredictionClient(prediction_key, endpoint=ENDPOINT)

test_img_url = base_image_url + "Images/Test/test_image.jpg"
results = predictor.predict_image_url(project.id, iteration.id, url=test_img_url)

# Display the results.
for prediction in results.predictions:
    print ("\t" + prediction.tag_name + ": {0:.2f}%".format(prediction.probability * 100))
```

## <a name="run-the-application"></a>运行应用程序

运行 *sample.py*。

```PowerShell
python sample.py
```

应用程序的输出应类似于以下文本：

```
Creating project...
Adding images...
Training...
Training status: Training
Training status: Completed
Done!
        Hemlock: 93.53%
        Japanese Cherry: 0.01%
```

然后，可以验证测试图像（在 **<base_image_url>/Images/Test/** 中找到）是否已正确标记。 也可返回到[自定义视觉网站](https://customvision.ai)，查看新创建项目的当前状态。

[!INCLUDE [clean-ic-project](includes/clean-ic-project.md)]

## <a name="next-steps"></a>后续步骤

现在你已了解如何在代码中完成图像分类过程的每一步。 此示例执行单次训练迭代，但通常需要多次训练和测试模型，以使其更准确。

> [!div class="nextstepaction"]
> [测试和重新训练模型](test-your-model.md)
