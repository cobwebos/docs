---
title: 使用 REST API 控制台-内容审查器审查作业
titlesuffix: Azure Cognitive Services
description: 使用审阅 API 的 Job 操作，可以在 Azure 内容审查器中对图像或文本内容启动端到端内容审查作业。
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 03/18/2019
ms.author: sajagtap
ms.openlocfilehash: 7827cee2af2dfc0c1fddc407c1d146dc9a66c514
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60607558"
---
# <a name="define-and-use-moderation-jobs-rest"></a>定义和使用审查作业 (REST)

审查作业可作为一种类型的包装器的功能的内容审查、 工作流和评审。 本指南演示如何使用作业 REST Api 启动并检查内容审查作业。 一旦您了解的 Api 结构，您可以轻松地移植到任何 REST 兼容平台这些调用。

## <a name="prerequisites"></a>必备组件

- 登录或在内容审查器上创建一个帐户[审阅工具](https://contentmoderator.cognitive.microsoft.com/)站点。
- （可选）[定义自定义工作流](./Review-Tool-User-Guide/Workflows.md)用于作业; 还可以使用默认工作流。

## <a name="create-a-job"></a>创建作业

若要创建审查作业，请转到[作业-创建](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5)API 引用页，选择密钥中你所在区域的按钮 (您可以在中找到此终结点 URL 上**凭据**页[评审工具](https://contentmoderator.cognitive.microsoft.com/))。 这将启动 API 控制台中，您可以轻松地构建和运行的 REST API 调用。

![作业-创建页面区域选择](images/test-drive-job-1.png)

### <a name="enter-rest-call-parameters"></a>输入 REST 调用参数

输入以下值来构造 REST 调用：

- **teamName**：您设置时创建的团队 ID 你[审阅工具](https://contentmoderator.cognitive.microsoft.com/)帐户 (在中找到**Id**评审工具的凭据屏幕上的字段)。
- **ContentType**:这可以是"Image"、"Text"或"视频"。
- **ContentId**：一个自定义的标识符的字符串。 此字符串将传递给 API，并通过回调返回。 它可用于将与审查作业的结果相关联的内部标识符或元数据。
- **workflowname**:以前创建的工作流 （或"default"的默认工作流） 的名称。
- **CallbackEndpoint**:（可选）要评审完成时接收回调信息的 URL。
- **Ocp-Apim-Subscription-Key**：你的内容审查器密钥。 您可以在上找到此**设置**选项卡[审阅工具](https://contentmoderator.cognitive.microsoft.com)。

### <a name="fill-in-the-request-body"></a>请求正文中填充

在 REST 调用的正文将包含一个字段**ContentValue**。 如果审查文本、 粘贴在原始文本内容中或如果您正在审查图像/视频输入图像或视频 URL。 可以使用以下示例图像 URL: [https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg](https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg)

![“作业 - 创建”控制台上的“查询参数”、“请求头”和“请求正文”框](images/job-api-console-inputs.PNG)

### <a name="submit-your-request"></a>提交请求

选择“发送”。  如果操作成功，**响应状态**是`200 OK`，和**响应内容**框会显示作业的 ID。 复制此 ID，然后在以下步骤中使用它。

![审查 - 创建控制台响应内容框显示审查 ID](images/test-drive-job-3.PNG)

## <a name="get-job-status"></a>获取作业状态

若要获取的状态和正在运行或已完成作业的详细信息，请转到[作业-获取](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c3)API 引用页，选择你所在区域的按钮 （在其中管理你的密钥的区域）。

![作业-Get 区域选择](images/test-drive-region.png)

输入 REST 调用参数，如在上面的部分。 此步骤中，对于**JobId**是在创建作业时收到的唯一 ID 字符串。 选择“发送”。  如果操作成功，**响应状态**是`200 OK`，和**响应内容**框显示该作业以 JSON 格式，如下所示：

```json
{  
  "Id":"2018014caceddebfe9446fab29056fd8d31ffe",
  "TeamName":"some team name",
  "Status":"Complete",
  "WorkflowId":"OCR",
  "Type":"Image",
  "CallBackEndpoint":"",
  "ReviewId":"201801i28fc0f7cbf424447846e509af853ea54",
  "ResultMetaData":[  
    {  
      "Key":"hasText",
      "Value":"True"
    },
    {  
      "Key":"ocrText",
      "Value":"IF WE DID \r\nALL \r\nTHE THINGS \r\nWE ARE \r\nCAPABLE \r\nOF DOING, \r\nWE WOULD \r\nLITERALLY \r\nASTOUND \r\nOURSELVE \r\n"
    }
  ],
  "JobExecutionReport":[  
    {  
      "Ts":"2018-01-07T00:38:29.3238715",
      "Msg":"Posted results to the Callbackendpoint: https://requestb.in/vxke1mvx"
    },
    {  
      "Ts":"2018-01-07T00:38:29.2928416",
      "Msg":"Job marked completed and job content has been removed"
    },
    {  
      "Ts":"2018-01-07T00:38:29.0856472",
      "Msg":"Execution Complete"
    },
    {  
      "Ts":"2018-01-07T00:38:26.7714671",
      "Msg":"Successfully got hasText response from Moderator"
    },
    {  
      "Ts":"2018-01-07T00:38:26.4181346",
      "Msg":"Getting hasText from Moderator"
    },
    {  
      "Ts":"2018-01-07T00:38:25.5122828",
      "Msg":"Starting Execution - Try 1"
    }
  ]
}
```

![作业-获取 REST 调用响应](images/test-drive-job-5.png)

### <a name="examine-the-new-reviews"></a>检查新 review(s)

如果内容作业导致评审的创建，则可以查看它在[审阅工具](https://contentmoderator.cognitive.microsoft.com)。 选择**评审** > **图像**/**文本**/**视频**（具体取决于什么内容使用）。 应显示内容，可供人工审阅。 人工审查器评审自动分配的标记和预测数据，并将提交最终审查决策后，作业 API 将提交所有这些信息的指定的回调终结点终结点。

## <a name="next-steps"></a>后续步骤

在本指南中，您学习了如何创建和查询使用 REST API 的内容审查作业。 接下来，将集成作业到一种端到端的适度性方案，如[电子商务审查](./ecommerce-retail-catalog-moderation.md)教程。