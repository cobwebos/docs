---
title: 快速入门：使用适用于 C# 的自定义视觉 SDK 创建对象检测项目
titlesuffix: Azure Cognitive Services
description: 使用 .NET SDK 和 C# 创建项目、添加标记、上传图像、训练项目以及检测对象。
services: cognitive-services
author: areddish
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: quickstart
ms.date: 10/31/2018
ms.author: areddish
ms.openlocfilehash: 926e9feaa5061c84ce8de6d828da820e133700ce
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2018
ms.locfileid: "51278854"
---
# <a name="quickstart-create-an-object-detection-project-with-the-custom-vision-net-sdk"></a>快速入门：使用自定义视觉 .NET SDK 创建对象检测项目

本文提供信息和示例代码，以帮助你开始通过 C# 使用自定义视觉 SDK 来构建对象检测模型。 创建该项目后，可以添加标记的区域、上传图像、训练项目、获取项目的默认预测终结点 URL 并使用终结点以编程方式测试图像。 使用此示例作为构建自己的 .NET 应用程序的模板。 

## <a name="prerequisites"></a>先决条件

- 任何版本的 [Visual Studio 2015 或 2017](https://www.visualstudio.com/downloads/)

## <a name="get-the-custom-vision-sdk-and-sample-code"></a>获取自定义视觉 SDK 和示例代码
若要编写使用自定义视觉的 .NET 应用，需要自定义视觉 NuGet 包。 这些都包括在要下载的示例项目中，但你可以在此处逐个访问它们。

* [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training/)
* [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction/)

克隆或下载[认知服务 .NET 示例](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples)项目。 导航到 **CustomVision/ObjectDetection** 文件夹，在 Visual Studio 中打开 ObjectDetection.csproj_。

此 Visual Studio 项目创建新的名为“我的新项目”的自定义视觉项目，该项目可以通过[自定义视觉网站](https://customvision.ai/)进行访问。 然后，它将上传图像以训练和测试对象检测模型。 在此项目中，将训练模型来检测图像中的叉子和剪刀。

[!INCLUDE [get-keys](includes/get-keys.md)]

## <a name="understand-the-code"></a>了解代码

打开 _Program.cs_ 文件并检查代码。 在 **Main** 方法的相应定义中插入订阅密钥。

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?range=18-27)]

### <a name="create-a-new-custom-vision-service-project"></a>创建新的自定义视觉服务项目

随后的代码创建对象检测项目。 创建的项目将显示在以前访问过的[自定义视觉网站](https://customvision.ai/)上。 

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?range=29-35)]

### <a name="add-tags-to-the-project"></a>将标记添加到项目中

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?range=37-39)]

### <a name="upload-and-tag-images"></a>上传和标记图像

在对象检测项目中标记图像时，需要使用标准化坐标指定每个标记对象的区域。 以下代码将每个示例图像与其标记的区域相关联。

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?range=41-84)]

然后，使用此关联映射上传每个示例图像及其区域坐标。

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?range=86-104)]

现在，所有示例图像都已上传，每个图像都有一个标记（**叉子**或**剪刀**），并且有一个与该标记对应的关联的像素矩形。

### <a name="train-the-project"></a>定型项目

此代码将在项目中创建第一个训练迭代。

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?range=106-117)]

### <a name="set-the-current-iteration-as-default"></a>将当前迭代设置为默认迭代

此代码将当前迭代标记为默认迭代。 默认迭代反映了将响应预测请求的模型版本。 每次重新训练模型时都应更新此版本。

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?range=119-124)]

### <a name="create-a-prediction-endpoint"></a>创建预测终结点

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?range=126-131)]

### <a name="use-the-prediction-endpoint"></a>使用预测终结点

此部分的脚本用于加载测试图像、查询模型终结点，以及将预测数据输出到控制台。

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?range=133-145)]

## <a name="run-the-application"></a>运行应用程序

应用程序运行时，会打开一个控制台窗口并写入以下输出：

```
Creating new project:
        Training
Done!

Making a prediction:
        fork: 98.2% [ 0.111609578, 0.184719115, 0.6607002, 0.6637112 ]
        scissors: 1.2% [ 0.112389535, 0.119195729, 0.658031344, 0.7023591 ]
```
然后，可以验证测试图像（在 **Images/Test/** 中找到）是否已正确标记，并验证检测区域是否正确。 此时，可以按任意键退出应用程序。

[!INCLUDE [clean-od-project](includes/clean-od-project.md)]

## <a name="next-steps"></a>后续步骤

现在你已了解如何在代码中完成对象检测过程的每一步。 此示例执行单次训练迭代，但通常需要多次训练和测试模型，以使其更准确。 以下指南涉及图像分类，但其原理与对象检测类似。

> [!div class="nextstepaction"]
> [测试和重新训练模型](test-your-model.md)