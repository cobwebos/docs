---
title: 示例：调用分析图像 API - 计算机视觉
titlesuffix: Azure Cognitive Services
description: 了解如何通过使用 Azure 认知服务中的 REST 调用计算机视觉 API。
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 01/20/2017
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: abbdda96acabb44a225ce16b4750726d37ff86b0
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/07/2019
ms.locfileid: "55882856"
---
# <a name="example-how-to-call-the-computer-vision-api"></a>示例：如何调用计算机视觉 API

本指南演示如何使用 REST 调用计算机视觉 API。 两个示例在 C# 中均使用计算机视觉 API 客户端库编写，并作为 HTTP POST/GET 调用。 我们将重点介绍：

-   如何获取“标记”、“说明”和“类别”。
-   如何获取“特定于域的”信息（名人）。

### <a name="Prerequisites">先决条件</a> 
图像 URL 或本地存储图像的路径。
  * 支持的输入方法：原始图像二进制，采用应用程序/八位字节流或图像 URL 的形式
  * 支持的图像格式：JPEG、PNG、GIF 和 BMP
  * 图像文件大小：小于 4MB
  * 图像维度：大于 50 x 50 像素
  
下面的示例演示了以下功能：

1. 分析图像并返回标记和说明数组。
2. 使用特定于域的模型分析图像（具体而言，“名人”模型）并以 JSON 格式返回相应结果。

功能细分为：

  * **选项一：** 范围内分析 - 仅分析给定模型
  * **选项二：** 强化分析 - 经过分析可提供具有 [86 个类别分类](../Category-Taxonomy.md)的更多详细信息
  
### <a name="Step1">步骤 1：授权 API 调用</a> 
每次调用计算机视觉 API 都需要订阅密钥。 此密钥需要通过查询字符串参数传递，或者在请求头中指定。 

要获取订阅密钥，请参阅[如何获取订阅密钥](../Vision-API-How-to-Topics/HowToSubscribe.md
)。

**1.** 通过查询字符串传递订阅密钥，将以下内容视为计算机视觉 API 示例：

```https://westus.api.cognitive.microsoft.com/vision/v2.0/analyze?visualFeatures=Description,Tags&subscription-key=<Your subscription key>```

**2.** 在 HTTP 请求标头中也可指定传递订阅密钥：

```ocp-apim-subscription-key: <Your subscription key>```

**3.** 使用客户端库时，通过 VisionServiceClient 的构造函数传入订阅密钥：

```var visionClient = new VisionServiceClient(“Your subscriptionKey”);```

### <a name="Step2">步骤 2：将图像上传到计算机视觉 API 服务并取回标记、说明和名人</a>
执行计算机视觉 API 调用的基本方法是直接上传图像。 此操作通过发送内容类型为 application/octet-stream 且包含从图像中读取的数据的“POST”请求完成。 对于“标记”和“说明”，所有计算机视觉 API 调用均使用此上传方法。 唯一的区别是用户指定的查询参数。 

下面介绍了如何获取给定图像中的“标记”和“说明”：

**选项一：** 获取“标记”列表和一个“说明”
```
POST https://westus.api.cognitive.microsoft.com/vision/v2.0/analyze?visualFeatures=Description,Tags&subscription-key=<Your subscription key>
```
```
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
### <a name="here-is-how-to-get-domain-specific-analysis-in-our-case-for-celebrities"></a>下面介绍了如何获取特定于域的分析（在本例中为名人分析）。

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

### <a name="Step3">步骤 3：检索并了解 analyze&visualFeatures=Tags, Description 的 JSON 输出</a>

下面是一个示例：
```
  {
    “tags”: [
    {
    "name": "outdoor",
        "score": 0.976
    },
    {
    "name": "bird",
        "score": 0.95
    }
            ],
    “description”: 
    {
    "tags": [
    "outdoor",
    "bird"
            ],
    "captions": [
    {
    "text”: “partridge in a pear tree”,
            “confidence”: 0.96
    }
                ]
    }
  }
```
字段   | Type  | 内容
------|------|------|
标记    | 对象    | 标记数组的顶级对象
tags[].Name | 字符串    | 标记分类器中的关键字
tags[].Score    | 数字    | 置信度分数，介于 0 和 1 之间。
description  | 对象   | 说明的顶级对象。
description.tags[] |    字符串  | 标记列表。  如果置信度不足无法生成标题，则标记可能是可提供给调用方的唯一信息。
description.captions[].text | 字符串    | 描述图像的短语。
description.captions[].confidence   | 数字    | 短语的置信度。

### <a name="Step4">步骤 4：检索并了解特定于域的模型的 JSON 输出</a>

**选项一：** 范围内分析 - 仅分析给定模型

输出将为标记数组，示例如下：
```
  { 
    "result": [ 
      { 
    "name": "golden retriever", 
    "score": 0.98
    },
    { 
    "name": "Labrador retriever", 
    "score": 0.78
    }
              ]
  }
```

**选项二：** 强化分析 - 经过分析可提供具有 86 个类别分类的更多详细信息

对于使用选项二（强化分析）的特定于域的模型，将扩展类别返回类型。 以下是一个示例：
```
  {
    "requestId": "87e44580-925a-49c8-b661-d1c54d1b83b5",
    "metadata":     {
      "width": 640,
      "height": 430,
      "format": "Jpeg"
                    },
    "result": {
      "celebrities": 
      [
        {
        "name": "Richard Nixon",
        "faceRectangle": {
          "left": 107,
          "top": 98,
          "width": 165,
          "height": 165
                         },
        "confidence": 0.9999827
        }
      ]
  }
```

类别字段是包含原始分类中 [86 个类别](../Category-Taxonomy.md)中的一个或多个的列表。 另请注意，以下划线结尾的类别将匹配该类别及其子类别（例如，在名人模型中，匹配 people_ 以及 people_group）。

字段   | Type  | 内容
------|------|------|
categories | 对象 | 顶级对象
categories[].name    | 字符串   | 86 个类别分类的名称
categories[].score  | 数字    | 置信度分数，介于 0 和 1 之间
categories[].detail  | 对象？      | 可选详细信息对象

请注意，如果多个类别匹配（例如，86 个类别分类器在 model=celebrities 时同时返回 people_ 和 people_young 的分数），则详细信息附加到最宽泛级别的匹配项（在该示例中为 people_）。

### <a name="Errors">错误响应</a>
这些内容与 vision.analyze 相同，另外还有一个 NotSupportedModel 错误 (HTTP 400)，在选项一和选项二两种情况下均可能返回该错误。 对于选项二（强化分析），如果无法识别详细信息中指定的任何模型，即使一个或多个模型有效，API 也会返回 NotSupportedModel。  用户可以通过调用 listModels 弄清哪些模型受支持。

### <a name="Summary">摘要</a>

这些是计算机视觉 API 的基本功能：如何上传图像并反过来检索有价值的元数据。

要使用 REST API，请转到[计算机视觉 API 参考](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)。
 
