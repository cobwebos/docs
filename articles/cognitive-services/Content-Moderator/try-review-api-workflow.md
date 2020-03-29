---
title: 使用 REST API 控制台定义审核工作流 - 内容审阅者
titleSuffix: Azure Cognitive Services
description: 可以使用 Azure 内容审阅者审阅 API 根据内容策略定义自定义工作流和阈值。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: pafarley
ms.openlocfilehash: 3e58be4b94457d95d28cf6528b9151e4be1802cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "72754190"
---
# <a name="define-and-use-moderation-workflows-rest"></a>定义和使用审核工作流 （REST）

工作流是基于云的自定义筛选器，可用于更高效地处理内容。 工作流可以连接到各种服务，以不同的方式筛选内容，然后采取适当的操作。 本指南介绍如何使用工作流 REST API（通过 API 控制台）创建和使用工作流。 了解 API 的结构后，可以轻松地将这些调用移植到任何与 REST 兼容的平台。

## <a name="prerequisites"></a>先决条件

- 在内容审阅人[审核工具](https://contentmoderator.cognitive.microsoft.com/)网站上登录或创建帐户。

## <a name="create-a-workflow"></a>创建工作流

要创建或更新工作流，请转到工作流**[- 创建或更新](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59)** API 参考页，然后选择关键区域的按钮（您可以在["审阅"工具](https://contentmoderator.cognitive.microsoft.com/)**的凭据**页面上的终结点 URL 中找到该按钮）。 这将启动 API 控制台，您可以在其中轻松构造和运行 REST API 调用。

![“工作流 - 创建或更新”页上的区域选择](images/test-drive-region.png)

### <a name="enter-rest-call-parameters"></a>输入 REST 呼叫参数

输入**团队**、**工作流名称**和**Ocp-Apim-订阅密钥**的值：

- **团队**：您在设置["审阅"工具](https://contentmoderator.cognitive.microsoft.com/)帐户时创建的团队 ID（在审阅工具的凭据屏幕上的 **"Id"** 字段中找到）。
- **工作流名称**：要添加的新工作流的名称（或要更新现有工作流的现有名称）。
- **Ocp-Apim-订阅密钥**：您的内容审阅者密钥。 您可以在["查看"工具](https://contentmoderator.cognitive.microsoft.com)的 **"设置"** 选项卡上找到此内容。

![“工作流 - 创建或更新”控制台查询参数和请求头](images/workflow-console-parameters.PNG)

### <a name="enter-a-workflow-definition"></a>输入工作流定义

1. 编辑 **"请求正文**"框以输入 JSON 请求，并包含**描述**和**类型**的详细信息`Text`（或`Image`）。
2. 对于**表达式**，复制默认工作流 JSON 表达式。 您的最终 JSON 字符串应如下所示：

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
> 您可以使用此 API 为工作流定义简单、复杂甚至嵌套表达式。 [工作流 - 创建或更新](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59)文档具有更复杂的逻辑示例。

### <a name="submit-your-request"></a>提交请求
  
选择 **"发送**"。 如果操作成功，“响应状态”**** 为“`200 OK`”，且“响应内容”**** 框显示“`true`”。

### <a name="examine-the-new-workflow"></a>检查新工作流

在["审阅"工具](https://contentmoderator.cognitive.microsoft.com/)中，选择 **"设置** > **工作流**"。 您的新工作流应显示在列表中。

![“审阅”工具的工作流列表](images/workflow-console-new-workflow.PNG)

选择工作流的 **"编辑"** 选项，然后转到 **"设计器"** 选项卡。在这里，您可以看到 JSON 逻辑的直观表示形式。

![选定工作流的“设计器”选项卡](images/workflow-console-new-workflow-designer.PNG)

## <a name="get-workflow-details"></a>获取工作流详细信息

若要检索有关现有工作流的详细信息，请转到工作流**[- 获取](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b44b3f9b0711b43c4c58)** API 参考页，然后选择区域的按钮（管理密钥的区域）。

![“工作流 - 获取”区域选择](images/test-drive-region.png)

输入上一节中所示的 REST 调用参数。 确保这次**工作流名称**是现有工作流的名称。

![“获取”查询参数和请求头](images/workflow-get-default.PNG)

选择 **"发送**"。 如果操作成功，**响应状态**为`200 OK`，**响应内容**框以 JSON 格式显示工作流，如下所示：

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