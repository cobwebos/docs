---
title: 列出在 Azure Arc 数据控制器中创建的启用了 Azure Arc 的 PostgreSQL 超大规模服务器组
description: 列出在 Azure Arc 数据控制器中创建的启用了 Azure Arc 的 PostgreSQL 超大规模服务器组
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 7577ca4b8a1d7db7ea99aadfef4fd2a445b66425
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90934823"
---
# <a name="list-the-azure-arc-enabled-postgresql-hyperscale-server-groups-created-in-an-azure-arc-data-controller"></a>列出在 Azure Arc 数据控制器中创建的启用了 Azure Arc 的 PostgreSQL 超大规模服务器组

本文介绍如何检索在 Arc 数据控制器中创建的服务器组的列表。

若要检索此列表，请在连接到 Arc 数据控制器后使用以下两种方法之一：

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="from-cli-with-azdata"></a>通过 azdata 的 CLI
命令的常规格式为：
```console
azdata arc postgres server list
```

它将返回如下所示的输出：
```console
Name        State    Workers
----------  -------  ---------
postgres01  Ready    2
postgres02  Ready    2
```
有关可用于此命令的参数的详细信息，请运行：
```console
azdata arc postgres server list --help
```

## <a name="from-cli-with-kubectl"></a>通过 kubectl 的 CLI
运行以下命令之一。

**若要列出不考虑 Postgres 版本的服务器组，请运行：**
```console
kubectl get postgresqls
```
它将返回如下所示的输出：
```console
NAME                                             STATE   READY-PODS   EXTERNAL-ENDPOINT   AGE
postgresql-12.arcdata.microsoft.com/postgres01   Ready   3/3          10.0.0.4:30499      51s
postgresql-12.arcdata.microsoft.com/postgres02   Ready   3/3          10.0.0.4:31066      6d
```

**若要列出 Postgres 的特定版本的服务器组，请运行：**
```console
kubectl get postgresql-12
```

若要列出运行第11版 Postgres 的服务器组，请将 _postgresql-12_ 替换为 _postgresql-11_。

## <a name="next-steps"></a>后续步骤：

* [阅读有关如何获取连接终结点和形成连接字符串以连接到服务器组的文章](get-connection-endpoints-and-connection-strings-postgres-hyperscale.md)
* [阅读有关显示 Azure Arc enabled PostgreSQL 超大规模服务器组的配置的文章](show-configuration-postgresql-hyperscale-server-group.md)
