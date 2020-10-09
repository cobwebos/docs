---
title: 使用 REST API 控制台定义审核工作流 - 内容审查器
titleSuffix: Azure Cognitive Services
description: 使用 Azure 内容审查器审阅 API，可以根据内容策略来定义自定义工作流和阈值。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: pafarley
ms.openlocfilehash: 3e58be4b94457d95d28cf6528b9151e4be1802cf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "72754190"
---
# <a name="define-and-use-moderation-workflows-rest"></a>定义和使用审核工作流 (REST)

工作流是基于云的自定义筛选器，可用于更有效地处理内容。 工作流可以连接到多种服务，以通过不同的方式筛选内容，然后采取相应操作。 本指南演示如何通过 API 控制台使用工作流 REST API 来创建和使用工作流。 了解 API 的结构后，可以轻松将这些调用移植到任何与 REST 兼容的平台。

## <a name="prerequisites"></a>先决条件

- 在内容审查器[审阅工具](https://contentmoderator.cognitive.microsoft.com/)站点上登录或创建帐户。

## <a name="create-a-workflow"></a>创建工作流

若要创建或更新工作流，请转到[工作流 - 创建或更新](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59)**** API 参考页，然后选择对应于密钥区域的按钮（位于[审阅工具](https://contentmoderator.cognitive.microsoft.com/)的“凭据”**** 页的“终结点 URL”中）。 这将启动 API 控制台，你可以在其中轻松构造和运行 REST API 调用。

![“工作流 - 创建或更新”页上的区域选择](images/test-drive-region.png)

### <a name="enter-rest-call-parameters"></a>输入 REST 调用参数

输入“team”****、“workflowname”**** 和“Ocp-Apim-Subscription-Key”**** 的值：

- **team**：设置[审阅工具](https://contentmoderator.cognitive.microsoft.com/)帐户时创建的团队 ID（位于审阅工具的“凭据”屏幕的“ID”**** 字段中）。
- **workflowname**：要添加的新工作流的名称（如果要更新现有工作流，则为现有名称）。
- **Ocp-Apim-Subscription-Key**：内容审查器密钥。 可以在[审阅工具](https://contentmoderator.cognitive.microsoft.com)的“设置”**** 选项卡上找到它。

![“工作流 - 创建或更新”控制台查询参数和请求头](images/workflow-console-parameters.PNG)

### <a name="enter-a-workflow-definition"></a>输入工作流定义

1. 编辑“请求正文”**** 框，以输入 JSON 请求，以及“说明”**** 和“类型”**** 的详细信息（`Image` 或 `Text`）。
2. 对于“表达式”****，复制默认工作流 JSON 表达式。 最终 JSON 字符串应如下所示：

```json
{
  "Description":"<A description for the Workflow>",
  "Type":"Text",
  "Expression":{
    "Type":"Logic",
    "If":{
      "ConnectorName":"moderator",
      "OutputName":"isAdult",
      "Operator":"eq",
      "Value":"true",
      "Type":"Condition"
    },
    "Then":{
      "Perform":[
        {
          "Name":"createreview",
          "CallbackEndpoint":null,
          "Tags":[

          ]
        }
      ],
      "Type":"Actions"
    }
  }
}
```

> [!NOTE]
> 使用此 API，可以为工作流定义简单、复杂和嵌套的表达式。 [工作流 - 创建或更新](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59)文档包含更复杂逻辑的示例。

### <a name="submit-your-request"></a>提交请求
  
选择“发送”。**** 如果操作成功，“响应状态”**** 为“`200 OK`”，且“响应内容”**** 框显示“`true`”。

### <a name="examine-the-new-workflow"></a>检查新工作流

在[审阅工具](https://contentmoderator.cognitive.microsoft.com/)中，依次选择“设置”**** > “工作流”****。 新工作流应在列表中显示。

![“审阅”工具的工作流列表](images/workflow-console-new-workflow.PNG)

选择工作流的“编辑”**** 选项，然后转到“设计器”**** 选项卡。在这里，可以看到 JSON 逻辑的直观表示形式。

![选定工作流的“设计器”选项卡](images/workflow-console-new-workflow-designer.PNG)

## <a name="get-workflow-details"></a>获取工作流详细信息

若要检索有关现有工作流的详细信息，请转到[工作流 - 获取](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b44b3f9b0711b43c4c58)**** API 参考页，然后选择对应于所在区域（管理密钥的区域）的按钮。

![“工作流 - 获取”区域选择](images/test-drive-region.png)

如上一部分所示，输入 REST 调用参数。 确保这次的“workflowname”**** 是现有工作流的名称。

![“获取”查询参数和请求头](images/workflow-get-default.PNG)

选择“发送”。**** 如果该操作成功，“响应状态”为 `200 OK`，且“响应内容”框以 JSON 格式显示工作流，如下所示**** ****：

```json
{
  "Name":"default",
  "Description":"Default",
  "Type":"Image",
  "Expression":{
    "If":{
      "ConnectorName":"moderator",
      "OutputName":"isadult",
      "Operator":"eq",
      "Value":"true",
      "AlternateInput":null,
      "Type":"Condition"
    },
    "Then":{
      "Perform":[
        {
          "Name":"createreview",
          "Subteam":null,
          "CallbackEndpoint":null,
          "Tags":[

          ]
        }
      ],
      "Type":"Actions"
    },
    "Else":null,
    "Type":"Logic"
  }
}
```

## <a name="next-steps"></a>后续步骤

- 了解如何结合使用工作流和[内容审查作业](try-review-api-job.md)。