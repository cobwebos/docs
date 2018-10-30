---
title: 教程：使用适用于 C# 的自定义视觉 SDK 创建图像分类项目
titlesuffix: Azure Cognitive Services
description: 创建项目、添加标记、上传图像、训练项目并使用默认终结点进行预测。
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: tutorial
ms.date: 05/03/2018
ms.author: anroth
ms.openlocfilehash: e046fe452a13384ae7929be805c6252d6ad2fbf9
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/24/2018
ms.locfileid: "49953037"
---
# <a name="tutorial-create-an-image-classification-project-with-the-custom-vision-sdk-for-c"></a>教程：使用适用于 C# 的自定义视觉 SDK 创建图像分类项目

了解如何在 C# 应用程序中使用自定义视觉服务 SDK。 创建该项目后，可以添加标记、上传图像、定型项目、获取项目的默认预测终结点 URL 并使用终结点以编程方式测试图像。 将此开源示例用作通过使用自定义影像服务 API 构建自己的 Windows 应用的模板。

## <a name="prerequisites"></a>先决条件

* 任何适用于 Windows 的 Visual Studio 2017 版本。

## <a name="get-the-custom-vision-sdk-and-samples"></a>获取自定义视觉 SDK 和示例
若要构建此示例，需要具有以下自定义视觉 SDK NuGet 包：

* [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training/)
* [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction/)

你可以下载图像以及 [C# 示例](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/CustomVision)。

## <a name="get-the-training-and-prediction-keys"></a>获取定型和预测密钥

若要获取此示例中使用的密钥，请访问[自定义影像网页](https://customvision.ai)并选择右上角齿轮图标。 在“帐户”部分中，复制“定型密钥”和“预测密钥”字段中的值。

![密钥 UI 的图像](./media/csharp-tutorial/training-prediction-keys.png)

## <a name="understand-the-code"></a>了解代码

在 Visual Studio 中，打开位于 SDK 项目的 `Samples/CustomVision.Sample/` 目录中的项目。

此应用程序使用之前检索到的定型密钥创建一个名为“我的新项目”的新项目。 然后，它上传图像以定型和测试分类器。 分类器标识树是 Hemlock 还是 Japanese Cherry。

以下代码片段实现了本示例的主要功能：

* 创建新的自定义影像服务项目：

    ```csharp
     // Create a new project
    Console.WriteLine("Creating new project:");
    var project = trainingApi.CreateProject("My New Project");
    ```

* 在项目中创建标记：

    ```csharp
    // Make two tags in the new project
    var hemlockTag = trainingApi.CreateTag(project.Id, "Hemlock");
    var japaneseCherryTag = trainingApi.CreateTag(project.Id, "Japanese Cherry");
    ```

* 上传和标记图像：

    ```csharp
    // Add some images to the tags
    Console.WriteLine("\tUploading images");
    LoadImagesFromDisk();

    // Images can be uploaded one at a time
    foreach (var image in hemlockImages)
    {
        using (var stream = new MemoryStream(File.ReadAllBytes(image)))
        {
            trainingApi.CreateImagesFromData(project.Id, stream, new List<string>() { hemlockTag.Id.ToString() });
        }
    }

    // Or uploaded in a single batch 
    var imageFiles = japaneseCherryImages.Select(img => new ImageFileCreateEntry(Path.GetFileName(img), File.ReadAllBytes(img))).ToList();
    trainingApi.CreateImagesFromFiles(project.Id, new ImageFileCreateBatch(imageFiles, new List<Guid>() { japaneseCherryTag.Id }));
    ```

* 定型分类器：

    ```csharp
    // Now there are images with tags start training the project
    Console.WriteLine("\tTraining");
    var iteration = trainingApi.TrainProject(project.Id);

    // The returned iteration will be in progress, and can be queried periodically to see when it has completed
    while (iteration.Status == "Completed")
    {
        Thread.Sleep(1000);

        // Re-query the iteration to get it's updated status
        iteration = trainingApi.GetIteration(project.Id, iteration.Id);
    }
    ```

* 设置预测终结点的默认迭代：

    ```csharp
    // The iteration is now trained. Make it the default project endpoint
    iteration.IsDefault = true;
    trainingApi.UpdateIteration(project.Id, iteration.Id, iteration);
    Console.WriteLine("Done!\n");
    ```

* 创建预测终结点：
 
    ```csharp
    // Create a prediction endpoint, passing in obtained prediction key
    PredictionEndpoint endpoint = new PredictionEndpoint() { ApiKey = predictionKey };
    ```
 
* 将图像发送到预测终结点：

    ```csharp
    // Make a prediction against the new project
    Console.WriteLine("Making a prediction:");
    var result = endpoint.PredictImage(project.Id, testImage);

    // Loop over each prediction and write out the results
    foreach (var c in result.Predictions)
    {
        Console.WriteLine($"\t{c.TagName}: {c.Probability:P1}");
    }
    ```

## <a name="run-the-application"></a>运行应用程序

1. 进行以下更改，以便将定型密钥和预测密钥添加到应用程序：

    * 将定型密钥添加到以下行：

        ```csharp
        string trainingKey = "<your key here>";
        ```

    * 将预测密钥添加到以下行：

        ```csharp
        string predictionKey = "<your key here>";
        ```

2. 运行应用程序。 应用程序运行时，以下输出将写入控制台：

    ```
    Creating new project:
            Uploading images
            Training
    Done!

    Making a prediction:
            Hemlock: 95.0%
            Japanese Cherry: 0.0%
    ```

3. 若要退出应用程序，请按任意键。
