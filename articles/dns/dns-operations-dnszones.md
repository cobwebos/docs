---
title: "在 Azure DNS 中管理 DNS 区域 - PowerShell | Microsoft Docs"
description: "可以使用 Azure PowerShell 管理 DNS 区域。 本文介绍如何在 Azure DNS 上更新、删除和创建 DNS 区域"
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.assetid: a67992ab-8166-4052-9b28-554c5a39e60c
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/22/2016
ms.author: gwallace
ms.openlocfilehash: 3f28e70bb6ef46f53375d256a520db40fcb71ad0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-manage-dns-zones-using-powershell"></a>如何使用 PowerShell 管理 DNS 区域

> [!div class="op_single_selector"]
> * [门户](dns-operations-dnszones-portal.md)
> * [PowerShell](dns-operations-dnszones.md)
> * [Azure CLI 1.0](dns-operations-dnszones-cli-nodejs.md)
> * [Azure CLI 2.0](dns-operations-dnszones-cli.md)

本文介绍如何使用 Azure PowerShell 管理 DNS 区域。 也可使用跨平台的 [Azure CLI](dns-operations-dnszones-cli.md) 或 Azure 门户管理 DNS 区域。

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

[!INCLUDE [dns-powershell-setup](../../includes/dns-powershell-setup-include.md)]


## <a name="create-a-dns-zone"></a>创建 DNS 区域

通过使用 `New-AzureRmDnsZone` cmdlet 创建 DNS 区域。

以下示例在名为 *MyResourceGroup* 的资源组中创建名为 *contoso.com* 的 DNS 区域：

```powershell
New-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
```

以下示例演示如何使用两个 [Azure Resource Manager 标记](dns-zones-records.md#tags)（*project = demo* 和 *env = test*）创建 DNS 区域：

```powershell
New-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup -Tag @{ project="demo"; env="test" }
```

Azure DNS 现在还支持专用 DNS 区域（当前为预览功能）。  有关如何创建专用 DNS 区域的示例，请参阅 [Azure DNS 专用区域入门（使用 PowerShell）](./private-dns-getstarted-powershell.md)。

## <a name="get-a-dns-zone"></a>获取 DNS 区域

若要检索 DNS 区域，请使用 `Get-AzureRmDnsZone` cmdlet。 此操作将返回与 Azure DNS 中现有区域相对应的 DNS 区域对象。 此对象包含有关区域的数据（例如记录集数），但不包含记录集本身（请参见 `Get-AzureRmDnsRecordSet`）。

```powershell
Get-AzureRmDnsZone -Name contoso.com –ResourceGroupName MyAzureResourceGroup

Name                  : contoso.com
ResourceGroupName     : myresourcegroup
Etag                  : 00000003-0000-0000-8ec2-f4879750d201
Tags                  : {project, env}
NameServers           : {ns1-01.azure-dns.com., ns2-01.azure-dns.net., ns3-01.azure-dns.org.,
                        ns4-01.azure-dns.info.}
NumberOfRecordSets    : 2
MaxNumberOfRecordSets : 5000
```

## <a name="list-dns-zones"></a>列出 DNS 区域

通过省略 `Get-AzureRmDnsZone` 中的区域名称，可以枚举资源组中的所有区域。 此操作将返回区域对象的数组。

```powershell
$zoneList = Get-AzureRmDnsZone -ResourceGroupName MyAzureResourceGroup
```

通过省略 `Get-AzureRmDnsZone` 的区域名和资源组名，可枚举 Azure 订阅中的所有区域。

```powershell
$zoneList = Get-AzureRmDnsZone
```

## <a name="update-a-dns-zone"></a>更新 DNS 区域

可以使用 `Set-AzureRmDnsZone` 对 DNS 区域资源进行更改。 此 cmdlet 不会更新区域中的任何 DNS 记录集（请参阅[如何管理 DNS 记录](dns-operations-recordsets.md)）。 该操作仅可用于更新区域资源本身的属性。 可写区域属性目前仅限于[区域资源的 Azure Resource Manager“标记”](dns-zones-records.md#tags)。

使用以下两种方式中的一种更新 DNS 区域：

### <a name="specify-the-zone-using-the-zone-name-and-resource-group"></a>指定使用区域名称和资源组的区域

此方法使用指定值替换现有区域标记。

```powershell
Set-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup -Tag @{ project="demo"; env="test" }
```

### <a name="specify-the-zone-using-a-zone-object"></a>指定使用 $zone 对象的区域

此方法检索现有区域对象、修改标记，并提交更改。 如此一来，可保留现有标记。

```powershell
# Get the zone object
$zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup

# Remove an existing tag
$zone.Tags.Remove("project")

# Add a new tag
$zone.Tags.Add("status","approved")

# Commit changes
Set-AzureRmDnsZone -Zone $zone
```

使用 `Set-AzureRmDnsZone` 与 $zone 对象时，[Etag 检查](dns-zones-records.md#etags)将用于确保不会覆盖并发更改。 可以使用可选的 `-Overwrite` 开关取消这些检查。

## <a name="delete-a-dns-zone"></a>删除 DNS 区域

可以使用 `Remove-AzureRmDnsZone` cmdlet 删除 DNS 区域。

> [!NOTE]
> 删除 DNS 区域也会删除该区域中的所有 DNS 记录。 此操作不可撤消。 如果 DNS 区域在使用中，则使用该区域的服务在区域删除后将无效。
>
>若要防止意外删除区域，请参阅[如何保护 DNS 区域和记录](dns-protect-zones-recordsets.md)。


使用以下两种方式中的一种删除 DNS 区域：

### <a name="specify-the-zone-using-the-zone-name-and-resource-group-name"></a>指定使用区域名称和资源组的区域

```powershell
Remove-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
```

### <a name="specify-the-zone-using-a-zone-object"></a>指定使用 $zone 对象的区域

可使用 `Get-AzureRmDnsZone` 返回的 `$zone` 对象指定要删除的区域。

```powershell
$zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
Remove-AzureRmDnsZone -Zone $zone
```

区域对象也可以通过管道输送，而不是作为参数传递：

```powershell
Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup | Remove-AzureRmDnsZone

```

和 `Set-AzureRmDnsZone` 一样，指定使用 `$zone` 对象的区域使 Etag 检查能够确保不会删除并发更改。 使用 `-Overwrite` 开关取消这些检查。

## <a name="confirmation-prompts"></a>确认提示

`New-AzureRmDnsZone`、`Set-AzureRmDnsZone` 和 `Remove-AzureRmDnsZone` cmdlet 都支持确认提示。

如果 `$ConfirmPreference` PowerShell 首选项变量的值为 `Medium` 或更低，则 `New-AzureRmDnsZone` 和 `Set-AzureRmDnsZone` 都会提示用户进行确认。 由于删除 DNS 区域存在的潜在影响力较大，如果 `$ConfirmPreference` PowerShell 变量拥有除 `None` 之外的其他值，则 `Remove-AzureRmDnsZone` cmdlet 会提示用户进行确认。

由于 `$ConfirmPreference` 的默认值为 `High`，则默认情况下仅 `Remove-AzureRmDnsZone` 会提示用户进行确认。

可以使用 `-Confirm` 参数重写当前的 `$ConfirmPreference` 设置。 如果指定 `-Confirm` 或 `-Confirm:$True`，cmdlet 会在运行之前提示用户进行确认。 如果指定 `-Confirm:$False`，cmdlet 不会提示用户进行确认。

有关 `-Confirm` 和 `$ConfirmPreference` 的详细信息，请参阅 [About Preference Variables](https://msdn.microsoft.com/powershell/reference/5.1/Microsoft.PowerShell.Core/about/about_Preference_Variables)（关于首选项变量）。

## <a name="next-steps"></a>后续步骤

了解如何在 DNS 区域中[管理记录集和记录](dns-operations-recordsets.md)。
<br>
了解如何[将域委派给 Azure DNS](dns-domain-delegation.md)。
<br>
查看 [Azure DNS PowerShell 参考文档](/powershell/module/azurerm.dns)。

