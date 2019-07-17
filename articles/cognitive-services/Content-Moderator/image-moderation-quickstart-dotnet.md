---
title: 快速入门：使用 C# 分析图像中是否存在令人反感的内容 - 内容审查器
titlesuffix: Azure Cognitive Services
description: 如何使用适用于 .NET 的内容审查器 SDK 分析图像内容中是否存在各种令人反感的材料
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 07/03/2019
ms.author: sajagtap
ms.openlocfilehash: d9a28dcd4af68cf9c00eb3d338c4bd83c8d89ecc
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/05/2019
ms.locfileid: "67604123"
---
# <a name="quickstart-analyze-images-for-objectionable-content-in-c"></a>快速入门：使用 C# 分析图像中是否存在令人反感的内容

本文中的信息和代码示例有助于你完成[适用于 .NET 的内容审查器 SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) 的使用入门。 本文介绍如何通过扫描方式查找成人或不雅内容、可提取的文本以及人脸，以便审查是否存在可能会令人反感的材料。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 

## <a name="prerequisites"></a>先决条件

- 内容审查器的订阅密钥。 按照[创建认知服务帐户](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)中的说明订阅内容审查器并获取密钥。
- 任何版本的 [Visual Studio 2015 或 2017](https://www.visualstudio.com/downloads/)


> [!NOTE]
> 本指南使用免费层内容审查器订阅。 若要了解为每个订阅层提供的内容，请参阅[定价和限制](https://azure.microsoft.com/pricing/details/cognitive-services/content-moderator/)页。

## <a name="create-the-visual-studio-project"></a>创建 Visual Studio 项目

1. 在 Visual Studio 中创建新的**控制台应用 (.NET Framework)** 项目并将其命名为 **ImageModeration**。 
1. 如果解决方案中有其他项目，请将此项目选为单一启动项目。
1. 获取所需的 NuGet 包。 右键单击解决方案资源管理器中的项目，选择“管理 NuGet 包”，然后找到并安装以下包： 
    - Microsoft.Azure.CognitiveServices.ContentModerator
    - Microsoft.Rest.ClientRuntime
    - Newtonsoft.Json

## <a name="add-image-moderation-code"></a>添加图像审查代码

接下来，需将代码从本指南复制并粘贴到项目中，以便实施基本的内容审查方案。

### <a name="include-namespaces"></a>包括命名空间

将以下 `using` 语句添加到 *Program.cs* 文件顶部。

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/image-moderation-quickstart-dotnet.cs?range=1-8)]

### <a name="create-the-content-moderator-client"></a>Create the Content Moderator client

向 *Program.cs* 文件添加以下代码，为订阅创建内容审查器客户端提供程序。 在同一命名空间中添加此代码和 **Program** 类。 还需使用区域标识符和订阅密钥的值更新  AzureRegion 和 CMSubscriptionKey  字段。

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/image-moderation-quickstart-dotnet.cs?range=84-107)]


### <a name="set-up-input-and-output-targets"></a>设置输入和输出目标

向 Program.cs 中的 Program 类添加以下静态字段   。 这些字段指定输入图像内容和输出 JSON 内容的文件。

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/image-moderation-quickstart-dotnet.cs?range=49-53)]

需创建 *_ImageFiles.txt* 输入文件并相应地更新其路径（相对路径相对于执行目录）。 打开 _ImageFiles.txt_，添加要审查的图像的 URL。 本快速入门使用以 URL 作为其示例输入。

```
https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg
https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png
```

### <a name="create-a-class-to-handle-results"></a>创建用于处理结果的类

在同一命名空间中将以下代码和 **Program** 类添加到 *Program.cs*。 将使用此类的实例来记录每个已审阅图像的审查结果。

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/image-moderation-quickstart-dotnet.cs?range=109-124)]


### <a name="define-the-image-evaluation-method"></a>定义图像评估方法

将以下方法添加到 **Program** 类。 此方法以三种不同的方式评估单张图像，并返回评估结果。 若要详细了解单个操作的功能，请单击[后续步骤](#next-steps)部分的链接。

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/image-moderation-quickstart-dotnet.cs?range=55-81)]

### <a name="load-the-input-images"></a>加载输入图像

在 **Program** 类的 **Main** 方法中添加以下代码。 此代码会将程序设置为检索输入文件中每个图像 URL 的评估数据， 然后会将该数据写入单个输出文件中。

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/image-moderation-quickstart-dotnet.cs?range=17-46)]

## <a name="run-the-program"></a>运行程序

程序会将 JSON 字符串数据写入 _ModerationOutput.json_ 文件。 本快速入门中使用的示例图像的输出如下。 每个图像有对应于 `ImageModeration`、`FaceDetection` 和 `TextDetection` 的不同部分，对应于 **EvaluateImage** 方法中的三个 API 调用。

```json
[{
  "ImageUrl": "https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg",
  "ImageModeration": {
    "cacheID": "7733c303-3b95-4710-a41e-7a322ae81a15_636488005858745661",
    "result": false,
    "trackingId": "WE_e1f20803b4ed471fb5de7df551f5bd9f_ContentModerator.Preview_687c356d-0f00-4aeb-ae5f-c7555af80247",
    "adultClassificationScore": 0.019196987152099609,
    "isImageAdultClassified": false,
    "racyClassificationScore": 0.032390203326940536,
    "isImageRacyClassified": false,
    "advancedInfo": [
      {
        "key": "ImageDownloadTimeInMs",
        "value": "116"
      },
      {
        "key": "ImageSizeInBytes",
        "value": "273405"
      }
    ],
    "status": {
      "code": 3000.0,
      "description": "OK",
      "exception": null
    }
  },
  "TextDetection": {
    "status": {
      "code": 3000.0,
      "description": "OK",
      "exception": null
    },
    "metadata": [
      {
        "key": "ImageDownloadTimeInMs",
        "value": "12"
      },
      {
        "key": "ImageSizeInBytes",
        "value": "273405"
      }
    ],
    "trackingId": "WE_e1f20803b4ed471fb5de7df551f5bd9f_ContentModerator.Preview_814fa162-c5d6-4ca6-997b-30ed0686ca83",
    "cacheId": "3fb69496-c64b-4de9-affd-6dd6d23f3e78_636488005876558920",
    "language": "eng",
    "text": "IF WE DID \r\nALL \r\nTHE THINGS \r\nWE ARE \r\nCAPABLE \r\nOF DOING, \r\nWE WOULD \r\nLITERALLY \r\nASTOUND \r\nOURSELVE \r\n",
    "candidates": []
  },
  "FaceDetection": {
    "status": {
      "code": 3000.0,
      "description": "OK",
      "exception": null
    },
    "trackingId": "WE_e1f20803b4ed471fb5de7df551f5bd9f_ContentModerator.Preview_a2c40dbe-609d-4eb8-b01c-9988388804ea",
    "cacheId": "e4c0b500-ea8e-4a31-8fb3-35f98c4fbd65_636488005889528303",
    "result": false,
    "count": 0,
    "advancedInfo": [
      {
        "key": "ImageDownloadTimeInMs",
        "value": "11"
      },
      {
        "key": "ImageSizeInBytes",
        "value": "273405"
      }
    ],
    "faces": []
  }
},
{
  "ImageUrl": "https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png",
  "ImageModeration": {
    "cacheID": "b4866aa2-5e69-44ed-806a-f9a5d618c8ae_636488005930693926",
    "result": false,
    "trackingId": "WE_e1f20803b4ed471fb5de7df551f5bd9f_ContentModerator.Preview_fdce5510-f689-4791-b081-c2ad54dcfe78",
    "adultClassificationScore": 0.0035635426174849272,
    "isImageAdultClassified": false,
    "racyClassificationScore": 0.021369094029068947,
    "isImageRacyClassified": false,
    "advancedInfo": [
      {
        "key": "ImageDownloadTimeInMs",
        "value": "109"
      },
      {
        "key": "ImageSizeInBytes",
        "value": "2278902"
      }
    ],
    "status": {
      "code": 3000.0,
      "description": "OK",
      "exception": null
    }
  },
  "TextDetection": {
    "status": {
      "code": 3000.0,
      "description": "OK",
      "exception": null
    },
    "metadata": [
      {
        "key": "ImageDownloadTimeInMs",
        "value": "46"
      },
      {
        "key": "ImageSizeInBytes",
        "value": "2278902"
      }
    ],
    "trackingId": "WE_e1f20803b4ed471fb5de7df551f5bd9f_ContentModerator.Preview_08a4bc19-6010-41bb-a440-a77278e167d8",
    "cacheId": "28b37471-41b3-4f79-bd23-965498bcff51_636488005950851288",
    "language": "eng",
    "text": "",
    "candidates": []
  },
  "FaceDetection": {
    "status": {
      "code": 3000.0,
      "description": "OK",
      "exception": null
    },
    "trackingId": "WE_e1f20803b4ed471fb5de7df551f5bd9f_ContentModerator.Preview_40f2ce07-14ba-47cd-ba09-58b557a89854",
    "cacheId": "ec9c1be3-99b7-4bd9-8bc4-dc958c74459f_636488005964914299",
    "result": true,
    "count": 6,
    "advancedInfo": [
      {
        "key": "ImageDownloadTimeInMs",
        "value": "60"
      },
      {
        "key": "ImageSizeInBytes",
        "value": "2278902"
      }
    ],
    "faces": [
      {
        "bottom": 598,
        "left": 44,
        "right": 268,
        "top": 374
      },
      {
        "bottom": 620,
        "left": 308,
        "right": 532,
        "top": 396
      },
      {
        "bottom": 575,
        "left": 594,
        "right": 773,
        "top": 396
      },
      {
        "bottom": 563,
        "left": 812,
        "right": 955,
        "top": 420
      },
      {
        "bottom": 611,
        "left": 972,
        "right": 1151,
        "top": 432
      },
      {
        "bottom": 510,
        "left": 1232,
        "right": 1456,
        "top": 286
      }
    ]
  }
}]
```

## <a name="next-steps"></a>后续步骤

在本快速入门中，你开发了一个简单的 .NET 应用程序，该程序使用内容审查器服务返回给定图像示例的相关信息。 接下来，请详细了解不同标记和分类的含义，以便确定所需数据以及应用处理该数据的方式。

> [!div class="nextstepaction"]
> [图像审查指南](image-moderation-api.md)
