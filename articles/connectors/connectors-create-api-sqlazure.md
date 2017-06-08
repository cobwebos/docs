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
ms.author: mandia; ladocs
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 3c228be32539050123b01c5ccd74547b0d04ed28
ms.contentlocale: zh-cn
ms.lasthandoff: 05/10/2017


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
操作是指在逻辑应用中定义的工作流所执行的操作。 [了解有关操作的详细信息](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts)。

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

## <a name="view-the-swagger"></a>查看 Swagger
请参阅 [Swagger 详细信息](/connectors/sql/)。 

## <a name="next-steps"></a>后续步骤
[创建逻辑应用](../logic-apps/logic-apps-create-a-logic-app.md)。 在我们的 [API 列表](apis-list.md)中了解逻辑应用中的其他可用连接器。


