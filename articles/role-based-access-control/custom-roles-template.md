---
title: 使用 Azure 资源管理器模板创建 Azure 自定义角色 - Azure RBAC
description: 了解如何使用 Azure 资源管理器模板和 Azure 基于角色的访问控制 (Azure RBAC) 创建 Azure 自定义角色。
services: role-based-access-control,azure-resource-manager
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.custom: subject-armqs
ms.workload: identity
ms.date: 06/25/2020
ms.author: rolyon
ms.openlocfilehash: bcf1966ffc326291448cb611d99390fe0d652151
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "85397937"
---
# <a name="create-an-azure-custom-role-using-an-azure-resource-manager-template"></a>使用 Azure 资源管理器模板创建 Azure 自定义角色

如果 [Azure 内置角色](built-in-roles.md)不满足组织的特定需求，你可以创建自己的[自定义角色](custom-roles.md)。 本文介绍如何使用 Azure 资源管理器模板创建自定义角色。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>先决条件

若要创建自定义角色，必须：

- 有权创建自定义角色，例如[所有者](built-in-roles.md#owner)或[用户访问管理员](built-in-roles.md#user-access-administrator)

## <a name="create-a-custom-role"></a>创建自定义角色

若要创建自定义角色，请指定角色名称、权限以及可使用角色的位置。 在本文中，你将创建一个名为“自定义角色 - RG 读者”的角色，其资源权限可在订阅或更低层次的范围内分配。

### <a name="review-the-template"></a>查看模板

本文中使用的模板来自 [Azure 快速入门模板](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-deployments/create-role-def)。 该模板具有四个参数和一个资源部分。 这四个参数为：

- 操作数组，默认值为 ["Microsoft.Resources/subscriptions/resourceGroups/read"]
- notActions 数组，默认值为空
- 角色名称，默认值为“自定义角色 - RG 读者”
- 角色说明，默认值为“角色定义的订阅级别部署”

该模板中定义了以下资源：

- [Microsoft.Authorization/roleDefinitions](/azure/templates/Microsoft.Authorization/roleDefinitions)

将可分配此自定义角色的范围设置为当前订阅。

:::code language="json" source="~/quickstart-templates/subscription-deployments/create-role-def/azuredeploy.json":::

### <a name="deploy-the-template"></a>部署模板

请遵照以下步骤部署上一模板。

1. 登录到 [Azure 门户](https://portal.azure.com)。

1. 打开适用于 PowerShell 的 Azure Cloud Shell。

1. 复制以下脚本并将其粘贴到 Cloud Shell 中。

    ```azurepowershell
    $location = Read-Host -Prompt "Enter a location (i.e. centralus)"
    [string[]]$actions = Read-Host -Prompt "Enter actions as a comma-separated list (i.e. action1,action2)"
    $actions = $actions.Split(',')

    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/subscription-deployments/create-role-def/azuredeploy.json"

    New-AzDeployment -Location $location -TemplateUri $templateUri -actions $actions
    ```

1. 输入部署的位置，例如 " *centralus*"。

1. 以逗号分隔列表的形式输入自定义角色的操作列表，如 Microsoft.Resources/resources/read,Microsoft.Resources/subscriptions/resourceGroups/read。

1. 如有必要，请按 Enter 运行 New-AzDeployment 命令。

    [New-AzDeployment](/powershell/module/az.resources/new-azdeployment) 命令部署模板来创建自定义角色。

    会得到类似于下面的输出：

    ```azurepowershell
    PS> New-AzDeployment -Location $location -TemplateUri $templateUri -actions $actions
    
    Id                      : /subscriptions/{subscriptionId}/providers/Microsoft.Resources/deployments/azuredeploy
    DeploymentName          : azuredeploy
    Location                : centralus
    ProvisioningState       : Succeeded
    Timestamp               : 6/25/2020 8:08:32 PM
    Mode                    : Incremental
    TemplateLink            :
                              Uri            : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/subscription-deployments/create-role-def/azuredeploy.json
                              ContentVersion : 1.0.0.0
    
    Parameters              :
                              Name               Type                       Value
                              =================  =========================  ==========
                              actions            Array                      [
                                "Microsoft.Resources/resources/read",
                                "Microsoft.Resources/subscriptions/resourceGroups/read"
                              ]
                              notActions         Array                      []
                              roleName           String                     Custom Role - RG Reader
                              roleDescription    String                     Subscription Level Deployment of a Role Definition
    
    Outputs                 :
    DeploymentDebugLogLevel :
    ```

## <a name="review-deployed-resources"></a>查看已部署的资源

按照以下步骤验证是否已创建自定义角色。

1. 运行 [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) 命令以列出自定义角色。

    ```azurepowershell
    Get-AzRoleDefinition "Custom Role - RG Reader" | ConvertTo-Json
    ```

    应该会看到与下面类似的输出：

    ```azurepowershell
    {
      "Name": "Custom Role - RG Reader",
      "Id": "11111111-1111-1111-1111-111111111111",
      "IsCustom": true,
      "Description": "Subscription Level Deployment of a Role Definition",
      "Actions": [
        "Microsoft.Resources/resources/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read"
      ],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/{subscriptionId}"
      ]
    }
    ```

1. 在 Azure 门户中，打开你的订阅。

1. 在左侧菜单中，单击“访问控制(IAM)”。

1. 单击“角色”选项卡。

1. 将“类型”列表设置为 CustomRole 。

1. 验证是否列出了“自定义角色 - RG 读者”角色。

   ![在 Azure 门户中新建自定义角色](./media/custom-roles-template/custom-role-template-portal.png)

## <a name="clean-up-resources"></a>清理资源

若要删除自定义角色，请按照以下步骤操作。

1. 运行以下命令以删除自定义角色。

    ```azurepowershell
    Get-AzRoleDefinition -Name "Custom Role - RG Reader" | Remove-AzRoleDefinition
    ```

1. 输入“Y”以确认要删除该自定义角色。

## <a name="next-steps"></a>后续步骤

- [了解 Azure 角色定义](role-definitions.md)
- [快速入门：使用 Azure 资源管理器模板添加 Azure 角色分配](quickstart-role-assignments-template.md)
- [ARM 模板文档](../azure-resource-manager/templates/index.yml)
