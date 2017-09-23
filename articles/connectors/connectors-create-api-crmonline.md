---
title: "从 Azure 逻辑应用连接到 Dynamics 365（联机）| Microsoft Docs"
description: "创建逻辑应用工作流，用于通过 Dynamics 365 连接器提供的 API 管理 Dynamics 365（联机）"
services: logic-apps
cloud: Azure Stack
author: Mattp123
manager: anneta
documentationcenter: 
tags: connectors
ms.assetid: 0dc2abef-7d2c-4a2d-87ca-fad21367d135
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2017
ms.author: matp; LADocs
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: d35647921ff540167a3a591fb489d3bab031a5c1
ms.contentlocale: zh-cn
ms.lasthandoff: 05/26/2017

---

# <a name="connect-to-dynamics-365-from-logic-app-workflows"></a>从逻辑应用工作流连接到 Dynamics 365

通过逻辑应用，可以连接到 Dynamics 365（联机），并可以创建有用的业务流，用于创建记录、更新项或返回记录列表。 借助 Dynamics 365 连接器，可以：

* 根据从 Dynamics 365（联机）中获取的数据生成业务流。
* 使用可获得响应的操作，然后使输出可用于其他操作。 例如，在 Dynamics 365（联机）中更新项后，可以使用 Office 365 发送一封电子邮件。

本主题介绍如何创建逻辑应用，每当在 Dynamics 365 中创建一个新潜在客户时，逻辑应用会在 Dynamics 365 中创建任务。

## <a name="prerequisites"></a>先决条件
* 一个 Azure 帐户。
* 一个 Dynamics 365（联机）帐户。

## <a name="create-a-task-when-a-new-lead-is-created-in-dynamics-365"></a>在 Dynamics 365 中创建一个新潜在客户时创建任务

1.  [登录 Azure](https://portal.azure.com)。

2.  在 Azure 搜索框中，键入“`Logic apps`”，然后按 Enter。

      ![查找逻辑应用](./media/connectors-create-api-crmonline/find-logic-apps.png)

3.  在“逻辑应用”下，单击“添加”。

      ![添加逻辑应用](./media/connectors-create-api-crmonline/add-logic-app.png)

4.  若要创建逻辑应用，完成“名称”、“订阅”、“资源组”和“位置”字段，然后单击“创建”。

5.  选择新的逻辑应用。 收到“部署成功”通知后，请单击“刷新”。

6.  在“开发工具”下，单击“逻辑应用设计器”。 在模板列表中，单击“空白逻辑应用”。

7.  在搜索框中，键入“`Dynamics 365`”。 从 Dynamics 365 触发器列表中选择“Dynamics 365 – 创建记录时”。

8.  如果系统提示登录 Dynamics 365，请立即登录。

9.  在触发器详细信息中，输入以下信息：

  * **组织名称**。 选择要让逻辑应用侦听的 Dynamics 365 实例。

  * **实体名称**。 选择要侦听的实体。 此事件充当触发器，用于启动逻辑应用。 
  本演练中选择“潜在客户”。

  * **你想多久检查一次项？** 这些值设置逻辑应用检查触发器相关更新的频率。 默认设置为每 3 分钟检查一次更新。

    * **频率**。 选择秒、分钟、小时或天。

    * **时间间隔**。 输入在下次检查前的秒数、分钟数、小时数或天数。

      ![逻辑应用触发器详细信息](./media/connectors-create-api-crmonline/trigger-details.png)

10. 单击“新建步骤”，然后单击“添加操作”。

11. 在搜索框中，键入“`Dynamics 365`”。 从操作列表中选择“Dynamics 365 – 创建新记录”。

12. 输入以下信息：

    * **组织名称**。 选择要让流在其中创建记录的 Dynamics 365 实例。 
    请注意，此实例可与从中触发事件的实例不同。

    * **实体名称**。 选择在触发事件时要创建记录的实体。 
    本演练将选择“任务”。

13. 单击显示的“使用者”框。 从显示的动态内容列表中选择以下任一字段：

    * **姓氏**。 创建任务记录后，选择此字段会将潜在客户的姓氏插入到任务的“使用者”字段中。
    * **主题**。 创建任务记录后，选择此字段会将潜在客户的“主题”字段插入到任务的“使用者”字段中。 
    单击“主题”以将其添加到“使用者”框。

      ![逻辑应用创建新记录详细信息](./media/connectors-create-api-crmonline/create-record-details.png)

14. 在“逻辑应用设计器”工具栏上，单击“保存”。

    ![“逻辑应用设计器”工具栏的“保存”按钮](./media/connectors-create-api-crmonline/designer-toolbar-save.png)

15. 若要启动逻辑应用，请单击“运行”。

    ![“逻辑应用设计器”工具栏的“保存”按钮](./media/connectors-create-api-crmonline/designer-toolbar-run.png)

16. 立即在 Dynamics 365 for Sales 中创建潜在客户记录并查看正在运行的流！

## <a name="set-advanced-options-for-a-logic-app-step"></a>设置逻辑应用步骤的高级选项

若要指定如何在逻辑应用步骤中筛选数据，单击该步骤中的“显示高级选项”，然后添加筛选查询或顺序查询。

例如，可以使用筛选查询只获取活动帐户并按帐户名称排序。 若要执行此任务，请输入 OData 筛选查询 `statuscode eq 1`，然后从动态内容列表选择“帐户名称”。 详细信息：[MSDN：$filter](https://msdn.microsoft.com/library/gg309461.aspx#Anchor_1) 和 [$orderby](https://msdn.microsoft.com/library/gg309461.aspx#Anchor_2)。

![逻辑应用高级选项](./media/connectors-create-api-crmonline/advanced-options.png)

### <a name="best-practices-when-using-advanced-options"></a>使用高级选项的最佳做法

向字段添加值时，无论是键入一个值还是从动态内容列表中选择一个值，该值都必须与字段类型匹配。

字段类型  |如何使用  |查找位置  |Name  |数据类型  
---------|---------|---------|---------|---------
文本字段|文本字段需要单个文本行或类型为文本字段的动态内容。 示例包括“类别”和“子类别”字段。|“设置”>“自定义”>“自定义系统”>“实体”>“任务”>“字段” |category |单个文本行        
整数字段 | 有些字段需要整数或类型为整数字段的动态内容。 示例包括“完成百分比”和“持续时间”。 |“设置”>“自定义”>“自定义系统”>“实体”>“任务”>“字段” |完成百分比 |整数         
数据字段 | 有些字段需要输入格式为 mm/dd/yyyy 的数据或类型为数据字段的动态内容。 示例包括“创建时间”、“开始日期”、“实际开始时间”、“上一暂候时间”、“实际结束时间”和“截止日期”。 | “设置”>“自定义”>“自定义系统”>“实体”>“任务”>“字段” |创建时间 |日期和时间
需要记录 ID 和查找类型的字段 |一些引用另一实体记录的字段需要记录 ID 和查找类型。 |“设置”>“自定义”>“自定义系统”>“实体”>“帐户”>“字段”  | 帐户 ID  | 主密钥

### <a name="more-examples-of-fields-that-require-both-a-record-id-and-lookup-type"></a>需要记录 ID 和查找类型的字段的更多示例
以下是未使用从动态内容列表选择的值的字段的更多示例，进一步介绍了上表的内容。 相反，这些字段需要在 PowerApps 的字段中输入的记录 ID 和查找类型。  
* 所有者和所有者类型。 “所有者”字段必须是有效的用户或团队记录 ID。 “所有者类型”必须是“系统用户”或“团队”。
* 客户和客户类型。 “客户”字段必须是有效的帐户或联系人记录 ID。 “客户类型”必须是“帐户”或“联系人”。
* 相关和相关类型。 “相关”字段必须是有效的记录 ID，如帐户或联系人记录 ID。 “相关类型”必须是记录的查找类型，如“帐户”或“联系人”。

下面的任务创建操作示例添加了一个帐户记录，该记录与将其添加到任务相关字段的记录 ID 相对应。

![流记录 ID 和类型帐户](./media/connectors-create-api-crmonline/recordid-type-account.png)

此示例还根据用户的记录 ID 将任务分配给指定用户。

![流记录 ID 和类型帐户](./media/connectors-create-api-crmonline/recordid-type-user.png)

若要查找记录 ID，请参阅以下部分：查找记录 ID

## <a name="find-the-record-id"></a>查找记录 ID

1. 打开一个记录，如帐户记录。

2. 在操作工具栏上，单击“弹出”![弹出记录](./media/connectors-create-api-crmonline/popout-record.png)。
或者，在操作工具栏上，单击“通过电子邮件发送链接”，将完整 URL 复制到你的默认电子邮件程序。

   记录 ID 将显示在 URL 的 %7b 和 %7d 编码字符之间。

   ![流记录 ID 和类型帐户](./media/connectors-create-api-crmonline/recordid.png)

## <a name="troubleshooting"></a>故障排除
若要解决逻辑应用中的失败步骤，请查看事件状态的详细信息。

1. 在“逻辑应用”下，选择你的逻辑应用，然后单击“概述”。 

   显示“摘要”区域，该区域会提供逻辑应用的运行状态。 

   ![逻辑应用运行状态](./media/connectors-create-api-crmonline/tshoot1.png)

2. 若要查看任何有关失败的运行的详细信息，请单击失败事件。 若要展开失败的步骤，请单击该步骤。

   ![展开失败的步骤](./media/connectors-create-api-crmonline/tshoot2.png)

   显示步骤的详细信息，这些信息有助于找到失败的原因。

   ![失败步骤的详细信息](./media/connectors-create-api-crmonline/tshoot3.png)

若要深入了解如何解决逻辑应用的故障，请参阅[诊断逻辑应用的故障](../logic-apps/logic-apps-diagnosing-failures.md)。

## <a name="connector-specific-details"></a>特定于连接器的详细信息

在[连接器详细信息](/connectors/crm/)中查看在 Swagger 中定义的触发器和操作，并查看限制。 

## <a name="next-steps"></a>后续步骤
在我们的 [API 列表](apis-list.md)中了解逻辑应用中的其他可用连接器。

