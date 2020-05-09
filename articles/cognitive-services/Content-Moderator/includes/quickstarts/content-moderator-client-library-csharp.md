---
title: 内容审查器 .NET 客户端库快速入门
titleSuffix: Azure Cognitive Services
description: 通过本快速入门开始使用适用于 .NET 的内容审查器客户端库。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 01/27/2020
ms.author: pafarley
ms.openlocfilehash: 50572c037cc7927a06bcf8d95305353238989ae9
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "82186511"
---
适用于 .NET 的内容审查器客户端库入门。 请按照以下步骤安装程序包并试用基本任务的示例代码。 内容审查器是一项认知服务，用于检查文本、图像和视频中是否存在可能的冒犯性内容、有风险内容或其他令人不适的内容。 找到此类内容时，此服务会将相应的标签（标记）应用到该内容。 然后，应用会处理标记的内容，使之符合法规的要求，或者为用户维持一个理想的环境。

使用适用于 .NET 的内容审查器客户端库可以：

* [审查文本](#moderate-text)
* [审查图像](#moderate-images)
* [创建评审](#create-a-review)

[参考文档](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/contentmoderator?view=azure-dotnet) | [库源代码](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.ContentModerator) | [包 (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) | [示例](https://docs.microsoft.com/azure/cognitive-services/content-moderator/samples-dotnet)

## <a name="prerequisites"></a>先决条件

* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/)
* [.NET Core](https://dotnet.microsoft.com/download/dotnet-core) 的当前版本。

## <a name="setting-up"></a>设置

### <a name="create-a-content-moderator-azure-resource"></a>创建内容审查器 Azure 资源

Azure 认知服务由你订阅的 Azure 资源表示。 在本地计算机上使用 [Azure 门户](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)或 [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) 创建内容审查器的资源。 你还可以：

* 免费获取在七天内有效的[试用密钥](https://azure.microsoft.com/try/cognitive-services/#decision)。 注册之后，[Azure 网站](https://azure.microsoft.com/try/cognitive-services/my-apis/)上会提供此密钥。  
* 在 [Azure 门户](https://portal.azure.com/)上查看资源

获取试用订阅或资源的密钥后，请为该密钥和终结点 URL [创建环境变量](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)，分别名为 `CONTENT_MODERATOR_SUBSCRIPTION_KEY` 和 `CONTENT_MODERATOR_ENDPOINT`。

### <a name="create-a-new-c-application"></a>新建 C# 应用程序

在首选文本编辑器或 IDE 中创建新的 .NET Core 应用程序。 

在控制台窗口（例如 cmd、PowerShell 或 Bash）中，使用 `dotnet new` 命令创建名为 `content-moderator-quickstart` 的新控制台应用。 此命令将创建包含单个源文件的简单“Hello World”C# 项目：*Program.cs*。

```console
dotnet new console -n content-moderator-quickstart
```

将目录更改为新创建的应用文件夹。 可使用以下代码生成应用程序：

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

在首选的编辑器或 IDE 中，从项目目录打开 *Program.cs* 文件。 添加以下 `using` 语句：

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_using)]

在 Program 类中，为资源的终结点位置创建变量，并将密钥创建为环境变量  。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_creds)]

> [!NOTE]
> 如果在启动应用程序后创建了环境变量，则需要关闭再重新打开运行该应用程序的编辑器、IDE 或 shell 才能访问该变量。

### <a name="install-the-client-library"></a>安装客户端库

在应用程序目录中，通过以下命令安装适用于 .NET 的内容审查器客户端库：

```console
dotnet add package Microsoft.Azure.CognitiveServices.ContentModerator --version 2.0.0
```

如果你使用的是 Visual Studio IDE，客户端库可用作可下载的 NuGet 包。

## <a name="object-model"></a>对象模型

以下类将处理内容审查器 .NET 客户端库的某些主要功能。

|名称|说明|
|---|---|
|[ContentModeratorClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.contentmoderatorclient?view=azure-dotnet)|所有内容审查器功能都需要此类。 请使用你的订阅信息实例化此类，然后使用它来生成其他类的实例。|
|[ImageModeration](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.imagemoderation?view=azure-dotnet)|此类提供用于分析成人内容、个人信息或人脸的功能。|
|[TextModeration](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.textmoderation?view=azure-dotnet)|此类提供用于在文本中分析语言、猥亵内容、错误和个人信息的功能。|
|[评审](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.reviews?view=azure-dotnet)|此类提供评审 API 的功能，包括用于创建作业、自定义工作流和人工评审的方法。|

## <a name="code-examples"></a>代码示例


这些代码片段演示如何使用适用于 .NET 的内容审查器客户端库执行以下任务：

* [对客户端进行身份验证](#authenticate-the-client)
* [审查文本](#moderate-text)
* [审查图像](#moderate-images)
* [创建评审](#create-a-review)

## <a name="authenticate-the-client"></a>验证客户端

在新方法中，使用终结点和密钥实例化客户端对象。 无需为每种方案都使用不同的客户端，但这有助于使代码保持条理性。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_client)]

## <a name="moderate-text"></a>审查文本

以下代码使用内容审查器客户端分析文本的正文，并将结果输出到控制台。 在 Program 类的根中，定义输入和输出文件  ：

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_text_vars)]

然后，在项目的根下添加一个 TextFile.txt 文件  。 将你自己的文本添加到此文件中，或使用以下示例文本：

```
Is this a grabage email abcdef@abcd.com, phone: 4255550111, IP: 255.255.255.255, 1234 Main Boulevard, Panapolis WA 96555.
Crap is the profanity here. Is this information PII? phone 4255550111
```

将以下方法调用添加到 `Main` 方法：

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_textmod_call)]

然后在 Program 类中的某个位置定义文本审查方法  ：

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_textmod)]

## <a name="moderate-images"></a>审查图像

以下代码使用内容审查器客户端和 [ImageModeration](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.imagemoderation?view=azure-dotnet) 对象，分析远程图像中的成人内容和猥亵内容。

> [!NOTE]
> 还可以分析本地图像的内容。 有关使用本地图像的方法和操作，请参阅[参考文档](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.imagemoderation.evaluatefileinputwithhttpmessagesasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_ContentModerator_ImageModeration_EvaluateFileInputWithHttpMessagesAsync_System_IO_Stream_System_Nullable_System_Boolean__System_Collections_Generic_Dictionary_System_String_System_Collections_Generic_List_System_String___System_Threading_CancellationToken_)。

### <a name="get-sample-images"></a>获取示例图像

定义输入和输出文件：

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_image_vars)]

然后，在项目的根下创建输入文件 ImageFiles.txt  。 在此文件中添加要分析的图像的 URL &mdash; 在每行添加一个 URL。 可使用以下示例图像：

```
https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg
https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png
```

将输入和输出文件传递到 `Main` 方法的以下方法调用中。 你将在以后的步骤中定义此方法。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_textmod_call)]

### <a name="define-helper-class"></a>使用帮助器类

在 Program 类中添加以下类定义  。 此内部类将处理图像审查结果。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_dataclass)]

### <a name="define-the-image-moderation-method"></a>定义图像审查方法

以下方法将循环访问文本文件中的图像 URL，创建 EvaluationData 实例，并分析图像中的成人/猥亵内容、文本和人脸  。 然后，它将最终的 EvaluationData 实例添加到列表中，并将返回数据的完整列表写入控制台  。

#### <a name="iterate-through-image-urls"></a>循环访问图像 URL

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_imagemod_iterate)]

#### <a name="analyze-content"></a>分析内容

有关内容审查器屏幕的图像属性的详细信息，请参阅[图像审查概念](../../image-moderation-api.md)指南。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_imagemod_analyze)]

#### <a name="write-moderation-results-to-file"></a>将审查结果写入文件

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_imagemod_save)]

## <a name="create-a-review"></a>创建评审

可使用内容审查器 .NET 客户端库将内容馈送到[评审工具](https://contentmoderator.cognitive.microsoft.com)，使审查人员可以评审该内容。 若要详细了解评审工具，请参阅[评审工具概念指南](../../review-tool-user-guide/human-in-the-loop.md)。

本节中的方法使用 [Reviews](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.reviews?view=azure-dotnet) 类以创建评审、检索评审 ID，并在通过评审工具的 Web 门户收到人工输入后检查评审详细信息。 它会将所有这些信息记录到输出文本文件中。 从 `Main` 方法中调用方法：

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_review_call)]

### <a name="get-sample-images"></a>获取示例图像

在 Program 类的根下声明以下数组  。 此变量引用用于创建评审的示例图像。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_review_urls)]

### <a name="get-review-credentials"></a>获取审阅凭据

登录到[评审工具](https://contentmoderator.cognitive.microsoft.com)并检索团队名称。 然后将其分配给 Program 类中的相应变量  。 或者，可以设置一个回调终结点用于接收有关评审活动的更新。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_review_vars)]

### <a name="define-helper-class"></a>使用帮助器类

将以下类定义添加到 Program 类中  。 此类将用于表示提交到评审工具的单个评审实例。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_review_item)]

### <a name="define-helper-method"></a>定义帮助程序方法

将以下方法添加到 **Program** 类。 此方法将评审查询结果写入输出文本文件。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_writeline)]

### <a name="define-the-review-creation-method"></a>定义评审创建方法

现在，你已准备好定义将处理评审创建和查询的方法。 添加新方法 CreateReviews 并定义以下局部变量  。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_createreview_fields)]

#### <a name="post-reviews-to-the-review-tool"></a>将评审发布到评审工具

然后，添加以下代码以循环访问给定的示例图像，添加元数据，并在单个批中将其发送到评审工具。 

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_createreview_create)]

从 API 调用返回的对象将包含上传的每个图像的唯一 ID 值。 以下代码分析这些 ID，并使用它们来查询内容审查器，以了解批中每个图像的状态。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_createreview_ids)]

### <a name="get-review-details"></a>获取评论详细信息

以下代码可使程序等待用户输入。 当你在运行时到达此步骤时，可直接转到[评审工具](https://contentmoderator.cognitive.microsoft.com)，验证是否已上传示例图像，并与其进行交互。 有关如何与评审进行交互的信息，请参阅[评审操作方法指南](https://docs.microsoft.com/azure/cognitive-services/content-moderator/review-tool-user-guide/review-moderated-images)。 完成后，可按任意键继续执行程序，并检索评审过程的结果。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_createreview_results)]

如果在此方案中使用了回调终结点，应会收到采用以下格式的事件：

```console
{'callback_endpoint': 'https://requestb.in/qmsakwqm',
 'content': '',
 'content_id': '3ebe16cb-31ed-4292-8b71-1dfe9b0e821f',
 'created_by': 'cspythonsdk',
 'metadata': [{'key': 'sc', 'value': 'True'}],
 'review_id': '201901i14682e2afe624fee95ebb248643139e7',
 'reviewer_result_tags': [{'key': 'a', 'value': 'True'},
                          {'key': 'r', 'value': 'True'}],
 'status': 'Complete',
 'sub_team': 'public',
 'type': 'Image'}
```

## <a name="run-the-application"></a>运行应用程序

从应用程序目录使用 `dotnet run` 命令运行应用程序。

```dotnet
dotnet run 
```

## <a name="clean-up-resources"></a>清理资源

如果想要清理并删除认知服务订阅，可以删除资源或资源组。 删除资源组同时也会删除与之相关联的任何其他资源。

* [门户](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>后续步骤

本快速入门已介绍如何使用内容审查器 .NET 库来执行审查任务。 接下来，请阅读概念指南来详细了解图像或其他媒体的审查。

> [!div class="nextstepaction"]
> [图像审查的概念](https://docs.microsoft.com/azure/cognitive-services/content-moderator/image-moderation-api)

* [什么是 Azure 内容审查器？](../../overview.md)
* 可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ContentModerator/Program.cs) 上找到此示例的源代码。
