---
title: include 文件
description: include 文件
services: logic-apps
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.author: estfan
ms.custom: include file
ms.date: 05/15/2018
ms.openlocfilehash: aa1001661d8fe03855e1a28b882f674bee3606b2
ms.sourcegitcommit: 9dec0358e5da3ceb0d0e9e234615456c850550f6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2019
ms.locfileid: "72311979"
---
* 如果使用 Azure SQL 数据库，请按照[连接到 Azure SQL 数据库](#connect-azure-sql-db)中的步骤进行操作。

* 如果使用 SQL Server，请按照[连接到 SQL Server](#connect-sql-server) 中的步骤进行操作。

<a name="connect-azure-sql-db"></a>

### <a name="connect-to-azure-sql-database"></a>连接到 Azure SQL 数据库

当 SQL 触发器或操作提示你提供连接信息时，请按照以下步骤操作，这些步骤适用于触发器和操作。

1. 为 "**连接名称**" 创建连接的名称。

1. 在 " **SQL Server 名称**" 下，选择 Azure SQL 服务器。 出现 " **SQL 数据库名称**" 列表时，选择您的数据库。 提供 Azure SQL 服务器的用户名和密码。

   你还可以在 SQL 数据库属性下的 Azure 门户中或在连接字符串中找到此信息：

   * `User ID=<your-user-name>`
   * `Password=<your-password>`

   ![创建 Azure SQL 数据库连接](./media/connectors-create-api-sqlazure/azure-sql-database-create-connection.png)

1. 完成操作后，选择“创建”。

1. 创建连接后，请继续[添加 sql 触发器](#add-sql-trigger)或[添加 sql 操作](#add-sql-action)。

<a name="connect-sql-server"></a>

### <a name="connect-to-sql-server"></a>连接到 SQL Server

当 SQL 触发器或操作提示你提供连接信息时，请按照以下步骤操作，这些步骤适用于触发器和操作。 但是，在开始之前，请确保已[设置本地数据网关](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-connection)。 否则，你的网关将不会在创建连接时显示在 "网关" 列表中。

1. 为 "**连接名称**" 创建连接的名称。

1. 在触发器或操作中，选择“通过本地数据网关连接”，以便显示 SQL Server 选项。

1. 对于**sql Server 名称**和**sql 数据库名称**，请提供您的 sql server 的地址和数据库的名称。 对于 "**用户名**" 和 "**密码**"，请提供服务器的用户名和密码。

   你还可以在连接字符串中找到此信息：

   * `Server=<your-server-address>`
   * `Database=<your-database-name>`
   * `User ID=<your-user-name>`
   * `Password=<your-password>`

   ![创建 SQL Server 连接](./media/connectors-create-api-sqlazure/sql-server-create-connection.png)

1. 如果 SQL server 使用 Windows 或基本身份验证，请选择**身份验证类型**。

1. 在 "**网关**" 下，选择与以前创建的本地数据网关关联的 Azure 订阅，并选择本地数据网关的名称。

   如果网关未出现在列表中，请检查是否已经正确[设置网关](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-connection)。

   ![已完成创建 SQL Server 连接](./media/connectors-create-api-sqlazure/sql-server-create-connection-complete.png)

1. 完成操作后，选择“创建”。

1. 创建连接后，继续[添加 SQL 触发器](#add-sql-trigger)或[添加 SQL 操作](#add-sql-action)。
