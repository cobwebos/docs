---
title: 删除 Azure Arc 数据控制器
description: 删除 Azure Arc 数据控制器
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 7e1a2d50e04601b977bb7a708f60e78089ddded1
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2020
ms.locfileid: "91631012"
---
# <a name="delete-azure-arc-data-controller"></a>删除 Azure Arc 数据控制器

以下文章介绍了如何删除 Azure Arc 数据控制器。

在继续操作之前，请确保删除了在数据控制器上创建的所有数据服务，如下所示：

## <a name="log-in-to-the-data-controller"></a>登录到数据控制器

登录到要删除的数据控制器：

```
azdata login
```

## <a name="list--delete-existing-data-services"></a>列出 & 删除现有数据服务

运行以下命令，检查是否已创建任何 SQL 托管实例：

```
azdata arc sql mi list
```

对于上述列表中的每个 SQL 托管实例，运行删除命令，如下所示：

```
azdata arc sql mi delete -n <name>
# for example: azdata arc sql mi delete -n sqlinstance1
```

同样，若要检查 PostgreSQL 超大规模实例，请运行：

```
azdata arc postgres server list
```

对于每个 PostgreSQL 超大规模实例，请按如下所示运行 delete 命令：
```
azdata arc postgres server delete -n <name>
# for example: azdata arc postgres server delete -n pg1
```

## <a name="delete-controller"></a>删除控制器

删除所有 SQL 托管实例和 PostgreSQL 超大规模实例后，可以按如下所示删除数据控制器：

```
azdata arc dc delete -n <name> -ns <namespace>
# for example: azdata arc dc delete -ns arc -n arcdc
```

### <a name="remove-sccs-red-hat-openshift-only"></a>删除 Scc (Red Hat OpenShift 仅) 

```console
oc adm policy remove-scc-from-user privileged -z default -n arc
oc adm policy remove-scc-from-user anyuid     -z default -n arc
```

### <a name="delete-cluster-level-objects"></a>删除群集级别对象

除了命名空间范围内的对象之外，如果还想要删除群集级别对象（如 CRDs、 `clusterroles` 和），请 `clusterrolebindings` 运行以下命令：

```
# Cleanup azure arc data service artifacts
kubectl delete crd datacontrollers.arcdata.microsoft.com 
kubectl delete crd sqlmanagedinstances.sql.arcdata.microsoft.com 
kubectl delete crd postgresql-11s.arcdata.microsoft.com 
kubectl delete crd postgresql-12s.arcdata.microsoft.com
kubectl delete clusterroles azure-arc-data:cr-arc-metricsdc-reader
kubectl delete clusterrolebindings azure-arc-data:crb-arc-metricsdc-reader
```

### <a name="optionally-delete-the-azure-arc-data-controller-namespace"></a>（可选）删除 Azure Arc 数据控制器命名空间


```console
kubectl delete ns <nameSpecifiedDuringCreation>
# for example kubectl delete ns arc
```

## <a name="next-steps"></a>后续步骤

[什么是启用了 Azure Arc 的数据服务？](overview.md)
