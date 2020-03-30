---
title: 配置数据内复制 - MySQL 的 Azure 数据库
description: 本文介绍如何为 Azure Database for MySQL 设置复制中数据。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/27/2020
ms.openlocfilehash: 2148ce41267627d9d6e0437897a99a8dbdbe0746
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2020
ms.locfileid: "80382760"
---
# <a name="how-to-configure-azure-database-for-mysql-data-in-replication"></a>如何配置 Azure Database for MySQL 的复制中数据

本文介绍如何通过配置主服务器和副本服务器在 Azure Database for MySQL 服务中设置“数据传入复制”。 凭借“数据传入复制”，可以将在本地或虚拟机中运行的主 MySQL 服务器或其他云提供程序托管的数据库服务中的数据同步到 Azure Database for MySQL 服务中的副本。 

本文假设读者在 MySQL 服务器和数据库方面有一定的经验。

在执行本文中的步骤之前，请查看数据内复制[的限制和要求](concepts-data-in-replication.md#limitations-and-considerations)。

## <a name="create-a-mysql-server-to-be-used-as-replica"></a>创建用作副本的 MySQL 服务器

1. 创建新的 Azure Database for MySQL 服务器

   创建新的 MySQL 服务器（例如 “replica.mysql.database.azure.com”）。 参阅[使用 Azure 门户创建 Azure Database for MySQL 服务器](quickstart-create-mysql-server-database-using-azure-portal.md)了解如何创建服务器。 此服务器是复制中数据中的“副本”服务器。

   > [!IMPORTANT]
   > 必须在“常规用途”或“内存优化”定价层中创建 Azure Database for MySQL 服务器。
   > 

2. 创建相同的用户帐户和对应的特权

   用户帐户不会从主服务器复制到副本服务器。 如果打算为用户提供副本服务器的访问权限，需要在此新建的 Azure Database for MySQL 服务器上创建所有帐户和对应的特权。

3. 将主服务器的 IP 地址添加到副本的防火墙规则中。 

   使用 [Azure 门户](howto-manage-firewall-using-portal.md)或 [Azure CLI](howto-manage-firewall-using-cli.md) 更新防火墙规则。

## <a name="configure-the-master-server"></a>配置主服务器
以下步骤准备并配置本地或虚拟机中托管的 MySQL 服务器或其他云提供程序托管的数据库服务，以便向内复制数据。 此服务器是“数据传入复制”中的“主”服务器。


1. 在继续操作之前，请查看[主服务器要求](concepts-data-in-replication.md#requirements)。 

   例如，确保主服务器允许端口 3306 上的入站和出站流量，并且主服务器具有公共**IP 地址**、DNS 是可公开访问的，或者具有完全限定的域名 （FQDN）。 
   
   尝试从工具（如在另一台计算机上托管的 MySQL 命令行或 Azure 门户中可用的[Azure 云外壳](https://docs.microsoft.com/azure/cloud-shell/overview)）进行连接，以测试与主服务器的连接 

2. 启用二进制日志记录

   运行以下命令以检查是否已在主服务器上启用了二进制日志记录： 

   ```sql
   SHOW VARIABLES LIKE 'log_bin';
   ```

   如果变量[`log_bin`](https://dev.mysql.com/doc/refman/8.0/en/replication-options-binary-log.html#sysvar_log_bin)返回的值"ON"，则在服务器上启用二进制日志记录。 

   如果`log_bin`返回的值"OFF"，请通过编辑 my.cnf 文件打开二进制日志记录，`log_bin=ON`以便重新启动服务器，使更改生效。

3. 主服务器设置

   “数据传入复制”要求参数 `lower_case_table_names` 在主服务器与副本服务器之间保持一致。 在 Azure Database for MySQL 中，此参数默认为 1。 

   ```sql
   SET GLOBAL lower_case_table_names = 1;
   ```

4. 创建新的复制角色并设置权限

   在主服务器上创建一个配置有复制特权的用户帐户。 可以通过 SQL 命令或 MySQL Workbench 等工具实现此目的。 考虑是否打算使用 SSL 进行复制，因为这需要在创建用户时指定。 请参阅 MySQL 文档以了解如何在主服务器上[添加用户帐户](https://dev.mysql.com/doc/refman/5.7/en/adding-users.html)。 

   在以下命令中，创建的新复制角色能够从任何计算机访问主服务器，而不仅仅是从本身托管主服务器的计算机访问主服务器。 可以通过在 create user 命令中指定“syncuser \@\ '%”来实现此目的。 请参阅 MySQL 文档，详细了解如何[指定帐户名称](https://dev.mysql.com/doc/refman/5.7/en/account-names.html)。

   **SQL 命令**

   *使用 SSL 复制*

   若要对所有用户连接要求使用 SSL，请使用以下命令创建用户： 

   ```sql
   CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
   GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%' REQUIRE SSL;
   ```

   *不使用 SSL 复制*

   若不要求对所有连接使用 SSL，请使用以下命令创建用户：

   ```sql
   CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
   GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%';
   ```

   **MySQL Workbench**

   若要在 MySQL Workbench 中创建复制角色，请从“管理”面板打开“用户和特权”面板。******** 然后单击“添加帐户”****。 
 
   ![用户和特权](./media/howto-data-in-replication/users_privileges.png)

   在“登录名”字段中键入用户名。**** 

   ![同步用户](./media/howto-data-in-replication/syncuser.png)
 
   单击“管理角色”面板，然后从“全局特权”列表中选择“复制从属角色”。************ 然后单击“应用”创建复制角色。****

   ![复制从属角色](./media/howto-data-in-replication/replicationslave.png)


5. 将主服务器设置为只读模式

   在开始转储数据库之前，需将服务器置于只读模式。 在只读模式下，主服务器无法处理任何写入事务。 请评估此设置对业务的影响，并根据需要将只读时段安排在非高峰时间。

   ```sql
   FLUSH TABLES WITH READ LOCK;
   SET GLOBAL read_only = ON;
   ```

6. 获取二进制日志文件名和偏移量

   运行该[`show master status`](https://dev.mysql.com/doc/refman/5.7/en/show-master-status.html)命令以确定当前二进制日志文件名称和偏移量。
    
   ```sql
   show master status;
   ```
   结果应如下所示。 请务必记下二进制文件名，因为需要在后续步骤中用到。

   ![主机状态结果](./media/howto-data-in-replication/masterstatus.png)
 
## <a name="dump-and-restore-master-server"></a>转储并还原主服务器

1. 从主服务器转储所有数据库

   可以使用 mysqldump 从主服务器转储数据库。 有关详细信息，请参阅[转储和还原](concepts-migrate-dump-restore.md)。 不需要转储 MySQL 库和测试库。

2. 将主服务器设置为读/写模式

   转储数据库后，将 MySQL 主服务器改回读/写模式。

   ```sql
   SET GLOBAL read_only = OFF;
   UNLOCK TABLES;
   ```

3. 将转储文件还原到新服务器

   将转储文件还原到 Azure Database for MySQL 服务中创建的服务器。 请参阅[转储和还原](concepts-migrate-dump-restore.md)，了解如何将转储文件还原到 MySQL 服务器。 如果转储文件较大，请将其上传到 Azure 中副本服务器所在的同一区域内的某个虚拟机。 将转储文件从虚拟机还原到 Azure Database for MySQL 服务器。

## <a name="link-master-and-replica-servers-to-start-data-in-replication"></a>链接主服务器和副本服务器以启动“数据传入复制”

1. 设置主服务器

   所有复制中数据函数均由存储过程执行。 可以在[复制中数据存储过程](reference-data-in-stored-procedures.md)中找到所有过程。 可以在 MySQL shell 或 MySQL Workbench 中运行存储过程。 

   若要链接两个服务器并启动复制，请在 Azure DB for MySQL 服务中登录到目标副本服务器，并将外部实例设置为主服务器。 为此，可在 Azure DB for MySQL 服务器上使用 `mysql.az_replication_change_master` 存储过程。

   ```sql
   CALL mysql.az_replication_change_master('<master_host>', '<master_user>', '<master_password>', 3306, '<master_log_file>', <master_log_pos>, '<master_ssl_ca>');
   ```

   - master_host：主服务器的主机名
   - master_user：主服务器的用户名
   - master_password：主服务器的密码
   - master_log_file：正在运行的 `show master status` 中的二进制日志文件名
   - master_log_pos：正在运行的 `show master status` 中的二进制日志位置
   - master_ssl_ca：CA 证书的上下文。 如果不使用 SSL，请传入空字符串。
       - 建议以变量形式传入此参数。 有关详细信息，请参阅以下示例。

> [!NOTE]
> 如果主服务器托管在 Azure VM 中，请将“允许访问 Azure 服务”设置为“启用”，以允许主服务器和副本服务器相互通信。 从“连接安全性”选项可更改此设置****。 请参阅[使用门户管理防火墙规则](howto-manage-firewall-using-portal.md)获取详细信息。

   **示例**

   *使用 SSL 复制*

   运行以下 MySQL 命令创建变量 `@cert`： 

   ```sql
   SET @cert = '-----BEGIN CERTIFICATE-----
   PLACE YOUR PUBLIC KEY CERTIFICATE'`S CONTEXT HERE
   -----END CERTIFICATE-----'
   ```

   使用 SSL 进行复制是在托管在域"companya.com"的主服务器和 MySQL Azure 数据库中托管的副本服务器之间设置的。 将在副本上运行此存储过程。 

   ```sql
   CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mysql-bin.000002', 120, @cert);
   ```
   *不使用 SSL 复制*

   在没有 SSL 的复制在托管在域"companya.com"的主服务器和 MySQL Azure 数据库中托管的副本服务器之间设置。 将在副本上运行此存储过程。

   ```sql
   CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mysql-bin.000002', 120, '');
   ```

1. 启动复制

   调用 `mysql.az_replication_start` 存储过程来启动复制。

   ```sql
   CALL mysql.az_replication_start;
   ```

1. 检查复制状态

   调用[`show slave status`](https://dev.mysql.com/doc/refman/5.7/en/show-slave-status.html)副本服务器上的命令以查看复制状态。
    
   ```sql
   show slave status;
   ```

   如果 和`Slave_IO_Running``Slave_SQL_Running`的状态为"是"，值`Seconds_Behind_Master`为"0"，则复制工作良好。 `Seconds_Behind_Master` 指示副本的陈旧状态。 如果其值不为“0”，则表示副本正在处理更新。 

## <a name="other-stored-procedures"></a>其他存储过程

### <a name="stop-replication"></a>停止复制

若要停止主服务器与副本服务器之间的复制，请使用以下存储过程：

```sql
CALL mysql.az_replication_stop;
```

### <a name="remove-replication-relationship"></a>删除复制关系

若要删除主服务器与副本服务器之间的关系，请使用以下存储过程：

```sql
CALL mysql.az_replication_remove_master;
```

### <a name="skip-replication-error"></a>跳过复制错误

若要跳过复制错误并允许复制继续，请使用以下存储过程：
    
```sql
CALL mysql.az_replication_skip_counter;
```

## <a name="next-steps"></a>后续步骤
- 详细了解 Azure Database for MySQL 的[向内复制数据](concepts-data-in-replication.md)。 
