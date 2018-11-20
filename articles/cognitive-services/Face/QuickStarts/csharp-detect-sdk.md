---
title: 快速入门：使用 Azure 人脸 .NET SDK 检测图像中的人脸
titleSuffix: Azure Cognitive Services
description: 在本快速入门中，请使用 Azure 人脸 SDK 和 C# 检测图像中的人脸。
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: quickstart
ms.date: 11/07/2018
ms.author: pafarley
ms.openlocfilehash: 4fbbde167a8c895a71ab3614e8c3ecbce26604a9
ms.sourcegitcommit: 0fc99ab4fbc6922064fc27d64161be6072896b21
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2018
ms.locfileid: "51578142"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-face-net-sdk"></a>快速入门：使用人脸 .NET SDK 检测图像中的人脸

在本快速入门中，请使用人脸服务 SDK 和 C# 检测图像中的人脸。 如需本快速入门中代码的工作示例，请查看 GitHub 上的[认知服务视觉 csharp 快速入门](https://github.com/Azure-Samples/cognitive-services-vision-csharp-sdk-quickstarts/tree/master/Face)存储库中的人脸项目。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 

## <a name="prerequisites"></a>先决条件

- 人脸 API 订阅密钥。 可以从[试用认知服务](https://azure.microsoft.com/try/cognitive-services/?api=face-api)获取免费试用的订阅密钥。 或者，按照[创建认知服务帐户](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)中的说明订阅人脸 API 服务并获取密钥。
- 任何版本的 [Visual Studio 2015 或 2017](https://www.visualstudio.com/downloads/)。

## <a name="create-the-visual-studio-project"></a>创建 Visual Studio 项目

1. 在 Visual Studio 中创建新的**控制台应用 (.NET Framework)** 项目并将其命名为 **FaceDetection**。 
1. 如果解决方案中有其他项目，请将此项目选为单一启动项目。
1. 获取所需的 NuGet 包。 在解决方案资源管理器中，右键单击项目并选择“管理 NuGet 包”。 单击“浏览”选项卡，选择“包括预发行版”，然后找到并安装以下包：
    - [Microsoft.Azure.CognitiveServices.Vision.Face 2.2.0-preview](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.2.0-preview)

## <a name="add-face-detection-code"></a>添加人脸检测代码

打开新项目的 *Program.cs* 文件。 在这里，请添加加载图像和检测人脸所需的代码。

### <a name="include-namespaces"></a>包括命名空间

将以下 `using` 语句添加到 *Program.cs* 文件顶部。

[!code-csharp[](~/cognitive-services-vision-csharp-sdk-quickstarts/Face/Program.cs?range=1-7)]

### <a name="add-essential-fields"></a>添加必要的字段

将以下字段添加到 Program 类。 该数据指定如何连接到人脸服务，以及在何处获取输入数据。 需使用订阅密钥的值更新 `subscriptionKey` 字段，并且可能需要更改 `faceEndpoint` 字符串，使之包含正确的区域标识符。 还需将 `localImagePath` 和/或 `remoteImageUrl` 的值设置为路径，使之指向实际的图像文件。

`faceAttributes` 字段只是一个数组，包含特定类型的属性。 它将指定要检索的有关已检测人脸的信息。

[!code-csharp[](~/cognitive-services-vision-csharp-sdk-quickstarts/Face/Program.cs?range=13-34)]

### <a name="create-and-use-the-face-client"></a>创建并使用人脸客户端

接下来，请将以下代码添加到 **Program** 类的 **Main** 方法。 这样会设置人脸 API 客户端。

[!code-csharp[](~/cognitive-services-vision-csharp-sdk-quickstarts/Face/Program.cs?range=38-41)]

另请在 **Main** 方法中添加以下代码，以便使用新创建的人脸客户端来检测远程图像和本地图像中的人脸。 检测方法将随后定义。 

[!code-csharp[](~/cognitive-services-vision-csharp-sdk-quickstarts/Face/Program.cs?range=43-49)]

### <a name="detect-faces"></a>检测人脸

将以下方法添加到 **Program** 类。 它使用人脸服务客户端检测通过 URL 引用的远程图像中的人脸。 请注意，它使用 `faceAttributes` 字段&mdash;添加到 `faceList` 的 **DetectedFace** 对象将具有指定的属性（在此示例中为年龄和性别）。

[!code-csharp[](~/cognitive-services-vision-csharp-sdk-quickstarts/Face/Program.cs?range=52-74)]

以类似方式添加 **DetectLocalAsync** 方法。 它使用人脸服务客户端检测通过文件路径引用的本地图像中的人脸。

[!code-csharp[](~/cognitive-services-vision-csharp-sdk-quickstarts/Face/Program.cs?range=76-101)]

### <a name="retrieve-and-display-face-attributes"></a>检索和显示人脸属性

接下来，定义 **GetFaceAttributes** 方法。 它返回一个字符串，其中包含相关的属性信息。

[!code-csharp[](~/cognitive-services-vision-csharp-sdk-quickstarts/Face/Program.cs?range=103-116)]

最后，定义 **DisplayAttributes** 方法，以便将人脸属性数据写入到控制台输出。

[!code-csharp[](~/cognitive-services-vision-csharp-sdk-quickstarts/Face/Program.cs?range=118-123)]

## <a name="run-the-app"></a>运行应用

成功的响应会显示图像中每张人脸的性别和年龄。 例如：

```
https://upload.wikimedia.org/wikipedia/commons/3/37/Dagestani_man_and_woman.jpg
Male 37   Female 56
```

## <a name="next-steps"></a>后续步骤

在本快速入门中，你创建了一个简单的 .NET 控制台应用程序，该应用程序可以使用人脸 API 服务检测本地图像和远程图像中的人脸。 接下来，请阅读更深入的教程，了解如何以直观的方式将人脸信息显示给用户。

> [!div class="nextstepaction"]
> [教程：创建一个用于检测和分析图像中人脸的 WPF 应用](../Tutorials/FaceAPIinCSharpTutorial.md)
