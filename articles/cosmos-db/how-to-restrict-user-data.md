---
title: 仅限用户使用 Azure Cosmos DB 访问数据操作
description: 了解如何使用 Azure Cosmos DB 限制对数据操作的访问权限
author: voellm
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/9/2019
ms.author: tvoellm
ms.openlocfilehash: 03cad9e4c3752b5f35be785a6280bf18aaa14860
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2019
ms.locfileid: "74980369"
---
# <a name="restrict-user-access-to-data-operations-only"></a>仅限用户对数据操作的访问权限

在 Azure Cosmos DB 中，有两种方法可以对与数据库服务的交互进行身份验证：
- 与 Azure 门户交互时使用 Azure Active Directory 标识。
- 从 Api 和 Sdk 发出调用时，使用 Azure Cosmos DB[密钥](secure-access-to-data.md#master-keys)或[资源令牌](secure-access-to-data.md#resource-tokens)。

每种身份验证方法都可以访问不同的操作集，但有一些重叠： ![按身份验证类型对操作进行拆分](./media/how-to-restrict-user-data/operations.png)

在某些情况下，你可能想要将组织的某些用户限制为仅执行数据操作（即 CRUD 请求和查询）。 这种情况通常适用于不需要创建或删除资源的开发人员，或更改正在处理的容器的预配吞吐量。

可以通过应用以下步骤来限制访问：
1. 为想要限制访问的用户创建自定义 Azure Active Directory 角色。 自定义 Active Directory 角色应具有对使用 Azure Cosmos DB[细化操作](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)的操作的精细访问级别。
1. 不允许执行非数据操作和键。 可以通过将这些操作限制为仅限 Azure 资源管理器调用来实现此目的。

本文的后续部分介绍了如何执行这些步骤。

> [!NOTE]
> 若要执行后续部分中的命令，需要安装 Azure PowerShell Module 3.0.0 或更高版本，以及要尝试修改的订阅上的[Azure 所有者角色](../role-based-access-control/built-in-roles.md#owner)。

在下一部分的 PowerShell 脚本中，将以下占位符替换为特定于你的环境的值：
- `$MySubscriptionId`-包含要限制权限的 Azure Cosmos 帐户的订阅 ID。 例如：`e5c8766a-eeb0-40e8-af56-0eb142ebf78e`。
- `$MyResourceGroupName`-包含 Azure Cosmos 帐户的资源组。 例如：`myresourcegroup`。
- `$MyAzureCosmosDBAccountName`-Azure Cosmos 帐户的名称。 例如：`mycosmosdbsaccount`。
- `$MyUserName`-要限制其访问权限的用户的登录名（username@domain）。 例如：`cosmosdbuser@contoso.com`。

## <a name="select-your-azure-subscription"></a>选择 Azure 订阅

Azure PowerShell 命令要求登录并选择要执行命令的订阅：

```azurepowershell
Login-AzAccount
Select-AzSubscription $MySubscriptionId
```

## <a name="create-the-custom-azure-active-directory-role"></a>创建自定义 Azure Active Directory 角色

以下脚本使用 Azure Cosmos 帐户的 "仅密钥" 访问权限创建 Azure Active Directory 角色分配。 该角色基于[Azure 资源的自定义角色](../role-based-access-control/custom-roles.md)和[Azure Cosmos DB 的具体操作](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)。 这些角色和操作是 `Microsoft.DocumentDB` Azure Active Directory 命名空间的一部分。

1. 首先，使用以下内容创建名为 `AzureCosmosKeyOnlyAccess.json` 的 JSON 文档：

    ```
    {
        "Name": "Azure Cosmos DB Key Only Access Custom Role",
        "Id": "00000000-0000-0000-0000-0000000000",
        "IsCustom": true,
        "Description": "This role restricts the user to read the account keys only.",
        "Actions":
        [
            "Microsoft.DocumentDB/databaseAccounts/listKeys/action"
        ],
        "NotActions": [],
        "DataActions": [],
        "NotDataActions": [],
        "AssignableScopes":
        [
            "/subscriptions/$MySubscriptionId"
        ]
    }
    ```

1. 运行以下命令以创建角色分配，并将其分配给用户：

    ```azurepowershell
    New-AzRoleDefinition -InputFile "AzureCosmosKeyOnlyAccess.json"
    New-AzRoleAssignment -SignInName $MyUserName -RoleDefinitionName "Azure Cosmos DB Key Only Access Custom Role" -ResourceGroupName $MyResourceGroupName -ResourceName $MyAzureCosmosDBAccountName -ResourceType "Microsoft.DocumentDb/databaseAccounts"
    ```

## <a name="disallow-the-execution-of-non-data-operations"></a>不允许执行非数据操作

以下命令将删除使用密钥的功能：
- 创建、修改或删除资源
- 更新容器设置（包括索引策略、吞吐量等）。

```azurepowershell
$cdba = Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName $MyResourceGroupName -ResourceName $MyAzureCosmosDBAccountName
$cdba.Properties.disableKeyBasedMetadataWriteAccess="True"
$cdba | Set-AzResource -Force
```

## <a name="next-steps"></a>后续步骤

- 详细了解[Cosmos DB 的基于角色的访问控制](role-based-access-control.md)
- 大致了解[Cosmos DB 中数据的安全访问](secure-access-to-data.md)
