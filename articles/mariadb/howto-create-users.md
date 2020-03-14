---
title: 创建用户-Azure Database for MariaDB
description: 本文介绍如何创建新的用户帐户，以与 Azure Database for MariaDB 服务器进行交互。
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: cbfcb097b4fda30bdeed940a5acb609b02f5d788
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79283350"
---
# <a name="create-users-in-azure-database-for-mariadb"></a>在 Azure Database for MariaDB 中创建用户 
本文介绍如何在 Azure Database for MariaDB 中创建用户。

首次创建 Azure Database for MariaDB 时，需要提供服务器管理员登录用户名和密码。 有关详细信息，可以参考[快速入门](quickstart-create-mariadb-server-database-using-azure-portal.md)。 你可以从 Azure 门户中找到你的服务器管理员登录用户名。

服务器管理员用户可以获得服务器的某些权限，如下所列：SELECT、INSERT、UPDATE、DELETE、CREATE、DROP、RELOAD、PROCESS、REFERENCES、INDEX、ALTER、SHOW DATABASES、CREATE TEMPORARY TABLES、LOCK TABLES、EXECUTE、REPLICATION SLAVE、REPLICATION CLIENT、CREATE VIEW、SHOW VIEW、CREATE ROUTINE、ALTER ROUTINE、CREATE USER、EVENT、TRIGGER

创建 Azure Database for MariaDB 服务器后，可以使用第一个服务器管理员用户帐户来创建其他用户，并授予这些用户管理员访问权限。 此外，服务器管理员帐户还可以用于创建只能访问各个数据库架构的权限较低的用户。

## <a name="create-additional-admin-users"></a>创建其他管理员用户
1. 获取连接信息和管理员用户名。
   若要连接到数据库服务器，需提供完整的服务器名称和管理员登录凭据。 你可以在 Azure 门户的服务器“概述”页或“属性”页中轻松找到服务器名称和登录信息。 

2. 使用管理员帐户和密码连接到你的数据库服务器。 使用你的首选客户端工具，如 MySQL Workbench、mysql.exe、HeidiSQL 或其他工具。 
   如果你不确定如何连接，请参阅[使用 MySQL Workbench 连接和查询数据](./connect-workbench.md)

3. 编辑并运行下面的 SQL 代码。 将占位符值 `new_master_user` 替换为你的新用户名。 此语法会将所有数据库架构 ( *.* ) 上列出的权限授予该用户名（本示例中的 new_master_user）。 

   ```sql
   CREATE USER 'new_master_user'@'%' IDENTIFIED BY 'StrongPassword!';
   
   GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, RELOAD, PROCESS, REFERENCES, INDEX, ALTER, SHOW DATABASES, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, REPLICATION SLAVE, REPLICATION CLIENT, CREATE VIEW, SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE, CREATE USER, EVENT, TRIGGER ON *.* TO 'new_master_user'@'%' WITH GRANT OPTION; 
   
   FLUSH PRIVILEGES;
   ```

4. 验证授予 
   ```sql
   USE sys;
   
   SHOW GRANTS FOR 'new_master_user'@'%';
   ```

## <a name="create-database-users"></a>创建数据库用户

1. 获取连接信息和管理员用户名。
   若要连接到数据库服务器，需提供完整的服务器名称和管理员登录凭据。 你可以在 Azure 门户的服务器“概述”页或“属性”页中轻松找到服务器名称和登录信息。 

2. 使用管理员帐户和密码连接到你的数据库服务器。 使用你的首选客户端工具，如 MySQL Workbench、mysql.exe、HeidiSQL 或其他工具。 
   如果你不确定如何连接，请参阅[使用 MySQL Workbench 连接和查询数据](./connect-workbench.md)

3. 编辑并运行下面的 SQL 代码。 将占位符值 `db_user` 替换为预期的新用户名，并将占位符值 `testdb` 替换为你自己的数据库名称。

   出于举例的目的，此 sql 代码语法将创建一个名为 testdb 的新数据库。 然后，它在 Azure Database for MariaDB 服务中创建新用户，并将所有权限授予该用户的新数据库架构 (testdb.\*)。 

   ```sql
   CREATE DATABASE testdb;
   
   CREATE USER 'db_user'@'%' IDENTIFIED BY 'StrongPassword!';
   
   GRANT ALL PRIVILEGES ON testdb . * TO 'db_user'@'%';
   
   FLUSH PRIVILEGES;
   ```

4. 验证数据库中的授予。
   ```sql
   USE testdb;
   
   SHOW GRANTS FOR 'db_user'@'%';
   ```

5. 使用新用户名和密码登录到服务器，指定选定的数据库。 此示例显示了 mysql 命令行。 使用此命令，会提示你输入用户名的密码。 替换你自己的服务器名称、数据库名称和用户名。

   ```bash
   mysql --host mydemoserver.mariadb.database.azure.com --database testdb --user db_user@mydemoserver -p
   ```
   有关用户帐户管理的详细信息，请参阅 MariaDB 文档，了解[用户帐户管理](https://mariadb.com/kb/en/library/user-account-management/)、[GRANT 语法](https://mariadb.com/kb/en/library/grant/)和[权限](https://mariadb.com/kb/en/library/grant/#privilege-levels)。

## <a name="next-steps"></a>后续步骤
打开防火墙以允许连接到新用户计算机的 IP 地址：[使用 Azure 门户创建和管理 Azure Database for MariaDB 防火墙规则](howto-manage-firewall-portal.md)  

<!--or [Azure CLI](howto-manage-firewall-using-cli.md).-->
