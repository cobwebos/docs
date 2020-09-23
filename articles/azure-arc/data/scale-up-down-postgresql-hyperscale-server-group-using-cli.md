---
title: '使用 CLI (azdata 或 kubectl，向上和向下缩放 Azure Database for PostgreSQL 超大规模服务器组) '
description: '使用 CLI (azdata 或 kubectl，向上和向下缩放 Azure Database for PostgreSQL 超大规模服务器组) '
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: b96f38d04fe3e3cb59fa75424ae588fe0e38f510
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90933516"
---
# <a name="scale-up-and-down-an-azure-database-for-postgresql-hyperscale-server-group-using-cli-azdata-or-kubectl"></a>使用 CLI (azdata 或 kubectl，向上和向下缩放 Azure Database for PostgreSQL 超大规模服务器组) 



有时可能需要更改服务器组的特征或定义。 例如：

- 增加或减少每个协调器或辅助角色节点使用的 Vcore 数
- 增加或减少每个协调器或辅助角色节点使用的内存

本指南说明如何缩放 vCore 和/或内存。

增加或减少服务器组的 vCore 或内存设置意味着可以设置每个 vCore 和内存设置的最小值和/或最大值。 如果要将服务器组配置为使用特定数量的 vCore 或特定数量的内存，请将最小设置设置为等于最大设置。

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="show-the-current-definition-of-the-server-group"></a>显示服务器组的当前定义

若要显示服务器组的当前定义并查看当前的 vCore 和内存设置，请运行以下命令之一：

### <a name="cli-with-azdata"></a>CLI 和 azdata

```console
azdata arc postgres server show -n <server group name>
```
### <a name="cli-with-kubectl"></a>CLI 和 kubectl

```console
kubectl describe postgresql-12/<server group name> [-n <namespace name>]
```
> [!NOTE]
> 如果创建了 PostgreSQL 版本11的服务器组，请改为运行 `kubectl describe postgresql-11/<server group name>` 。

它将返回服务器组的配置。 如果已使用默认设置创建了服务器组，则应会看到如下所示的定义：

```console
"scheduling": {
      "default": {
        "resources": {
          "requests": {
            "memory": "256Mi"
          }
        }
      }
    },
```

## <a name="interpret-the-definition-of-the-server-group"></a>解释服务器组的定义

在服务器组的定义中，将每个节点的最小/最大 vCore 设置的部分和每个节点的最小/最大内存设置为 **"计划"** 部分。 在该部分中，最大设置将保留在名为 **"限制"** 的子节中，最小设置保存在名为 **"请求"** 的子节中。

如果设置了与最大设置不同的最小设置，则配置可保证在需要时为服务器组分配所需的资源。 它不会超过您设置的限制。

服务器组实际使用的资源 (Vcore 和内存) 最大为最大设置，并且取决于工作负荷和群集上可用的资源。 如果未将设置上限到最大值，则服务器组可能会使用 Kubernetes 群集分配给服务器组的 Kubernetes 节点的所有资源。

这些 vCore 和内存设置适用于)  (协调器节点和辅助节点的每个 PostgreSQL 超大规模节点。 尚不支持分别设置协调器节点的定义和辅助角色节点。

在默认配置中，仅将最小内存设置为256Mi，因为它是运行 PostgreSQL 超大规模时建议使用的最小内存量。

> [!NOTE]
> 设置最小值并不意味着服务器组将至少使用该最小值。 这意味着，如果服务器组需要该服务，则保证至少分配此最低级别。 例如，让我们考虑一下 `--minCpu 2` 。 这并不意味着服务器组始终使用至少2个 Vcore。 这意味着，如果服务器组不需要这么多，就可以开始使用少于2个 Vcore，并且在以后需要时至少分配2个 Vcore。 这意味着，Kubernetes 群集将资源分配给其他工作负荷，使其能够在需要时将2个 Vcore 分配给服务器组。

>[!NOTE]
>在修改系统的配置之前，请确保在[此处](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)熟悉 Kubernetes 资源模型

## <a name="scale-up-the-server-group"></a>向上扩展服务器组

要设置的设置必须在为 Kubernetes 群集设置的配置中考虑。 请确保未设置 Kubernetes 群集无法满足的值。 这可能导致错误或不可预测的行为。 例如，如果在更改配置后，服务器组的状态长时间处于 " _正在更新_ " 状态，则可能表示将以下参数设置为 Kubernetes 群集无法满足的值。 如果是这种情况，请还原更改或读取 _troubleshooting_section。

假设你要将服务器组的定义向上扩展到：

- 最小 vCore = 2
- Max vCore = 4
- 最小内存 = 512Mb
- 最大内存 = 1Gb

您可以使用以下方法之一：

### <a name="cli-with-azdata"></a>CLI 和 azdata

```console
azdata arc postgres server edit -n <name of your server group> --cores-request 2  --cores-limit 4  --memory-request 512Mi  --memory-limit 1024Mi
```

命令在显示以下内容时成功执行：

```console
<name of your server group> is Ready
```

> [!NOTE]
> 有关这些参数的详细信息，请运行 `azdata arc postgres server edit --help` 。

### <a name="cli-with-kubectl"></a>CLI 和 kubectl

```console
kubectl edit postgresql-12/<server group name> [-n <namespace name>]
```

这会将你带到 vi 编辑器中，你可以在其中导航和更改配置。 使用以下内容将所需的设置映射到该规范中的字段名称：

- Min vCore = 2-> scheduling\default\resources\requests\cpu
- Max vCore = 4-> scheduling\default\resources\limits\cpu
- 最小内存 = 512Mb-> scheduling\default\resources\requests\cpu
- 最大内存 = 1Gb-> scheduling\default\resources\limits\cpu

如果你不熟悉 vi 编辑器，请参阅 [此处](https://www.computerhope.com/unix/uvi.htm)所需命令的说明：
- 编辑模式： `i`
- 用箭头移动
- _stop 编辑： `esc`
- _exit 而不保存： `:qa!`
- 保存后 _exit： `:qw!`


## <a name="show-the-scaled-up-definition-of-the-server-group"></a>显示服务器组的向上扩展定义

再次运行命令以显示服务器组的定义，并验证其是否已设置为所需的设置：

### <a name="cli-with-azdata"></a>CLI 和 azdata

```console
azdata arc postgres server show -n <the name of your server group>
```
### <a name="cli-with-kubectl"></a>CLI 和 kubectl

```console
kubectl describe postgresql-12/<server group name>  [-n <namespace name>]
```
> [!NOTE]
> 如果创建了 PostgreSQL 版本11的服务器组，请改为运行 `kubectl describe postgresql-11/<server group name>` 。


它将显示服务器组的新定义：

```console
"scheduling": {
      "default": {
        "resources": {
          "limits": {
            "cpu": "4",
            "memory": "1024Mi"
          },
          "requests": {
            "cpu": "2",
            "memory": "512Mi"
          }
        }
      }
    },
```

## <a name="scale-down-the-server-group"></a>缩小服务器组

若要缩减服务器组，请执行相同的命令，但为要缩减的设置设置较小的值。 若要删除请求和/或限制，请将其值指定为空字符串。

## <a name="next-steps"></a>后续步骤

- [横向扩展 Azure Database for PostgreSQL 超大规模服务器组](scale-out-postgresql-hyperscale-server-group.md)
- [存储配置和 Kubernetes 存储概念](storage-configuration.md)
- [扩展永久性卷声明](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#expanding-persistent-volumes-claims)
- [Kubernetes 资源模型](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)
