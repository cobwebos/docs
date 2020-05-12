---
title: 快速入门：适用于 .NET 的计算机视觉客户端库
description: 在本快速入门中，开始使用适用于 .NET 的计算机视觉客户端库。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: 5a2de6cb4c7de6f789041d8df7ca562e97ee0863
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82595330"
---
<a name="HOLTop"></a>

[参考文档](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/computervision?view=azure-dotnet) | [库源代码](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.ComputerVision) | [包 (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision/) | [示例](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>先决条件

* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/)
* 最新版本的 [.NET Core SDK](https://dotnet.microsoft.com/download/)。

## <a name="setting-up"></a>设置

### <a name="create-a-computer-vision-azure-resource"></a>创建计算机视觉 Azure 资源

Azure 认知服务由你订阅的 Azure 资源表示。 在本地计算机上使用 [Azure 门户](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)或 [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) 创建计算机视觉的资源。 你还可以：

* 免费获取在七天内有效的[试用密钥](https://azure.microsoft.com/try/cognitive-services/#decision)。 注册之后，[Azure 网站](https://azure.microsoft.com/try/cognitive-services/my-apis/)上会提供此密钥。  
* 在 [Azure 门户](https://portal.azure.com/)上查看资源。

获取试用订阅或资源的密钥后，请为该密钥和终结点 URL [创建环境变量](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)，分别名为 `COMPUTER_VISION_SUBSCRIPTION_KEY` 和 `COMPUTER_VISION_ENDPOINT`。

### <a name="create-a-new-c-application"></a>新建 C# 应用程序

在首选编辑器或 IDE 中创建新的 .NET Core 应用程序。 

在控制台窗口（例如 cmd、PowerShell 或 Bash）中，使用 `dotnet new` 命令创建名为 `computer-vision-quickstart` 的新控制台应用。 此命令将创建包含单个源文件的简单“Hello World”C# 项目：ComputerVisionQuickstart.cs  。

```dotnetcli
dotnet new console -n computer-vision-quickstart
```

将目录更改为新创建的应用文件夹。 可使用以下代码生成应用程序：

```dotnetcli
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

在你喜欢使用的编辑器或 IDE 中，打开项目目录中的 ComputerVisionQuickstart.cs  文件。 然后，添加以下 `using` 指令：

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_using)]

在应用程序的 **Program** 类中，为资源的 Azure 终结点和密钥创建变量。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_vars)]

### <a name="install-the-client-library"></a>安装客户端库

在应用程序目录中，使用以下命令安装适用于 .NET 的计算机视觉客户端库：

```dotnetcli
dotnet add package Microsoft.Azure.CognitiveServices.Vision.ComputerVision --version 5.0.0
```

如果你使用的是 Visual Studio IDE，客户端库可用作可下载的 NuGet 包。

## <a name="object-model"></a>对象模型

以下类和接口将处理计算机视觉 .NET SDK 的某些主要功能。

|名称|说明|
|---|---|
| [ComputerVisionClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-dotnet) | 所有计算机视觉功能都需要此类。 可以使用订阅信息实例化此类，然后使用它来执行大多数图像操作。|
|[ComputerVisionClientExtensions](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclientextensions?view=azure-dotnet)| 此类包含 ComputerVisionClient  的其他方法。|
|[VisualFeatureTypes](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-dotnet)| 此枚举定义可在标准分析操作中执行的不同类型的图像分析。 请根据需求指定一组 VisualFeatureTypes 值。 |

## <a name="code-examples"></a>代码示例

这些代码片段演示如何使用适用于 .NET 的计算机视觉客户端库执行以下任务：

* [对客户端进行身份验证](#authenticate-the-client)
* [分析图像](#analyze-an-image)
* [读取印刷体文本和手写文本](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>验证客户端

> [!NOTE]
> 本快速入门假设已经为计算机视觉密钥和终结点（分别名为 `COMPUTER_VISION_SUBSCRIPTION_KEY` 和 `COMPUTER_VISION_ENDPOINT`）[创建了环境变量](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)。

在新方法中，使用终结点和密钥实例化客户端。 使用密钥创建一个 **[ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.apikeyserviceclientcredentials?view=azure-dotnet)** 对象，并在终结点中使用该对象创建一个 **[ComputerVisionClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-dotnet)** 对象。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_auth)]

你可能想要在 `Main` 方法中调用此方法。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_client)]

## <a name="analyze-an-image"></a>分析图像

以下代码定义方法 `AnalyzeImageUrl`，该方法使用客户端对象分析远程图像并输出结果。 该方法返回文本说明、分类、标记列表、检测到的人脸、成人内容标志、主颜色和图像类型。

在 `Main` 方法中添加方法调用。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_analyzeinmain)]

### <a name="set-up-test-image"></a>设置测试图像

在 Program 类中，保存对要分析的图像的 URL 的引用  。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_analyze_url)]

> [!NOTE]
> 还可以分析本地图像。 请参阅 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/ComputerVisionQuickstart.cs) 上的示例代码以了解涉及本地图像的方案。

### <a name="specify-visual-features"></a>指定视觉特性

定义新的图像分析方法。 添加下面的代码，它指定要在分析中提取的视觉特征。 有关完整列表，请参阅 **[VisualFeatureTypes](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-dotnet)** 枚举。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_visualfeatures)]

将以下任一代码块插入 AnalyzeImageUrl  方法中以实现其功能。 请记得在末尾添加右括号。

```csharp
}
```

### <a name="analyze"></a>分析

**AnalyzeImageAsync** 方法将返回包含所有提取信息的 **ImageAnalysis** 对象。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_analyze_call)]

以下部分说明如何详细分析此信息。

### <a name="get-image-description"></a>获取图像说明

下面的代码获取为图像生成的描述文字列表。 有关更多详细信息，请参阅[描述图像](../../concept-describing-images.md)。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_describe)]

### <a name="get-image-category"></a>获取图像类别

下面的代码获取所检测到的图像类别。 有关更多详细信息，请参阅[对图像进行分类](../../concept-categorizing-images.md)。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_categorize)]

### <a name="get-image-tags"></a>获取图像标记

以下代码获取图像中检测到的标记集。 有关更多详细信息，请参阅[内容标记](../../concept-tagging-images.md)。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_tags)]

### <a name="detect-objects"></a>检测物体

以下代码检测图像中的常见物体并将其输出到控制台。 有关更多详细信息，请参阅[物体检测](../../concept-object-detection.md)。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_objects)]

### <a name="detect-brands"></a>检测品牌

以下代码检测图像中的公司品牌和徽标，并将其输出到控制台。 有关更多详细信息，请参阅[品牌检测](../../concept-brand-detection.md)。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_brands)]

### <a name="detect-faces"></a>检测人脸

下面的代码返回图像中检测到的人脸及其矩形坐标，以及选择面属性。 有关更多详细信息，请参阅[人脸检测](../../concept-detecting-faces.md)。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_faces)]

### <a name="detect-adult-racy-or-gory-content"></a>检测成人、色情或血腥内容

以下代码输出图像中检测到的成人内容。 有关更多详细信息，请参阅[成人、色情或血腥内容](../../concept-detecting-adult-content.md)。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_adult)]

### <a name="get-image-color-scheme"></a>获取图像配色方案

以下代码输出图像中检测到的颜色属性，如主色和主题色。 有关更多详细信息，请参阅[配色方案](../../concept-detecting-color-schemes.md)。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_color)]

### <a name="get-domain-specific-content"></a>获取特定于域的内容

计算机视觉可以使用专用模型对图像进行进一步分析。 有关更多详细信息，请参阅[特定于域的内容](../../concept-detecting-domain-content.md)。 

以下代码分析了图像中检测到的名人的相关数据。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_celebs)]

以下代码分析了图像中检测到的地标的相关数据。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_landmarks)]

### <a name="get-the-image-type"></a>获取图像类型

以下代码输出有关图像类型的信息&mdash;无论它是剪贴画还是线条图。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_type)]

## <a name="read-printed-and-handwritten-text"></a>读取印刷体文本和手写文本

计算机视觉可以读取图像中的可见文本，并将其转换为字符流。 本部分中的代码定义了方法 `ExtractTextUrl`，该方法使用客户端对象检测并提取图像中的印刷体文本或手写文本。

在 `Main` 方法中添加方法调用。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_extracttextinmain)]

### <a name="set-up-test-image"></a>设置测试图像

在 **Program** 类中，保存要从中提取文本的图像的 URL 的引用。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_extracttext_url)]

> [!NOTE]
> 还可以从本地图像提取文本。 请参阅 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/ComputerVisionQuickstart.cs) 上的示例代码以了解涉及本地图像的方案。

### <a name="call-the-read-api"></a>调用读取 API

定义用于读取文本的新方法。 添加以下代码，该代码对给定图像调用 **BatchReadFileAsync** 方法。 这会返回一个操作 ID 并启动异步进程来读取图像的内容。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_extract_call)]

### <a name="get-read-results"></a>获取读取结果

接下来，获取从 **BatchReadFileAsync** 调用返回的操作 ID，并使用它查询服务以获取操作结果。 下面的代码每隔一秒钟检查一次操作，直到返回结果。 然后，它将提取的文本数据输出到控制台。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_extract_response)]

### <a name="display-read-results"></a>显示读取结果

添加以下代码来分析和显示检索到的文本数据，并完成方法定义。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_extract_display)]

## <a name="run-the-application"></a>运行应用程序

从应用程序目录使用 `dotnet run` 命令运行应用程序。

```dotnetcli
dotnet run
```

## <a name="clean-up-resources"></a>清理资源

如果想要清理并删除认知服务订阅，可以删除资源或资源组。 删除资源组同时也会删除与之相关联的任何其他资源。

* [门户](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
>[计算机视觉 API 参考 (.NET)](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/computervision?view=azure-dotnet)

* [什么是计算机视觉？](../../Home.md)
* 可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/ComputerVisionQuickstart.cs) 上找到此示例的源代码。
