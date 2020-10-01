---
title: 配置数据传入复制 - Azure Database for MariaDB
description: 本文介绍如何在 Azure Database for MariaDB 中设置数据传入复制。
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: how-to
ms.date: 9/29/2020
ms.openlocfilehash: 2de6b6311a1a5d452907b8c4b6a2ffeb9c0e133e
ms.sourcegitcommit: ffa7a269177ea3c9dcefd1dea18ccb6a87c03b70
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91598190"
---
# <a name="configure-data-in-replication-in-azure-database-for-mariadb"></a>在 Azure Database for MariaDB 中配置数据传入复制

本文介绍如何通过配置源服务器和副本服务器在 Azure Database for MariaDB 中设置 [数据传入复制](concepts-data-in-replication.md) 。 本文假设读者在 MariaDB 服务器和数据库方面有一定的经验。

若要在 Azure Database for MariaDB 服务中创建副本， [数据传入复制](concepts-data-in-replication.md) 在虚拟机 (vm) 或在云数据库服务中同步源 MariaDB 服务器中的数据。 数据传入复制功能依靠的是基于二进制日志 (binlog) 文件位置、从本机到 MariaDB 的复制。 要详细了解 binlog 复制，请参阅 [binlog 复制概述](https://mariadb.com/kb/en/library/replication-overview/)。

在执行本文中的步骤之前，请查看数据传入复制的[限制和要求](concepts-data-in-replication.md#limitations-and-considerations)。

> [!NOTE]
> 如果源服务器是版本10.2 或更高版本，我们建议使用 [全局事务 ID](https://mariadb.com/kb/en/library/gtid/)设置数据传入复制。


## <a name="create-a-mariadb-server-to-use-as-a-replica"></a>创建用作副本的 MariaDB 服务器

1. 创建新的 Azure Database for MariaDB server (例如，replica.mariadb.database.azure.com) 。 在数据传入复制中，该服务器为副本服务器。

    若要了解如何创建服务器，请参阅[使用 Azure 门户创建 Azure Database for MariaDB 服务器](quickstart-create-mariadb-server-database-using-azure-portal.md)。

   > [!IMPORTANT]
   > 必须在“常规用途”或“内存优化”定价层中创建 Azure Database for MariaDB 服务器。

2. 创建相同的用户帐户和相应的特权。
    
    用户帐户不会从源服务器复制到副本服务器。 若要为用户提供副本服务器的访问权限，必须在新建的 Azure Database for MariaDB 服务器上创建所有帐户和对应的特权。

3. 将源服务器的 IP 地址添加到副本的防火墙规则。 

   使用 [Azure 门户](howto-manage-firewall-portal.md)或 [Azure CLI](howto-manage-firewall-cli.md) 更新防火墙规则。

> [!NOTE]
> 无偏差通信
>
> Microsoft 支持多样化的包容性环境。 本文包含对单词 slave 的引用。 Microsoft 的[无偏差通信风格指南](https://github.com/MicrosoftDocs/microsoft-style-guide/blob/master/styleguide/bias-free-communication.md)将其视为排他性单词。 本文使用该单词旨在保持一致性，因为目前软件中使用的是该单词。 如果软件更新后删除了该单词，则本文也将更新以保持一致。
>

## <a name="configure-the-source-server"></a>配置源服务器

以下步骤准备并配置本地、VM 或云数据库服务中托管的 MariaDB 服务器，以实现数据传入复制。 MariaDB 服务器是数据传入复制中的源。

1. 请先查看[主服务器要求](concepts-data-in-replication.md#requirements)，然后再继续。 

2. 请确保源服务器允许端口3306上的入站和出站流量，并且源服务器具有 **公共 IP 地址**、DNS 可公开访问，或者具有完全限定的域名 (FQDN) 。 
   
   尝试从在其他计算机上托管的 MySQL 命令行或从 Azure 门户中提供的 [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) 中进行连接，以测试与源服务器的连接。

   如果你的组织具有严格的安全策略，并且将不允许源服务器上的所有 IP 地址启用从 Azure 到源服务器的通信，则可以使用以下命令来确定你的 Azure Database for MariaDB 服务器的 IP 地址。
    
   1. 使用 MySQL 命令行之类的工具登录 Azure Database for MariaDB。
   2. 执行下面的查询。
      ```bash
      mysql> SELECT @@global.redirect_server_host;
      ```
      下面是一些示例输出：
      ```bash 
      +-----------------------------------------------------------+
      | @@global.redirect_server_host                             |
      +-----------------------------------------------------------+
      | e299ae56f000.tr1830.westus1-a.worker.database.windows.net |
       +-----------------------------------------------------------+
      ```
   3. 退出 MySQL 命令行。
   4. 执行以下命令来获取 IP 地址。
      ```bash
      ping <output of step 2b>
      ``` 
      例如： 
      ```bash      
      C:\Users\testuser> ping e299ae56f000.tr1830.westus1-a.worker.database.windows.net
      Pinging tr1830.westus1-a.worker.database.windows.net (**11.11.111.111**) 56(84) bytes of data.
      ```

   5. 配置源服务器的防火墙规则，以便在端口3306上包括上一步的输出 IP 地址。

   > [!NOTE]
   > 此 IP 地址可能因维护/部署操作而更改。 此连接方法仅适用于无法允许3306端口上的所有 IP 地址的客户。

2. 启用二进制日志记录。
    
    若要查看是否已在主服务器上启用二进制日志记录，请输入以下命令：

   ```sql
   SHOW VARIABLES LIKE 'log_bin';
   ```

   如果变量 [`log_bin`](https://mariadb.com/kb/en/library/replication-and-binary-log-server-system-variables/#log_bin) 返回了值 `ON`，则表示已在服务器上启用了二进制日志记录。

   如果 `log_bin` 返回了值 `OFF`，请编辑 **my.cnf** 文件，使 `log_bin=ON` 启用二进制日志记录。 重启服务器，使更改生效。

3. 配置源服务器设置。

    数据传入复制要求参数在 `lower_case_table_names` 源服务器和副本服务器之间保持一致。 在 Azure Database for MariaDB 中，`lower_case_table_names` 参数默认设置为 `1`。

   ```sql
   SET GLOBAL lower_case_table_names = 1;
   ```

4. 创建新的复制角色并设置权限。

   在使用复制权限配置的源服务器上创建用户帐户。 可以使用 SQL 命令或 MySQL Workbench 创建帐户。 如果你打算使用 SSL 进行复制，则必须在创建用户帐户时指定此设置。
   
   若要了解如何在源服务器上添加用户帐户，请参阅 [MariaDB 文档](https://mariadb.com/kb/en/library/create-user/)。

   通过使用以下命令，新的复制角色可以从任何计算机访问源，而不只是托管源本身的计算机。 若要进行这种访问，可在用于创建用户的命令中指定 **syncuser\@'%'** 。
   
   若要详细了解如何[指定帐户名称](https://mariadb.com/kb/en/library/create-user/#account-names)，请参阅 MariaDB 文档。

   **SQL 命令**

   - 使用 SSL 进行复制

       如果所有用户连接都要求 SSL，请输入以下命令来创建用户：

       ```sql
       CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
       GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%' REQUIRE SSL;
       ```

   - 在不使用 SSL 的情况下进行复制

       如果所有用户连接都不要求 SSL，请输入以下命令来创建用户：
    
       ```sql
       CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
       GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%';
       ```

   **MySQL Workbench**

   若要在 MySQL Workbench 中创建复制角色，请在“管理”窗格中选择“用户和特权”。  然后选择“添加帐户”。
 
   ![用户和特权](./media/howto-data-in-replication/users_privileges.png)

   在“登录名”字段中输入用户名。

   ![同步用户](./media/howto-data-in-replication/syncuser.png)
 
   选择“管理角色”面板，然后在“全局特权”列表中选择“复制从属实例”。   选择“应用”以创建复制角色。

   ![复制从属实例](./media/howto-data-in-replication/replicationslave.png)


5. 将源服务器设置为只读模式。

   在开始转储数据库之前，必须将服务器置于只读模式。 在只读模式下，源无法处理任何写入事务。 为帮助避免对业务造成影响，请将只读时段安排在非高峰期。

   ```sql
   FLUSH TABLES WITH READ LOCK;
   SET GLOBAL read_only = ON;
   ```

6. 获取当前的二进制日志文件名和偏移量。

   若要确定当前的二进制日志文件名和偏移量，请运行 [`show master status`](https://mariadb.com/kb/en/library/show-master-status/) 命令。
    
   ```sql
   show master status;
   ```
   结果应类似于下表中的内容：
   
   ![主机状态结果](./media/howto-data-in-replication/masterstatus.png)

   请记下二进制文件名，因为后面的步骤中需要用到。
   
7. 获取 GTID 位置（可选，使用 GTID 复制时需要用到）。

   运行函数 [`BINLOG_GTID_POS`](https://mariadb.com/kb/en/library/binlog_gtid_pos/) 获取相应 binlog 文件名和偏移量的 GTID 位置。
  
    ```sql
    select BINLOG_GTID_POS('<binlog file name>', <binlog offset>);
    ```
 

## <a name="dump-and-restore-the-source-server"></a>转储和还原源服务器

1. 从源服务器转储所有数据库。

   使用 mysqldump 从源服务器转储所有数据库。 不需要转储 MySQL 库和测试库。

    有关详细信息，请参阅[转储和还原](howto-migrate-dump-restore.md)。

2. 将源服务器设置为读/写模式。

   转储数据库后，将源 MariaDB 服务器重新更改为读/写模式。

   ```sql
   SET GLOBAL read_only = OFF;
   UNLOCK TABLES;
   ```

3. 将转储文件还原到新服务器。

   将转储文件还原到 Azure Database for MariaDB 服务中创建的服务器。 请参阅[转储和还原](howto-migrate-dump-restore.md)，了解如何将转储文件还原到 MariaDB 服务器。

   如果转储文件较大，请将它上传到副本服务器所在区域的 Azure 中的 VM。 将转储文件从 VM 还原到 Azure Database for MariaDB 服务器。

## <a name="link-the-source-and-replica-servers-to-start-data-in-replication"></a>将源服务器和副本服务器链接到开始数据传入复制

1. 设置源服务器。

   所有数据传入复制功能都是通过存储过程完成的。 可以在[数据传入复制存储过程](reference-data-in-stored-procedures.md)中找到所有过程。 存储过程可以在 MySQL shell 或 MySQL Workbench 中运行。

   若要链接两个服务器并启动复制，请在 Azure DB for MariaDB 服务中登录到目标副本服务器。 接下来，使用 `mysql.az_replication_change_master` `mysql.az_replication_change_master_with_gtid` Azure DB for MariaDB 服务器上的或存储过程将外部实例设置为源服务器。

   ```sql
   CALL mysql.az_replication_change_master('<master_host>', '<master_user>', '<master_password>', 3306, '<master_log_file>', <master_log_pos>, '<master_ssl_ca>');
   ```
   
   或
   
   ```sql
   CALL mysql.az_replication_change_master_with_gtid('<master_host>', '<master_user>', '<master_password>', 3306, '<master_gtid_pos>', '<master_ssl_ca>');
   ```

   - master_host：源服务器的主机名
   - master_user：源服务器的用户名
   - master_password：源服务器的密码
   - master_log_file：正在运行的 `show master status` 中的二进制日志文件名
   - master_log_pos：正在运行的 `show master status` 中的二进制日志位置
   - master_gtid_pos：正在运行的 `select BINLOG_GTID_POS('<binlog file name>', <binlog offset>);` 中的 GTID 位置
   - master_ssl_ca：CA 证书的上下文。 如果未使用 SSL，请传入空字符串。*
    
    
    \* 建议在 master_ssl_ca 参数中以变量形式传入此字符串。 有关详细信息，请参阅以下示例。

   **示例**

   - 使用 SSL 进行复制

       运行以下命令以创建变量 `@cert`：

       ```sql
       SET @cert = '-----BEGIN CERTIFICATE-----
       PLACE YOUR PUBLIC KEY CERTIFICATE\'S CONTEXT HERE
       -----END CERTIFICATE-----'
       ```

       使用 SSL 进行的复制是在域 companya.com 中托管的源服务器和 Azure Database for MariaDB 中托管的副本服务器之间设置的。 将在副本上运行此存储过程。
    
       ```sql
       CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mariadb-bin.000016', 475, @cert);
       ```
   - 在不使用 SSL 的情况下进行复制

       在域 companya.com 中托管的源服务器与 Azure Database for MariaDB 中托管的副本服务器之间设置不带 SSL 的复制。 将在副本上运行此存储过程。

       ```sql
       CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mariadb-bin.000016', 475, '');
       ```

2. 启动复制。

   调用 `mysql.az_replication_start` 存储过程以启动复制。

   ```sql
   CALL mysql.az_replication_start;
   ```

3. 检查复制状态。

   在副本服务器上调用 [`show slave status`](https://mariadb.com/kb/en/library/show-slave-status/) 命令查看复制状态。
    
   ```sql
   show slave status;
   ```

   如果 `Slave_IO_Running` 和 `Slave_SQL_Running` 的状态为 `yes`，并且 `Seconds_Behind_Master` 的值为 `0`，则表示复制正常运行。 `Seconds_Behind_Master` 指示副本的陈旧状态。 如果值不是 `0`，则表示副本正在处理更新。

4. 更新相应的服务器变量，使数据传入复制更安全（仅当不使用 GTID 进行复制时才需要这样做）。
    
    由于 MariaDB 中的本机复制限制，在不使用 GTID 方案进行复制时，必须设置 [`sync_master_info`](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#sync_master_info) 和 [`sync_relay_log_info`](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#sync_relay_log_info) 变量。

    检查从属服务器的 `sync_master_info` 和 `sync_relay_log_info` 变量，确保数据传入复制稳定，并将这些变量设置为 `1`。
    
## <a name="other-stored-procedures"></a>其他存储过程

### <a name="stop-replication"></a>停止复制

若要停止源服务器和副本服务器之间的复制，请使用以下存储过程：

```sql
CALL mysql.az_replication_stop;
```

### <a name="remove-the-replication-relationship"></a>删除复制关系

若要删除源服务器和副本服务器之间的关系，请使用以下存储过程：

```sql
CALL mysql.az_replication_remove_master;
```

### <a name="skip-the-replication-error"></a>跳过复制错误

若要跳过复制错误并允许复制，请使用以下存储过程：
    
```sql
CALL mysql.az_replication_skip_counter;
```

## <a name="next-steps"></a>后续步骤
详细了解 Azure Database for MariaDB 的[数据传入复制](concepts-data-in-replication.md)。
