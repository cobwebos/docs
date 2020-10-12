---
title: 将数据从 PostgreSQL 数据库迁移到启用了 Azure Arc 的 PostgreSQL 超大规模服务器组
titleSuffix: Azure Arc enabled database services
description: 将数据从 PostgreSQL 数据库迁移到启用了 Azure Arc 的 PostgreSQL 超大规模服务器组
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 521fd61f18d6673e21c23dbca4cfc12d2ee4bf0b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90934525"
---
# <a name="migrate-postgresql-database-to-azure-arc-enabled-postgresql-hyperscale-server-group"></a>将 PostgreSQL 数据库迁移到启用了 Azure Arc 的 PostgreSQL 超大规模服务器组

本文档介绍了如何将现有的 PostgreSQL 数据库 (不在启用了 Azure Arc 的数据服务) 中的数据库托管到启用了 Azure Arc 的 PostgreSQL 超大规模服务器组中。

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="considerations"></a>注意事项

启用了 Azure Arc PostgreSQL 超大规模服务器组是 PostgreSQL 的社区版本，并已启用 CitusData 扩展。 因此，在 Azure Arc 之外处理 PostgreSQL 的任何工具都应该适用于启用了 Azure Arc 的 PostgreSQL 超大规模服务器组。


因此，使用今天用于 Postgres 的一组工具，你应该能够：
1. 从 Azure Arc 外托管的实例备份 Postgres 数据库
2. 在启用了 Azure Arc 的 PostgreSQL 超大规模服务器组中还原它

您要做的就是：
- 重置服务器参数
- 重置安全上下文：重新创建用户、角色和重置权限 .。。

若要执行此备份/还原操作，可以使用任何能够对 Postgres 执行备份/还原操作的工具。 例如：
- Azure Data Studio 及其 Postgres 扩展
- `pgcli`
- `pgAdmin`
- `pg_dump`
- `pg_restore`
- `psql`
- ...

## <a name="example"></a>示例
让我们使用工具来说明这些步骤 `pgAdmin` 。
请考虑下列设置：
- **Source：**  
    在裸机服务器上本地运行并名为 JEANYDSRV 的 Postgres 服务器。 它的版本为12，并托管一个名为 MyOnPremPostgresDB 的数据库，该数据库具有一个具有1行迁移的表 T1 :::image type="content" source="media/postgres-hyperscale/migrate-pg-source.jpg" alt-text="-源":::

- **位置**  
    在 Azure Arc 环境中运行并名为 postgres01 的 Postgres 服务器。 它的版本为12。 它没有任何数据库，标准 Postgres 数据库除外。  
    :::image type="content" source="media/postgres-hyperscale/migrate-pg-destination.jpg" alt-text="-源":::


### <a name="take-a-backup-of-the-source-database-on-premises"></a>在本地备份源数据库

:::image type="content" source="media/postgres-hyperscale/Migrate-PG-Source-Backup.jpg" alt-text="-源":::

配置：
1. 为它指定文件名： **MySourceBackup**
2. 将格式设置为**自定义** 
 :::image type="content" source="media/postgres-hyperscale/Migrate-PG-Source-Backup2.jpg" alt-text="-源":::

备份成功完成：  
:::image type="content" source="media/postgres-hyperscale/Migrate-PG-Source-Backup3.jpg" alt-text="-源":::

### <a name="create-an-empty-database-on-the-destination-system-in-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>在启用了 Azure Arc 的 PostgreSQL 超大规模服务器组中的目标系统上创建空数据库

> [!NOTE]
> 若要在该工具中注册 Postgres 实例 `pgAdmin` ，需要在 Kubernetes 群集中使用实例的公共 IP，并适当地设置端口和安全上下文。 `psql`运行以下命令后，你将在终结点行上找到这些详细信息：

```console
azdata arc postgres endpoint list -n postgres01
```
这会返回如下所示的输出：
```console
[
  {
    "Description": "PostgreSQL Instance",
    "Endpoint": "postgresql://postgres:<replace with password>@12.345.123.456:1234"
  },
  {
    "Description": "Log Search Dashboard",
    "Endpoint": "https://12.345.123.456:12345/kibana/app/kibana#/discover?_a=(query:(language:kuery,query:'custom_resource_name:\"postgres01\"'))"
  },
  {
    "Description": "Metrics Dashboard",
    "Endpoint": "https://12.345.123.456:12345/grafana/d/postgres-metrics?var-Namespace=arc3&var-Name=postgres01"
  }
]
```

让我们为目标数据库命名 **RESTORED_MyOnPremPostgresDB**  
:::image type="content" source="media/postgres-hyperscale/migrate-pg-destination-dbcreate.jpg" alt-text="迁移-目标-db-创建"lightbox="media/postgres-hyperscale/migrate-pg-destination-dbcreate.jpg":::

### <a name="restore-the-database-in-your-arc-setup"></a>在弧线设置中还原数据库
:::image type="content" source="media/postgres-hyperscale/migrate-pg-destination-dbrestore.jpg" alt-text="-源" 或 "tar**- 
    :::image type="content" source="media/postgres-hyperscale/migrate-pg-destination-dbrestore2.jpg" alt-text="-源"

3. 请单击“还原”。  

   还原成功。  
   :::image type="content" source="media/postgres-hyperscale/migrate-pg-destination-dbrestore3.jpg" alt-text="-源":::

### <a name="verify-that-the-database-was-successfully-restored-in-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>验证是否已在启用了 Azure Arc 的 PostgreSQL 超大规模服务器组中成功还原数据库

使用以下方法之一：

**源 `pgAdmin` ：**  

展开在 Azure Arc 设置中托管的 Postgres 实例。 您将看到数据库中已还原的表，当您选择数据时，它会显示与在本地实例中相同的行：

   :::image type="content" source="media/postgres-hyperscale/migrate-pg-destination-dbrestoreverif.jpg" alt-text="-源"
     },
     {
       "Description": "Log Search Dashboard",
       "Endpoint": "https://12.345.123.456:12345/kibana/app/kibana#/discover?_a=(query:(language:kuery,query:'custom_resource_name:\"postgres01\"'))"
     },
     {
       "Description": "Metrics Dashboard",
       "Endpoint": "https://12.345.123.456:12345/grafana/d/postgres-metrics?var-Namespace=arc3&var-Name=postgres01"
     }
   ]
   ```

1. 在 `psql` 连接字符串中，使用 `-d` 参数指示数据库名称。 在下面的命令中，系统将提示你输入密码：

   ```console
   psql -d RESTORED_MyOnPremPostgresDB -U postgres -h 10.0.0.4 -p 32639
   ```

   `psql` 连线.

   ```output
   Password for user postgres:
   psql (10.12 (Ubuntu 10.12-0ubuntu0.18.04.1), server 12.3 (Debian 12.3-1.pgdg100+1))
   WARNING: psql major version 10, server major version 12.
         Some psql features might not work.
   SSL connection (protocol: TLSv1.3, cipher: TLS_AES_256_GCM_SHA384, bits: 256, compression: off)
   Type "help" for help.

   RESTORED_MyOnPremPostgresDB=#   
   ```

1. 选择表，你将看到从本地 Postgres 实例还原的数据：

   ```console
   RESTORED_MyOnPremPostgresDB=# select * from t1;
   ```

   ```output
    col1 |    col2
   ------+-------------
       1 | BobbyIsADog
   (1 row)
   ```

> [!NOTE]
> - 在 PostgreSQL 超大规模上运行的 Azure Arc 上运行时，你将看不到如此多的性能好处，直到你向外扩展，并跨 PostgreSQL 超大规模服务器组的辅助角色节点分片/分布数据。 请参阅 [后续步骤](#next-steps)。
>
> - 目前不可能将在本地或任何其他云中运行的现有 Postgres 实例 "加入 Azure Arc"。 换句话说，不能在现有的 Postgres 实例上安装某种类型的 "Azure Arc agent"，使其成为 Azure Arc 启用的 Postgres 设置。相反，你需要创建一个新的 Postgres 实例并将数据传输到该实例。 您可以使用以上所示的方法来执行此操作，也可以使用所选的任何 ETL 工具。

## <a name="next-steps"></a>后续步骤

- 阅读 Azure Database for PostgreSQL 超大规模的概念和操作方法指南，以将数据分散到多个 PostgreSQL 超大规模节点并受益于 Azure Database for PostgreSQL 超大规模的所有功能：
    * [节点和表](../../postgresql/concepts-hyperscale-nodes.md)
    * [确定应用程序类型](../../postgresql/concepts-hyperscale-app-type.md)
    * [选择分布列](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
    * [表共置](../../postgresql/concepts-hyperscale-colocation.md)
    * [分发和修改表](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
    * [设计多租户数据库](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
    * [设计实时分析仪表板](../../postgresql/tutorial-design-database-hyperscale-realtime.md)*

> * 在这些文档中，跳过 **登录到 Azure 门户**的部分， **创建 Azure Database For Postgres-超大规模 (Citus) **。 在 Azure Arc 部署中执行剩余步骤。 这些章节特定于在 Azure 云中作为 PaaS 服务提供的 Azure Database for PostgreSQL 超大规模 (Citus) ，但文档的其他部分直接适用于启用了 Azure Arc 的 PostgreSQL 超大规模。

- [横向扩展 Azure Database for PostgreSQL 超大规模服务器组](scale-out-postgresql-hyperscale-server-group.md)
