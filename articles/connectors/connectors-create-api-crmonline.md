---
title: "将 Dynamics 365（联机）连接器添加到 Azure 逻辑应用 | Microsoft Docs"
description: "使用 Azure App Service 创建逻辑应用。 Dynamics 365（联机）连接提供程序提供了一个 API，用于处理 Dynamics 365（联机）上的实体。"
services: logic-apps
cloud: Azure Stack
documentationcenter: 
author: Mattp123
manager: anneta
ms.assetid: 0dc2abef-7d2c-4a2d-87ca-fad21367d135
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2017
ms.author: matp
translationtype: Human Translation
ms.sourcegitcommit: fa426f474f4efd4023da5dfd9954dacf96f635ab
ms.openlocfilehash: 99d5379ad1e6965dd9ed88de456cc850d7e40d5a


---
# <a name="create-a-logic-app-with-the-dynamics-365-connector"></a>创建使用 Dynamics 365 连接器的逻辑应用

通过逻辑应用，可以连接到 Dynamics 365（联机），并可以创建有用的业务流，用于创建新记录、更新项或返回记录列表。 借助 Dynamics 365 连接器，可以：

* 根据从 Dynamics 365（联机）中获取的数据生成业务流。
* 使用可获得响应的操作，然后使输出可用于其他操作。 例如，在 Dynamics 365（联机）中更新项后，可以使用 Office 365 发送一封电子邮件。

本主题介绍如何创建逻辑应用，每当在 Dynamics 365 中创建一个新潜在客户时，逻辑应用会在 Dynamics 365 中创建任务。

## <a name="prerequisites"></a>先决条件
* 一个 Azure 帐户。
* 一个 Dynamics 365（联机）帐户。

## <a name="walkthrough-create-a-task-whenever-a-new-lead-is-created-in-dynamics-365"></a>演练：在 Dynamics 365 中创建一个新潜在客户时创建任务
1.    [登录 Azure](https://portal.azure.com)。
2.    在“搜索”框中键入“逻辑应用”，然后按 Enter。
3.    在逻辑应用服务区域中，单击“添加”。

  ![添加逻辑应用](./media/connectors-create-api-crmonline/add-logic-app.png)

4.    完成“名称”、“订阅”、“资源组”和“位置”字段以创建逻辑应用对象，然后单击“创建”。

5.    选择新的逻辑应用。 收到“部署成功”通知后，请单击“刷新”。

6.    在“开发工具”下，单击“逻辑应用设计器”，然后在可用模板列表中，单击“空白逻辑应用”。

7.    键入“Dynamics 365”。 列表中有几个 Dynamics 365 触发器，请单击“Dynamics 365 - 创建记录时”。

8.    如果系统提示你登录 Dynamics 365，请立即登录。

9.    在触发器详细信息中，输入以下信息。

  * **组织名称**。 选择要让逻辑应用侦听的 Dynamics 365 实例。

  * **实体名称**。 选择要侦听的实体，该实体将用作启动逻辑应用的触发器。 本演练中选择“潜在客户”。

  * **你想多久检查一次项？** 这些值设置逻辑应用检查触发器相关更新的频率。 默认设置为每&3; 分钟检查一次更新。

    * **频率**。 选择秒、分钟、小时或天。

    * **时间间隔**。 输入一个数字，该数字指示在下次检查前的秒数、分钟数、小时数或天数。

    ![逻辑应用触发器详细信息](./media/connectors-create-api-crmonline/trigger-details.png)

10.    单击“新建步骤”，然后单击“添加操作”。

11.    键入“Dynamics 365”，然后在列表中单击“Dynamics 365 - 创建新记录”。

12.    输入以下信息。
  * **组织名称**。 选择要让流在其中创建记录的 Dynamics 365 实例。 请注意，它可与从中触发事件的实例不同。
  * **实体名称**。 选择在触发事件时要创建记录的实体。 本演练将选择“任务”。

13.    将显示一个“主题”框。 单击该框时，将显示一个动态内容窗格，在窗格中可选择以下任一字段。
  * **姓氏**。 创建任务记录后，选择此字段会将潜在客户的姓氏插入到任务的“主题”字段中。
  * **主题**。 创建任务记录后，选择此字段会将潜在客户的“主题”字段插入到任务的“主题”字段中。
单击“主题”以将其添加到“主题”框。

  ![逻辑应用创建新记录详细信息](./media/connectors-create-api-crmonline/create-record-details.png)

14.    在“逻辑应用设计器”工具栏上，单击“保存”。

  ![“逻辑应用设计器”工具栏的“保存”按钮](./media/connectors-create-api-crmonline/designer-toolbar-save.png)

15.    若要启动逻辑应用，请单击“运行”。

  ![“逻辑应用设计器”工具栏的“保存”按钮](./media/connectors-create-api-crmonline/designer-toolbar-run.png)

16. 立即在 Dynamics 365 for Sales 中创建潜在客户记录并查看正在运行的流！

## <a name="using-advanced-options"></a>使用高级选项
向逻辑应用添加步骤时，单击“显示高级选项”可通过添加筛选器控制在步骤中筛选数据的方式，或根据查询进行排序。

例如，可以使用筛选查询只检索活动帐户并按帐户名称排序。 若要执行此操作，请输入 OData 筛选查询“statuscode eq 1”，然后从动态内容窗格选择“帐户名称”。 详细信息：[MSDN：$filter](https://msdn.microsoft.com/library/gg309461.aspx#Anchor_1) 和 [$orderby](https://msdn.microsoft.com/library/gg309461.aspx#Anchor_2)。

  ![逻辑应用高级选项](./media/connectors-create-api-crmonline/advanced-options.png)

### <a name="best-practices-when-using-advanced-options"></a>使用高级选项的最佳做法
请注意，向字段添加值时，无论是键入一个值还是从显示的动态内容中选择一个值，该值都必须与字段类型匹配。

字段类型  |如何使用  |查找位置  |Name  |数据类型  
---------|---------|---------|---------|---------
文本字段|文本字段需要单个文本行或类型为文本字段的动态内容。 示例包括“类别”和“子类别”字段。|“设置”>“自定义”>“自定义系统”>“实体”>“任务”>“字段” |category |单个文本行。       
整数字段 | 有些字段需要整数或类型为整数字段的动态内容。 示例包括“完成百分比”和“持续时间”。 |“设置”>“自定义”>“自定义系统”>“实体”>“任务”>“字段” |完成百分比 |整数         
数据字段 | 有些字段需要输入格式为 mm/dd/yyyy 的数据或类型为数据字段的动态内容。 示例包括“创建时间”、“开始日期”、“实际开始时间”、“上一暂候时间”、“实际结束时间”和“截止日期”。 | “设置”>“自定义”>“自定义系统”>“实体”>“任务”>“字段” |创建时间 |日期和时间         
需要记录 ID 和查找类型的字段 |一些引用另一实体记录的字段需要记录 ID 和查找类型。 |“设置”>“自定义”>“自定义系统”>“实体”>“帐户”>“字段”  | 帐户 ID   | 主密钥

### <a name="more-examples-of-fields-that-require-both-a-record-id-and-lookup-type"></a>需要记录 ID 和查找类型的字段的更多示例
以下是未使用从动态内容列表选择的值的字段的更多示例，进一步介绍了上表的内容。 相反，这些字段需要在 PowerApps 的字段中输入的记录 ID 和查找类型。  
*  所有者和所有者类型。 “所有者”字段必须是有效的用户或团队记录 ID。 “所有者类型”必须是“系统用户”或“团队”。
* 客户和客户类型。 “客户”字段必须是有效的帐户或联系人记录 ID。 “客户类型”必须是“帐户”或“联系人”。
* 相关和相关类型。 “相关”字段必须是有效的记录 ID，如帐户或联系人记录 ID。 “相关类型”必须是记录的查找类型，如“帐户”或“联系人”。

下面的任务创建操作示例添加了一个帐户记录，该记录与将其添加到任务相关字段的记录 ID 相对应。

  ![流记录 ID 和类型帐户](./media/connectors-create-api-crmonline/recordid-type-account.png)

此示例还根据用户的记录 ID 将任务分配给指定用户。
  ![流记录 ID 和类型帐户](./media/connectors-create-api-crmonline/recordid-type-user.png)

若要查找记录 ID，请参阅下面的“查找记录 ID”部分。

## <a name="find-the-record-id"></a>查找记录 ID
1. 打开一个记录，如帐户记录。

2. 在操作工具栏上，单击“弹出”![弹出记录](./media/connectors-create-api-crmonline/popout-record.png)。
或者，在操作工具栏上，单击“通过电子邮件发送链接”，将完整 URL 复制到你的默认电子邮件程序。

3. 记录 ID 将显示在 URL 的 %7b 和 %7d 编码字符之间。

  ![流记录 ID 和类型帐户](./media/connectors-create-api-crmonline/recordid.png)

## <a name="troubleshooting"></a>故障排除
若要解决逻辑应用中的失败步骤，请查看事件状态的详细信息。

1. 在“逻辑应用”区域，单击你的逻辑应用，然后单击“概述”。 显示“摘要”区域，它可提供逻辑应用的运行状态。 如果存在失败的运行，请单击要查看其详细信息的失败事件。

  ![逻辑应用故障排除步骤 1](./media/connectors-create-api-crmonline/tshoot1.png)

2. 单击失败步骤以将其展开。

  ![逻辑应用故障排除步骤 2](./media/connectors-create-api-crmonline/tshoot2.png)

3. 显示步骤的详细信息，有助于找到失败的原因。

    ![逻辑应用故障排除步骤 2](./media/connectors-create-api-crmonline/tshoot3.png)

若要深入了解如何解决逻辑应用的故障，请参阅[诊断逻辑应用的故障](../logic-apps/logic-apps-diagnosing-failures.md)。

## <a name="technical-details"></a>技术详细信息
## <a name="triggers"></a>触发器
| 触发器 | 说明 |
| --- | --- |
| 创建记录时 |在 Dynamics 365 中创建对象时，触发流。 |
| 更新记录时 |在 Dynamics 365 中修改对象时，触发流。 |
| 删除记录时 |在 Dynamics 365 中删除对象时，触发流。 |

## <a name="actions"></a>操作
| 操作 | 说明 |
| --- | --- |
| 列出记录 |此操作可获取实体的记录。 |
| 新建记录 |此操作可创建新的实体记录。 |
| 获取记录 |此操作可获取实体的指定记录。 |
| 删除记录 |此操作可从实体集合中删除记录。 |
| 更新记录 |此操作可更新实体的现有记录。 |

### <a name="trigger-and-action-details"></a>触发器和操作详细信息
在此部分中，查看有关每个触发器和每项操作的具体详细信息，包括任何必需或可选的输入属性以及与连接器相关联的任何相应输出。

#### <a name="when-a-record-is-created"></a>创建记录时
在 Dynamics 365 中创建对象时，触发流。

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| dataset* |组织名称 |Dynamics 365 组织的名称（如 Contoso） |
| table* |实体名称 |实体的名称 |
| $filter |筛选查询 |要限制已返回项的 ODATA 筛选查询 |
| $orderby |排序依据 |用于指定项顺序的 ODATA orderBy 查询 |

星号 (*) 表示该属性是必需的。

##### <a name="output-details"></a>输出详细信息
ItemsList

| 属性名称 | 数据类型 |
| --- | --- |
| value |数组 |

#### <a name="when-a-record-is-updated"></a>更新记录时
在 Dynamics 365 中修改对象时，触发流。

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| dataset* |组织名称 |Dynamics 365 组织的名称（如 Contoso） |
| table* |实体名称 |实体的名称 |

星号 (*) 表示该属性是必需的。

##### <a name="output-details"></a>输出详细信息
ItemsList

| 属性名称 | 数据类型 |
| --- | --- |
| value |数组 |

#### <a name="when-a-record-is-deleted"></a>删除记录时
在 Dynamics 365 中删除对象时，触发流。

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| dataset* |组织名称 |Dynamics 365 组织的名称（如 Contoso） |
| table* |实体名称 |实体的名称 |


星号 (*) 表示该属性是必需的。

##### <a name="output-details"></a>输出详细信息
ItemsList

| 属性名称 | 数据类型 |
| --- | --- |
| value |数组 |

#### <a name="list-records"></a>列出记录
此操作可获取实体的记录。

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| dataset* |组织名称 |Dynamics 365 组织的名称（如 Contoso） |
| table* |实体名称 |实体的名称 |
| $filter |筛选查询 |要限制已返回项的 ODATA 筛选查询 |
| $orderby |排序依据 |用于指定项顺序的 ODATA orderBy 查询 |

星号 (*) 表示该属性是必需的。

##### <a name="output-details"></a>输出详细信息
ItemsList

| 属性名称 | 数据类型 |
| --- | --- |
| value |数组 |

#### <a name="create-a-new-record"></a>新建记录
此操作可创建新的实体记录。

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| dataset* |组织名称 |Dynamics 365 组织的名称（如 Contoso） |
| table* |实体名称 |实体的名称 |

星号 (*) 表示该属性是必需的。

##### <a name="output-details"></a>输出详细信息
无。

#### <a name="get-record"></a>获取记录
此操作可获取实体的指定记录。

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| dataset* |组织名称 |Dynamics 365 组织的名称（如 Contoso） |
| table* |实体名称 |实体的名称 |
| ID* |项目标识符 |指定记录的标识符 |

星号 (*) 表示该属性是必需的。

##### <a name="output-details"></a>输出详细信息
无。

#### <a name="delete-a-record"></a>删除记录
此操作可从实体集合中删除记录。

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| dataset* |组织名称 |Dynamics 365 组织的名称（如 Contoso） |
| table* |实体名称 |实体的名称 |
| ID* |项目标识符 |指定记录的标识符 |

星号 (*) 表示该属性是必需的。

#### <a name="update-a-record"></a>更新记录
此操作可更新实体的现有记录。

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| dataset* |组织名称 |Dynamics 365 组织的名称（如 Contoso） |
| table* |实体名称 |实体的名称 |
| ID* |记录标识符 |指定记录的标识符 |

星号 (*) 表示该属性是必需的。

##### <a name="output-details"></a>输出详细信息
无。

## <a name="http-responses"></a>HTTP 响应
操作和触发器可以返回以下一个或多个 HTTP 状态代码：

| Name | 说明 |
| --- | --- |
| 200 |确定 |
| 202 |已接受 |
| 400 |错误的请求 |
| 401 |未授权 |
| 403 |禁止 |
| 404 |未找到 |
| 500 |内部服务器错误。 发生未知错误。 |
| default |操作失败。 |

## <a name="next-steps"></a>后续步骤
在我们的 [API 列表](apis-list.md)中了解逻辑应用中的其他可用连接器。



<!--HONumber=Feb17_HO2-->


