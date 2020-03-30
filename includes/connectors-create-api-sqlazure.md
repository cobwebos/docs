---
ms.service: logic-apps
author: ecfan
ms.author: estfan
ms.topic: include
ms.date: 11/08/2019
ms.openlocfilehash: ea0ae1b1527aa1f527c8ac8fbcd3b4e4f6b6fe2f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789184"
---
* 如果使用 Azure SQL 数据库，请按照[连接到 Azure SQL 数据库](#connect-azure-sql-db)中的步骤进行操作。

* 如果使用 SQL Server，请按照[连接到 SQL Server](#connect-sql-server) 中的步骤进行操作。

<a name="connect-azure-sql-db"></a>

### <a name="connect-to-azure-sql-database"></a>连接到 Azure SQL 数据库

当 SQL 触发器或操作提示您获得连接信息时，请按照以下步骤操作触发器和操作。

1. 对于**连接名称**，为连接创建名称。

1. 在**SQL 服务器名称**下，选择 Azure SQL 服务器。 当**SQL 数据库名称**列表出现时，选择数据库。 提供 Azure SQL 服务器的用户名和密码。

   您还可以在 SQL 数据库属性下的 Azure 门户或连接字符串中找到此信息：

   * `User ID=<your-user-name>`
   * `Password=<your-password>`

   ![创建与 Azure SQL 数据库的连接](./media/connectors-create-api-sqlazure/azure-sql-database-create-connection.png)

1. 完成操作后，选择“创建”****。

1. 创建连接后，继续[添加 SQL 触发器](#add-sql-trigger)或添加[SQL 操作](#add-sql-action)。

<a name="connect-sql-server"></a>

### <a name="connect-to-sql-server"></a>连接到 SQL Server

当 SQL 触发器或操作提示您获得连接信息时，请按照以下步骤操作触发器和操作。 对于要求在本地计算机上安装[本地数据网关](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-install)并[创建 Azure 数据网关资源](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-connection)的情况，请确保首先完成这些要求。 否则，创建连接时网关资源将不会显示在网关列表中。

此外，要在[集成服务环境 （ISE）](https://docs.microsoft.com/azure/logic-apps/connect-virtual-network-vnet-isolated-environment-overview)中使用 SQL Server 连接器的 Windows 身份验证，请使用连接器的非 ISE 版本和本地数据网关。 ISE 标记的版本不支持 Windows 身份验证。

1. 对于**连接名称**，为连接创建名称。

1. 在触发器或操作中，选择“通过本地数据网关连接”****，以便显示 SQL Server 选项。

1. 对于**SQL 服务器名称**和**SQL 数据库名称**，请提供 SQL 服务器的地址和数据库的名称。 对于**用户名和密码**，**Password**请提供服务器的用户名和密码。

   您还可以在连接字符串中找到此信息：

   * `Server=<your-server-address>`
   * `Database=<your-database-name>`
   * `User ID=<your-user-name>`
   * `Password=<your-password>`

   ![创建与 SQL 服务器的连接](./media/connectors-create-api-sqlazure/sql-server-create-connection.png)

1. 如果您的 SQL 服务器使用 Windows 或基本身份验证，请选择**身份验证类型**。

1. 在**网关**下，选择与以前创建的本地数据网关关联的 Azure 订阅，然后选择本地数据网关的名称。

   如果网关未出现在列表中，请检查是否已经正确[设置网关](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-connection)。

   ![创建已完成的 SQL 服务器连接](./media/connectors-create-api-sqlazure/sql-server-create-connection-complete.png)

1. 完成操作后，选择“创建”****。

1. 创建连接后，继续[添加 SQL 触发器](#add-sql-trigger)或[添加 SQL 操作](#add-sql-action)。
