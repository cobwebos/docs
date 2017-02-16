---
title: "在逻辑应用中添加 Azure SQL 数据库连接器 | Microsoft Docs"
description: "使用 REST API 参数的 Azure SQL 数据库连接器概述"
services: 
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: d8a319d0-e4df-40cf-88f0-29a6158c898c
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/18/2016
ms.author: mandia
translationtype: Human Translation
ms.sourcegitcommit: 66fc8f7e1da55dbe6bb1dd8b8d6a535c498c1cf7
ms.openlocfilehash: ce3a622db8667df8b3f1d1391c2aa0d7e1e012a5


---
# <a name="get-started-with-the-azure-sql-database-connector"></a>Azure SQL 数据库连接器入门
使用 Azure SQL 数据库连接器，为管理表中数据的组织创建工作流。 

借助 SQL 数据库，可以：

* 通过向客户数据库添加新客户或在订单数据库中更新订单生成工作流。
* 使用操作获取数据行、插入新行，甚至删除。 例如，在 Dynamics CRM Online 中创建记录时（触发器），则在 Azure SQL 数据库中插入行（操作）。 

本主题演示了如何在逻辑应用中使用 SQL 数据库连接器，还列出了相关操作。

> [!NOTE]
> 此文章版本适用于逻辑应用通用版本 (GA)。 
> 
> 

若要了解有关逻辑应用的详细信息，请参阅[什么是逻辑应用](../logic-apps/logic-apps-what-are-logic-apps.md)和[创建逻辑应用](../logic-apps/logic-apps-create-a-logic-app.md)。

## <a name="connect-to-azure-sql-database"></a>连接到 Azure SQL 数据库
需要先创建到任何服务的*连接*，然后逻辑应用才能访问该服务。 连接提供逻辑应用和其他服务之间的连接性。 例如，若要连接到 SQL 数据库，首先创建 SQL 数据库*连接*。 若要创建连接，输入通常用于访问要连接到的服务的凭据。 因此在 SQL 数据库中，输入 SQL 数据库凭据以创建连接。 

#### <a name="create-the-connection"></a>创建连接
> [!INCLUDE [Create the connection to SQL Azure](../../includes/connectors-create-api-sqlazure.md)]
> 
> 

## <a name="use-a-trigger"></a>使用触发器
此连接器没有任何触发器。 使用其他触发器启动逻辑应用，例如重复触发器、HTTP Webhook 触发器、可用于其他连接器的触发器等。 [创建逻辑应用](../logic-apps/logic-apps-create-a-logic-app.md)提供了一个示例。

## <a name="use-an-action"></a>使用操作
操作是指在逻辑应用中定义的由工作流执行的操作。 [了解有关操作的详细信息](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts)。

1. 选择加号。 可看到多个选项：“添加操作”、“添加条件”或“更多”选项之一。
   
    ![](./media/connectors-create-api-sqlazure/add-action.png)
2. 选择“添加操作”。
3. 在文本框中，键入“sql”获取所有可用操作的列表。
   
    ![](./media/connectors-create-api-sqlazure/sql-1.png) 
4. 在此示例中，选择“SQL Server - 获取行”。 如果连接已存在，则从下拉列表中选择“表名称”，并输入要返回的“行 ID”。
   
    ![](./media/connectors-create-api-sqlazure/sample-table.png)
   
    如果提示你提供连接信息，则输入详细信息以创建连接。 本主题中的[创建连接](connectors-create-api-sqlazure.md#create-the-connection)介绍了这些属性。 
   
   > [!NOTE]
   > 在此示例中，我们从表中返回一行。 若要查看此行中的数据，添加使用表中的字段创建文件的另一项操作。 例如，添加一个 OneDrive 操作，该操作使用 FirstName 和 LastName 字段在云存储帐户中创建新文件。 
   > 
   > 
5. **保存**更改（工具栏的左上角）。 你的逻辑应用将保存，并且可能自动启用。

## <a name="technical-details"></a>技术详细信息
## <a name="sql-database-actions"></a>SQL 数据库操作
操作是指在逻辑应用中定义的由工作流执行的操作。 SQL 数据库连接器包括以下操作。 

| 操作 | 说明 |
| --- | --- |
| [ExecuteProcedure](connectors-create-api-sqlazure.md#execute-stored-procedure) |执行 SQL 中的存储过程 |
| [GetRow](connectors-create-api-sqlazure.md#get-row) |从 SQL 表中检索单个行 |
| [GetRows](connectors-create-api-sqlazure.md#get-rows) |从 SQL 表中检索多个行 |
| [InsertRow](connectors-create-api-sqlazure.md#insert-row) |在 SQL 表中插入一个新行 |
| [DeleteRow](connectors-create-api-sqlazure.md#delete-row) |从 SQL 表中删除行 |
| [GetTables](connectors-create-api-sqlazure.md#get-tables) |从 SQL 数据库中检索表 |
| [UpdateRow](connectors-create-api-sqlazure.md#update-row) |更新 SQL 表中的现有行 |

### <a name="action-details"></a>操作详细信息
在此部分中，查看有关每项操作的具体详细信息，包括任何必需或可选的输入属性以及与连接器相关联的任何相应输出。

#### <a name="execute-stored-procedure"></a>执行存储过程
执行 SQL 中的存储过程。  

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| procedure * |过程名称 |要执行的存储过程的名称 |
| 参数 * |输入参数 |参数是动态的，并且基于所选的存储过程。 <br/><br/> 例如，如果使用 Adventure Works 示例数据库，请选择 *ufnGetCustomerInformation* 存储过程。 显示**客户 ID** 输入参数。 输入“6”或其他客户 ID 之一。 |

星号 (*) 表示该属性是必需的。

##### <a name="output-details"></a>输出详细信息
ProcedureResult：携带存储过程执行的结果

| 属性名称 | 数据类型 | 说明 |
| --- | --- | --- |
| OutputParameters |对象 |输出参数值 |
| ReturnCode |integer |过程的返回代码 |
| ResultSets |对象 |结果集 |

#### <a name="get-row"></a>获取行
从 SQL 表中检索单个行。  

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| table * |表名称 |SQL 表的名称 |
| id * |行 ID |要检索的行的唯一标识符 |

星号 (*) 表示该属性是必需的。

##### <a name="output-details"></a>输出详细信息
项目

| 属性名称 | 数据类型 |
| --- | --- |
| ItemInternalId |字符串 |

#### <a name="get-rows"></a>获取行
从 SQL 表中检索多个行。  

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| table* |表名称 |SQL 表的名称 |
| $skip |跳过计数 |要跳过的项数（默认值 = 0） |
| $top |最大获取计数 |要检索的最大项数（默认值 = 256） |
| $filter |筛选查询 |要限制项数的 ODATA 筛选查询 |
| $orderby |排序依据 |用于指定项顺序的 ODATA orderBy 查询 |

星号 (*) 表示该属性是必需的。

##### <a name="output-details"></a>输出详细信息
ItemsList

| 属性名称 | 数据类型 |
| --- | --- |
| value |数组 |

#### <a name="insert-row"></a>插入行
在 SQL 表中插入一个新行。  

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| table* |表名称 |SQL 表的名称 |
| item* |行 |要插入 SQL 的指定表中的行 |

星号 (*) 表示该属性是必需的。

##### <a name="output-details"></a>输出详细信息
项目

| 属性名称 | 数据类型 |
| --- | --- |
| ItemInternalId |字符串 |

#### <a name="delete-row"></a>删除行
从 SQL 表中删除行。  

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| table* |表名称 |SQL 表的名称 |
| id* |行 ID |要删除的行的唯一标识符 |

星号 (*) 表示该属性是必需的。

##### <a name="output-details"></a>输出详细信息
无。

#### <a name="get-tables"></a>获取表
从 SQL 数据库中检索表。  

此调用没有任何参数。 

##### <a name="output-details"></a>输出详细信息
TablesList

| 属性名称 | 数据类型 |
| --- | --- |
| value |数组 |

#### <a name="update-row"></a>更新行
更新 SQL 表中的现有行。  

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| table* |表名称 |SQL 表的名称 |
| id* |行 ID |要更新的行的唯一标识符 |
| item* |行 |具有已更新值的行 |

星号 (*) 表示该属性是必需的。

##### <a name="output-details"></a>输出详细信息
项目

| 属性名称 | 数据类型 |
| --- | --- |
| ItemInternalId |字符串 |

### <a name="http-responses"></a>HTTP 响应
调用不同的操作时，可能会收到特定响应。 下表概述了这些响应及其说明：  

| Name | 说明 |
| --- | --- |
| 200 |确定 |
| 202 |已接受 |
| 400 |错误的请求 |
| 401 |未授权 |
| 403 |禁止 |
| 404 |未找到 |
| 500 |内部服务器错误。 发生未知错误 |
| default |操作失败。 |

## <a name="next-steps"></a>后续步骤
[创建逻辑应用](../logic-apps/logic-apps-create-a-logic-app.md)。 在我们的 [API 列表](apis-list.md)中了解逻辑应用中的其他可用连接器。




<!--HONumber=Jan17_HO3-->


