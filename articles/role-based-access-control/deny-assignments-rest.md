---
title: 使用 REST API 列出 Azure 拒绝分配-Azure RBAC
description: 了解如何使用 REST API 和 Azure 基于角色的访问控制（Azure RBAC）列出用户、组和应用程序的 Azure 拒绝分配。
services: active-directory
documentationcenter: na
author: rolyon
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: role-based-access-control
ms.workload: multiple
ms.tgt_pltfrm: rest-api
ms.devlang: na
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: dae0352566e6cb4f8ed1384ca12213e2aaa07f9d
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/03/2020
ms.locfileid: "82733864"
---
# <a name="list-azure-deny-assignments-using-the-rest-api"></a>使用 REST API 列出 Azure 拒绝分配

[Azure 拒绝分配](deny-assignments.md)会阻止用户执行特定的 Azure 资源操作，即使角色分配授予访问权限。 本文介绍如何使用 REST API 列出拒绝分配。

> [!NOTE]
> 不能直接创建自己的拒绝分配。 有关如何创建拒绝分配的信息，请参阅[Azure 拒绝分配](deny-assignments.md)。

## <a name="prerequisites"></a>必备条件

如要获取拒绝分配的相关信息，必须具有：

- `Microsoft.Authorization/denyAssignments/read`权限包括在大多数[Azure 内置角色](built-in-roles.md)中。

## <a name="list-a-single-deny-assignment"></a>列出单个拒绝分配

1. 从下面的请求开始：

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/denyAssignments/{deny-assignment-id}?api-version=2018-07-01-preview
    ```

1. 在 URI 中，将 {scope} 替换为要列出拒绝分配的范围  。

    > [!div class="mx-tableFixed"]
    > | 范围 | 类型 |
    > | --- | --- |
    > | `subscriptions/{subscriptionId}` | 订阅 |
    > | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | 资源组 |
    > | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1` | 资源 |

1. 将 {deny-assignment-id} 替换为要检索的拒绝分配标识符  。

## <a name="list-multiple-deny-assignments"></a>列出多个拒绝分配

1. 先处理下述请求之一：

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview
    ```

    具有可选参数：

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview&$filter={filter}
    ```

1. 在 URI 中，将 {scope} 替换为要列出拒绝分配的范围  。

    > [!div class="mx-tableFixed"]
    > | 范围 | 类型 |
    > | --- | --- |
    > | `subscriptions/{subscriptionId}` | 订阅 |
    > | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | 资源组 |
    > | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1` | 资源 |

1. 将 {filter} 替换为筛选拒绝分配列表时要应用的条件  。

    > [!div class="mx-tableFixed"]
    > | “筛选器” | 说明 |
    > | --- | --- |
    > | (无筛选器) | 列出指定范围之内、之上和之下的所有拒绝分配。 |
    > | `$filter=atScope()` | 仅列出指定范围内及其上的拒绝分配。 不包含子范围处的拒绝分配。 |
    > | `$filter=assignedTo('{objectId}')` | 列出指定用户或服务主体的拒绝分配。<br/>如果用户属于包含拒绝分配的组，则也会列出该拒绝分配。 此筛选器对于组是可传递的，这意味着如果用户是组的成员，并且该组是包含拒绝分配的另一个组的成员，则该拒绝分配也会列出。<br/>此筛选器仅接受用户或服务主体的对象 ID。 不能传递组的对象 ID。 |
    > | `$filter=atScope()+and+assignedTo('{objectId}')` | 列出指定范围内指定用户或服务主体的拒绝分配。 |
    > | `$filter=denyAssignmentName+eq+'{deny-assignment-name}'` | 列出具有指定名称的拒绝分配。 |
    > | `$filter=principalId+eq+'{objectId}'` | 列出指定用户、组或服务主体的拒绝分配。 |

## <a name="list-deny-assignments-at-the-root-scope-"></a>列出根范围 (/) 处的拒绝分配

1. 提升访问权限[以管理所有 Azure 订阅和管理组](elevate-access-global-admin.md)中所述的访问权限。

1. 使用以下请求：

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview&$filter={filter}
    ```

1. 将 {filter} 替换为筛选拒绝分配列表时要应用的条件  。 需使用筛选器。

    > [!div class="mx-tableFixed"]
    > | “筛选器” | 说明 |
    > | --- | --- |
    > | `$filter=atScope()` | 仅列出根范围处的拒绝分配。 不包含子范围处的拒绝分配。 |
    > | `$filter=denyAssignmentName+eq+'{deny-assignment-name}'` | 列出具有指定名称的拒绝分配。 |

1. 删除已提升的访问权限。

## <a name="next-steps"></a>后续步骤

- [了解 Azure 拒绝分配](deny-assignments.md)
- [提升访问权限以管理所有 Azure 订阅和管理组](elevate-access-global-admin.md)
- [Azure REST API 参考](/rest/api/azure/)
