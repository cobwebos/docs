---
title: Azure 内容审查器 - 审查工作流 | Microsoft Docs
description: 配合使用工作流和内容审核。
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 02/04/2018
ms.author: sajagtap
ms.openlocfilehash: 079fcd119f1536f9e76ca57fccc76538b3c3ed78
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365555"
---
# <a name="moderation-workflows"></a>审查工作流

内容审查器包括用于管理工作流的工具和 API。 配合使用工作流和[评审 API 的作业操作](review-api.md)，以便基于内容策略和阈值自动执行“人工介入”评审创建。

使用评审 API，可通过下列方法在内容审核流程中引入人工监督：

1. “作业”操作，用于一步启动计算机辅助审查和人工评审创建。
1. “评审”操作，用于执行审查步骤之外的人工评审创建。
1. “工作流”操作，用于管理自动执行阈值扫描的工作流以实现评审创建。

本文介绍“工作流”操作。 要了解内容审核作业和评审，请阅读[作业和评审](review-api.md)概述。

查看默认工作流是开始了解内容审查器中的工作流的最佳方式。

## <a name="your-first-workflow"></a>第一个工作流

第一个工作流与[评审工具团队](https://contentmoderator.cognitive.microsoft.com/)紧密相连。 如果尚未注册，请先注册。

导航到“设置”选项卡下的[评审工具工作流](Review-Tool-User-Guide/Workflows.md)屏幕。可以看到默认工作流，如下图所示：

![内容审查器工作流](Review-Tool-User-Guide/images/2-workflows-1.png)

### <a name="open-the-default-workflow"></a>打开默认工作流

使用“编辑”选项打开工作流编辑页，如下图所示：![内容审查器默认工作流](images/default-workflow-listed.PNG)

### <a name="the-designer-view"></a>设计器视图

可以看到工作流的“设计器”选项卡。 设计器视图显示以下步骤：

1. 要对其求值的工作流的“条件”。 在此示例中，工作流调用内容审查器图像 API 并检查 `isAdult` 输出是否等于 `true`。
1. 满足条件后要执行的“操作”。 在此示例中，如果 `isAdult` 输出为 `true`，则工作流将在评审工具中创建评审。

![内容审查器默认工作流 - 设计器](images/default-workflow-designer.png)

### <a name="the-json-view"></a>JSON 视图

要查看工作流的 JSON 定义，请选择“JSON”选项卡。

    {
        "Type": "Logic",
        If": {
            "ConnectorName": "moderator",
            "OutputName": "isAdult",
            "Operator": "eq",
            "Value": "true",
            "Type": "Condition"
        },
        "Then": {
        "Perform": [
        {
            "Name": "createreview",
            "CallbackEndpoint": null,
            "Tags": []
        }
        ],
        "Type": "Actions"
        }
    }

### <a name="key-learning"></a>学习要点

内容审查器中的工作流易于配置且十分灵活。 如果内置设计器不能满足需求，请以 JSON 格式编写工作流定义。 然后配合使用 JSON 定义和[工作流 API](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59) 来创建工作流，并通过应用程序进行管理。

## <a name="define-a-custom-workflow"></a>定义自定义工作流

内容审查器的工作流功能支持定义和使用自定义工作流。 利用[评审工具工作流操作指南](Review-Tool-User-Guide/Workflows.md)一文来定义自定义工作流。 此工作流使用内容审查器的 OCR 功能来提取示例图像中的文本。 然后在评审工具中创建评审。

### <a name="the-sample-image"></a>示例图像

将[示例图像](https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png)保存到本地驱动器。 需使用此图像进行练习。

### <a name="the-designer-view"></a>设计器视图

选择“设计器”选项卡并借助[工作流创建教程](Review-Tool-User-Guide/Workflows.md)来定义自定义工作流。 下图显示设计器的“条件”视图。 请参阅教程，查看剩余步骤。

![内容审查器 - 工作流条件](Review-Tool-User-Guide/images/ocr-workflow-step-2-condition.PNG)

### <a name="the-json-view"></a>JSON 视图

要查看自定义工作流的以下 JSON 定义，请选择“JSON”选项卡。 请注意 JSON 定义中的 If-Then 语句与使用设计器视图定义的步骤的对应方式。

    {
        "Type": "Logic",
        "If": {
            "ConnectorName": "moderator",
            "OutputName": "hasText",
            "Operator": "eq",
            "Value": "true",
            "Type": "Condition"
        },
        "Then": {
        "Perform": [
        {
            "Name": "createreview",
            "CallbackEndpoint": null,
            "Tags": [
            {
                "Tag": "a",
                "IfCondition": {
                    "ConnectorName": "moderator",
                    "OutputName": "hasText",
                    "Operator": "eq",
                    "Value": "true",
                    "Type": "Condition"
                }
            }
            ]
        }
        ],
        "Type": "Actions"
        }
    }

### <a name="workflow-result"></a>工作流结果

在工作流屏幕中测试工作流后，将创建以下评审。 导航到“评审”下的“图像”选项卡，查看评审。
该工作流已创建评审，因为针对文本是否存在的主要条件测试得出了肯定结果。 此外，评审还在图像评审中突出显示了 `a` 标记。

![内容审查器 - 简单工作流输出](images/ocr-sample-image-workflow1.PNG)


## <a name="advanced-workflow-with-combination"></a>高级组合工作流

### <a name="the-sample-image"></a>示例图像

使用前一部分中使用的同一张[示例图像](https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png)。

但这一次将主条件更改为两个检查的组合。 除了检查文本，还需检查文本是否包含任何不当字词。 如果工作流找到文本并检测到其中存在不当字词，工作流将创建评审。

### <a name="the-designer-view"></a>设计器视图

要将“条件”更改为“组合条件”，请修改工作流。 下图显示在设计器中显示的新视图。

![内容审查器 - 修改后的工作流条件](images/ocr-workflow-2-designer.PNG)

### <a name="the-json-view"></a>JSON 视图

要查看修改后的自定义工作流的以下 JSON 定义，请选择“JSON”选项卡。 请注意 JSON 定义中的 If-Then 语句与工作流中的新增步骤的对应方式。

    {
        "Type": "Logic",
        "If": {
        "Left": {
            "ConnectorName": "moderator",
            "OutputName": "hasText",
            "Operator": "eq",
            "Value": "true",
            "Type": "Condition"
            },
        "Right": {
            "ConnectorName": "moderator",
            "OutputName": "text.HasProfanity",
            "Operator": "eq",
            "Value": "true",
            "Type": "Condition",
            "AlternateInput": "moderator.ocrText"
            },
        "Combine": "AND",
        "Type": "Combine"
        },
        "Then": {
        "Perform": [
        {
            "Name": "createreview",
            "CallbackEndpoint": null,
            "Tags": [
            {
                "Tag": "a",
                "IfCondition": {
                    "ConnectorName": "moderator",
                    "OutputName": "hasText",
                    "Operator": "eq",
                    "Value": "true",
                    "Type": "Condition"
                }
            }
            ]
        }
        ],
        "Type": "Actions"
        }
    }

    
### <a name="workflow-result"></a>工作流结果

再次测试工作流后，可发现未创建任何评审。 若要确认没有任何评审，请导航到“评审”下的“图像”选项卡。
此工作流未创建评审，因为它未在提取文本中检测到不当字词。

![内容审查器 - 修改后的工作流输出](images/ocr-workflow-2-result.PNG)


## <a name="the-workflow-api"></a>工作流 API

[工作流操作](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59)提供工作流功能的编程接口。 使用工作流 API 创建工作流、获取工作流详细信息和更新工作流定义。

### <a name="get-all-workflow-details"></a>获取 [全部] 工作流详细信息

“工作流获取”操作接受以下输入：

- **team**：设置[评审工具帐户](https://contentmoderator.cognitive.microsoft.com/)时创建的团队 ID。 
- **workflowname**：工作流的名称。 以 `default` 开头。
- **Ocp-Apim-Subscription-Key**：位于“设置”选项卡。有关详细信息，请参阅[概述](overview.md)。

如果该操作成功，“响应状态”将为 `200 OK`，且“响应内容”框将以 JSON 格式显示工作流定义。
要了解详细信息，请阅读[工作流 API 控制台快速入门](try-review-api-job.md)。

### <a name="create-or-update-workflow"></a>创建或更新工作流

创建和更新操作允许通过 API 创建工作流。

“工作流创建”或“工作流更新”操作接受以下输入：

- **team**：设置[评审工具帐户](https://contentmoderator.cognitive.microsoft.com/)时创建的团队 ID。 
- **workflowname**：工作流的名称。 以 `default` 开头。
- **Ocp-Apim-Subscription-Key**：位于“设置”选项卡。有关详细信息，请参阅[概述](overview.md)。

如果该操作成功，“响应状态”将为 `200 OK`，且“响应内容”框显示 `true`。 要了解详细信息，请[体验 `Create` 操作](try-review-api-job.md)。

## <a name="next-steps"></a>后续步骤

要了解如何创建自定义工作流，请查看[评审工具工作流教程](Review-Tool-User-Guide/Workflows.md)。 

体验[工作流 API 控制台](try-review-api-job.md)和使用 REST API 代码示例。 

最后，通过“作业”操作使用自定义工作流，如[作业 API 控制台](try-review-api-job.md)和[作业 .NET 快速入门](moderation-jobs-quickstart-dotnet.md)中所示。
