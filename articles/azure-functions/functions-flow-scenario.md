---
title: 从 Microsoft Flow 调用 Azure 函数 | Microsoft Docs
description: 创建一个自定义连接器，然后使用此连接器调用函数。
services: functions
keywords: 云应用, 云服务, Microsoft Flow, 商业流程, 商业应用程序
documentationcenter: ''
author: ggailey777
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/14/2017
ms.author: glenga
ms.reviewer: sunayv
ms.custom: ''
ms.openlocfilehash: 57d80ad836a16b8821ba0cce42c822728c654dfd
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2018
ms.locfileid: "38467738"
---
# <a name="call-a-function-from-microsoft-flow"></a>从 Microsoft Flow 调用函数

通过 [Microsoft Flow](https://flow.microsoft.com/)，可在喜爱的应用和服务之间轻松自动执行工作流和业务流程。 专业开发人员可以使用 Azure Functions 扩展 Microsoft Flow 的功能，同时使流构建者摆脱技术细节。

本主题基于风力涡轮机的维护方案生成流。 介绍如何调用在[为函数创建 OpenAPI 定义](functions-openapi-definition.md)中定义的函数。 此函数确定风力涡轮机上的紧急修复是否经济高效。 如果经济高效，该流会发送一封电子邮件，建议进行修复。

有关从 PowerApps 调用同一函数的信息，请参阅[从 PowerApps 调用函数](functions-powerapps-scenario.md)。

本主题介绍如何执行下列操作：

> [!div class="checklist"]
> * 在 SharePoint 中创建列表。
> * 导出 API 定义。
> * 添加到 API 的连接。
> * 如果修复经济高效，将创建用于发送电子邮件的流。
> * 运行该流。

## <a name="prerequisites"></a>先决条件

+ 一个有效的 [Microsoft Flow 帐户](https://flow.microsoft.com/documentation/sign-up-sign-in/)，其中登录凭据与 Azure 帐户相同。 
+ SharePoint，用作此流的数据源。 如果还没有 SharePoint，请注册 [Office 365 免费试用版](https://signup.microsoft.com/Signup?OfferId=467eab54-127b-42d3-b046-3844b860bebf&dl=O365_BUSINESS_PREMIUM&ali=1)。
+ 完成[为函数创建 OpenAPI 定义](functions-openapi-definition.md)教程。

## <a name="create-a-sharepoint-list"></a>创建 SharePoint 列表
首先创建用作流的数据源的列表。 该列表具有以下列。

| 列表列     | 数据类型           | 说明                                    |
|-----------------|---------------------|------------------------------------------|
| **标题**           | 单个文本行 | 涡轮机的名称                      |
| **LastServiceDate** | 日期                |                                          |
| **MaxOutput**       | Number              | 涡轮机的输出，以 KwH 为单位            |
| **ServiceRequired** | Yes/No              |                                          |
| **EstimatedEffort** | Number              | 预计的修复时间，以小时为单位 |

1. 在 SharePoint 站点中，依次单击或点击“新建”、“列表”。

    ![新建 SharePoint 列表](./media/functions-flow-scenario/new-list.png)

2. 输入名称 `Turbines`，然后单击或点击“创建”。

    ![指定新列表的名称](./media/functions-flow-scenario/create-list.png)

    将创建带有默认“标题”字段的“涡轮机”列表。

    ![涡轮机列表](./media/functions-flow-scenario/initial-list.png)

3. 依次单击或点击![“新建项”图标](./media/functions-flow-scenario/icon-new.png)、“日期”。

    ![添加一行文本字段](./media/functions-flow-scenario/add-column.png)

4. 输入名称 `LastServiceDate`，然后单击或点击“创建”。

    ![创建 LastServiceDate 列](./media/functions-flow-scenario/date-column.png)

5. 针对该列表中的其他三列，重复执行最后两个步骤：

    1. **Number** >“MaxOutput”

    2. **Yes/No** >“ServiceRequired”

    3. **Number** >“EstimatedEffort”

就这么简单，现在应该会有一个空的列表，如下图所示。 创建流后，将数据添加到该列表。

![空列表](media/functions-flow-scenario/empty-list.png)

[!INCLUDE [Export an API definition](../../includes/functions-export-api-definition.md)]

## <a name="add-a-connection-to-the-api"></a>添加到 API 的连接
自定义 API（也称为自定义连接器）可用于 Microsoft Flow，但必须先创建到 API 的连接，然后才能用于流。

1. 在 [flow.microsoft.com](https://flow.microsoft.com) 中，单击齿轮图标（位于右上角），然后单击“连接”。

    ![流连接](media/functions-flow-scenario/flow-connections.png)

1. 单击“创建连接”，向下滚动到“涡轮机修复”连接器并单击它。

    ![创建连接](media/functions-flow-scenario/create-connection.png)

1. 输入 API 密钥，然后单击“创建连接”。

    ![输入 API 密钥并创建](media/functions-flow-scenario/api-key.png)

> [!NOTE]
> 如果与他人共享流，在流上操作或使用流的所有用户也必须输入 API 密钥才能连接到 API。 此行为将来可能会更改，到时我们将更新此主题以反映更改。


## <a name="create-a-flow"></a>创建流

现在可以创建使用自定义的连接器和已创建的 SharePoint 列表的流。

### <a name="add-a-trigger-and-specify-a-condition"></a>添加触发器并指定条件

首先从头开始创建流（不带模板），然后添加一个在 SharePoint 列表中创建项时会触发的*触发器*。 随后添加一个*条件*，用于确定接下来进行的操作。

1. 在 [flow.microsoft.com](https://flow.microsoft.com) 中，依次单击“我的流”、“从空白创建”。

    ![从空白创建](media/functions-flow-scenario/create-from-blank.png)

2. 单击 SharePoint 触发器“在创建项时”。

    ![选择触发器](media/functions-flow-scenario/choose-trigger.png)

    如果尚未登录到 SharePoint，系统会提示登录。

3. 对于“站点地址”，输入 SharePoint 站点名称，而对于“列表名称”，则输入包含涡轮机数据的列表。

    ![选择触发器](media/functions-flow-scenario/site-list.png)

4. 单击“新步骤”，然后单击“添加条件”。

    ![添加条件](media/functions-flow-scenario/add-condition.png)

    Microsoft Flow 会向流添加两个分支：“如果是”和“如果否”。 在定义要匹配的条件后，将步骤添加到其中一个或全部两个分支。

    ![条件分支](media/functions-flow-scenario/condition-branches.png)

5. 在“条件”卡上，单击第一个框，然后从“动态内容”对话框中选择“ServiceRequired”。

    ![选择条件字段](media/functions-flow-scenario/condition1-field.png)

6. 为条件输入值 `True`。

    ![为条件输入 true](media/functions-flow-scenario/condition1-yes.png)

    该值在 SharePoint 列表中显示为 `Yes` 或 `No`，但它存储为*布尔值*，即 `True` 或 `False`。 

7. 单击页面顶部的“创建流”。 请确保定期单击“更新流”。

对于在列表中创建的任何项，流将检查 **ServiceRequired** 字段是否设置为 `Yes`，然后相应地转到“如果是”分支或“如果否”分支。 为了节省时间，在本主题中仅指定针对“如果是”分支的操作。

### <a name="add-the-custom-connector"></a>添加自定义连接器

现在，在 Azure 中添加了调用函数的自定义连接器。 将自定义连接器添加到流就像添加标准连接器一样。 

1. 在“如果是”分支中，请单击“添加操作”。

    ![添加操作](media/functions-flow-scenario/condition1-yes-add-action.png)

2. 在“选择操作”对话框中，搜索 `Turbine Repair`，然后选择操作“涡轮机修复 - 计算成本”。

    ![选择操作](media/functions-flow-scenario/choose-turbine-repair.png)

    下图显示添加到流的卡。 字段和说明来自连接器的 OpenAPI 定义。

    ![计算成本默认值](media/functions-flow-scenario/calculates-costs-default.png)

3. 在“计算成本”卡上，使用“动态内容”对话框以选择该函数的输入。 Microsoft Flow 显示数值字段，但不是日期字段，因为 OpenAPI 定义指定“小时”和“容量”为数值。

    对于“小时”，选择“EstimatedEffort”，对于“容量”，选择“MaxOutput”。

    ![选择操作](media/functions-flow-scenario/calculates-costs-fields.png)

     现在，基于函数的输出添加另一个条件。

4. 在“如果是”分支的底部，请单击“更多”，然后单击“添加条件”。

    ![添加条件](media/functions-flow-scenario/condition2-add.png)

5. 在“条件 2”卡上，单击第一个框，然后从“动态内容”对话框中选择“消息”。

    ![选择条件字段](media/functions-flow-scenario/condition2-field.png)

6. 输入值 `Yes`。 根据由函数返回的消息为“是”（修复）或“否”（不修复），流将转到下一个“如果是”分支或“如果否”分支。 

    ![为条件输入“是”](media/functions-flow-scenario/condition2-yes.png)

现在，该流应如下图所示。

![为条件输入“是”](media/functions-flow-scenario/flow-checkpoint1.png)

### <a name="send-email-based-on-function-results"></a>基于函数结果发送电子邮件

此时在流中，该函数已返回“消息”值 `Yes` 或 `No`，以及关于成本和潜在收入的其他信息。 在第二个条件的“如果是”分支中，将发送一封电子邮件，但可以执行任意数量的操作，如写回到 SharePoint 列表或启动[审批过程](https://flow.microsoft.com/documentation/modern-approvals/)。

1. 在第二个条件的“如果是”分支，单击“添加操作”。

    ![添加操作](media/functions-flow-scenario/condition2-yes-add-action.png)

2. 在“选择操作”对话框中，搜索 `email`，然后基于使用的电子邮件系统（在本例中是 Outlook）选择发送电子邮件操作。

    ![Outlook 发送电子邮件](media/functions-flow-scenario/outlook-send-email.png)

3. 在“发送电子邮件”卡上，编写一封电子邮件。 在“收件人”字段中输入组织中的有效名称。 在下图中，可以看到其他字段都是来自“动态内容”对话框的文本和令牌的组合。 

    ![电子邮件字段](media/functions-flow-scenario/email-fields.png)

    **标题**令牌来自 SharePoint 列表，**CostToFix** 和 **RevenueOpportunity** 由函数返回。

    已完成的流应如下图所示（我们忽略了第一个“如果否”分支以节省空间）。

    ![完成流](media/functions-flow-scenario/complete-flow.png)

4. 单击页面顶部的“更新流”，然后单击“完成”。

## <a name="run-the-flow"></a>运行该流

现在，该流完成后，将行添加到 SharePoint 列表，并查看流如何响应。

1. 返回到 SharePoint 列表，并单击“快速编辑”。

    ![快速编辑](media/functions-flow-scenario/quick-edit.png)

2. 在编辑网格中输入以下值。

    | 列表列     | 值           |
    |-----------------|---------------------|
    | **标题**           | 涡轮机 60 |
    | **LastServiceDate** | 08/04/2017 |
    | **MaxOutput**       | 2500 |
    | **ServiceRequired** | 是 |
    | **EstimatedEffort** | 10 |

3. 单击“Done”（完成） 。

    ![完成快速编辑](media/functions-flow-scenario/quick-edit-done.png)

    在添加项时，它将触发流，这将在下一步中查看。

4. 在 [flow.microsoft.com](https://flow.microsoft.com) 上，单击“我的流”，然后单击创建的流。

    ![我的流](media/functions-flow-scenario/my-flows.png)

5. 在“运行历史记录”下，单击流运行。

    ![运行历史记录](media/functions-flow-scenario/run-history.png)

    如果成功运行，可以在下一页上查看流操作。 如果出于任何原因而运行失败，下一页将提供疑难解答信息。

6. 展开卡以查看流过程中发生的事件。 例如，展开“计算成本”卡可查看输入到函数的内容及从函数输出的内容。 

    ![计算成本输入和输出](media/functions-flow-scenario/calculates-costs-outputs.png)

7. 查看电子邮件帐户，找出在“发送电子邮件”卡中指定的“收件人”字段。 从流发送的电子邮件应类似下图。

    ![流电子邮件](media/functions-flow-scenario/flow-email.png)

    你可以看到令牌如何替换为来自 SharePoint 列表和函数的正确值。

## <a name="next-steps"></a>后续步骤
本主题介绍了如何执行以下操作：

> [!div class="checklist"]
> * 在 SharePoint 中创建列表。
> * 导出 API 定义。
> * 添加到 API 的连接。
> * 如果修复经济高效，将创建用于发送电子邮件的流。
> * 运行该流。

若要了解有关 Microsoft Flow 的详细信息，请参阅[开始使用 Microsoft Flow](https://flow.microsoft.com/documentation/getting-started/)。

若要了解其他有关使用 Azure Functions 的有趣方案，请参阅[从 PowerApps 调用函数](functions-powerapps-scenario.md)和[创建与 Azure 逻辑应用集成的函数](functions-twitter-email.md)。