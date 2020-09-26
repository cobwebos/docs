---
title: 横向扩展 Azure Database for PostgreSQL 超大规模服务器组
description: 横向扩展 Azure Database for PostgreSQL 超大规模服务器组
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: df0620308fab2e813fe3802dc7effb9dc1ce226c
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91285377"
---
# <a name="scale-out-your-azure-arc-enabled-postgresql-hyperscale-server-group-by-adding-more-worker-nodes"></a>通过添加更多的工作节点向外扩展启用了 Azure Arc 的 PostgreSQL 超大规模服务器组
本文档介绍如何横向扩展启用了 Azure Arc 的 PostgreSQL 超大规模服务器组。 它通过学习方案来实现此目的。 **如果你不希望通过此方案运行，并且只想了解如何横向扩展，请跳转到段落 [扩大](#scale-out)**。

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="get-started"></a>入门
如果已熟悉启用了 Azure Arc 的缩放模型 PostgreSQL 超大规模或 Azure Database for PostgreSQL 超大规模 (Citus) ，则可以跳过此段落。 如果不是，建议你首先阅读 Azure Database for PostgreSQL 超大规模 (Citus) 的文档页中的此缩放模型。 Azure Database for PostgreSQL 超大规模 (Citus) 与 Azure 中作为一项服务托管的技术 (平台即服务也称为 PAAS) ，而不是作为启用了 Azure Arc 的数据服务的一部分提供：
- [节点和表](../../postgresql/concepts-hyperscale-nodes.md)
- [确定应用程序类型](../../postgresql/concepts-hyperscale-app-type.md)
- [选择分布列](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
- [表共置](../../postgresql/concepts-hyperscale-colocation.md)
- [分发和修改表](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
- [设计多租户数据库](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
- [设计实时分析仪表板](../../postgresql/tutorial-design-database-hyperscale-realtime.md)*

> \* 在上述文档中，跳过 **登录到 Azure 门户**的部分，& **创建 Azure Database for PostgreSQL (Citus) **。 在 Azure Arc 部署中执行剩余步骤。 这些章节特定于在 Azure 云中作为 PaaS 服务提供的 Azure Database for PostgreSQL 超大规模 (Citus) ，但文档的其他部分直接适用于启用了 Azure Arc 的 PostgreSQL 超大规模。

## <a name="scenario"></a>方案
此方案是指在 [创建支持 Azure Arc 的 PostgreSQL 超大规模服务器组](create-postgresql-hyperscale-server-group.md) 文档中作为示例创建的 PostgreSQL 超大规模服务器组。

### <a name="load-test-data"></a>加载测试数据
该方案使用了可从 [Citus data 网站](https://www.citusdata.com/) 获取的公开可用 GitHub 数据的示例， (Citus 数据是 Microsoft) 的一部分。

#### <a name="connect-to-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>连接到已启用 Azure Arc 的 PostgreSQL 超大规模服务器组

##### <a name="list-the-connection-information"></a>列出连接信息
首先获取连接信息，通过以下方式连接到已启用 Azure Arc 的 PostgreSQL 超大规模服务器组：此命令的常规格式为：
```console
azdata arc postgres endpoint list -n <server name>
```
例如：
```console
azdata arc postgres endpoint list -n postgres01
```

示例输出：

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

##### <a name="connect-with-the-client-tool-of-your-choice"></a>连接到所选的客户端工具。

运行以下查询，验证当前是否有两个 (或多个超大规模辅助角色节点) ，每个节点对应于 Kubernetes pod：

```sql
SELECT * FROM pg_dist_node;
```

```console
 nodeid | groupid |                       nodename                        | nodeport | noderack | hasmetadata | isactive | noderole | nodecluster | metadatasynced | shouldhaveshards
--------+---------+-------------------------------------------------------+----------+----------+-------------+----------+----------+-------------+----------------+------------------
      1 |       1 | pg1-1.pg1-svc.default.svc.cluster.local |     5432 | default  | f           | t        | primary  | default     | f              | t
      2 |       2 | pg1-2.pg1-svc.default.svc.cluster.local |     5432 | default  | f           | t        | primary  | default     | f              | t
(2 rows)
```

#### <a name="create-a-sample-schema"></a>创建示例架构
通过运行以下查询来创建两个表：

```sql
CREATE TABLE github_events
(
    event_id bigint,
    event_type text,
    event_public boolean,
    repo_id bigint,
    payload jsonb,
    repo jsonb,
    user_id bigint,
    org jsonb,
    created_at timestamp
);

CREATE TABLE github_users
(
    user_id bigint,
    url text,
    login text,
    avatar_url text,
    gravatar_id text,
    display_login text
);
```

JSONB 是 PostgreSQL 中二进制格式的 JSON 数据类型。 它在单个列中存储一个灵活的架构，并使用 PostgreSQL。 该架构的 GIN 索引用于对其中的每个键和值编制索引。 使用 GIN 索引，可以快速轻松地使用各个负载上的各种条件进行查询。 因此，在加载数据之前，我们将继续创建一些索引：

```sql
CREATE INDEX event_type_index ON github_events (event_type);
CREATE INDEX payload_index ON github_events USING GIN (payload jsonb_path_ops);
```

若要分片标准表，请对每个表运行查询。 指定要分片的表，并指定要在其上分片的键。 User_id 上的 "事件" 和 "用户" 表将分片：

```sql
SELECT create_distributed_table('github_events', 'user_id');
SELECT create_distributed_table('github_users', 'user_id');
```

#### <a name="load-sample-data"></a>加载示例数据
加载包含副本的数据 .。。从程序：

```sql
COPY github_users FROM PROGRAM 'curl "https://examples.citusdata.com/users.csv"' WITH ( FORMAT CSV );
COPY github_events FROM PROGRAM 'curl "https://examples.citusdata.com/events.csv"' WITH ( FORMAT CSV );
```

#### <a name="query-the-data"></a>查询数据
现在，使用两个节点测量简单查询所用的时间：

```sql
SELECT COUNT(*) FROM github_events;
```
记下查询执行时间。


## <a name="scale-out"></a>向外扩展
横向扩展命令的常规格式为：
```console
azdata arc postgres server edit -n <server group name> -w <target number of worker nodes>
```

> [!CAUTION]
> 预览版本不支持向后缩放。 例如，尚不能减少辅助角色节点的数目。 如果需要执行此操作，则需要提取/备份数据，删除服务器组，使用较少的辅助角色节点创建新的服务器组，然后导入数据。

在此示例中，我们将运行以下命令，将辅助角色节点数从2增加到4：

```console
azdata arc postgres server edit -n postgres01 -w 4
```

添加节点后，你将看到该服务器组的挂起状态。 例如：
```console
azdata arc postgres server list
```

```console
Name        State          Workers
----------  -------------  ---------
postgres01  Pending 4/5    4
```

节点可用后，超大规模分片 Rebalancer 会自动运行，然后将数据重新分发到新节点。 横向扩展操作是一个联机操作。 在添加节点并跨节点重新分发数据时，数据仍可用于查询。

### <a name="verify-the-new-shape-of-the-server-group-optional"></a>验证服务器组的新形状 (可选) 
使用以下任一方法来验证服务器组现在是否正在使用您添加的附加辅助角色节点。

#### <a name="with-azdata"></a>With azdata：
运行以下命令：
```console
azdata arc postgres server list
```

它将返回命名空间中创建的服务器组的列表，并指示其辅助角色节点数。 例如：
```console
Name        State    Workers
----------  -------  ---------
postgres01  Ready    4
```

#### <a name="with-kubectl"></a>With kubectl：
运行以下命令：
```console
kubectl get postgresql-12
```

它将返回命名空间中创建的服务器组的列表，并指示其辅助角色节点数。 例如：
```console
NAME         STATE   READY-PODS   EXTERNAL-ENDPOINT   AGE
postgres01   Ready   4/4          10.0.0.4:31066      4d20h
```
> [!NOTE]
> 如果创建了版本 11 PostgreSQL 的服务器组而不是12，请改为运行以下命令： _kubectl get PostgreSQL-11_

#### <a name="with-a-sql-query"></a>使用 SQL 查询：
用所选的客户端工具连接到你的服务器组，并运行以下查询：

```sql
SELECT * FROM pg_dist_node;
```

```console
 nodeid | groupid |                       nodename                        | nodeport | noderack | hasmetadata | isactive | noderole | nodecluster | metadatasynced | shouldhaveshards
--------+---------+-------------------------------------------------------+----------+----------+-------------+----------+----------+-------------+----------------+------------------
      1 |       1 | pg1-1.pg1-svc.default.svc.cluster.local |     5432 | default  | f           | t        | primary  | default     | f              | t
      2 |       2 | pg1-2.pg1-svc.default.svc.cluster.local |     5432 | default  | f           | t        | primary  | default     | f              | t
      3 |       3 | pg1-3.pg1-svc.default.svc.cluster.local |     5432 | default  | f           | t        | primary  | default     | f              | t
      4 |       4 | pg1-4.pg1-svc.default.svc.cluster.local |     5432 | default  | f           | t        | primary  | default     | f              | t
(4 rows)
```

## <a name="return-to-the-scenario"></a>返回到方案

如果要比较对示例数据集的 select count 查询的执行时间，请使用相同的计数查询。 它可用于四个工作节点，无需在 SQL 语句中进行任何更改。

```sql
SELECT COUNT(*) FROM github_events;
```
请注意执行时间。


> [!NOTE]
> 根据你的环境（例如，如果已将测试服务器组部署 `kubeadm` 在单个节点 VM 上），你可能会看到执行时间的适度改善。 若要更好地了解使用启用了 Azure Arc 的 PostgreSQL 超大规模的性能改进，请观看以下简短视频：
>* [高性能 HTAP 与 Azure PostgreSQL 超大规模 (Citus) ](https://www.youtube.com/watch?v=W_3e07nGFxY)
>* [构建 HTAP 应用程序和 Python & Azure PostgreSQL 超大规模 (Citus) ](https://www.youtube.com/watch?v=YDT8_riLLs0)


## <a name="next-steps"></a>后续步骤

- 了解如何 [扩展和缩减 (内存，vcore) 启用了 Azure Arc 的 PostgreSQL 超大规模服务器组](scale-up-down-postgresql-hyperscale-server-group-using-cli.md)
- 了解如何在启用了 Azure Arc 的 PostgreSQL 超大规模服务器组中设置服务器参数
- 阅读 Azure Database for PostgreSQL 超大规模的概念和操作方法指南，以便跨多个 PostgreSQL 超大规模节点分发数据，并从 Azure Database for Postgres 超大规模的所有功能中获益。 :
    * [节点和表](../../postgresql/concepts-hyperscale-nodes.md)
    * [确定应用程序类型](../../postgresql/concepts-hyperscale-app-type.md)
    * [选择分布列](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
    * [表共置](../../postgresql/concepts-hyperscale-colocation.md)
    * [分发和修改表](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
    * [设计多租户数据库](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
    * [设计实时分析仪表板](../../postgresql/tutorial-design-database-hyperscale-realtime.md)*

 > \* 在上述文档中，跳过 **登录到 Azure 门户**的部分，& **创建 Azure Database for PostgreSQL (Citus) **。 在 Azure Arc 部署中执行剩余步骤。 这些章节特定于在 Azure 云中作为 PaaS 服务提供的 Azure Database for PostgreSQL 超大规模 (Citus) ，但文档的其他部分直接适用于启用了 Azure Arc 的 PostgreSQL 超大规模。

- [存储配置和 Kubernetes 存储概念](storage-configuration.md)
- [扩展永久性卷声明](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#expanding-persistent-volumes-claims)
- [Kubernetes 资源模型](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)
