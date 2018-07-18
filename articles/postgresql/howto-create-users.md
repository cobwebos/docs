---
title: 在 Azure Database for PostgreSQL 服务器中创建用户
description: 本文介绍了如何创建新的用户帐户，用以与 Azure Database for PostgreSQL 服务器进行交互。
services: postgresql
author: jasonwhowell
ms.author: jasonh
editor: jasonwhowell
manager: jhubbard
ms.service: postgresql-database
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 87a73929185112190d5dd6698e014db225ebc08e
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
ms.locfileid: "29574180"
---
# <a name="create-users-in-azure-database-for-postgresql-server"></a>在 Azure Database for PostgreSQL 服务器中创建用户 
本文介绍了如何在 Azure Database for PostgreSQL 服务器中创建用户。

## <a name="the-server-admin-account"></a>服务器管理员帐户
首次创建 Azure Database for PostgreSQL 时，需要提供服务器管理员登录用户名和密码。 有关详细信息，可以参考[快速入门](quickstart-create-server-database-portal.md)来查看分步方法。 由于服务器管理员用户名是自定义名称，因此可以从 Azure 门户中找到所选的服务器管理员用户名。

创建 Azure Database for PostgreSQL 服务器时会创建所定义的 3 个默认角色。 可以运行以下命令来查看这些角色：`SELECT rolname FROM pg_roles;`
- azure_pg_admin
- azure_superuser
- 服务器管理员用户

服务器管理员用户是 azure_pg_admin 角色的成员。 但是，服务器管理员帐户不是 azure_superuser 角色的一部分。 由于此服务是托管的 PaaS 服务，因此只有 Microsoft 是超级用户角色的一部分。 

PostgreSQL 引擎使用权限来控制对数据库对象的访问权限，如 [PostgreSQL 产品文档](https://www.postgresql.org/docs/current/static/sql-createrole.html)中所述。 在 Azure Database for PostgreSQL 中，向服务器管理员用户授予了以下权限：LOGIN、NOSUPERUSER、INHERIT、CREATEDB、CREATEROLE、NOREPLICATION

可以使用服务器管理员用户帐户创建其他用户并向这些用户授予 azure_pg_admin 角色。 此外，还可以使用服务器管理员帐户创建只能访问各个数据库和架构的权限较低的用户和角色。

## <a name="how-to-create-additional-admin-users-in-azure-database-for-postgresql"></a>如何在 Azure Database for PostgreSQL 中创建其他管理员用户
1. 获取连接信息和管理员用户名。
   若要连接到数据库服务器，需提供完整的服务器名称和管理员登录凭据。 你可以在 Azure 门户的服务器“概述”页或“属性”页中轻松找到服务器名称和登录信息。 

2. 使用管理员帐户和密码连接到你的数据库服务器。 使用你喜欢的客户端工具，例如 pgAdmin 或 psql。
   如果不确定如何连接，请参阅[在 Cloud Shell 中使用 psql 连接到 PostgreSQL 数据库](./quickstart-create-server-database-portal.md#connect-to-the-postgresql-database-by-using-psql-in-cloud-shell)

3. 编辑并运行下面的 SQL 代码。 将占位符值 <new_user> 替换为新用户名，将占位符密码替换为你自己的强密码。 

   ```sql
   CREATE ROLE <new_user> WITH LOGIN NOSUPERUSER INHERIT CREATEDB CREATEROLE NOREPLICATION PASSWORD '<StrongPassword!>';
   
   GRANT azure_pg_admin TO <new_user>;
   ```

## <a name="how-to-create-database-users-in-azure-database-for-postgresql"></a>如何在 Azure Database for PostgreSQL 中创建数据库用户

1. 获取连接信息和管理员用户名。
   若要连接到数据库服务器，需提供完整的服务器名称和管理员登录凭据。 你可以在 Azure 门户的服务器“概述”页或“属性”页中轻松找到服务器名称和登录信息。 

2. 使用管理员帐户和密码连接到你的数据库服务器。 使用你喜欢的客户端工具，例如 pgAdmin 或 psql。

3. 编辑并运行下面的 SQL 代码。 将占位符值 `<db_user>` 替换为预期的新用户名，并将占位符值 `<newdb>` 替换为你自己的数据库名称。 将占位符密码替换为你自己的强密码。 

   为了举例说明，此 sql 代码语法将创建一个名为 testdb 的新数据库。 然后，它在 PostgreSQL 服务中创建一个新用户，并向该用户授予对新数据库的连接权限。 

   ```sql
   CREATE DATABASE <newdb>;
   
   CREATE ROLE <db_user> WITH LOGIN NOSUPERUSER INHERIT CREATEDB NOCREATEROLE NOREPLICATION PASSWORD '<StrongPassword!>';
   
   GRANT CONNECT ON DATABASE testdb TO <db_user>;
   ```

4. 使用管理员帐户，你可能需要授予其他权限来保护数据库中的对象。 有关数据库角色和权限的进一步详细信息，请参阅 [PostgreSQL 文档](https://www.postgresql.org/docs/current/static/ddl-priv.html)。 例如： 
   ```sql
   GRANT ALL PRIVILEGES ON DATABASE <newdb> TO <db_user>;
   ```

5. 指定选定的数据库并使用新用户名和密码，登录到服务器。 此示例显示了 psql 命令行。 使用此命令，会提示你输入用户名的密码。 替换你自己的服务器名称、数据库名称和用户名。

   ```azurecli-interactive
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=db_user@mydemoserver --dbname=newdb
   ```

## <a name="next-steps"></a>后续步骤
打开防火墙以允许连接到新用户计算机的 IP 地址：通过 [Azure CLI](howto-manage-firewall-using-cli.md) 或 [使用 Azure 门户创建和管理 Azure Database for PostgreSQL 防火墙规则](howto-manage-firewall-using-portal.md)。

有关用户帐户管理的详细信息，请参阅 PostgreSQL 产品文档来了解[数据库角色和权限](https://www.postgresql.org/docs/current/static/user-manag.html)、[GRANT 语法](https://www.postgresql.org/docs/current/static/sql-grant.html)和[权限](https://www.postgresql.org/docs/current/static/ddl-priv.html)。
