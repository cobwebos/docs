---
title: 配置数据复制-Azure Database for MariaDB
description: 本文介绍如何在 Azure Database for MariaDB 中设置数据传入复制。
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 0dbbc9b09d5d4770296223db9dc909c17f574fe8
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2019
ms.locfileid: "74767018"
---
# <a name="configure-data-in-replication-in-azure-database-for-mariadb"></a>在 Azure Database for MariaDB 中配置数据传入复制

本文介绍如何通过配置主服务器和副本服务器在 Azure Database for MariaDB 中设置数据传入复制。 本文假定你事先对 MariaDB 服务器和数据库有一些经验。

若要在 Azure Database for MariaDB 服务中创建副本，数据传入复制在本地、虚拟机（Vm）或云数据库服务中同步 MariaDB 主服务器中的数据。

> [!NOTE]
> 如果主服务器是版本10.2 或更高版本，我们建议使用[全局事务 ID](https://mariadb.com/kb/en/library/gtid/)设置数据传入复制。


## <a name="create-a-mariadb-server-to-use-as-a-replica"></a>创建要用作副本的 MariaDB 服务器

1. 创建新的 Azure Database for MariaDB 服务器（例如，replica.mariadb.database.azure.com）。 服务器是数据传入复制中的副本服务器。

    若要了解如何创建服务器，请参阅[使用 Azure 门户创建 Azure Database for MariaDB 服务器](quickstart-create-mariadb-server-database-using-azure-portal.md)。

   > [!IMPORTANT]
   > 必须在常规用途或内存优化定价层中创建 Azure Database for MariaDB 服务器。

2. 创建相同的用户帐户和相应的特权。
    
    用户帐户不会从主服务器复制到副本服务器。 若要向用户提供对副本服务器的访问，你必须在新创建的 Azure Database for MariaDB 服务器上手动创建所有帐户和相应的权限。

## <a name="configure-the-master-server"></a>配置主服务器

以下步骤准备并配置在本地、VM 或数据传入复制的云数据库服务中托管的 MariaDB 服务器。 MariaDB 服务器是数据传入复制中的主服务器。

1. 启用二进制日志记录。
    
    若要查看是否在主节点上启用了二进制日志记录，请输入以下命令：

   ```sql
   SHOW VARIABLES LIKE 'log_bin';
   ```

   如果变量[`log_bin`](https://mariadb.com/kb/en/library/replication-and-binary-log-server-system-variables/#log_bin)返回值 `ON`，则在您的服务器上启用二进制日志记录。

   如果 `log_bin` 返回 `OFF`的值，则编辑 **.cnf**文件，以便 `log_bin=ON` 启用二进制日志记录。 重新启动服务器以使更改生效。

2. 配置主服务器设置。

    数据传入复制要求参数 `lower_case_table_names` 在主服务器和副本服务器之间保持一致。 默认情况下，`lower_case_table_names` 参数设置为在 Azure Database for MariaDB 中 `1`。

   ```sql
   SET GLOBAL lower_case_table_names = 1;
   ```

3. 创建新的复制角色并设置权限。

   在使用复制权限配置的主服务器上创建用户帐户。 可以通过使用 SQL 命令或 MySQL 工作台来创建帐户。 如果计划与 SSL 一起复制，则在创建用户帐户时必须指定此帐户。
   
   若要了解如何在主服务器上添加用户帐户，请参阅[MariaDB 文档](https://mariadb.com/kb/en/library/create-user/)。

   通过使用以下命令，新的复制角色可以从任何计算机访问主副本，而不只是承载主机本身的计算机。 对于此访问权限，请在命令中指定**syncuser\@"%"** 以创建用户。
   
   若要了解有关 MariaDB 文档的详细信息，请参阅[指定帐户名称](https://mariadb.com/kb/en/library/create-user/#account-names)。

   **SQL 命令**

   - 与 SSL 之间的复制

       若要对所有用户连接要求 SSL，请输入以下命令以创建用户：

       ```sql
       CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
       GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%' REQUIRE SSL;
       ```

   - 不带 SSL 的复制

       如果所有连接都不需要 SSL，请输入以下命令创建用户：
    
       ```sql
       CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
       GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%';
       ```

   **MySQL Workbench**

   若要在 MySQL 工作台中创建复制角色，请在 "**管理**" 窗格中选择 "**用户和特权**"。 然后选择 "**添加帐户**"。
 
   ![用户和特权](./media/howto-data-in-replication/users_privileges.png)

   在 "**登录名**" 字段中输入用户名。

   ![同步用户](./media/howto-data-in-replication/syncuser.png)
 
   选择 "**管理角色**" 面板，然后在**全局权限**列表中选择 "**复制从属**"。 选择 "**应用**" 以创建复制角色。

   ![复制从属角色](./media/howto-data-in-replication/replicationslave.png)


4. 将 "主服务器" 设置为 "只读" 模式。

   在转储数据库之前，服务器必须处于只读模式。 当处于只读模式时，主节点无法处理任何写入事务。 为了帮助避免业务影响，请在非高峰时段计划只读窗口。

   ```sql
   FLUSH TABLES WITH READ LOCK;
   SET GLOBAL read_only = ON;
   ```

5. 获取当前二进制日志文件的名称和偏移量。

   若要确定当前的二进制日志文件名称和偏移量，请运行命令[`show master status`](https://mariadb.com/kb/en/library/show-master-status/)。
    
   ```sql
   show master status;
   ```
   结果应类似于下表：
   
   ![主机状态结果](./media/howto-data-in-replication/masterstatus.png)

   请注意二进制文件名，因为后面的步骤会用到它。
   
6. 获取 GTID 位置（可选，通过 GTID 复制所需的位置）。

   运行函数[`BINLOG_GTID_POS`](https://mariadb.com/kb/en/library/binlog_gtid_pos/)以获取相应 binlog 文件名和偏移量的 GTID 位置。
  
    ```sql
    select BINLOG_GTID_POS('<binlog file name>', <binlog offset>);
    ```
 

## <a name="dump-and-restore-the-master-server"></a>转储和还原主服务器

1. 转储主服务器中的所有数据库。

   使用 mysqldump 从主服务器转储所有数据库。 不需要转储 MySQL 库和测试库。

    有关详细信息，请参阅[转储和还原](howto-migrate-dump-restore.md)。

2. 将主服务器设置为读/写模式。

   转储数据库后，将 master MariaDB 服务器改回读/写模式。

   ```sql
   SET GLOBAL read_only = OFF;
   UNLOCK TABLES;
   ```

3. 将转储文件还原到新服务器。

   将转储文件还原到 Azure Database for MariaDB 服务中创建的服务器。 有关如何将转储文件还原到 MariaDB 服务器的详细说明，请参阅[转储 & 还原](howto-migrate-dump-restore.md)。

   如果转储文件很大，请将其上传到与副本服务器位于同一区域的 Azure 中的 VM。 将其从 VM 还原到 Azure Database for MariaDB 服务器。

## <a name="link-the-master-and-replica-servers-to-start-data-in-replication"></a>将主服务器和副本服务器链接到开始数据传入复制

1. 设置主服务器。

   所有复制中数据函数均由存储过程执行。 可以在[复制中数据存储过程](reference-data-in-stored-procedures.md)中找到所有过程。 存储过程可在 MySQL shell 或 MySQL 工作台中运行。

   若要链接两个服务器并开始复制，请登录到 Azure DB for MariaDB 服务中的目标副本服务器。 接下来，使用 Azure DB for MariaDB 服务器上的 `mysql.az_replication_change_master` 或 `mysql.az_replication_change_master_with_gtid` 存储过程将外部实例设置为主服务器。

   ```sql
   CALL mysql.az_replication_change_master('<master_host>', '<master_user>', '<master_password>', 3306, '<master_log_file>', <master_log_pos>, '<master_ssl_ca>');
   ```
   
   或
   
   ```sql
   CALL mysql.az_replication_change_master_with_gtid('<master_host>', '<master_user>', '<master_password>', 3306, '<master_gtid_pos>', '<master_ssl_ca>');
   ```

   - master_host：主服务器的主机名
   - master_user：主服务器的用户名
   - master_password：主服务器的密码
   - master_log_file：正在运行的 `show master status` 中的二进制日志文件名
   - master_log_pos：正在运行的 `show master status` 中的二进制日志位置
   - master_gtid_pos：从运行的 GTID 位置 `select BINLOG_GTID_POS('<binlog file name>', <binlog offset>);`
   - master_ssl_ca：CA 证书的上下文。 如果使用的不是 SSL，请传入空字符串。 *
    
    
    \* 建议将 master_ssl_ca 参数作为变量传入。 有关详细信息，请参阅以下示例。

   **示例**

   - 与 SSL 之间的复制

       通过运行以下命令创建变量 `@cert`：

       ```sql
       SET @cert = '-----BEGIN CERTIFICATE-----
       PLACE YOUR PUBLIC KEY CERTIFICATE’S CONTEXT HERE
       -----END CERTIFICATE-----'
       ```

       使用 SSL 进行的复制是在域 companya.com 中托管的主服务器和 Azure Database for MariaDB 中托管的副本服务器之间设置的。 将在副本上运行此存储过程。
    
       ```sql
       CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mariadb-bin.000016', 475, @cert);
       ```
   - 不带 SSL 的复制

       在域 companya.com 中托管的主服务器与 Azure Database for MariaDB 中托管的副本服务器之间设置不带 SSL 的复制。 将在副本上运行此存储过程。

       ```sql
       CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mariadb-bin.000016', 475, '');
       ```

2. 开始复制。

   调用 `mysql.az_replication_start` 存储过程来启动复制。

   ```sql
   CALL mysql.az_replication_start;
   ```

3. 检查复制状态。

   在副本服务器上调用 [`show slave status`](https://mariadb.com/kb/en/library/show-slave-status/) 命令查看复制状态。
    
   ```sql
   show slave status;
   ```

   如果 `Slave_IO_Running` 和 `Slave_SQL_Running` 处于状态 `yes`，并且 `Seconds_Behind_Master` 的值为 `0`，则复制将正常运行。 `Seconds_Behind_Master` 指示副本的陈旧状态。 如果值不是 `0`，则副本将处理更新。

4. 更新相应的服务器变量以使数据复制更安全（只有在没有 GTID 的情况下才需要复制）。
    
    由于 MariaDB 中的本机复制限制，你必须在不使用 GTID 方案的情况下在复制上设置[`sync_master_info`](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#sync_master_info)和[`sync_relay_log_info`](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#sync_relay_log_info)变量。

    检查从属服务器的 `sync_master_info` 和 `sync_relay_log_info` 变量，确保数据复制是稳定的，并将变量设置为 `1`。
    
## <a name="other-stored-procedures"></a>其他存储过程

### <a name="stop-replication"></a>停止复制

若要停止主服务器与副本服务器之间的复制，请使用以下存储过程：

```sql
CALL mysql.az_replication_stop;
```

### <a name="remove-the-replication-relationship"></a>删除复制关系

若要删除主服务器和副本服务器之间的关系，请使用以下存储过程：

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
