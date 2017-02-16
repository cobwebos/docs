---
title: "使用 Azure PowerShell 管理 Azure DNS 中的 DNS 记录 | Microsoft Docs"
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
ms.date: 12/21/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: f8dcbdb573fb50d32dfdefbb52c7af71bdc1cb95
ms.openlocfilehash: 2264acb9c78a162adb7b9937568838ab5ec2c720

---

# <a name="manage-dns-records-in-azure-dns-using-azure-powershell"></a>使用 Azure PowerShell 管理 Azure DNS 中的 DNS 记录

> [!div class="op_single_selector"]
> * [Azure 门户](dns-operations-recordsets-portal.md)
> * [Azure CLI](dns-operations-recordsets-cli.md)
> * [PowerShell](dns-operations-recordsets.md)

本文介绍如何使用 Azure PowerShell 管理 DNS 区域的 DNS 记录。 也可使用跨平台的 [Azure CLI](dns-operations-recordsets-cli.md) 或 [Azure 门户](dns-operations-recordsets-portal.md)管理 DNS 记录。

本文中的示例假设用户已经[安装并登录 Azure PowerShell 以及创建了 DNS 区域](dns-operations-dnszones.md)。

## <a name="introduction"></a>介绍

在 Azure DNS 中创建 DNS 记录之前，首先需了解 Azure DNS 如何将 DNS 记录组织到 DNS 记录集中。

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

有关 Azure DNS 中的 DNS 记录的详细信息，请参阅 [DNS 区域和记录](dns-zones-records.md)。


## <a name="create-a-new-dns-record"></a>创建新的 DNS 记录

如果新记录与现有记录具有相同的名称和类型，则需[将其添加到现有记录集](#add-a-record-to-an-existing-record-set)。 如果新记录与所有现有记录的名称和类型都不同，则需创建新的记录集。 

### <a name="create-a-records-in-a-new-record-set"></a>在新的记录集中创建 A 记录

可以使用 `New-AzureRmDnsRecordSet` cmdlet 创建记录集。 创建记录集时，需指定记录集名称、区域、生存时间 (TTL)、记录类型，以及要创建的记录。

添加记录到记录集的参数会因记录集的类型而有所变化。 例如，在使用类型“A”记录集时，需使用参数 `-IPv4Address` 指定 IP 地址。 其他参数用于其他记录类型。 有关详细信息，请参阅[其他记录类型示例](#additional-record-type-examples)。

下面的示例在 DNS 区域“contoso.com”中创建具有相对名称“www”的记录集。 记录集的完全限定名称为“www.contoso.com”。 记录类型为“A”，TTL 为 3600 秒。 记录集包含单个记录，IP 地址为“1.2.3.4”

```powershell
New-AzureRmDnsRecordSet -Name www -RecordType A -ZoneName contoso.com -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -IPv4Address 1.2.3.4) 
```

若要在区域的“顶点”（在此例中为“contoso.com”）创建记录集，请使用记录集名称 "@"（包括引号）：

```powershell
New-AzureRmDnsRecordSet -Name "@" -RecordType A -ZoneName contoso.com -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -IPv4Address 1.2.3.4) 
```

如需创建包含多条记录的记录集，请先创建本地数组并添加记录，然后将数组传递到 `New-AzureRmDnsRecordSet`，如下所示：

```powershell
$aRecords = @()
$aRecords += New-AzureRmDnsRecordConfig -IPv4Address 1.2.3.4
$aRecords += New-AzureRmDnsRecordConfig -IPv4Address 2.3.4.5
New-AzureRmDnsRecordSet -Name www –ZoneName contoso.com -ResourceGroupName MyResourceGroup -Ttl 3600 -RecordType A -DnsRecords $aRecords
```

可使用[记录集元数据](dns-zones-records.md#tags-and-metadata)，以键-值对的形式将应用程序特定的数据与每个记录集相关联。 以下示例说明如何使用“dept=finance”和“environment=production”这两个元数据条目创建记录集

```powershell
New-AzureRmDnsRecordSet -Name www -RecordType A -ZoneName contoso.com -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -IPv4Address 1.2.3.4) -Metadata @{ dept="finance"; environment="production" } 
```

Azure DNS 还支持“空”记录集，此类记录集可充当占位符，用于在创建 DNS 记录之前保留某个 DNS 名称。 空记录集在 Azure DNS 控制平面可见，但也会显示在 Azure DNS 名称服务器上。 以下示例创建一个空记录集：

```powershell
New-AzureRmDnsRecordSet -Name www -RecordType A -ZoneName contoso.com -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords @()
```

## <a name="create-records-of-other-types"></a>创建其他类型的记录

详细了解如何创建“A”记录以后，还可通过以下示例了解如何创建 Azure DNS 支持的其他记录类型的记录。

每个示例中都演示了如何创建包含单条记录的记录集。 可以对“A”记录此前的示例进行修改，以便使用元数据创建其他类型的包含多个记录的记录集，或者创建空记录集。

我们没有提供创建 SOA 记录集的示例，因为 SOA 是随每个 DNS 区域一起创建和删除的，不能单独创建或删除。 但是，[可以修改 SOA，如后面的示例所示](#to-modify-an-SOA-record)。

### <a name="create-an-aaaa-record-set-with-a-single-record"></a>创建一个包含一条记录的 AAAA 记录集

```powershell
New-AzureRmDnsRecordSet -Name "test-aaaa" -RecordType AAAA -ZoneName contoso.com -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -Ipv6Address 2607:f8b0:4009:1803::1005) 
```

### <a name="create-a-cname-record-set-with-a-single-record"></a>创建一个包含一条记录的 CNAME 记录集

> [!NOTE]
> DNS 标准不允许在区域的顶点创建 CNAME 记录 (`-Name "@"`)，也不允许记录集包含多个记录。
> 
> 有关详细信息，请参阅 [CNAME 记录](dns-zones-records.md#cname-records)。


```powershell
New-AzureRmDnsRecordSet -Name test-cname -RecordType CNAME -ZoneName contoso.com -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -Cname www.contoso.com) 
```

### <a name="create-an-mx-record-set-with-a-single-record"></a>创建一个包含一条记录的 MX 记录集

在此示例中，使用记录集名称 "@" 在区域顶端（在本例中为“contoso.com”）创建 MX 记录。


```powershell
New-AzureRmDnsRecordSet -Name "@" -RecordType MX -ZoneName contoso.com -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -Exchange mail.contoso.com -Preference 5) 
```

### <a name="create-an-ns-record-set-with-a-single-record"></a>创建一个包含一条记录的 NS 记录集

```powershell
New-AzureRmDnsRecordSet -Name test-ns -RecordType NS -ZoneName contoso.com -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -Nsdname ns1.contoso.com) 
```

### <a name="create-a-ptr-record-set-with-a-single-record"></a>创建一个包含一条记录的 PTR 记录集

在此示例中，“my-arpa-zone.com”表示代表用户 IP 范围的 ARPA 区域。 此区域中的每个 PTR 记录集对应于此 IP 范围内的一个 IP 地址。 记录名称“10”是此 IP 范围内由此记录表示的 IP 地址的最后一个八位字节。

```powershell
New-AzureRmDnsRecordSet -Name 10 -RecordType PTR -ZoneName my-arpa-zone.com -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -Ptrdname myservice.contoso.com) 
```

### <a name="create-an-srv-record-set-with-a-single-record"></a>创建一个包含一条记录的 SRV 记录集

创建 [SRV 记录集](dns-zones-records.md#srv-records)时，请在记录集名称中指定 *\_service* 和 *\_protocol*。 在区域顶点创建 SRV 记录集时，无需在记录集名称中包括 "@"。

```powershell
New-AzureRmDnsRecordSet -Name _sip._tls -RecordType SRV -ZoneName contoso.com -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -Priority 0 -Weight 5 -Port 8080 -Target sip.contoso.com) 
```


### <a name="create-a-txt-record-set-with-a-single-record"></a>创建包含一条记录的 TXT 记录集

以下示例说明如何创建 TXT 记录。 如需详细了解 TXT 记录中支持的最大字符串长度，请参阅 [TXT 记录](dns-zones-records.md#txt-records)。

```powershell
New-AzureRmDnsRecordSet -Name test-txt -RecordType TXT -ZoneName contoso.com -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -Value "This is a TXT record") 
```


## <a name="get-a-record-set"></a>获取记录集

若要检索现有的记录集，请使用 `Get-AzureRmDnsRecordSet`。 此 cmdlet 返回一个本地对象，该对象表示 Azure DNS 中的记录集。

对于 `New-AzureRmDnsRecordSet`，给定的记录集名称必须是*相对* 名称，这意味着它必须排除区域名称。 还需指定记录类型，以及包含记录集的区域。

以下示例说明如何检索记录集。 在此示例中，区域使用 `-ZoneName` 和 `-ResourceGroupName` 参数指定。

```powershell
$rs = Get-AzureRmDnsRecordSet -Name www -RecordType A -ZoneName contoso.com -ResourceGroupName MyResourceGroup
```

此外，也可以使用通过“-Zone”参数传递的区域对象指定区域。 

```powershell
$zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyResourceGroup
$rs = Get-AzureRmDnsRecordSet -Name www -RecordType A -Zone $zone
```

## <a name="list-record-sets"></a>列出记录集

也可以使用 `Get-AzureRmDnsZone` 列出区域中的记录集，省略 `-Name` 和/或 `-RecordType` 参数。

以下示例返回区域中的所有记录集：

```powershell
$recordsets = Get-AzureRmDnsRecordSet -ZoneName contoso.com -ResourceGroupName MyResourceGroup
```

以下示例说明如何在指定记录类型的同时省略记录集名称，以便检索给定类型的所有记录集：

```powershell
$recordsets = Get-AzureRmDnsRecordSet -RecordType A -ZoneName contoso.com -ResourceGroupName MyResourceGroup
```

若要跨记录类型检索具有给定名称的所有记录集，需先检索所有记录集，然后对结果进行筛选：

```powershell
$recordsets = Get-AzureRmDnsRecordSet -ZoneName contoso.com -ResourceGroupName MyResourceGroup | where {$_.Name.Equals("www")}
```

在上述所有示例中，区域都可以通过 `-ZoneName` 和 `-ResourceGroupName` 参数指定（如下所示），或者通过指定区域对象进行指定：

```powershell
$zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyResourceGroup
$recordsets = Get-AzureRmDnsRecordSet -Zone $zone
```

## <a name="add-a-record-to-an-existing-record-set"></a>将记录添加到现有记录集

若要将记录添加到现有记录集，请执行以下三个步骤：

1. 获取现有记录集

    ```powershell
    $rs = Get-AzureRmDnsRecordSet -Name www –ZoneName contoso.com -ResourceGroupName MyResourceGroup -RecordType A
    ```

2. 将新记录添加到本地记录集。 这是一种脱机操作。

    ```powershell
    Add-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address"5.6.7.8
    ```

3. 将更改提交回 Azure DNS 服务。 

    ```powershell
    Set-AzureRmDnsRecordSet -RecordSet $rs
    ```

使用 `Set-AzureRmDnsRecordSet` 可将 Azure DNS 中的现有记录集（及其包含的所有记录）*替换* 为指定的记录集。 使用 [Etag 检查](dns-zones-records.md#etags)可确保不覆盖并发更改。 可以使用可选的 `-Overwrite` 开关取消这些检查。

此操作序列也可*通过管道传递*，即通过管道传递记录集对象，而不是将其作为参数传递：

```powershell
Get-AzureRmDnsRecordSet -Name www –ZoneName contoso.com -ResourceGroupName MyResourceGroup -RecordType A | Add-AzureRmDnsRecordConfig -Ipv4Address 5.6.7.8 | Set-AzureRmDnsRecordSet
```

上述示例说明了如何将“A”记录添加到现有的类型“A”记录集。 可以使用类似操作顺序向其他类型的记录集添加记录，将 `Add-AzureRmDnsRecordConfig` 的 `-Ipv4Address` 参数替换为特定于每个记录类型的其他参数。 每个记录类型的参数与 `New-AzureRmDnsRecordConfig` cmdlet 的参数相同，如上面的[其他记录类型示例](#additional-record-type-examples)所示。

类型为“CNAME”或“SOA”的记录集不能包含多个记录。 此约束源自 DNS 标准， 不是 Azure DNS 的限制。

## <a name="remove-a-record-from-an-existing-record-set"></a>从现有记录集中删除记录

从记录集删除记录的过程类似于向现有记录集添加记录的过程：

1. 获取现有记录集

    ```powershell
    $rs = Get-AzureRmDnsRecordSet -Name www –ZoneName contoso.com -ResourceGroupName MyResourceGroup -RecordType A
    ```

2. 从本地记录集对象删除记录。 这是一种脱机操作。 要删除的记录必须与所有参数中的现有记录完全匹配。

    ```powershell
    Remove-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address 5.6.7.8
    ```

3. 将更改提交回 Azure DNS 服务。 对于并发更改，使用可选的 `-Overwrite` 开关取消 [Etag 检查](dns-zones-records.md#etags)。

    ```powershell
    Set-AzureRmDnsRecordSet -RecordSet $rs
    ```

按以上顺序从某个记录集中删除最后一条记录不会删除该记录集，而是留下一个空的记录集。 若要彻底删除记录集，请参阅[删除记录集](#delete-a-record-set)。

与添加记录到记录集类似，也可通过管道传递删除记录集的操作顺序：

```powershell
Get-AzureRmDnsRecordSet -Name www –ZoneName contoso.com -ResourceGroupName MyResourceGroup -RecordType A | Remove-AzureRmDnsRecordConfig -Ipv4Address 5.6.7.8 | Set-AzureRmDnsRecordSet
```

将适当的特定于类型的参数传递给 `Remove-AzureRmDnsRecordSet` 即可支持不同的记录类型。 每个记录类型的参数与 `New-AzureRmDnsRecordConfig` cmdlet 的参数相同，如上面的[其他记录类型示例](#additional-record-type-examples)所示。


## <a name="modify-an-existing-record-set"></a>修改现有记录集

修改现有记录集的步骤类似于通过记录集添加或删除记录时所采取的步骤：

1. 使用 `Get-AzureRmDnsRecordSet` 检索现有记录集。
2. 通过以下方式修改本地记录集对象：
    * 添加或删除记录
    * 更改现有记录的参数
    * 更改记录集元数据和生存时间 (TTL)
3. 使用 `Set-AzureRmDnsRecordSet` cmdlet 提交更改。 这样会将 Azure DNS 中的现有记录集*替换* 为指定的记录集。

使用 `Set-AzureRmDnsRecordSet` 时，可通过 [Etag 检查](dns-zones-records.md#etags)确保不覆盖并发更改。 可以使用可选的 `-Overwrite` 开关取消这些检查。

### <a name="to-update-a-record-in-an-existing-record-set"></a>更新现有记录集中的记录

在此示例中，我们更改现有“A”记录的 IP 地址：

```powershell
$rs = Get-AzureRmDnsRecordSet -name www -RecordType A -ZoneName contoso.com -ResourceGroupName MyResourceGroup
$rs.Records[0].Ipv4Address = 9.8.7.6
Set-AzureRmDnsRecordSet -RecordSet $rs
```

### <a name="to-modify-an-soa-record"></a>修改 SOA 记录

不能在区域顶点从自动创建的 SOA 记录集（`-Name "@"`，包括引号）添加或删除记录。 但是，可以修改 SOA 记录和记录集 TTL 中的任何参数（“Host”除外）。

下面的示例演示如何更改 SOA 记录的 *Email* 属性：

```powershell
$rs = Get-AzureRmDnsRecordSet -Name "@" -RecordType SOA -ZoneName contoso.com -ResourceGroupName MyResourceGroup
$rs.Records[0].Email = "admin.contoso.com"
Set-AzureRmDnsRecordSet -RecordSet $rs
```

### <a name="to-modify-ns-records-at-the-zone-apex"></a>修改区域顶点处的 NS 记录

不能在区域顶点的自动创建的 NS 记录集（`-Name "@"`，包括引号）中添加、删除或修改记录。 允许的更改仅限修改记录集 TTL 和元数据。

下面的示例演示如何更改 NS 记录集的 TTL 属性：

```powershell
$rs = Get-AzureRmDnsRecordSet -Name "@" -RecordType NS -ZoneName contoso.com -ResourceGroupName MyResourceGroup
$rs.Ttl = 300
Set-AzureRmDnsRecordSet -RecordSet $rs
```

### <a name="to-modify-record-set-metadata"></a>修改记录集元数据

可使用[记录集元数据](dns-zones-records.md#tags-and-metadata)，以键-值对的形式将应用程序特定的数据与每个记录集相关联。

以下示例说明如何修改现有记录集的元数据：

```powershell
# Get the record set
$rs = Get-AzureRmDnsRecordSet -Name www -RecordType A -ZoneName contoso.com -ResourceGroupName MyResourceGroup

# Add "dept=finance" name-value pair
$rs.Metadata.Add("dept", "finance") 

# Remove metadata item named "environment"
$rs.Metadata.Remove("environment")  

# Commit changes
Set-AzureRmDnsRecordSet -RecordSet $rs
```


## <a name="delete-a-record-set"></a>删除记录集

可以通过使用 `Remove-AzureRmDnsRecordSet` cmdlet 删除记录集。 删除记录集也会删除记录集内的所有记录。

> [!NOTE]
> 无法删除区域顶点的 SOA 和 NS 记录集 (`-Name "@"`)。  这些记录集是在创建区域时自动创建的，会在删除区域时自动删除。

以下示例说明如何删除记录集。 在此示例中，记录集名称、记录集类型、区域名称和资源组都进行了显式指定。

```powershell
Remove-AzureRmDnsRecordSet -Name www -RecordType A -ZoneName contoso.com -ResourceGroupName MyResourceGroup
```

此外，也可通过名称和类型指定记录集，通过对象指定区域：

```powershell
$zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyResourceGroup
Remove-AzureRmDnsRecordSet -Name www -RecordType A -Zone $zone
```

第三个选项是通过记录集对象指定记录集本身：

```powershell
$rs = Get-AzureRmDnsRecordSet -Name www -RecordType A -ZoneName contoso.com -ResourceGroupName MyResourceGroup
Remove-AzureRmDnsRecordSet -RecordSet $rs
```

使用记录集对象指定要删除的记录集时，可以通过 [Etag 检查](dns-zones-records.md#etags)确保不删除并发更改。 可以使用可选的 `-Overwrite` 开关取消这些检查。

记录集对象也可以通过管道输送，而不是作为参数传递：

```powershell
Get-AzureRmDnsRecordSet -Name www -RecordType A -ZoneName contoso.com -ResourceGroupName MyResourceGroup | Remove-AzureRmDnsRecordSet
```

## <a name="confirmation-prompts"></a>确认提示

`New-AzureRmDnsRecordSet`、`Set-AzureRmDnsRecordSet` 和 `Remove-AzureRmDnsRecordSet` cmdlet 都支持确认提示。

如果 `$ConfirmPreference` PowerShell 首选项变量的值为 `Medium` 或更低，则每个 cmdlet 都会提示用户进行确认。 由于 `$ConfirmPreference` 的默认值为 `High`，因此在使用默认的 PowerShell 设置时不会发出这些提示。

可以使用 `-Confirm` 参数重写当前的 `$ConfirmPreference` 设置。 如果指定 `-Confirm` 或 `-Confirm:$True`，cmdlet 会在运行之前提示用户进行确认。 如果指定 `-Confirm:$False`，cmdlet 不会提示用户进行确认。 

有关 `-Confirm` 和 `$ConfirmPreference` 的详细信息，请参阅 [About Preference Variables](https://msdn.microsoft.com/powershell/reference/5.1/Microsoft.PowerShell.Core/about/about_Preference_Variables)（关于首选项变量）。

## <a name="next-steps"></a>后续步骤

详细了解 [Azure DNS 中的区域和记录](dns-zones-records.md)。
<br>
了解如何在使用 Azure DNS 时[保护区域和记录](dns-protect-zones-recordsets.md)。
<br>
查看 [Azure DNS PowerShell 参考文档](/powershell/resourcemanager/azurerm.dns/v2.3.0/azurerm.dns)。



<!--HONumber=Jan17_HO2-->


