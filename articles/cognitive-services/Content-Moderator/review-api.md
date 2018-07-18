---
title: Azure 内容审查器 - 审查作业和人机回环审阅 | Microsoft Docs
description: 为了获得最佳结果，请在计算机辅助审查中引入人工监督。
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 1/21/2018
ms.author: sajagtap
ms.openlocfilehash: 35b3cdaa410712c3fd08d3df4ebe4c83e3955d50
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365523"
---
# <a name="moderation-jobs-and-reviews"></a>审查作业和审阅

为了获得最佳业务结果，请使用 Azure 内容审查器的[审阅 API](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5)，将计算机辅助审查与人机回环功能相结合。

使用审阅 API，可以通过下列方法，在内容审查流程中引入人工监督：

* `Job` 操作用于在一步中启动计算机辅助审查和人工审阅创建。
* `Review` 操作用于分开执行人工审阅创建和审查步骤。
* `Workflow` 操作用于管理自动执行扫描的工作流（含审阅创建阈值）。

`Job` 和 `Review` 操作接受用于接收状态和结果的回叫终结点。

本文介绍了 `Job` 和 `Review` 操作。 若要了解如何创建、编辑和获取工作流定义，请阅读[工作流概述](workflow-api.md)。

## <a name="job-operations"></a>作业操作

### <a name="start-a-job"></a>启动作业
`Job.Create` 操作可用于启动审查和人工审阅创建作业。 内容审查器扫描内容，并评估指定的工作流。 根据工作流结果，内容审查器要么创建审阅，要么跳过这一步。 它还向回叫终结点提交审查后和审阅后标记。

输入内容包含以下信息：

- 审阅团队 ID。
- 要审查的内容。
- 工作流名称。 （默认名称为“默认”工作流。）
- 用于通知的 API 回叫点。
 
下面的响应展示了已启动作业的标识符。 作业标识符可用于获取作业状态，并接收详细信息。

    {
        "JobId": "2018014caceddebfe9446fab29056fd8d31ffe"
    }

### <a name="get-job-status"></a>获取作业状态

使用 `Job.Get` 操作和作业标识符可以获取正在运行或已完成作业的详细信息。 此操作返回以异步方式运行的审查作业的结果。 结果通过回叫终结点返回。

输入内容包含以下信息：

- 审阅团队 ID：上一操作返回的作业标识符

响应包含以下信息：

- 已创建审阅的标识符。 （此 ID 可用于获取最终审阅结果。）
- 作业状态（已完成或进行中）：分配的审查标记（键值对）。
- 作业执行报告。
 
 
        {
            "Id": "2018014caceddebfe9446fab29056fd8d31ffe",
            "TeamName": "some team name",
            "Status": "Complete",
            "WorkflowId": "OCR",
            "Type": "Image",
            "CallBackEndpoint": "",
            "ReviewId": "201801i28fc0f7cbf424447846e509af853ea54",
            "ResultMetaData":[
            {
            "Key": "hasText",
            "Value": "True"
            },
            {
            "Key": "ocrText",
            "Value": "IF WE DID \r\nALL \r\nTHE THINGS \r\nWE ARE \r\nCAPABLE \r\nOF DOING, \r\nWE WOULD \r\nLITERALLY \r\nASTOUND \r\nOURSELVE \r\n"
            }
            ],
            "JobExecutionReport": [
            {
                "Ts": "2018-01-07T00:38:29.3238715",
                "Msg": "Posted results to the Callbackendpoint: https://requestb.in/vxke1mvx"
                },
                {
                "Ts": "2018-01-07T00:38:29.2928416",
                "Msg": "Job marked completed and job content has been removed"
                },
                {
                "Ts": "2018-01-07T00:38:29.0856472",
                "Msg": "Execution Complete"
                },
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
 
![供人工审查方审阅的图像](images/ocr-sample-image.PNG)

## <a name="review-operations"></a>审阅操作

### <a name="create-reviews"></a>创建审阅

`Review.Create` 操作可用于创建人工审阅。 要么在其他地方审查它们，要么使用自定义逻辑分配审查标记。

此操作的输入内容包括：

- 要审阅的内容。
- 供人工审查方审阅的已分配标记（键值对）。

下面的响应展示了审阅标识符：

    [
        "201712i46950138c61a4740b118a43cac33f434",
    ]


### <a name="get-review-status"></a>获取审阅状态
`Review.Get` 操作可用于在完成人工审阅已审查的图像后获取结果。 通知通过所提供的回叫终结点返回。 

此操作返回两组标记： 

* 审查服务分配的标记
* 人工审阅完成后的标记

输入内容至少包括：

- 审阅团队名称
- 上一操作返回的审阅标识符

响应包含以下信息：

- 审阅状态
- 人工审阅者确认的标记（键值对）
- 审查服务分配的标记（键值对）

下面的示例响应包含审阅者分配的标记（“reviewerResultTags”）和初始标记（“metadata”）：

    {
        "reviewId": "201712i46950138c61a4740b118a43cac33f434",
        "subTeam": "public",
        "status": "Complete",
        "reviewerResultTags": [
        {
            "key": "a",
            "value": "False"
        },
        {
            "key": "r",
            "value": "True"
        },
        {
            "key": "sc",
            "value": "True"
        }
        ],
        "createdBy": "{teamname}",
        "metadata": [
        {
            "key": "sc",
            "value": "true"
        }
        ],
        "type": "Image",
        "content": "https://reviewcontentprod.blob.core.windows.net/{teamname}/IMG_201712i46950138c61a4740b118a43cac33f434",
        "contentId": "0",
        "callbackEndpoint": "{callbackUrl}"
    }

## <a name="next-steps"></a>后续步骤

* 试用[作业 API 控制台](try-review-api-job.md)，并运行 REST API 代码示例。 如果熟悉 Visual Studio 和 C#，还请参阅[作业 .NET 快速入门](moderation-jobs-quickstart-dotnet.md)。 
* 对于审阅，请开始使用[审阅 API 控制台](try-review-api-review.md)，并运行 REST API 代码示例。 然后，请参阅[审阅 .NET 快速入门](moderation-reviews-quickstart-dotnet.md)。
* 对于视频审阅，请参阅[视频审阅快速入门](video-reviews-quickstart-dotnet.md)，并了解如何[向视频审阅添加脚本](video-transcript-reviews-quickstart-dotnet.md)。
