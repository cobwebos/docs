---
title: 授予创建 Azure 企业订阅的访问权限
description: 了解如何使用户或服务主体能够以编程方式创建 Azure Enterprise 订阅。
author: jureid
manager: jureid
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: jureid
ms.openlocfilehash: b77efd7e5cf7ff016605e0ba2e74cff9ea8dab89
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75478872"
---
# <a name="grant-access-to-create-azure-enterprise-subscriptions-preview"></a>授予创建 Azure Enterprise 订阅（预览版）所需的访问权限

作为[企业协议 (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) 的 Azure 客户，你可以授予其他用户或服务主体创建计费到你的帐户的订阅所需的权限。 本文介绍如何使用[基于角色的访问控制 (RBAC)](../../active-directory/role-based-access-control-configure.md) 共享创建订阅的功能，以及如何审核订阅创建。 必须在想要共享的帐户上具备所有者角色。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="grant-access"></a>授予访问权限

若要[在注册帐户下创建订阅](programmatically-create-subscription.md)，用户必须在该帐户上拥有[RBAC 所有者角色](../../role-based-access-control/built-in-roles.md#owner)。 可以通过执行以下步骤，为用户或用户组授予注册帐户上的 RBAC 所有者角色：

1. 获取要授予访问权限的注册帐户的对象 ID

    若要向其他人授予注册帐户上的 RBAC 所有者角色，你必须是帐户所有者或帐户的 RBAC 所有者。

    # <a name="resttabrest"></a>[REST](#tab/rest)

    请求列出你有权访问的所有注册帐户：

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

    使用 `principalName` 属性来标识要授予 RBAC 所有者访问权限的帐户。 复制该帐户的 `name`。 例如，如果你想要授予 RBAC 所有者对 SignUpEngineering@contoso.com 注册帐户的访问权限，则可以复制 ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```。 这是注册帐户的对象 ID。 将此值粘贴到某个位置，以便在下一步中将其用作 `enrollmentAccountObjectId`。

    # <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

    使用 [Get-AzEnrollmentAccount](/powershell/module/az.billing/get-azenrollmentaccount) cmdlet 列出你可以访问的所有注册帐户。 选择 "**尝试**" 以打开[Azure Cloud Shell](https://shell.azure.com/)。 若要粘贴代码，请右键单击 shell 窗口，然后选择 "**粘贴**"。

    ```azurepowershell-interactive
    Get-AzEnrollmentAccount
    ```

    Azure 会使用你有权访问的注册帐户列表进行响应：

    ```azurepowershell
    ObjectId                               | PrincipalName
    747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | SignUpEngineering@contoso.com
    4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | BillingPlatformTeam@contoso.com
    ```

    使用 `principalName` 属性来标识要授予 RBAC 所有者访问权限的帐户。 复制该帐户的 `ObjectId`。 例如，如果你想要授予 RBAC 所有者对 SignUpEngineering@contoso.com 注册帐户的访问权限，则可以复制 ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```。 将此对象 ID 粘贴到某个位置，以便在下一步中将其用作 `enrollmentAccountObjectId`。

    # <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

    使用 [az billing enrollment-account list](https://aka.ms/EASubCreationPublicPreviewCLI) 命令列出有访问权限的所有注册帐户。 选择 "**尝试**" 以打开[Azure Cloud Shell](https://shell.azure.com/)。 若要粘贴代码，请右键单击 shell 窗口，然后选择 "**粘贴**"。

    ```azurecli-interactive
    az billing enrollment-account list
    ```

    Azure 会使用你有权访问的注册帐户列表进行响应：

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

    ---

    使用 `principalName` 属性来标识要授予 RBAC 所有者访问权限的帐户。 复制该帐户的 `name`。 例如，如果你想要授予 RBAC 所有者对 SignUpEngineering@contoso.com 注册帐户的访问权限，则可以复制 ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```。 这是注册帐户的对象 ID。 将此值粘贴到某个位置，以便在下一步中将其用作 `enrollmentAccountObjectId`。

1. <a id="userObjectId"></a>获取要向其授予 RBAC 所有者角色的用户或组的对象 ID

    1. 在 Azure 门户中，搜索**Azure Active Directory**。
    1. 如果要授予用户访问权限，请单击左侧菜单中的 "**用户**"。 若要授予对组的访问权限，请单击 "**组**"。
    1. 选择要为其授予 RBAC 所有者角色的用户或组。
    1. 如果选择了一个用户，则会在 "配置文件" 页中找到对象 ID。 如果选择了组，则对象 ID 将在 "概述" 页中。 通过单击文本框右侧的图标来复制**ObjectID** 。 将此粘贴到某个位置，以便在下一步中将其用作 `userObjectId`。

1. 向用户或组授予注册帐户上的 RBAC 所有者角色

    使用前两个步骤中收集的值向用户或组授予注册帐户上的 RBAC 所有者角色。

    # <a name="resttabrest-2"></a>[REST](#tab/rest-2)

    运行以下命令，将 ```<enrollmentAccountObjectId>``` 替换为在第一个步骤中复制的 `name` （```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```）。 将 ```<userObjectId>``` 替换为从第二步复制的对象 ID。

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

    # <a name="powershelltabazure-powershell-2"></a>[PowerShell](#tab/azure-powershell-2)

    运行以下[AzRoleAssignment](../../active-directory/role-based-access-control-manage-access-powershell.md)命令，将 ```<enrollmentAccountObjectId>``` 替换为第一步中收集的 `ObjectId` （```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```）。 将 ```<userObjectId>``` 替换为在第二步中收集的对象 ID。

    ```azurepowershell-interactive
    New-AzRoleAssignment -RoleDefinitionName Owner -ObjectId <userObjectId> -Scope /providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>
    ```

    # <a name="azure-clitabazure-cli-2"></a>[Azure CLI](#tab/azure-cli-2)

    运行以下[az role 赋值 create](../../active-directory/role-based-access-control-manage-access-azure-cli.md)命令，并将 ```<enrollmentAccountObjectId>``` 替换为在第一步中复制的 `name` （```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```）。 将 ```<userObjectId>``` 替换为在第二步中收集的对象 ID。

    ```azurecli-interactive
    az role assignment create --role Owner --assignee-object-id <userObjectId> --scope /providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>
    ```

    一旦用户成为注册帐户的 RBAC 所有者，他们就可以在其下[以编程方式创建订阅](programmatically-create-subscription.md)。 委托用户创建的订阅仍具有原始帐户所有者作为服务管理员，但默认情况下，它还将委托用户作为 RBAC 所有者。

    ---

## <a name="audit-who-created-subscriptions-using-activity-logs"></a>使用活动日志审核订阅的创建者

若要通过此 API 跟踪创建的订阅，请使用[租户活动日志 API](/rest/api/monitor/tenantactivitylogs)。 目前不可以使用 PowerShell、CLI 或 Azure 门户跟踪订阅创建信息。

1. Azure AD 租户的租户管理员可以[提升访问权限](../../active-directory/role-based-access-control-tenant-admin-access.md)，然后将一个“读者”角色分配给 `/providers/microsoft.insights/eventtypes/management` 范围内的审核用户。
1. 审核用户可以调用[租户活动日志 API](/rest/api/monitor/tenantactivitylogs) 查看订阅创建活动。 示例：

    ```
    GET "/providers/Microsoft.Insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '{greaterThanTimeStamp}' and eventTimestamp le '{lessThanTimestamp}' and eventChannels eq 'Operation' and resourceProvider eq 'Microsoft.Subscription'"
    ```

若要快速从命令行调用此 API，请尝试使用 [ARMClient](https://github.com/projectkudu/ARMClient)。

## <a name="next-steps"></a>后续步骤

* 既然用户或服务主体有权创建订阅，你可以使用该标识[以编程方式创建 Azure Enterprise 订阅](programmatically-create-subscription.md)。
* 有关使用 .NET 创建订阅的示例，请参阅 [GitHub 上的示例代码](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core)。
* 若要了解有关 Azure 资源管理器及其 API 的详细信息，请参阅 [Azure 资源管理器概述](overview.md)。
* 要详细了解如何使用管理组管理大量订阅，请参阅[使用 Azure 管理组整理资源](../../governance/management-groups/overview.md)
* 若要查看有关大型组织订阅管理的综合最佳做法指南，请参阅 [Azure 企业基架 - 规范性订阅管理](/azure/architecture/cloud-adoption-guide/subscription-governance)
