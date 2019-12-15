---
title: 快速入门：使用适用于 Java 的自定义视觉 SDK 创建图像分类项目
titleSuffix: Azure Cognitive Services
description: 使用 Java SDK 创建项目、添加标记、上传图像、训练项目以及进行预测。
services: cognitive-services
author: areddish
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 12/05/2019
ms.author: areddish
ms.openlocfilehash: 9e4ac74df269f2e3db2431a3f01490a5769c9aca
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2019
ms.locfileid: "74970238"
---
# <a name="quickstart-create-an-image-classification-project-with-the-custom-vision-sdk-for-java"></a>快速入门：使用适用于 Java 的自定义视觉 SDK 创建图像分类项目

本文介绍如何开始使用自定义视觉 Java SDK 来构建图像分类模型。 创建该项目后，可以添加标记、上传图像、定型项目、获取项目的默认预测终结点 URL 并使用终结点以编程方式测试图像。 使用此示例作为构建自己的 Java 应用程序的模板。 若要在不使用代码的情况下了解生成和使用分类模型的过程，  请改为查看[基于浏览器的指南](getting-started-build-a-classifier.md)。

## <a name="prerequisites"></a>先决条件

- 所选 Java IDE
- 已安装 [JDK 7 或 8](https://aka.ms/azure-jdks)。
- 已安装 Maven
- [!INCLUDE [create-resources](includes/create-resources.md)]

## <a name="get-the-custom-vision-sdk-and-sample-code"></a>获取自定义视觉 SDK 和示例代码

若要编写使用自定义视觉的 Java 应用，需要自定义视觉 maven 包。 这些包将包含在要下载的示例项目中，但你可以在此处逐个访问它们。

可从 maven 中央存储库安装自定义影像 SDK：

- [定型 SDK](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-training)
- [预测 SDK](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-prediction)

克隆或下载[认知服务 Java SDK 示例](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master)项目。 导航到 **Vision/CustomVision/** 文件夹。

此 Java 项目创建新的名为“示例 Java 项目”  的自定义视觉图像分类项目，该项目可以通过[自定义视觉网站](https://customvision.ai/)进行访问。 然后，它上传图像以定型和测试分类器。 在此项目中，分类器用于确定树是铁杉树  还是日本樱花树  。

[!INCLUDE [get-keys](includes/get-keys.md)]

程序已经过配置，可以将密钥数据存储为环境变量。 导航到 PowerShell 中的 **Vision/CustomVision** 文件夹，设置这些变量。 然后输入命令：

```powershell
$env:AZURE_CUSTOMVISION_TRAINING_API_KEY ="<your training api key>"
$env:AZURE_CUSTOMVISION_PREDICTION_API_KEY ="<your prediction api key>"
```

## <a name="understand-the-code"></a>了解代码

在 Java IDE 中加载 `Vision/CustomVision` 项目，打开 _CustomVisionSamples.java_ 文件。 找到 **runSample** 方法，注释掉 **ObjectDetection_Sample** 方法调用&mdash;此方法会执行本指南中未介绍的对象检测方案。 **ImageClassification_Sample** 方法实现本示例的主要功能；请导航到其定义并检查代码。

### <a name="create-a-custom-vision-service-project"></a>创建自定义影像服务项目

这第一段代码创建图像分类项目。 创建的项目将显示在以前访问过的[自定义视觉网站](https://customvision.ai/)上。 请查看 [CreateProject](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.customvision.training.trainings.createproject?view=azure-java-stable#com_microsoft_azure_cognitiveservices_vision_customvision_training_Trainings_createProject_String_CreateProjectOptionalParameter_) 方法重载，以在创建项目时指定其他选项（在[生成分类器](getting-started-build-a-classifier.md) Web 门户指南中进行了说明）。

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_create)]

### <a name="create-tags-in-the-project"></a>在项目中创建标记

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_tags)]

### <a name="upload-and-tag-images"></a>上传和标记图像

示例图像包括在项目的 **src/main/resources** 文件夹中。 将它们从该处读取以后，接着就会将其与相应的标记一起上传到服务。

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_upload)]

上一代码片段使用两个帮助程序函数，以资源流的形式检索图像并将其上传到服务（最多可以在单个批次中上传 64 个图像）。

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_helpers)]

### <a name="train-the-classifier-and-publish"></a>训练分类器和发布

此代码在项目中创建第一个迭代，然后将该迭代发布到预测终结点。 为发布的迭代起的名称可用于发送预测请求。 在发布迭代之前，迭代在预测终结点中不可用。

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_train)]

### <a name="use-the-prediction-endpoint"></a>使用预测终结点

此处的 `predictor` 对象表示的预测终结点是一个引用，用于将图像提交到当前模型并获取分类预测。 在此示例中，`predictor` 在别处使用预测密钥环境变量来定义。

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_predict)]

## <a name="run-the-application"></a>运行应用程序

若要使用 maven 来编译和运行解决方案，请在 PowerShell 的项目目录中运行以下命令：

```powershell
mvn compile exec:java
```

应用程序的控制台输出看起来应类似于以下文本：

```console
Creating project...
Adding images...
Adding image: hemlock_1.jpg
Adding image: hemlock_2.jpg
Adding image: hemlock_3.jpg
Adding image: hemlock_4.jpg
Adding image: hemlock_5.jpg
Adding image: hemlock_6.jpg
Adding image: hemlock_7.jpg
Adding image: hemlock_8.jpg
Adding image: hemlock_9.jpg
Adding image: hemlock_10.jpg
Adding image: japanese_cherry_1.jpg
Adding image: japanese_cherry_2.jpg
Adding image: japanese_cherry_3.jpg
Adding image: japanese_cherry_4.jpg
Adding image: japanese_cherry_5.jpg
Adding image: japanese_cherry_6.jpg
Adding image: japanese_cherry_7.jpg
Adding image: japanese_cherry_8.jpg
Adding image: japanese_cherry_9.jpg
Adding image: japanese_cherry_10.jpg
Training...
Training status: Training
Training status: Training
Training status: Completed
Done!
        Hemlock: 93.53%
        Japanese Cherry: 0.01%
```

然后，可以验证测试图像预测（输出的最后几行）是否正确。

[!INCLUDE [clean-ic-project](includes/clean-ic-project.md)]

## <a name="next-steps"></a>后续步骤

现在你已了解如何在代码中完成图像分类过程的每一步。 此示例执行单次训练迭代，但通常需要多次训练和测试模型，以使其更准确。

> [!div class="nextstepaction"]
> [测试和重新训练模型](test-your-model.md)
