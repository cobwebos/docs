---
title: Azure DNS 专用区域入门（使用 PowerShell）| Microsoft Docs
description: 了解如何在 Azure DNS 中创建专用 DNS 区域和记录。 这是有关使用 PowerShell 创建和管理第一个专用 DNS 区域和记录的分步指南。
services: dns
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: dns
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/20/2017
ms.author: kumud
ms.openlocfilehash: d9e5c9b8caa5349fbcda9b71f7524fb9e99b976c
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2018
---
# <a name="get-started-with-azure-dns-private-zones-using-powershell"></a>Azure DNS 专用区域入门（使用 PowerShell）

本文将逐步引导你完成使用 PowerShell 创建第一个专用 DNS 区域和记录的步骤。

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

DNS 区域用来托管某个特定域的 DNS 记录。 若要开始在 Azure DNS 中托管域，需要为该域名创建 DNS 区域。 随后会在此 DNS 区域内为每个 DNS 记录创建域。 若要向虚拟网络发布专用 DNS 区域，请指定一个列表，其中包含允许在区域中解析记录的虚拟网络。  这些虚拟网络称为“解析虚拟网络”。  也可指定一个虚拟网络，让 Azure DNS 在创建 VM、更改 IP 或销毁 VM 时为其保留主机名记录。  这些虚拟网络称为“注册虚拟网络”。

# <a name="get-the-preview-powershell-modules"></a>获取预览版 PowerShell 模块
本文中的说明假设读者已安装并登录到 Azure PowerShell，并已安装“专用区域”功能所需的模块。 

[!INCLUDE [dns-powershell-setup](../../includes/dns-powershell-setup-include.md)]

## <a name="create-the-resource-group"></a>创建资源组

在创建 DNS 区域之前，创建了包含 DNS 区域的资源组。 以下示例显示了命令。

```powershell
New-AzureRMResourceGroup -name MyResourceGroup -location "westus"
```

## <a name="create-a-dns-private-zone"></a>创建 DNS 专用区域

结合 ZoneType 参数的“Private”值使用 `New-AzureRmDnsZone` cmdlet 创建 DNS 区域。 以下示例在名为“MyResourceGroup”的资源组中创建名为“contoso.local”的 DNS 区域，并将 DNS 区域提供给名为“MyAzureVnet”的虚拟网络。 使用该示例创建 DNS 区域，将相应的值替换成自己的值。

请注意，如果省略 ZoneType 参数，则会创建公共区域。因此，如果需要创建专用区域，则必须指定此参数。 

```powershell
$vnet = Get-AzureRmVirtualNetwork -Name MyAzureVnet -ResourceGroupName VnetResourceGroup
New-AzureRmDnsZone -Name contoso.local -ResourceGroupName MyResourceGroup -ZoneType Private -ResolutionVirtualNetworkId @($vnet.Id)
```

如果需要 Azure 在区域中自动创建主机名记录，请使用参数 *RegistrationVirtualNetworkId* 而非 *ResolutionVirtualNetworkId*。  将自动允许注册虚拟网络进行解析。

```powershell
$vnet = Get-AzureRmVirtualNetwork -Name MyAzureVnet -ResourceGroupName VnetResourceGroup
New-AzureRmDnsZone -Name contoso.local -ResourceGroupName MyResourceGroup -ZoneType Private -RegistrationVirtualNetworkId @($vnet.Id)
```

## <a name="create-a-dns-record"></a>创建 DNS 记录

可以使用 `New-AzureRmDnsRecordSet` cmdlet 创建记录集。 下面的示例在 DNS 区域“contoso.local”的资源组“MyResourceGroup”中创建相对名称为“db”的一个记录集。 记录集的完全限定名称为“db.contoso.local”。 记录类型为“A”，IP 地址为“10.0.0.4”，TTL 为 3600 秒。

```powershell
New-AzureRmDnsRecordSet -Name db -RecordType A -ZoneName contoso.local -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -IPv4Address "10.0.0.4")
```

对于其他记录类型，对于具有一条以上记录的记录集，若要修改现有记录，请参阅[使用 Azure PowerShell 管理 DNS 记录和记录集](dns-operations-recordsets.md)。 

## <a name="view-records"></a>查看记录

若要列出区域中的 DNS 记录，请使用：

```powershell
Get-AzureRmDnsRecordSet -ZoneName contoso.local -ResourceGroupName MyResourceGroup
```

# <a name="list-dns-private-zones"></a>列出 Azure DNS 专用区域

通过省略 `Get-AzureRmDnsZone` 中的区域名称，可以枚举资源组中的所有区域。 此操作将返回区域对象的数组。

```powershell
$zoneList = Get-AzureRmDnsZone -ResourceGroupName MyAzureResourceGroup
```

通过省略 `Get-AzureRmDnsZone` 的区域名和资源组名，可枚举 Azure 订阅中的所有区域。

```powershell
$zoneList = Get-AzureRmDnsZone
```

## <a name="update-a-dns-private-zone"></a>更新 DNS 专用区域

可以使用 `Set-AzureRmDnsZone` 对 DNS 区域资源进行更改。 此 cmdlet 不会更新区域中的任何 DNS 记录集（请参阅[如何管理 DNS 记录](dns-operations-recordsets.md)）。 该操作仅可用于更新区域资源本身的属性。 可写的区域属性目前限制为[区域资源的 Azure 资源管理器“标记”](dns-zones-records.md#tags)，以及专用区域的“RegistrationVirtualNetworkId”和“ResolutionVirtualNetworkId”参数。

以下示例将已链接到某个区域的注册虚拟网络替换为新的 MyNewAzureVnet。

请注意，与 create 不同，不能为 update 指定 ZoneType 参数。 

```powershell
$vnet = Get-AzureRmVirtualNetwork -Name MyNewAzureVnet -ResourceGroupName MyResourceGroup
Set-AzureRmDnsZone -Name contoso.local -ResourceGroupName MyResourceGroup -RegistrationVirtualNetworkId @($vnet.Id)
```

以下示例将已链接到某个区域的解析虚拟网络替换为名为“MyNewAzureVnet”的新虚拟网络。

```powershell
$vnet = Get-AzureRmVirtualNetwork -Name MyNewAzureVnet -ResourceGroupName MyResourceGroup
Set-AzureRmDnsZone -Name contoso.local -ResourceGroupName MyResourceGroup -ResolutionVirtualNetworkId @($vnet.Id)
```

## <a name="delete-a-dns-private-zone"></a>删除 DNS 专用区域

可以像删除公共区域一样，使用 `Remove-AzureRmDnsZone` cmdlet 删除 DNS 专用区域。

> [!NOTE]
> 删除 DNS 区域也会删除该区域中的所有 DNS 记录。 此操作不可撤消。 如果 DNS 区域在使用中，则使用该区域的服务在区域删除后将无效。
>
>若要防止意外删除区域，请参阅[如何保护 DNS 区域和记录](dns-protect-zones-recordsets.md)。

使用以下两种方式中的一种删除 DNS 区域：

### <a name="specify-the-zone-using-the-zone-name-and-resource-group-name"></a>指定使用区域名称和资源组的区域

```powershell
Remove-AzureRmDnsZone -Name contoso.local -ResourceGroupName MyAzureResourceGroup
```

### <a name="specify-the-zone-using-a-zone-object"></a>指定使用 $zone 对象的区域

可使用 `Get-AzureRmDnsZone` 返回的 `$zone` 对象指定要删除的区域。

```powershell
$zone = Get-AzureRmDnsZone -Name contoso.local -ResourceGroupName MyResourceGroup
Remove-AzureRmDnsZone -Zone $zone
```

区域对象也可以通过管道输送，而不是作为参数传递：

```powershell
Get-AzureRmDnsZone -Name contoso.local -ResourceGroupName MyResourceGroup | Remove-AzureRmDnsZone

```

## <a name="confirmation-prompts"></a>确认提示

`New-AzureRmDnsZone`、`Set-AzureRmDnsZone` 和 `Remove-AzureRmDnsZone` cmdlet 都支持确认提示。

如果 `$ConfirmPreference` PowerShell 首选项变量的值为 `Medium` 或更低，则 `New-AzureRmDnsZone` 和 `Set-AzureRmDnsZone` 都会提示用户进行确认。 由于删除 DNS 区域存在的潜在影响力较大，如果 `$ConfirmPreference` PowerShell 变量拥有除 `None` 之外的其他值，则 `Remove-AzureRmDnsZone` cmdlet 会提示用户进行确认。

由于 `$ConfirmPreference` 的默认值为 `High`，则默认情况下仅 `Remove-AzureRmDnsZone` 会提示用户进行确认。

可以使用 `-Confirm` 参数重写当前的 `$ConfirmPreference` 设置。 如果指定 `-Confirm` 或 `-Confirm:$True`，cmdlet 会在运行之前提示用户进行确认。 如果指定 `-Confirm:$False`，cmdlet 不会提示用户进行确认。

有关 `-Confirm` 和 `$ConfirmPreference` 的详细信息，请参阅 [About Preference Variables](https://msdn.microsoft.com/powershell/reference/5.1/Microsoft.PowerShell.Core/about/about_Preference_Variables)（关于首选项变量）。


## <a name="delete-all-resources"></a>删除所有资源

若要删除在本文中创建的所有资源，请执行以下步骤：

```powershell
Remove-AzureRMResourceGroup -Name MyResourceGroup
```

## <a name="next-steps"></a>后续步骤

若要详细了解专用 DNS 区域，请参阅[将 Azure DNS 用于专用域](private-dns-overview.md)。

请继续阅读[专用区域场景](./private-dns-scenarios.md)，了解可以通过 Azure DNS 中的专用区域实现的一些常见场景。

若要了解有关管理 Azure DNS 中的 DNS 记录的详细信息，请参阅[使用 PowerShell 管理 Azure DNS 中的 DNS 记录和记录集](dns-operations-recordsets.md)。

