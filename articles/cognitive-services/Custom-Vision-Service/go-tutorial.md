---
title: 快速入门：使用适用于 Go 的自定义视觉 SDK 创建图像分类项目
titlesuffix: Azure Cognitive Services
description: 使用 Go SDK 创建项目、添加标记、上传图像、训练项目以及进行预测。
services: cognitive-services
author: areddish
manager: daauld
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 03/21/2019
ms.author: areddish
ms.openlocfilehash: 94eacf6815a3fc0b65aa03d5620f19e783139a5e
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593034"
---
# <a name="quickstart-create-an-image-classification-project-with-the-custom-vision-go-sdk"></a>快速入门：使用自定义视觉 Go SDK 创建图像分类项目

本文提供信息和示例代码，以帮助你开始通过 Go 使用自定义视觉 SDK 来构建图像分类模型。 创建该项目后，可以添加标记、上传图像、训练项目、获取项目的已发布预测终结点 URL 并使用终结点以编程方式测试图像。 使用此示例作为构建自己的 Go 应用程序的模板。 若要在不使用代码的情况下了解生成和使用分类模型的过程，  请改为查看[基于浏览器的指南](getting-started-build-a-classifier.md)。

## <a name="prerequisites"></a>先决条件

- [Go 1.8+](https://golang.org/doc/install)

## <a name="install-the-custom-vision-sdk"></a>安装自定义视觉 SDK

若要安装适用于 Go 的自定义视觉服务 SDK，请在 PowerShell 中运行以下命令：

```
go get -u github.com/Azure/azure-sdk-for-go/...
```

或者，如果使用 dep，则在存储库中运行：
```
dep ensure -add github.com/Azure/azure-sdk-for-go
```

[!INCLUDE [get-keys](includes/get-keys.md)]

[!INCLUDE [python-get-images](includes/python-get-images.md)]

## <a name="add-the-code"></a>添加代码

在首选项目目录中创建名为 sample.go  的新文件。

### <a name="create-the-custom-vision-service-project"></a>创建自定义视觉服务项目

将以下代码添加到脚本中以创建新的自定义视觉服务项目。 在适当的定义中插入订阅密钥。

```go
import(
    "context"
    "bytes"
    "fmt"
    "io/ioutil"
    "path"
    "log"
    "time"
    "github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v3.0/customvision/training"
    "github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v3.0/customvision/prediction"
)

var (
    training_key string = "<your training key>"
    prediction_key string = "<your prediction key>"
    prediction_resource_id = "<your prediction resource id>"
    endpoint string = "https://southcentralus.api.cognitive.microsoft.com"
    project_name string = "Go Sample Project"
    iteration_publish_name = "classifyModel"
    sampleDataDirectory = "<path to sample images>"
)

func main() {
    fmt.Println("Creating project...")

    ctx = context.Background()

    trainer := training.New(training_key, endpoint)

    project, err := trainer.CreateProject(ctx, project_name, "sample project", nil, string(training.Multilabel))
    if (err != nil) {
        log.Fatal(err)
    }
```

### <a name="create-tags-in-the-project"></a>在项目中创建标记

若要在项目中创建分类标记，请将以下代码添加到 sample.go  末尾：

```go
    // Make two tags in the new project
    hemlockTag, _ := trainer.CreateTag(ctx, *project.ID, "Hemlock", "Hemlock tree tag", string(training.Regular))
    cherryTag, _ := trainer.CreateTag(ctx, *project.ID, "Japanese Cherry", "Japanese cherry tree tag", string(training.Regular))
```

### <a name="upload-and-tag-images"></a>上传和标记图像

要将示例图像添加到项目，请在创建标记后插入以下代码。 此代码会上传具有相应标记的每个图像。 需根据下载认知服务 Go SDK 示例项目的位置输入基本的图像 URL 路径。

> [!NOTE]
> 需根据此前下载认知服务 Go SDK 示例项目的位置更改图像的路径。

```go
    fmt.Println("Adding images...")
    japaneseCherryImages, err := ioutil.ReadDir(path.Join(sampleDataDirectory, "Japanese Cherry"))
    if err != nil {
        fmt.Println("Error finding Sample images")
    }

    hemLockImages, err := ioutil.ReadDir(path.Join(sampleDataDirectory, "Hemlock"))
    if err != nil {
        fmt.Println("Error finding Sample images")
    }

    for _, file := range hemLockImages {
        imageFile, _ := ioutil.ReadFile(path.Join(sampleDataDirectory, "Hemlock", file.Name()))
        imageData := ioutil.NopCloser(bytes.NewReader(imageFile))

        trainer.CreateImagesFromData(ctx, *project.ID, imageData, []string{ hemlockTag.ID.String() })
    }

    for _, file := range japaneseCherryImages {
        imageFile, _ := ioutil.ReadFile(path.Join(sampleDataDirectory, "Japanese Cherry", file.Name()))
        imageData := ioutil.NopCloser(bytes.NewReader(imageFile))
        trainer.CreateImagesFromData(ctx, *project.ID, imageData, []string{ cherryTag.ID.String() })
    }
```

### <a name="train-the-classifier-and-publish"></a>训练分类器和发布

此代码在项目中创建第一个迭代，然后将该迭代发布到预测终结点。 为发布的迭代起的名称可用于发送预测请求。 在发布迭代之前，迭代在预测终结点中不可用。

```go
    fmt.Println("Training...")
    iteration, _ := trainer.TrainProject(ctx, *project.ID)
    for {
        if *iteration.Status != "Training" {
            break
        }
        fmt.Println("Training status: " + *iteration.Status)
        time.Sleep(1 * time.Second)
        iteration, _ = trainer.GetIteration(ctx, *project.ID, *iteration.ID)
    }
    fmt.Println("Training status: " + *iteration.Status)

    trainer.PublishIteration(ctx, *project.ID, *iteration.ID, iteration_publish_name, prediction_resource_id))
```

### <a name="get-and-use-the-published-iteration-on-the-prediction-endpoint"></a>获取并使用预测终结点上发布的迭代

若要将图像发送到预测终结点并检索预测，请将以下代码添加到文件末尾：

```go
    fmt.Println("Predicting...")
    predictor := prediction.New(prediction_key, endpoint)

    testImageData, _ := ioutil.ReadFile(path.Join(sampleDataDirectory, "Test", "test_image.jpg"))
    results, _ := predictor.ClassifyImage(ctx, *project.ID, iteration_publish_name, ioutil.NopCloser(bytes.NewReader(testImageData)), "")

    for _, prediction := range *results.Predictions {
        fmt.Printf("\t%s: %.2f%%", *prediction.TagName, *prediction.Probability * 100)
        fmt.Println("")
    }
}
```

## <a name="run-the-application"></a>运行应用程序

运行 sample.go  。

```powershell
go run sample.go
```

应用程序的输出应类似于以下文本：

```
Creating project...
Adding images...
Training...
Training status: Training
Training status: Training
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