---
title: 授予创建 Azure Enterprise 订阅所需的访问权限 | Microsoft Docs
description: 了解如何使用户或服务主体能够以编程方式创建 Azure Enterprise 订阅。
services: azure-resource-manager
author: jureid
manager: jureid
editor: ''
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/09/2019
ms.author: jureid
ms.openlocfilehash: 742658e36da956c46bd932b59903e68786c65b93
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2019
ms.locfileid: "65794565"
---
# <a name="grant-access-to-create-azure-enterprise-subscriptions-preview"></a>授予创建 Azure Enterprise 订阅（预览版）所需的访问权限

作为[企业协议 (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) 的 Azure 客户，你可以授予其他用户或服务主体创建计费到你的帐户的订阅所需的权限。 本文介绍如何使用[基于角色的访问控制 (RBAC)](../active-directory/role-based-access-control-configure.md) 共享创建订阅的功能，以及如何审核订阅创建。 必须在想要共享的帐户上具备所有者角色。

若要创建订阅，请参阅[以编程方式创建 Azure Enterprise 订阅（预览版）](programmatically-create-subscription.md)。

## <a name="grant-subscription-creation-access-to-a-user-or-group"></a>订阅创建访问权限授予用户或组

若要创建的注册帐户下的订阅，用户必须具有[RBAC 所有者角色](../role-based-access-control/built-in-roles.md#owner)该帐户上。 您可以授予用户或一组用户上注册帐户的 RBAC 所有者角色通过执行以下步骤：

### <a name="1-get-the-object-id-of-the-enrollment-account-you-want-to-grant-access-to"></a>1.获取你想要授予访问权限的注册帐户的对象 ID

若要授予其他人上注册帐户的 RBAC 所有者角色，你必须是帐户所有者或帐户的 RBAC 所有者。

### <a name="resttabrest"></a>[REST](#tab/rest)

若要列出有权访问的所有注册帐户的请求：

```json
GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts?api-version=2018-03-01-preview
```

Azure 使用有访问权限的所有注册帐户列表做出响应：

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Billing/enrollmentAccounts",
      "properties": {
        "principalName": "SignUpEngineering@contoso.com"
      }
    },
    {
      "id": "/providers/Microsoft.Billing/enrollmentAccounts/4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Billing/enrollmentAccounts",
      "properties": {
        "principalName": "BillingPlatformTeam@contoso.com"
      }
    }
  ]
}
```

使用`principalName`属性来标识你想要授予的 RBAC 所有者访问权限的帐户。 复制`name`的该帐户。 例如，如果你想要授予的 RBAC 所有者访问权限SignUpEngineering@contoso.com注册帐户，会将复制```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```。 这是注册帐户的对象 ID。 将此值粘贴到某个位置，以便可以使用它作为在下一步`enrollmentAccountObjectId`。

### <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

打开[Azure Cloud Shell](https://shell.azure.com/)并选择 PowerShell。

使用 [Get-AzEnrollmentAccount](/powershell/module/az.billing/get-azenrollmentaccount) cmdlet 列出你可以访问的所有注册帐户。

```azurepowershell-interactive
Get-AzEnrollmentAccount
```

Azure 使用有权访问注册帐户列表做出响应：

```azurepowershell
ObjectId                               | PrincipalName
747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | SignUpEngineering@contoso.com
4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | BillingPlatformTeam@contoso.com
```

使用`principalName`属性来标识你想要授予的 RBAC 所有者访问权限的帐户。 复制`ObjectId`的该帐户。 例如，如果你想要授予的 RBAC 所有者访问权限SignUpEngineering@contoso.com注册帐户，会将复制```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```。 将某个位置，以便可以使用它作为下一步中粘贴此对象 ID `enrollmentAccountObjectId`。

### <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

使用 [az billing enrollment-account list](https://aka.ms/EASubCreationPublicPreviewCLI) 命令列出有访问权限的所有注册帐户。

```azurecli-interactive 
az billing enrollment-account list
```

Azure 使用有权访问注册帐户列表做出响应：

```json
[
  {
    "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "name": "747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "principalName": "SignUpEngineering@contoso.com",
    "type": "Microsoft.Billing/enrollmentAccounts",
  },
  {
    "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "name": "4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "principalName": "BillingPlatformTeam@contoso.com",
    "type": "Microsoft.Billing/enrollmentAccounts",
  }
]

```

使用`principalName`属性来标识你想要授予的 RBAC 所有者访问权限的帐户。 复制`name`的该帐户。 例如，如果你想要授予的 RBAC 所有者访问权限SignUpEngineering@contoso.com注册帐户，会将复制```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```。 这是注册帐户的对象 ID。 将此值粘贴到某个位置，以便可以使用它作为在下一步`enrollmentAccountObjectId`。

<a id="userObjectId"></a>

### <a name="2-get-object-id-of-the-user-or-group-you-want-to-give-the-rbac-owner-role-to"></a>2.获取用户或组你想要提供的 RBAC 所有者角色的对象 ID

1. 在 Azure 门户中，搜索**Azure Active Directory**。
1. 如果你想要授予用户访问权限，请单击**用户**在左侧菜单中。 如果你想要向组授予访问权限，请单击**组**。
1. 选择用户或组你想要提供的 RBAC 所有者角色。
1. 如果所选用户，您会发现的对象 ID 的配置文件页。 如果选择组时，对象 ID 将在概述页中。 复制**ObjectID**单击文本框右侧的图标。 粘贴此位置，这样您可以将其作为下一步`userObjectId`。

### <a name="3-grant-the-user-or-group-the-rbac-owner-role-on-the-enrollment-account"></a>3.授予用户或组上注册帐户的 RBAC 所有者角色

使用在前两个步骤中收集的值，授予用户或组上注册帐户的 RBAC 所有者角色。

### <a name="resttabrest-2"></a>[REST](#tab/rest-2)

运行以下命令，替换```<enrollmentAccountObjectId>```与`name`第一步中复制 (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```)。 替换为```<userObjectId>```与第二个步骤中复制的对象 ID。

```json
PUT  https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>/providers/Microsoft.Authorization/roleAssignments/<roleAssignmentGuid>?api-version=2015-07-01

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

### <a name="powershelltabazure-powershell-2"></a>[PowerShell](#tab/azure-powershell-2)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

运行以下[新建 AzRoleAssignment](../active-directory/role-based-access-control-manage-access-powershell.md)命令，用```<enrollmentAccountObjectId>```与`ObjectId`收集的第一步中 (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```)。 替换为```<userObjectId>```使用的对象 ID 将收集在第二个步骤。

```azurepowershell-interactive
New-AzRoleAssignment -RoleDefinitionName Owner -ObjectId <userObjectId> -Scope /providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>
```

### <a name="azure-clitabazure-cli-2"></a>[Azure CLI](#tab/azure-cli-2)

运行以下[创建的 az 角色分配](../active-directory/role-based-access-control-manage-access-azure-cli.md)命令，用```<enrollmentAccountObjectId>```与`name`第一步中复制 (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```)。 替换为```<userObjectId>```使用的对象 ID 将收集在第二个步骤。

```azurecli-interactive
az role assignment create --role Owner --assignee-object-id <userObjectId> --scope /providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>
```

----

当用户变得 RBAC 所有者为你的注册帐户时，他们可以[以编程方式创建订阅](programmatically-create-subscription.md)下它。 创建委派的用户的订阅仍具有原始帐户所有者作为服务管理员，但它也有委派的用户作为 RBAC 所有者默认情况下。

## <a name="audit-who-created-subscriptions-using-activity-logs"></a>使用活动日志审核订阅的创建者

若要通过此 API 跟踪创建的订阅，请使用[租户活动日志 API](/rest/api/monitor/tenantactivitylogs)。 目前不可以使用 PowerShell、CLI 或 Azure 门户跟踪订阅创建信息。

1. Azure AD 租户的租户管理员可以[提升访问权限](../active-directory/role-based-access-control-tenant-admin-access.md)，然后将一个“读者”角色分配给 `/providers/microsoft.insights/eventtypes/management` 范围内的审核用户。
1. 审核用户可以调用[租户活动日志 API](/rest/api/monitor/tenantactivitylogs) 查看订阅创建活动。 示例：

```
GET "/providers/Microsoft.Insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '{greaterThanTimeStamp}' and eventTimestamp le '{lessThanTimestamp}' and eventChannels eq 'Operation' and resourceProvider eq 'Microsoft.Subscription'" 
```

> 若要快速从命令行调用此 API，请尝试使用 [ARMClient](https://github.com/projectkudu/ARMClient)。

## <a name="next-steps"></a>后续步骤

* 既然用户或服务主体有权创建订阅，你可以使用该标识[以编程方式创建 Azure Enterprise 订阅](programmatically-create-subscription.md)。
* 有关使用 .NET 创建订阅的示例，请参阅 [GitHub 上的示例代码](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core)。
* 若要了解有关 Azure 资源管理器及其 API 的详细信息，请参阅 [Azure 资源管理器概述](resource-group-overview.md)。
* 要详细了解如何使用管理组管理大量订阅，请参阅[使用 Azure 管理组整理资源](management-groups-overview.md)
* 若要查看有关大型组织订阅管理的综合最佳做法指南，请参阅 [Azure 企业基架 - 规范性订阅管理](/azure/architecture/cloud-adoption-guide/subscription-governance)
