---
title: 删除启用了 Azure Arc 的 PostgreSQL 超大规模服务器组
description: 删除启用了 Azure Arc 的 Postgres 超大规模服务器组
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: dcabe4b1520c66b8d5bfa398dc1248972587cd32
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90934825"
---
# <a name="delete-an-azure-arc-enabled-postgresql-hyperscale-server-group"></a>删除启用了 Azure Arc 的 PostgreSQL 超大规模服务器组

本文档介绍了从 Azure Arc 设置中删除服务器组的步骤。

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="delete-the-server-group"></a>删除服务器组

例如，我们想要从以下安装程序中删除 _postgres01_ 实例：

```console
azdata arc postgres server list
Name        State    Workers
----------  -------  ---------
postgres01  Ready    3
```

Delete 命令的常规格式为：
```console
azdata arc postgres server delete -n <server group name>
```
有关 delete 命令的详细信息，请运行：
```console
azdata arc postgres server delete --help
```

### <a name="lets-delete-the-server-group-used-in-this-example"></a>删除本示例中使用的服务器组：
```console
azdata arc postgres server delete -n postgres01
```

## <a name="reclaim-the-kubernetes-persistent-volume-claims-pvcs"></a>回收 Kubernetes 永久性卷声明 (Pvc) 

删除服务器组不会删除其关联的 [pvc](https://kubernetes.io/docs/concepts/storage/persistent-volumes/)。 这是设计的结果。 目的是帮助用户访问数据库文件，以防意外删除实例。 删除 Pvc 不是必需的。 但建议使用。 如果不回收这些 Pvc，最终会出现错误，因为 Kubernetes 群集会认为磁盘空间不足。 若要收回 Pvc，请执行以下步骤：

### <a name="1-list-the-pvcs-for-the-server-group-you-deleted"></a>1. 列出已删除的服务器组的 Pvc
若要列出 Pvc，请运行以下命令：
```console
kubectl get pvc [-n <namespace name>]
```

它将返回 PVSs 的列表，特别是删除的服务器组的 Pvc。 例如：
```console
kubectl get pvc
NAME                STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
data-postgres01-0   Bound    pvc-72ccc225-dad0-4dee-8eae-ed352be847aa   5Gi        RWO            default        2d18h
data-postgres01-1   Bound    pvc-ce6f0c51-faed-45ae-9472-8cdf390deb0d   5Gi        RWO            default        2d18h
data-postgres01-2   Bound    pvc-5a863ab9-522a-45f3-889b-8084c48c32f8   5Gi        RWO            default        2d18h
data-postgres01-3   Bound    pvc-00e1ace3-1452-434f-8445-767ec39c23f2   5Gi        RWO            default        2d15h
logs-postgres01-0   Bound    pvc-8b810f4c-d72a-474a-a5d7-64ec26fa32de   5Gi        RWO            default        2d18h
logs-postgres01-1   Bound    pvc-51d1e91b-08a9-4b6b-858d-38e8e06e60f9   5Gi        RWO            default        2d18h
logs-postgres01-2   Bound    pvc-8e5ad55e-300d-4353-92d8-2e383b3fe96e   5Gi        RWO            default        2d18h
logs-postgres01-3   Bound    pvc-f9e4cb98-c943-45b0-aa07-dd5cff7ea585   5Gi        RWO            default        2d15h
```
此服务器组有8个 Pvc。

### <a name="2-delete-each-of-the-pvcs"></a>2. 删除每个 Pvc
删除已删除的服务器组 (协调器和工作线程) 的每个 PostgreSQL 超大规模节点的数据和日志 Pvc。
此命令的常规格式为： 
```console
kubectl delete pvc <name of pvc>  [-n <namespace name>]
```

例如：
```console
kubectl delete pvc data-postgres01-0
kubectl delete pvc data-postgres01-1 
kubectl delete pvc data-postgres01-2
kubectl delete pvc data-postgres01-3
kubectl delete pvc logs-postgres01-0
kubectl delete pvc logs-postgres01-1
kubectl delete pvc logs-postgres01-2
kubectl delete pvc logs-postgres01-3
```

其中每个 kubectl 命令都将确认成功删除 PVC。 例如：
```console
persistentvolumeclaim "data-postgres01-0" deleted
```
  

>**注意** 如前所述，如果不删除这些 Pvc，可能最终会在出现错误的情况下获取 Kubernetes 群集。 其中的一些错误可能包括无法通过 azdata 登录到 Kubernetes 群集，因为此存储问题 (正常 Kubernetes 行为) 。
>
> 例如，你可能会在日志中看到类似于以下内容的消息：  
    > 批注： microsoft.com/ignore-pod-health： true  
    > 状态：失败  
    > 原因：逐出  
    > 消息：节点资源不足：临时存储。 容器控制器使用的是16372Ki，超过了其请求的0。
    
## <a name="next-step"></a>下一步
创建 [启用 Azure Arc 的 PostgreSQL 超大规模](create-postgresql-hyperscale-server-group.md)
