---
title: 配置数据传入复制 - Azure Database for MySQL
description: 本文介绍如何为 Azure Database for MySQL 设置复制中数据。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 6/11/2020
ms.openlocfilehash: 7b66f227469328767f23c6858fda15803832704b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84737557"
---
# <a name="how-to-configure-azure-database-for-mysql-data-in-replication"></a>如何配置 Azure Database for MySQL 的数据传入复制

本文介绍如何通过配置主服务器和副本服务器在 Azure Database for MySQL 中设置[数据传入复制](concepts-data-in-replication.md)。 本文假设读者在 MySQL 服务器和数据库方面有一定的经验。

> [!NOTE]
> 无偏差通信
>
> Microsoft 支持多样化的包容性环境。 本文包含对单词 slave 的引用。 Microsoft 的[无偏差通信风格指南](https://github.com/MicrosoftDocs/microsoft-style-guide/blob/master/styleguide/bias-free-communication.md)将其视为排他性单词。 本文使用该单词旨在保持一致性，因为目前软件中使用的是该单词。 如果软件更新后删除了该单词，则本文也将更新以保持一致。
>

若要在 Azure Database for MySQL 服务中创建副本，[数据传入复制](concepts-data-in-replication.md)在本地、虚拟机（vm）或云数据库服务中同步主 MySQL server 中的数据。 复制中数据以基于二进制日志 (binlog) 文件位置的从本机到 MySQL 的复制为基础。 若要了解有关 binlog 复制的详细信息，请参阅 [MySQL binlog 复制概述](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html)。

在执行本文中的步骤之前，请查看数据传入复制的[限制和要求](concepts-data-in-replication.md#limitations-and-considerations)。

## <a name="create-a-mysql-server-to-be-used-as-replica"></a>创建将要用作副本的 MySQL 服务器

1. 创建新的 Azure Database for MySQL 服务器

   创建新的 MySQL 服务器（例如 “replica.mysql.database.azure.com”）。 请参阅[使用 Azure 门户创建 Azure Database for MySQL 服务器](quickstart-create-mysql-server-database-using-azure-portal.md)，了解如何创建服务器。 在数据传入复制中，此服务器为“副本”服务器。

   > [!IMPORTANT]
   > 必须在“常规用途”或“内存优化”定价层中创建 Azure Database for MySQL 服务器。
   > 

2. 创建相同的用户帐户和相应的特权

   用户帐户不会从主服务器复制到副本服务器。 如果打算为用户提供访问副本服务器的权限，则需在这个新创建的 Azure Database for MySQL 服务器上手动创建所有帐户和相应的特权。

3. 将主服务器的 IP 地址添加到副本的防火墙规则。 

   使用 [Azure 门户](howto-manage-firewall-using-portal.md)或 [Azure CLI](howto-manage-firewall-using-cli.md) 更新防火墙规则。

## <a name="configure-the-master-server"></a>配置主服务器
以下步骤准备并配置本地或虚拟机中托管的 MySQL 服务器或其他云提供程序托管的数据库服务，以便向内复制数据。 此服务器是“数据传入复制”中的“主”服务器。


1. 请先查看[主服务器要求](concepts-data-in-replication.md#requirements)，然后再继续。 

   例如，请确保主服务器允许端口 3306 上的入站和出站流量，并且主服务器具有公共 IP 地址  ，DNS 可公开访问或者具有完全限定的域名 (FQDN)。 
   
   通过尝试从其他计算机上托管的 MySQL 命令行或 Azure 门户中提供的[Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview)来连接到主服务器的连接。

2. 启用二进制日志记录

   运行以下命令以检查是否已在主服务器上启用了二进制日志记录： 

   ```sql
   SHOW VARIABLES LIKE 'log_bin';
   ```

   如果返回了值为“ON”的变量 [`log_bin`](https://dev.mysql.com/doc/refman/8.0/en/replication-options-binary-log.html#sysvar_log_bin)，则表示已在服务器上启用了二进制日志记录。 

   如果返回了值为“OFF”的 `log_bin`，请将 my.cnf 文件编辑为 `log_bin=ON` 以启用二进制日志记录，并重启服务器使更改生效。

3. 主服务器设置

   “数据传入复制”要求参数 `lower_case_table_names` 在主服务器与副本服务器之间保持一致。 在 Azure Database for MySQL 中，该参数默认为 1。 

   ```sql
   SET GLOBAL lower_case_table_names = 1;
   ```

4. 创建新的复制角色并设置权限

   在主服务器上创建一个配置有复制特权的用户帐户。 这可以通过 SQL 命令或 MySQL Workbench 之类的工具来完成。 考虑是否打算使用 SSL 进行复制，因为这需要在创建用户时指定。 请参阅 MySQL 文档以了解如何在主服务器上[添加用户帐户](https://dev.mysql.com/doc/refman/5.7/en/user-names.html)。 

   在以下命令中，创建的新复制角色能够从任何计算机访问主服务器，而不仅仅是从本身托管主服务器的计算机访问主服务器。 这可以通过在创建用户的命令中指定“syncuser@'%'”来完成。 请参阅 MySQL 文档，详细了解如何[指定帐户名称](https://dev.mysql.com/doc/refman/5.7/en/account-names.html)。

   **SQL 命令**

   *使用 SSL 复制*

   如果所有用户连接都要求 SSL，请使用以下命令来创建用户： 

   ```sql
   CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
   GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%' REQUIRE SSL;
   ```

   *不使用 SSL 复制*

   如果所有用户连接都不要求 SSL，请使用以下命令来创建用户：

   ```sql
   CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
   GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%';
   ```

   **MySQL Workbench**

   若要在 MySQL Workbench 中创建复制角色，请在“管理”面板中打开“用户和特权”面板。   然后单击“添加帐户”  。 
 
   ![用户和特权](./media/howto-data-in-replication/users_privileges.png)

   在“登录名称”字段中键入用户名。  

   ![同步用户](./media/howto-data-in-replication/syncuser.png)
 
   单击“管理角色”面板，然后从“全局特权”列表中选择“复制从属实例”。    然后单击“应用”  ，创建复制角色。

   ![复制从属实例](./media/howto-data-in-replication/replicationslave.png)


5. 将主服务器设置为只读模式

   在开始转储数据库之前，需将服务器置于只读模式。 在只读模式下，主服务器无法处理任何写入事务。 评估对业务的影响，根据需要在非高峰时间计划只读窗口。

   ```sql
   FLUSH TABLES WITH READ LOCK;
   SET GLOBAL read_only = ON;
   ```

6. 获取二进制日志文件名和偏移量

   运行 [`show master status`](https://dev.mysql.com/doc/refman/5.7/en/show-master-status.html) 命令，确定当前的二进制日志文件名和偏移量。
    
   ```sql
   show master status;
   ```
   结果应如下所示。 确保记下此二进制文件名，因为在后面的步骤中会用到它。

   ![主机状态结果](./media/howto-data-in-replication/masterstatus.png)
 
## <a name="dump-and-restore-master-server"></a>转储并还原主服务器

1. 从主服务器转储所有数据库

   可以使用 mysqldump 从主服务器转储数据库。 有关详细信息，请参阅[转储和还原](concepts-migrate-dump-restore.md)。 不需转储 MySQL 库和测试库。

2. 将主服务器设置为读/写模式

   转储数据库后，将 MySQL 主服务器改回读/写模式。

   ```sql
   SET GLOBAL read_only = OFF;
   UNLOCK TABLES;
   ```

3. 将转储文件还原到新服务器

   将转储文件还原到在 Azure Database for MySQL 服务中创建的服务器。 请参阅[转储和还原](concepts-migrate-dump-restore.md)，了解如何将转储文件还原到 MySQL 服务器。 如果转储文件较大，请将它上传到副本服务器所在区域的 Azure 中的虚拟机。 将它从虚拟机还原到 Azure Database for MySQL 服务器。

## <a name="link-master-and-replica-servers-to-start-data-in-replication"></a>链接主服务器和副本服务器以启动“数据传入复制”

1. 设置主服务器

   所有数据传入复制功能都是通过存储过程完成的。 可以在[数据传入复制存储过程](reference-data-in-stored-procedures.md)中找到所有过程。 这些存储过程可以在 MySQL shell 或 MySQL Workbench 中运行。 

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
> 如果主服务器托管在 Azure VM 中，请将“允许访问 Azure 服务”设置为“启用”，以允许主服务器和副本服务器相互通信。 从“连接安全性”选项可更改此设置  。 请参阅[使用门户管理防火墙规则](howto-manage-firewall-using-portal.md)获取详细信息。

   **示例**

   *使用 SSL 复制*

   运行以下 MySQL 命令创建变量 `@cert`： 

   ```sql
   SET @cert = '-----BEGIN CERTIFICATE-----
   PLACE YOUR PUBLIC KEY CERTIFICATE'`S CONTEXT HERE
   -----END CERTIFICATE-----'
   ```

   在域“companya.com”中托管的主服务器与 Azure Database for MySQL 中托管的副本服务器之间设置了使用 SSL 进行复制的功能。 将在副本上运行此存储过程。 

   ```sql
   CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mysql-bin.000002', 120, @cert);
   ```
   *不使用 SSL 复制*

   在域“companya.com”中托管的主服务器与 Azure Database for MySQL 中托管的副本服务器之间设置了不使用 SSL 进行复制的功能。 将在副本上运行此存储过程。

   ```sql
   CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mysql-bin.000002', 120, '');
   ```

1. 启动复制

   调用 `mysql.az_replication_start` 存储过程来启动复制。

   ```sql
   CALL mysql.az_replication_start;
   ```

1. 检查复制状态

   在副本服务器上调用 [`show slave status`](https://dev.mysql.com/doc/refman/5.7/en/show-slave-status.html) 命令查看复制状态。
    
   ```sql
   show slave status;
   ```

   如果 `Slave_IO_Running` 和 `Slave_SQL_Running` 状态为“yes”，并且 `Seconds_Behind_Master` 的值为“0”，则表示复制正常运行。 `Seconds_Behind_Master` 指示副本的陈旧状态。 如果其值不为“0”，则表示副本正在处理更新。 

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
