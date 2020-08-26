---
title: 删除对委派的访问权限
description: 了解如何删除已委派给 Azure Lighthouse 服务提供商的资源的访问权限。
ms.date: 08/12/2020
ms.topic: how-to
ms.openlocfilehash: 72a2c49dde8cccfcc298d4128384a10bb7e8840f
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2020
ms.locfileid: "88167214"
---
# <a name="remove-access-to-a-delegation"></a>删除对委派的访问权限

将客户的订阅或资源组委托给 [Azure Lighthouse](../overview.md)的服务提供商后，可以根据需要删除委派。 删除委派后，以前向服务提供商租户中的用户授予的 [Azure 委托资源管理](../concepts/azure-delegated-resource-management.md) 访问权限将不再适用。

只要用户具有适当的权限，就可以在客户租户或服务提供商租户中删除委托。

> [!TIP]
> 尽管我们指的是本主题中的服务提供商和客户，但 [管理多个租户的企业](../concepts/enterprise.md) 可以使用相同的过程。

## <a name="customers"></a>客户

客户租户中具有订阅的 [所有者内置角色](../../role-based-access-control/built-in-roles.md#owner) 的用户可以删除该订阅的服务提供者访问权限 (或该订阅中的资源组) 。 为此，客户租户中的用户可以访问 Azure 门户的 " [服务提供商" 页](view-manage-service-providers.md#add-or-remove-service-provider-offers) ，在 " **服务提供商产品/服务** " 屏幕上找到产品/服务的 "垃圾箱" 图标，然后选择该产品的行中的 "垃圾桶" 图标。

确认删除后，服务提供商的租户中的任何用户都将无法访问之前已委派的资源。

## <a name="service-providers"></a>服务提供商

如果管理租户中的用户为客户的资源授予了 [托管服务注册分配删除角色](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) ，则该用户可以删除对这些资源的访问权限。 如果未将此角色分配给任何服务提供商用户，则只能由客户租户中的用户删除委派。

下面的示例演示了一个分配，该分配授予可在[载入过程](onboard-customer.md)中包含在参数文件中的**托管服务注册分配删除角色**：

```json
    "authorizations": [ 
        { 
            "principalId": "cfa7496e-a619-4a14-a740-85c5ad2063bb", 
            "principalIdDisplayName": "MSP Operators", 
            "roleDefinitionId": "91c1777a-f3dc-4fae-b103-61d183457e46" 
        } 
    ] 
```

[创建托管服务产品/服务](../../marketplace/partner-center-portal/create-new-managed-service-offer.md#authorization)以发布到 Azure Marketplace 时，还可以在**授权**中选择此角色。

具有此权限的用户可以通过以下的一种方法删除委托。

### <a name="azure-portal"></a>Azure 门户

1. 导航到[“我的客户”页面](view-manage-customers.md)。
2. 选择 " **委托**"。
3. 找到要删除的委派，然后选择显示在其行中的 "垃圾桶" 图标。

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# Sign in as a user from the managing tenant directory 

Login-AzAccount

# Select the subscription that is delegated - or contains the delegated resource group(s)

Select-AzSubscription -SubscriptionName "<subscriptionName>"

# Get the registration assignment

Get-AzManagedServicesAssignment -Scope "/subscriptions/{delegatedSubscriptionId}"

# Delete the registration assignment

Remove-AzManagedServicesAssignment -ResourceId "/subscriptions/{delegatedSubscriptionId}/providers/Microsoft.ManagedServices/registrationAssignments/{assignmentGuid}"
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# Sign in as a user from the managing tenant directory

az login

# Select the subscription that is delegated – or contains the delegated resource group(s)

az account set -s <subscriptionId/name>

# List registration assignments

az managedservices assignment list

# Delete the registration assignment

az managedservices assignment delete --assignment <id or full resourceId>
```

## <a name="next-steps"></a>后续步骤

- 了解 [Azure 委派资源管理](../concepts/azure-delegated-resource-management.md)。
- 在 Microsoft Azure 门户中转到“我的客户”，以[查看和管理客户](view-manage-customers.md)。
