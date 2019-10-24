---
title: 通过 REST API 控制台定义审核工作流-内容审查器
titleSuffix: Azure Cognitive Services
description: 你可以使用 Azure 内容审查器审核 Api 来定义基于你的内容策略的自定义工作流和阈值。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: pafarley
ms.openlocfilehash: 3e58be4b94457d95d28cf6528b9151e4be1802cf
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/22/2019
ms.locfileid: "72754190"
---
# <a name="define-and-use-moderation-workflows-rest"></a>定义和使用审核工作流（REST）

工作流是一种基于云的自定义筛选器，可用于更有效地处理内容。 工作流可以连接到多种服务，以不同的方式筛选内容，然后采取相应的措施。 本指南演示如何通过 API 控制台使用工作流 REST Api 创建和使用工作流。 了解 Api 的结构后，可以轻松地将这些调用移植到任何与 REST 兼容的平台。

## <a name="prerequisites"></a>必备组件

- 登录或创建内容审查器[审核工具](https://contentmoderator.cognitive.microsoft.com/)站点上的帐户。

## <a name="create-a-workflow"></a>创建工作流

若要创建或更新工作流，请在 " **[工作流"-"创建或更新](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59)** API 引用" 页上，选择用于关键区域的按钮（可在 "[查看工具](https://contentmoderator.cognitive.microsoft.com/)" 的 "**凭据**" 页上的 "终结点 URL" 中找到此项）。 这会启动 API 控制台，你可以在其中轻松构造和运行 REST API 调用。

![“工作流 - 创建或更新”页上的区域选择](images/test-drive-region.png)

### <a name="enter-rest-call-parameters"></a>输入 REST 调用参数

为**team**、 **workflowname**和**Apim-Key**输入值：

- **团队**：设置[审阅工具](https://contentmoderator.cognitive.microsoft.com/)帐户时创建的团队 ID （在查看工具的凭据屏幕上的**ID**字段中找到）。
- **workflowname**：要添加的新工作流的名称（或现有名称，如果要更新现有工作流）。
- **Apim-key**：你的内容审查器密钥。 可以在 "[查看" 工具](https://contentmoderator.cognitive.microsoft.com)的 "**设置**" 选项卡上找到。

![“工作流 - 创建或更新”控制台查询参数和请求头](images/workflow-console-parameters.PNG)

### <a name="enter-a-workflow-definition"></a>输入工作流定义

1. 编辑 "**请求正文**" 框，输入包含 "**说明**" 和 "**类型**" 的详细信息（`Image` 或 `Text`）的 JSON 请求。
2. 对于**Expression**，请复制默认工作流 JSON 表达式。 最终的 JSON 字符串应如下所示：

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
> 您可以使用此 API 为工作流定义简单、复杂甚至嵌套的表达式。 [工作流创建或更新](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59)文档包含更复杂逻辑的示例。

### <a name="submit-your-request"></a>提交请求
  
选择“发送”。 如果操作成功，“响应状态”为“`200 OK`”，且“响应内容”框显示“`true`”。

### <a name="examine-the-new-workflow"></a>检查新工作流

在 "[查看" 工具](https://contentmoderator.cognitive.microsoft.com/)中，选择 "**设置**"  > **工作流**"。 新工作流应出现在列表中。

![“审阅”工具的工作流列表](images/workflow-console-new-workflow.PNG)

选择工作流的 "**编辑**" 选项，并中转到 "**设计器**" 选项卡。在这里，可以看到 JSON 逻辑的直观表示形式。

![选定工作流的“设计器”选项卡](images/workflow-console-new-workflow-designer.PNG)

## <a name="get-workflow-details"></a>获取工作流详细信息

若要检索有关现有工作流的详细信息，请转到 " **[工作流-获取](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b44b3f9b0711b43c4c58)** API 引用" 页，然后选择你的区域（你的密钥被管理到的区域）的按钮。

![“工作流 - 获取”区域选择](images/test-drive-region.png)

输入 REST 调用参数，如以上部分所示。 请确保此时间， **workflowname**是现有工作流的名称。

![“获取”查询参数和请求头](images/workflow-get-default.PNG)

选择“发送”。 如果操作成功，则**响应状态**为 "`200 OK`"，"**响应内容**" 框显示 JSON 格式的工作流，如下所示：

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