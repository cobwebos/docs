---
title: 支持的版本 Postgres 启用了 Azure Arc PostgreSQL 超大规模
description: 支持的版本 Postgres 启用了 Azure Arc PostgreSQL 超大规模
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 0f3f9e7f4566800bdea6871cae1c5bafb3b37b95
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90934349"
---
# <a name="supported-versions-of-postgres-with-azure-arc-enabled-postgresql-hyperscale"></a>将支持的 Postgres 版本与已启用 Azure Arc 的 PostgreSQL 超大规模配合使用

本文介绍了哪些版本的 Postgres 可用于启用了 Azure Arc PostgreSQL 超大规模。
受支持版本的列表会随着时间的推移而变化。 目前，支持的主要版本如下：
- Postgres 12 (默认值) 
- Postgres 11

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="how-to-chose-between-versions"></a>如何在版本之间进行选择？
建议你查看应用程序的设计版本，以及每个版本的功能。 若要了解详细信息，请阅读官方 Postgres 站点上的每个版本：
- [Postgres 12 (默认值) ](https://www.postgresql.org/docs/12/index.html)
- [Postgres 11](https://www.postgresql.org/docs/11/index.html)

## <a name="how-to-create-a-particular-version-in-azure-arc-enabled-postgresql-hyperscale"></a>如何在启用的 Azure Arc 中创建特定版本 PostgreSQL 超大规模？
在创建时，可以通过传递 _--engine 版本_ 参数来指示要创建的版本。 如果不指示版本信息，则默认情况下将创建 Postgres 版本12的服务器组。

## <a name="how-do-be-notified-when-other-versions-are-available"></a>其他版本可用时如何通知？
返回并阅读本文。 它将相应地进行更新。 你还可以在 Kubernetes 群集中的 Arc 数据控制器 (.CRD) 中列出自定义资源定义的类型。
运行以下命令：
```console
kubectl get crds
```

它将返回如下所示的输出：
```console
NAME                                            CREATED AT
datacontrollers.arcdata.microsoft.com           2020-08-31T20:15:16Z
postgresql-11s.arcdata.microsoft.com            2020-08-31T20:15:16Z
postgresql-12s.arcdata.microsoft.com            2020-08-31T20:15:16Z
sqlmanagedinstances.sql.arcdata.microsoft.com   2020-08-31T20:15:16Z
```

在此示例中，此输出指示有两种类型的 .CRD 与 Postgres 相关：
- postgresql-12s.arcdata.microsoft.com 是 Postgres 12 的 .CRD
- postgresql-11s.arcdata.microsoft.com 是 Postgres 11 的 .CRD

这些是 CRDs，而不是服务器组。 存在 .CRD 的情况并不表示你具有（或未创建）该版本的服务器组。
.CRD 是可创建的资源类型的指示。

## <a name="next-steps"></a>后续步骤：
- [了解如何创建启用 Azure Arc 的 PostgreSQL 超大规模](create-postgresql-hyperscale-server-group.md)
- [了解如何获取在 Arc 数据控制器中创建的启用了 Azure Arc PostgreSQL 超大规模服务器组的列表](list-server-groups-postgres-hyperscale.md)
