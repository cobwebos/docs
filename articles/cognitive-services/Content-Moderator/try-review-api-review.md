---
title: 使用 REST API 控制台创建审核审核 - 内容审阅者
titleSuffix: Azure Cognitive Services
description: 使用 Azure 内容审阅者审阅 API 创建图像或文本审阅以进行人工审核。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: pafarley
ms.openlocfilehash: a9726e41a84926d00d48b51e31f534a3d8c2fe0c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "72757143"
---
# <a name="create-human-reviews-rest"></a>创建人工评论 （REST）

[评论](./review-api.md#reviews)存储和显示内容，供人工版主评估。 当用户完成审核时，结果将发送到指定的回调终结点。 在本指南中，您将了解如何使用通过 API 控制台查看 REST API 设置评论。 了解 API 的结构后，可以轻松地将这些调用移植到任何与 REST 兼容的平台。

## <a name="prerequisites"></a>先决条件

- 在内容审阅人[审核工具](https://contentmoderator.cognitive.microsoft.com/)网站上登录或创建帐户。

## <a name="create-a-review"></a>创建评审

要创建审核，请转到**["审阅 - 创建](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4)** API 参考页"，然后为关键区域选择按钮（您可以在["审阅"工具](https://contentmoderator.cognitive.microsoft.com/)的 **"凭据"** 页上的终结点 URL 中找到该按钮）。 这将启动 API 控制台，您可以在其中轻松构造和运行 REST API 调用。

![查看 - 获取区域选择](images/test-drive-region.png)

### <a name="enter-rest-call-parameters"></a>输入 REST 呼叫参数

输入**团队名称**和**Ocp-Apim-订阅密钥**的值：

- **团队名称**：您在设置[审阅工具](https://contentmoderator.cognitive.microsoft.com/)帐户时创建的团队 ID（在审阅工具的凭据屏幕上的 **"Id"** 字段中找到）。
- **Ocp-Apim-订阅密钥**：您的内容审阅者密钥。 您可以在["查看"工具](https://contentmoderator.cognitive.microsoft.com)的 **"设置"** 选项卡上找到此内容。

### <a name="enter-a-review-definition"></a>输入审阅定义

编辑 **"请求正文**"框以输入具有以下字段的 JSON 请求：

- **元数据**：要返回到回调终结点的自定义键值对。 如果该键是在["审阅"工具](https://contentmoderator.cognitive.microsoft.com)中定义的短代码，则该键显示为标记。
- **内容**：在图像和视频内容的情况下，这是指向内容的 URL 字符串。 对于文本内容，这是实际的文本字符串。
- **内容 Id**：自定义标识符字符串。 此字符串将传递给 API，并通过回调返回。 它可用于将内部标识符或元数据与审核作业的结果相关联。
- **回调终结点**：（可选） 审核完成后接收回调信息的 URL。

默认请求正文显示您可以创建的不同类型的评论的示例：

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
  
选择 **"发送**"。 如果操作成功，**响应状态**为`200 OK`，**响应内容**框将显示审阅的 ID。 复制此 ID，然后在以下步骤中使用它。

![审查 - 创建控制台响应内容框显示审查 ID](images/test-drive-review-2.PNG)

### <a name="examine-the-new-review"></a>检查新审核

在["审阅"工具](https://contentmoderator.cognitive.microsoft.com)中，选择 **"查看** > **图像**/**文本**/**视频**"（具体取决于您使用的内容）。 应显示您上传的内容，以便进行人工审阅。

![足球的审查工具图像](images/test-drive-review-5.PNG)

## <a name="get-review-details"></a>获取评论详细信息

若要检索有关现有审核的详细信息，请转到["审核 - 获取](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c2)API 参考页"并选择您所在区域的按钮（管理密钥的区域）。

![“工作流 - 获取”区域选择](images/test-drive-region.png)

输入上一节中所示的 REST 调用参数。 对于此步骤 **，reviewId**是创建审阅时收到的唯一 ID 字符串。

![审查 - 创建控制台获取结果](images/test-drive-review-3.PNG)
  
选择 **"发送**"。 如果操作成功，**响应状态**为`200 OK`，**响应内容**框以 JSON 格式显示审阅详细信息，如下所示：

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

请注意响应中的以下字段：

- **状态**
- **审阅者结果标签**：如果人工审阅团队手动添加了任何标记（显示**创建 By**字段），则会出现此标记。
- **元数据**：这显示了在人工评审团队进行更改之前最初在审核中添加的标记。

## <a name="next-steps"></a>后续步骤

在本指南中，您学习了如何使用 REST API 创建内容审核。 接下来，将评论集成到端到端审核方案中，如[电子商务审核](./ecommerce-retail-catalog-moderation.md)教程。