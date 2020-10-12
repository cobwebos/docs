---
title: 删除启用了 Azure Arc 的 SQL 托管实例
description: 删除启用了 Azure Arc 的 SQL 托管实例
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: vin-yu
ms.author: vinsonyu
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: e531349e8f404380d9f0601caa3b66557c297062
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90934833"
---
# <a name="delete-azure-arc-enabled-sql-managed-instance"></a>删除启用了 Azure Arc 的 SQL 托管实例
本文介绍如何删除启用了 Azure Arc 的 SQL 托管实例。

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="view-existing-azure-arc-enabled-sql-managed-instances"></a>查看启用了现有 Azure Arc 的 SQL 托管实例
若要查看 SQL 托管实例，请运行以下命令：

```console
azdata arc sql mi list
```

输出应如下所示：

```console
Name    Replicas    ServerEndpoint    State
------  ----------  ----------------  -------
demo-mi 1/1         10.240.0.4:32023  Ready
```

## <a name="delete-a-azure-arc-enabled-sql-managed-instance"></a>删除启用了 Azure Arc 的 SQL 托管实例
若要删除 SQL 托管实例，请运行以下命令：

```console
azdata arc sql mi delete -n <NAME_OF_INSTANCE>
```

输出应如下所示：

```console
# azdata arc sql mi delete -n demo-mi
Deleted demo-mi from namespace arc
```

## <a name="reclaim-the-kubernetes-persistent-volume-claims-pvcs"></a>回收 Kubernetes 永久性卷声明 (Pvc) 

删除 SQL 托管实例不会删除其关联的 [pvc](https://kubernetes.io/docs/concepts/storage/persistent-volumes/)。 这是设计的结果。 目的是帮助用户访问数据库文件，以防意外删除实例。 删除 PVC 不是必需的， 但建议这样做。 如果不回收这些 Pvc，最终会出现错误，因为 Kubernetes 群集将会耗尽磁盘空间。 若要回收 PVC，请执行以下步骤：

### <a name="1-list-the-pvcs-for-the-server-group-you-deleted"></a>1. 列出已删除的服务器组的 Pvc
若要列出 Pvc，请运行以下命令：
```console
kubectl get pvc
```

在下面的示例中，请注意删除的 SQL 托管实例的 Pvc。
```console
# kubectl get pvc -n arc

NAME                    STATUS    VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS      AGE
data-demo-mi-0        Bound     pvc-1030df34-4b0d-4148-8986-4e4c20660cc4   5Gi        RWO            managed-premium   13h
logs-demo-mi-0        Bound     pvc-11836e5e-63e5-4620-a6ba-d74f7a916db4   5Gi        RWO            managed-premium   13h
```

### <a name="2-delete-each-of-the-pvcs"></a>2. 删除每个 Pvc
删除已删除的每个 SQL 托管实例的数据和日志 Pvc。
此命令的常规格式为： 
```console
kubectl delete pvc <name of pvc>
```

例如：
```console
kubectl delete pvc data-demo-mi-0 -n arc
kubectl delete pvc logs-demo-mi-0 -n arc
```

其中每个 kubectl 命令都将确认成功删除 PVC。 例如：
```console
persistentvolumeclaim "data-demo-mi-0" deleted
persistentvolumeclaim "logs-demo-mi-0" deleted
```
  

> [!NOTE]
> 如前所述，如果不删除这些 Pvc，可能最终会在出现错误的情况下获取 Kubernetes 群集。 其中的一些错误可能包括无法通过 azdata 登录到 Kubernetes 群集，因为此存储问题 (正常 Kubernetes 行为) 。
>
> 例如，你可能会在日志中看到类似于以下内容的消息：  
> - 批注： microsoft.com/ignore-pod-health： true  
> - 状态：失败  
> - 原因：逐出  
> - 消息：节点资源不足：临时存储。 容器控制器使用的是16372Ki，超过了其请求的0。

## <a name="next-steps"></a>后续步骤

了解有关[已启用 Azure Arc 的 SQL 托管实例的特性和功能](managed-instance-features.md)的详细信息

[首先创建数据控制器](create-data-controller.md)

已创建数据控制器？ [创建已启用 Azure Arc 的 SQL 托管实例](create-sql-managed-instance.md)