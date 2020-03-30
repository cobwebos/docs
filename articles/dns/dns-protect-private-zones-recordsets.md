---
title: 保护专用 DNS 区域和记录 - Azure DNS
description: 在此学习路径中，开始保护 Microsoft Azure DNS 中的专用 DNS 区域和记录集。
services: dns
author: asudbring
ms.service: dns
ms.topic: article
ms.date: 02/18/2020
ms.author: allensu
ms.openlocfilehash: da94c9aa97483ab5792e917d6a8f60f846b0722e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77473059"
---
# <a name="how-to-protect-private-dns-zones-and-records"></a>如何保护专用 DNS 区域和记录

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

专用 DNS 区域和记录是关键资源。 删除 DNS 区域或单个 DNS 记录可能会导致服务中断。 保护 DNS 区域和记录免受未经授权的或意外更改非常重要。

本文介绍 Azure DNS 如何保护专用 DNS 区域和记录免受此类更改。  我们应用 Azure 资源管理器提供的两个强大证券功能：[基于角色的访问控制](../role-based-access-control/overview.md)[和资源锁](../azure-resource-manager/management/lock-resources.md)。

## <a name="role-based-access-control"></a>基于角色的访问控制

Azure 基于角色的访问控制 (RBAC) 可用于对 Azure 用户、组和资源进行细致的访问管理。 使用 RBAC，您可以授予用户所需的访问级别。 如需了解 RBAC 如何帮助你管理访问权限的详细信息，请参阅[什么是基于角色的访问控制](../role-based-access-control/overview.md)。

### <a name="the-private-dns-zone-contributor-role"></a>专用 DNS 区域参与者角色

专用 DNS 区域参与者角色是管理专用 DNS 资源的内置角色。 此角色应用于用户或组，使他们能够管理专用 DNS 资源。

资源组*myPrivateDNS*包含康托索公司的五个区域。 将 DNS 管理员专用 DNS 区域参与者权限授予该资源组，可以完全控制这些 DNS 区域。 它避免授予不必要的权限。 DNS 管理员无法创建或停止虚拟机。

分配 RBAC 权限最简单方法是[通过 Azure 门户](../role-based-access-control/role-assignments-portal.md)进行分配。  

打开资源组**的访问控制 （IAM），** 选择 **"添加**"，然后选择**专用 DNS 区域参与者**角色。 选择要授予权限所需的用户或组。

![使用 Azure 门户的资源组级别 RBAC](./media/dns-protect-private-zones-recordsets/rbac1.png)

也可以[使用 Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)授予权限：

```azurepowershell-interactive
# Grant 'Private DNS Zone Contributor' permissions to all zones in a resource group

$rsg = "<resource group name>"
$usr = "<user email address>"
$rol = "Private DNS Zone Contributor"

New-AzRoleAssignment -SignInName $usr -RoleDefinitionName $rol -ResourceGroupName $rsg
```

也可[通过 Azure CLI](../role-based-access-control/role-assignments-cli.md) 提供等效命令：

```azurecli-interactive
# Grant 'Private DNS Zone Contributor' permissions to all zones in a resource group

az role assignment create \
--assignee "<user email address>" \
--role "Private DNS Zone Contributor" \
--resource-group "<resource group name>"
```

### <a name="private-zone-level-rbac"></a>专用区域级 RBAC

Azure RBAC 规则可应用于订阅，资源组或单个资源。 该资源可以是单个 DNS 区域或单个记录集。

例如，资源组*myPrivateDNS*包含区域*private.contoso.com*和子区域*customers.private.contoso.com*。 为每个客户帐户创建 CNAME 记录。 用于管理 CNAME 记录的管理员帐户被分配在*customers.private.contoso.com*区域中创建记录的权限。 该帐户只能管理*customers.private.contoso.com。*

可以通过 Azure 门户授予区域级别的 RBAC 权限。  打开区域**的访问控制 （IAM），** 选择 **"添加**"，然后选择**专用 DNS 区域参与者**角色。 选择要授予权限所需的用户或组。

![使用 Azure 门户的 DNS 区域级别 RBAC](./media/dns-protect-private-zones-recordsets/rbac2.png)

也可以[使用 Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)授予权限：

```azurepowershell-interactive
# Grant 'Private DNS Zone Contributor' permissions to a specific zone

$rsg = "<resource group name>"
$usr = "<user email address>"
$zon = "<zone name>"
$rol = "Private DNS Zone Contributor"
$rsc = "Microsoft.Network/privateDnsZones"

New-AzRoleAssignment -SignInName $usr -RoleDefinitionName $rol -ResourceGroupName $rsg -ResourceName $zon -ResourceType $rsc
```

也可[通过 Azure CLI](../role-based-access-control/role-assignments-cli.md) 提供等效命令：

```azurecli-interactive
# Grant 'Private DNS Zone Contributor' permissions to a specific zone

az role assignment create \
--assignee <user email address> \
--role "Private DNS Zone Contributor" \
--scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/privateDnsZones/<zone name>/"
```

### <a name="record-set-level-rbac"></a>记录集级别 RBAC

权限在记录集级别应用。  用户被授予对他们需要的条目的控件，并且无法进行任何其他更改。

可以使用记录集页中的**访问控制 （IAM）** 按钮通过 Azure 门户配置记录集级别 RBAC 权限：

![使用 Azure 门户的记录集级别 RBAC](./media/dns-protect-private-zones-recordsets/rbac3.png)

![使用 Azure 门户的记录集级别 RBAC](./media/dns-protect-private-zones-recordsets/rbac4.png)

也可以[使用 Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)授予记录集级别 RBAC 权限：

```azurepowershell-interactive
# Grant permissions to a specific record set

$usr = "<user email address>"
$rol = "Private DNS Zone Contributor"
$sco = 
"/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/privateDnsZones/<zone name>/<record type>/<record name>"

New-AzRoleAssignment -SignInName $usr -RoleDefinitionName $rol -Scope $sco
```

也可[通过 Azure CLI](../role-based-access-control/role-assignments-cli.md) 提供等效命令：

```azurecli-interactive
# Grant permissions to a specific record set

az role assignment create \
--assignee "<user email address>" \
--role "Private DNS Zone Contributor" \
--scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/privateDnsZones/<zone name>/<record type>/<record name>"
```

### <a name="custom-roles"></a>自定义角色

内置专用 DNS 区域参与者角色可完全控制 DNS 资源。 可以构建自己的自定义 Azure 角色，以提供更细粒度的控件。

用于管理 CNAMA 的帐户被授予仅管理 CNAME 记录的权限。 帐户无法修改其他类型的记录。 该帐户无法执行区域级操作，如区域删除。

以下示例显示了仅用于管理 CNAME 记录的自定义角色定义：

```json
{
    "Name": "Private DNS CNAME Contributor",
    "Id": "",
    "IsCustom": true,
    "Description": "Can manage DNS CNAME records only.",
    "Actions": [
        "Microsoft.Network/privateDnsZones/CNAME/*",
        "Microsoft.Network/privateDNSZones/read",
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
        "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ]
}
```

操作属性定义以下特定于 DNS 的权限：

* `Microsoft.Network/privateDnsZones/CNAME/*` 授予对 CNAME 记录的完全控制权
* `Microsoft.Network/privateDNSZones/read`授予读取 DNS 专用区域的权限，但不修改它们，从而允许您查看在其中创建 CNAME 的区域。

> [!NOTE]
> 使用自定义 RBAC 角色防止删除记录集的同时仍允许它们更新，这不是一种有效的控制方式。 此方法仅可防止记录集被删除，但不会阻止它们被修改。  允许的修改包括添加和删除记录集中的记录，还包括删除所有记录，只留下空记录集。 这与从 DNS 解析视点中删除记录集具有相同的效果。

当前无法通过 Azure 门户定义自定义角色定义。 可以使用 Azure PowerShell 创建基于此角色定义的自定义角色：

```azurepowershell-interactive
# Create new role definition based on input file

New-AzRoleDefinition -InputFile <file path>
```

也可以通过 Azure CLI 创建：

```azurecli-interactive
# Create new role definition based on input file

az role create -inputfile <file path>
```

如本文前面部分所述，该角色可由与内置角色相同的方式进行分配。

如需了解如何创建、管理和分配自定义角色的详细信息，请参阅 [Azure RBAC 中的自定义角色](../role-based-access-control/custom-roles.md)。

## <a name="resource-locks"></a>资源锁

Azure 资源管理器支持另一种类型的安全控制，即锁定资源的能力。 资源锁应用于资源，并在所有用户和角色中有效。 有关详细信息，请参阅使用[Azure 资源管理器锁定资源](../azure-resource-manager/management/lock-resources.md)。

有两种类型的资源锁：**无法删除**和**只读**。 这些锁类型可以应用于专用 DNS 区域或单个记录集。  以下各节描述了几种常见情况以及如何使用资源锁支持它们。

### <a name="protecting-against-all-changes"></a>防止所有更改

为了防止进行更改，请对区域应用"只读"锁。 此锁阻止创建新记录集，以及修改或删除现有记录集。

可通过 Azure 门户创建区域级别的资源锁。  从 DNS 区域页上，选择“锁定”****，然后选择“+添加”****：

![使用 Azure 门户的区域级别资源锁](./media/dns-protect-private-zones-recordsets/locks1.png)

区域级资源锁也可以通过[Azure PowerShell](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcelock?view=latest)创建：

```azurepowershell-interactive
# Lock a DNS zone

$lvl = "<lock level>"
$lnm = "<lock name>"
$rsc = "<zone name>"
$rty = "Microsoft.Network/privateDnsZones"
$rsg = "<resource group name>"

New-AzResourceLock -LockLevel $lvl -LockName $lnm -ResourceName $rsc -ResourceType $rty -ResourceGroupName $rsg
```

也可[通过 Azure CLI](https://docs.microsoft.com/cli/azure/lock?view=azure-cli-latest#az-lock-create) 提供等效命令：

```azurecli-interactive
# Lock a DNS zone

az lock create \
--lock-type "<lock level>" \
--name "<lock name>" \
--resource-name "<zone name>" \
--namespace "Microsoft.Network" \
--resource-type "privateDnsZones" \
--resource-group "<resource group name>"
```
### <a name="protecting-individual-records"></a>保护单个记录

要防止对现有 DNS 记录集的修改，可将 ReadOnly 锁应用到记录集。

> [!NOTE]
> 将 CanNotDelete 锁应用到记录集不能达到有效控制。 它仅可防止记录集被删除，但不会阻止其被修改。  允许的修改包括添加和删除记录集中的记录，还包括删除所有记录，只留下空记录集。 这与从 DNS 解析视点中删除记录集具有相同的效果。

记录集级别资源锁定当前只能使用 Azure PowerShell 进行配置。  Azure 门户或 Azure CLI 不支持它们。

Azure PowerShell

```azurepowershell-interactive
# Lock a DNS record set

$lvl = "<lock level>"
$lnm = "<lock name>"
$rnm = "<zone name>/<record set name>"
$rty = "Microsoft.Network/privateDnsZones"
$rsg = "<resource group name>"

New-AzResourceLock -LockLevel $lvl -LockName $lnm -ResourceName $rnm -ResourceType $rty -ResourceGroupName $rsg
```
### <a name="protecting-against-zone-deletion"></a>防止区域删除

在 Azure DNS 中删除区域时，将删除区域中的所有记录集。  无法撤消此操作。 意外删除关键区域有可能产生巨大的业务影响。  防止意外区域删除非常重要。

在该区域应用 CanNotDelete 锁即可防止区域被删除。 锁由子资源继承。 锁可防止删除区域中的任何记录集。 如上文注释所述，它无效，因为记录仍然可以从现有记录集中删除。

作为替代方法，将 CanNotDelete 锁应用于区域中的记录集，如 SOA 记录集。 如果不删除记录集，也不会删除该区域。 此锁可防止区域删除，同时仍允许自由修改区域中的记录集。 如果尝试删除区域，Azure 资源管理器将检测到此删除。 删除还将删除 SOA 记录集，Azure 资源管理器会阻止调用，因为 SOA 已锁定。  而不会删除任何记录集。

以下 PowerShell 命令针对给定区域的 SOA 记录创建 CanNotDelete 锁：

```azurepowershell-interactive
# Protect against zone delete with CanNotDelete lock on the record set

$lvl = "CanNotDelete"
$lnm = "<lock name>"
$rnm = "<zone name>/@"
$rty = "Microsoft.Network/privateDnsZones/SOA"
$rsg = "<resource group name>"

New-AzResourceLock -LockLevel $lvl -LockName $lnm -ResourceName $rnm -ResourceType $rty -ResourceGroupName $rsg
```
防止意外删除区域的另一个选项是使用自定义角色。 此角色可确保用于管理区域的帐户没有区域删除权限。 

当您确实需要删除区域时，可以强制实施两步删除：

 - 首先，授予区域删除权限
 - 其次，授予删除区域的权限。

自定义角色适用于这些帐户访问的所有区域。 具有区域删除权限的帐户（如订阅所有者）仍可能意外删除区域。

可以同时使用资源锁定和自定义角色这两种方法，作为 DNS 区域保护的纵深防御方法。

## <a name="next-steps"></a>后续步骤

* 有关使用 RBAC 的详细信息，请参阅 [Azure 门户中的访问管理入门](../role-based-access-control/overview.md)。
* 有关使用资源锁的详细信息，请参阅[使用 Azure 资源管理器锁定资源](../azure-resource-manager/management/lock-resources.md)。
