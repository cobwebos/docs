---
title: 连接到 SQL Server 或 Azure SQL 数据库 - Azure 逻辑应用 | Microsoft Docs
description: 如何使用 Azure 逻辑应用通过自动化工作流来访问和管理本地或云中的 SQL 数据库
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.date: 05/15/2018
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: dccb91c782408a5fed5c3ef1b68f9918823ce402
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2018
ms.locfileid: "35296282"
---
# <a name="connect-to-sql-server-or-azure-sql-database-from-azure-logic-apps"></a>通过 Azure 逻辑应用连接到 SQL Server 或 Azure SQL 数据库

本文介绍如何通过逻辑应用使用 SQL Server 连接器访问 SQL 数据库中的数据。 这样一来，你可以通过创建逻辑应用来自动执行任务、进程和工作流，以便管理你的 SQL 数据和资源。 连接器适用于[本地 SQL Server](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation) 和[云中的 Azure SQL 数据库](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview)。 

可以创建在受 SQL 数据库或其他系统（例如 Dynamics CRM Online）中的事件触发时运行的逻辑应用。 逻辑应用也可获取、插入或删除数据，以及执行 SQL 查询或存储过程。 例如，可以生成一个逻辑应用，让其自动检查 Dynamics CRM Online 中是否存在新记录，在存在新记录的情况下向 SQL 数据库添加相应的项，然后发送电子邮件警报。

如果没有 Azure 订阅，请<a href="https://azure.microsoft.com/free/" target="_blank">注册一个免费 Azure 帐户</a>。 如果不熟悉逻辑应用，请查看[什么是 Azure 逻辑应用](../logic-apps/logic-apps-overview.md)和[快速入门：创建第一个逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。 如需特定于连接器的技术信息，请参阅 <a href="https://docs.microsoft.com/connectors/sql/" target="blank">SQL Server 连接器参考</a>。

## <a name="prerequisites"></a>先决条件

* 需在其中访问 SQL 数据库的逻辑应用。 若要通过 SQL 触发器启动逻辑应用，需要一个[空白逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。 

* 一个 [Azure SQL 数据库](../sql-database/sql-database-get-started-portal.md)或 [SQL Server 数据库](https://docs.microsoft.com/sql/relational-databases/databases/create-a-database) 

  表必须有数据，这样逻辑应用在调用操作时才能够返回结果。 如果创建 Azure SQL 数据库，可以使用随附的示例数据库。 

* SQL Server 名称、数据库名称、用户名和密码。 需要提供这些凭据才能授权应用访问 SQL Server。 

  * 对于 Azure SQL 数据库，可以在连接字符串中查找这些详细信息，也可以在 Azure 门户的 SQL 数据库属性下查找：

    "Server=tcp:<*yourServerName*>.database.windows.net,1433;Initial Catalog=<*yourDatabaseName*>;Persist Security Info=False;User ID=<*yourUserName*>;Password=<*yourPassword*>;MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;"

  * 对于 SQL Server，可以在连接字符串中查找这些详细信息： 

    "Server=<*yourServerAddress*>;Database=<*yourDatabaseName*>;User Id=<*yourUserName*>;Password=<*yourPassword*>;"

* 在将逻辑应用连接到本地系统（例如 SQL Server）之前，必须[设置本地数据网关](../logic-apps/logic-apps-gateway-install.md)。 这样便可以在为逻辑应用创建 SQL 连接时选择网关。

<a name="add-sql-trigger"></a>

## <a name="add-sql-trigger"></a>添加 SQL 触发器

在 Azure 逻辑应用中，每个逻辑应用都必须从[触发器](../logic-apps/logic-apps-overview.md#logic-app-concepts)开始，该触发器在发生特定事件或特定条件得到满足的情况下触发。 每当触发器触发时，逻辑应用引擎就会创建一个逻辑应用实例并开始运行应用的工作流。

1. 在 Azure 门户或 Visual Studio 中创建一个空白的逻辑应用，以便打开逻辑应用设计器。 此示例使用 Azure 门户。

2. 在搜索框中，输入“sql server”作为筛选器。 在触发器列表中，选择所需的 SQL 触发器。 

   对于此示例，请选择此触发器：**SQL Server - 创建项时**

   ![选择“SQL Server - 创建项时”触发器](./media/connectors-create-api-sqlazure/sql-server-trigger.png)

3. 如果系统提示输入连接详细信息，请[立即创建 SQL 连接](#create-connection)。 
   或者，如果连接已存在，请从列表中选择所需的**表名**。

   ![选择表](./media/connectors-create-api-sqlazure/azure-sql-database-table.png)

4. 设置“时间间隔”和“频率”属性，以便指定逻辑应用检查表的频率。

   此示例仅检查选定表，不检查其他。 
   可以添加执行所需任务的操作，让操作更有意思。 
   
   例如，若要查看表中的新项，可以添加其他操作（例如创建一个文件，其字段来自表），然后发送电子邮件警报。 
   若要了解此连接器或其他连接器的其他操作，请参阅[逻辑应用连接器](../connectors/apis-list.md)。

5. 完成后，请在设计器工具栏上选择“保存”。 

   此步骤自动在 Azure 中实时启用和发布逻辑应用。 

<a name="add-sql-action"></a>

## <a name="add-sql-action"></a>添加 SQL 操作

在 Azure 逻辑应用中，[操作](../logic-apps/logic-apps-overview.md#logic-app-concepts)是指工作流中紧跟在某个触发器或另一操作后面执行的一个步骤。 在此示例中，逻辑应用一开始使用[定期触发器](../connectors/connectors-native-recurrence.md)，然后调用一个从 SQL 数据库获取行的操作。

1. 在 Azure 门户或 Visual Studio 的逻辑应用设计器中打开逻辑应用。 此示例使用 Azure 门户。

2. 在逻辑应用设计器的触发器或操作下，选择“新建步骤” > “添加操作”。

   ![选择“新建步骤”>“添加操作”](./media/connectors-create-api-sqlazure/add-action.png)
   
   若要在现有步骤之间添加操作，请将鼠标移到连接箭头上方。 
   选择出现的加号 (**+**)，然后选择“添加操作”。

2. 在搜索框中，输入“sql server”作为筛选器。 从操作列表中，选择所需的任意 SQL 操作。 

   对于此示例，请选择用于获取单个记录的以下操作：**SQL Server - 获取行**

   ![输入“sql server”，选择“SQL Server - 获取行”](./media/connectors-create-api-sqlazure/select-sql-get-row.png) 

3. 如果系统提示输入连接详细信息，请[立即创建 SQL 连接](#create-connection)。 
   或者，如果连接已存在，请选择一个**表名**，然后输入所需记录对应的**行 ID**。

   ![输入表名和行 ID](./media/connectors-create-api-sqlazure/table-row-id.png)
   
   此示例仅从选定表返回一个行，不返回其他。 
   若要查看此行中的数据，可以添加其他操作，以便使用行中的字段创建一个供以后查看的文件，然后将该文件存储在云存储帐户中。 若要了解此连接器或其他连接器中的其他操作，请参阅[逻辑应用连接器](../connectors/apis-list.md)。

4. 完成后，请在设计器工具栏上选择“保存”。 

<a name="create-connection"></a>

## <a name="connect-to-your-database"></a>连接到数据库

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

[!INCLUDE [Create a connection to SQL Server or Azure SQL Database](../../includes/connectors-create-api-sqlazure.md)]

## <a name="process-data-in-bulk"></a>批量处理数据

如果处理的结果集很大，以致连接器无法一次性返回所有结果，或者需要更好地控制结果集的大小和结构，则可使用分页，以较小的集来管理这些结果。 

[!INCLUDE [Set up pagination for results exceeding default page size](../../includes/connectors-pagination-bulk-data-transfer.md)]

### <a name="create-a-stored-procedure"></a>创建存储过程

获取或插入多个行时，逻辑应用可以在这些[限制](../logic-apps/logic-apps-limits-and-config.md)中使用 [*until loop*](../logic-apps/logic-apps-control-flow-loops.md#until-loop) 来循环访问这些项。 但是，有时候逻辑应用需要处理的记录集很大（例如行的数目达到数千或数百万的地步），需要尽量减少针对数据库的调用的成本。 

可以改为创建一个<a href="https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine" target="blank">*存储过程*</a>，该过程在 SQL 实例中运行，并使用 **SELECT - ORDER BY** 语句按你所需要的方式来组织结果。 此解决方案可以更好地控制结果的大小和结构。 逻辑应用可以使用 SQL Server 连接器的“执行存储过程”操作调用存储过程。 

如需解决方案详细信息，请参阅以下文章：

* <a href="https://social.technet.microsoft.com/wiki/contents/articles/40060.sql-pagination-for-bulk-data-transfer-with-logic-apps.aspx" target="_blank">SQL Pagination for bulk data transfer with Logic Apps</a>（通过 SQL 分页使用逻辑应用进行批量数据传输）

* <a href="https://docs.microsoft.com/sql/t-sql/queries/select-order-by-clause-transact-sql" target="_blank">SELECT - ORDER BY 子句</a>

## <a name="connector-specific-details"></a>特定于连接器的详细信息

有关此连接器的触发器、操作和限制的技术信息，请参阅[连接器的参考详细信息](/connectors/sql/)。 

## <a name="next-steps"></a>后续步骤

* 了解其他[逻辑应用连接器](../connectors/apis-list.md)

