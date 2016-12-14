---
title: "使用 PowerShell 管理 DNS 区域 | Microsoft Docs"
description: "可以使用 Azure PowerShell 管理 DNS 区域。 如何在 Azure DNS 上创建、更新和删除 DNS 区域"
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
ms.date: 08/16/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 02d720a04fdc0fa302c2cb29b0af35ee92c14b3b
ms.openlocfilehash: f6403718f106b5021ea5b41e0e91a11bd92d4830

---
# <a name="how-to-manage-dns-zones-using-powershell"></a>如何使用 PowerShell 管理 DNS 区域

> [!div class="op_single_selector"]
> * [Azure CLI](dns-operations-dnszones-cli.md)
> * [PowerShell](dns-operations-dnszones.md)

本文将介绍如何使用 PowerShell 来管理 DNS 区域。 要使用这些步骤，你需要安装最新版本的 Azure Resource Manager PowerShell cmdlet（1.0 或更高）。 有关安装 PowerShell cmdlet 的详细信息，请参阅 [如何安装和配置 Azure PowerShell](../powershell-install-configure.md) 。

## <a name="create-a-new-dns-zone"></a>创建新的 DNS 区域

若要创建 DNS 区域，请参阅[使用 PowerShell 创建 DNS 区域](dns-getstarted-create-dnszone.md)。

## <a name="get-a-dns-zone"></a>获取 DNS 区域

若要检索 DNS 区域，请使用 `Get-AzureRmDnsZone` cmdlet。 此操作将返回与 Azure DNS 中现有区域相对应的 DNS 区域对象。 此对象包含有关区域的数据（例如记录集数量），但不包含记录集本身。

```powershell
$zone = Get-AzureRmDnsZone -Name contoso.com –ResourceGroupName MyAzureResourceGroup
```

## <a name="list-dns-zones"></a>列出 DNS 区域

通过省略 `Get-AzureRmDnsZone` 中的区域名称，可以枚举资源组中的所有区域。 此操作将返回区域对象的数组。

```powershell
$zoneList = Get-AzureRmDnsZone -ResourceGroupName MyAzureResourceGroup
```

## <a name="update-a-dns-zone"></a>更新 DNS 区域

可以使用 `Set-AzureRmDnsZone` 对 DNS 区域资源进行更改。 这不会更新区域中的任何 DNS 记录集（请参阅[如何管理 DNS 记录](dns-operations-recordsets.md)）。 该操作仅可用于更新区域资源本身的属性。 这目前仅限于区域资源的 Azure Resource Manager“标记”。 有关详细信息，请参阅 [Etag 和标记](dns-getstarted-create-dnszone.md#tagetag)。

使用以下两种方式中的一种更新 DNS 区域：

### <a name="specify-the-zone-using-the-zone-name-and-resource-group"></a>指定使用区域名称和资源组的区域

```powershell
Set-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup [-Tag $tags]
```

### <a name="specify-the-zone-using-a-zone-object"></a>指定使用 $zone 对象的区域

从 `Get-AzureRmDnsZone` 指定使用 $zone 对象的区域。 使用 `Set-AzureRmDnsZone` 与 $zone 对象时，Etag 检查将用于确保不会覆盖并发更改。 可以使用可选的 *-Overwrite* 开关来取消这些检查。 有关详细信息，请参阅 [Etag 和标记](dns-getstarted-create-dnszone.md#tagetag)。

```powershell
$zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
<..modify $zone.Tags here...>
Set-AzureRmDnsZone -Zone $zone [-Overwrite]
```

## <a name="delete-a-dns-zone"></a>删除 DNS 区域

可以使用 Remove-AzureRmDnsZone cmdlet 删除 DNS 区域。

在 Azure DNS 中删除 DNS 区域之前，需要删除所有记录集（创建区域时自动创建的区域根目录下的 NS 和 SOA 记录除外）。

使用以下两种方式中的一种删除 DNS 区域：

### <a name="specify-the-zone-using-the-zone-name-and-resource-group-name"></a>指定使用区域名称和资源组的区域

此操作具有可选 `-Force` 开关，这可取消提示是否确实要删除 DNS 区域。

```powershell
Remove-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup [-Force]
```

### <a name="specify-the-zone-using-a-zone-object"></a>指定使用 $zone 对象的区域

从 `Get-AzureRmDnsZone` 指定使用 $zone 对象的区域。 此操作具有可选 `-Force` 开关，这可取消提示是否确实要删除 DNS 区域。 和 `Set-AzureRmDnsZone` 一样，指定使用 $zone 对象的区域使 Etag 检查能够确保不会删除并发更改。

可选 `-Overwrite` 标志将取消这些检查。 有关详细信息，请参阅 [Etag 和标记](dns-getstarted-create-dnszone.md#tagetag)。

```powershell
$zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
Remove-AzureRmDnsZone -Zone $zone [-Force] [-Overwrite]
```

区域对象也可以通过管道输送，而不是作为参数传递：

```powershell
Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup | Remove-AzureRmDnsZone [-Force] [-Overwrite]
```

## <a name="next-steps"></a>后续步骤

创建 DNS 区域后，创建 [记录集和记录](dns-getstarted-create-recordset.md) ，开始解析 Internet 域的名称。




<!--HONumber=Nov16_HO3-->


