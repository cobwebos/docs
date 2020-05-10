---
title: 连接到 Common Data Service
description: 使用 Azure 逻辑应用创建和管理 Common Data Service 记录
services: logic-apps
ms.suite: integration
ms.reviewer: jdaly, logicappspm
ms.topic: conceptual
ms.date: 05/08/2020
tags: connectors
ms.openlocfilehash: 98da7e959e4b59ad2d0f3f3f79364391b4ceddbd
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/09/2020
ms.locfileid: "82997095"
---
# <a name="create-and-manage-records-in-common-data-service-by-using-azure-logic-apps"></a>使用 Azure 逻辑应用创建和管理 Common Data Service 中的记录

通过[Azure 逻辑应用](../logic-apps/logic-apps-overview.md)和[Common Data Service 连接器](https://docs.microsoft.com/connectors/commondataservice/)，你可以构建管理[Common Data Service](https://docs.microsoft.com/powerapps/maker/common-data-service/data-platform-intro)数据库中的记录的自动化工作流。 这些工作流可以创建记录、更新记录和执行其他操作。 你还可以从 Common Data Service 数据库中获取信息，并使输出可用于在逻辑应用中使用的其他操作。 例如，在 Common Data Service 数据库中更新记录时，可以使用 Office 365 Outlook connector 发送电子邮件。

本文介绍如何构建逻辑应用，以便在每次创建新的潜在客户记录时创建任务记录。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有 Azure 订阅，请[注册一个免费 Azure 帐户](https://azure.microsoft.com/free/)。

* [Common Data Service 环境](https://docs.microsoft.com/power-platform/admin/environments-overview)，它是组织存储、管理和共享业务数据和 Common Data Service 数据库的空间。 有关详细信息，请参阅以下资源：<p>

  * [了解： Common Data Service 入门](https://docs.microsoft.com/learn/modules/get-started-with-powerapps-common-data-service/)
  * [Power Platform-环境概述](https://docs.microsoft.com/power-platform/admin/environments-overview)

* 有关[如何创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)和逻辑应用的基本知识，你需要从该位置访问 Common Data Service 数据库中的记录。 若要使用 Common Data Service 触发器启动逻辑应用，需要使用空白逻辑应用。 如果不熟悉 Azure 逻辑应用，请查看[快速入门：使用 Azure 逻辑应用创建第一个工作流](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

## <a name="add-common-data-service-trigger"></a>添加 Common Data Service 触发器

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

在此示例中，添加在创建新记录时激发的 Common Data Service 触发器。

1. 在 [Azure 门户](https://portal.azure.com)上的逻辑应用设计器中打开空白逻辑应用（如果尚未打开）。

1. 在搜索框中输入 `common data service`。 对于本示例，请在触发器列表中选择以下触发器：“创建记录时”****

   ![选择 "创建记录时" 触发器](./media/connect-common-data-service/select-when-record-created-trigger.png)

1. 如果系统提示，请登录到 Common Data Service。

1. 在触发器中，提供有关要监视新 "潜在顾客" 记录的环境的信息，例如：

   ![用于监视环境的触发器信息](./media/connect-common-data-service/when-record-created-trigger-details.png)

   | 属性 | 必须 | 说明 |
   |----------|----------|-------------|
   | **环境** | 是 | 要监视的环境，例如 "Fabrikam 销售生产"。 有关详细信息，请参阅[Power Platform-环境概述](https://docs.microsoft.com/power-platform/admin/environments-overview)。 |
   | **实体名称** | 是 | 要监视的实体，例如 "Lead" |
   | **Scope** | 是 | 创建新记录的源，例如，业务部门中的用户或组织中的任何用户。 此示例使用 "Business unit"。 |
   ||||

## <a name="add-common-data-service-action"></a>添加 Common Data Service 操作

现在添加一个 Common Data Service 操作，该操作可为新的 "潜在顾客" 记录创建任务记录。

1. 在 "**创建记录时**" 触发器下，选择 "**新建步骤**"。

1. 在搜索框中输入 `common data service`。 在操作列表中选择以下操作：“创建新记录”****

   ![选择 "创建新记录" 操作](./media/connect-common-data-service/select-create-new-record-action.png)

1. 在操作中，提供有关要在其中创建新任务记录的环境的信息。 如果可用，还会根据你为此操作选择的实体显示其他属性，例如：

   ![要在其中创建记录的环境的操作信息](./media/connect-common-data-service/create-new-record-action-details.png)

   | 属性 | 必须 | 说明 |
   |----------|----------|-------------|
   | **组织名称** | 是 | 要在其中创建记录的环境，该记录在触发器中不必是相同的环境，而是在此示例中为 "Fabrikam 销售生产" |
   | **实体名称** | 是 | 要在其中创建记录的实体，例如“Tasks” |
   | **主题** | 是，基于在此示例中选择的实体 | 有关此任务的目标的简短说明 |
   ||||

   1. 对于 "**主题**" 属性，请输入以下文本，其中包含尾随空格：

      `Follow up with new lead:`

   1. 将指针保留在 "**主题**" 框中，使动态内容列表保持可见。
   
   1. 在列表中，在 "**创建记录**" 部分中，选择要包括在任务记录中的触发器输出，例如：

      ![选择要在任务记录中使用的触发器输出](./media/connect-common-data-service/create-new-record-action-select-trigger-outputs.png)

      | 触发器输出 | 说明 |
      |----------------|-------------|
      | **名字** | 要用作任务记录中主要联系人的潜在顾客记录的名字 |
      | **姓氏** | 要用作任务记录中主要联系人的潜在客户记录的姓氏 |
      | **说明** | 要包括在任务记录中的其他输出，如电子邮件地址和办公电话号码 |
      |||

   完成操作后，该操作可能类似于以下示例：

   ![已完成 "创建新记录" 操作](./media/connect-common-data-service/finished-create-record-action-details.png)

1. 保存逻辑应用。 在设计器工具栏上，选择“保存”****。

1. 若要手动启动逻辑应用，请在设计器工具栏上选择 "**运行**"。 若要测试逻辑应用，请创建新的 "潜在顾客" 记录。

## <a name="trigger-only-on-updated-attributes"></a>仅在更新的属性上触发

对于在记录更新时运行的触发器（例如，**当记录更新时**），可以使用筛选器属性，以便仅在更新指定属性时才运行逻辑应用。 此功能可帮助防止不必要的逻辑应用运行。

1. 在触发器的 "**添加新参数**" 列表中，选择 "**属性筛选器**"。

   ![添加 "属性筛选器" 属性](./media/connect-common-data-service/when-record-updated-trigger-add-attribute-filters.png)

1. 对于 "每个**属性筛选器项**"，请选择要监视其更新的属性，例如：

   ![添加 "属性筛选器" 属性](./media/connect-common-data-service/when-record-updated-trigger-select-attribute-filter.png)

## <a name="list-records-based-on-a-filter"></a>基于筛选器列出记录

对于返回记录的操作（如**List 记录**操作），可以使用基于指定筛选器返回记录的 ODATA 查询。 例如，您只具有活动帐户的记录。

1. 在操作中，打开 "**添加新参数**" 列表，然后选择 "**筛选查询**" 属性。

   ![添加 "筛选查询" 属性](./media/connect-common-data-service/list-records-action-filter-query.png)

1. 在现在显示在 "操作" 中的 "**筛选器查询**" 属性中，输入以下 ODATA 筛选器查询：`statuscode eq 1`

   ![输入用于筛选记录的 ODATA 筛选器查询](./media/connect-common-data-service/list-records-action-filter-query-value.png)

有关`$filter`系统查询选项的详细信息，请参阅[Common Data Service-Filter results](https://docs.microsoft.com/powerapps/developer/common-data-service/webapi/query-data-web-api#filter-results)。

## <a name="list-records-based-on-an-order"></a>基于订单列出记录

对于返回记录的操作（如**List 记录**操作），可以使用按指定顺序返回记录的 ODATA 查询，该查询根据操作返回的记录而变化。 例如，你可以让操作根据帐户名称列出记录。

1. 在操作中，打开 "**添加新参数**" 列表，然后选择 "**排序依据**" 属性。

   ![添加 "Order by" 属性](./media/connect-common-data-service/list-records-action-order-by.png)

1. 在现在出现在操作中的**Order By**属性中，输入以下 ODATA 筛选器查询：`name`

   ![输入用于对记录进行排序的 ODATA 筛选器查询](./media/connect-common-data-service/list-records-action-order-by-value.png)

有关`$orderby`系统查询选项的详细信息，请参阅[Common Data Service 顺序结果](https://docs.microsoft.com/powerapps/developer/common-data-service/webapi/query-data-web-api#order-results)。

## <a name="field-data-types"></a>字段数据类型

不管是手动输入值还是从触发器或操作中的字段的动态内容列表中选择一个值，值的数据类型都必须与字段的所需数据类型匹配。

下表介绍了某些字段类型以及这些字段的值所需的数据类型。

| 字段 | 数据类型 | 说明 |
|-------|-----------|-------------|
| 文本字段 | 单个文本行 | 需要具有文本数据类型的一行文本或动态内容，例如，以下属性： <p><p>- **2008** <br>- **类别** |
| 整数字段 | 整数 | 需要具有整数数据类型的整数或动态内容，例如，以下属性： <p><p>- **完成百分比** <br>- **持续时间** |
| 日期字段 | 日期和时间 | 需要采用 MM/DD/YYY 格式的日期或具有日期数据类型的动态内容，例如，以下属性： <p><p>- **创建于** <br>- **开始日期** <br>- **实际开始时间** <br>- **实际结束** <br>- **截止日期** |
| 引用其他实体记录的字段 | 主密钥 | 要求使用记录 ID （如 GUID）和查找类型，这意味着动态内容列表中的值将不起作用，例如，以下属性： <p><p>- **所有者**：必须是有效的用户 id 或团队记录 ID。 <br>- **所有者类型**：必须分别是查找类型，如`systemusers`或`teams`。 <p><p>- **相关**：必须是有效的记录 id，如帐户 id 或联系人记录 id。 <br>- **相关类型**：必须分别是查找类型，如`accounts`或`contacts`。 <p><p>- **Customer**：必须是有效的记录 id，如帐户 id 或联系人记录 id。 <br>- **Customer type**：必须分别是查找类型，如`accounts`或。 `contacts` |
||||

此示例演示了 "**创建新记录**" 操作如何创建与其他实体记录关联的新 "任务" 记录，特别是用户记录和帐户记录。 操作通过使用与相关属性的预期数据类型匹配的值来指定这些实体记录的 Id 和查找类型。

* "所有者" 属性（指定 "用户 ID"）和 "**所有者类型**" 属性（指定`systemusers`查找类型）基于 "**所有者**" 属性，该操作将新的 "任务" 记录与特定用户相关联。

* 基于**相关**属性（指定记录 ID）和 "**相关类型**" 属性（指定`accounts`查找类型），操作将新 "任务" 记录与特定帐户关联。

![创建与 Id 和查找类型关联的 "任务" 记录](./media/connect-common-data-service/create-new-record-task-properties.png)

## <a name="connector-reference"></a>连接器参考

有关基于连接器的 Swagger 说明的技术信息，如触发器、操作、限制和其他详细信息，请参阅[连接器的参考页](https://docs.microsoft.com/connectors/commondataservice/)。

## <a name="next-steps"></a>后续步骤

* 了解其他[适用于 Azure 逻辑应用的连接器](../connectors/apis-list.md)