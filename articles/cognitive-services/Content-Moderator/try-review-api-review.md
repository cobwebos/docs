---
title: 创建使用 REST API 控制台-内容审查器审查评论
titlesuffix: Azure Cognitive Services
description: 使用 Azure 内容审查器评审 Api 创建人工审查的图像或文本审阅。
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 03/18/2019
ms.author: sajagtap
ms.openlocfilehash: 254269ccedc92b9dfc164cc4665a8a8513682773
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60607496"
---
# <a name="create-human-reviews-rest"></a>创建人工审阅 (REST)

[评审](./review-api.md#reviews)存储和显示审阅人，若要评估的内容。 当用户完成评审时，结果将发送到指定的回调终结点。 在本指南中，将了解如何设置以使用通过 API 控制台的 REST Api 查看评论。 一旦您了解的 Api 结构，您可以轻松地移植到任何 REST 兼容平台这些调用。

## <a name="prerequisites"></a>必备组件

- 登录或在内容审查器上创建一个帐户[审阅工具](https://contentmoderator.cognitive.microsoft.com/)站点。

## <a name="create-a-review"></a>创建评审

若要创建评审，请转到**[查看-创建](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4)** API 引用页，选择密钥中你所在区域的按钮 (您可以在中找到此终结点 URL 上**凭据**页[审阅工具](https://contentmoderator.cognitive.microsoft.com/))。 这将启动 API 控制台中，您可以轻松地构建和运行的 REST API 调用。

![复习-Get 区域选择](images/test-drive-region.png)

### <a name="enter-rest-call-parameters"></a>输入 REST 调用参数

输入值**teamName**，并**Ocp Apim 订阅密钥**:

- **teamName**：您设置时创建的团队 ID 你[审阅工具](https://contentmoderator.cognitive.microsoft.com/)帐户 (在中找到**Id**评审工具的凭据屏幕上的字段)。
- **Ocp-Apim-Subscription-Key**：你的内容审查器密钥。 您可以在上找到此**设置**选项卡[审阅工具](https://contentmoderator.cognitive.microsoft.com)。

### <a name="enter-a-review-definition"></a>输入评审定义

编辑**请求正文**框中输入 JSON 请求包含以下字段：

- **元数据**：若要返回到你的回调终结点的自定义键 / 值对。 如果该键中定义的短代码[审阅工具](https://contentmoderator.cognitive.microsoft.com)，它将显示为一个标记。
- **内容**:对于图像和视频内容，这是指向内容的 URL 字符串。 对于文本内容，这是实际的文本字符串。
- **ContentId**：一个自定义的标识符的字符串。 此字符串将传递给 API，并通过回调返回。 它可用于将与审查作业的结果相关联的内部标识符或元数据。
- **CallbackEndpoint**:（可选）要评审完成时接收回调信息的 URL。

默认请求正文演示了的评审可以创建不同类型的示例：

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
  
选择“发送”。 如果操作成功，**响应状态**是`200 OK`，和**响应内容**框显示为评审 ID。 复制此 ID，然后在以下步骤中使用它。

![审查 - 创建控制台响应内容框显示审查 ID](images/test-drive-review-2.PNG)

### <a name="examine-the-new-review"></a>检查新评审

在中[审阅工具](https://contentmoderator.cognitive.microsoft.com)，选择**评审** > **映像**/**文本**/ **视频**（使用具体取决于哪些内容）。 应显示已上传的内容，准备好进行人工审阅。

![足球的审查工具图像](images/test-drive-review-5.PNG)

## <a name="get-review-details"></a>获取查看详细信息

若要检索有关现有评审的详细信息，请转到[查看-获取](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c2)API 引用页，选择你所在区域的按钮 （在其中管理你的密钥的区域）。

![“工作流 - 获取”区域选择](images/test-drive-region.png)

输入 REST 调用参数，如在上面的部分。 此步骤中，对于**reviewId**收到创建评审时的唯一 ID 字符串。

![审查 - 创建控制台获取结果](images/test-drive-review-3.PNG)
  
选择“发送”。 如果操作成功，**响应状态**是`200 OK`，和**响应内容**框显示查看详细信息以 JSON 格式，如下所示：

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
- **Reviewerresulttag**:这会显示任何标记已手动添加由人工审阅团队 (所示**createdBy**字段)。
- **metadata**：这将显示在查看之前的人工审阅团队所做更改最初添加的标记。

## <a name="next-steps"></a>后续步骤

在本指南中，您学习了如何创建使用 REST API 的内容审查评论。 接下来，将集成评审到一种端到端的适度性方案，如[电子商务审查](./ecommerce-retail-catalog-moderation.md)教程。