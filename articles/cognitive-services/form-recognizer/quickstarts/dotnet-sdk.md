---
title: 快速入门：适用于 .NET 的表单识别器客户端库 | Microsoft Docs
description: 适用于 .NET 的表单识别器客户端库入门。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 07/12/2019
ms.author: pafarley
ms.openlocfilehash: 74bb062713eac44310edcc8d1cdaed605e964681
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2019
ms.locfileid: "72264438"
---
# <a name="quickstart-form-recognizer-client-library-for-net"></a>快速入门：适用于 .NET 的表单识别器客户端库

适用于 .NET 的表单识别器客户端库入门。 表单识别器是一个认知服务，该服务使用机器学习技术从表单文档中识别和提取键值对和表数据。 然后，它会输出包含原始文件中的关系的结构化数据。 请遵循以下步骤安装 SDK 包并试用基本任务的示例代码。

使用适用于 .NET 的表单识别器客户端库可以：

* [训练自定义的表单识别器模型](#train-a-custom-model)
* [获取提取的密钥列表](#get-a-list-of-extracted-keys)
* [使用自定义模型分析表单](#analyze-forms-with-a-custom-model)
* [获取自定义模型列表](#get-a-list-of-custom-models)
* [删除自定义模型](#delete-a-custom-model)

[参考文档](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/formrecognizer?view=azure-dotnet-preview) | [库源代码](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.FormRecognizer) | [包 (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.FormRecognizer/)

## <a name="prerequisites"></a>先决条件

* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/)。
* 有权访问表单识别器受限访问预览版。 若要访问预览版，请填写并提交[表单识别器访问请求](https://aka.ms/FormRecognizerRequestAccess)表单。
* 包含一组训练数据的 Azure 存储 Blob。 有关整理训练数据的提示和选项，请参阅[为自定义模型生成训练数据集](../build-training-data-set.md)。 对于本快速入门，可以使用[示例数据集](https://go.microsoft.com/fwlink/?linkid=2090451)的 **Train** 文件夹下的文件。
* [.NET Core](https://dotnet.microsoft.com/download/dotnet-core) 的当前版本。

## <a name="setting-up"></a>设置

### <a name="create-a-form-recognizer-azure-resource"></a>创建表单识别器 Azure 资源

[!INCLUDE [create resource](../includes/create-resource.md)]

获取试用订阅或资源的密钥后，请为该密钥创建名为 `FORM_RECOGNIZER_KEY` 的[环境变量](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)。

### <a name="create-a-new-c-application"></a>新建 C# 应用程序

在控制台窗口（例如 cmd、PowerShell 或 Bash）中，使用 `dotnet new` 命令创建名为 `formrecognizer-quickstart` 的新控制台应用。 此命令将创建包含单个源文件的简单“Hello World”C# 项目：_Program.cs_。 

```console
dotnet new console -n formrecognizer-quickstart
```

将目录更改为新创建的应用文件夹。 然后使用以下代码生成应用程序：

```console
dotnet build
```

生成输出不应包含警告或错误。 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

在首选的编辑器或 IDE 中，从项目目录打开 _Program.cs_ 文件。 添加以下 `using` 语句：

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_using)]

然后在应用程序的 **Main** 方法中添加以下代码。 稍后将会定义此异步任务。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_main)]

### <a name="install-the-client-library"></a>安装客户端库

在应用程序目录中，使用以下命令安装适用于 .NET 的表单识别器客户端库：

```console
dotnet add package Microsoft.Azure.CognitiveServices.FormRecognizer --version 0.8.0-preview
```

如果你使用的是 Visual Studio IDE，客户端库可用作可下载的 NuGet 包。

## <a name="object-model"></a>对象模型

以下类将处理表单识别器 SDK 的主要功能。

|Name|说明|
|---|---|
|[FormRecognizerClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.formrecognizerclient?view=azure-dotnet-preview)|表单识别器的所有功能都需要此类。 请使用你的订阅信息实例化此类，然后使用它来生成其他类的实例。|
|[TrainRequest](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.trainrequest?view=azure-dotnet-preview)| 使用此类可以通过自己的训练输入数据训练自定义表单识别器模型。 |
|[TrainResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.trainresult?view=azure-dotnet-preview)| 此类提供自定义模型训练操作的结果，包括随后可用于分析表单的模型 ID。 |
|[AnalyzeResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.analyzeresult?view=azure-dotnet-preview)| 此类提供自定义模型分析操作的结果。 它包含 **ExtractedPage** 实例的列表。 |
|[ExtractedPage](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.extractedpage?view=azure-dotnet-preview)| 此类代表从单个表单文档提取的所有数据。|

## <a name="code-examples"></a>代码示例

<!--
    Include code snippets and short descriptions for each task you list in the the bulleted list. Briefly explain each operation, but include enough clarity to explain complex or otherwise tricky operations.

    Include links to the service's reference content when introducing a class for the first time
-->

这些代码片段演示如何使用适用于 .NET 的表单识别器客户端库执行以下任务：

* [对客户端进行身份验证](#authenticate-the-client)
* [训练自定义的表单识别器模型](#train-a-custom-model)
* [获取提取的密钥列表](#get-a-list-of-extracted-keys)
* [使用自定义模型分析表单](#analyze-forms-with-a-custom-model)
* [获取自定义模型列表](#get-a-list-of-custom-models)
* [删除自定义模型](#delete-a-custom-model)

## <a name="define-variables"></a>定义变量

在定义任何方法之前，请将以下变量定义添加到 **Program** 类的顶部。 你需要自行填写一些变量。 

* 可在 Azure 门户中的“概述”部分找到服务的“终结点”值。  
* 若要检索训练数据的 SAS URL，请打开 Microsoft Azure 存储资源管理器，右键单击容器，然后选择“获取共享访问签名”。  确保选中“读取”  和“列表”  权限，然后单击“创建”  。 然后复制 **URL** 部分中的值。 它应当采用 `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>` 形式。
* 如果需要示例表单进行分析，可以使用[示例数据集](https://go.microsoft.com/fwlink/?linkid=2090451)的 **Test** 文件夹下的文件之一。 本指南仅使用 PDF 格式。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_variables)]

## <a name="authenticate-the-client"></a>验证客户端

在 `Main` 方法的下面，定义要在 `Main` 中引用的任务。 此处，你将使用前面定义的订阅变量对客户端对象进行身份验证。 稍后将要定义其他方法。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_maintask)]

## <a name="train-a-custom-model"></a>训练自定义模型

以下方法使用表单识别器客户端对象，基于 Azure Blob 容器中存储的文档训练新的识别模型。 它使用一个帮助器方法显示有关新训练的模型的信息（由 [ModelResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.modelresult?view=azure-dotnet-preview) 对象表示），并返回模型 ID。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_train)]

以下帮助器方法显示有关表单识别器模型的信息。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_displaymodel)]

## <a name="get-a-list-of-extracted-keys"></a>获取提取的键列表

训练完成后，自定义模型将保留已从训练文档中提取的键列表。 它需要将来的表单文档包含这些键，并在“分析”操作中提取它们的对应值。 使用以下方法检索提取的键列表，并将其输出到控制台。 这是一种验证训练过程是否有效的好方法。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_getkeys)]

## <a name="analyze-forms-with-a-custom-model"></a>使用自定义模型分析表单

此方法使用表单识别器客户端和模型 ID 来分析 PDF 表单文档并提取键/值数据。 它使用一个帮助器方法来显示结果（由 [AnalyzeResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.analyzeresult?view=azure-dotnet-preview) 对象表示）。

> [!NOTE]
> 以下方法分析 PDF 表单。 有关分析 JPEG 和 PNG 表单的类似方法，请参阅 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/FormRecognizer) 上的完整示例代码。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_analyzepdf)]

以下帮助器方法显示有关分析操作的信息。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_displayanalyze)]

## <a name="get-a-list-of-custom-models"></a>获取自定义模型列表

可以返回属于你的帐户的所有已训练模型列表，并可以检索有关这些模型的创建时间的信息。 模型列表由 [ModelsResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.modelsresult?view=azure-dotnet-preview) 对象表示。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_getmodellist)]

## <a name="delete-a-custom-model"></a>删除自定义模型

如果要从帐户中删除自定义模型，请使用以下方法：

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_deletemodel)]

## <a name="run-the-application"></a>运行应用程序

从应用程序目录调用 `dotnet run` 命令运行应用程序。

```console
dotnet run
```

## <a name="clean-up-resources"></a>清理资源

如果想要清理并删除认知服务订阅，可以删除资源或资源组。 删除资源组同时也会删除与之相关联的任何其他资源。

* [门户](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

此外，如果你训练了要从帐户中删除的自定义模型，请运行[删除自定义模型](#delete-a-custom-model)中的方法。

## <a name="next-steps"></a>后续步骤

在本快速入门中，你已使用表单识别器 .NET 客户端库训练了一个自定义模型，并分析了表单。 接下来，请了解如何创建更好的训练数据集并生成更准确的模型。

> [!div class="nextstepaction"]
>[生成训练数据集](../build-training-data-set.md)

* [什么是表单识别器？](../overview.md)
* 可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/FormRecognizer) 上找到此示例的源代码。
