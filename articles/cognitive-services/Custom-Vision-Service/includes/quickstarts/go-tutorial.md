---
author: areddish
ms.author: areddish
ms.service: cognitive-services
ms.date: 09/15/2020
ms.openlocfilehash: 481c0d85420ab2cc57f5636ed1862a525ace553b
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/16/2020
ms.locfileid: "90604936"
---
本指南提供说明和示例代码，以帮助你开始使用适用于 Go 的自定义视觉客户端库来构建图像分类模型。 你将创建一个项目，添加标记，训练该项目，并使用该项目的预测终结点 URL 以编程方式对其进行测试。 使用此示例作为模板来构建你自己的图像识别应用。

> [!NOTE]
> 若要在不编写代码的情况下构建和训练分类模型，请改为参阅[基于浏览器的指南](../../getting-started-build-a-classifier.md)。

## <a name="prerequisites"></a>先决条件

- [Go 1.8+](https://golang.org/doc/install)
- [!INCLUDE [create-resources](../../includes/create-resources.md)]

## <a name="install-the-custom-vision-client-library"></a>安装自定义视觉客户端库

若要使用适用于 Go 的自定义视觉来编写图像分析应用，需要自定义视觉服务客户端库。 在 PowerShell 中运行以下命令：

```shell
go get -u github.com/Azure/azure-sdk-for-go/...
```

或者，如果使用 `dep`，则在存储库中运行：
```shell
dep ensure -add github.com/Azure/azure-sdk-for-go
```

[!INCLUDE [get-keys](../../includes/get-keys.md)]

[!INCLUDE [python-get-images](../../includes/python-get-images.md)]

## <a name="add-the-code"></a>添加代码

在首选项目目录中创建名为 sample.go** 的新文件。

## <a name="create-the-custom-vision-project"></a>创建自定义视觉项目

将以下代码添加到脚本中以创建新的自定义视觉服务项目。 在适当的定义中插入订阅密钥。 另外，从自定义视觉网站的“设置”页面获取终结点 URL。

请查看 [CreateProject](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.customvision.training.trainings.createproject?view=azure-java-stable#com_microsoft_azure_cognitiveservices_vision_customvision_training_Trainings_createProject_String_CreateProjectOptionalParameter_) 方法，以在创建项目时指定其他选项（在[生成分类器](../../getting-started-build-a-classifier.md) Web 门户指南中进行了说明）。

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
    endpoint string = "<your endpoint URL>"
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

## <a name="create-tags-in-the-project"></a>在项目中创建标记

若要在项目中创建分类标记，请将以下代码添加到 sample.go** 末尾：

```go
// Make two tags in the new project
hemlockTag, _ := trainer.CreateTag(ctx, *project.ID, "Hemlock", "Hemlock tree tag", string(training.Regular))
cherryTag, _ := trainer.CreateTag(ctx, *project.ID, "Japanese Cherry", "Japanese cherry tree tag", string(training.Regular))
```

## <a name="upload-and-tag-images"></a>上传和标记图像

要将示例图像添加到项目，请在创建标记后插入以下代码。 此代码会上传具有相应标记的每个图像。 最多可以在单个批次中上传 64 个图像。

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

## <a name="train-and-publish-the-project"></a>训练并发布项目

此代码创建预测模型的第一个迭代，然后将该迭代发布到预测终结点。 为发布的迭代起的名称可用于发送预测请求。 在发布迭代之前，迭代在预测终结点中不可用。

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

## <a name="use-the-prediction-endpoint"></a>使用预测终结点

若要将图像发送到预测终结点并检索预测，请将以下代码添加到文件末尾：

```go
    fmt.Println("Predicting...")
    predictor := prediction.New(prediction_key, endpoint)

    testImageData, _ := ioutil.ReadFile(path.Join(sampleDataDirectory, "Test", "test_image.jpg"))
    results, _ := predictor.ClassifyImage(ctx, *project.ID, iteration_publish_name, ioutil.NopCloser(bytes.NewReader(testImageData)), "")

    for _, prediction := range *results.Predictions    {
        fmt.Printf("\t%s: %.2f%%", *prediction.TagName, *prediction.Probability * 100)
        fmt.Println("")
    }
}
```

## <a name="run-the-application"></a>运行应用程序

运行 sample.go**。

```shell
go run sample.go
```

应用程序的输出应类似于以下文本：

```console
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

[!INCLUDE [clean-ic-project](../../includes/clean-ic-project.md)]

## <a name="next-steps"></a>后续步骤

现在你已了解如何在代码中完成对象检测过程的每一步。 此示例执行单次训练迭代，但通常需要多次训练和测试模型，以使其更准确。

> [!div class="nextstepaction"]
> [测试和重新训练模型](../../test-your-model.md)

* 什么是自定义视觉？
* [SDK 参考文档（训练）](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/customvision/training)
* [SDK 参考文档（预测）](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.1/customvision/prediction)