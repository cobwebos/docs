---
title: 在 Azure Database for MariaDB 配置数据复制 |Microsoft Docs
description: 本文介绍如何设置数据在 Azure 数据库中复制的 MariaDB。
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 21e8a88cc6f03b4d54a6c5299b0b6be36cc32d6d
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444794"
---
# <a name="configure-data-in-replication-in-azure-database-for-mariadb"></a>在 Azure Database for MariaDB 配置数据复制

本文介绍如何通过配置主和副本服务器设置的数据在 Azure 数据库中复制的 MariaDB。 本文假定你具有使用 MariaDB 服务器和数据库的一些经验。

若要在 Azure Database for MariaDB 服务中创建一个副本，数据在复制同步从主 MariaDB 服务器本地，在虚拟机 (Vm) 中或在云数据库服务中的数据。

> [!NOTE]
> 如果主服务器是 10.2 或更高版本，我们建议使用设置数据复制[全局事务 ID](https://mariadb.com/kb/en/library/gtid/)。


## <a name="create-a-mariadb-server-to-use-as-a-replica"></a>创建 MariaDB 服务器以使用作为副本

1. 创建新的 Azure Database for MariaDB 服务器 (例如，replica.mariadb.database.azure.com)。 服务器是在数据中复制的副本服务器。

    若要了解如何创建服务器，请参阅[使用 Azure 门户创建 Azure Database for MariaDB 服务器](quickstart-create-mariadb-server-database-using-azure-portal.md)。

   > [!IMPORTANT]
   > 必须在常规用途或内存优化的定价层中创建 Azure Database for MariaDB 服务器。

2. 创建相同的用户帐户和相应的权限。
    
    用户帐户不是从主服务器复制到副本服务器。 若要提供到副本服务器的用户访问权限，您必须手动创建所有帐户和相应的权限上新创建的 Azure Database for MariaDB 服务器。

## <a name="configure-the-master-server"></a>配置主服务器

以下步骤准备和配置 MariaDB 服务器托管在本地，在 VM 中，或在数据中复制的云数据库服务。 MariaDB 服务器是数据在复制中的主机。

1. 启用二进制日志记录。
    
    若要查看是否在主机上启用二进制日志记录，请输入以下命令：

   ```sql
   SHOW VARIABLES LIKE 'log_bin';
   ```

   如果将变量[ `log_bin` ](https://mariadb.com/kb/en/library/replication-and-binary-log-server-system-variables/#log_bin)返回的值`ON`，在服务器上启用二进制日志记录。

   如果`log_bin`返回的值`OFF`，编辑**my.cnf**文件，以便`log_bin=ON`启用二进制日志记录。 重新启动服务器以使更改生效。

2. 配置主服务器设置。

    数据复制要求参数`lower_case_table_names`主和副本服务器之间保持一致。 `lower_case_table_names`参数设置为`1`默认情况下，Azure Database for MariaDB 中。

   ```sql
   SET GLOBAL lower_case_table_names = 1;
   ```

3. 创建新的复制角色并设置权限。

   复制权限使用配置主密钥服务器上创建用户帐户。 使用 SQL 命令或 MySQL Workbench，可以创建一个帐户。 如果计划通过 SSL 进行复制，您必须创建用户帐户时指定此。
   
   若要了解如何在主服务器上添加用户帐户，请参阅[MariaDB 文档](https://mariadb.com/kb/en/library/create-user/)。

   通过使用以下命令，新的复制角色可以访问来自任何计算机，而不仅仅是主机本身的计算机的主机。 对于此访问权限，指定**syncuser\@%** 在命令中创建的用户。
   
   若要了解有关 MariaDB 文档的详细信息，请参阅[指定帐户名称](https://mariadb.com/kb/en/library/create-user/#account-names)。

   **SQL 命令**

   - 使用 SSL 进行复制

       若要要求所有用户连接使用 SSL，输入以下命令以创建用户：

       ```sql
       CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
       GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%' REQUIRE SSL;
       ```

   - 在不使用 SSL 的情况下进行复制

       如果并非所有连接都需要 SSL，输入以下命令以创建用户：
    
       ```sql
       CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
       GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%';
       ```

   **MySQL Workbench**

   若要创建在 MySQL Workbench 中的复制角色**管理**窗格中，选择**用户和特权**。 然后选择**添加帐户**。
 
   ![用户和特权](./media/howto-data-in-replication/users_privileges.png)

   输入中的用户名**登录名**字段。

   ![同步用户](./media/howto-data-in-replication/syncuser.png)
 
   选择**管理角色**面板中，然后在列表**全局权限**，选择**Replication Slave**。 选择**应用**创建复制角色。

   ![复制从属角色](./media/howto-data-in-replication/replicationslave.png)


4. 设置为只读模式的主服务器。

   转储数据库之前，服务器必须放置在只读模式下。 在只读模式下，主机无法处理任何写入事务。 为了帮助避免对业务的影响，计划在非峰值时间期间只读窗口。

   ```sql
   FLUSH TABLES WITH READ LOCK;
   SET GLOBAL read_only = ON;
   ```

5. 获取当前二进制日志文件的名称和偏移量。

   若要确定当前二进制日志文件的名称和偏移量，请运行命令[ `show master status` ](https://mariadb.com/kb/en/library/show-master-status/)。
    
   ```sql
   show master status;
   ```
   结果应类似于以下表：
   
   ![主机状态结果](./media/howto-data-in-replication/masterstatus.png)

   请注意二进制文件名称，因为将在后续步骤中使用它。
   
6. （可选，使用 GTID 复制时需要），请获取 GTID 位置。

   运行此函数[ `BINLOG_GTID_POS` ](https://mariadb.com/kb/en/library/binlog_gtid_pos/) GTID 位置获取相应 binlog 的文件名和偏移量。
  
    ```sql
    select BINLOG_GTID_POS('<binlog file name>', <binlog offset>);
    ```
 

## <a name="dump-and-restore-the-master-server"></a>转储和还原主服务器

1. 转储从主服务器的所有数据库。

   使用 mysqldump 转储从主服务器的所有数据库。 没有必要转储 MySQL 库并测试库。

    有关详细信息，请参阅[转储和还原](howto-migrate-dump-restore.md)。

2. 设置主服务器以读/写模式。

   转储数据库后，更改主 MariaDB server 后为读/写模式。

   ```sql
   SET GLOBAL read_only = OFF;
   UNLOCK TABLES;
   ```

3. 将转储文件还原到新服务器。

   将转储文件还原到 Azure Database for MariaDB 服务中创建的服务器。 请参阅[转储和还原](howto-migrate-dump-restore.md)有关如何将转储文件还原到 MariaDB 服务器。

   如果转储文件较大，将其上载到 Azure 中的 VM 作为副本服务器在同一区域内。 将其还原到 Azure Database for MariaDB 服务器从虚拟机。

## <a name="link-the-master-and-replica-servers-to-start-data-in-replication"></a>主和副本服务器，以启动数据复制链接

1. 设置主服务器。

   所有复制中数据函数均由存储过程执行。 可以在[复制中数据存储过程](reference-data-in-stored-procedures.md)中找到所有过程。 可以在 MySQL 命令行程序或 MySQL Workbench 中运行存储的过程。

   若要链接的两个服务器并启动复制，请登录到 Azure DB for MariaDB 服务中的目标副本服务器。 接下来，使用作为主服务器设置的外部实例`mysql.az_replication_change_master`或`mysql.az_replication_change_master_with_gtid`Azure DB for MariaDB 服务器上存储过程。

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
   - master_gtid_pos:运行 GTID 位置 `select BINLOG_GTID_POS('<binlog file name>', <binlog offset>);`
   - master_ssl_ca：CA 证书的上下文。 如果您不使用 SSL，则传入空 string.*
    
    
    \* 我们建议为变量 master_ssl_ca 参数中的传递。 有关详细信息，请参阅以下示例。

   **示例**

   - 使用 SSL 进行复制

       创建变量`@cert`通过运行以下命令：

       ```sql
       SET @cert = '-----BEGIN CERTIFICATE-----
       PLACE YOUR PUBLIC KEY CERTIFICATE’S CONTEXT HERE
       -----END CERTIFICATE-----'
       ```

       托管域 companya.com 中的主服务器和副本服务器托管在 Azure Database for MariaDB 之间设置复制使用 SSL。 将在副本上运行此存储过程。
    
       ```sql
       CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mariadb-bin.000016', 475, @cert);
       ```
   - 在不使用 SSL 的情况下进行复制

       托管域 companya.com 中的主服务器和副本服务器托管在 Azure Database for MariaDB 之间设置复制不使用 SSL。 将在副本上运行此存储过程。

       ```sql
       CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mariadb-bin.000016', 475, '');
       ```

2. 启动复制。

   调用`mysql.az_replication_start`存储过程来启动复制。

   ```sql
   CALL mysql.az_replication_start;
   ```

3. 检查复制状态。

   在副本服务器上调用 [`show slave status`](https://mariadb.com/kb/en/library/show-slave-status/) 命令查看复制状态。
    
   ```sql
   show slave status;
   ```

   如果`Slave_IO_Running`并`Slave_SQL_Running`处于状态`yes`，和的值`Seconds_Behind_Master`是`0`，复制是否有效。 `Seconds_Behind_Master` 指示副本的陈旧状态。 如果该值不`0`，然后在副本正在处理更新。

4. 更新相应的服务器变量，以使数据复制更安全 （仅对复制 GTID 不是必需）。
    
    由于在 MariaDB 的本机复制限制，必须设置[ `sync_master_info` ](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#sync_master_info)并[ `sync_relay_log_info` ](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#sync_relay_log_info)上的复制不使用 GTID 方案的变量。

    检查你的从属服务器`sync_master_info`并`sync_relay_log_info`变量，以确保数据复制是稳定的并将变量设置为`1`。
    
## <a name="other-stored-procedures"></a>其他存储过程

### <a name="stop-replication"></a>停止复制

若要停止主服务器与副本服务器之间的复制，请使用以下存储过程：

```sql
CALL mysql.az_replication_stop;
```

### <a name="remove-the-replication-relationship"></a>删除复制关系

若要删除的主和副本服务器之间的关系，请使用以下存储的过程：

```sql
CALL mysql.az_replication_remove_master;
```

### <a name="skip-the-replication-error"></a>跳过复制错误

若要跳过复制错误，并允许复制，请使用以下存储的过程：
    
```sql
CALL mysql.az_replication_skip_counter;
```

## <a name="next-steps"></a>后续步骤
详细了解 Azure Database for MariaDB 的[数据传入复制](concepts-data-in-replication.md)。
