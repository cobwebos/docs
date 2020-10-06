---
author: areddish
ms.author: areddish
ms.service: cognitive-services
ms.date: 09/15/2020
ms.openlocfilehash: a091222b01669c6b83c599787c61dcd6b62b05d0
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/16/2020
ms.locfileid: "90604939"
---
本指南提供说明和示例代码，以帮助你开始使用适用于 Python 的自定义视觉客户端库来构建图像分类模型。 你将创建一个项目，添加标记，训练该项目，并使用该项目的预测终结点 URL 以编程方式对其进行测试。 使用此示例作为模板来构建你自己的图像识别应用。

> [!NOTE]
> 若要在不编写代码的情况下构建和训练分类模型，请改为参阅[基于浏览器的指南](../../getting-started-build-a-classifier.md)。

## <a name="prerequisites"></a>先决条件

- [Python 2.7+ 或 3.5+](https://www.python.org/downloads/)
- [pip](https://pip.pypa.io/en/stable/installing/) 工具
- [!INCLUDE [create-resources](../../includes/create-resources.md)]

## <a name="install-the-custom-vision-client-library"></a>安装自定义视觉客户端库

若要使用适用于 Python 的自定义视觉来编写图像分析应用，需要自定义视觉客户端库。 在 PowerShell 中运行以下命令：

```powershell
pip install azure-cognitiveservices-vision-customvision
```

[!INCLUDE [get-keys](../../includes/get-keys.md)]

[!INCLUDE [python-get-images](../../includes/python-get-images.md)]

## <a name="add-the-code"></a>添加代码

在首选项目目录中创建名为 *sample.py* 的新文件。

## <a name="create-the-custom-vision-project"></a>创建自定义视觉项目

将以下代码添加到脚本中以创建新的自定义视觉服务项目。 在适当的定义中插入订阅密钥。 另外，从自定义视觉网站的“设置”页面获取终结点 URL。

请查看 [create_project](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.training.operations.customvisiontrainingclientoperationsmixin?view=azure-python#create-project-name--description-none--domain-id-none--classification-type-none--target-export-platforms-none--custom-headers-none--raw-false----operation-config-) 方法，以在创建项目时指定其他选项（在[生成分类器](../../getting-started-build-a-classifier.md) Web 门户指南中进行了说明）。  

```Python
from azure.cognitiveservices.vision.customvision.training import CustomVisionTrainingClient
from azure.cognitiveservices.vision.customvision.training.models import ImageFileCreateBatch, ImageFileCreateEntry
from msrest.authentication import ApiKeyCredentials

ENDPOINT = "<your API endpoint>"

# Replace with a valid key
training_key = "<your training key>"
prediction_key = "<your prediction key>"
prediction_resource_id = "<your prediction resource id>"

publish_iteration_name = "classifyModel"

credentials = ApiKeyCredentials(in_headers={"Training-key": training_key})
trainer = CustomVisionTrainingClient(ENDPOINT, credentials)

# Create a new project
print ("Creating project...")
project = trainer.create_project("My New Project")
```

## <a name="create-tags-in-the-project"></a>在项目中创建标记

若要在项目中创建分类标记，请将以下代码添加到 *sample.py* 末尾：

```Python
# Make two tags in the new project
hemlock_tag = trainer.create_tag(project.id, "Hemlock")
cherry_tag = trainer.create_tag(project.id, "Japanese Cherry")
```

## <a name="upload-and-tag-images"></a>上传和标记图像

要将示例图像添加到项目，请在创建标记后插入以下代码。 此代码会上传具有相应标记的每个图像。 最多可以在单个批次中上传 64 个图像。

> [!NOTE]
> 需根据此前下载认知服务 Python SDK 示例存储库的位置更改图像的路径。

```Python
base_image_url = "<path to repo directory>/cognitive-services-python-sdk-samples/samples/vision/"

print("Adding images...")

image_list = []

for image_num in range(1, 11):
    file_name = "hemlock_{}.jpg".format(image_num)
    with open(base_image_url + "images/Hemlock/" + file_name, "rb") as image_contents:
        image_list.append(ImageFileCreateEntry(name=file_name, contents=image_contents.read(), tag_ids=[hemlock_tag.id]))

for image_num in range(1, 11):
    file_name = "japanese_cherry_{}.jpg".format(image_num)
    with open(base_image_url + "images/Japanese Cherry/" + file_name, "rb") as image_contents:
        image_list.append(ImageFileCreateEntry(name=file_name, contents=image_contents.read(), tag_ids=[cherry_tag.id]))

upload_result = trainer.create_images_from_files(project.id, ImageFileCreateBatch(images=image_list))
if not upload_result.is_batch_successful:
    print("Image batch upload failed.")
    for image in upload_result.images:
        print("Image status: ", image.status)
    exit(-1)
```

## <a name="train-and-publish-the-project"></a>训练并发布项目

此代码创建预测模型的第一个迭代，然后将该迭代发布到预测终结点。 为发布的迭代起的名称可用于发送预测请求。 在发布迭代之前，迭代在预测终结点中不可用。

```Python
import time

print ("Training...")
iteration = trainer.train_project(project.id)
while (iteration.status != "Completed"):
    iteration = trainer.get_iteration(project.id, iteration.id)
    print ("Training status: " + iteration.status)
    time.sleep(1)

# The iteration is now trained. Publish it to the project endpoint
trainer.publish_iteration(project.id, iteration.id, publish_iteration_name, prediction_resource_id)
print ("Done!")
```

## <a name="use-the-prediction-endpoint"></a>使用预测终结点

若要将图像发送到预测终结点并检索预测，请将以下代码添加到文件末尾：

```python
from azure.cognitiveservices.vision.customvision.prediction import CustomVisionPredictionClient
from msrest.authentication import ApiKeyCredentials

# Now there is a trained endpoint that can be used to make a prediction
prediction_credentials = ApiKeyCredentials(in_headers={"Prediction-key": prediction_key})
predictor = CustomVisionPredictionClient(ENDPOINT, prediction_credentials)

with open(base_image_url + "images/Test/test_image.jpg", "rb") as image_contents:
    results = predictor.classify_image(
        project.id, publish_iteration_name, image_contents.read())

    # Display the results.
    for prediction in results.predictions:
        print("\t" + prediction.tag_name +
              ": {0:.2f}%".format(prediction.probability * 100))
```

## <a name="run-the-application"></a>运行应用程序

运行 *sample.py*。

```powershell
python sample.py
```

应用程序的输出应类似于以下文本：

```console
Creating project...
Adding images...
Training...
Training status: Training
Training status: Completed
Done!
        Hemlock: 93.53%
        Japanese Cherry: 0.01%
```

然后，可以验证测试图像（在 **<base_image_url>images/Test/** 中找到）是否已正确标记。 也可返回到[自定义视觉网站](https://customvision.ai)，查看新创建项目的当前状态。

[!INCLUDE [clean-ic-project](../../includes/clean-ic-project.md)]

## <a name="next-steps"></a>后续步骤

现在你已了解如何在代码中完成对象检测过程的每一步。 此示例执行单次训练迭代，但通常需要多次训练和测试模型，以使其更准确。

> [!div class="nextstepaction"]
> [测试和重新训练模型](../../test-your-model.md)

* 什么是自定义视觉？
* [SDK 参考文档](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/customvision?view=azure-python)