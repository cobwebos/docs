---
title: include 文件
description: include 文件
services: logic-apps
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.date: 05/15/2018
ms.author: estfan
ms.custom: include file
ms.openlocfilehash: 4ffda692da0ab7b63f7376c36dfab0bec914e334
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2018
ms.locfileid: "37138059"
---
* 如果使用 Azure SQL 数据库，请按照[连接到 Azure SQL 数据库](#connect-azure-sql-db)中的步骤进行操作。 

* 如果使用 SQL Server，请按照[连接到 SQL Server](#connect-sql-server) 中的步骤进行操作。

<a name="connect-azure-sql-db"></a>

### <a name="connect-to-azure-sql-database"></a>连接到 Azure SQL 数据库

1. SQL 触发器或操作提示你输入连接信息时，请按照下列步骤操作：

   1. 创建连接名称。

   2. 选择 SQL server，然后选择数据库。 

      仅在选择 SQL server 后，数据库列表才会出现。
 
   3. 提供服务器的用户名和密码。

      可在 Azure 门户中的 SQL 数据库属性下或连接字符串中找到该信息： 
      
      "User ID=<*yourUserName*>"
      <br>
      "Password=<*yourPassword*>"

   此示例显示了触发器的连接信息，但这些步骤同样适用于操作。

   ![创建 Azure SQL 数据库连接](./media/connectors-create-api-sqlazure/azure-sql-database-create-connection.png)
   <br>
   星号 (*) 表示必填值。

   | 属性 | 值 | 详细信息 | 
   |----------|-------|---------| 
   | 连接名称 | <my-sql-connection> | 连接名称 | 
   | SQL Server 名称 | <my-sql-server> | SQL Server 名称 |
   | SQL 数据库名 | <my-sql-database>  | SQL 数据库名称 | 
   | 用户名 | <my-sql-username> | 用于访问数据库的用户名称 |
   | 密码 | <my-sql-password> | 用于访问数据库的密码 | 
   |||| 

2. 完成后，选择“创建”。

3. 创建连接后，继续[添加 SQL 触发器](#add-sql-trigger)或[添加 SQL 操作](#add-sql-action)。

<a name="connect-sql-server"></a>

### <a name="connect-to-sql-server"></a>连接到 SQL Server

选择网关前，请确保已经[设置数据网关](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-connection)。 这样一来，创建连接时，网关会显示在网关列表中。

1. SQL 触发器或操作提示你输入连接信息时，请按照下列步骤操作：

   1. 在触发器或操作中，选择“通过本地数据网关连接”，以便显示 SQL Server 选项。

   2. 创建连接名称。

   3. 提供 SQL Server 地址，然后提供数据库名称。
   
      可在连接字符串中找到该信息： 
      
      * "Server=<*yourServerAddress*>"
      * "Database=<*yourDatabaseName*>"

   4. 提供服务器的用户名和密码。

      可在连接字符串中找到该信息： 
      
      * "User ID=<*yourUserName*>"
      * "Password=<*yourPassword*>"

   5. 如果 SQL Server 使用 Windows 或基本身份验证，选择身份验证类型。

   6. 选择之前创建的本地数据网关的名称。
   
      如果网关未出现在列表中，请检查是否已经正确[设置网关](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-connection)。

   此示例显示了触发器的连接信息，但这些步骤同样适用于操作。

   ![创建 SQL Server 连接](./media/connectors-create-api-sqlazure/sql-server-create-connection.png)
   <br>
   星号 (*) 表示必填值。

   | 属性 | 值 | 详细信息 | 
   |----------|-------|---------| 
   | 通过本地网关连接 | 首先为 SQL Server 设置选择该选项。 | | 
   | 连接名称 | <my-sql-connection> | 连接名称 | 
   | SQL Server 名称 | <my-sql-server> | SQL Server 名称 |
   | SQL 数据库名 | <my-sql-database>  | SQL 数据库名称 |
   | 用户名 | <my-sql-username> | 用于访问数据库的用户名称 |
   | 密码 | <my-sql-password> | 用于访问数据库的密码 | 
   | 身份验证类型 | Windows 或基本 | 可选：SQL server 的身份验证类型 | 
   | 网关 | <my-data-gateway> | 本地数据网关的名称 | 
   |||| 

2. 完成后，选择“创建”。 

3. 创建连接后，继续[添加 SQL 触发器](#add-sql-trigger)或[添加 SQL 操作](#add-sql-action)。
