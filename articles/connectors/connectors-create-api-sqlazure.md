---
title: 连接到 SQL Server 或 Azure SQL 数据库-Azure 逻辑应用
description: 使用 Azure 逻辑应用为本地或云中的 SQL 数据库自动执行任务
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam; LADocs
manager: carmonm
ms.topic: conceptual
tags: connectors
ms.date: 10/14/2019
ms.openlocfilehash: 880ae4b661d247889815fc5b9ad08a759fe0aa5b
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2019
ms.locfileid: "73161609"
---
# <a name="automate-workflows-for-sql-server-or-azure-sql-database-by-using-azure-logic-apps"></a>使用 Azure 逻辑应用自动执行 SQL Server 或 Azure SQL 数据库的工作流

本文介绍如何通过逻辑应用使用 SQL Server 连接器访问 SQL 数据库中的数据。 这样，你就可以通过创建逻辑应用来自动管理 SQL 数据和资源的任务、进程或工作流。 SQL Server 连接器适用于[本地 SQL Server](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation)和[基于云的 Azure SQL 数据库](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview)。

可以创建在受 SQL 数据库或其他系统（例如 Dynamics CRM Online）中的事件触发时运行的逻辑应用。 逻辑应用还可以获取、插入和删除数据，以及运行 SQL 查询和存储过程。 例如，你可以生成一个逻辑应用，用于在 Dynamics CRM Online 中自动检查新记录，将项目添加到 SQL 数据库中的任何新记录，然后发送有关已添加项的电子邮件警报。

如果不熟悉逻辑应用，请查看[什么是 Azure 逻辑应用](../logic-apps/logic-apps-overview.md)和[快速入门：创建第一个逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。 有关连接器特定的技术信息、限制和已知问题，请参阅[SQL Server 连接器参考页](https://docs.microsoft.com/connectors/sql/)。

## <a name="prerequisites"></a>必备组件

* Azure 订阅。 如果没有订阅，可以[注册免费的 Azure 帐户](https://azure.microsoft.com/free/)。

* [SQL Server 数据库](https://docs.microsoft.com/sql/relational-databases/databases/create-a-database)或[Azure SQL 数据库](../sql-database/sql-database-get-started-portal.md)

  表必须有数据，这样逻辑应用在调用操作时才能够返回结果。 如果创建 Azure SQL 数据库，可以使用随附的示例数据库。

* SQL Server 名称、数据库名称、用户名和密码。 需要提供这些凭据才能授权应用访问 SQL Server。

  * 对于 SQL Server，可以在连接字符串中查找这些详细信息：

    `Server={your-server-address};Database={your-database-name};User Id={your-user-name};Password={your-password};`

  * 对于 Azure SQL 数据库，可以在连接字符串中查找这些详细信息，也可以在 Azure 门户的 SQL 数据库属性下查找：

    `Server=tcp:{your-server-name}.database.windows.net,1433;Initial Catalog={your-database-name};Persist Security Info=False;User ID={your-user-name};Password={your-password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;`

* 必须先[设置本地数据网关](../logic-apps/logic-apps-gateway-install.md)，然后才能将逻辑应用连接到 SQL Server 等本地系统。 这样便可以在为逻辑应用创建 SQL 连接时选择网关。

* 需在其中访问 SQL 数据库的逻辑应用。 若要通过 SQL 触发器启动逻辑应用，需要一个[空白逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

<a name="add-sql-trigger"></a>

## <a name="add-a-sql-trigger"></a>添加 SQL 触发器

在 Azure 逻辑应用中，每个逻辑应用都必须从[触发器](../logic-apps/logic-apps-overview.md#logic-app-concepts)开始，该触发器在发生特定事件或特定条件得到满足的情况下触发。 每当触发器触发时，逻辑应用引擎就会创建一个逻辑应用实例，并开始运行逻辑应用的工作流。

1. 在 Azure 门户或 Visual Studio 中创建一个空白的逻辑应用，以便打开逻辑应用设计器。 此示例使用 Azure 门户。

1. 在设计器的搜索框中，输入 "sql server" 作为筛选器。 在触发器列表中，选择所需的 SQL 触发器。

   此示例使用 "**创建项时**" 触发器。

   ![选择 "创建项时" 触发器](./media/connectors-create-api-sqlazure/select-sql-server-trigger.png)

1. 如果系统提示你创建连接，请[立即创建 SQL 连接](#create-connection)。 如果连接存在，请选择一个**表名称**。

   ![选择所需的表](./media/connectors-create-api-sqlazure/azure-sql-database-table.png)

1. 设置“时间间隔”和“频率”属性，以便指定逻辑应用检查表的频率。

   此触发器仅返回所选表中的一行，而不返回任何其他行。 若要执行其他任务，请添加执行所需任务的其他操作。 例如，若要查看此行中的数据，您可以添加其他操作，以创建包含返回行中的字段的文件，然后发送电子邮件警报。 若要了解此连接器的其他可用操作，请参阅[连接器的参考页](https://docs.microsoft.com/connectors/sql/)。

1. 完成后，请在设计器工具栏上选择 "**保存**"。

   此步骤自动在 Azure 中实时启用和发布逻辑应用。

<a name="add-sql-action"></a>

## <a name="add-a-sql-action"></a>添加 SQL 操作

在 Azure 逻辑应用中，[操作](../logic-apps/logic-apps-overview.md#logic-app-concepts)是指工作流中紧跟在某个触发器或另一操作后面执行的一个步骤。 在此示例中，逻辑应用一开始使用[定期触发器](../connectors/connectors-native-recurrence.md)，然后调用一个从 SQL 数据库获取行的操作。

1. 在 Azure 门户或 Visual Studio 的逻辑应用设计器中打开逻辑应用。 此示例使用 Azure 门户。

1. 在要添加 SQL 操作的触发器或操作下，选择 "**新建步骤**"。

   ![向逻辑应用添加新步骤](./media/connectors-create-api-sqlazure/select-new-step-logic-app.png)

   若要在现有步骤之间添加操作，请将鼠标移到连接箭头上方。 选择出现的加号（ **+** ），然后选择 "**添加操作**"。

1. 在 "**选择操作**" 下的 "搜索" 框中，输入 "sql server" 作为筛选器。 从 "操作" 列表中，选择所需的 SQL 操作。

   此示例使用获取**行**操作，该操作将获取单个记录。

   ![查找并选择 SQL "获取行" 操作](./media/connectors-create-api-sqlazure/find-select-sql-get-row-action.png)

   此操作仅返回所选表中的一行，而不返回任何其他行。 若要查看此行中的数据，您可以添加其他操作，以创建包含返回行中的字段的文件，并将该文件存储在云存储帐户中。 若要了解此连接器的其他可用操作，请参阅[连接器的参考页](https://docs.microsoft.com/connectors/sql/)。

1. 如果系统提示你创建连接，请[立即创建 SQL 连接](#create-connection)。 如果连接存在，请选择一个**表名称**，然后输入所需的记录的**行 ID** 。

   ![输入表名和行 ID](./media/connectors-create-api-sqlazure/specify-table-row-id-property-value.png)

1. 完成后，请在设计器工具栏上选择 "**保存**"。

   此步骤自动在 Azure 中实时启用和发布逻辑应用。

<a name="create-connection"></a>

## <a name="connect-to-your-database"></a>连接到数据库

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

[!INCLUDE [Create a connection to SQL Server or Azure SQL Database](../../includes/connectors-create-api-sqlazure.md)]

## <a name="handle-bulk-data"></a>处理大容量数据

有时，您必须处理结果集，使连接器不会同时返回所有结果，或者需要更好地控制结果集的大小和结构。 下面是一些处理此类大型结果集的方法：

* 为了帮助你以较小的集管理结果，请启用*分页*。 有关详细信息，请参阅[使用分页获取批量数据、记录和项](../logic-apps/logic-apps-exceed-default-page-size-with-pagination.md)。

* 创建一个存储过程，该存储过程按所需的方式组织结果。

  获取或插入多个行时，逻辑应用可以通过在这些[限制](../logic-apps/logic-apps-limits-and-config.md)内使用[*until 循环*](../logic-apps/logic-apps-control-flow-loops.md#until-loop)来循环访问这些行。 但是，当你的逻辑应用程序必须使用记录集（例如成千上万行或数百万行）时，你想要将对数据库的调用导致的成本降至最低。

  若要以所需的方式组织结果，可以创建在 SQL 实例中运行并使用**SELECT-ORDER BY**语句的[*存储过程*](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine)。 此解决方案可以更好地控制结果的大小和结构。 逻辑应用可以使用 SQL Server 连接器的“执行存储过程”操作调用存储过程。

  有关更多解决方案的详细信息，请参阅以下文章：

  * [SQL Pagination for bulk data transfer with Logic Apps](https://social.technet.microsoft.com/wiki/contents/articles/40060.sql-pagination-for-bulk-data-transfer-with-logic-apps.aspx)（通过 SQL 分页使用逻辑应用进行批量数据传输）

  * [SELECT - ORDER BY 子句](https://docs.microsoft.com/sql/t-sql/queries/select-order-by-clause-transact-sql)

## <a name="connector-specific-details"></a>特定于连接器的详细信息

有关此连接器的触发器、操作和限制的技术信息，请参阅[连接器的参考页](https://docs.microsoft.com/connectors/sql/)。

## <a name="next-steps"></a>后续步骤

* 了解[Azure 逻辑应用的其他连接器](../connectors/apis-list.md)