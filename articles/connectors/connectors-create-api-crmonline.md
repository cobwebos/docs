---
title: 连接到 Dynamics 365 - Azure 逻辑应用 | Microsoft Docs
description: 使用 Dynamics 365（联机）REST API 和 Azure 逻辑应用创建和管理记录
author: Mattp123
ms.author: matp
ms.service: logic-apps
services: logic-apps
ms.reviewer: estfan, LADocs
ms.suite: integration
ms.topic: article
ms.date: 08/18/2018
tags: connectors
ms.openlocfilehash: 9fe41cf2946525948897635a4e30213d161431ef
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2019
ms.locfileid: "58295294"
---
# <a name="manage-dynamics-365-records-with-azure-logic-apps"></a>使用 Azure 逻辑应用管理 Dynamics 365 记录

使用 Azure 逻辑应用和 Dynamics 365 连接器，可以基于 Dynamics 365 中的记录创建自动化的任务和工作流。 工作流可在 Dynamics 365 帐户中创建记录、更新项、返回记录以及执行其他操作。 可在逻辑应用中包含操作，用于从 Dynamics 365 获取响应，并使输出可供其他操作使用。 例如，在 Dynamics 365 中更新项后，可以使用 Office 365 发送一封电子邮件。

本文介绍如何生成一个逻辑应用，每当在 Dynamics 365 中创建新的潜在顾客记录时，该逻辑应用就会在 Dynamics 365 中创建一个任务。
如果你不熟悉逻辑应用，请查看[什么是 Azure 逻辑应用？](../logic-apps/logic-apps-overview.md)。

## <a name="prerequisites"></a>必备组件

* Azure 订阅。 如果没有 Azure 订阅，请<a href="https://azure.microsoft.com/free/" target="_blank">注册一个免费 Azure 帐户</a>。 

* 一个 [Dynamics 365 帐户](https://dynamics.microsoft.com)

* 有关[如何创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知识

* 要在其中访问 Dynamics 365 帐户的逻辑应用。 若要通过 Dynamics 365 触发器启动逻辑应用，需要一个[空白逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。 

## <a name="add-dynamics-365-trigger"></a>添加 Dynamics 365 触发器

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

首先添加一个 Dynamics 365 触发器，当 Dynamics 365 中出现新的潜在顾客记录时，该触发器将会激发。

1. 在 [Azure 门户](https://portal.azure.com)上的逻辑应用设计器中打开空白逻辑应用（如果尚未打开）。

1. 在搜索框中，输入“Dynamics 365”作为筛选器。 对于本例，请在触发器列表中，选择以下触发器：**创建记录时**

   ![选择触发器](./media/connectors-create-api-crmonline/select-dynamics-365-trigger.png)

1. 如果系统提示登录到 Dynamics 365，请立即登录。

1. 提供以下触发器详细信息：

   | 属性 | 需要 | 描述 | 
   |----------|----------|-------------| 
   | **组织名称** | 是 | 要监视的组织 Dynamics 365 实例的名称，例如“Contoso” |
   | **实体名称** | 是 | 要监视的实体的名称，例如“Leads” | 
   | **频率** | 是 | 检查触发器相关的更新时，对时间间隔使用的时间单位 |
   | 间隔 | 是 | 下一次检查之前所要经过的秒数、分钟数、小时数、天数、周数或月数。 |
   ||| 

   ![触发器详细信息](./media/connectors-create-api-crmonline/trigger-details.png)

## <a name="add-dynamics-365-action"></a>添加 Dynamics 365 操作

现在添加 Dynamics 365 操作，用于创建新潜在顾客记录的任务记录。

1. 在触发器下，选择“新建步骤”。

1. 在搜索框中，输入“Dynamics 365”作为筛选器。 在操作列表中选择此操作：**新建记录**

   ![选择操作](./media/connectors-create-api-crmonline/select-action.png)

1. 提供以下操作详细信息：

   | 属性 | 需要 | 描述 | 
   |----------|----------|-------------| 
   | **组织名称** | 是 | 要在其中创建记录的 Dynamics 365 实例，不一定是触发器中的同一实例；在本示例中为“Contoso” |
   | **实体名称** | 是 | 要在其中创建记录的实体，例如“Tasks” | 
   | | |

   ![操作详细信息](./media/connectors-create-api-crmonline/action-details.png)

1. 操作中显示“主题”框后，请在“主题”框中单击，以显示动态内容列表。 在此列表中，选择要包含在与新潜在顾客记录关联的任务记录中的字段值：

   | 字段 | 描述 | 
   |-------|-------------| 
   | **姓氏** | 记录中的潜在顾客（主要联系人）的姓氏 |
   | **主题** | 记录中的潜在顾客的描述性名称 | 
   | | | 

   ![任务记录详细信息](./media/connectors-create-api-crmonline/create-record-details.png)

1. 在设计器工具栏上，选择逻辑应用旁边的“保存”。 

1. 若要手动启动逻辑应用，请在设计器工具栏上选择“运行”。

   ![运行逻辑应用](./media/connectors-create-api-crmonline/designer-toolbar-run.png)

1. 现在，请在 Dynamics 365 中创建潜在顾客记录，以便可以触发逻辑应用的工作流。

## <a name="add-filter-or-query"></a>添加筛选器或查询

若要指定如何在 Dynamics 365 操作中筛选数据，请在该操作中选择“显示高级选项”。 然后可以添加筛选器，或按查询排序。
例如，可以使用筛选器查询来只获取活动帐户，并按帐户名将这些记录排序。 对于此任务，请执行以下步骤：

1. 在“筛选器查询”下，输入以下 OData 筛选器查询：`statuscode eq 1`

2. 显示动态内容列表时，请在“排序依据”下选择“帐户名”。 

   ![指定筛选器和顺序](./media/connectors-create-api-crmonline/advanced-options.png)

有关详细信息，请参阅以下 Dynamics 365 客户参与 Web API 的系统查询选项： 

* [$filter](https://docs.microsoft.com/dynamics365/customer-engagement/developer/webapi/query-data-web-api#filter-results)
* [$orderby](https://docs.microsoft.com/dynamics365/customer-engagement/developer/webapi/query-data-web-api#order-results)

### <a name="best-practices-for-advanced-options"></a>高级选项的最佳做法

为操作或触发器中的字段指定值时，不管是手动输入值，还是从动态内容列表中选择值，该值的数据类型都必须与字段类型相匹配。

下表描述了一些字段类型及其值的所需数据类型。

| 字段类型 | 所需数据类型 | 描述 | 
|------------|--------------------|-------------|
| 文本字段 | 单个文本行 | 这些字段需要单行文本，或者文本类型的动态内容。 <p><p>*示例字段*:**描述**和**类别** | 
| 整数字段 | 整数 | 某些字段需要整数，或者整数类型的动态内容。 <p><p>*示例字段*:**完成百分比**和**持续时间** | 
| 数据字段 | 日期和时间 | 某些字段需要 mm/dd/yyyy 格式的日期，或者日期类型的动态内容。 <p><p>*示例字段*:**在创建**，**开始日期**，**实际开始**，**实际结束**，和**截止日期** | 
| 需要记录 ID 和查找类型的字段 | 主密钥 | 某些引用另一实体记录的字段需要记录 ID 和查找类型。 | 
||||

下面是 Dynamics 365 触发器和操作中的一些示例字段，它们在上述字段类型的基础上有所延伸，需要记录 ID 和查找类型。 此项要求意味着，从动态列表中选择的值不起作用。 

| 字段 | 描述 | 
|-------|-------------|
| **所有者** | 必须是有效的用户 ID 或团队记录 ID。 | 
| **所有者类型** | 必须是 **systemusers** 或 **teams**。 | 
| **相关** | 必须是有效的记录 ID，例如帐户 ID 或联系人记录 ID。 | 
| **相关类型** | 必须是查找类型，例如 **accounts** 或 **contacts**。 | 
| **客户** | 必须是有效的记录 ID，例如帐户 ID 或联系人记录 ID。 | 
| **客户类型** | 必须是查找类型，例如 **accounts** 或 **contacts**。 | 
|||

在本示例中，名为“创建新记录”的操作将创建新的任务记录： 

![创建具有记录 ID 和查找类型的任务记录](./media/connectors-create-api-crmonline/create-record-advanced.png)

此操作基于“所有者”字段中的记录 ID 以及“所有者类型”字段中的查找类型，将任务记录分配到特定的用户 ID 或团队记录 ID：

![所有者记录 ID 和查找类型](./media/connectors-create-api-crmonline/owner-record-id-and-lookup-type.png)

此操作还会添加与“相关”字段中的记录 ID 以及“相关类型”字段中的查找类型相关联的帐户记录： 

![相关记录 ID 和查找类型](./media/connectors-create-api-crmonline/regarding-record-id-lookup-type-account.png)

## <a name="find-record-id"></a>查找记录 ID

若要查找记录 ID，请执行以下步骤： 

1. 在 Dynamics 365 中打开一条记录，例如帐户记录。

2. 在操作工具栏上选择以下步骤之一：

   * 选择“弹出”。![弹出记录](./media/connectors-create-api-crmonline/popout-record.png) 
   * 选择“通过电子邮件发送链接”，以便将完整的 URL 复制到默认的电子邮件程序中。

   记录 ID 显示在 URL 中的 `%7b` 与 `%7d` 编码字符之间：

   ![查找记录 ID](./media/connectors-create-api-crmonline/find-record-ID.png)

## <a name="troubleshoot-failed-runs"></a>排查运行失败的问题

若要查找并检查逻辑应用中失败的步骤，可以查看逻辑应用的运行历史记录、状态、输入、输出等等。

1. 在 Azure 门户上的逻辑应用主菜单中，选择“概述”。 在显示了逻辑应用的所有运行状态的“运行历史记录”部分，选择一个失败的运行以查看详细信息。

   ![逻辑应用运行状态](./media/connectors-create-api-crmonline/run-history.png)

1. 展开失败的步骤，以便查看更多详细信息。 

   ![展开失败的步骤](./media/connectors-create-api-crmonline/expand-failed-step.png)

1. 查看步骤详细信息，例如输入和输出，以帮助找出失败的原因。

   ![失败的步骤 - 输入和输出](./media/connectors-create-api-crmonline/expand-failed-step-inputs-outputs.png)

若要深入了解如何解决逻辑应用的故障，请参阅[诊断逻辑应用的故障](../logic-apps/logic-apps-diagnosing-failures.md)。

## <a name="connector-reference"></a>连接器参考

如需技术详细信息（例如触发器、操作和限制，如连接器的 Swagger 文件所述），请查看[连接器的参考页](/connectors/dynamicscrmonline/)。 

## <a name="get-support"></a>获取支持

* 有关问题，请访问 [Azure 逻辑应用论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)。
* 若要提交功能建议或对功能建议进行投票，请访问[逻辑应用用户反馈网站](https://aka.ms/logicapps-wish)。

## <a name="next-steps"></a>后续步骤

* 了解其他[逻辑应用连接器](../connectors/apis-list.md)
