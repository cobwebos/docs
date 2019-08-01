---
title: 示例：调用分析图像 API - 计算机视觉
titleSuffix: Azure Cognitive Services
description: 了解如何通过使用 Azure 认知服务中的 REST 调用计算机视觉 API。
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 03/21/2019
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: 35e6ad922ab54748165fcf8e273d93ee44bc42cc
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68564524"
---
# <a name="example-how-to-call-the-computer-vision-api"></a>示例：如何调用计算机视觉 API

本指南演示如何使用 REST 调用计算机视觉 API。 两个示例在 C# 中均使用计算机视觉 API 客户端库编写，并作为 HTTP POST/GET 调用。 我们将重点介绍：

- 如何获取“标记”、“说明”和“类别”。
- 如何获取“特定于域的”信息（名人）。

## <a name="prerequisites"></a>先决条件

- 图像 URL 或本地存储图像的路径。
- 支持的输入方法：原始图像二进制，采用应用程序/八位字节流或图像 URL 的形式
- 支持的图像格式：JPEG、PNG、GIF 和 BMP
- 图像文件大小：小于 4MB
- 图像维度：大于 50 x 50 像素
  
下面的示例演示了以下功能：

1. 分析图像并返回标记和说明数组。
2. 使用特定于域的模型分析图像（具体而言，“名人”模型）并以 JSON 格式返回相应结果。

功能细分为：

- **选项一：** 范围内分析 - 仅分析给定模型
- **选项二：** 强化分析 - 经过分析可提供具有 [86 个类别分类](../Category-Taxonomy.md)的更多详细信息
  
## <a name="authorize-the-api-call"></a>授权 API 调用

每次调用计算机视觉 API 都需要订阅密钥。 此密钥需要通过查询字符串参数传递，或者在请求头中指定。

要获取免费试用的密钥，请参阅[试用认知服务](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision)。 或者，按照[创建认知服务帐户](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)中的说明订阅计算机视觉并获取密钥。

1. 通过查询字符串传递订阅密钥，将以下内容视为计算机视觉 API 示例：

```https://westus.api.cognitive.microsoft.com/vision/v2.0/analyze?visualFeatures=Description,Tags&subscription-key=<Your subscription key>```

1. 在 HTTP 请求标头中也可指定传递订阅密钥：

```ocp-apim-subscription-key: <Your subscription key>```

1. 使用客户端库时，通过 VisionServiceClient 的构造函数传入订阅密钥：

```var visionClient = new VisionServiceClient("Your subscriptionKey");```

## <a name="upload-an-image-to-the-computer-vision-api-service-and-get-back-tags-descriptions-and-celebrities"></a>将图像上传到计算机视觉 API 服务并取回标记、说明和名人

执行计算机视觉 API 调用的基本方法是直接上传图像。 此操作通过发送内容类型为 application/octet-stream 且包含从图像中读取的数据的“POST”请求完成。 对于“标记”和“说明”，所有计算机视觉 API 调用均使用此上传方法。 唯一的区别是用户指定的查询参数。 

下面介绍了如何获取给定图像中的“标记”和“说明”：

**选项一：** 获取“标记”列表和一个“说明”

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.0/analyze?visualFeatures=Description,Tags&subscription-key=<Your subscription key>
```

```csharp
using Microsoft.ProjectOxford.Vision;
using Microsoft.ProjectOxford.Vision.Contract;
using System.IO;

AnalysisResult analysisResult;
var features = new VisualFeature[] { VisualFeature.Tags, VisualFeature.Description };

using (var fs = new FileStream(@"C:\Vision\Sample.jpg", FileMode.Open))
{
  analysisResult = await visionClient.AnalyzeImageAsync(fs, features);
}
```

**选项二**仅获取“标记”列表，或仅获取“说明”列表：

###### <a name="tags-only"></a>仅标记：

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.0/tag&subscription-key=<Your subscription key>
var analysisResult = await visionClient.GetTagsAsync("http://contoso.com/example.jpg");
```

###### <a name="description-only"></a>仅说明：

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.0/describe&subscription-key=<Your subscription key>
using (var fs = new FileStream(@"C:\Vision\Sample.jpg", FileMode.Open))
{
  analysisResult = await visionClient.DescribeAsync(fs);
}
```

### <a name="get-domain-specific-analysis-celebrities"></a>获取特定领域的分析（名人）

**选项一：** 范围内分析 - 仅分析给定模型
```
POST https://westus.api.cognitive.microsoft.com/vision/v2.0/models/celebrities/analyze
var celebritiesResult = await visionClient.AnalyzeImageInDomainAsync(url, "celebrities");
```

对于此选项，所有其他查询参数 {visualFeatures, details} 无效。 如果要查看所有支持的模型，请使用：

```
GET https://westus.api.cognitive.microsoft.com/vision/v2.0/models 
var models = await visionClient.ListModelsAsync();
```

**选项二：** 强化分析 - 经过分析可提供具有 [86 个类别分类](../Category-Taxonomy.md)的更多详细信息

对于你不止要获取一个或多个特定于域的模型中的详细信息，还希望获取常规图像分析的应用程序，我们会使用模型查询参数扩展 v1 API。

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.0/analyze?details=celebrities
```

调用此方法时，我们将首先调用 86 个类别分类器。 如果任何类别均与已知/匹配模型的类别匹配，将执行第二轮分类器调用。 例如，如果“details=all”，或者“details”包括“celebrities”，我们将在 86 个类别分类器被调用后调用名人模型，并且结果包括人员类别。 相比选项一，选项二会增加用户感兴趣的名人的延迟时间。

本例中的所有 v1 查询参数的行为均相同。  如果 visualFeatures = categories 未指定，则将隐式启用。

## <a name="retrieve-and-understand-the-json-output-for-analysis"></a>检索并了解 JSON 输出以进行分析

下面是一个示例：

```json
{  
  "tags":[  
    {  
      "name":"outdoor",
      "score":0.976
    },
    {  
      "name":"bird",
      "score":0.95
    }
  ],
  "description":{  
    "tags":[  
      "outdoor",
      "bird"
    ],
    "captions":[  
      {  
        "text":"partridge in a pear tree",
        "confidence":0.96
      }
    ]
  }
}
```

字段 | Type | 内容
------|------|------|
Tags  | `object` | 标记数组的顶级对象
tags[].Name | `string`  | 标记分类器中的关键字
tags[].Score    | `number`  | 置信度分数，介于 0 和 1 之间。
description  | `object` | 说明的顶级对象。
description.tags[] |    `string`    | 标记列表。  如果置信度不足无法生成标题，则标记可能是可提供给调用方的唯一信息。
description.captions[].text | `string`  | 描述图像的短语。
description.captions[].confidence   | `number`  | 短语的置信度。

## <a name="retrieve-and-understand-the-json-output-of-domain-specific-models"></a>检索并了解特定于域的模型的 JSON 输出

**选项一：** 范围内分析 - 仅分析给定模型

输出将为标记数组，示例如下：

```json
{  
  "result":[  
    {  
      "name":"golden retriever",
      "score":0.98
    },
    {  
      "name":"Labrador retriever",
      "score":0.78
    }
  ]
}
```

**选项二：** 强化分析 - 经过分析可提供具有 86 个类别分类的更多详细信息

对于使用选项二（强化分析）的特定于域的模型，将扩展类别返回类型。 以下是一个示例：

```json
{  
  "requestId":"87e44580-925a-49c8-b661-d1c54d1b83b5",
  "metadata":{  
    "width":640,
    "height":430,
    "format":"Jpeg"
  },
  "result":{  
    "celebrities":[  
      {  
        "name":"Richard Nixon",
        "faceRectangle":{  
          "left":107,
          "top":98,
          "width":165,
          "height":165
        },
        "confidence":0.9999827
      }
    ]
  }
}
```

类别字段是包含原始分类中 [86 个类别](../Category-Taxonomy.md)中的一个或多个的列表。 另请注意，以下划线结尾的类别将匹配该类别及其子类别（例如，在名人模型中，匹配 people_ 以及 people_group）。

字段   | Type  | 内容
------|------|------|
categories | `object`   | 顶级对象
categories[].name    | `string` | 86 个类别分类的名称
categories[].score  | `number`  | 置信度分数，介于 0 和 1 之间
categories[].detail  | `object?`      | 可选详细信息对象

请注意，如果多个类别匹配（例如，86 个类别分类器在 model=celebrities 时同时返回 people_ 和 people_young 的分数），则详细信息附加到最宽泛级别的匹配项（在该示例中为 people_）。

## <a name="errors-responses"></a>错误响应

这些内容与 vision.analyze 相同，另外还有一个 NotSupportedModel 错误 (HTTP 400)，在选项一和选项二两种情况下均可能返回该错误。 对于选项二（强化分析），如果无法识别详细信息中指定的任何模型，即使一个或多个模型有效，API 也会返回 NotSupportedModel。  用户可以通过调用 listModels 弄清哪些模型受支持。

## <a name="next-steps"></a>后续步骤

要使用 REST API，请转到[计算机视觉 API 参考](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)。
