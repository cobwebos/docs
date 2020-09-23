---
title: Kubernetes 群集节点上的 PostgreSQL 超大规模服务器组的位置
description: 说明如何将形成 PostgreSQL 超大规模服务器组的 PostgreSQL 实例放置在 Kubernetes 群集节点上
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 5da00916a3f7a6a3685b1de1c56dd032355e28fa
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90934173"
---
# <a name="azure-arc-enabled-postgresql-hyperscale-server-group-placement"></a>启用 Azure Arc PostgreSQL 超大规模服务器组放置

在本文中，我们将演示如何将 Azure Arc 启用 PostgreSQL 超大规模服务器组的 PostgreSQL 实例放置在托管它们的 Kubernetes 群集的物理节点上的示例。 

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="configuration"></a>Configuration

在此示例中，我们使用的是具有四个物理节点 (AKS) 群集的 Azure Kubernetes 服务。 

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/1_cluster_portal.png" alt-text="Azure 门户中的4节点 AKS 群集":::

通过运行以下命令列出 Kubernetes 群集的物理节点：

```console
kubectl get nodes
```

其中显示了 Kubernetes 群集中的四个物理节点：

```output
NAME                                STATUS   ROLES   AGE   VERSION
aks-agentpool-42715708-vmss000000   Ready    agent   11h   v1.17.9
aks-agentpool-42715708-vmss000001   Ready    agent   11h   v1.17.9
aks-agentpool-42715708-vmss000002   Ready    agent   11h   v1.17.9
aks-agentpool-42715708-vmss000003   Ready    agent   11h   v1.17.9
```

该体系结构可表示为：

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/2_logical_cluster.png" alt-text="在 Kubernetes 群集中分组的4个节点的逻辑表示形式":::

Kubernetes 群集托管一个 Azure Arc 数据控制器和一个已启用的 Azure Arc PostgreSQL 超大规模服务器组。 此服务器组是三个 PostgreSQL 实例的构成：一个协调器和两个辅助角色。

用命令列出 pod：

```console
kubectl get pods -n arc3
```
这会生成以下输出：

```output
NAME                 READY   STATUS    RESTARTS   AGE
…
postgres01-0         3/3     Running   0          9h
postgres01-1         3/3     Running   0          9h
postgres01-2         3/3     Running   0          9h
```
其中每个盒都托管一个 PostgreSQL 实例。 它们共同构成了已启用 Azure Arc PostgreSQL 超大规模服务器组：

```output
Pod name    Role in the server group
postgres01-0            Coordinator
postgres01-1    Worker
postgres01-2    Worker
```

## <a name="placement"></a>放置
让我们看看 Kubernetes 如何放置服务器组的箱。 描述每个 pod，并识别在 Kubernetes 群集中放置的物理节点。 例如，对于协调器，请运行以下命令：

```console
kubectl describe pod postgres01-0 -n arc3
```

这会生成以下输出：

```output
Name:         postgres01-0
Namespace:    arc3
Priority:     0
Node:         aks-agentpool-42715708-vmss000000
Start Time:   Thu, 17 Sep 2020 00:40:33 -0700
…
```

为每个 pod 运行此命令时，我们会将当前的位置汇总为：

| 服务器组角色|服务器组 pod|托管 pod 的 Kubernetes 物理节点 |
|--|--|--|
| 辅助角色|postgres01-1|aks-agentpool-42715708-vmss000002 |
| 辅助角色|postgres01-2|aks-agentpool-42715708-vmss000003 |

同时还请注意，在 pod 的描述中，每个 pod 承载的容器的名称。 例如，对于第二个辅助角色，请运行以下命令：

```console
kubectl describe pod postgres01-2 -n arc3
```

这会生成以下输出：

```output
…
Node:         aks-agentpool-42715708-vmss000003/10.240.0.7
..
Containers:
  Fluentbit:
…
  Postgres:
…
  Telegraf:
…
```

已启用 Azure Arc 的 PostgreSQL 超大规模服务器组中的每个 pod 承载以下三个容器：

|容器|说明
|----|----|
|`Fluentbit` |数据 * 日志收集器： https://fluentbit.io/
|`Postgres`|启用了 Azure Arc PosgreSQL 超大规模服务器组的 PostgreSQL 实例部分
|`Telegraf` |指标收集器： https://www.influxdata.com/time-series-platform/telegraf/

体系结构如下：

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/3_pod_placement.png" alt-text="3个每个箱放置在单独的节点上":::

这意味着，此时，每个 PostgreSQL 实例构成启用了 Azure Arc 的 PostgreSQL 超大规模服务器组将托管在 Kubernetes 容器中的特定物理主机上。 这是帮助在启用了 Azure Arc 的 PostgreSQL 超大规模服务器组的情况下获得最佳性能的最佳配置，因为每个角色 (协调器和工作人员) 使用每个物理节点的资源。 这些资源不会在多个 PostgreSQL 角色之间共享。

## <a name="scale-out-azure-arc-enabled-postgresql-hyperscale"></a>已启用 Scale out Azure Arc PostgreSQL 超大规模

现在，让我们向外扩展，向服务器组添加第三个工作节点并观察发生了什么情况。 它将创建一个将托管在第四个 pod 中的第四个 PostgreSQL 实例。
若要横向扩展，请运行命令：

```console
azdata arc postgres server edit --name postgres01 --workers 3
```

这将生成以下输出：

```output
Updating postgres01 in namespace `arc3`
postgres01 is Ready
```

列出 Azure Arc 数据控制器中部署的服务器组，并验证该服务器组现在是否运行了三个辅助角色。 运行以下命令：

```console
azdata arc postgres server list
```

并观察到，它确实从两个工作人员扩大到了三个工作人员：

```output
Name        State    Workers
----------  -------  ---------
postgres01  Ready    3
```

如前文所述，请注意服务器组现在总共使用四个 pod：

```console
kubectl get pods -n arc3
```

```output
NAME                 READY   STATUS    RESTARTS   AGE
…
postgres01-0         3/3     Running   0          11h
postgres01-1         3/3     Running   0          11h
postgres01-2         3/3     Running   0          11h
postgres01-3         3/3     Running   0          5m2s
```

并描述新的 pod，以确定托管 Kubernetes 群集的哪些物理节点。
运行以下命令：

```console
kubectl describe pod postgres01-3 -n arc3
```

标识宿主节点的名称：

```output
Name:         postgres01-3
Namespace:    arc3
Priority:     0
Node:         aks-agentpool-42715708-vmss000000
```

现在，群集的物理节点上的 PostgreSQL 实例的位置：

|服务器组角色|服务器组 pod|托管 pod 的 Kubernetes 物理节点
|-----|-----|-----
|Coordinator|postgres01-0|aks-agentpool-42715708-vmss000000
|辅助角色|postgres01-1|aks-agentpool-42715708-vmss000002
|辅助角色|postgres01-2|aks-agentpool-42715708-vmss000003
|辅助角色|postgres01-3|aks-agentpool-42715708-vmss000000

请注意，新工作线程 (postgres01-3) 的 pod 已置于与协调器相同的节点上。 

体系结构如下：

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/4_pod_placement_.png" alt-text="与协调器相同的节点上的第四个 pod":::

为什么不将新的辅助角色/pod 放置在 Kubernetes 群集的剩余物理节点上 aks-agentpool-42715708-vmss000003？

原因在于，Kubernetes 群集的最后一个物理节点实际上承载了多个用于托管运行支持 Azure Arc 的数据服务所需的组件的 pod。 Kubernetes 评估了在计划时最好的候选项（即 aks-agentpool-42715708-vmss000000 物理节点）。 

使用与上面相同的命令;我们发现每个物理节点的宿主：

|其他 pod 名称\* |使用情况|托管 pod 的 Kubernetes 物理节点
|----|----|----
|引导程序-jh48b||aks-agentpool-42715708-vmss000003
|gwmbs||aks-agentpool-42715708-vmss000002
|controldb-0||aks-agentpool-42715708-vmss000001
|controlwd-zzjp7||aks-agentpool-42715708-vmss000000
|logsdb-0|Elasticsearch，从 `Fluentbit` 每个 pod 的容器接收数据|aks-agentpool-42715708-vmss000003
|logsui-5fzv5||aks-agentpool-42715708-vmss000003
|metricsdb-0|InfluxDB，从 `Telegraf` 每个 pod 的容器接收数据|aks-agentpool-42715708-vmss000000
|metricsdc-47d47||aks-agentpool-42715708-vmss000002
|metricsdc-864kj||aks-agentpool-42715708-vmss000001
|metricsdc-l8jkf||aks-agentpool-42715708-vmss000003
|metricsdc-nxm4l||aks-agentpool-42715708-vmss000000
|metricsui-4fb7l||aks-agentpool-42715708-vmss000003
|mgmtproxy-4qppp||aks-agentpool-42715708-vmss000002

> \* Pod 名称上的后缀将因其他部署而异。 此外，我们仅在此处列出 Azure Arc 数据控制器的 Kubernetes 命名空间中托管的 pod。

体系结构如下：

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/5_full_list_of_pods.png" alt-text="各个节点的命名空间中的所有 pod":::

这意味着启用了 Azure Arc Postgres 超大规模服务器组的协调器节点 (Pod 1) 与服务器组的第三个辅助角色节点 (Pod 4) 共享相同的物理资源。 这是可接受的，因为协调器节点通常使用非常少的资源来与辅助角色节点所使用的资源进行比较。 从这种情况中，你可能会推断到你应仔细选择：
- Kubernetes 群集的大小以及其每个物理节点 (内存、vCore) 的特征
- Kubernetes 群集内的物理节点数
- 在 Kubernetes 群集上托管的应用程序或工作负荷。

对于启用了 Azure Arc 的 PostgreSQL 超大规模服务器组，在 Kubernetes 群集上托管过多的工作负荷可能会受到限制。 如果发生这种情况，您将无法从其水平缩放的能力中获益。 您从系统中获得的性能不仅仅是物理节点或存储系统的位置或物理特征。 还介绍了如何配置 Kubernetes 群集内运行的每个资源 (包括启用了 Azure Arc PostgreSQL 超大规模) ，例如，为内存和 vCore 设置的请求和限制。 可在给定 Kubernetes 群集上托管的工作负荷量相对于 Kubernetes 群集的特性、工作负荷的性质、用户数、Kubernetes 群集操作的完成方式 .。。

## <a name="scale-out-aks"></a>横向扩展 AKS

我们来看一下，横向扩展 AKS 群集和启用了 Azure Arc 的 PostgreSQL 超大规模服务器是一种从支持 Azure Arc 的 PostgreSQL 超大规模的高性能中获益最大的方法。
让我们向 AKS 群集添加第五个节点：

:::row:::
    :::column:::
        以前
    :::column-end:::
    :::column:::
        完成
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        :::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/6_layout_before.png" alt-text="Azure 门户布局":::
    :::column-end:::
    :::column:::
        :::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/7_layout_after.png" alt-text="之后 Azure 门户布局":::
    :::column-end:::
:::row-end:::

体系结构如下：

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/8_logical_layout_after.png" alt-text="更新后 Kubernetes 群集上的逻辑布局":::

让我们通过运行命令来查看在新的 AKS 物理节点上承载 Arc 数据控制器命名空间的哪些 pod：

```console
kubectl describe node aks-agentpool-42715708-vmss000004
```

接下来，我们将更新系统体系结构的表示形式：

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/9_updated_list_of_pods.png" alt-text="群集逻辑关系图上的所有 pod":::

我们可以看到，Kubernetes 群集的新物理节点仅托管 Azure Arc 数据服务所需的指标 pod。 请注意，在此示例中，我们仅重点介绍 Arc 数据控制器的命名空间，而不表示其他 pod。

## <a name="scale-out-azure-arc-enabled-postgresql-hyperscale-again"></a>已启用横向扩展 Azure Arc PostgreSQL 超大规模

第五个物理节点尚未托管任何工作负荷。 当我们横向扩展启用了 Azure Arc 的 PostgreSQL 超大规模时，Kubernetes 将优化新 PostgreSQL pod 的位置，而不应在已承载更多工作负荷的物理节点上归置。 运行以下命令，将启用了 Azure Arc 的 PostgreSQL 超大规模从3个辅助角色扩展到4个辅助角色。 在操作结束时，服务器组将被构成并分布在五个 PostgreSQL 实例中，一个协调器和四个工作线程。

```console
azdata arc postgres server edit --name postgres01 --workers 4
```

这将生成以下输出：

```output
Updating postgres01 in namespace `arc3`
postgres01 is Ready
```

列出在数据控制器中部署的服务器组，并验证服务器组现在是否运行有四个辅助角色：

```console
azdata arc postgres server list
```

并观察它确实从三个工作人员向外扩展。 

```console
Name        State    Workers
----------  -------  ---------
postgres01  Ready    4
```

如前文所述，观察服务器组现在使用四个 pod：

```output
kubectl get pods -n arc3

NAME                 READY   STATUS    RESTARTS   AGE
…
postgres01-0         3/3     Running   0          13h
postgres01-1         3/3     Running   0          13h
postgres01-2         3/3     Running   0          13h
postgres01-3         3/3     Running   0          179m
postgres01-4         3/3     Running   0          3m13s
```

服务器组的形状现在为：

|服务器组角色|服务器组 pod
|----|-----
|Coordinator|postgres01-0
|辅助角色|postgres01-1
|辅助角色|postgres01-2
|辅助角色|postgres01-3
|辅助角色|postgres01-4

让我们介绍 postgres01-4 pod，以确定它所托管的物理节点：

```console
kubectl describe pod postgres01-4 -n arc3
```

并观察它运行的是什么：

|服务器组角色|服务器组 pod| Pod
|----|-----|------
|Coordinator|postgres01-0|aks-agentpool-42715708-vmss000000
|辅助角色|postgres01-1|aks-agentpool-42715708-vmss000002
|辅助角色|postgres01-2|aks-agentpool-42715708-vmss000003
|辅助角色|postgres01-3|aks-agentpool-42715708-vmss000000
|辅助角色|postgres01-4|aks-agentpool-42715708-vmss000004

体系结构如下所示：

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/10_kubernetes_schedules_newest_pod.png" alt-text="Kubernetes 计划使用最低用量的节点中的最新 pod":::

Kubernetes 已在 Kubernetes 群集的最小加载物理节点中计划新的 PostgreSQL pod。

## <a name="summary"></a>总结

为了更充分地利用可伸缩性和性能，将 Azure Arc 启用的服务器组横向扩展，应避免在 Kubernetes 群集内发生资源争用：
- 在启用了 Azure Arc 的 PostgreSQL 超大规模服务器组和托管于同一 Kubernetes 群集上的其他工作负载之间
- 在构成启用了 Azure Arc PostgreSQL 超大规模服务器组的所有 PostgreSQL 实例之间

可以通过多种方式实现此目的：
- 同时扩展 Kubernetes 和 Azure Arc enabled Postgres 超大规模：考虑横向缩放 Kubernetes 群集，这与缩放启用了 Azure Arc 的 PostgreSQL 超大规模服务器组的方式相同。 将添加到服务器组中的每个辅助角色的物理节点添加到群集。
- 横向扩展启用了 Azure Arc 的 Postgres 超大规模不使用 Kubernetes：设置正确的资源约束 (请求和限制内存和 vCore) 在 Kubernetes (启用了 Azure Arc) PostgreSQL 超大规模的工作负荷中，你将在归置上启用工作负荷 Kubernetes 并降低资源争用的风险。 你需要确保 Kubernetes 群集的物理节点的物理特性可以遵循你定义的资源约束。 还应确保平衡随着时间的推移而不断演化，或在 Kubernetes 群集中添加更多工作负荷。
- 使用 Kubernetes 机制 (pod 选择器、相关性、反相关性) 来影响盒的位置。

## <a name="next-steps"></a>后续步骤

[通过添加更多的工作节点向外扩展启用了 Azure Arc 的 PostgreSQL 超大规模服务器组](scale-out-postgresql-hyperscale-server-group.md)
