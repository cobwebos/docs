---
title: 快速入门：使用适用于 C# 的自定义视觉 SDK 创建图像分类项目
titlesuffix: Azure Cognitive Services
description: 使用 .NET SDK 和 C# 创建项目、添加标记、上传图像、训练项目以及进行预测。
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 10/31/2018
ms.author: anroth
ms.openlocfilehash: 01c4da98bbc89a49007aa7064907abd9ed01cc0d
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2019
ms.locfileid: "55227533"
---
# <a name="quickstart-create-an-image-classification-project-with-the-custom-vision-net-sdk"></a>快速入门：使用自定义视觉 .NET SDK 创建图像分类项目

本文提供信息和示例代码，以帮助你开始通过 C# 使用自定义视觉 SDK 来构建图像分类模型。 创建该项目后，可以添加标记、上传图像、定型项目、获取项目的默认预测终结点 URL 并使用终结点以编程方式测试图像。 使用此示例作为构建自己的 .NET 应用程序的模板。 若要在不使用代码的情况下了解生成和使用分类模型的过程，请改为查看[基于浏览器的指南](getting-started-build-a-classifier.md)。

## <a name="prerequisites"></a>先决条件
- 任何版本的 [Visual Studio 2015 或 2017](https://www.visualstudio.com/downloads/)


## <a name="get-the-custom-vision-sdk-and-sample-code"></a>获取自定义视觉 SDK 和示例代码
若要编写使用自定义视觉的 .NET 应用，需要自定义视觉 NuGet 包。 这些都包括在要下载的示例项目中，但你可以在此处逐个访问它们。

* [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training/)
* [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction/)

克隆或下载[认知服务 .NET 示例](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples)项目。 导航到 **CustomVision/ImageClassification** 文件夹，在 Visual Studio 中打开 _ImageClassification.csproj_。

此 Visual Studio 项目会新建名为“我的新项目”的自定义视觉项目，该项目可以通过[自定义视觉网站](https://customvision.ai/)进行访问。 然后，它上传图像以定型和测试分类器。 在此项目中，分类器用于确定树是铁杉树还是日本樱花树。

[!INCLUDE [get-keys](includes/get-keys.md)]

## <a name="understand-the-code"></a>了解代码

打开 _Program.cs_ 文件并检查代码。 在 **Main** 方法的相应定义中插入订阅密钥。

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?range=21-30)]

Endpoint 参数应指向创建包含自定义视觉资源的 Azure 资源组的区域。 对于此示例，我们假定“美国中南部”区域，并使用：

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?range=14-14)]

以下代码行执行项目的主要功能。

### <a name="create-a-new-custom-vision-service-project"></a>创建新的自定义视觉服务项目

创建的项目将显示在以前访问过的[自定义视觉网站](https://customvision.ai/)上。 

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?range=32-34)]

### <a name="create-tags-in-the-project"></a>在项目中创建标记

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?range=36-38)]

### <a name="upload-and-tag-images"></a>上传和标记图像

此项目的图像已包括。 它们在 _Program.cs_ 的 **LoadImagesFromDisk** 方法中引用。

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?range=40-55)]

### <a name="train-the-classifier"></a>训练分类器

此代码在项目中创建第一个迭代，并将其标记为默认迭代。 默认迭代反映了将响应预测请求的模型版本。 每次重新训练模型时都应更新此项。

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?range=57-73)]

### <a name="set-the-prediction-endpoint"></a>设置预测终结点

预测终结点是一个引用，可以用于将图像提交到当前模型并获取分类预测。
 
[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?range=77-82)]
 
### <a name="submit-an-image-to-the-default-prediction-endpoint"></a>将图像提交到默认预测终结点

在此脚本中，测试图像加载到 **LoadImagesFromDisk** 方法中，模型的预测输出将显示在控制台中。

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?range=84-92)]

## <a name="run-the-application"></a>运行应用程序

应用程序运行时，会打开一个控制台窗口并写入以下输出：

```
Creating new project:
        Uploading images
        Training
Done!

Making a prediction:
        Hemlock: 95.0%
        Japanese Cherry: 0.0%
```

然后，可以验证测试图像（在 **Images/Test/** 中找到）是否已正确标记。 若要退出应用程序，请按任意键。 也可返回到[自定义视觉网站](https://customvision.ai)，查看新创建项目的当前状态。

[!INCLUDE [clean-ic-project](includes/clean-ic-project.md)]

## <a name="next-steps"></a>后续步骤

现在你已了解如何在代码中完成图像分类过程的每一步。 此示例执行单次训练迭代，但通常需要多次训练和测试模型，以使其更准确。

> [!div class="nextstepaction"]
> [测试和重新训练模型](test-your-model.md)
