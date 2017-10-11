---
title: "保护 DNS 区域和记录 | Microsoft Docs"
description: "如何保护 DNS 区域和 Microsoft Azure DNS 中的记录集。"
services: dns
documentationcenter: na
author: jtuliani
manager: carmonm
ms.assetid: 190e69eb-e820-4fc8-8e9a-baaf0b3fb74a
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/20/2016
ms.author: jonatul
ms.openlocfilehash: 0b7040d6273b3a6b85cd55850d596807226b87fc
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="how-to-protect-dns-zones-and-records"></a>如何保护 DNS 区域和记录

DNS 区域和记录是关键资源。 删除 DNS 区域或单个 DNS 记录都可能导致总服务中断。  因此，重要的是保护关键的 DNS 区域和记录，防止未经授权或意外的更改。

本文介绍了 Azure DNS 如何保护 DNS 区域和记录，避免受到此类更改。  我们应用了 Azure Resource Manager 提供的两个强大的安全功能：[基于角色的访问控制](../active-directory/role-based-access-control-what-is.md)和[资源锁](../azure-resource-manager/resource-group-lock-resources.md)。

## <a name="role-based-access-control"></a>基于角色的访问控制

Azure 基于角色的访问控制 (RBAC) 可用于对 Azure 用户、组和资源进行细致的访问管理。 使用 RBAC，可仅授予用户执行其作业所需的访问次数。 有关 RBAC 如何帮助你管理的访问的详细信息，请参阅[什么是基于角色的访问控制](../active-directory/role-based-access-control-what-is.md)。

### <a name="the-dns-zone-contributor-role"></a>“DNS 区域参与者”角色

“DNS 区域参与者”角色是 Azure 为管理 DNS 资源提供的内置角色。  将 DNS 区域参与者权限分配给用户或组可让该组管理 DNS 资源，但不能管理任何其他类型的资源。

例如，假设资源组“myzones”包含 Contoso Corporation 的五个区域。 授予 DNS 管理员对该资源组的 DNS 区域参与者权限，可以完全控制这些 DNS 区域。 它还避免了授予不必要的权限，例如 DNS 管理员无法创建或停止虚拟机。

分配 RBAC 权限最简单方法是[通过 Azure 门户](../active-directory/role-based-access-control-configure.md)进行分配。  打开资源组的访问控制 (IAM) 边栏选项卡，然后单击添加，然后选择 DNS 区域参与者角色并选择要授予权限的所需的用户或组。

![使用 Azure 门户的资源组级别 RBAC](./media/dns-protect-zones-recordsets/rbac1.png)

也可以[使用 Azure PowerShell](../active-directory/role-based-access-control-manage-access-powershell.md)授予权限：

```powershell
# Grant 'DNS Zone Contributor' permissions to all zones in a resource group
New-AzureRmRoleAssignment -SignInName "<user email address>" -RoleDefinitionName "DNS Zone Contributor" -ResourceGroupName "<resource group name>"
```

也可[通过 Azure CLI](../active-directory/role-based-access-control-manage-access-azure-cli.md) 提供等效命令：

```azurecli
# Grant 'DNS Zone Contributor' permissions to all zones in a resource group
azure role assignment create --signInName "<user email address>" --roleName "DNS Zone Contributor" --resourceGroup "<resource group name>"
```

### <a name="zone-level-rbac"></a>区域级别 RBAC

Azure RBAC 规则可应用于订阅，资源组或单个资源。 在 Azure DNS 的情况下，该资源可以是单个 DNS 区域，或甚至单个记录集。

例如，假设资源组“myzones”包含区域“contoso.com”和子区域“customers.contoso.com”（其中针对每个客户帐户创建 CNAME 记录）。  应为用于管理这些 CNAME 记录的帐户分配权限，以便仅在“customers.contoso.com”区域中创建记录，但该帐户不应具有对其他区域的访问权限。

可以通过 Azure 门户授予区域级别的 RBAC 权限。  打开该区域的访问控制 (IAM) 边栏选项卡，然后单击添加，然后选择 DNS 区域参与者角色并选择要授予权限的所需的用户或组。

![使用 Azure 门户的 DNS 区域级别 RBAC](./media/dns-protect-zones-recordsets/rbac2.png)

也可以[使用 Azure PowerShell](../active-directory/role-based-access-control-manage-access-powershell.md)授予权限：

```powershell
# Grant 'DNS Zone Contributor' permissions to a specific zone
New-AzureRmRoleAssignment -SignInName "<user email address>" -RoleDefinitionName "DNS Zone Contributor" -ResourceGroupName "<resource group name>" -ResourceName "<zone name>" -ResourceType Microsoft.Network/DNSZones
```

也可[通过 Azure CLI](../active-directory/role-based-access-control-manage-access-azure-cli.md) 提供等效命令：

```azurecli
# Grant 'DNS Zone Contributor' permissions to a specific zone
azure role assignment create --signInName <user email address> --roleName "DNS Zone Contributor" --resource-name <zone name> --resource-type Microsoft.Network/DNSZones --resource-group <resource group name>
```

### <a name="record-set-level-rbac"></a>记录集级别 RBAC

再进一步分析。 假设 Contoso Corporation 的邮件管理员需要访问“contoso.com”区域核心位置的 MX 和 TXT 记录。  她无需访问任何其他 MX 或 TXT 记录，也无需访问任何其他类型的任何记录。  可通过 Azure DNS 在记录集级别分配权限，仅针对邮件管理员需要访问的记录分配权限。  邮件管理员仅被授予所需控制权，无法进行任何其他更改。

记录集级别的 RBAC 权限可在 Azure 门户中使用“记录集”边栏选项卡中的“用户”按钮进行配置：

![使用 Azure 门户的记录集级别 RBAC](./media/dns-protect-zones-recordsets/rbac3.png)

也可以[使用 Azure PowerShell](../active-directory/role-based-access-control-manage-access-powershell.md)授予记录集级别 RBAC 权限：

```powershell
# Grant permissions to a specific record set
New-AzureRmRoleAssignment -SignInName "<user email address>" -RoleDefinitionName "DNS Zone Contributor" -Scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/dnszones/<zone name>/<record type>/<record name>"
```

也可[通过 Azure CLI](../active-directory/role-based-access-control-manage-access-azure-cli.md) 提供等效命令：

```azurecli
# Grant permissions to a specific record set
azure role assignment create --signInName "<user email address>" --roleName "DNS Zone Contributor" --scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/dnszones/<zone name>/<record type>/<record name>"
```

### <a name="custom-roles"></a>自定义角色

内置“DNS区域参与者”角色可以完全控制 DNS 资源。 还可以构建自己的客户 Azure 角色，以提供更细致的控制。

再次以为每个 Contoso Corporation 客户帐户创建区域“customers.contoso.com”中的 CNAME 记录为例。  应对用于管理这些 CNAME 的帐户仅授予管理 CNAME 记录的权限。  这样，它将无法修改其他类型的记录（例如更改 MX 记录）或执行区域级操作（如区域删除）。

以下示例显示了仅用于管理 CNAME 记录的自定义角色定义：

```json
{
    "Name": "DNS CNAME Contributor",
    "Id": "",
    "IsCustom": true,
    "Description": "Can manage DNS CNAME records only.",
    "Actions": [
        "Microsoft.Network/dnsZones/CNAME/*",
        "Microsoft.Network/dnsZones/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
    ],
    "NotActions": [
    ],
    "AssignableScopes": [
        "/subscriptions/ c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ]
}
```

操作属性定义以下特定于 DNS 的权限：

* `Microsoft.Network/dnsZones/CNAME/*` 授予对 CNAME 记录的完全控制权
* `Microsoft.Network/dnsZones/read`授予读取 DNS 区域的权限，但不能修改它们，用户可查看创建 CNAME 的区域。

其余操作从 [DNS 区域参与者内置角色](../active-directory/role-based-access-built-in-roles.md#dns-zone-contributor)中复制。

> [!NOTE]
> 使用自定义 RBAC 角色防止删除记录集的同时仍允许它们更新，这不是一种有效的控制方式。 此方法仅可防止记录集被删除，但不会阻止它们被修改。  允许的修改包括添加和删除记录集中的记录，还包括删除所有记录，只留下“空”记录集。 这与从 DNS 解析视点中删除记录集具有相同的效果。

当前无法通过 Azure 门户定义自定义角色定义。 可以使用 Azure PowerShell 创建基于此角色定义的自定义角色：

```powershell
# Create new role definition based on input file
New-AzureRmRoleDefinition -InputFile <file path>
```

也可以通过 Azure CLI 创建：

```azurecli
# Create new role definition based on input file
azure role create -inputfile <file path>
```

如本文前面部分所述，该角色可由与内置角色相同的方式进行分配。

如需了解如何创建、管理和分配自定义角色的详细信息，请参阅 [Azure RBAC 中的自定义角色](../active-directory/role-based-access-control-custom-roles.md)。

## <a name="resource-locks"></a>资源锁

除支持 RBAC 外，Azure Resource Manager 还支持另一种类型的安全控制，即“锁定”资源的能力。 其中 RBAC 规则用于控制特定用户和组的操作，而资源锁将应用于资源且对所有用户和角色都有效。 有关详细信息，请参阅 [使用 Azure Resource Manager 锁定资源](../azure-resource-manager/resource-group-lock-resources.md)。

有两种类型的资源锁：**DoNotDelete** 和 **ReadOnly**。 它们都可应用到 DNS 区域或单个记录集。  以下各节描述了几种常见情况以及如何使用资源锁支持它们。

### <a name="protecting-against-all-changes"></a>防止所有更改

若要防止进行任何更改，可在该区域应用 ReadOnly 锁。  这会阻止创建新的记录集，并防止修改或删除现有记录集。

可通过 Azure 门户创建区域级别的资源锁。  在 DNS 区域边栏选项卡上，单击“锁定”，并单击“添加”：

![使用 Azure 门户的区域级别资源锁](./media/dns-protect-zones-recordsets/locks1.png)

也可通过 Azure PowerShell 创建区域级别的资源锁：

```powershell
# Lock a DNS zone
New-AzureRmResourceLock -LockLevel <lock level> -LockName <lock name> -ResourceName <zone name> -ResourceType Microsoft.Network/DNSZones -ResourceGroupName <resource group name>
```

目前暂不支持通过 Azure CLI 配置 Azure 资源锁。

### <a name="protecting-individual-records"></a>保护单个记录

要防止对现有 DNS 记录集的修改，可将 ReadOnly 锁应用到记录集。

> [!NOTE]
> 将 DoNotDelete 锁应用到记录集不能达到有效控制。 它仅可防止记录集被删除，但不会阻止其被修改。  允许的修改包括添加和删除记录集中的记录，还包括删除所有记录，只留下“空”记录集。 这与从 DNS 解析视点中删除记录集具有相同的效果。

记录集级别资源锁定当前只能使用 Azure PowerShell 进行配置。  它们在 Azure 门户或 Azure CLI 中不受支持。

```powershell
# Lock a DNS record set
New-AzureRmResourceLock -LockLevel <lock level> -LockName "<lock name>" -ResourceName "<zone name>/<record set name>" -ResourceType "Microsoft.Network/DNSZones/<record type>" -ResourceGroupName "<resource group name>"
```

### <a name="protecting-against-zone-deletion"></a>防止区域删除

在 Azure DNS 中删除区域时，也将删除区域中的所有记录集。  此操作不可撤消。  意外删除关键区域有可能产生巨大的业务影响。  因此，防止区域意外删除是非常重要的。

在该区域应用 DoNotDelete 锁即可防止区域被删除。  但是由于锁由子资源继承，它还将阻止删除区域中的任何记录集（这可能是不希望发生的）。  此外，如上面的说明中所述，由于记录仍可从现有记录集中删除，因此它也不起作用。

作为替代方法，请考虑将 DoNotDelete 锁应用于该区域的记录集，例如 SOA 记录集。  由于在不删除记录集的情况下不能删除区域，因此这可在防止区域删除的同时允许随意修改区域内的记录集。 如果尝试删除区域，Azure Resource Manager 检测到此操作还将删除 SOA 记录集，由于已锁定 SOA，因此会阻止调用。  而不会删除任何记录集。

以下 PowerShell 命令针对给定区域的 SOA 记录创建 DoNotDelete 锁：

```powershell
# Protect against zone delete with DoNotDelete lock on the record set
New-AzureRmResourceLock -LockLevel DoNotDelete -LockName "<lock name>" -ResourceName "<zone name>/@" -ResourceType" Microsoft.Network/DNSZones/SOA" -ResourceGroupName "<resource group name>"
```

防止意外删除区域的另一种方法是使用自定义角色，确保用于管理区域的操作员和服务帐户不具有区域删除权限。 需要删除区域时，可以强制执行两步删除，首先授予区域删除权限（在区域作用域内，防止删除错误的区域），并删除区域。

第二种方法的优点在于，它适用于由这些帐户访问的所有区域，而不必记住创建任何锁。 它的缺点是，任何具有区域删除权限的帐户（如订阅所有者）仍然可能会意外删除关键区域。

可以同时使用资源锁和自定义角色这两种方法，作为 DNS 区域保护的深度防御方法。

## <a name="next-steps"></a>后续步骤

* 有关使用 RBAC 的详细信息，请参阅 [Azure 门户中的访问管理入门](../active-directory/role-based-access-control-what-is.md)。
* 有关使用资源锁的详细信息，请参阅[使用 Azure Resource Manager 锁定资源](../azure-resource-manager/resource-group-lock-resources.md)。

