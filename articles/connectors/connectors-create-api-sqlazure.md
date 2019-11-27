---
title: 连接到 SQL Server 或 Azure SQL 数据库 - Azure 逻辑应用
description: 使用 Azure 逻辑应用自动完成本地或云中的 SQL 数据库的任务
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam; LADocs
manager: carmonm
ms.topic: conceptual
tags: connectors
ms.date: 11/08/2019
ms.openlocfilehash: a6367e5897e9bd548550b099c0bd2e6186845d6d
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826336"
---
# <a name="automate-workflows-for-sql-server-or-azure-sql-database-by-using-azure-logic-apps"></a>使用 Azure 逻辑应用自动完成 SQL Server 或 Azure SQL 数据库的工作流

本文介绍如何通过逻辑应用使用 SQL Server 连接器访问 SQL 数据库中的数据。 这样一来，便可通过创建逻辑应用来自动执行任务、进程或工作流，以便管理 SQL 数据和资源。 SQL Server 连接器适用于[本地 SQL Server](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation) 和[基于云的 Azure SQL 数据库](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview)。

可以创建在受 SQL 数据库或其他系统（例如 Dynamics CRM Online）中的事件触发时运行的逻辑应用。 逻辑应用也可获取、插入或删除数据，以及运行 SQL 查询或存储过程。 例如，可以生成一个逻辑应用，让其自动检查 Dynamics CRM Online 中是否存在新记录，在存在新记录的情况下向 SQL 数据库添加相应的项，然后发送有关已添加项的电子邮件警报。

如果不熟悉逻辑应用，请查看[什么是 Azure 逻辑应用](../logic-apps/logic-apps-overview.md)和[快速入门：创建第一个逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。 有关特定于连接器的技术信息、限制和已知问题，请参阅 [SQL Server 连接器参考页](https://docs.microsoft.com/connectors/sql/)。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有订阅，可以[注册免费的 Azure 帐户](https://azure.microsoft.com/free/)。

* 一个 [SQL Server 数据库](https://docs.microsoft.com/sql/relational-databases/databases/create-a-database)或 [Azure SQL 数据库](../sql-database/sql-database-get-started-portal.md)

  表必须有数据，这样逻辑应用在调用操作时才能够返回结果。 如果创建 Azure SQL 数据库，可以使用随附的示例数据库。

* SQL Server 名称、数据库名称、用户名和密码。 需要提供这些凭据才能授权应用访问 SQL Server。

  * 对于 SQL Server，可以在连接字符串中查找这些详细信息：

    `Server={your-server-address};Database={your-database-name};User Id={your-user-name};Password={your-password};`

  * 对于 Azure SQL 数据库，可以在连接字符串中查找这些详细信息，也可以在 Azure 门户的 SQL 数据库属性下查找：

    `Server=tcp:{your-server-name}.database.windows.net,1433;Initial Catalog={your-database-name};Persist Security Info=False;User ID={your-user-name};Password={your-password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;`

* 本地[数据网关](../logic-apps/logic-apps-gateway-install.md)安装在本地计算机上，并在为这些方案[在 Azure 门户中创建的 Azure 数据网关资源](../logic-apps/logic-apps-gateway-connection.md)：

  * 逻辑应用不会在[integration service 环境（ISE）](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)中运行。

  * 逻辑*应用在*integration service 环境中运行，但必须为 SQL Server 连接使用 Windows 身份验证。 对于这种情况，请将 SQL Server 连接器的非 ISE 版本与数据网关一起使用，因为 ISE 版本不支持 Windows 身份验证。

* 需在其中访问 SQL 数据库的逻辑应用。 若要通过 SQL 触发器启动逻辑应用，需要一个[空白逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

<a name="add-sql-trigger"></a>

## <a name="add-a-sql-trigger"></a>添加 SQL 触发器

在 Azure 逻辑应用中，每个逻辑应用都必须从[触发器](../logic-apps/logic-apps-overview.md#logic-app-concepts)开始，该触发器在发生特定事件或特定条件得到满足的情况下触发。 每当触发器触发时，逻辑应用引擎就会创建一个逻辑应用实例并开始运行逻辑应用的工作流。

1. 在 Azure 门户或 Visual Studio 中创建一个空白的逻辑应用，以便打开逻辑应用设计器。 此示例使用 Azure 门户。

1. 在设计器的搜索框中，输入“sql server”作为筛选器。 在触发器列表中，选择所需的 SQL 触发器。

   此示例使用“创建项时”触发器。

   ![选择“创建项时”触发器](./media/connectors-create-api-sqlazure/select-sql-server-trigger.png)

1. 如果系统提示你创建一个连接，请[立即创建 SQL 连接](#create-connection)。 如果连接已存在，请选择一个**表名**。

   ![选择所需的表](./media/connectors-create-api-sqlazure/azure-sql-database-table.png)

1. 设置“时间间隔”和“频率”属性，以便指定逻辑应用检查表的频率。

   此触发器仅从选定表返回一个行，不返回其他内容。 若要执行其他任务，请添加用于执行所需任务的其他操作。 例如，若要查看此行中的数据，可以添加其他操作（用于创建一个文件，其字段来自返回的行），然后发送电子邮件警报。 若要了解此连接器的其他可用操作，请参阅[连接器的参考页](https://docs.microsoft.com/connectors/sql/)。

1. 完成后，请在设计器工具栏上选择“保存”。

   此步骤自动在 Azure 中实时启用和发布逻辑应用。

<a name="add-sql-action"></a>

## <a name="add-a-sql-action"></a>添加 SQL 操作

在 Azure 逻辑应用中，[操作](../logic-apps/logic-apps-overview.md#logic-app-concepts)是指工作流中紧跟在某个触发器或另一操作后面执行的一个步骤。 在此示例中，逻辑应用一开始使用[定期触发器](../connectors/connectors-native-recurrence.md)，然后调用一个从 SQL 数据库获取行的操作。

1. 在 Azure 门户或 Visual Studio 的逻辑应用设计器中打开逻辑应用。 此示例使用 Azure 门户。

1. 在要添加 SQL 操作的触发器或操作下，选择“新建步骤”。

   ![向逻辑应用添加新步骤](./media/connectors-create-api-sqlazure/select-new-step-logic-app.png)

   若要在现有步骤之间添加操作，请将鼠标移到连接箭头上方。 选择出现的加号 ( **+** )，然后选择“添加操作”。

1. 在“选择操作”下的搜索框中，输入“sql server”作为筛选器。 从操作列表中，选择所需的 SQL 操作。

   此示例使用“获取行”操作来获取单个记录。

   ![查找并选择 SQL 的“获取行”操作](./media/connectors-create-api-sqlazure/find-select-sql-get-row-action.png)

   此操作仅从选定表返回一个行，不返回其他内容。 若要查看此行中的数据，可以添加其他操作，以便创建一个文件（其中包含已返回行中的字段），然后将该文件存储在云存储帐户中。 若要了解此连接器的其他可用操作，请参阅[连接器的参考页](https://docs.microsoft.com/connectors/sql/)。

1. 如果系统提示你创建一个连接，请[立即创建 SQL 连接](#create-connection)。 如果连接已存在，请选择一个**表名**，然后输入所需记录对应的**行 ID**。

   ![输入表名和行 ID](./media/connectors-create-api-sqlazure/specify-table-row-id-property-value.png)

1. 完成后，请在设计器工具栏上选择“保存”。

   此步骤自动在 Azure 中实时启用和发布逻辑应用。

<a name="create-connection"></a>

## <a name="connect-to-your-database"></a>连接到数据库

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

[!INCLUDE [Create a connection to SQL Server or Azure SQL Database](../../includes/connectors-create-api-sqlazure.md)]

## <a name="handle-bulk-data"></a>处理批量数据

有时，你必须处理大到连接器不能同时返回所有结果的结果集，或者你希望更好地控制结果集的大小和结构。 以下是处理如此大的结果集的一些方法：

* 为了帮助你将结果作为较小的集进行管理，请启用“分页”。 有关详细信息，请参阅[使用分页获取批量数据、记录和项](../logic-apps/logic-apps-exceed-default-page-size-with-pagination.md)。

* 创建按所需方式组织结果的存储过程。

  获取或插入多个行时，逻辑应用可以在这些[限制*中使用* ](../logic-apps/logic-apps-control-flow-loops.md#until-loop)until loop[](../logic-apps/logic-apps-limits-and-config.md) 来循环访问这些行。 但是，当逻辑应用必须处理非常大的记录（例如，数千或数百万行）时，你希望将调用数据库的成本降到最低。

  若要按所需方式组织结果，可以创建在 SQL 实例中运行并使用 [SELECT - ORDER BY *语句的*](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine)存储过程。 此解决方案可以更好地控制结果的大小和结构。 逻辑应用可以使用 SQL Server 连接器的“执行存储过程”操作调用存储过程。

  如需更多解决方案详细信息，请参阅以下文章：

  * [SQL Pagination for bulk data transfer with Logic Apps](https://social.technet.microsoft.com/wiki/contents/articles/40060.sql-pagination-for-bulk-data-transfer-with-logic-apps.aspx)（通过 SQL 分页使用逻辑应用进行批量数据传输）

  * [SELECT - ORDER BY 子句](https://docs.microsoft.com/sql/t-sql/queries/select-order-by-clause-transact-sql)

## <a name="connector-specific-details"></a>特定于连接器的详细信息

有关此连接器的触发器、操作和限制的技术信息，请参阅[连接器的参考页](https://docs.microsoft.com/connectors/sql/)。

## <a name="next-steps"></a>后续步骤

* 了解其他[适用于 Azure 逻辑应用的连接器](../connectors/apis-list.md)
