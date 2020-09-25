---
title: 连接到 SQL Server、Azure SQL 数据库或 Azure SQL 托管实例
description: 使用 Azure 逻辑应用自动完成本地或云中的 SQL 数据库的任务
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, jonfan, logicappspm
ms.topic: conceptual
ms.date: 06/06/2020
tags: connectors
ms.openlocfilehash: a50a171536d7f81de42da415960398d31ec64827
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91326773"
---
# <a name="automate-workflows-for-a-sql-database-by-using-azure-logic-apps"></a>使用 Azure 逻辑应用自动完成 SQL 数据库的工作流

本文介绍如何通过逻辑应用使用 SQL Server 连接器访问 SQL 数据库中的数据。 这样一来，便可通过创建逻辑应用来自动执行任务、进程或工作流，以便管理 SQL 数据和资源。 SQL Server 连接器适用于 [SQL Server](/sql/sql-server/sql-server-technical-documentation) 以及 [Azure SQL 数据库](../azure-sql/database/sql-database-paas-overview.md)和 [Azure SQL 托管实例](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)。

可以创建在受 SQL 数据库或其他系统（例如 Dynamics CRM Online）中的事件触发时运行的逻辑应用。 逻辑应用也可获取、插入或删除数据，以及运行 SQL 查询或存储过程。 例如，可以生成一个逻辑应用，让其自动检查 Dynamics CRM Online 中是否存在新记录，在存在新记录的情况下向 SQL 数据库添加相应的项，然后发送有关已添加项的电子邮件警报。

如果不熟悉逻辑应用，请查看[什么是 Azure 逻辑应用](../logic-apps/logic-apps-overview.md)和[快速入门：创建第一个逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。 有关特定于连接器的技术信息、限制和已知问题，请参阅 [SQL Server 连接器参考页](/connectors/sql/)。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有订阅，可以[注册免费的 Azure 帐户](https://azure.microsoft.com/free/)。

* 一个 [SQL Server 数据库](/sql/relational-databases/databases/create-a-database)、[Azure SQL 数据库](../azure-sql/database/single-database-create-quickstart.md)或 [Azure SQL 托管实例](../azure-sql/managed-instance/instance-create-quickstart.md)。

  表必须有数据，这样逻辑应用在调用操作时才能够返回结果。 如果你使用 Azure SQL 数据库，可以使用随附的示例数据库。

* SQL Server 名称、数据库名称、用户名和密码。 需要提供这些凭据才能授权应用访问 SQL Server。

  * 对于本地 SQL Server，可以在连接字符串中找到这些详细信息：

    `Server={your-server-address};Database={your-database-name};User Id={your-user-name};Password={your-password};`

  * 对于 Azure SQL 数据库，可以在连接字符串中找到这些详细信息。
  
    例如，若要在 Azure 门户中查找此字符串，请打开数据库。 在数据库菜单上，选择“连接字符串”或“属性”：

    `Server=tcp:{your-server-name}.database.windows.net,1433;Initial Catalog={your-database-name};Persist Security Info=False;User ID={your-user-name};Password={your-password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;`

<a name="multi-tenant-or-ise"></a>

* 根据逻辑应用是要在全局、多租户 Azure 还是 [integration service 环境中运行 (ISE) ](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)，以下是连接到本地 SQL Server 的其他要求：

  * 对于全局多租户 Azure 中连接到本地 SQL Server 的逻辑应用，你需要在本地计算机上安装[本地数据网关](../logic-apps/logic-apps-gateway-install.md)，并且需要具有[已在 Azure 中创建的数据网关资源](../logic-apps/logic-apps-gateway-connection.md)。

  * 对于连接到本地 SQL Server 并使用 Windows 身份验证的 ISE 中的逻辑应用，ISE 版本控制 SQL Server 连接器不支持 Windows 身份验证。 因此，你仍需要使用数据网关和非 ISE SQL Server 连接器。 对于其他身份验证类型，无需使用数据网关，而可以使用 ISE 版本控制连接器。

* 需在其中访问 SQL 数据库的逻辑应用。 若要通过 SQL 触发器启动逻辑应用，需要一个[空白逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

<a name="create-connection"></a>

## <a name="connect-to-your-database"></a>连接到数据库

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

现在，继续执行以下步骤：

* [连接到基于云的 Azure SQL 数据库或托管实例](#connect-azure-sql-db)
* [连接到本地 SQL Server](#connect-sql-server)

<a name="connect-azure-sql-db"></a>

### <a name="connect-to-azure-sql-database-or-managed-instance"></a>连接到 Azure SQL 数据库或托管实例

第一次添加 [SQL 触发器](#add-sql-trigger)或 [SQL 操作](#add-sql-action)时，如果尚未创建到数据库的连接，系统会提示你完成以下步骤：

1. 对于“身份验证类型”，选择所需的并已在 Azure SQL 数据库或 Azure SQL 托管实例中的数据库上启用的身份验证：

   | 身份验证 | 说明 |
   |----------------|-------------|
   | [**Azure AD 集成**](../azure-sql/database/authentication-aad-overview.md) | - 支持非 ISE 和 ISE SQL Server 连接器。 <p><p>- 需要 Azure Active Directory (Azure AD) 中有权访问你的数据库的一个有效标识。 <p>有关详细信息，请参阅以下主题： <p>- [Azure SQL 安全概述 - 身份验证](../azure-sql/database/security-overview.md#authentication) <br>- [授权数据库访问 Azure SQL - 身份验证和授权](../azure-sql/database/logins-create-manage.md#authentication-and-authorization) <br>- [Azure SQL - Azure AD 集成身份验证](../azure-sql/database/authentication-aad-overview.md) |
   | [**SQL Server 身份验证**](/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-sql-server-authentication) | - 支持非 ISE 和 ISE SQL Server 连接器。 <p><p>- 需要一个在你的数据库中创建并存储的有效用户名和强密码。 <p>有关详细信息，请参阅以下主题： <p>- [Azure SQL 安全概述 - 身份验证](../azure-sql/database/security-overview.md#authentication) <br>- [授权数据库访问 Azure SQL - 身份验证和授权](../azure-sql/database/logins-create-manage.md#authentication-and-authorization) |
   |||

   此示例将继续使用 **Azure AD 集成**：

   ![屏幕截图，显示已打开 "身份验证类型" 列表并选择 "Azure AD 集成" 的 "SQL Server" 连接窗口。](./media/connectors-create-api-sqlazure/select-azure-ad-authentication.png)

1. 在选择“Azure AD 集成”后，选择“登录”。 根据你使用的是 Azure SQL 数据库还是 Azure SQL 托管实例，选择用于身份验证的用户凭据。

1. 为你的数据库选择以下值：

   | 属性 | 必选 | 说明 |
   |----------|----------|-------------|
   | **服务器名称** | 是 | 你的 SQL Server 的地址，例如 `Fabrikam-Azure-SQL.database.windows.net` |
   | **数据库名称** | 是 | 你的 SQL 数据库的名称，例如 `Fabrikam-Azure-SQL-DB` |
   | **表名称** | 是 | 要使用的表，例如 `SalesLT.Customer` |
   ||||

   > [!TIP]
   > 可在数据库的连接字符串中找到此信息。 例如，在 Azure 门户中，找到并打开你的数据库。 在数据库菜单上，选择你可以在其中找到此字符串的“连接字符串”或“属性”：
   >
   > `Server=tcp:{your-server-address}.database.windows.net,1433;Initial Catalog={your-database-name};Persist Security Info=False;User ID={your-user-name};Password={your-password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;`

   此示例显示了这些值的可能外观：

   ![创建到 SQL 数据库的连接](./media/connectors-create-api-sqlazure/azure-sql-database-create-connection.png)

1. 现在，继续在[添加 SQL 触发器](#add-sql-trigger)或[添加 SQL 操作](#add-sql-action)中执行你尚未完成的步骤。

<a name="connect-sql-server"></a>

### <a name="connect-to-on-premises-sql-server"></a>连接到本地 SQL Server

第一次添加 [SQL 触发器](#add-sql-trigger)或 [SQL 操作](#add-sql-action)时，如果尚未创建到数据库的连接，系统会提示你完成以下步骤：

1. 对于需要本地数据网关的到本地 SQL Server 的连接，请确保[已完成这些先决条件](#multi-tenant-or-ise)。

   否则，当你创建连接时，你的数据网关资源不会显示在“连接网关”列表中。

1. 对于“身份验证类型”，请选择所需的且已在 SQL Server 上启用的身份验证：

   | 身份验证 | 说明 |
   |----------------|-------------|
   | [**Windows 身份验证**](/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-windows-authentication) | - 仅支持非 ISE SQL Server 连接器，此连接器需要以前在 Azure 中为你的连接创建的数据网关资源，不管你使用的是多租户 Azure 还是 ISE。 <p><p>- 需要有效的 Windows 用户名和密码，以便通过 Windows 帐户确认你的身份。 <p>有关详细信息，请参阅 [Windows 身份验证](/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-windows-authentication)。 |
   | [**SQL Server 身份验证**](/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-sql-server-authentication) | - 支持非 ISE 和 ISE SQL Server 连接器。 <p><p>- 需要一个在你的 SQL Server 中创建并存储的有效用户名和强密码。 <p>有关详细信息，请参阅 [SQL Server 身份验证](/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-sql-server-authentication)。 |
   |||

   此示例将继续使用 **Windows 身份验证**：

   ![选择要使用的身份验证类型](./media/connectors-create-api-sqlazure/select-windows-authentication.png)

1. 为你的 SQL 数据库选择或提供以下值：

   | 属性 | 必选 | 说明 |
   |----------|----------|-------------|
   | **SQL Server 名称** | 是 | 你的 SQL Server 的地址，例如 `Fabrikam-Azure-SQL.database.windows.net` |
   | **SQL 数据库名称** | 是 | 你的 SQL Server 数据库的名称，例如 `Fabrikam-Azure-SQL-DB` |
   | **用户名** | 是 | 你用于 SQL Server 和数据库的用户名 |
   | **密码** | 是 | 你用于 SQL Server 和数据库的密码 |
   | **订阅** |  是（对于 Windows 身份验证） | 你之前在 Azure 中创建的数据网关资源的 Azure 订阅 |
   | **连接网关** | 是（对于 Windows 身份验证） | 你之前在 Azure 中创建的数据网关资源的名称 <p><p>**提示**：如果网关未出现在列表中，请检查是否已经正确[设置网关](../logic-apps/logic-apps-gateway-connection.md)。 |
   |||

   > [!TIP]
   > 可在数据库的连接字符串中找到此信息：
   > 
   > * `Server={your-server-address}`
   > * `Database={your-database-name}`
   > * `User ID={your-user-name}`
   > * `Password={your-password}`

   此示例显示了这些值的可能外观：

   ![创建 SQL Server 连接的操作已完成](./media/connectors-create-api-sqlazure/sql-server-create-connection-complete.png)

1. 准备就绪后，选择“创建”。

1. 现在，继续在[添加 SQL 触发器](#add-sql-trigger)或[添加 SQL 操作](#add-sql-action)中执行你尚未完成的步骤。

<a name="add-sql-trigger"></a>

## <a name="add-a-sql-trigger"></a>添加 SQL 触发器

1. 在 [Azure 门户](https://portal.azure.com)或 Visual Studio 中，创建一个空白的逻辑应用，这会打开逻辑应用设计器。 此示例将继续使用 Azure 门户。

1. 在设计器上的搜索框中输入 `sql server`。 在触发器列表中，选择所需的 SQL 触发器。 此示例使用“创建项时”触发器。

   ![选择“创建项时”触发器](./media/connectors-create-api-sqlazure/select-sql-server-trigger.png)

1. 如果你是首次连接到 SQL 数据库，系统会提示你[立即创建 SQL 数据库连接](#create-connection)。 创建此连接后，可以继续执行下一步。

1. 在触发器中，指定触发器对表进行检查的时间间隔和频率。

1. 若要为此触发器添加其他可用属性，请打开“添加新参数”列表。

   此触发器仅从选定表返回一个行，不返回其他内容。 若要执行其他任务，请继续添加 [SQL 连接器操作](#add-sql-action)或[其他操作](../connectors/apis-list.md)，以便执行逻辑应用工作流中所需的下一个任务。
   
   例如，若要查看此行中的数据，可以添加其他操作（用于创建一个文件，其字段来自返回的行），然后发送电子邮件警报。 若要了解此连接器的其他可用操作，请参阅[连接器的参考页](/connectors/sql/)。

1. 在设计器工具栏上选择“保存”。

   尽管此步骤会在 Azure 中自动地实时启用并发布你的逻辑应用，但你的逻辑应用当前所采取的唯一操作是根据指定的时间间隔和频率检查你的数据库。

<a name="add-sql-action"></a>

## <a name="add-a-sql-action"></a>添加 SQL 操作

在此示例中，逻辑应用一开始使用[定期触发器](../connectors/connectors-native-recurrence.md)，然后调用一个从 SQL 数据库获取行的操作。

1. 在 [Azure 门户](https://portal.azure.com)或 Visual Studio 的逻辑应用设计器中打开你的逻辑应用。 此示例将继续使用 Azure 门户。

1. 在要添加 SQL 操作的触发器或操作下，选择“新建步骤”。

   ![向逻辑应用中添加操作](./media/connectors-create-api-sqlazure/select-new-step-logic-app.png)

   或者，若要在现有步骤之间添加操作，请将鼠标移到连接箭头上方。 选择出现的加号 ( **+** )，然后选择“添加操作”。

1. 在“选择操作”下的搜索框中输入 `sql server`。 从操作列表中，选择所需的 SQL 操作。 此示例使用“获取行”操作来获取单个记录。

   ![选择 SQL 的“获取行”操作](./media/connectors-create-api-sqlazure/select-sql-get-row-action.png)

1. 如果你是首次连接到 SQL 数据库，系统会提示你[立即创建 SQL 数据库连接](#create-connection)。 创建此连接后，可以继续执行下一步。

1. 选择“表名”，在此示例中为 `SalesLT.Customer`。 输入所需记录的**行 ID**。

   ![选择表名并指定行 ID](./media/connectors-create-api-sqlazure/specify-table-row-id.png)

   此操作仅从选定表返回一个行，不返回其他内容。 因此，若要查看此行中的数据，可以添加其他操作，以便创建一个文件（其中包含已返回行中的字段），然后将该文件存储在云存储帐户中。 若要了解此连接器的其他可用操作，请参阅[连接器的参考页](/connectors/sql/)。

1. 完成后，请在设计器工具栏上选择“保存”。

   此步骤自动在 Azure 中实时启用和发布逻辑应用。

## <a name="handle-bulk-data"></a>处理批量数据

有时，你必须处理大到连接器不能同时返回所有结果的结果集，或者你希望更好地控制结果集的大小和结构。 下面提供了一些可用于处理此类大型结果集的方法：

* 为了帮助你将结果作为较小的集进行管理，请启用“分页”。 有关详细信息，请参阅[使用分页获取批量数据、记录和项](../logic-apps/logic-apps-exceed-default-page-size-with-pagination.md)。

* 创建按所需方式组织结果的存储过程。

  获取或插入多个行时，逻辑应用可以在这些[限制](../logic-apps/logic-apps-limits-and-config.md)中使用 [*until loop*](../logic-apps/logic-apps-control-flow-loops.md#until-loop) 来循环访问这些行。 但是，当逻辑应用必须处理非常大的记录（例如，数千或数百万行）时，你希望将调用数据库的成本降到最低。

  若要按所需方式组织结果，可以创建在 SQL 实例中运行并使用 **SELECT - ORDER BY** 语句的[*存储过程*](/sql/relational-databases/stored-procedures/stored-procedures-database-engine)。 此解决方案可以更好地控制结果的大小和结构。 逻辑应用可以使用 SQL Server 连接器的“执行存储过程”操作调用存储过程。

  如需更多解决方案详细信息，请参阅以下文章：

  * [SQL Pagination for bulk data transfer with Logic Apps](https://social.technet.microsoft.com/wiki/contents/articles/40060.sql-pagination-for-bulk-data-transfer-with-logic-apps.aspx)（通过 SQL 分页使用逻辑应用进行批量数据传输）

  * [SELECT - ORDER BY 子句](/sql/t-sql/queries/select-order-by-clause-transact-sql)

### <a name="handle-dynamic-bulk-data"></a>处理动态批量数据

当使用 SQL Server 连接器调用存储过程时，返回的输出有时候是动态的。 在这种情况下，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com)的逻辑应用设计器中打开逻辑应用。

1. 通过执行测试运行来查看输出格式。 复制并保存示例输出。

1. 在设计器中，选择你调用存储过程的操作下的“新建步骤”。

1. 在“选择操作”下，查找并选择[**分析 JSON**](../logic-apps/logic-apps-perform-data-operations.md#parse-json-action) 操作。

1. 在“分析 JSON”操作中，选择“使用示例有效负载生成架构” 。

1. 在“输入或粘贴示例 JSON 有效负载”框中，粘贴示例输出，然后选择“完成”。

   > [!NOTE]
   > 如果出现逻辑应用无法生成架构的错误，请检查是否正确设置了示例输出的语法格式。 如果仍无法生成架构，请在“架构”框中手动输入架构。

1. 在设计器工具栏上选择“保存”。

1. 若要引用 JSON 内容属性，请在你要在其中引用这些属性的编辑框中单击，以便显示动态内容列表。 在列表中的[**分析 JSON**](../logic-apps/logic-apps-perform-data-operations.md#parse-json-action) 标题下，为你所需的 JSON 内容属性选择数据令牌。

## <a name="connector-specific-details"></a>特定于连接器的详细信息

有关此连接器的触发器、操作和限制的技术信息，请参阅[连接器的参考页](/connectors/sql/)，这是基于 Swagger 说明生成的。

## <a name="next-steps"></a>后续步骤

* 了解其他[适用于 Azure 逻辑应用的连接器](../connectors/apis-list.md)

