---
title: 快速入门：使用 C# 分析文本内容 - 内容审查器
titleSuffix: Azure Cognitive Services
description: 如何使用适用于 .NET 的内容审查器 SDK 分析文本内容中是否存在各种令人反感的材料
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 07/03/2019
ms.author: sajagtap
ms.openlocfilehash: 45ac641948531aa27334762704eba3333aebba4d
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68564319"
---
# <a name="quickstart-analyze-text-content-for-objectionable-material-in-c"></a>快速入门：使用 C# 分析文本内容中是否存在令人反感的材料

本文中的信息和代码示例有助于你完成[适用于 .NET 的内容审查器 SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) 的使用入门。 本文介绍如何对文本内容执行基于字词的筛选和分类，以便审查其中是否存在可能会令人反感的材料。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 

## <a name="prerequisites"></a>先决条件
- 内容审查器的订阅密钥。 按照[创建认知服务帐户](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)中的说明订阅内容审查器并获取密钥。
- 任何版本的 [Visual Studio 2015 或 2017](https://www.visualstudio.com/downloads/)

> [!NOTE]
> 本指南使用免费层内容审查器订阅。 若要了解为每个订阅层提供的内容，请参阅[定价和限制](https://azure.microsoft.com/pricing/details/cognitive-services/content-moderator/)页。

## <a name="create-the-visual-studio-project"></a>创建 Visual Studio 项目

1. 在 Visual Studio 中创建新的**控制台应用 (.NET Framework)** 项目并将其命名为 **TextModeration**。 
1. 如果解决方案中有其他项目，请将此项目选为单一启动项目。
1. 获取所需的 NuGet 包。 右键单击解决方案资源管理器中的项目，选择“管理 NuGet 包”，然后找到并安装以下包： 
    - `Microsoft.Azure.CognitiveServices.ContentModerator`
    - `Microsoft.Rest.ClientRuntime`
    - `Newtonsoft.Json`

## <a name="add-text-moderation-code"></a>添加文本审查代码

接下来，需将代码从本指南复制并粘贴到项目中，以便实施基本的内容审查方案。

### <a name="include-namespaces"></a>包括命名空间

将以下 `using` 语句添加到 *Program.cs* 文件顶部。

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/text-moderation-quickstart-dotnet.cs?range=1-8)]

### <a name="create-the-content-moderator-client"></a>Create the Content Moderator client

向 *Program.cs* 文件添加以下代码，为订阅创建内容审查器客户端提供程序。 在同一命名空间中添加此代码和 **Program** 类。 还需使用区域标识符和订阅密钥的值更新  AzureRegion 和 CMSubscriptionKey  字段。

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/text-moderation-quickstart-dotnet.cs?range=54-77)]

### <a name="set-up-input-and-output-targets"></a>设置输入和输出目标

向 Program.cs 中的 Program 类添加以下静态字段   。 这些字段指定输入文本内容和输出 JSON 内容的文件。

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/text-moderation-quickstart-dotnet.cs?range=15-19)]

需创建 TextFile.txt  输入文件并更新其路径（路径相对于执行目录）。 打开 _TextFile.txt_ 并添加需审查的文本。 本快速入门使用以下示例文本：

```
Is this a grabage or crap email abcdef@abcd.com, phone: 6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.
These are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 
0800 820 3300. Also, 999-99-9999 looks like a social security number (SSN).
```

### <a name="load-the-input-text"></a>加载输入文本

将以下代码添加到 Main 方法  。 **ScreenText** 方法是重要的操作， 其参数指定将要执行的内容审查操作。 在此示例中，该方法配置为：
- 检测文本中可能存在的猥亵语言。
- 规范化文本和自动更正错别字。
- 检测个人数据，例如美国和英国的电话号码、电子邮件地址和美国邮寄地址。
- 使用基于机器学习的模型将文本分为三类。

若要详细了解这些操作的功能，请单击[后续步骤](#next-steps)部分的链接。

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/text-moderation-quickstart-dotnet.cs?range=23-48)]

## <a name="run-the-program"></a>运行程序

程序会将 JSON 字符串数据写入 _TextModerationOutput.txt_ 文件。 本快速入门中使用的示例文本的输出如下：

```json
Autocorrect typos, check for matching terms, PII, and classify.
{
"OriginalText": "\"Is this a grabage or crap email abcdef@abcd.com, phone: 6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052. These are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300. Also, 999-99-9999 looks like a social security number (SSN).\"",
"NormalizedText": "\" Is this a garbage or crap email abide@ abed. com, phone: 6657789887, IP: 255. 255. 255. 255, 1 Microsoft Way, Redmond, WA 98052. These are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300. Also, 999-99-9999 looks like a social security number ( SSN) . \"",
"AutoCorrectedText": "\" Is this a garbage or crap email abide@ abed. com, phone: 6657789887, IP: 255. 255. 255. 255, 1 Microsoft Way, Redmond, WA 98052. These are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300. Also, 999-99-9999 looks like a social security number ( SSN) . \"",
"Misrepresentation": null,
    
"Classification": {
        "Category1": {
        "Score": 1.5113095059859916E-06
        },
        "Category2": {
        "Score": 0.12747249007225037
        },
        "Category3": {
        "Score": 0.98799997568130493
        },
        "ReviewRecommended": true
  },
  "Status": {
    "Code": 3000,
    "Description": "OK",
    "Exception": null
  },
  "PII": {
        "Email": [
            {
            "Detected": "abcdef@abcd.com",
            "SubType": "Regular",
            "Text": "abcdef@abcd.com",
            "Index": 33
            }
            ],
        "IPA": [
            {
            "SubType": "IPV4",
            "Text": "255.255.255.255",
            "Index": 73
            }
            ],
        "Phone": [
            {
            "CountryCode": "US",
            "Text": "6657789887",
            "Index": 57
            },
            {
            "CountryCode": "US",
            "Text": "870 608 4000",
            "Index": 211
            },
            {
            "CountryCode": "UK",
            "Text": "+44 870 608 4000",
            "Index": 207
            },
            {
            "CountryCode": "UK",
            "Text": "0344 800 2400",
            "Index": 227
            },
            {
        "CountryCode": "UK",
            "Text": "0800 820 3300",
            "Index": 244
            }
            ],
         "Address": [{
             "Text": "1 Microsoft Way, Redmond, WA 98052",
             "Index": 89
                }]
    },
  "Language": "eng",
  "Terms": [
    {
        "Index": 22,
        "OriginalIndex": 22,
        "ListId": 0,
        "Term": "crap"
    }
  ],
  "TrackingId": "9392c53c-d11a-441d-a874-eb2b93d978d3"
}
```

## <a name="next-steps"></a>后续步骤

在本快速入门中，你开发了一个简单的 .NET 应用程序，该程序使用内容审查器服务返回给定文本示例的相关信息。 接下来，请详细了解不同标记和分类的含义，以便确定所需数据以及应用处理该数据的方式。

> [!div class="nextstepaction"]
> [文本审查指南](text-moderation-api.md)
