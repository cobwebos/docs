---
title: 定义与 REST API 控制台-内容审查器审查工作流
titlesuffix: Azure Cognitive Services
description: Azure 内容审查器评审 Api 可用于定义自定义工作流和基于内容策略阈值。
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 03/14/2019
ms.author: sajagtap
ms.openlocfilehash: e150b1321f2fbd348e737222c752203281503643
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/01/2019
ms.locfileid: "58756587"
---
# <a name="define-and-use-moderation-workflows-rest"></a>定义和使用审查工作流 (REST)

工作流是基于云的自定义筛选器可用来更有效地处理内容。 工作流可以连接到各种服务来以不同方式筛选内容，然后采取相应的措施。 本指南演示如何使用 API 控制台中，通过工作流 REST Api 创建和使用工作流。 一旦您了解的 Api 结构，您可以轻松地移植到任何 REST 兼容平台这些调用。

## <a name="prerequisites"></a>必备组件

- 登录或在内容审查器上创建一个帐户[审阅工具](https://contentmoderator.cognitive.microsoft.com/)站点。

## <a name="create-a-workflow"></a>创建工作流

若要创建或更新的工作流，请转到**[工作流-创建或更新](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59)** API 引用页，选择密钥中你所在区域的按钮 (您可以在中找到此终结点 URL 上**凭据**页的[审阅工具](https://contentmoderator.cognitive.microsoft.com/))。 这将启动 API 控制台中，您可以轻松地构建和运行的 REST API 调用。

![“工作流 - 创建或更新”页上的区域选择](images/test-drive-region.png)

### <a name="enter-rest-call-parameters"></a>输入 REST 调用参数

输入值**团队**， **workflowname**，并**Ocp Apim 订阅密钥**:

- **team**：您设置时创建的团队 ID 你[审阅工具](https://contentmoderator.cognitive.microsoft.com/)帐户 (在中找到**Id**评审工具的凭据屏幕上的字段)。
- **workflowname**：要添加的新工作流 （或现有的名称，如果你想要更新现有工作流） 的名称。
- **Ocp-Apim-Subscription-Key**：你的内容审查器密钥。 您可以在上找到此**设置**选项卡[审阅工具](https://contentmoderator.cognitive.microsoft.com)。

![“工作流 - 创建或更新”控制台查询参数和请求头](images/workflow-console-parameters.PNG)

### <a name="enter-a-workflow-definition"></a>输入工作流定义

1. 编辑**请求正文**框中输入的详细信息的 JSON 请求**说明**并**类型**(或者`Image`或`Text`)。
2. 有关**表达式**，复制 JSON 表达式的默认工作流。 最终的 JSON 字符串应如下所示：

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
> 可以为使用此 API 工作流定义简单而甚至嵌套的复杂的表达式。 [工作流-创建或更新](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59)文档中有更复杂的逻辑的示例。

### <a name="submit-your-request"></a>提交请求
  
选择“发送”。 如果操作成功，“响应状态”为“`200 OK`”，且“响应内容”框显示“`true`”。

### <a name="examine-the-new-workflow"></a>检查新的工作流

在中[审阅工具](https://contentmoderator.cognitive.microsoft.com/)，选择**设置** > **工作流**。 新工作流应显示在列表中。

![“审阅”工具的工作流列表](images/workflow-console-new-workflow.PNG)

选择**编辑**工作流选项并转到**设计器**选项卡。这里，您可以看到 JSON 逻辑的直观表示形式。

![选定工作流的“设计器”选项卡](images/workflow-console-new-workflow-designer.PNG)

## <a name="get-workflow-details"></a>获取工作流详细信息

若要检索有关现有工作流的详细信息，请转到**[工作流-Get](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b44b3f9b0711b43c4c58)**  API 引用页，选择你所在区域的按钮 （在其中管理你的密钥的区域）。

![“工作流 - 获取”区域选择](images/test-drive-region.png)

输入 REST 调用参数，如在上面的部分。 请确保此时间**workflowname**是现有工作流的名称。

![“获取”查询参数和请求头](images/workflow-get-default.PNG)

选择“发送”。 如果操作成功，**响应状态**是`200 OK`，和**响应内容**框显示在工作流以 JSON 格式，如下所示：

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