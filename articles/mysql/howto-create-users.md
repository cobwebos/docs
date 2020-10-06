---
title: 创建数据库和用户-Azure Database for MySQL
description: 本文介绍如何创建新的用户帐户以与 Azure Database for MySQL 服务器进行交互。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: how-to
ms.date: 10/1/2020
ms.openlocfilehash: 3e1f24b3ae6133241660751293f52fec63dfbe73
ms.sourcegitcommit: d9ba60f15aa6eafc3c5ae8d592bacaf21d97a871
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2020
ms.locfileid: "91766881"
---
# <a name="create-databases-and-users-in-azure-database-for-mysql"></a>在 Azure Database for MySQL 中创建数据库和用户

[!INCLUDE[applies-to-single-flexible-server](includes/applies-to-single-flexible-server.md)]

本文介绍如何在 Azure Database for MySQL 中创建用户。

> [!NOTE]
> **无偏差通信**
>
> Microsoft 支持多样化的包容性环境。 本文包含对单词 slave 的引用。 Microsoft 的[无偏差通信风格指南](https://github.com/MicrosoftDocs/microsoft-style-guide/blob/master/styleguide/bias-free-communication.md)将其视为排他性单词。 本文中使用的词是为了保持一致，因为它是当前显示在软件中的单词。 如果软件更新后删除了该单词，则本文也将更新以保持一致。
>

首次创建 Azure Database for MySQL 服务器时，你提供了服务器管理员用户名和密码。 有关详细信息，请参阅此 [快速入门](quickstart-create-mysql-server-database-using-azure-portal.md)。 可以在 Azure 门户中确定服务器管理员的用户名。

服务器管理员用户具有以下权限： 

   选择、插入、更新、删除、创建、放置、重载、处理、引用、索引、更改、显示数据库、创建临时表、锁定表、执行、复制从属、复制客户端、创建视图、显示视图、创建例程、更改例程、创建用户、事件、触发器


创建 Azure Database for MySQL 服务器后，您可以使用第一个服务器管理员帐户来创建其他用户并授予其管理员访问权限。 你还可以使用服务器管理员帐户来创建有权访问单个数据库架构的更少权限的用户。

> [!NOTE]
> 不支持超级权限和 DBA 角色。 请在“限制”一文中查看[权限](concepts-limits.md#privileges--data-manipulation-support)，以了解服务中不支持的权限。
>
> `validate_password`服务不支持类似于和的密码插件 `caching_sha2_password` 。


## <a name="to-create-a-database-with-a-non-admin-user-in-azure-database-for-mysql"></a>使用 Azure Database for MySQL 中的非管理员用户创建数据库

1. 获取连接信息和管理员用户名。
   若要连接到数据库服务器，需提供完整的服务器名称和管理员登录凭据。 您可以在服务器 " **概述** " 页或 Azure 门户的 " **属性** " 页中轻松找到服务器名称和登录信息。

2. 使用管理员帐户和密码连接到你的数据库服务器。 使用你喜欢的客户端工具（如 MySQL 工作台、mysql.exe 或 HeidiSQL）。
   
   如果你不确定如何连接，请参阅 [连接和查询单一服务器的数据](./connect-workbench.md) 或 [连接和查询数据以用于灵活的服务器](./flexible-server/connect-workbench.md)。

3. 编辑并运行下面的 SQL 代码。 将占位符值替换 `db_user` 为所需的新用户名。 将占位符值替换 `testdb` 为你的数据库名称。

   此 SQL 代码创建名为 testdb 的新数据库。 然后，它在 MySQL 服务中创建一个新用户，并为新的数据库架构 (testdb 授予所有权限。 \* 向该用户) 。

   ```sql
   CREATE DATABASE testdb;

   CREATE USER 'db_user'@'%' IDENTIFIED BY 'StrongPassword!';

   GRANT ALL PRIVILEGES ON testdb . * TO 'db_user'@'%';

   FLUSH PRIVILEGES;
   ```

4. 验证数据库中的授予：

   ```sql
   USE testdb;

   SHOW GRANTS FOR 'db_user'@'%';
   ```

5. 登录到服务器，指定指定的数据库并使用新用户名和密码。 此示例显示了 mysql 命令行。 使用此命令时，系统将提示你输入用户的密码。 使用自己的服务器名称、数据库名称和用户名。

   # <a name="single-server"></a>[单一服务器](#tab/single-server)

   ```azurecli-interactive
   mysql --host mydemoserver.mysql.database.azure.com --database testdb --user db_user@mydemoserver -p
   ```
   # <a name="flexible-server"></a>[灵活服务器](#tab/flexible-server)

   ```azurecli-interactive
   mysql --host mydemoserver.mysql.database.azure.com --database testdb --user db_user -p
   ```
 ---

## <a name="to-create-additional-admin-users-in-azure-database-for-mysql"></a>在 Azure Database for MySQL 中创建其他管理员用户

1. 获取连接信息和管理员用户名。
   若要连接到数据库服务器，需提供完整的服务器名称和管理员登录凭据。 您可以在服务器 " **概述** " 页或 Azure 门户的 " **属性** " 页中轻松找到服务器名称和登录信息。

2. 使用管理员帐户和密码连接到你的数据库服务器。 使用你喜欢的客户端工具（如 MySQL 工作台、mysql.exe 或 HeidiSQL）。
   
   如果你不确定如何连接，请参阅 [使用 MySQL 工作台来连接和查询数据](./connect-workbench.md)。

3. 编辑并运行下面的 SQL 代码。 将占位符值替换 `new_master_user` 为新的用户名。 此语法将所有数据库架构上列出的权限授予 (*。* `new_master_user` 在本示例) 中 () 用户。

   ```sql
   CREATE USER 'new_master_user'@'%' IDENTIFIED BY 'StrongPassword!';

   GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, RELOAD, PROCESS, REFERENCES, INDEX, ALTER, SHOW DATABASES, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, REPLICATION SLAVE, REPLICATION CLIENT, CREATE VIEW, SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE, CREATE USER, EVENT, TRIGGER ON *.* TO 'new_master_user'@'%' WITH GRANT OPTION;

   FLUSH PRIVILEGES;
   ```

4. 验证授予：

   ```sql
   USE sys;

   SHOW GRANTS FOR 'new_master_user'@'%';
   ```

## <a name="azure_superuser"></a>azure_superuser

所有 Azure Database for MySQL 服务器都是使用名为 "azure_superuser" 的用户创建的。 这是由 Microsoft 创建的系统帐户，用于管理服务器以执行监视、备份和其他定期维护。 待命工程师还可以使用此帐户在使用证书身份验证的情况下访问服务器，并且必须使用实时 (JIT) 过程请求访问。

## <a name="next-steps"></a>后续步骤

针对新用户计算机的 IP 地址打开防火墙，使其能够连接：
- [在单一服务器上创建和管理防火墙规则](howto-manage-firewall-using-portal.md) 
- [ 在灵活的服务器上创建和管理防火墙规则](flexible-server/how-to-connect-tls-ssl.md)

有关用户帐户管理的详细信息，请参阅用于 [用户帐户管理](https://dev.mysql.com/doc/refman/5.7/en/access-control.html)的 MySQL 产品文档、 [GRANT 语法](https://dev.mysql.com/doc/refman/5.7/en/grant.html)和 [权限](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html)。
