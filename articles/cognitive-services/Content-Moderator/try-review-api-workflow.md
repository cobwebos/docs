---
title: Azure 内容审查器 - API 控制台中的内容审查工作流 | Microsoft Docs
description: 了解如何使用 API 控制台中的内容审查工作流。
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 02/05/2018
ms.author: sajagtap
ms.openlocfilehash: 700b2bea5e902141659266a94d61ceb810c1b802
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365525"
---
# <a name="workflows-from-the-api-console"></a>API 控制台中的工作流

在 Azure 内容审查器中使用审阅 API 的 [workflow 操作](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59)，可以创建或更新工作流，也可以获取工作流详细信息。 使用此 API，可以为工作流定义简单、复杂和嵌套的表达式。 工作流显示在“审阅”工具中，以供团队使用。 工作流还可供审阅 API 的 Job 操作使用。

## <a name="prerequisites"></a>先决条件

1. 转到[“审阅”工具](https://contentmoderator.cognitive.microsoft.com/)。 如果尚未注册，请先注册。 
2. 在“审阅”工具中，选择“设置”下的“工作流”选项卡，如“审阅”工具的[工作流教程](Review-Tool-User-Guide/Workflows.md)中所示。

### <a name="browse-to-the-workflows-screen"></a>转到“工作流”屏幕

在内容审查器仪表板上，依次选择“审阅” > “设置” > “工作流”。 此时，默认工作流显示。

  ![默认工作流](images/default-workflow-listed.PNG)

### <a name="get-the-json-definition-of-the-default-workflow"></a>获取默认工作流的 JSON 定义

依次选择工作流的“编辑”选项和“JSON”选项卡。此时，以下 JSON 表达式显示：

    {
        "Type": "Logic",
        "If": {
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

## <a name="get-workflow-details"></a>获取工作流详细信息

“工作流 - 获取”操作可用于获取现有默认工作流的详细信息。

在“审阅”工具中，转到[“凭据”](Review-Tool-User-Guide/credentials.md#the-review-tool)部分。

### <a name="browse-to-the-api-reference"></a>转到“API 参考”

1. 在“凭据”视图中，选择[“API 参考”](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59)。 
2. 当“工作流 - 创建或更新”页打开时，转到[“工作流 - 获取”](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b44b3f9b0711b43c4c58)参考。

### <a name="select-your-region"></a>选择区域

对于“开放 API 测试控制台”，选择与所在位置最相关的区域。

  ![“工作流 - 获取”区域选择](images/test-drive-region.png)

  此时，“工作流 - 获取”API 控制台打开。

### <a name="enter-parameters"></a>输入参数

输入“team”、“workflowname”和“Ocp-Apim-Subscription-Key”（订阅密钥）的值：

- **team**：设置[“审阅”工具帐户](https://contentmoderator.cognitive.microsoft.com/)时创建的团队 ID。 
- **workflowname**：工作流名称。 使用 `default`。
- **Ocp-Apim-Subscription-Key**：位于“设置”选项卡上。有关详细信息，请参阅[概述](overview.md)。

  ![“获取”查询参数和请求头](images/workflow-get-default.PNG)

### <a name="submit-your-request"></a>提交请求
  
选择“发送”。 如果操作成功，“响应状态”为“`200 OK`”，且“响应内容”框显示以下 JSON 工作流：

    {
        "Name": "default",
        "Description": "Default",
        "Type": "Image",
        "Expression": {
        "If": {
            "ConnectorName": "moderator",
            "OutputName": "isadult",
            "Operator": "eq",
            "Value": "true",
            "AlternateInput": null,
            "Type": "Condition"
            },
        "Then": {
            "Perform": [{
                "Name": "createreview",
                "Subteam": null,
                "CallbackEndpoint": null,
                "Tags": []
            }],
            "Type": "Actions"
            },
            "Else": null,
            "Type": "Logic"
            }
    }


## <a name="create-a-workflow"></a>创建工作流

在“审阅”工具中，转到[“凭据”](Review-Tool-User-Guide/credentials.md#the-review-tool)部分。

### <a name="browse-to-the-api-reference"></a>转到“API 参考”

在“凭据”视图中，选择[“API 参考”](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59)。 此时，“工作流 - 创建或更新”页打开。

### <a name="select-your-region"></a>选择区域

对于“开放 API 测试控制台”，选择与所在位置最相关的区域。

  ![“工作流 - 创建或更新”页上的区域选择](images/test-drive-region.png)

  此时，“工作流 - 创建或更新”API 控制台打开。

### <a name="enter-parameters"></a>输入参数

输入“team”、“workflowname”和“Ocp-Apim-Subscription-Key”（订阅密钥）的值：

- **team**：设置[“审阅”工具帐户](https://contentmoderator.cognitive.microsoft.com/)时创建的团队 ID。 
- **workflowname**：新工作流的名称。
- **Ocp-Apim-Subscription-Key**：位于“设置”选项卡上。有关详细信息，请参阅[概述](overview.md)。

  ![“工作流 - 创建或更新”控制台查询参数和请求头](images/workflow-console-parameters.PNG)

### <a name="enter-the-workflow-definition"></a>输入工作流定义

1. 编辑“请求正文”框，以输入 JSON 请求，以及“说明”和“类型”（“图像”或“文本”）的详细信息。 
2. 对于“表达式”，复制上一部分中的默认工作流表达式，如下所示：

        {
            "Description": "Default workflow from API console",
            "Type": "Image",
            "Expression": 
                // Copy the default workflow expression from the preceding section
        }

    请求正文如下面的 JSON 请求所示：

        {
            "Description": "Default workflow from API console",
            "Type": "Image",
            "Expression": {
                "Type": "Logic",
                "If": {
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
                    "Tags": [ ]
                }
                ],
                "Type": "Actions"
                }
            }
        }
 
### <a name="submit-your-request"></a>提交请求
  
选择“发送”。 如果操作成功，“响应状态”为“`200 OK`”，且“响应内容”框显示“`true`”。

### <a name="check-out-the-new-workflow"></a>检查新工作流

在“审阅”工具中，依次选择“审阅” > “设置” > “工作流”。 此时，新建的工作流显示，可供使用。

  ![“审阅”工具的工作流列表](images/workflow-console-new-workflow.PNG)
  
### <a name="review-your-new-workflow-details"></a>查看新工作流的详细信息

1. 依次选择工作流的“编辑”选项、“设计器”和“JSON”选项卡。

   ![选定工作流的“设计器”选项卡](images/workflow-console-new-workflow-designer.PNG)

2. 若要查看工作流的 JSON 视图，请选择“JSON”选项卡。

## <a name="next-steps"></a>后续步骤

* 有关更多复杂工作流示例，请参阅[工作流概述](workflow-api.md)。
* 了解如何结合使用工作流和[内容审查作业](try-review-api-job.md)。
