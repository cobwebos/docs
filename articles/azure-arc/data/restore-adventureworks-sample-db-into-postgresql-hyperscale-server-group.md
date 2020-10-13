---
title: 将 AdventureWorks 示例数据库还原到启用了 Azure Arc 的 PostgreSQL 超大规模
description: 将 AdventureWorks 示例数据库还原到启用了 Azure Arc 的 PostgreSQL 超大规模
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 2b51c5ca2295671a30fa6c0aee8d313c4c333900
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90934261"
---
# <a name="restore-the-adventureworks-sample-database-to-azure-arc-enabled-postgresql-hyperscale"></a>将 AdventureWorks 示例数据库还原到启用了 Azure Arc 的 PostgreSQL 超大规模

[AdventureWorks](/sql/samples/adventureworks-install-configure) 是一个示例数据库，其中包含教程和示例中使用的 OLTP 数据库。 它由 Microsoft 提供并维护，作为 [SQL Server 示例 GitHub 存储库](https://github.com/microsoft/sql-server-samples/tree/master/samples/databases)的一部分。

开源项目已转换 AdventureWorks 数据库，使其兼容启用了 Azure Arc 的 PostgreSQL 超大规模。
- [原始项目](https://github.com/lorint/AdventureWorks-for-Postgres)
- [在项目中，将 CSV 文件预转换为与 PostgreSQL 兼容](https://github.com/NorfolkDataSci/adventure-works-postgres)

本文档介绍了将 AdventureWorks 示例数据库还原到 PostgreSQL 超大规模服务器组的一个简单过程。

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="download-the-adventureworks-backup-file"></a>下载 AdventureWorks 备份文件

将 PostgreSQL 文件下载到你的超大规模服务器组容器。 在此示例中，我们将使用 `kubectl exec` 命令远程执行 PostgreSQL 超大规模服务器组容器中的命令，将文件下载到容器中。 可以从可访问的任何位置下载此文件 `curl` 。 如果有其他数据库备份要纳入 PostgreSQL 超大规模服务器组容器中的文件，请使用相同的方法。 在 PostgreSQL 超大规模服务器组容器中时，可以轻松创建数据库、架构，并填充数据。

运行类似于下面的命令来下载文件，并在运行之前替换 pod 名称和命名空间名称的值：

> [!NOTE]
>  容器需要通过 Internet 连接才能通过443从 GitHub 下载文件。

> [!NOTE]
>  使用 Postgres 超大规模服务器组的 "协调器" 节点的 pod 名称。 其名称为 <server group name> -0。  如果你不确定 pod 名称，请运行命令 `kubectl get pod`

```console
kubectl exec <PostgreSQL pod name> -n <namespace name> -c postgres  -- /bin/bash -c "cd /tmp && curl -k -O https://raw.githubusercontent.com/microsoft/azure_arc/master/azure_arc_data_jumpstart/aks/arm_template/postgres_hs/AdventureWorks.sql"

#Example:
#kubectl exec postgres02-0 -n arc -c postgres -- /bin/bash -c "cd /tmp && curl -k -O https://raw.githubusercontent.com/microsoft/azure_arc/master/azure_arc_data_jumpstart/aks/arm_template/postgres_hs/AdventureWorks.sql"
```

## <a name="step-2-restore-the-adventureworks-database"></a>步骤2：还原 AdventureWorks 数据库

同样，你可以运行 kubectl exec 命令来使用 PostgreSQL 超大规模服务器组容器中包含的 psql CLI 工具来创建和加载数据库。

运行类似于下面的命令，以创建空数据库首先替换 pod 名称和命名空间名称的值，然后再运行该数据库。

```console
kubectl exec <PostgreSQL pod name> -n <namespace name> -c postgres -- psql --username postgres -c 'CREATE DATABASE "adventureworks";'

#Example
#kubectl exec postgres02-0 -n arc -c postgres -- psql --username postgres -c 'CREATE DATABASE "adventureworks";'
```

然后，运行类似于下面的命令来还原数据库，以在运行之前替换 pod 名称和命名空间名称的值。

```console
kubectl exec <PostgreSQL pod name> -n <namespace name> -c postgres -- psql --username postgres -d adventureworks -f /tmp/AdventureWorks.sql

#Example
#kubectl exec postgres02-0 -n arc -c postgres -- psql --username postgres -d adventureworks -f /tmp/AdventureWorks.sql
```


> **注意：在 Azure Arc 启用的 PostgreSQL 超大规模上运行时，你将看不到如此多的性能好处，直到你向外扩展，并在 PostgreSQL 超大规模服务器组的辅助角色节点上分片/分发数据/表。请参阅 [建议的后续步骤](#suggested-next-steps)。**

## <a name="suggested-next-steps"></a>建议的后续步骤
- 阅读 Azure Database for PostgreSQL 超大规模的概念和操作方法指南，以将数据分散到多个 PostgreSQL 超大规模节点并受益于 Azure Database for PostgreSQL 超大规模的所有功能。 :
    * [节点和表](../../postgresql/concepts-hyperscale-nodes.md)
    * [确定应用程序类型](../../postgresql/concepts-hyperscale-app-type.md)
    * [选择分布列](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
    * [表共置](../../postgresql/concepts-hyperscale-colocation.md)
    * [分发和修改表](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
    * [设计多租户数据库](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
    * [设计实时分析仪表板](../../postgresql/tutorial-design-database-hyperscale-realtime.md)*

   > \* 在上述文档中，跳过 **登录到 Azure 门户**的部分，& **创建 Azure Database for PostgreSQL (Citus) **。 在 Azure Arc 部署中执行剩余步骤。 这些章节特定于在 Azure 云中作为 PaaS 服务提供的 Azure Database for PostgreSQL 超大规模 (Citus) ，但文档的其他部分直接适用于启用了 Azure Arc 的 PostgreSQL 超大规模。

- [横向扩展 Azure Database for PostgreSQL 超大规模服务器组](scale-out-postgresql-hyperscale-server-group.md)
