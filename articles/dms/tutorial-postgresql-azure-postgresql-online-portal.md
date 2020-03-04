---
title: 教程：通过 Azure 门户将 PostgreSQL 迁移到 Azure DB for PostgreSQL online
titleSuffix: Azure Database Migration Service
description: 了解如何通过 Azure 门户使用 Azure 数据库迁移服务，从本地 PostgreSQL 联机迁移到 Azure Database for PostgreSQL。
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 02/17/2020
ms.openlocfilehash: 67eced7f647d50733dc8d273bdd9cd8a31b7b6dc
ms.sourcegitcommit: d4a4f22f41ec4b3003a22826f0530df29cf01073
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2020
ms.locfileid: "78255504"
---
# <a name="tutorial-migrate-postgresql-to-azure-db-for-postgresql-online-using-dms-via-the-azure-portal"></a>教程：通过 Azure 门户将 PostgreSQL 迁移到 Azure DB for PostgreSQL online

可以使用 Azure 数据库迁移服务将数据库从本地 PostgreSQL 实例迁移到[Azure Database for PostgreSQL](https://docs.microsoft.com/azure/postgresql/) ，并使应用程序的停机时间最短。 本教程介绍如何在 Azure 数据库迁移服务中使用联机迁移活动将 **DVD Rental** 示例数据库从 PostgreSQL 9.6 的本地实例迁移到 Azure Database for PostgreSQL。

在本教程中，你将了解如何执行以下操作：
> [!div class="checklist"]
>
> * 使用 pg_dump 实用程序迁移示例架构。
> * 创建 Azure 数据库迁移服务的实例。
> * 在 Azure 数据库迁移服务中创建迁移项目。
> * 运行迁移。
> * 监视迁移。
> * 执行迁移转换。

> [!NOTE]
> 使用 Azure 数据库迁移服务执行联机迁移需要基于“高级”定价层创建实例。

> [!IMPORTANT]
> 为获得最佳迁移体验，Microsoft 建议在目标数据库所在的 Azure 区域中创建 Azure 数据库迁移服务的实例。 跨区域或地理位置移动数据可能会减慢迁移过程并引入错误。

## <a name="prerequisites"></a>必备条件

要完成本教程，需要：

* 下载并安装[PostgreSQL 社区 edition](https://www.postgresql.org/download/) 9.4、9.5、9.6 或10。 源 PostgreSQL 服务器版本必须是9.4、9.5、9.6、10或11。 有关详细信息，请参阅[支持的 PostgreSQL 数据库版本](https://docs.microsoft.com/azure/postgresql/concepts-supported-versions)一文。

    另外，本地 PostgreSQL 版本必须与 Azure Database for PostgreSQL 版本相符。 例如，PostgreSQL 9.6 只能迁移到 Azure Database for PostgreSQL 9.6、10或11，但不能 Azure Database for PostgreSQL 9.5。

* [创建 Azure Database for PostgreSQL 服务器](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal)或[创建 Azure Database for PostgreSQL-超大规模（Citus）服务器](https://docs.microsoft.com/azure/postgresql/quickstart-create-hyperscale-portal)。
* 使用 Azure 资源管理器部署模型创建 Azure 数据库迁移服务的 Microsoft Azure 虚拟网络，该模型通过使用[ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction)或[VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)为本地源服务器提供站点到站点连接。 有关创建虚拟网络的详细信息，请参阅[虚拟网络文档](https://docs.microsoft.com/azure/virtual-network/)，尤其是包含分步详细信息的快速入门文章。

    > [!NOTE]
    > 在虚拟网络安装期间，如果将 ExpressRoute 与 Microsoft 的网络对等互连一起使用，请将以下服务[终结点](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)添加到将在其中预配服务的子网中：
    >
    > * 目标数据库终结点（例如，SQL 终结点、Cosmos DB 终结点等）
    > * 存储终结点
    > * 服务总线终结点
    >
    > Azure 数据库迁移服务缺少 Internet 连接，因此必须提供此配置。

* 请确保虚拟网络的网络安全组（NSG）规则不会阻止以下到 Azure 数据库迁移服务的入站通信端口：443、53、9354、445、12000。 有关虚拟网络 NSG 流量筛选的详细信息，请参阅[筛选网络流量和网络安全组](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm)一文。
* 配置[针对数据库引擎访问的 Windows 防火墙](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access)。
* 打开 Windows 防火墙，使 Azure 数据库迁移服务能够访问源 PostgreSQL 服务器（默认情况下为 TCP 端口 5432）。
* 在源数据库的前面使用了防火墙设备时，可能需要添加防火墙规则以允许 Azure 数据库迁移服务访问要迁移的源数据库。
* 为 Azure Database for PostgreSQL 创建服务器级[防火墙规则](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)，以允许 Azure 数据库迁移服务访问目标数据库。 提供用于 Azure 数据库迁移服务的虚拟网络的子网范围。
* 在 postgresql.config 文件中启用逻辑复制，并设置以下参数：

  * wal_level = **logical**
  * max_replication_slots = [槽数]，建议设置为“5 个槽”
  * max_wal_senders =[并发任务数] - max_wal_senders 参数设置可以运行的并发任务数，建议设置为“10 个任务”

> [!IMPORTANT]
> 现有数据库中的所有表都需要主键，以确保可以将更改同步到目标数据库。

## <a name="migrate-the-sample-schema"></a>迁移示例架构

若要完成所有数据库对象（例如表架构、索引和存储过程），需从源数据库提取架构并将其应用到此数据库。

1. 使用 pg_dump -s 命令为数据库创建架构转储文件。

    ```
    pg_dump -o -h hostname -U db_username -d db_name -s > your_schema.sql
    ```

    例如，若要为**dvdrental**数据库创建架构转储文件：

    ```
    pg_dump -o -h localhost -U postgres -d dvdrental -s -O -x > dvdrentalSchema.sql
    ```

    若要详细了解如何使用 pg_dump 实用程序，请参阅 [pg-dump](https://www.postgresql.org/docs/9.6/static/app-pgdump.html#PG-DUMP-EXAMPLES) 教程中的示例。

2. 在目标环境中创建一个空数据库，即 Azure Database for PostgreSQL。

    有关如何连接和创建数据库的详细信息，请参阅文章[在 Azure 门户中创建 Azure Database for PostgreSQL 服务器](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal)或[在 Azure 门户中创建 Azure Database for PostgreSQL-超大规模（Citus）服务器](https://docs.microsoft.com/azure/postgresql/quickstart-create-hyperscale-portal)。

    > [!NOTE]
    > Azure Database for PostgreSQL-超大规模（Citus）的实例只有单个数据库： **Citus**。

3. 通过还原架构转储文件，将架构导入已创建的目标数据库。

    ```
    psql -h hostname -U db_username -d db_name < your_schema.sql
    ```

    例如：

    ```
    psql -h mypgserver-20170401.postgres.database.azure.com  -U postgres -d dvdrental citus < dvdrentalSchema.sql
    ```

4. 若要提取 drop 外键脚本并将其添加到目标（Azure Database for PostgreSQL）上，请在 PgAdmin 或 psql 中运行以下脚本。

   > [!IMPORTANT]
   > 架构中的外键将导致迁移的初始加载和连续同步失败。

    ```
    SELECT Queries.tablename
           ,concat('alter table ', Queries.tablename, ' ', STRING_AGG(concat('DROP CONSTRAINT ', Queries.foreignkey), ',')) as DropQuery
                ,concat('alter table ', Queries.tablename, ' ',
                                                STRING_AGG(concat('ADD CONSTRAINT ', Queries.foreignkey, ' FOREIGN KEY (', column_name, ')', 'REFERENCES ', foreign_table_name, '(', foreign_column_name, ')' ), ',')) as AddQuery
        FROM
        (SELECT
        tc.table_schema,
        tc.constraint_name as foreignkey,
        tc.table_name as tableName,
        kcu.column_name,
        ccu.table_schema AS foreign_table_schema,
        ccu.table_name AS foreign_table_name,
        ccu.column_name AS foreign_column_name
    FROM
        information_schema.table_constraints AS tc
        JOIN information_schema.key_column_usage AS kcu
          ON tc.constraint_name = kcu.constraint_name
          AND tc.table_schema = kcu.table_schema
        JOIN information_schema.constraint_column_usage AS ccu
          ON ccu.constraint_name = tc.constraint_name
          AND ccu.table_schema = tc.table_schema
    WHERE constraint_type = 'FOREIGN KEY') Queries
      GROUP BY Queries.tablename;
    ```

5. 运行查询结果中的 drop foreign key（第二列）。

6. 若要禁用目标数据库中的触发器，请运行以下脚本。

   > [!IMPORTANT]
   > 数据中的触发器（insert 或 update）在目标之前强制从要从源复制的数据执行数据完整性。 因此，建议你在迁移过程中在**目标**的所有表中禁用触发器，然后在迁移完成后重新启用触发器。

    ```
    select concat ('alter table ', event_object_table, ' disable trigger ', trigger_name)
    from information_schema.triggers;
    ```

## <a name="register-the-microsoftdatamigration-resource-provider"></a>注册 Microsoft.DataMigration 资源提供程序

1. 登录到 Azure 门户，选择“所有服务”，然后选择“订阅”。

   ![显示门户订阅](media/tutorial-postgresql-to-azure-postgresql-online-portal/portal-select-subscriptions.png)

2. 选择要在其中创建 Azure 数据库迁移服务实例的订阅，再选择“资源提供程序”。

    ![显示资源提供程序](media/tutorial-postgresql-to-azure-postgresql-online-portal/portal-select-resource-provider.png)

3. 搜索迁移服务，再选择“Microsoft.DataMigration”右侧的“注册”。

    ![注册资源提供程序](media/tutorial-postgresql-to-azure-postgresql-online-portal/portal-register-resource-provider.png)

## <a name="create-a-dms-instance"></a>创建 DMS 实例

1. 在 Azure 门户中，选择 **+ 创建资源**，搜索 Azure 数据库迁移服务，然后从下拉列表选择**Azure 数据库迁移服务**。

    ![Azure 市场](media/tutorial-postgresql-to-azure-postgresql-online-portal/portal-marketplace.png)

2. 在“Azure 数据库迁移服务”屏幕上，选择“创建”。

    ![创建 Azure 数据库迁移服务实例](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-create1.png)
  
3. 在 "**创建迁移服务**" 屏幕上，指定名称、订阅、新的或现有的资源组以及服务的位置。

4. 选择现有的虚拟网络，或创建一个新的虚拟网络。

    虚拟网络为 Azure 数据库迁移服务提供源 PostgreSQL 服务器和目标 Azure Database for PostgreSQL 实例的访问权限。

    有关如何在 Azure 门户中创建虚拟网络的详细信息，请参阅[使用 Azure 门户创建虚拟网络](https://aka.ms/DMSVnet)一文。

5. 选择定价层。

    有关成本和定价层的详细信息，请参阅[价格页](https://aka.ms/dms-pricing)。

    ![配置 Azure 数据库迁移服务实例设置](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-settings4.png)

6. 选择 "**查看 + 创建**" 创建服务。

   服务创建将在大约10到15分钟内完成。

## <a name="create-a-migration-project"></a>创建迁移项目

创建服务后，在 Azure 门户中找到并打开它，然后创建一个新的迁移项目。

1. 在 Azure 门户中，选择“所有服务”，搜索 Azure 数据库迁移服务，然后选择“Azure 数据库迁移服务”。

      ![查找 Azure 数据库迁移服务的所有实例](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-search.png)

2. 在 " **Azure 数据库迁移服务**" 屏幕上，搜索所创建的 Azure 数据库迁移服务实例的名称，选择该实例，然后选择 "+**新建迁移项目**"。

3. 在 "**新建迁移项目**" 屏幕上，指定项目的名称，在 "**源服务器类型**" 文本框中，选择 " **PostgresSQL**"，在 "**目标服务器类型**" 文本框中，选择**Azure Database for PostgreSQL**。

4. 在“选择活动类型”部分选择“联机数据迁移”。

    ![创建 Azure 数据库迁移服务项目](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-create-project.png)

    > [!NOTE]
    > 也可以现在就选择“仅创建项目”来创建迁移项目，在以后再执行迁移。

5. 选择 "**保存**"，注意成功使用 Azure 数据库迁移服务迁移数据的要求，然后选择 "**创建和运行活动**"。

## <a name="specify-source-details"></a>指定源详细信息

1. 在 "**添加源详细信息**" 屏幕上，为源 PostgreSQL 实例指定连接详细信息。

    ![“添加源详细信息”屏幕](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-add-source-details.png)

2. 选择“保存”。

## <a name="specify-target-details"></a>指定目标详细信息

1. 在 "**目标详细信息**" 屏幕上，指定目标超大规模（Citus）服务器的连接详细信息，这是使用 pg_dump 部署了**DVD 租赁**架构的超大规模（Citus）的预设置实例。

    ![“目标详细信息”屏幕](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-add-target-details.png)

2. 选择“保存”，然后在“映射到目标数据库”屏幕上，映射源和目标数据库以进行迁移。

    如果目标数据库包含的数据库名称与源数据库的相同，则 Azure 数据库迁移服务默认会选择目标数据库。

    !["映射到目标数据库" 屏幕](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-map-target-databases.png)

3. 选择 "**保存**"，然后在 "**迁移设置**" 屏幕上接受默认值。

    !["迁移设置" 屏幕](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-migration-settings.png)

4. 选择“保存”，在“迁移摘要”屏幕上的“活动名称”文本框中指定迁移活动的名称，然后查看摘要，确保源和目标详细信息与此前指定的信息相符。

    ![迁移摘要屏幕](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-migration-summary.png)

## <a name="run-the-migration"></a>运行迁移

* 选择“运行迁移”。

    此时将显示 "迁移活动" 窗口，并且活动的**状态**将更新为 **"正在进行备份"** 。

## <a name="monitor-the-migration"></a>监视迁移

1. 在迁移活动屏幕上选择“刷新”，以便更新显示，直到迁移的“状态”显示为“完成”。

     ![监视迁移过程](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-monitor-migration.png)

2. 迁移完成后，在 "**数据库名称**" 下，选择特定数据库以获取**完整数据加载**和**增量数据同步**操作的迁移状态。

   > [!NOTE]
   > “完整数据加载”会显示初始加载迁移状态，而“增量数据同步”则会显示变更数据捕获 (CDC) 状态。

     ![完整数据加载详细信息](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-full-data-load-details.png)

     ![增量数据同步详细信息](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-incremental-data-sync-details.png)

## <a name="perform-migration-cutover"></a>执行迁移直接转换

完成初始的完整加载以后，数据库会被标记为“直接转换可供执行”。

1. 如果准备完成数据库迁移，请选择“启动直接转换”。

2. 等待 "**挂起的更改**" 计数器显示**0** ，以确保停止源数据库的所有传入事务，选中 "**确认**" 复选框，然后选择 "**应用**"。

    ![完成切换屏幕](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-complete-cutover.png)

3. 数据库迁移状态显示为 "**已完成**" 时，将应用程序连接到 Azure Database for PostgreSQL 的新目标实例。

## <a name="next-steps"></a>后续步骤

* 若要了解联机迁移到 Azure Database for PostgreSQL 时的已知问题和限制，请参阅 [Azure Database for PostgreSQL 联机迁移的已知问题和解决方法](known-issues-azure-postgresql-online.md)一文。
* 若要了解 Azure 数据库迁移服务，请参阅[什么是 Azure 数据库迁移服务？](https://docs.microsoft.com/azure/dms/dms-overview)一文。
* 有关 Azure Database for PostgreSQL 的信息，请参阅[什么是 Azure Database for PostgreSQL？](https://docs.microsoft.com/azure/postgresql/overview)一文。
