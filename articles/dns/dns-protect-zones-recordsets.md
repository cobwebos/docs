---
title: 保护 DNS 区域和记录-Azure DNS
description: 在此学习路径中，开始保护 Microsoft Azure DNS 中的 DNS 区域和记录集。
services: dns
author: asudbring
ms.service: dns
ms.topic: article
ms.date: 2/20/2020
ms.author: allensu
ms.openlocfilehash: 89a945f146601084795b2e12a721a03a1b96aaea
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/14/2020
ms.locfileid: "79371471"
---
# <a name="how-to-protect-dns-zones-and-records"></a>如何保护 DNS 区域和记录

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

DNS 区域和记录是关键资源。 删除 DNS 区域或单个 DNS 记录可能会导致服务中断。 请务必保护 DNS 区域和记录，防止未经授权或意外的更改。

本文介绍 Azure DNS 如何使你能够针对此类更改保护专用 DNS 区域和记录。  我们应用 Azure 资源管理器提供的两项强大的证券功能：[基于角色的访问控制](../role-based-access-control/overview.md)和[资源锁](../azure-resource-manager/management/lock-resources.md)。

## <a name="role-based-access-control"></a>基于角色的访问控制

Azure 基于角色的访问控制 (RBAC) 可用于对 Azure 用户、组和资源进行细致的访问管理。 使用 RBAC，可以授予用户所需的访问级别。 如需了解 RBAC 如何帮助你管理访问权限的详细信息，请参阅[什么是基于角色的访问控制](../role-based-access-control/overview.md)。

### <a name="the-dns-zone-contributor-role"></a>“DNS 区域参与者”角色

DNS 区域参与者角色是用于管理专用 DNS 资源的内置角色。 此角色应用于用户或组，使他们能够管理 DNS 资源。

资源组*myResourceGroup*包含 Contoso Corporation 的五个区域。 授予 DNS 管理员对该资源组的 DNS 区域参与者权限，可以完全控制这些 DNS 区域。 它避免授予不必要的权限。 DNS 管理员无法创建或停止虚拟机。

分配 RBAC 权限最简单方法是[通过 Azure 门户](../role-based-access-control/role-assignments-portal.md)进行分配。  

对于资源组，请打开 "**访问控制（IAM）** "，选择 "**添加**"，然后选择 " **DNS 区域参与者**" 角色。 选择所需的用户或组来授予权限。

![使用 Azure 门户的资源组级别 RBAC](./media/dns-protect-zones-recordsets/rbac1.png)

也可以[使用 Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)授予权限：

```azurepowershell
# Grant 'DNS Zone Contributor' permissions to all zones in a resource group

$usr = "<user email address>"
$rol = "DNS Zone Contributor"
$rsg = "<resource group name>"

New-AzRoleAssignment -SignInName $usr -RoleDefinitionName $rol -ResourceGroupName $rsg
```

也可[通过 Azure CLI](../role-based-access-control/role-assignments-cli.md) 提供等效命令：

```azurecli
# Grant 'DNS Zone Contributor' permissions to all zones in a resource group

az role assignment create \
--assignee "<user email address>" \
--role "DNS Zone Contributor" \
--resource-group "<resource group name>"
```

### <a name="zone-level-rbac"></a>区域级别 RBAC

Azure RBAC 规则可应用于订阅，资源组或单个资源。 该资源可以是单个 DNS 区域，也可以是单个记录集。

例如，资源组*myResourceGroup*包含区域*contoso.com*和子*customers.contoso.com*。 为每个客户帐户创建 CNAME 记录。 用于管理 CNAME 记录的管理员帐户被分配了在*customers.contoso.com*区域中创建记录的权限。 帐户只能管理*customers.contoso.com* 。

可以通过 Azure 门户授予区域级别的 RBAC 权限。  为区域打开 "**访问控制（IAM）** "，选择 "**添加**"，然后选择 " **DNS 区域参与者**" 角色，并选择所需的用户或组来授予权限。

![使用 Azure 门户的 DNS 区域级别 RBAC](./media/dns-protect-zones-recordsets/rbac2.png)

也可以[使用 Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)授予权限：

```azurepowershell
# Grant 'DNS Zone Contributor' permissions to a specific zone

$usr = "<user email address>"
$rol = "DNS Zone Contributor"
$rsg = "<resource group name>"
$zon = "<zone name>"
$typ = "Microsoft.Network/DNSZones"

New-AzRoleAssignment -SignInName $usr -RoleDefinitionName $rol -ResourceGroupName $rsg -ResourceName $zon -ResourceType $typ
```

也可[通过 Azure CLI](../role-based-access-control/role-assignments-cli.md) 提供等效命令：

```azurecli
# Grant 'DNS Zone Contributor' permissions to a specific zone

az role assignment create \
--assignee <user email address> \
--role "DNS Zone Contributor" \
--scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/DnsZones/<zone name>/"
```

### <a name="record-set-level-rbac"></a>记录集级别 RBAC

在记录集级别应用权限。  用户被授予对其所需的项的控制，无法进行任何其他更改。

可以使用 "记录集" 页中的 "**访问控制（IAM）** " 按钮，通过 Azure 门户配置记录集级别的 RBAC 权限：

![使用 Azure 门户的记录集级别 RBAC](./media/dns-protect-zones-recordsets/rbac3.png)

也可以[使用 Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)授予记录集级别 RBAC 权限：

```azurepowershell
# Grant permissions to a specific record set

$usr = "<user email address>"
$rol = "DNS Zone Contributor"
$sco = 
"/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/dnszones/<zone name>/<record type>/<record name>"

New-AzRoleAssignment -SignInName $usr -RoleDefinitionName $rol -Scope $sco
```

也可[通过 Azure CLI](../role-based-access-control/role-assignments-cli.md) 提供等效命令：

```azurecli
# Grant permissions to a specific record set

az role assignment create \
--assignee "<user email address>" \
--role "DNS Zone Contributor" \
--scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/dnszones/<zone name>/<record type>/<record name>"
```

### <a name="custom-roles"></a>自定义角色

内置“DNS 区域参与者”角色可以完全控制 DNS 资源。 可以构建自己的自定义 Azure 角色，以提供更精细的控制。

用于管理 Cname 的帐户仅授予管理 CNAME 记录的权限。 帐户无法修改其他类型的记录。 该帐户无法执行区域级别的操作，如区域删除。

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
        "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ]
}
```

操作属性定义以下特定于 DNS 的权限：

* `Microsoft.Network/dnsZones/CNAME/*` 授予对 CNAME 记录的完全控制权
* `Microsoft.Network/dnsZones/read` 授予读取 DNS 区域的权限，但不能修改它们，可查看创建 CNAME 的区域。

其余操作从 [DNS 区域参与者内置角色](../role-based-access-control/built-in-roles.md#dns-zone-contributor)中复制。

> [!NOTE]
> 使用自定义 RBAC 角色防止删除记录集的同时仍允许它们更新，这不是一种有效的控制方式。 此方法仅可防止记录集被删除，但不会阻止它们被修改。  允许的修改包括添加和删除记录集中的记录，还包括删除所有记录，只留下空记录集。 这与从 DNS 解析视点中删除记录集具有相同的效果。

当前无法通过 Azure 门户定义自定义角色定义。 可以使用 Azure PowerShell 创建基于此角色定义的自定义角色：

```azurepowershell
# Create new role definition based on input file
New-AzRoleDefinition -InputFile <file path>
```

也可以通过 Azure CLI 创建：

```azurecli
# Create new role definition based on input file
az role create -inputfile <file path>
```

如本文前面部分所述，该角色可由与内置角色相同的方式进行分配。

如需了解如何创建、管理和分配自定义角色的详细信息，请参阅 [Azure RBAC 中的自定义角色](../role-based-access-control/custom-roles.md)。

## <a name="resource-locks"></a>资源锁

Azure 资源管理器支持另一种安全控制类型，即能够锁定资源。 资源锁适用于资源，并在所有用户和角色之间有效。 有关详细信息，请参阅 [使用 Azure 资源管理器锁定资源](../azure-resource-manager/management/lock-resources.md)。

有两种类型的资源锁： **CanNotDelete**和**ReadOnly**。 这些锁定类型可以应用到专用 DNS 区域，也可以应用于单个记录集。 以下各节描述了几种常见情况以及如何使用资源锁支持它们。

### <a name="protecting-against-all-changes"></a>防止所有更改

若要防止发生更改，请将 ReadOnly 锁应用于该区域。 此锁定会阻止创建新的记录集，以及修改或删除现有记录集。

可通过 Azure 门户创建区域级别的资源锁。  从 DNS 区域页上，选择“锁定”，然后选择“+添加”：

![使用 Azure 门户的区域级别资源锁](./media/dns-protect-zones-recordsets/locks1.png)

还可以通过[Azure PowerShell](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcelock?view=latest)创建区域级别的资源锁：

```azurepowershell
# Lock a DNS zone

$lvl = "<lock level>"
$lnm = "<lock name>"
$rsc = "<zone name>"
$rty = "Microsoft.Network/DNSZones"
$rsg = "<resource group name>"

New-AzResourceLock -LockLevel $lvl -LockName $lnm -ResourceName $rsc -ResourceType $rty -ResourceGroupName $rsg
```

也可[通过 Azure CLI](https://docs.microsoft.com/cli/azure/lock?view=azure-cli-latest#az-lock-create) 提供等效命令：

```azurecli
# Lock a DNS zone

az lock create \
--lock-type "<lock level>" \
--name "<lock name>" \
--resource-name "<zone name>" \
--namespace "Microsoft.Network" \
--resource-type "DnsZones" \
--resource-group "<resource group name>"
```

### <a name="protecting-individual-records"></a>保护单个记录

要防止对现有 DNS 记录集的修改，可将 ReadOnly 锁应用到记录集。

> [!NOTE]
> 将 CanNotDelete 锁应用到记录集不能达到有效控制。 它仅可防止记录集被删除，但不会阻止其被修改。  允许的修改包括添加和删除记录集中的记录，还包括删除所有记录，只留下空记录集。 这与从 DNS 解析视点中删除记录集具有相同的效果。

记录集级别资源锁定当前只能使用 Azure PowerShell 进行配置。  它们不受 Azure 门户或 Azure CLI 支持。

```azurepowershell
# Lock a DNS record set

$lvl = "<lock level>"
$lnm = "<lock name>"
$rsc = "<zone name>/<record set name>"
$rty = "Microsoft.Network/DNSZones/<record type>"
$rsg = "<resource group name>"

New-AzResourceLock -LockLevel $lvl -LockName $lnm -ResourceName $rsc -ResourceType $rty -ResourceGroupName $rsg
```

### <a name="protecting-against-zone-deletion"></a>防止区域删除

在 Azure DNS 中删除区域时，将删除该区域中的所有记录集。  此操作无法撤消。 意外删除关键区域有可能产生巨大的业务影响。  必须防止意外删除区域，这一点很重要。

在该区域应用 CanNotDelete 锁即可防止区域被删除。 锁由子资源继承。 锁定会阻止删除区域中的所有记录集。 如上文所述，这是无效的，因为仍可以从现有记录集中删除记录。

作为替代方法，请将 CanNotDelete 锁应用到区域中的记录集，如 SOA 记录集。 不删除记录集也不会删除该区域。 此锁定可防止区域删除，同时仍然允许在区域内自由修改记录集。 如果尝试删除区域，Azure 资源管理器会检测到此删除。 删除操作还会删除 SOA 记录集，Azure 资源管理器会阻止呼叫，因为 SOA 已锁定。  而不会删除任何记录集。

以下 PowerShell 命令针对给定区域的 SOA 记录创建 CanNotDelete 锁：

```azurepowershell
# Protect against zone delete with CanNotDelete lock on the record set

$lvl = "CanNotDelete"
$lnm = "<lock name>"
$rsc = "<zone name>/@"
$rty = "Microsoft.Network/DNSZones/SOA"
$rsg = "<resource group name>"

New-AzResourceLock -LockLevel $lvl -LockName $lnm -ResourceName $rsc -ResourceType $rty -ResourceGroupName $rsg
```

防止意外删除区域的另一种方法是使用自定义角色。 此角色确保用于管理区域的帐户不具有区域删除权限。 

确实需要删除区域时，可以强制执行两步删除：

 - 首先，授予区域删除权限
 - 其次，授予删除区域的权限。

自定义角色适用于这些帐户访问的所有区域。 具有区域删除权限的帐户（如订阅所有者）仍可能会意外删除区域。

可以同时使用资源锁和自定义角色这两种方法，作为 DNS 区域保护的深层防御方法。

## <a name="next-steps"></a>后续步骤

* 有关使用 RBAC 的详细信息，请参阅 [Azure 门户中的访问管理入门](../role-based-access-control/overview.md)。
* 有关使用资源锁的详细信息，请参阅[使用 Azure 资源管理器锁定资源](../azure-resource-manager/management/lock-resources.md)。
