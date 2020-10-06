---
author: PatrickFarley
ms.author: pafarley
ms.service: cognitive-services
ms.date: 09/15/2020
ms.openlocfilehash: c9f5b5e84955c1974c19d0ccff1a89560fd3e78a
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/16/2020
ms.locfileid: "90604820"
---
本指南提供说明和示例代码，以帮助你开始使用适用于 C# 的自定义视觉客户端库来构建对象检测模型。 你将创建一个项目，添加标记，训练该项目，并使用该项目的预测终结点 URL 以编程方式对其进行测试。 使用此示例作为模板来构建你自己的图像识别应用。

> [!NOTE]
> 若要在不编写代码的情况下构建和训练对象检测模型，请改为参阅[基于浏览器的指南](../../get-started-build-detector.md)。

## <a name="prerequisites"></a>先决条件

- 任何版本的 [Visual Studio 2015 或 2017](https://www.visualstudio.com/downloads/)
- [!INCLUDE [create-resources](../../includes/create-resources.md)]

## <a name="install-the-custom-vision-client-library"></a>安装自定义视觉客户端库

若要使用适用于 .NET 的自定义视觉编写图像分析应用，需要自定义视觉 NuGet 包。 这些包将包含在要下载的示例项目中，但你可以在此处逐个访问它们。

- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training/)
- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction/)

克隆或下载[认知服务 .NET 示例](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples)项目。 导航到 **CustomVision/ObjectDetection** 文件夹，在 Visual Studio 中打开 _ObjectDetection.csproj_。

此 Visual Studio 项目会新建名为“我的新项目”的自定义视觉项目，该项目可以通过[自定义视觉网站](https://customvision.ai/)进行访问。____ 然后，该项目会上传图像来训练和测试对象检测模型。 在此项目中，会训练模型对图像中的叉子和剪刀进行检测。

[!INCLUDE [get-keys](../../includes/get-keys.md)]

## <a name="examine-the-code"></a>检查代码

打开 _Program.cs_ 文件并检查代码。 分别为称作 `CUSTOM_VISION_TRAINING_KEY` 和 `CUSTOM_VISION_PREDICTION_KEY` 的训练和预测键[创建环境变量](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)。 该脚本将查找这些变量。

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_keys)]

另外，从自定义视觉网站的“设置”页面获取终结点 URL。 将其保存到称为 `CUSTOM_VISION_ENDPOINT` 的环境变量。 该脚本在类的根目录保存对它的引用。

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_endpoint)]

## <a name="create-a-new-custom-vision-service-project"></a>创建新的自定义视觉服务项目

随后的代码创建对象检测项目。 创建的项目将显示在以前访问过的[自定义视觉网站](https://customvision.ai/)上。 请查看 [CreateProject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclientextensions.createproject?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_CustomVisionTrainingClientExtensions_CreateProject_Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_ICustomVisionTrainingClient_System_String_System_String_System_Nullable_System_Guid__System_String_System_Collections_Generic_IList_System_String__&preserve-view=true) 方法，以在创建项目时指定其他选项（在[生成检测器](../../get-started-build-detector.md) Web 门户指南中进行了说明）。  

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_create)]


## <a name="add-tags-to-the-project"></a>将标记添加到项目中

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_tags)]

## <a name="upload-and-tag-images"></a>上传和标记图像

在对象检测项目中标记图像时，需要使用标准化坐标指定每个标记对象的区域。 以下代码将每个示例图像与其标记的区域相关联。

> [!NOTE]
> 如果没有用于标记区域坐标的单击并拖动实用工具，则可以使用 [Customvision.ai](https://www.customvision.ai/) 的 Web UI。 在此示例中，已提供坐标。

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_upload_regions)]

然后，使用此关联映射上传每个示例图像及其区域坐标。 最多可以在单个批次中上传 64 个图像。

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_upload)]

至此，你已上传了所有示例图像，并使用关联的像素矩形标记了每个示例图像（**叉子**或**剪刀**）。

## <a name="train-the-project"></a>定型项目

此代码将在项目中创建第一个训练迭代。

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_train)]

> [!TIP]
> 使用选定标记进行训练
>
> 可以选择只对应用的标记的子集进行训练。 如果你还没有应用足够多的特定标记，但是你确实有足够多的其他标记，则可能需要这样做。 在 [TrainProject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclientextensions.trainproject?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_CustomVisionTrainingClientExtensions_TrainProject_Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_ICustomVisionTrainingClient_System_Guid_System_String_System_Nullable_System_Int32__System_Nullable_System_Boolean__System_String_Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_Models_TrainingParameters_&preserve-view=true) 调用中，使用 trainingParameters 参数。 构造一个 [TrainingParameters](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.models.trainingparameters?view=azure-dotnet&preserve-view=true)，并将其 SelectedTags 属性设置为要使用的标记的 ID 列表。 模型将训练成只识别该列表中的标记。

## <a name="publish-the-current-iteration"></a>发布当前迭代

为发布的迭代起的名称可用于发送预测请求。 在发布迭代之前，迭代在预测终结点中不可用。

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_publish)]

## <a name="create-a-prediction-endpoint"></a>创建预测终结点

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_prediction_endpoint)]

## <a name="test-the-prediction-endpoint"></a>测试预测终结点

此部分的脚本用于加载测试图像、查询模型终结点，以及将预测数据输出到控制台。

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_prediction)]

## <a name="run-the-application"></a>运行应用程序

应用程序运行时，会打开一个控制台窗口并写入以下输出：

```console
Creating new project:
        Training
Done!

Making a prediction:
        fork: 98.2% [ 0.111609578, 0.184719115, 0.6607002, 0.6637112 ]
        scissors: 1.2% [ 0.112389535, 0.119195729, 0.658031344, 0.7023591 ]
```

然后，可以验证测试图像（在 **Images/Test/** 中找到）是否已正确标记，并验证检测区域是否正确。 此时，可以按任意键退出应用程序。

[!INCLUDE [clean-od-project](../../includes/clean-od-project.md)]

## <a name="next-steps"></a>后续步骤

现在，你已在代码中完成了对象检测过程的每一步。 此示例执行单次训练迭代，但通常需要多次训练和测试模型，以使其更准确。 以下指南涉及图像分类，但其原理与对象检测类似。

> [!div class="nextstepaction"]
> [测试和重新训练模型](../../test-your-model.md)

* 什么是自定义视觉？
* [SDK 参考文档](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/customvision?view=azure-dotnet)