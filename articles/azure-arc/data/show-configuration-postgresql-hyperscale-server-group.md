---
title: 显示启用了 Arc 的 PostgreSQL 超大规模服务器组的配置
titleSuffix: Azure Arc enabled data services
description: 显示启用了 Arc 的 PostgreSQL 超大规模服务器组的配置
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: ceab9af7e6556b2d957fafce8cd89d4a0daf9508
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90934820"
---
# <a name="show-the-configuration-of-an-arc-enabled-postgresql-hyperscale-server-group"></a>显示启用了 Arc 的 PostgreSQL 超大规模服务器组的配置

本文介绍如何)  (显示服务器组的配置。 这样做的目的是预测您可能会问自己的一些问题，并回答这些问题。 有时可能会有多个有效的答案。 本文 pitches 最常见或最有用的项目。 它按主题对这些问题进行分组：

- 从 Kubernetes 的角度来看
- 从启用了 Azure Arc 的数据服务角度来看

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="from-a-kubernetes-point-of-view"></a>从 Kubernetes 的角度来看

### <a name="how-many-pods-are-used-by-azure-arc-enabled-postgresql-hyperscale"></a>Azure Arc 启用了多少盒 PostgreSQL 超大规模？

列出 Postgres 类型的 Kubernetes 资源。 运行以下命令：

```console
kubectl get postgresqls [-n <namespace name>]
```

此命令的输出显示创建的服务器组的列表。 对于每个，它表示 pod 的数目。 例如：

```output
NAME                                             STATE   READY-PODS   EXTERNAL-ENDPOINT   AGE
postgresql-12.arcdata.microsoft.com/postgres01   Ready   3/3          10.0.0.4:30499      6h34m
postgresql-12.arcdata.microsoft.com/postgres02   Ready   3/3          10.0.0.4:31066      6d7h
```

此示例显示2个服务器组已创建，每个服务器组在3个 pod 上运行 (1 协调器 + 2 个工作线程) 。 这意味着在此 Azure Arc 数据控制器中创建的服务器组使用6个 pod。

### <a name="what-pods-are-used-by-azure-arc-enabled-postgresql-hyperscale-server-groups"></a>启用了 Azure Arc 的 PostgreSQL 超大规模服务器组使用哪些 pod？

运行：

```console
kubectl get pods [-n <namespace name>]
```

这会返回 pod 列表。 你会看到服务器组根据你为这些服务器组提供的名称使用的 pod。 例如：

```console 
NAME                 READY   STATUS    RESTARTS   AGE
bootstrapper-vdltm   1/1     Running   0          6d8h
control-h6kc9        2/2     Running   0          6d8h
controldb-0          2/2     Running   0          6d8h
controlwd-96sbn      1/1     Running   0          6d8h
logsdb-0             1/1     Running   0          6d8h
logsui-7wkg2         1/1     Running   0          6d8h
metricsdb-0          1/1     Running   0          6d8h
metricsdc-28ffl      1/1     Running   0          6d8h
metricsui-k7qsh      1/1     Running   0          6d8h
mgmtproxy-gd84z      2/2     Running   0          6d8h
postgres01-0         3/3     Running   0          6h50m
postgres01-1         3/3     Running   0          6h50m
postgres01-2         3/3     Running   0          6h50m
postgres02-0         3/3     Running   0          22h
postgres02-1         3/3     Running   0          22h
postgres02-2         3/3     Running   0          22h
```

在此示例中，承载创建的两个服务器组的六个 pod 是：
- `postgres01-0`
- `postgres01-1`
- `postgres01-2`
- `postgres02-0`
- `postgres02-1`
- `postgres02-2`  

### <a name="what-server-group-pod-is-used-for-what-role-the-server-group"></a>哪个服务器组 pod 用于服务器组的角色？

带有后缀的任何 pod 名称 `-0` 表示协调器节点。 以1或更大的为后缀的任何节点名称 `-x` 为辅助节点。 在上述示例中：
- 协调员为： `postgres01-0` 、 `postgres02-0`
- 辅助角色为： `postgres01-2` 、 `postgres01-2` 、 `postgres02-1` 、 `postgres02-2`

### <a name="what-is-the-status-of-the-pods"></a>盒的状态是什么？

运行 `kubectl get pods` 并查看列 `STATUS`

### <a name="what-persistent-volume-claims-pvcs-are-being-used"></a>使用哪些永久性卷声明 (Pvc) ？ 

若要了解使用哪些 Pvc 以及哪些 Pvc 用于数据、日志和备份，请运行： 

```console
kubectl get pvc [-n <namespace name>]
```

默认情况下，PVC 的名称前缀指示其用法：

- `backups-`...：是用于备份的 PVC
- `data-`...：是用于数据文件的 PVC
- `logs-`...：是用于事务日志/WAL 文件的 PVC

例如：

```output
NAME                   STATUS   VOLUME              CAPACITY   ACCESS MODES   STORAGECLASS    AGE
backups-postgres01-0   Bound    local-pv-485e37db   1938Gi     RWO            local-storage   6d6h
backups-postgres01-1   Bound    local-pv-9d3d4a15   1938Gi     RWO            local-storage   6d6h
backups-postgres01-2   Bound    local-pv-7b8dd819   1938Gi     RWO            local-storage   6d6h
...
data-postgres01-0      Bound    local-pv-3c1a8cc5   1938Gi     RWO            local-storage   6d6h
data-postgres01-1      Bound    local-pv-8303ab19   1938Gi     RWO            local-storage   6d6h
data-postgres01-2      Bound    local-pv-55572fe6   1938Gi     RWO            local-storage   6d6h
...
logs-postgres01-0      Bound    local-pv-5e852b76   1938Gi     RWO            local-storage   6d6h
logs-postgres01-1      Bound    local-pv-55d309a7   1938Gi     RWO            local-storage   6d6h
logs-postgres01-2      Bound    local-pv-5ccd02e6   1938Gi     RWO            local-storage   6d6h
...
```


## <a name="from-an-azure-arc-enabled-data-services-point-of-view"></a>从启用了 Azure Arc 的数据服务角度来看：

* 在 Arc 数据控制器中创建了多少个服务器组？
* 它们的名称是什么？
* 它们使用多少辅助角色节点？
* 它们运行的 Postgres 版本是什么？

使用以下命令之一。
- **With kubectl：**

   ```console
   kubectl get postgresqls [-n <namespace name>]
   ``` 

   例如，它将生成以下输出，其中每行都是 `servergroup` 。 下面显示的名称的结构如下所示：

   `<Name-Of-Custom-Resource-Definition>`/`<Server-Group-Name>`

   ```output
   NAME                                             STATE   READY-PODS   EXTERNAL-ENDPOINT   AGE
   postgresql-12.arcdata.microsoft.com/postgres01   Ready   3/3          10.0.0.4:30499      7h15m
   postgresql-12.arcdata.microsoft.com/postgres02   Ready   3/3          10.0.0.4:31066      6d7h
   ```

   上面的输出显示2个 Postgres 版本为12的服务器组。 如果版本为 Postgres 11，则 .CRD 的名称将为 postgresql-11.arcdata.microsoft.com。

   其中每个在3个节点上运行：1个协调器和2个辅助角色。

- **With azdata：**

运行以下命令。 输出显示的信息类似于 kubectl 显示的信息：

   ```console
   azdata arc postgres server list

   `output
   Name        State    Workers
   ----------  -------  ---------
   postgres01  Ready    2
   postgres02  Ready    2
   ```


### <a name="how-much-memory-and-vcores-are-being-used-and-what-extensions-were-created-for-a-group"></a>正在使用多少内存和 Vcore 以及为某个组创建了哪些扩展？

运行以下命令之一

**With Kubectl：**

使用 kubectl 描述 Postgres 资源。 为此，需要 (名称 (.CRD) 为相应的 Postgres 版本，如上文所示) 和服务器组的名称。
此命令的常规格式为：

```console
kubectl describe <CRD name>/<server group name> [-n <namespace name>]
```

例如：

```console
kubectl describe postgresql-12/postgres02
```

此命令显示服务器组的配置：

```output
Name:         postgres02
Namespace:    arc
Labels:       <none>
Annotations:  <none>
API Version:  arcdata.microsoft.com/v1alpha1
Kind:         postgresql-12
Metadata:
  Creation Timestamp:  2020-08-31T21:01:07Z
  Generation:          10
  Resource Version:    569516
  Self Link:           /apis/arcdata.microsoft.com/v1alpha1/namespaces/arc/postgresql-12s/postgres02
  UID:                 8a9cd118-361b-4a2e-8a9d-5f9257bf6abb
Spec:
  Backups:
    Delta Minutes:  3
    Full Minutes:   10
    Tiers:
      Retention:
        Maximums:
          6
          512MB
        Minimums:
          3
      Storage:
        Volume Size:  1Gi
  Engine:
    Extensions:
      Name:  citus
      Name:  pg_stat_statements
  Scale:
    Shards:  2
  Scheduling:
    Default:
      Resources:
        Limits:
          Cpu:     4
          Memory:  1024Mi
        Requests:
          Cpu:     1
          Memory:  512Mi
  Service:
    Type:  NodePort
  Storage:
    Data:
      Class Name:  local-storage
      Size:        5Gi
    Logs:
      Class Name:  local-storage
      Size:        5Gi
Status:
  External Endpoint:  10.0.0.4:31066
  Ready Pods:         3/3
  State:              Ready
Events:               <none>
```

让我们来了解上面所示的说明中的一些具体相关要点 `servergroup` 。 它告诉我们有关此服务器组的内容是什么？

- 这是 Postgres 的版本12： 
   > 好         `postgresql-12`
- 它是在2020年8月创建的：
   > 创建时间戳：  `2020-08-31T21:01:07Z`
- 此服务器组中创建了两个 Postgres 扩展： `citus` 和 `pg_stat_statements`
   > Engine：扩展：名称：  `citus` 名称：  `pg_stat_statements`
- 它使用两个辅助角色节点
   > Scale：分片：  `2`
- 保证每个节点使用1个 cpu/vCore 和 512MB Ram。 它将使用4个以上的 cpu/Vcore 和1024MB 内存：
   > 计划：默认：资源：限制： Cpu：4内存：1024Mi 请求： Cpu：1内存：512Mi
 - 它可用于查询，没有任何问题。 所有节点都已启动并正在运行：
   > 状态： .。。Ready 盒：3/3 状态：就绪

**With azdata：**

命令的常规格式为：

```console
azdata arc postgres server show -n <server group name>
```

例如：

```console
azdata arc postgres server show -n postgres02
```

返回下面的输出，格式与内容非常类似于 kubectl 返回的输出。

```output
{
  "apiVersion": "arcdata.microsoft.com/v1alpha1",
  "kind": "postgresql-12",
  "metadata": {
    "creationTimestamp": "2020-08-31T21:01:07Z",
    "generation": 10,
    "name": "postgres02",
    "namespace": "arc",
    "resourceVersion": "569516",
    "selfLink": "/apis/arcdata.microsoft.com/v1alpha1/namespaces/arc/postgresql-12s/postgres02",
    "uid": "8a9cd118-361b-4a2e-8a9d-5f9257bf6abb"
  },
  "spec": {
    "backups": {
      "deltaMinutes": 3,
      "fullMinutes": 10,
      "tiers": [
        {
          "retention": {
            "maximums": [
              "6",
              "512MB"
            ],
            "minimums": [
              "3"
            ]
          },
          "storage": {
            "volumeSize": "1Gi"
          }
        }
      ]
    },
    "engine": {
      "extensions": [
        {
          "name": "citus"
        },
        {
          "name": "pg_stat_statements"
        }
      ]
    },
    "scale": {
      "shards": 2
    },
    "scheduling": {
      "default": {
        "resources": {
          "limits": {
            "cpu": "4",
            "memory": "1024Mi"
          },
          "requests": {
            "cpu": "1",
            "memory": "512Mi"
          }
        }
      }
    },
    "service": {
      "type": "NodePort"
    },
    "storage": {
      "data": {
        "className": "local-storage",
        "size": "5Gi"
      },
      "logs": {
        "className": "local-storage",
        "size": "5Gi"
      }
    }
  },
  "status": {
    "externalEndpoint": "10.0.0.4:31066",
    "readyPods": "3/3",
    "state": "Ready"
  }
}
```

## <a name="next-steps"></a>后续步骤
- [了解 Azure Arc enabled PostgreSQL 超大规模的概念](concepts-distributed-postgres-hyperscale.md)
- [阅读有关如何横向扩展 () 服务器组添加辅助角色节点](scale-out-postgresql-hyperscale-server-group.md)
- [了解如何扩展/缩减 (增加或减少) 服务器组的内存和/或 Vcore](scale-up-down-postgresql-hyperscale-server-group-using-cli.md)
- [了解存储配置](storage-configuration.md)
- [阅读如何监视数据库实例](monitor-grafana-kibana.md)
- [在启用了 Azure Arc 的 PostgreSQL 超大规模服务器组中使用 PostgreSQL 扩展](using-extensions-in-postgresql-hyperscale-server-group.md)
- [为已启用 Azure Arc 的 PostgreSQL 超大规模服务器组配置安全性](configure-security-postgres-hyperscale.md)
