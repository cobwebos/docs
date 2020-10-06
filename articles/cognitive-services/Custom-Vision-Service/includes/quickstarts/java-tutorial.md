---
author: areddish
ms.custom: devx-track-java
ms.author: areddish
ms.service: cognitive-services
ms.date: 09/15/2020
ms.openlocfilehash: f4b64c25bff93683c79aa1aa3eb556988c798cb0
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/16/2020
ms.locfileid: "90604937"
---
本指南提供说明和示例代码，以帮助你开始使用适用于 Java 的自定义视觉客户端库来构建图像分类模型。 你将创建一个项目，添加标记，训练该项目，并使用该项目的预测终结点 URL 以编程方式对其进行测试。 使用此示例作为模板来构建你自己的图像识别应用。

> [!NOTE]
> 若要在不编写代码的情况下构建和训练分类模型，请改为参阅[基于浏览器的指南](../../getting-started-build-a-classifier.md)。

## <a name="prerequisites"></a>先决条件

- 所选 Java IDE
- 已安装 [JDK 7 或 8](https://aka.ms/azure-jdks)。
- 已安装 [Maven](https://maven.apache.org/)
- [!INCLUDE [create-resources](../../includes/create-resources.md)]

## <a name="get-the-custom-vision-client-library"></a>获取自定义视觉客户端库

若要使用适用于 Java 的自定义视觉编写图像分析应用，需要自定义视觉 maven 包。 这些包将包含在要下载的示例项目中，但你可以在此处逐个访问它们。

可在 maven 中央存储库中找到自定义视觉客户端库：

- [定型 SDK](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-training)
- [预测 SDK](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-prediction)

克隆或下载[认知服务 Java SDK 示例](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master)项目。 导航到 **Vision/CustomVision/** 文件夹。

此 Java 项目创建新的名为“示例 Java 项目”____ 的自定义视觉图像分类项目，该项目可以通过[自定义视觉网站](https://customvision.ai/)进行访问。 然后，它上传图像以定型和测试分类器。 在此项目中，分类器用于确定树是铁杉树____ 还是日本樱花树____。

[!INCLUDE [get-keys](../../includes/get-keys.md)]

程序已经过配置，可以将密钥数据作为环境变量引用。 导航到 **Vision/CustomVision** 文件夹，然后输入以下 PowerShell 命令来设置环境变量。 

> [!NOTE]
> 如果使用的是非 Windows 操作系统，请参阅[配置环境变量](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account?tabs=multiservice%2Cwindows#configure-an-environment-variable-for-authentication)，了解相关说明。

```powershell
$env:AZURE_CUSTOMVISION_TRAINING_API_KEY ="<your training api key>"
$env:AZURE_CUSTOMVISION_PREDICTION_API_KEY ="<your prediction api key>"
```

## <a name="examine-the-code"></a>检查代码

在 Java IDE 中加载 `Vision/CustomVision` 项目，打开 _CustomVisionSamples.java_ 文件。 找到 **runSample** 方法，注释掉 **ObjectDetection_Sample** 方法调用&mdash;此方法会执行本指南中未介绍的对象检测方案。 **ImageClassification_Sample** 方法实现本示例的主要功能；请导航到其定义并检查代码。

## <a name="create-a-custom-vision-project"></a>创建自定义视觉项目

这第一段代码创建图像分类项目。 创建的项目将显示在以前访问过的[自定义视觉网站](https://customvision.ai/)上。 请查看 [CreateProject](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.customvision.training.trainings.createproject?view=azure-java-stable#com_microsoft_azure_cognitiveservices_vision_customvision_training_Trainings_createProject_String_CreateProjectOptionalParameter_) 方法重载，以在创建项目时指定其他选项（在[生成分类器](../../getting-started-build-a-classifier.md) Web 门户指南中进行了说明）。

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_create)]

## <a name="create-tags-in-the-project"></a>在项目中创建标记

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_tags)]

## <a name="upload-and-tag-images"></a>上传和标记图像

示例图像包括在项目的 **src/main/resources** 文件夹中。 将它们从该处读取以后，接着就会将其与相应的标记一起上传到服务。

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_upload)]

上一代码片段使用两个帮助程序函数，以资源流的形式检索图像并将其上传到服务（最多可以在单个批次中上传 64 个图像）。

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_helpers)]

## <a name="train-and-publish-the-project"></a>训练并发布项目

此代码创建预测模型的第一个迭代，然后将该迭代发布到预测终结点。 为发布的迭代起的名称可用于发送预测请求。 在发布迭代之前，迭代在预测终结点中不可用。

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_train)]

## <a name="use-the-prediction-endpoint"></a>使用预测终结点

此处的 `predictor` 对象表示的预测终结点是一个引用，用于将图像提交到当前模型并获取分类预测。 在此示例中，`predictor` 在别处使用预测密钥环境变量来定义。

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_predict)]

## <a name="run-the-application"></a>运行应用程序

若要使用 maven 编译并运行解决方案，请在命令提示符下导航到项目目录 (**Vision/CustomVision**)，并执行 run 命令：

```bash
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

[!INCLUDE [clean-ic-project](../../includes/clean-ic-project.md)]

## <a name="next-steps"></a>后续步骤

现在你已了解如何在代码中完成对象检测过程的每一步。 此示例执行单次训练迭代，但通常需要多次训练和测试模型，以使其更准确。

> [!div class="nextstepaction"]
> [测试和重新训练模型](../../test-your-model.md)

* 什么是自定义视觉？
* * [SDK 参考文档](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/customvision?view=azure-java-stable)