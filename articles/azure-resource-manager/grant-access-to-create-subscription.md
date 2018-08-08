---
title: 授予创建 Azure Enterprise 订阅所需的访问权限 | Microsoft Docs
description: 了解如何使用户或服务主体能够以编程方式创建 Azure Enterprise 订阅。
services: azure-resource-manager
author: adpick
manager: adpick
editor: ''
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/05/2018
ms.author: adpick
ms.openlocfilehash: 86e457cf553c84386937c35bab1ab0fd20518bed
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2018
ms.locfileid: "39368505"
---
# <a name="grant-access-to-create-azure-enterprise-subscriptions-preview"></a>授予创建 Azure Enterprise 订阅（预览版）所需的访问权限

作为[企业协议 (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) 的 Azure 客户，你可以授予其他用户或服务主体创建计费到你的帐户的订阅所需的权限。 本文介绍如何使用[基于角色的访问控制 (RBAC)](../active-directory/role-based-access-control-configure.md) 共享创建订阅的功能，以及如何审核订阅创建。 必须在想要共享的帐户上具备所有者角色。

要创建订阅，请参阅[以编程方式创建 Azure Enterprise 订阅（预览版）](programmatically-create-subscription.md)。

## <a name="delegate-access-to-an-enrollment-account-using-rbac"></a>委托访问使用 RBAC 的注册帐户的权限

若要为另一个用户或服务主体提供能够创建针对特定帐户的订阅的能力，请[提供他们在注册帐户范围内的 RBAC 所有者角色](../active-directory/role-based-access-control-manage-access-rest.md)。 下面的示例向 `principalId` 为 `<userObjectId>`（针对 SignUpEngineering@contoso.com）的租户用户提供注册帐户的所有者角色。 若要查找注册帐户 ID 和主体 ID，请参阅[以编程方式创建 Azure Enterprise 订阅（预览版）](programmatically-create-subscription.md)。

# <a name="resttabrest"></a>[REST](#tab/rest)

```json
PUT  https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx/providers/Microsoft.Authorization/roleAssignments/<roleAssignmentGuid>?api-version=2015-07-01

{
  "properties": {
    "roleDefinitionId": "/providers/Microsoft.Billing/enrollmentAccounts/providers/Microsoft.Authorization/roleDefinitions/<ownerRoleDefinitionId>",
    "principalId": "<userObjectId>"
  }
}
```
所有者角色在注册帐户范围成功分配时，Azure 使用角色分配信息做出响应：

```json
{
  "properties": {
    "roleDefinitionId": "/providers/Microsoft.Billing/enrollmentAccounts/providers/Microsoft.Authorization/roleDefinitions/<ownerRoleDefinitionId>",
    "principalId": "<userObjectId>",
    "scope": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "createdOn": "2018-03-05T08:36:26.4014813Z",
    "updatedOn": "2018-03-05T08:36:26.4014813Z",
    "createdBy": "<assignerObjectId>",
    "updatedBy": "<assignerObjectId>"
  },
  "id": "/providers/Microsoft.Billing/enrollmentAccounts/providers/Microsoft.Authorization/roleDefinitions/<ownerRoleDefinitionId>",
  "type": "Microsoft.Authorization/roleAssignments",
  "name": "<roleAssignmentGuid>"
}
```

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

使用 [New-AzureRmRoleAssignment](../active-directory/role-based-access-control-manage-access-powershell.md) 为其他用户所有者提供访问注册帐户的权限。

```azurepowershell-interactive
New-AzureRmRoleAssignment -RoleDefinitionName Owner -ObjectId <userObjectId> -Scope /providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

使用 [az role assignment create](../active-directory/role-based-access-control-manage-access-azure-cli.md) 为其他用户所有者提供访问注册帐户的权限。

```azurecli-interactive 
az role assignment create --role Owner --assignee-object-id <userObjectId> --scope /providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

----

用户成为注册帐户的 RBAC 所有者之后，他们即可以编程方式在该帐户下创建订阅。 委托用户创建的订阅仍有作为服务管理员的原始帐户所有者，但它默认情况下也有作为所有者的委托用户。 

## <a name="audit-who-created-subscriptions-using-activity-logs"></a>使用活动日志审核订阅的创建者

若要通过此 API 跟踪创建的订阅，请使用[租户活动日志 API](/rest/api/monitor/tenantactivitylogs)。 目前不可以使用 PowerShell、CLI 或 Azure 门户跟踪订阅创建信息。

1. Azure AD 租户的租户管理员可以[提升访问权限](../active-directory/role-based-access-control-tenant-admin-access.md)，然后将一个“读者”角色分配给 `/providers/microsoft.insights/eventtypes/management` 范围内的审核用户。
1. 审核用户可以调用[租户活动日志 API](/rest/api/monitor/tenantactivitylogs) 查看订阅创建活动。 示例：

```
GET "/providers/Microsoft.Insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '{greaterThanTimeStamp}' and eventTimestamp le '{lessThanTimestamp}' and eventChannels eq 'Operation' and resourceProvider eq 'Microsoft.Subscription'" 
```

> [!NOTE]
> 若要快速从命令行调用此 API，请尝试使用 [ARMClient](https://github.com/projectkudu/ARMClient)。

## <a name="next-steps"></a>后续步骤

* 既然用户或服务主体有权创建订阅，你可以使用该标识[以编程方式创建 Azure Enterprise 订阅](programmatically-create-subscription.md)。
* 有关使用 .NET 创建订阅的示例，请参阅 [GitHub 上的示例代码](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core)。
* 若要了解有关 Azure 资源管理器及其 API 的详细信息，请参阅 [Azure 资源管理器概述](resource-group-overview.md)。
* 要详细了解如何使用管理组管理大量订阅，请参阅[使用 Azure 管理组整理资源](management-groups-overview.md)
* 要查看有关大型组织订阅管理的综合最佳做法指南，请参阅 [Azure 企业基架 - 规范性订阅管理](/azure/architecture/cloud-adoption-guide/subscription-governance)
