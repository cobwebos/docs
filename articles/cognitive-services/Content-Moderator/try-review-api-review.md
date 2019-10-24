---
title: REST API 控制台创建审核评审-内容审查器
titleSuffix: Azure Cognitive Services
description: 使用 Azure 内容审查器查看 Api 为人员审核创建图像或文本审阅。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: pafarley
ms.openlocfilehash: a9726e41a84926d00d48b51e31f534a3d8c2fe0c
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/22/2019
ms.locfileid: "72757143"
---
# <a name="create-human-reviews-rest"></a>创建人工审阅（REST）

[查看](./review-api.md#reviews)存储并显示要评估的人的内容。 当用户完成评审后，结果将发送到指定的回调终结点。 在本指南中，你将了解如何通过 API 控制台使用评审 REST Api 设置评论。 了解 Api 的结构后，可以轻松地将这些调用移植到任何与 REST 兼容的平台。

## <a name="prerequisites"></a>必备组件

- 登录或创建内容审查器[审核工具](https://contentmoderator.cognitive.microsoft.com/)站点上的帐户。

## <a name="create-a-review"></a>创建评审

若要创建评审，请在 " **[查看-创建](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4)** API 参考" 页上，选择用于关键区域的按钮（可在 "[查看工具](https://contentmoderator.cognitive.microsoft.com/)" 的 "**凭据**" 页上的 "终结点 URL" 中找到此项）。 这会启动 API 控制台，你可以在其中轻松构造和运行 REST API 调用。

![审阅-获取区域选择](images/test-drive-region.png)

### <a name="enter-rest-call-parameters"></a>输入 REST 调用参数

输入**teamName**和**Apim**的值：

- **teamName**：设置[审阅工具](https://contentmoderator.cognitive.microsoft.com/)帐户时创建的团队 ID （在查看工具的凭据屏幕上的**ID**字段中找到）。
- **Apim-key**：你的内容审查器密钥。 可以在 "[查看" 工具](https://contentmoderator.cognitive.microsoft.com)的 "**设置**" 选项卡上找到。

### <a name="enter-a-review-definition"></a>输入审阅定义

编辑 "**请求正文**" 框，输入具有以下字段的 JSON 请求：

- **Metadata**：要返回到回调终结点的自定义键值对。 如果该密钥是在 "[审阅" 工具](https://contentmoderator.cognitive.microsoft.com)中定义的简短代码，它将显示为标记。
- **内容**：对于图像和视频内容，这是指向内容的 URL 字符串。 对于文本内容，这是实际的文本字符串。
- **Id 为**：自定义标识符字符串。 此字符串将传递给 API，并通过回调返回。 它可用于将内部标识符或元数据与裁决作业的结果关联起来。
- **CallbackEndpoint**：（可选）在评审完成后接收回调信息的 URL。

默认请求正文显示了你可以创建的不同类型的评论的示例：

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
  
选择“发送”。 如果操作成功，则**响应状态**为 "`200 OK`"，"**响应内容**" 框显示评审的 ID。 复制此 ID，然后在以下步骤中使用它。

![审查 - 创建控制台响应内容框显示审查 ID](images/test-drive-review-2.PNG)

### <a name="examine-the-new-review"></a>检查新评审

在 "[查看" 工具](https://contentmoderator.cognitive.microsoft.com)中，选择 "**查看** > **图像**" /**文本**/**视频**（取决于你使用的内容）。 你已上传的内容应显示，可供人工审阅。

![足球的审查工具图像](images/test-drive-review-5.PNG)

## <a name="get-review-details"></a>获取评论详细信息

若要检索有关现有评审的详细信息，请转到 "[审阅-获取](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c2)API 参考" 页，然后选择你所在区域的按钮（你的密钥的管理区域）。

![“工作流 - 获取”区域选择](images/test-drive-region.png)

输入 REST 调用参数，如以上部分所示。 对于此步骤， **reviewId**是创建评审时收到的唯一 ID 字符串。

![审查 - 创建控制台获取结果](images/test-drive-review-3.PNG)
  
选择“发送”。 如果操作成功，则**响应状态**为 "`200 OK`"，"**响应内容**" 框显示 JSON 格式的查看详细信息，如下所示：

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

在响应中记下以下字段：

- **status**
- **reviewerResultTags**：如果人工审阅团队手动添加了任何标记（显示为**system.createdby**字段），则会出现此情况。
- **元数据**：此项显示在人工审阅团队做出更改之前，在评审中最初添加的标记。

## <a name="next-steps"></a>后续步骤

在本指南中，您学习了如何使用 REST API 创建内容裁决审查。 接下来，将评审集成到端到端裁决方案，如[电子商务审核](./ecommerce-retail-catalog-moderation.md)教程。