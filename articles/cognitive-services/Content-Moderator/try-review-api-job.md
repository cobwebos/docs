---
title: 将审核作业与 REST API 控制台配合使用 - 内容审查器
titleSuffix: Azure Cognitive Services
description: 使用审阅 API 的 Job 操作，可以在 Azure 内容审查器中对图像或文本内容启动端到端内容审查作业。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: pafarley
ms.openlocfilehash: 83ee8e0c0583cba72da8702e196f0f38128f8d8a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "72935942"
---
# <a name="define-and-use-moderation-jobs-rest"></a>定义和使用审核作业 (REST)

审核作业充当内容审核、工作流和审阅功能的一种包装器。 本指南演示如何使用作业 REST API 来启动和检查内容审核作业。 了解 API 的结构后，可以轻松将这些调用移植到任何与 REST 兼容的平台。

## <a name="prerequisites"></a>先决条件

- 在内容审查器[审阅工具](https://contentmoderator.cognitive.microsoft.com/)站点上登录或创建帐户。
- （可选）[定义自定义工作流](./Review-Tool-User-Guide/Workflows.md)以与作业配合使用；还可以使用默认工作流。

## <a name="create-a-job"></a>创建作业

若要创建审核作业，请转到[作业 - 创建](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5) API 参考页，然后选择对应于订阅区域的按钮（位于[审阅工具](https://contentmoderator.cognitive.microsoft.com/)的“凭据”**** 页的“终结点 URL”中）。 这将启动 API 控制台，你可以在其中轻松构造和运行 REST API 调用。

![“作业 - 创建”页区域选择](images/test-drive-job-1.png)

### <a name="enter-rest-call-parameters"></a>输入 REST 调用参数

输入以下值来构造 REST 调用：

- **teamName**：设置[审阅工具](https://contentmoderator.cognitive.microsoft.com/)帐户时创建的团队 ID（位于审阅工具的“凭据”屏幕的“ID”**** 字段中）。
- **ContentType**：可以是“图像”、“文本”或“视频”。
- **ContentId**：自定义标识符字符串。 此字符串将传递给 API，并通过回调返回。 它在关联内部标识符或元数据与审查作业结果时十分有用。
- **Workflowname**：之前创建的工作流的名称（对于默认工作流，为“default”）。
- **CallbackEndpoint**：（可选）完成审阅时用于接收回调信息的 URL。
- **Ocp-Apim-Subscription-Key**：内容审查器密钥。 可以在[审阅工具](https://contentmoderator.cognitive.microsoft.com)的“设置”**** 选项卡上找到它。

### <a name="fill-in-the-request-body"></a>填写请求正文

REST 调用的正文包含“ContentValue”**** 字段。 如果要审核文本，请粘贴原始文本内容；如果要审核图像/视频，请输入图像或视频 URL。 可使用以下示例图像 URL：[https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg](https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg)

![“作业 - 创建”控制台上的“查询参数”、“请求头”和“请求正文”框](images/job-api-console-inputs.PNG)

### <a name="submit-your-request"></a>提交请求

选择“发送”。**** 如果该操作成功，“响应状态”为 `200 OK`，且“响应内容”框显示作业的 ID**** ****。 复制此 ID，然后在以下步骤中使用它。

![审查 - 创建控制台响应内容框显示审查 ID](images/test-drive-job-3.PNG)

## <a name="get-job-status"></a>获取作业状态

若要获取正在运行或已完成的作业的状态和详细信息，请转到[作业 - 获取](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c3) API 参考页，然后选择对应于所在区域（管理密钥的区域）的按钮。

![“作业 - 获取”区域选择](images/test-drive-region.png)

如上一部分所示，输入 REST 调用参数。 对于此步骤，“JobId”**** 是创建作业时收到的唯一 ID 字符串。 选择“发送”。**** 如果该操作成功，“响应状态”**** 为 `200 OK`，且“响应内容”**** 框以 JSON 格式显示作业，如下所示：

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

![“作业 - 获取”REST 调用响应](images/test-drive-job-5.png)

### <a name="examine-the-new-reviews"></a>检查新审阅

如果内容作业导致创建审阅，则可以在[审阅工具](https://contentmoderator.cognitive.microsoft.com)中进行查看。 选择“审阅”**** > “图像”****/“文本”****/“视频”****（取决于使用的内容）。 此时应显示内容，并可供人工审阅。 人工审查方会审阅自动分配的标记和预测数据，并提交最终审核决定。此后，作业 API 会将所有此类信息都提交到指定的回调终结点。

## <a name="next-steps"></a>后续步骤

在本指南中，你学习了如何使用 REST API 创建和查询内容审核作业。 接下来，请将作业集成到端到端审核方案中，例如[电子商务审核](./ecommerce-retail-catalog-moderation.md)教程。