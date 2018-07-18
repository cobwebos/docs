---
title: 创建自定义影像服务 Python 教程 - Azure 认知服务 | Microsoft Docs
description: 介绍一款使用 Microsoft 认知服务中的自定义影像 API 的基本 Python 应用。 创建项目、添加标记、上载图像、定型你的项目并使用默认终结点进行预测。
services: cognitive-services
author: areddish
manager: chbuehle
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: article
ms.date: 05/07/2018
ms.author: areddish
ms.openlocfilehash: 0359935bf266d4f2a5cf845dd0d23183f4f77b72
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35366029"
---
# <a name="custom-vision-api-python-tutorial"></a>自定义影像 API Python 教程

了解如何使用自定义影像服务和基本的 Python 脚本创建图像分类项目。 创建该项目后，你可以添加标记、上载图像、定型项目、获取项目的默认预测终结点 URL 并将其用于以编程方式测试图像。 将此开源示例用作通过使用自定义影像 API 构建自己的应用的模板。



## <a name="prerequisites"></a>先决条件

- Python 2.7+ 或 Python 3.5+。
- PIP 工具。

## <a name="get-the-training-and-prediction-keys"></a>获取定型密钥和预测密钥

若要获取此示例中使用的密钥，请访问[自定义影像网页](https://customvision.ai)并选择右上角齿轮图标。 在“帐户”部分中，复制“定型密钥”和“预测密钥”字段中的值。

![密钥 UI 的图像](./media/python-tutorial/training-prediction-keys.png)

## <a name="install-the-custom-vision-service-sdk"></a>安装自定义影像服务 SDK

若要安装自定义影像服务 SDK，请使用以下命令：

```
pip install azure-cognitiveservices-vision-customvision
```

## <a name="get-example-images"></a>获取示例图像

此示例使用 [https://github.com/Microsoft/Cognitive-CustomVision-Windows](https://github.com/Microsoft/Cognitive-CustomVision-Windows/tree/master/Samples/Images) 项目的 `Samples/Images` 目录中的图像。 克隆或下载并将该项目提取到你的开发环境。

## <a name="create-a-custom-vision-service-project"></a>创建自定义影像服务项目

若要创建新的自定义影像服务项目，请创建名为 `sample.py` 的新文件。 将以下代码用作文件内容：

> [!IMPORTANT]
> 将 `training_key` 设置为先前检索到的定型密钥值。
>
> 将 `prediction_key` 设置为先前检索到的预测密钥值。

```python
from azure.cognitiveservices.vision.customvision.training import training_api
from azure.cognitiveservices.vision.customvision.training.models import ImageUrlCreateEntry

# Replace with a valid key
training_key = "<your training key>"
prediction_key = "<your prediction key>"

trainer = training_api.TrainingApi(training_key)

# Create a new project
print ("Creating project...")
project = trainer.create_project("My Project")
```

## <a name="add-tags-to-your-project"></a>将标记添加到你的项目

要将标记添加到你的项目，请将以下代码添加到 `sample.py` 文件的末尾：

```python
# Make two tags in the new project
hemlock_tag = trainer.create_tag(project.id, "Hemlock")
cherry_tag = trainer.create_tag(project.id, "Japanese Cherry")
```

## <a name="upload-images-to-the-project"></a>将图像上载到项目

要将示例图像添加到项目，请在创建标记后插入以下代码。 此代码会上载具有相应标记的图像：

> [!IMPORTANT]
>
> 根据先前下载 Cognitive-CustomVision-Windows 项目的位置更改图像的路径。

```python
base_image_url = "https://raw.githubusercontent.com/Microsoft/Cognitive-CustomVision-Windows/master/Samples/"

print ("Adding images...")
for image_num in range(1,10):
    image_url = base_image_url + "Images/Hemlock/hemlock_{}.jpg".format(image_num)
    trainer.create_images_from_urls(project.id, [ ImageUrlCreateEntry(url=image_url, tag_ids=[ hemlock_tag.id ] ) ])

for image_num in range(1,10):
    image_url = base_image_url + "Images/Japanese Cherry/japanese_cherry_{}.jpg".format(image_num)
    trainer.create_images_from_urls(project.id, [ ImageUrlCreateEntry(url=image_url, tag_ids=[ cherry_tag.id ] ) ])


# Alternatively, if the images were on disk in a folder called Images alongside the sample.py, then
# they can be added by using the following:
#
#import os
#hemlock_dir = "Images\\Hemlock"
#for image in os.listdir(os.fsencode("Images\\Hemlock")):
#    with open(hemlock_dir + "\\" + os.fsdecode(image), mode="rb") as img_data: 
#        trainer.create_images_from_data(project.id, img_data, [ hemlock_tag.id ])
#
#cherry_dir = "Images\\Japanese Cherry"
#for image in os.listdir(os.fsencode("Images\\Japanese Cherry")):
#    with open(cherry_dir + "\\" + os.fsdecode(image), mode="rb") as img_data: 
#        trainer.create_images_from_data(project.id, img_data, [ cherry_tag.id ])
```

## <a name="train-the-project"></a>定型项目

要定型分类器，请将以下代码添加到 `sample.py` 文件的末尾：

```python
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

## <a name="get-and-use-the-default-prediction-endpoint"></a>获取并使用默认预测终结点

若要将图像发送到预测终结点并检索预测，请将以下代码添加到 `sample.py` 文件的末尾：

```python
from azure.cognitiveservices.vision.customvision.prediction import prediction_endpoint
from azure.cognitiveservices.vision.customvision.prediction.prediction_endpoint import models

# Now there is a trained endpoint that can be used to make a prediction

predictor = prediction_endpoint.PredictionEndpoint(prediction_key)

test_img_url = base_image_url + "Images/Test/test_image.jpg"
results = predictor.predict_image_url(project.id, iteration.id, url=test_img_url)

# Alternatively, if the images were on disk in a folder called Images alongside the sample.py, then
# they can be added by using the following.
#
# Open the sample image and get back the prediction results.
# with open("Images\\test\\test_image.jpg", mode="rb") as test_data:
#     results = predictor.predict_image(project.id, test_data, iteration.id)

# Display the results.
for prediction in results.predictions:
    print ("\t" + prediction.tag_name + ": {0:.2f}%".format(prediction.probability * 100))
```

## <a name="run-the-example"></a>运行示例

运行解决方案。 控制台上将显示预测结果。

```
python sample.py
```

应用程序的输出类似于以下文本：

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