---
title: 使用 Python 和自定义视觉 API 进行对象检测 - Azure 认知服务 | Microsoft Docs
description: 探究一个使用 Microsoft 认知服务中的自定义视觉 API 的基本 Windows 应用。 创建项目、添加标记、上传图像、训练项目并使用默认终结点进行预测。
services: cognitive-services
author: areddish
manager: chbuehle
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: article
ms.date: 05/03/2018
ms.author: areddish
ms.openlocfilehash: b946265b431a7dcb16bf99e3bf78e09f2d0a7de3
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/21/2018
ms.locfileid: "36301006"
---
# <a name="use-custom-vision-api-to-build-an-object-detection-project-with-python"></a>通过 Python 使用自定义视觉 API 构建对象检测项目

探索一个使用计算机视觉 API 创建对象检测项目的基本 Python 脚本。 创建该项目后，可以添加标记的区域、上传图像、定型项目、获取项目的默认预测终结点 URL 并使用终结点以编程方式测试图像。 将此开源示例用作通过使用自定义视觉 API 构建自己的应用的模板。

## <a name="prerequisites"></a>先决条件

若要使用本教程，需要执行以下操作：

- 安装 Python 2.7 或更高版本，或安装 Python 3.5 或更高版本。
- 安装 pip。

### <a name="platform-requirements"></a>平台要求
已针对 Python 开发此示例。

### <a name="get-the-custom-vision-sdk"></a>获取自定义视觉 SDK

若要构建此示例，需要为自定义视觉 API 安装 Python SDK：

```
pip install azure-cognitiveservices-vision-customvision
```

可以下载图像以及 [Python 示例](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)。

## <a name="step-1-get-the-training-and-prediction-keys"></a>步骤 1：获取定型和预测密钥

若要获取此示例中使用的密钥，请访问[自定义视觉站点](https://customvision.ai)并选择右上角的__齿轮图标__。 在“帐户”部分中，复制“定型密钥”和“预测密钥”字段中的值。

![密钥 UI 的图像](./media/python-tutorial/training-prediction-keys.png)

本示例使用[此位置](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/tree/master/samples/vision/images)中的图像。

## <a name="step-2-create-a-custom-vision-service-project"></a>步骤 2：创建自定义影像服务项目

若要新建自定义影像服务项目，请创建 sample.py 脚本文件并添加以下内容。 请注意，创建对象检测和图像分类项目之间的区别是为 create_project 调用指定的域。

```Python
from azure.cognitiveservices.vision.customvision.training import training_api
from azure.cognitiveservices.vision.customvision.training.models import ImageFileCreateEntry, Region

# Replace with a valid key
training_key = "<your training key>"
prediction_key = "<your prediction key>"

trainer = training_api.TrainingApi(training_key)

# Find the object detection domain
obj_detection_domain = next(domain for domain in trainer.get_domains() if domain.type == "ObjectDetection")

# Create a new project
print ("Creating project...")
project = trainer.create_project("My Detection Project", domain_id=obj_detection_domain.id)
```

## <a name="step-3-add-tags-to-your-project"></a>步骤 3：向项目中添加标记

若要向项目中添加标记，请插入以下代码以创建两个标记：

```Python
# Make two tags in the new project
fork_tag = trainer.create_tag(project.id, "fork")
scissors_tag = trainer.create_tag(project.id, "scissors")
```

## <a name="step-4-upload-images-to-the-project"></a>步骤 4：将图像上传到项目

对于对象检测项目，需要上传图像、区域和标记。 该区域处于标准化坐标中，并指定标记对象的位置。

若要将图像、区域和标记添加到项目，请在创建标记后插入以下代码。 请注意，对于本教程，区域与代码进行内联硬编码。 区域指定标准化坐标中的边界框。

```Python

fork_image_regions = {
    "fork_1": [ 0.145833328, 0.3509314, 0.5894608, 0.238562092 ],
    "fork_2": [ 0.294117659, 0.216944471, 0.534313738, 0.5980392 ],
    "fork_3": [ 0.09191177, 0.0682516545, 0.757352948, 0.6143791 ],
    "fork_4": [ 0.254901975, 0.185898721, 0.5232843, 0.594771266 ],
    "fork_5": [ 0.2365196, 0.128709182, 0.5845588, 0.71405226 ],
    "fork_6": [ 0.115196079, 0.133611143, 0.676470637, 0.6993464 ],
    "fork_7": [ 0.164215669, 0.31008172, 0.767156839, 0.410130739 ],
    "fork_8": [ 0.118872553, 0.318251669, 0.817401946, 0.225490168 ],
    "fork_9": [ 0.18259804, 0.2136765, 0.6335784, 0.643790841 ],
    "fork_10": [ 0.05269608, 0.282303959, 0.8088235, 0.452614367 ],
    "fork_11": [ 0.05759804, 0.0894935, 0.9007353, 0.3251634 ],
    "fork_12": [ 0.3345588, 0.07315363, 0.375, 0.9150327 ],
    "fork_13": [ 0.269607842, 0.194068655, 0.4093137, 0.6732026 ],
    "fork_14": [ 0.143382356, 0.218578458, 0.7977941, 0.295751631 ],
    "fork_15": [ 0.19240196, 0.0633497, 0.5710784, 0.8398692 ],
    "fork_16": [ 0.140931368, 0.480016381, 0.6838235, 0.240196079 ],
    "fork_17": [ 0.305147052, 0.2512582, 0.4791667, 0.5408496 ],
    "fork_18": [ 0.234068632, 0.445702642, 0.6127451, 0.344771236 ],
    "fork_19": [ 0.219362751, 0.141781077, 0.5919118, 0.6683006 ],
    "fork_20": [ 0.180147052, 0.239820287, 0.6887255, 0.235294119 ]
}

scissors_image_regions = {
    "scissors_1": [ 0.4007353, 0.194068655, 0.259803921, 0.6617647 ],
    "scissors_2": [ 0.426470578, 0.185898721, 0.172794119, 0.5539216 ],
    "scissors_3": [ 0.289215684, 0.259428144, 0.403186262, 0.421568632 ],
    "scissors_4": [ 0.343137264, 0.105833367, 0.332107842, 0.8055556 ],
    "scissors_5": [ 0.3125, 0.09766343, 0.435049027, 0.71405226 ],
    "scissors_6": [ 0.379901975, 0.24308826, 0.32107842, 0.5718954 ],
    "scissors_7": [ 0.341911763, 0.20714055, 0.3137255, 0.6356209 ],
    "scissors_8": [ 0.231617644, 0.08459154, 0.504901946, 0.8480392 ],
    "scissors_9": [ 0.170343131, 0.332957536, 0.767156839, 0.403594762 ],
    "scissors_10": [ 0.204656869, 0.120539248, 0.5245098, 0.743464053 ],
    "scissors_11": [ 0.05514706, 0.159754932, 0.799019635, 0.730392158 ],
    "scissors_12": [ 0.265931368, 0.169558853, 0.5061275, 0.606209159 ],
    "scissors_13": [ 0.241421565, 0.184264734, 0.448529422, 0.6830065 ],
    "scissors_14": [ 0.05759804, 0.05027781, 0.75, 0.882352948 ],
    "scissors_15": [ 0.191176474, 0.169558853, 0.6936275, 0.6748366 ],
    "scissors_16": [ 0.1004902, 0.279036, 0.6911765, 0.477124184 ],
    "scissors_17": [ 0.2720588, 0.131977156, 0.4987745, 0.6911765 ],
    "scissors_18": [ 0.180147052, 0.112369314, 0.6262255, 0.6666667 ],
    "scissors_19": [ 0.333333343, 0.0274019931, 0.443627447, 0.852941155 ],
    "scissors_20": [ 0.158088237, 0.04047389, 0.6691176, 0.843137264 ]
}

# Go through the data table above and create the images
print ("Adding images...")
tagged_images_with_regions = []

for file_name in fork_image_regions.keys():
    x,y,w,h = fork_image_regions[file_name]
    regions = [ Region(tag_id=fork_tag.id, left=x,top=y,width=w,height=h) ]

    with open("images/fork/" + file_name + ".jpg", mode="rb") as image_contents:
        tagged_images_with_regions.append(ImageFileCreateEntry(name=file_name, contents=image_contents.read(), regions=regions))

for file_name in scissors_image_regions.keys():
    x,y,w,h = scissors_image_regions[file_name]
    regions = [ Region(tag_id=scissors_tag.id, left=x,top=y,width=w,height=h) ]

    with open("images/scissors/" + file_name + ".jpg", mode="rb") as image_contents:
        tagged_images_with_regions.append(ImageFileCreateEntry(name=file_name, contents=image_contents.read(), regions=regions))


trainer.create_images_from_files(project.id, images=tagged_images_with_regions)
```

## <a name="step-5-train-the-project"></a>步骤 5：训练项目

现在，已向项目中添加了标记和图像，可以对其进行训练了： 

1. 插入以下代码。 这将在项目中创建第一个迭代。 
2. 将此迭代标记为默认迭代。

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

## <a name="step-6-get-and-use-the-default-prediction-endpoint"></a>步骤 6：获取并使用默认预测终结点

现在，你已准备就绪，可以使用模型进行预测了： 

1. 获取与默认迭代关联的终结点。 
2. 使用该终结点向项目发送一个测试图像。

```Python
from azure.cognitiveservices.vision.customvision.prediction import prediction_endpoint
from azure.cognitiveservices.vision.customvision.prediction.prediction_endpoint import models

# Now there is a trained endpoint that can be used to make a prediction

predictor = prediction_endpoint.PredictionEndpoint(prediction_key)

# Open the sample image and get back the prediction results.
with open("images/test/test_image.jpg", mode="rb") as test_data:
    results = predictor.predict_image(project.id, test_data, iteration.id)

# Display the results.
for prediction in results.predictions:
    print ("\t" + prediction.tag_name + ": {0:.2f}%".format(prediction.probability * 100), prediction.bounding_box.left, prediction.bounding_box.top, prediction.bounding_box.width, prediction.bounding_box.height)
```

## <a name="step-7-run-the-example"></a>步骤 7：运行示例

运行解决方案。 预测结果将显示在控制台上。

```
python sample.py
```
