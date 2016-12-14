---
title: "使用 PowerShell 为 DNS 区域创建记录集和记录 | Microsoft Docs"
description: "如何为 Azure DNS 创建主机记录。使用 PowerShell 设置记录集和记录"
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.assetid: a8068c5a-f248-4e97-be97-8bd0d79eeffd
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/16/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 02d720a04fdc0fa302c2cb29b0af35ee92c14b3b
ms.openlocfilehash: a6b72b96c241e3d459abad0d3986f2d757cf1752

---

# <a name="create-dns-record-sets-and-records-by-using-powershell"></a>使用 PowerShell 创建 DNS 记录集和记录

> [!div class="op_single_selector"]
> * [Azure 门户](dns-getstarted-create-recordset-portal.md)
> * [PowerShell](dns-getstarted-create-recordset.md)
> * [Azure CLI](dns-getstarted-create-recordset-cli.md)

本文将指导你完成使用 Windows PowerShell 创建记录和记录集的过程。 在创建 DNS 区域后，为域添加 DNS 记录。 若要执行此操作，首先需要了解 DNS 记录和记录集。

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

## <a name="verify-that-you-have-the-latest-version-of-powershell"></a>确认你有新版本的 PowerShell

确认已安装最新版本的 Azure Resource Manager PowerShell cmdlet。 有关安装 PowerShell cmdlet 的详细信息，请参阅 [如何安装和配置 Azure PowerShell](../powershell-install-configure.md) 。

## <a name="create-a-record-set-and-record"></a>创建记录集和记录

本部分描述如何创建记录集和记录。

### <a name="1-connect-to-your-subscription"></a>1.连接到订阅

打开 PowerShell 控制台并连接到你的帐户。 使用下面的示例来帮助你连接：

```powershell
Login-AzureRmAccount
```

检查该帐户的订阅。

```powershell
Get-AzureRmSubscription
```

指定要使用的订阅。

```powershell
Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
```

有关使用 PowerShell 的详细信息，请参阅[将 Windows PowerShell 与 Resource Manager 结合使用](../powershell-azure-resource-manager.md)。

### <a name="2-create-a-record-set"></a>2.创建记录集

可以使用 `New-AzureRmDnsRecordSet` cmdlet 创建记录集。 当创建记录集时，需要指定记录集名称、区域、生存时间 (TTL) 和记录类型。

若要在区域（在此例中为“contoso.com”）顶点创建记录集，请使用记录名称 "@",（包括引号）。 这是常见的 DNS 约定。

下面的示例在 DNS 区域“contoso.com”中创建具有相对名称“www”的记录集。 记录集的完全限定名称为“www.contoso.com”。 记录类型为“A”，TTL 为 60 秒。 完成此步骤后，你将有一个分配给变量 *$rs* 的空“www”记录集。

```powershell
$rs = New-AzureRmDnsRecordSet -Name "www" -RecordType "A" -ZoneName "contoso.com" -ResourceGroupName "MyAzureResourceGroup" -Ttl 60
```

#### <a name="if-a-record-set-already-exists"></a>如果记录集已存在

如果记录集已存在，除非使用 *-Overwrite* 开关，否则命令将失败。 *-Overwrite* 选项触发确认提示，可通过使用 *-Force* 开关取消此提示。

```powershell
$rs = New-AzureRmDnsRecordSet -Name www -RecordType A -Ttl 300 -ZoneName contoso.com -ResouceGroupName MyAzureResouceGroup [-Tag $tags] [-Overwrite] [-Force]
```

在此示例中，通过使用区域名称和资源组名称指定区域。 或者，你可以指定 `Get-AzureRmDnsZone` 或 `New-AzureRmDnsZone` 返回的区域对象。

```powershell
$zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
$rs = New-AzureRmDnsRecordSet -Name www -RecordType A -Ttl 300 -Zone $zone [-Tag $tags] [-Overwrite] [-Force]
```

`New-AzureRmDnsRecordSet` 返回一个本地对象，该对象表示在 Azure DNS 中创建的记录集。

### <a name="3-add-a-record"></a>3.添加记录

若要使用新创建的“www”记录集，需要为其添加记录。 可以通过使用下面的示例将 IPv4 *A* 记录添加到“www”记录集。 此示例依赖你在上一步中设置的变量 *$rs*。

使用 `Add-AzureRmDnsRecordConfig` 将记录添加到记录集是脱机操作。 只有本地变量 *$rs* 才会进行更新。

```powershell
Add-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address 134.170.185.46
Add-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address 134.170.188.221
```

### <a name="4-commit-the-changes"></a>4.提交更改

确认对记录集的更改。 使用 `Set-AzureRmDnsRecordSet` 将更改上载到记录集，再上载到 Azure DNS。

```powershell
Set-AzureRmDnsRecordSet -RecordSet $rs
```

### <a name="5-retrieve-the-record-set"></a>5.检索记录集

如下面的示例所示，可以使用 `Get-AzureRmDnsRecordSet` 从 Azure DNS 检索记录集。

    Get-AzureRmDnsRecordSet -Name www -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup


    Name              : www
    ZoneName          : contoso.com
    ResourceGroupName : MyAzureResourceGroup
    Ttl               : 3600
    Etag              : 68e78da2-4d74-413e-8c3d-331ca48246d9
    RecordType        : A
    Records           : {134.170.185.46, 134.170.188.221}
    Tags              : {}


可以使用 nslookup 工具或其他 DNS 工具查询新的记录集。

如果你还未将域委派到 Azure DNS 名称服务器，则需要明确指定区域的名称、服务器和地址。

    nslookup www.contoso.com ns1-01.azure-dns.com

    Server: ns1-01.azure-dns.com
    Address:  208.76.47.1

    Name:    www.contoso.com
    Addresses:  134.170.185.46
                134.170.188.221

## <a name="create-a-record-set-of-each-type-with-a-single-record"></a>创建每种类型包含一条记录的记录集

以下示例演示如何创建每个记录类型的记录集。 每个记录集只包含一个单一记录。

[!INCLUDE [dns-add-record-ps-include](../../includes/dns-add-record-ps-include.md)]

## <a name="next-steps"></a>后续步骤

[如何使用 PowerShell 管理 DNS 区域](dns-operations-dnszones.md)

[使用 PowerShell 管理 DNS 记录和记录集](dns-operations-recordsets.md)

[使用 .NET SDK 自动执行 Azure 操作](dns-sdk.md)



<!--HONumber=Nov16_HO3-->


