---
title: 在 Azue 内容审查器中运行内容审查作业 | Microsoft Docs
description: 了解如何运行 API 控制台中的内容审查作业。
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 08/03/2017
ms.author: sajagtap
ms.openlocfilehash: 6f741be1001ae70d5fdbf6f374204aaad1601abe
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365464"
---
# <a name="start-a-moderation-job-from-the-api-console"></a>启动 API 控制台中的审查作业

使用审阅 API 的 [Job 操作](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5)，可以在 Azure 内容审查器中对图像或文本内容启动端到端内容审查作业。 

审查作业使用内容审查器图像审查 API 或文本审查 API 来扫描内容。 然后，审查作业使用（在“审阅”工具中定义的）工作流，在“审阅”工具中生成审阅。 

人工审查方会审阅自动分配的标记和预测数据，并提交最终审查决定。此后，审阅 API 会将所有信息都提交到 API 终结点。

## <a name="prerequisites"></a>先决条件

转到[“审阅”工具](https://contentmoderator.cognitive.microsoft.com/)。 如果尚未注册，请先注册。 在“审阅”工具中，[定义自定义工作流](Review-Tool-User-Guide/Workflows.md)，以供此 `Job` 操作使用。

## <a name="use-the-api-console"></a>使用 API 控制台
若要使用联机控制台试用 API，需要在控制台中输入下面一些值：
    
- `teamName`：使用“审阅”工具的“凭据”屏幕中的 `Id` 字段。 
- `ContentId`：此字符串传递到 API，并通过回叫返回。 ContentId 用于将内部标识符或元数据与审查作业结果相关联。- `Workflowname`：在上一部分中[创建的工作流](Review-Tool-User-Guide/Workflows.md)的名称。
- `Ocp-Apim-Subscription-Key`：位于“设置”选项卡中。有关详细信息，请参阅[概述](overview.md)。

在“凭据”窗口中访问 API 控制台。

### <a name="navigate-to-the-api-reference"></a>转到“API 参考”
在“凭据”窗口中，选择[“API 参考”](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5)。

  此时，“`Job.Create`”页打开。

### <a name="select-your-region"></a>选择区域
对于“开放 API 测试控制台”，选择与所在位置最相关的区域。
  ![“作业 - 创建”页上的区域选择](images/test-drive-job-1.png)

  此时，`Job.Create` API 控制台打开。 

### <a name="enter-parameters"></a>输入参数

输入相应查询参数的值和订阅密钥。 在“请求正文”框中，指定要扫描的信息位置。 本示例要扫描这张[示例图像](https://moderatorsampleimages.blob.core.windows.net/samples/sample6.png)。

  ![“作业 - 创建”控制台上的“查询参数”、“请求头”和“请求正文”框](images/job-api-console-inputs.PNG)

### <a name="submit-your-request"></a>提交请求
选择“发送”。 此时，系统创建作业 ID。 复制此 ID，以便在后续步骤中使用。

  `"JobId": "2018014caceddebfe9446fab29056fd8d31ffe"`

### <a name="open-the-get-job-details-page"></a>打开“获取作业详细信息”页
选择“获取”，再选择与区域匹配的按钮来打开 API。

  ![“作业 - 创建”控制台上的“获取”结果](images/test-drive-job-4.png)

### <a name="review-the-response"></a>检查响应

输入 teamName 和 JobID 的值。 输入订阅密钥，再选择“发送”。 下面的响应包含示例作业状态和详细信息。

```
    {
        "Id": "2018014caceddebfe9446fab29056fd8d31ffe",
        "TeamName": "some team name",
        "Status": "InProgress",
        "WorkflowId": "OCR",
        "Type": "Image",
        "CallBackEndpoint": "",
        "ReviewId": "",
        "ResultMetaData": [],
        "JobExecutionReport": 
        [
            {
            "Ts": "2018-01-07T00:38:26.7714671",
                "Msg": "Successfully got hasText response from Moderator"
            },
            {
                "Ts": "2018-01-07T00:38:26.4181346",
                "Msg": "Getting hasText from Moderator"
            },
            {
                "Ts": "2018-01-07T00:38:25.5122828",
                "Msg": "Starting Execution - Try 1"
            }
        ]
    }
```

## <a name="navigate-to-the-review-tool"></a>转到“审阅”工具
在内容审查器仪表板上，依次选择“审阅” > “图像”。 此时，已扫描的图像显示，可供人工审阅。

  ![三个骑自行车的人的“审阅”工具图像](images/ocr-sample-image.PNG)

## <a name="next-steps"></a>后续步骤

若要与应用集成，请在代码中使用 REST API，或从[作业 .NET 快速入门](moderation-jobs-quickstart-dotnet.md)入手。
