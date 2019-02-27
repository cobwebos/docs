---
title: 使用 REST API 为 Azure 资源列出拒绝分配 - Azure | Microsoft Docs
description: 了解如何使用 Azure 资源基于角色的访问控制 (RBAC) 和 REST API 来列出用户、组和应用程序的拒绝分配。
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
ms.date: 09/24/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 29b8e0953109238b724cc8df9f456706f71a041e
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/18/2019
ms.locfileid: "56341617"
---
# <a name="list-deny-assignments-for-azure-resources-using-the-rest-api"></a>使用 REST API 列出 Azure 资源的拒绝分配

当前，拒绝分配为“只读”，且只能由 Azure 设置。 即使无法自行创建拒绝分配，也可列出拒绝分配，因为它们可能会影响你的有效权限。 本文介绍了如何使用 RBAC 和 REST API 列出拒绝分配。

## <a name="list-a-single-deny-assignment"></a>列出单个拒绝分配

1. 从下面的请求开始：

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/denyAssignments/{deny-assignment-id}?api-version=2018-07-01-preview
    ```

1. 在 URI 中，将 {scope} 替换为要列出拒绝分配的范围。

    | 范围 | Type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | 订阅 |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | 资源组 |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | 资源 |

1. 将 {deny-assignment-id} 替换为要检索的拒绝分配标识符。

## <a name="list-multiple-deny-assignments"></a>列出多个拒绝分配

1. 先处理下述请求之一：

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview
    ```

    具有可选参数：

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview&$filter={filter}
    ```

1. 在 URI 中，将 {scope} 替换为要列出拒绝分配的范围。

    | 范围 | Type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | 订阅 |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | 资源组 |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | 资源 |

1. 将 {filter} 替换为筛选拒绝分配列表时要应用的条件。

    | 筛选器 | 说明 |
    | --- | --- |
    | (无筛选器) | 列出指定范围处、之上和之下的所有拒绝分配。 |
    | `$filter=atScope()` | 仅列出指定范围及之上的拒绝分配。 不包含子范围处的拒绝分配。 |
    | `$filter=denyAssignmentName%20eq%20'{deny-assignment-name}'` | 列出具有指定名称的拒绝分配。 |

## <a name="list-deny-assignments-at-the-root-scope-"></a>列出根范围 (/) 处的拒绝分配

1. 按[提升 Azure Active Directory 中全局管理员的访问权限](elevate-access-global-admin.md)中所述提升访问权限。

1. 使用以下请求：

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview&$filter={filter}
    ```

1. 将 {filter} 替换为筛选拒绝分配列表时要应用的条件。 需使用筛选器。

    | 筛选器 | 说明 |
    | --- | --- |
    | `$filter=atScope()` | 仅列出根范围处的拒绝分配。 不包含子范围处的拒绝分配。 |
    | `$filter=denyAssignmentName%20eq%20'{deny-assignment-name}'` | 列出具有指定名称的拒绝分配。 |

1. 删除已提升的访问权限。

## <a name="next-steps"></a>后续步骤

- [了解 Azure 资源的拒绝分配](deny-assignments.md)
- [提升 Azure Active Directory 中全局管理员的访问权限](elevate-access-global-admin.md)
- [Azure REST API 参考](/rest/api/azure/)
