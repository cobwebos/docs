---
title: 使用 REST API 控制台创建审查评审 - 内容审查器
titleSuffix: Azure Cognitive Services
description: 使用 Azure 内容审查器评审 API 为人工审查创建图像或文本评审。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: pafarley
ms.openlocfilehash: a9726e41a84926d00d48b51e31f534a3d8c2fe0c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "72757143"
---
# <a name="create-human-reviews-rest"></a>创建人工评审 (REST)

[审阅](./review-api.md#reviews)存储并显示供人工审查方评估的内容。 当用户完成评审时，系统会将结果发送到指定的回调终结点。 在本指南中，你将了解如何通过 API 控制台使用评审 REST API 来设置评审。 了解 API 的结构后，可以轻松将这些调用移植到任何与 REST 兼容的平台。

## <a name="prerequisites"></a>先决条件

- 在内容审查器[审阅工具](https://contentmoderator.cognitive.microsoft.com/)站点上登录或创建帐户。

## <a name="create-a-review"></a>创建评审

若要创建评审，请转到[评审 - 创建](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4) API 参考页并选择你的键区域的按钮（可以在[评审工具](https://contentmoderator.cognitive.microsoft.com/)的“凭据”页上的终结点 URL 中找到此项）**** ****。 这将启动 API 控制台，你可以在其中轻松构造和运行 REST API 调用。

![“评审 - 获取”区域选择](images/test-drive-region.png)

### <a name="enter-rest-call-parameters"></a>输入 REST 调用参数

输入“teamName”和“Ocp-Apim-Subscription-Key”的值**** ****：

- **teamName**：设置[审阅工具](https://contentmoderator.cognitive.microsoft.com/)帐户时创建的团队 ID（位于审阅工具的“凭据”屏幕的“ID”**** 字段中）。
- **Ocp-Apim-Subscription-Key**：内容审查器密钥。 可以在[审阅工具](https://contentmoderator.cognitive.microsoft.com)的“设置”**** 选项卡上找到它。

### <a name="enter-a-review-definition"></a>输入评审定义

编辑“请求正文”框，通过以下字段输入 JSON 请求****：

- **元数据**：要返回到回调终结点的自定义键值对。 如果此键是[评审工具](https://contentmoderator.cognitive.microsoft.com)中定义的短代码，则显示为标记。
- **内容**：对于图像和视频内容，这是指向内容的 URL 字符串。 对于文本内容，这是实际的文本字符串。
- **ContentId**：自定义标识符字符串。 此字符串将传递给 API，并通过回调返回。 它在关联内部标识符或元数据与审查作业结果时十分有用。
- **CallbackEndpoint**：（可选）在评审完成时用于接收回调信息的 URL。

默认请求正文显示了可以创建的不同类型评审的示例：

```json
[Image]
[
  {
    "Metadata": [
      {
        "Key": "string",
        "Value": "string"
      }
    ],
    "Type": "Image",
    "Content": "<Content Url>",
    "ContentId": "<Your identifier for this content>",
    "CallbackEndpoint": "<Url where you would receive callbacks>"
  }
]
[Text]
[
  {
    "Metadata": [
      {
        "Key": "string",
        "Value": "string"
      }
    ],
    "Type": "Text",
    "Content": "<Your Text Content>",
    "ContentId": "<Your identifier for this content>",
    "CallbackEndpoint": "<Url where you would receive callbacks>"
  }
]
[Video]
[
  {
    "VideoFrames":[
      {
          "Id": "<Frame Id>",
          "Timestamp": "<Frame Timestamp",
          "FrameImage":"<Frame Image URL",
          "Metadata": [
            {
              "Key": "<Key>",
              "Value": "<Value"
            }
          ],
          "ReviewerResultTags": [
          ]
    ], 
    "Metadata": [
      {
        "Key": "string",
        "Value": "string"
      },
      //For encrypted Videos
        {
          "Key": "protectedType",
          "Value": "AES or FairPlay or Widevine or Playready"
        },
        {
          "Key": "authenticationToken",
          "Value": "your viewtoken(In case of Video Indexer AES encryption type, this value is viewtoken from breakdown json)"
        },
      //For FairPlay encrypted type video include certificateUrl as well
        {
          "Key": "certificateUrl",
          "Value": "your certificate url"
        }
    ],
    "Type": "Video",
    "Content": "<Stream Url>",
    "ContentId": "<Your identifier for this content>",
    "CallbackEndpoint": "<Url where you would receive callbacks>",
    [Optional]
    "Timescale": "<Timescale of the video>
  }
]
```

### <a name="submit-your-request"></a>提交请求
  
选择“发送”。**** 如果该操作成功，“响应状态”将为 `200 OK`，且“响应内容”框显示评审的 ID**** ****。 复制此 ID，然后在以下步骤中使用它。

![审查 - 创建控制台响应内容框显示审查 ID](images/test-drive-review-2.PNG)

### <a name="examine-the-new-review"></a>检查新评审

在[评审工具](https://contentmoderator.cognitive.microsoft.com)中，选择“评审” > “图像”/“文本”/“视频”（取决于所使用的内容）**** **** **** ****。 现在，上传的内容应已显示，可供人工评审。

![足球的审查工具图像](images/test-drive-review-5.PNG)

## <a name="get-review-details"></a>获取评论详细信息

若要检索有关现有审阅的详细信息，请转到[评审 - 获取](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c2) API 参考页，并选择代表你的区域（管理键的区域）的按钮。

![“工作流 - 获取”区域选择](images/test-drive-region.png)

按以上所述输入 REST 调用参数。 对于此步骤，reviewId 是创建评审时收到的唯一 ID 字符串****。

![审查 - 创建控制台获取结果](images/test-drive-review-3.PNG)
  
选择“发送”。**** 如果操作成功，“响应状态”为 `200 OK`，“响应内容”框将以 JSON 格式显示评审详细信息，如下所示**** ****：

```json
{  
  "reviewId":"201712i46950138c61a4740b118a43cac33f434",
  "subTeam":"public",
  "status":"Complete",
  "reviewerResultTags":[  
    {  
      "key":"a",
      "value":"False"
    },
    {  
      "key":"r",
      "value":"True"
    },
    {  
      "key":"sc",
      "value":"True"
    }
  ],
  "createdBy":"<teamname>",
  "metadata":[  
    {  
      "key":"sc",
      "value":"true"
    }
  ],
  "type":"Image",
  "content":"https://reviewcontentprod.blob.core.windows.net/<teamname>/IMG_201712i46950138c61a4740b118a43cac33f434",
  "contentId":"0",
  "callbackEndpoint":"<callbackUrl>"
}
```

记下响应中的以下字段：

- **status**
- **reviewerResultTags**：如果有标记是由人工评审团队手动添加的（通过“createdBy”字段指示），会出现该字段****。
- **metadata**：该字段显示，在人工评审团队更改之前，最初添加到评审中的标记。

## <a name="next-steps"></a>后续步骤

在本指南中，你了解了如何使用 REST API 创建内容审查评审。 接下来，将评审集成到端到端的审查方案中，例如[电子商务审查](./ecommerce-retail-catalog-moderation.md)教程。