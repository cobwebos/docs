---
title: 教程：通过 Azure 门户以联机方式将 Azure DB for PostgreSQL 迁移到 Azure DB for PostgreSQL
titleSuffix: Azure Database Migration Service
description: 了解如何通过 Azure 门户使用 Azure 数据库迁移服务执行从一个 Azure DB for PostgreSQL 到另一个 Azure Database for PostgreSQL 的联机迁移。
services: dms
author: arunkumarthiags
ms.author: arthiaga
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 07/21/2020
ms.openlocfilehash: 713b1698bff703507f46e1a8f76c6be385f41ec5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91282454"
---
# <a name="tutorial-migrate-azure-db-for-postgresql---single-server-to-azure-db-for-postgresql---single-server-or-hyperscale-citus-online-using-dms-via-the-azure-portal"></a>教程：通过 Azure 门户使用 DMS 以联机方式将 Azure DB for PostgreSQL（单一服务器）迁移到 Azure DB for PostgreSQL（单一服务器或超大规模 (Citus)）

可以使用 Azure 数据库迁移服务在尽量缩短停机时间的情况下，将数据库从 [Azure Database for PostgreSQL - 单一服务器](https://docs.microsoft.com/azure/postgresql/overview#azure-database-for-postgresql---single-server)实例迁移到 [Azure Database for PostgreSQL 上的超大规模 (Citus)](https://docs.microsoft.com/azure/postgresql/overview#azure-database-for-postgresql---hyperscale-citus) 实例。 本教程介绍如何在 Azure 数据库迁移服务中使用联机迁移活动，将“DVD Rental”示例数据库从 Azure Database for PostgreSQL v10 迁移到 Azure Database for PostgreSQL 上的超大规模 (Citus)。

本教程介绍如何执行下列操作：
> [!div class="checklist"]
>
> * 使用 pg_dump 实用工具迁移示例架构。
> * 创建 Azure 数据库迁移服务的实例。
> * 在 Azure 数据库迁移服务中创建迁移项目。
> * 运行迁移。
> * 监视迁移。
> * 执行直接转换迁移。

> [!NOTE]
> 使用 Azure 数据库迁移服务执行联机迁移需要基于“高级”定价层创建实例。 我们对磁盘进行加密，以防止在迁移过程中数据被盗

> [!IMPORTANT]
> 为获得最佳迁移体验，Microsoft 建议在目标数据库所在的 Azure 区域中创建 Azure 数据库迁移服务的实例。 跨区域或地理位置移动数据可能会减慢迁移过程并引入错误。

> [!IMPORTANT]
> PostgreSQL 版本 10 及更高版本支持从 Azure Database for PostgreSQL 进行迁移。 你还可以使用本教程从一个 Azure Database for PostgreSQL 实例迁移到另一个 Azure Database for PostgreSQL 实例或超大规模 (Citus) 实例。

## <a name="prerequisites"></a>先决条件

要完成本教程，需要：

* 请检查 [Azure 数据库迁移服务支持的迁移方案的状态](https://docs.microsoft.com/azure/dms/resource-scenario-status)，以了解支持的迁移和版本组合。 
* 现有的 [Azure Database for PostgreSQL](https://docs.microsoft.com/azure/postgresql/) 版本 10 和更高版本的实例，以及“DVD Rental”数据库。 Azure 数据库迁移服务不支持从 Azure DB for PostgreSQL 9.5 或 9.6 进行迁移。

    另请注意，目标 Azure Database for PostgreSQL 版本必须等于或晚于本地 PostgreSQL 版本。 例如，PostgreSQL 10 可以迁移到 Azure Database for PostgreSQL 10 或 11，但不能迁移到 Azure Database for PostgreSQL 9.6。

* [创建 Azure Database for PostgreSQL 服务器](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal)或[创建 Azure Database for PostgreSQL - 超大规模 (Citus) 服务器](https://docs.microsoft.com/azure/postgresql/quickstart-create-hyperscale-portal)，作为要将数据迁移到的目标数据库服务器。
* 使用 Azure 资源管理器部署模型为 Azure 数据库迁移服务创建 Microsoft Azure 虚拟网络。 有关创建虚拟网络的详细信息，请参阅[虚拟网络文档](https://docs.microsoft.com/azure/virtual-network/)，尤其是提供了分步详细信息的快速入门文章。

* 确保虚拟网络的网络安全组 (NSG) 规则未阻止到 Azure 数据库迁移服务的以下入站通信端口：443、53、9354、445、12000。 有关虚拟网络 NSG 流量筛选的更多详细信息，请参阅[使用网络安全组筛选网络流量](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm)一文。
* 为 Azure Database for PostgreSQL 源创建服务器级[防火墙规则](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)，以允许 Azure 数据库迁移服务访问源数据库。 提供用于 Azure 数据库迁移服务的虚拟网络子网范围。
* 为 Azure Database for PostgreSQL 目标创建服务器级[防火墙规则](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)，以允许 Azure 数据库迁移服务访问目标数据库。 提供用于 Azure 数据库迁移服务的虚拟网络子网范围。
* 在用作源的 Azure Database for PostgreSQL 实例中设置以下服务器参数：

  * max_replication_slots = [槽数]，建议设置为“5 个槽” 
  * max_wal_senders =[并发任务数] - max_wal_senders 参数设置可以运行的并发任务数，建议设置为“10 个任务” 

> [!NOTE]
> 以上服务器参数是静态的，需要重新启动 Azure Database for PostgreSQL 实例才能生效。 有关切换服务器参数的详细信息，请参阅[配置 Azure Database for PostgreSQL 服务器参数](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-portal)。

> [!IMPORTANT]
> 现有数据库中的所有表都需要主键，以确保可以将更改同步到目标数据库。

## <a name="migrate-the-sample-schema"></a>迁移示例架构

若要完成所有数据库对象（例如表架构、索引和存储过程），需从源数据库提取架构并将其应用到此数据库。

1. 使用 pg_dump -s 命令为数据库创建架构转储文件。

    ```
    pg_dump -o -h hostname -U db_username -d db_name -s > your_schema.sql
    ```

    例如，若要为 dvdrental 数据库创建架构转储文件：

    ```
    pg_dump -o -h mypgserver-source.postgres.database.azure.com -U pguser@mypgserver-source -d dvdrental -s -O -x > dvdrentalSchema.sql
    ```

    若要详细了解如何使用 pg_dump 实用程序，请参阅 [pg-dump](https://www.postgresql.org/docs/9.6/static/app-pgdump.html#PG-DUMP-EXAMPLES) 教程中的示例。

2. 在目标环境中创建一个空数据库，即 Azure Database for PostgreSQL。

    有关如何连接和创建数据库的详细信息，请参阅文章[在 Azure 门户中创建 Azure Database for PostgreSQL 服务器](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal)或[在 Azure 门户中创建 Azure Database for PostgreSQL - 超大规模 (Citus) 服务器](https://docs.microsoft.com/azure/postgresql/quickstart-create-hyperscale-portal)。

    > [!NOTE]
    > Azure Database for PostgreSQL - 超大规模 (Citus) 的实例只有单个数据库：citus。

3. 通过还原架构转储文件，将架构导入已创建的目标数据库。

    ```
    psql -h hostname -U db_username -d db_name < your_schema.sql
    ```

    例如：

    ```
    psql -h mypgserver-source.postgres.database.azure.com  -U pguser@mypgserver-source -d dvdrental citus < dvdrentalSchema.sql
    ```

4. 若要提取 drop foreign key 脚本并将其添加到目标 (Azure Database for PostgreSQL) 中，请在 PgAdmin 或 psql 中运行以下脚本。

   > [!IMPORTANT]
   > 如果架构中有外键，则迁移的初始加载和连续同步将失败。

    ```
    SELECT Q.table_name
        ,CONCAT('ALTER TABLE ', table_schema, '.', table_name, STRING_AGG(DISTINCT CONCAT(' DROP CONSTRAINT ', foreignkey), ','), ';') as DropQuery
            ,CONCAT('ALTER TABLE ', table_schema, '.', table_name, STRING_AGG(DISTINCT CONCAT(' ADD CONSTRAINT ', foreignkey, ' FOREIGN KEY (', column_name, ')', ' REFERENCES ', foreign_table_schema, '.', foreign_table_name, '(', foreign_column_name, ')' ), ','), ';') as AddQuery
    FROM
        (SELECT
        S.table_schema,
        S.foreignkey,
        S.table_name,
        STRING_AGG(DISTINCT S.column_name, ',') AS column_name,
        S.foreign_table_schema,
        S.foreign_table_name,
        STRING_AGG(DISTINCT S.foreign_column_name, ',') AS foreign_column_name
    FROM
        (SELECT DISTINCT
        tc.table_schema,
        tc.constraint_name AS foreignkey,
        tc.table_name,
        kcu.column_name,
        ccu.table_schema AS foreign_table_schema,
        ccu.table_name AS foreign_table_name,
        ccu.column_name AS foreign_column_name
        FROM information_schema.table_constraints AS tc
        JOIN information_schema.key_column_usage AS kcu ON tc.constraint_name = kcu.constraint_name AND tc.table_schema = kcu.table_schema
        JOIN information_schema.constraint_column_usage AS ccu ON ccu.constraint_name = tc.constraint_name AND ccu.table_schema = tc.table_schema
    WHERE constraint_type = 'FOREIGN KEY'
        ) S
        GROUP BY S.table_schema, S.foreignkey, S.table_name, S.foreign_table_schema, S.foreign_table_name
        ) Q
        GROUP BY Q.table_schema, Q.table_name;
    ```

5. 运行查询结果中的 drop foreign key（第二列）。

6. 若要在目标数据库中禁用触发器，请运行以下脚本。

   > [!IMPORTANT]
   > 数据中的触发器（插入或更新触发器）会赶在从源中复制数据之前在目标中强制实施数据完整性。 因此，建议在迁移期间禁用目标的所有表中的触发器，然后在迁移完成后重新启用这些触发器。

    ```
    SELECT DISTINCT CONCAT('ALTER TABLE ', event_object_schema, '.', event_object_table, ' DISABLE TRIGGER ', trigger_name, ';')
    FROM information_schema.triggers
    ```

## <a name="register-the-microsoftdatamigration-resource-provider"></a>注册 Microsoft.DataMigration 资源提供程序

1. 登录到 Azure 门户，选择“所有服务”，然后选择“订阅”。

   ![显示门户订阅](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/portal-select-subscriptions.png)

2. 选择要在其中创建 Azure 数据库迁移服务实例的订阅，再选择“资源提供程序”。

    ![显示资源提供程序](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/portal-select-resource-provider.png)

3. 搜索迁移服务，再选择“Microsoft.DataMigration”右侧的“注册” 。

    ![注册资源提供程序](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/portal-register-resource-provider.png)

## <a name="create-a-dms-instance"></a>创建 DMS 实例

1. 在 Azure 门户中，选择 **+ 创建资源**，搜索 Azure 数据库迁移服务，然后从下拉列表选择**Azure 数据库迁移服务**。

    ![Azure 市场](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/portal-marketplace.png)

2. 在“Azure 数据库迁移服务”屏幕上，选择“创建” 。

    ![创建 Azure 数据库迁移服务实例](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-create1.png)
  
3. 在“创建迁移服务”屏幕上，指定服务的名称、订阅、新的或现有资源组以及位置。

4. 选择现有虚拟网络或新建一个。

    虚拟网络为 Azure 数据库迁移服务提供对源 PostgreSQL 服务器和目标 Azure Database for PostgreSQL 实例的访问权限。

    有关如何在 Azure 门户中创建虚拟网络的详细信息，请参阅[使用 Azure 门户创建虚拟网络](https://aka.ms/DMSVnet)一文。

5. 选择定价层。

    有关成本和定价层的详细信息，请参阅[价格页](https://aka.ms/dms-pricing)。

    ![配置 Azure 数据库迁移服务实例设置](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-settings4.png)

6. 选择“查看 + 创建”以创建服务。

   服务创建将在约 10 到 15 分钟内完成。

## <a name="create-a-migration-project"></a>创建迁移项目

创建服务后，在 Azure 门户中找到并打开它，然后创建一个新的迁移项目。

1. 在 Azure 门户中，选择“所有服务”，搜索 Azure 数据库迁移服务，然后选择“Azure 数据库迁移服务”。

      ![查找 Azure 数据库迁移服务的所有实例](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-search.png)

2. 在“Azure 数据库迁移服务”屏幕上，搜索所创建的 Azure 数据库迁移服务实例名称，选择该实例，然后选择“+ 新建迁移项目” 。

3. 在“新建迁移项目”屏幕上指定项目名称，在“源服务器类型”文本框中选择“PostgreSQL”，在“目标服务器类型”文本框中选择“Azure Database for PostgreSQL”    。
    > [!NOTE]
    > 在“源服务器类型”中选择“PostgreSQL”，即使源服务器是“Azure Database for PostgreSQL”实例  。  

4. 在“选择活动类型”部分选择“联机数据迁移”。 

    ![创建 Azure 数据库迁移服务项目](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-create-project.png)

    > [!NOTE]
    > 也可以现在就选择“仅创建项目”来创建迁移项目，在以后再执行迁移。

5. 选择“保存”，注意成功使用 Azure 数据库迁移服务迁移数据所要满足的要求，然后选择“创建并运行活动” 。

## <a name="specify-source-details"></a>指定源详细信息

1. 在“添加源详细信息”  屏幕上，指定源 PostgreSQL 实例的连接详细信息。

    ![“添加源详细信息”屏幕](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-add-source-details.png)

    > [!NOTE]
    > 可以在“Azure Database for PostgreSQL”门户中找到诸如“服务器名称”、“服务器端口”、“数据库名称”等详细信息。

2. 选择“保存” 。

## <a name="specify-target-details"></a>指定目标详细信息

1. 在“目标详细信息”屏幕上，指定目标超大规模 (Citus) 服务器的连接详细信息，该服务器是超大规模 (Citus) 的预配实例，DVD Rental 架构通过使用 pg\dump 部署到该实例 。

    ![“目标详细信息”屏幕](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-add-target-details.png)

    > [!NOTE]
    > 可以从一个 Azure Database for PostgreSQL 实例迁移到另一个 Azure Database for PostgreSQL 单一服务器实例或超大规模 (Citus) 服务器。

2. 选择“保存”，然后在“映射到目标数据库”屏幕上，映射源和目标数据库以进行迁移。 

    如果目标数据库包含的数据库名称与源数据库的相同，则 Azure 数据库迁移服务默认会选择目标数据库。

    ![“映射到目标数据库”屏幕](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-map-target-databases.png)

3. 选择“保存”，然后在“迁移设置”屏幕上接受默认值 。

    ![“迁移设置”屏幕](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-migration-settings.png)

4. 选择“保存”，在“迁移摘要”屏幕上的“活动名称”文本框中指定迁移活动的名称，然后查看摘要，确保源和目标详细信息与此前指定的信息相符  。

    ![“迁移摘要”屏幕](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-migration-summary.png)

## <a name="run-the-migration"></a>运行迁移

* 选择“运行迁移”。

    迁移活动窗口随即出现，活动的“状态”应更新并显示为“正在进行备份” 。

## <a name="monitor-the-migration"></a>监视迁移

1. 在迁移活动屏幕上选择“刷新”****，以便更新显示，直到迁移的“状态”**** 显示为“完成”****。

     ![监视迁移过程](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-monitor-migration.png)

2. 完成迁移后，请在“数据库名称”下选择特定数据库即可转到“完整数据加载”和“增量数据同步”操作的迁移状态  。

   > [!NOTE]
   > “完整数据加载”会显示初始加载迁移状态，而“增量数据同步”则会显示变更数据捕获 (CDC) 状态。 

     ![完整数据加载详细信息](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-full-data-load-details.png)

     ![增量数据同步详细信息](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-incremental-data-sync-details.png)

## <a name="perform-migration-cutover"></a>执行迁移直接转换

完成初始的完整加载以后，数据库会被标记为“直接转换可供执行”。

1. 如果准备完成数据库迁移，请选择“启动直接转换”。 

2. 等到“挂起的更改”  计数器显示“0”  以确保源数据库的所有传入事务都已停止，选中“确认”  复选框，然后选择“应用”  。

    ![“完成直接转换”屏幕](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-complete-cutover.png)

3. 当数据库迁移状态显示“已完成”后，请将应用程序连接到 Azure Database for PostgreSQL 的新目标实例。

## <a name="next-steps"></a>后续步骤

* 若要了解联机迁移到 Azure Database for PostgreSQL 时的已知问题和限制，请参阅 [Azure Database for PostgreSQL 联机迁移的已知问题和解决方法](known-issues-azure-postgresql-online.md)一文。
* 若要了解 Azure 数据库迁移服务，请参阅[什么是 Azure 数据库迁移服务？](https://docs.microsoft.com/azure/dms/dms-overview)一文。
* 有关 Azure Database for PostgreSQL 的信息，请参阅[什么是 Azure Database for PostgreSQL？](https://docs.microsoft.com/azure/postgresql/overview)一文。
