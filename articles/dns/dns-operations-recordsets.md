---
title: "使用 Azure 门户来管理 DNS 记录集和记录 | Microsoft Docs"
description: "当在 Azure DNS 上托管域时在 Azure DNS 上管理 DNS 记录集和记录。 记录集和记录上的操作的所有 PowerShell 命令。"
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.assetid: 7136a373-0682-471c-9c28-9e00d2add9c2
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/16/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 02d720a04fdc0fa302c2cb29b0af35ee92c14b3b
ms.openlocfilehash: e3f7967843b3d9e38c79b45d90e333192fd3a900

---

# <a name="manage-dns-records-and-record-sets-by-using-powershell"></a>使用 PowerShell 管理 DNS 记录和记录集

> [!div class="op_single_selector"]
> * [Azure 门户](dns-operations-recordsets-portal.md)
> * [Azure CLI](dns-operations-recordsets-cli.md)
> * [PowerShell](dns-operations-recordsets.md)

本文演示如何通过使用 Windows PowerShell 来管理 DNS 区域的记录集和记录。

请务必了解 DNS 记录集和单独的 DNS 记录之间的差异。 记录集是区域中具有相同名称和相同类型的记录的集合。 有关详细信息，请参阅[使用 Azure 门户创建 DNS 记录集和记录](dns-getstarted-create-recordset-portal.md)。

若要管理记录集和记录，需要安装最新版本的 Azure Resource Manager PowerShell cmdlet。 有关详细信息，请参阅[如何安装和配置 Azure PowerShell](../powershell-install-configure.md)。 有关使用 PowerShell 的详细信息，请参阅[将 Azure PowerShell 与 Azure Resource Manager 结合使用](../powershell-azure-resource-manager.md)。

## <a name="create-a-new-record-set-and-a-record"></a>创建新的记录集和记录

若要使用 PowerShell 创建记录集，请参阅[使用 PowerShell 创建 DNS 记录集和记录](dns-getstarted-create-recordset.md)。

## <a name="get-a-record-set"></a>获取记录集

若要检索现有记录集，请使用 `Get-AzureRmDnsRecordSet`。 指定记录集的相对名称、记录类型和区域。

```powershell
$rs = Get-AzureRmDnsRecordSet -Name www -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
```

对于 `New-AzureRmDnsRecordSet`，记录名称必须是相对名称，这意味着它必须排除区域名称。

可以通过使用区域名称和资源组名称，或通过使用区域对象，来指定区域：

```powershell
$zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
$rs = Get-AzureRmDnsRecordSet -Name www -RecordType A -Zone $zone
```

`Get-AzureRmDnsRecordSet` 返回一个本地对象，该对象表示在 Azure DNS 中创建的记录集。

## <a name="list-record-sets"></a>列出记录集

还可在省略 *Name* 和/或 *RecordType* 参数的情况下使用 `Get-AzureRmDnsRecordSet` 列出记录集。

### <a name="to-list-all-record-sets"></a>列出所有记录集

此示例返回所有记录集，而不考虑名称或记录类型：

```powershell
$list = Get-AzureRmDnsRecordSet -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
```

### <a name="to-list-record-sets-of-a-given-record-type"></a>列出给定记录类型的记录集

此示例返回所有与给定记录类型匹配的记录集。 在此例中，返回的记录集为“A”记录：

```powershell
$list = Get-AzureRmDnsRecordSet -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
```

可以通过使用 `-ZoneName` 和 `-ResourceGroupName` 参数（如下所示），或通过指定区域对象，来指定区域：

```powershell
$zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
$list = Get-AzureRmDnsRecordSet -Zone $zone
```

## <a name="add-a-record-to-a-record-set"></a>将记录添加到记录集

可以使用 `Add-AzureRmDnsRecordConfig` cmdlet 将记录添加到记录集。 这是一种脱机操作。 仅更改代表记录集的本地对象。

添加记录到记录集的参数会因记录集的类型而有所变化。 例如，在使用类型“A”记录集时，将只能使用参数 *-IPv4Address* 指定记录。

通过对 `Add-AzureRmDnsRecordConfig` 的其他调用，可以将更多记录添加到每个记录集。 可以将最多 20 条记录添加到任何记录集。 但是，类型为“CNAME”的记录集最多可以包含一条记录，记录集不能包含两个相同的记录。 可以创建空记录集（具有零个记录），但它不会出现在 Azure DNS 名称服务器中。

记录集包含所需的记录集合后，你需要使用 `Set-AzureRmDnsRecordSet` cmdlet 提交它。 提交记录集后，它将替换 Azure DNS 中的现有记录集。

### <a name="to-create-an-a-record-set-with-a-single-record"></a>创建一个包含一条记录的记录集

```powershell
$rs = New-AzureRmDnsRecordSet -Name "test-a" -RecordType A -Ttl 60 -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
Add-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address "1.2.3.4"
Set-AzureRmDnsRecordSet -RecordSet $rs
```

用于创建记录的操作序列也可以*通过管道传递*，这意味着通过使用管道传递记录集对象，而不是将其作为参数传递。 例如：

```powershell
New-AzureRmDnsRecordSet -Name "test-a" -RecordType A -Ttl 60 -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup | Add-AzureRmDnsRecordConfig -Ipv4Address "1.2.3.4" | Set-AzureRmDnsRecordSet
```

### <a name="additional-record-type-examples"></a>其他记录类型示例

[!INCLUDE [dns-add-record-ps-include](../../includes/dns-add-record-ps-include.md)]

## <a name="modify-existing-record-sets"></a>修改现有记录集

用于修改现有记录集的步骤类似于在创建记录时所采取的步骤。 操作序列如下所示︰

1. 使用 `Get-AzureRmDnsRecordSet` 检索现有记录集。
2. 通过添加记录、删除记录、更改记录参数或更改记录集的生存时间 (TTL)，来修改记录集。 这是一种脱机操作。 仅更改代表记录集的本地对象。
3. 使用 `Set-AzureRmDnsRecordSet` cmdlet 提交更改。 这将替换 Azure DNS 中的现有记录集。

### <a name="to-update-a-record-in-an-existing-record-set"></a>更新现有记录集中的记录

在此示例中，我们更改现有“A”记录的 IP 地址：

```powershell
$rs = Get-AzureRmDnsRecordSet -name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
$rs.Records[0].Ipv4Address = "134.170.185.46"
Set-AzureRmDnsRecordSet -RecordSet $rs
```

`Set-AzureRmDnsRecordSet` Cmdlet 使用 etag 检查以确保并发更改不会被覆盖。 使用 *-Overwrite* 标志可取消这些检查。 有关详细信息，请参阅[关于 etag 和标记](dns-getstarted-create-dnszone.md#tagetag)。

### <a name="to-modify-an-soa-record"></a>修改 SOA 记录

不能在区域（名称 = "@").）顶点处的自动创建的 SOA 记录集中添加或删除记录。但是，可以修改 SOA 记录中的任何参数（“Host”除外）和记录集 TTL。

下面的示例演示如何更改 SOA 记录的 *Email* 属性：

```powershell
$rs = Get-AzureRmDnsRecordSet -Name "@" -RecordType SOA -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
$rs.Records[0].Email = "admin.contoso.com"
Set-AzureRmDnsRecordSet -RecordSet $rs
```

### <a name="to-modify-ns-records-at-the-zone-apex"></a>修改区域顶点处的 NS 记录

不能在区域（名称 = "@").）顶点处的自动创建的 NS 记录集中添加、删除或修改记录。允许的唯一更改是修改记录集 TTL。

下面的示例演示如何更改 NS 记录集的 TTL 属性：

```powershell
$rs = Get-AzureRmDnsRecordSet -Name "@" -RecordType NS -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
$rs.Ttl = 300
Set-AzureRmDnsRecordSet -RecordSet $rs
```

### <a name="to-add-records-to-an-existing-record-set"></a>将记录添加到现有记录集

在此示例中，我们将两个其他 MX 记录添加到现有记录集：

```powershell
$rs = Get-AzureRmDnsRecordSet -name "test-mx" -RecordType MX -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
Add-AzureRmDnsRecordConfig -RecordSet $rs -Exchange "mail2.contoso.com" -Preference 10
Add-AzureRmDnsRecordConfig -RecordSet $rs -Exchange "mail3.contoso.com" -Preference 20
Set-AzureRmDnsRecordSet -RecordSet $rs
```

## <a name="remove-a-record-from-an-existing-record-set"></a>从现有记录集中删除记录

可通过使用 `Remove-AzureRmDnsRecordConfig` 从记录集中删除记录。 要删除的记录必须与所有参数中的现有记录完全匹配。 必须通过使用 `Set-AzureRmDnsRecordSet` 提交更改。

从记录集中删除最后一条记录不会删除记录集。 有关详细信息，请参阅下面的[删除记录集](#delete-a-record-set)。

```powershell
$rs = Get-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
Remove-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address "1.2.3.4"
Set-AzureRmDnsRecordSet -RecordSet $rs
```

用于从记录集删除记录的操作序列也可以通过管道传递，这意味着通过使用管道传递记录集对象，而不是将其作为参数传递。 例如：

```powershell
Get-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup | Remove-AzureRmDnsRecordConfig -Ipv4Address "1.2.3.4" | Set-AzureRmDnsRecordSet
```

### <a name="remove-an-aaaa-record-from-a-record-set"></a>从记录集中删除 AAAA 记录

```powershell
$rs = Get-AzureRmDnsRecordSet -Name "test-aaaa" -RecordType AAAA -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
Remove-AzureRmDnsRecordConfig -RecordSet $rs -Ipv6Address "2607:f8b0:4009:1803::1005"
Set-AzureRmDnsRecordSet -RecordSet $rs
```

### <a name="remove-a-cname-record-from-a-record-set"></a>从记录集中删除 CNAME 记录

由于 CNAME 记录集最多可以包含一条记录，删除该记录会留下一个空的记录集。

```powershell
$rs =  Get-AzureRmDnsRecordSet -name "test-cname" -RecordType CNAME -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
Remove-AzureRmDnsRecordConfig -RecordSet $rs -Cname "www.contoso.com"
Set-AzureRmDnsRecordSet -RecordSet $rs
```

### <a name="remove-an-mx-record-from-a-record-set"></a>从记录集中删除 MX 记录

```powershell
$rs = Get-AzureRmDnsRecordSet -name "test-mx" -RecordType MX -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
Remove-AzureRmDnsRecordConfig -RecordSet $rs -Exchange "mail.contoso.com" -Preference 5
Set-AzureRmDnsRecordSet -RecordSet $rs
```

### <a name="remove-an-ns-record-from-record-set"></a>从记录集中删除 NS 记录

```powershell
$rs = Get-AzureRmDnsRecordSet -Name "test-ns" -RecordType NS -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
Remove-AzureRmDnsRecordConfig -RecordSet $rs -Nsdname "ns1.contoso.com"
Set-AzureRmDnsRecordSet -RecordSet $rs
```

### <a name="remove-an-srv-record-from-a-record-set"></a>从记录集中删除 SRV 记录

```powershell
$rs = Get-AzureRmDnsRecordSet -Name "_sip._tls" -RecordType SRV -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
Remove-AzureRmDnsRecordConfig -RecordSet $rs -Priority 0 -Weight 5 -Port 8080 -Target "sip.contoso.com"
Set-AzureRmDnsRecordSet -RecordSet $rs
```

### <a name="remove-a-txt-record-from-a-record-set"></a>从记录集中删除 TXT 记录

```powershell
$rs = Get-AzureRmDnsRecordSet -Name "test-txt" -RecordType TXT -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
Remove-AzureRmDnsRecordConfig -RecordSet $rs -Value "This is a TXT record"
Set-AzureRmDnsRecordSet -RecordSet $rs
```

## <a name="delete-a-record-set"></a>删除记录集

可以通过使用 `Remove-AzureRmDnsRecordSet` cmdlet 删除记录集。 不能在创建区域时自动创建的区域顶点处（名称 = "@")）删除 SOA 和 NS 记录集。 如果删除区域，它们将被自动删除。

使用以下三种方法之一删除记录集：

### <a name="specify-all-the-parameters-by-name"></a>按名称指定所有参数

可以使用可选的 *-Force* 开关来取消此确认提示。

```powershell
Remove-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup [-Force]
```

### <a name="specify-the-record-set-by-name-and-type-and-specify-the-zone-by-object"></a>按名称和类型指定记录集，并按对象指定区域

```powershell
$zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
Remove-AzureRmDnsRecordSet -Name "test-a" -RecordType A -Zone $zone [-Force]
```

### <a name="specify-the-record-set-by-object"></a>按对象指定记录集

```powershell
$rs = Get-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
Remove-AzureRmDnsRecordSet -RecordSet $rs [-Overwrite] [-Force]
```

当使用对象指定记录集时，它启用 etag 检查以确保并发更改不被删除。 可选 *-Overwrite* 标志将取消这些检查。 有关详细信息，请参阅 [etag 和标记](dns-getstarted-create-dnszone.md#tagetag)。

记录集对象也可以通过管道输送，而不是作为参数传递：

```powershell
Get-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup | Remove-AzureRmDnsRecordSet [-Overwrite] [-Force]
```

## <a name="next-steps"></a>后续步骤

有关 Azure DNS 的详细信息，请参阅 [Azure DNS 概述](dns-overview.md)。 有关自动执行 DNS 的信息，请参阅[使用 .NET SDK 创建 DNS 区域和记录集](dns-sdk.md)。

有关反向 DNS 记录的详细信息，请参阅[如何使用 PowerShell 管理服务的反向 DNS 记录](dns-reverse-dns-record-operations-ps.md)。



<!--HONumber=Nov16_HO3-->


