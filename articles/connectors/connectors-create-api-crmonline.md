---
title: "将 Dynamics CRM Online 连接器添加到逻辑应用 | Microsoft Docs"
description: "使用 Azure App Service 创建逻辑应用。 Dynamics CRM Online 连接提供程序提供了一个 API，用于处理 Dynamics CRM Online 上的实体。"
services: logic-apps
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: 0dc2abef-7d2c-4a2d-87ca-fad21367d135
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 11/07/2016
ms.author: mandia
translationtype: Human Translation
ms.sourcegitcommit: 71f9dd111ebdbe885f33d162b2ea320dfaa167bb
ms.openlocfilehash: 317d146dec09cf3239a72c9af471257ce98c458d


---
# <a name="get-started-with-the-dynamics-crm-online-connector"></a>Dynamics CRM Online 连接器入门
连接到 Dynamics CRM Online，新建记录、更新项目等。 通过 CRM Online，你可以：

* 根据从 CRM Online 中获取的数据生成你的业务流。 
* 使用删除记录、获取实体等操作。 这些操作可获得响应，然后使输出可用于其他操作。 例如，在 CRM 中更新项目后，你可以使用 Office 365 发送一封电子邮件。

本主题演示了如何在逻辑应用中使用 Dynamics CRM Online 连接器，还列出了触发器和操作。

> [!NOTE]
> 此文章版本适用于逻辑应用通用版本 (GA)。
> 
> 

若要了解有关逻辑应用的详细信息，请参阅[什么是逻辑应用](../app-service-logic/app-service-logic-what-are-logic-apps.md)和[创建逻辑应用](../app-service-logic/app-service-logic-create-a-logic-app.md)。

## <a name="connect-to-dynamics-crm-online"></a>连接到 Dynamics CRM Online
需要先创建到任何服务的*连接*，然后逻辑应用才能访问该服务。 连接提供逻辑应用和其他服务之间的连接性。 例如，若要连接到 Dynamics，首先需要 Dynamics CRM Online *连接*。 若要创建连接，请输入通常用于访问要连接到的服务的凭据。 因此，在 Dynamics 中，将凭据输入到 Dynamics CRM Online 帐户以创建连接。

### <a name="create-the-connection"></a>创建连接
> [!INCLUDE [Steps to create a connection to Dynamics CRM Online Connection Provider](../../includes/connectors-create-api-crmonline.md)]
> 
> 

## <a name="use-a-trigger"></a>使用触发器
触发器是用于启动在逻辑应用中定义的工作流的事件。 触发器以你希望的间隔和频率“轮询”服务。 [了解有关触发器的详细信息](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts)。

1. 在逻辑应用中，键入“dynamics”获取触发器列表：  
   
    ![](./media/connectors-create-api-crmonline/dynamics-triggers.png)
2. 选择“Dynamics CRM Online - 创建记录时”。 如果连接已存在，从下拉列表中选择组织和实体。
   
    ![](./media/connectors-create-api-crmonline/select-organization.png)
   
    如果提示你登录，则输入登录详细信息以创建连接。 本主题中的[创建连接](connectors-create-api-crmonline.md#create-the-connection)列出了相关步骤。 
   
   > [!NOTE]
   > 在此示例中，逻辑应用在创建记录时运行。 若要查看此触发器的结果，请添加另一个向你发送电子邮件的操作。 例如，添加在添加新记录时向你发送电子邮件的 Office 365“发送电子邮件”操作。 
   > 
   > 
3. 选择“编辑”按钮并设置“频率”和“间隔”值。 例如，如果你希望触发器每 15 分钟轮询一次，将“频率”设置为“分钟”，将“间隔”设置为“15”。 
   
    ![](./media/connectors-create-api-crmonline/edit-properties.png)
4. **保存**更改（工具栏的左上角）。 你的逻辑应用将保存，并且可能自动启用。

## <a name="use-an-action"></a>使用操作
操作是指在逻辑应用中定义的由工作流执行的操作。 [了解有关操作的详细信息](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts)。

1. 选择加号。 可看到多个选项：“添加操作”、“添加条件”或“更多”选项之一。
   
    ![](./media/connectors-create-api-crmonline/add-action.png)
2. 选择“添加操作”。
3. 在文本框中，键入“dynamics”获取所有可用操作的列表。
   
    ![](./media/connectors-create-api-crmonline/dynamics-actions.png)
4. 在我们的示例中，选择“Dynamics CRM Online - 更新记录”。 如果连接已存在，依次选择“组织名称”、“实体名称”和其他属性：  
   
    ![](./media/connectors-create-api-crmonline/sample-action.png)
   
    如果提示你提供连接信息，则输入详细信息以创建连接。 本主题中的[创建连接](connectors-create-api-crmonline.md#create-the-connection)介绍了这些属性。 
   
   > [!NOTE]
   > 在此示例中，我们更新 CRM Online 中的现有记录。 可使用来自其他触发器的输出更新记录。 例如，添加 SharePoint *修改现有项目时*触发器。 然后，添加 CRM Online *更新记录*操作，可使用 SharePoint 字段更新 CRM Online 中的现有记录。 
   > 
   > 
5. **保存**更改（工具栏的左上角）。 你的逻辑应用将保存，并且可能自动启用。

## <a name="technical-details"></a>技术详细信息
## <a name="triggers"></a>触发器
| 触发器 | 说明 |
| --- | --- |
| [创建记录时](connectors-create-api-crmonline.md#when-a-record-is-created) |在 CRM 中创建对象时，触发流。 |
| [更新记录时](connectors-create-api-crmonline.md#when-a-record-is-updated) |在 CRM 中修改对象时，触发流。 |
| [删除记录时](connectors-create-api-crmonline.md#when-a-record-is-deleted) |在 CRM 中删除对象时，触发流。 |

## <a name="actions"></a>操作
| 操作 | 说明 |
| --- | --- |
| [列出记录](connectors-create-api-crmonline.md#list-records) |此操作可获取实体的记录。 |
| [新建记录](connectors-create-api-crmonline.md#create-a-new-record) |此操作可创建新的实体记录。 |
| [获取记录](connectors-create-api-crmonline.md#get-record) |此操作可获取实体的指定记录。 |
| [删除记录](connectors-create-api-crmonline.md#delete-a-record) |此操作可从实体集合中删除记录。 |
| [更新记录](connectors-create-api-crmonline.md#update-a-record) |此操作可更新实体的现有记录。 |

### <a name="trigger-and-action-details"></a>触发器和操作详细信息
在此部分中，查看有关每个触发器和每项操作的具体详细信息，包括任何必需或可选的输入属性以及与连接器相关联的任何相应输出。

#### <a name="when-a-record-is-created"></a>创建记录时
在 CRM 中创建对象时，触发流。 

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| dataset* |组织名称 |CRM 组织的名称（如 Contoso） |
| table* |实体名称 |实体的名称 |
| $skip |跳过计数 |要跳过的项数（默认值 = 0） |
| $top |最大获取计数 |要获取的最大项数（默认值 = 256） |
| $filter |筛选查询 |要限制已返回项的 ODATA 筛选查询 |
| $orderby |排序依据 |用于指定项顺序的 ODATA orderBy 查询 |

星号 (*) 表示该属性是必需的。

##### <a name="output-details"></a>输出详细信息
ItemsList

| 属性名称 | 数据类型 |
| --- | --- |
| value |数组 |

#### <a name="when-a-record-is-updated"></a>更新记录时
在 CRM 中修改对象时，触发流。 

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| dataset* |组织名称 |CRM 组织的名称（如 Contoso） |
| table* |实体名称 |实体的名称 |
| $skip |跳过计数 |要跳过的项数（默认值 = 0） |
| $top |最大获取计数 |要获取的最大项数（默认值 = 256） |
| $filter |筛选查询 |要限制已返回项的 ODATA 筛选查询 |
| $orderby |排序依据 |用于指定项顺序的 ODATA orderBy 查询 |

星号 (*) 表示该属性是必需的。

##### <a name="output-details"></a>输出详细信息
ItemsList

| 属性名称 | 数据类型 |
| --- | --- |
| value |数组 |

#### <a name="when-a-record-is-deleted"></a>删除记录时
在 CRM 中删除对象时，触发流。 

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| dataset* |组织名称 |CRM 组织的名称（如 Contoso） |
| table* |实体名称 |实体的名称 |
| $skip |跳过计数 |要跳过的项数（默认值 = 0） |
| $top |最大获取计数 |要获取的最大项数（默认值 = 256） |
| $filter |筛选查询 |要限制已返回项的 ODATA 筛选查询 |
| $orderby |排序依据 |用于指定项顺序的 ODATA orderBy 查询 |

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
| dataset* |组织名称 |CRM 组织的名称（如 Contoso） |
| table* |实体名称 |实体的名称 |
| $skip |跳过计数 |要跳过的项数（默认值 = 0） |
| $top |最大获取计数 |要获取的最大项数（默认值 = 256） |
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
| dataset* |组织名称 |CRM 组织的名称（如 Contoso） |
| table* |实体名称 |实体的名称 |

星号 (*) 表示该属性是必需的。

##### <a name="output-details"></a>输出详细信息
无。

#### <a name="get-record"></a>获取记录
此操作可获取实体的指定记录。 

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| dataset* |组织名称 |CRM 组织的名称（如 Contoso） |
| table* |实体名称 |实体的名称 |
| ID* |项目标识符 |指定记录的标识符 |

星号 (*) 表示该属性是必需的。

##### <a name="output-details"></a>输出详细信息
无。

#### <a name="delete-a-record"></a>删除记录
此操作可从实体集合中删除记录。 

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| dataset* |组织名称 |CRM 组织的名称（如 Contoso） |
| table* |实体名称 |实体的名称 |
| ID* |项目标识符 |指定记录的标识符 |

星号 (*) 表示该属性是必需的。

#### <a name="update-a-record"></a>更新记录
此操作可更新实体的现有记录。 

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| dataset* |组织名称 |CRM 组织的名称（如 Contoso） |
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
[创建逻辑应用](../app-service-logic/app-service-logic-create-a-logic-app.md)。 在我们的 [API 列表](apis-list.md)中了解逻辑应用中的其他可用连接器。




<!--HONumber=Nov16_HO3-->


