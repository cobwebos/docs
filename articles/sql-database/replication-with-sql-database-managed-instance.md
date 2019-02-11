---
title: 在 Azure SQL 数据库托管实例中配置复制 | Microsoft Docs
description: 了解如何在 Azure SQL 数据库托管实例中配置事务复制
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: howto
author: allenwux
ms.author: xiwu
ms.reviewer: mathoma
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: b0188a0983ea18490f3997b857386e313daa58ed
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2019
ms.locfileid: "55467657"
---
# <a name="configure-replication-in-azure-sql-database-managed-instance"></a>在 Azure SQL 数据库托管实例中配置复制

通过事务复制，可以将数据从 SQL Server 或 Azure SQL 数据库托管实例数据库复制到托管实例，或将托管实例的数据库中所做的更改推送到其他 SQL Server、SQL 数据库单一数据库或弹性池，或其他托管实例。 复制在 [Azure SQL 数据库托管实例](sql-database-managed-instance.md)上以公共预览版提供。 托管实例可以托管发布服务器、分发服务器和订阅服务器数据库。 有关可用配置，请参阅[事务复制配置](sql-database-managed-instance-transactional-replication.md#common-configurations)。

## <a name="requirements"></a>要求

Azure SQL 数据库上的发布服务器和分发服务器需要：

- 不在异地灾难恢复配置中的 Azure SQL 数据库托管实例。

   >[!NOTE]
   >尚未使用托管实例进行配置的 Azure SQL 数据库只能是订阅服务器。

- SQL Server 的所有实例需要在同一 vNet 上。

- 连接时，在复制参与者之间使用 SQL 身份验证。

- 适用于复制工作目录的 Azure 存储帐户共享。

- 需要在托管实例子网的安全规则中打开端口 445（TCP 出站）才能访问 Azure 文件共享

## <a name="features"></a>功能

支持：

- 事务和快照复制混合，混合了本地和 Azure SQL 数据库托管实例。

- 订阅服务器可以是本地的，可以是 Azure SQL 数据库中的单一数据库，还可以是 Azure SQL 数据库弹性池中的入池数据库。

- 单向或双向复制。

不支持以下功能：

- 可更新的订阅。

- 活动异地复制。

## <a name="configure-publishing-and-distribution-example"></a>配置发布和分发示例

1. 在门户中[创建 Azure SQL 数据库托管实例](sql-database-managed-instance-create-tutorial-portal.md)。
2. [创建 Azure 存储帐户](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account#create-a-storage-account)（适用于工作目录）。

   确保复制存储密钥。 请参阅[查看和复制存储访问密钥](../storage/common/storage-account-manage.md#access-keys
)。
3. 为发布服务器创建数据库。

   在下面的示例脚本中，请将 `<Publishing_DB>` 替换为此数据库的名称。

4. 使用适用于发布服务器的 SQL 身份验证创建数据库用户。 使用安全密码。

   在下面的示例脚本中，将 `<SQL_USER>` 和 `<PASSWORD>` 与此 SQL Server 帐户数据库用户和密码配合使用。

5. [连接到 SQL 数据库托管实例](sql-database-connect-query-ssms.md)。

6. 运行以下查询，以便添加分发服务器和分发数据库。

   ```sql
   USE [master]
   GO
   EXEC sp_adddistributor @distributor = @@ServerName;
   EXEC sp_adddistributiondb @database = N'distribution';
   ```

7. 若要将发布服务器配置为使用指定的分发数据库，请更新并运行以下查询。

   将 `<SQL_USER>` 和 `<PASSWORD>` 替换为 SQL Server 帐户和密码。

   将 `\\<STORAGE_ACCOUNT>.file.core.windows.net\<SHARE>` 替换为存储帐户的值。  

   在 Microsoft Azure 存储帐户的“访问密钥”选项卡中，将 `<STORAGE_CONNECTION_STRING>` 替换为连接字符串。

   更新以下查询后，请运行它。

   ```sql
   USE [master]
   EXEC sp_adddistpublisher @publisher = @@ServerName,
                @distribution_db = N'distribution',
                @security_mode = 0,
                @login = N'<SQL_USER>',
                @password = N'<PASSWORD>',
                @working_directory = N'\\<STORAGE_ACCOUNT>.file.core.windows.net\<SHARE>',
                @storage_connection_string = N'<STORAGE_CONNECTION_STRING>';
   GO
   ```

8. 配置用于复制的发布服务器。

    在以下查询中，请将 `<Publishing_DB>` 替换为发布服务器数据库的名称。

    将 `<Publication_Name>` 替换为发布的名称。

    将 `<SQL_USER>` 和 `<PASSWORD>` 替换为 SQL Server 帐户和密码。

    更新查询后，请运行它，以便创建发布。

   ```sql
   USE [<Publishing_DB>]
   EXEC sp_replicationdboption @dbname = N'<Publishing_DB>',
                @optname = N'publish',
                @value = N'true';

   EXEC sp_addpublication @publication = N'<Publication_Name>',
                @status = N'active';

   EXEC sp_changelogreader_agent @publisher_security_mode = 0,
                @publisher_login = N'<SQL_USER>',
                @publisher_password = N'<PASSWORD>',
                @job_login = N'<SQL_USER>',
                @job_password = N'<PASSWORD>';

   EXEC sp_addpublication_snapshot @publication = N'<Publication_Name>',
                @frequency_type = 1,
                @publisher_security_mode = 0,
                @publisher_login = N'<SQL_USER>',
                @publisher_password = N'<PASSWORD>',
                @job_login = N'<SQL_USER>',
                @job_password = N'<PASSWORD>'
   ```

9. 添加文章、订阅和推送订阅代理。

   若要添加这些对象，请更新以下脚本。

   - 将 `<Object_Name>` 替换为发布对象的名称。
   - 将 `<Object_Schema>` 替换为源架构的名称。
   - 替换尖括号 `<>` 中的其他参数，使之与前述脚本中的值匹配。

   ```sql
   EXEC sp_addarticle @publication = N'<Publication_Name>',
                @type = N'logbased',
                @article = N'<Object_Name>',
                @source_object = N'<Object_Name>',
                @source_owner = N'<Object_Schema>'

   EXEC sp_addsubscription @publication = N'<Publication_Name>',
                @subscriber = @@ServerName,
                @destination_db = N'<Subscribing_DB>',
                @subscription_type = N'Push'

   EXEC sp_addpushsubscription_agent @publication = N'<Publication_Name>',
                @subscriber = @@ServerName,
                @subscriber_db = N'<Subscribing_DB>',
                @subscriber_security_mode = 0,
                @subscriber_login = N'<SQL_USER>',
                @subscriber_password = N'<PASSWORD>',
                @job_login = N'<SQL_USER>',
                @job_password = N'<PASSWORD>'
   GO
   ```
   
## <a name="see-also"></a>另请参阅

- [事务复制](sql-database-managed-instance-transactional-replication.md)
- [什么是托管实例？](sql-database-managed-instance.md)
