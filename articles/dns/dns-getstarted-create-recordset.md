---
title: "使用 PowerShell 为 DNS 区域创建记录集和记录 | Microsoft Docs"
description: "如何为 Azure DNS 创建主机记录。 使用 PowerShell 设置记录集和记录"
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
ms.date: 12/05/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: f4c17d03ff637659a7bc7cde378878d8a4827b80
ms.openlocfilehash: 175e8620828a2b0a0aff6de0b1a39860ea59514b

---

# <a name="create-dns-record-sets-and-records-by-using-powershell"></a>使用 PowerShell 创建 DNS 记录集和记录

> [!div class="op_single_selector"]
> * [Azure 门户](dns-getstarted-create-recordset-portal.md)
> * [PowerShell](dns-getstarted-create-recordset.md)
> * [Azure CLI](dns-getstarted-create-recordset-cli.md)

本文介绍使用 Azure PowerShell 创建记录和记录集这一过程。

## <a name="introduction"></a>介绍

在 Azure DNS 中创建 DNS 记录之前，首先需了解 Azure DNS 如何将 DNS 记录组织到 DNS 记录集中。

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

有关 Azure DNS 中的 DNS 记录的详细信息，请参阅 [DNS 区域和记录](dns-zones-records.md)。

## <a name="create-a-record-set-and-record"></a>创建记录集和记录

本部分介绍如何在 Azure DNS 中创建 DNS 记录。 这些示例假设用户已经[安装并登录 Azure PowerShell 以及创建了 DNS 区域](dns-getstarted-create-dnszone.md)。

此页上所有示例都使用“A”DNS 记录类型。 有关其他记录类型以及如何管理 DNS 记录和记录集的详细信息，请参阅[使用 PowerShell 管理 DNS 记录和记录集](dns-operations-recordsets.md)。

如果新记录与现有记录具有相同的名称和类型，则需[将其添加到现有记录集](#add-a-record-to-an-existing-record-set)。 如果新记录与所有现有记录的名称和类型都不同，则需[创建新的记录集](#create-records-in-a-new-record-set)。 

### <a name="create-records-in-a-new-record-set"></a>在新的记录集中创建记录

可以使用 `New-AzureRmDnsRecordSet` cmdlet 创建记录集。 创建记录集时，需指定记录集名称、区域、生存时间 (TTL)、记录类型，以及要创建的记录。

若要在区域（在此例中为“contoso.com”）顶点创建记录集，请使用记录名称 "@",（包括引号）。 这是常见的 DNS 约定。

以下示例在 DNS 区域“contoso.com”中创建具有相对名称“www”的新记录集。 记录集的完全限定名称为“www.contoso.com”。 记录类型为“A”，TTL 为 3600 秒。 记录集包含单个记录，IP 地址为“1.2.3.4”

```powershell
New-AzureRmDnsRecordSet -Name "www" -RecordType "A" -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -IPv4Address "1.2.3.4")
```

如需创建包含多个记录的新记录集，则需先创建本地数组，其中包含要添加的记录。  该数组将传递到 `New-AzureRmDnsRecordSet`，如下所示：

```powershell
$aRecords = @()
$aRecords += New-AzureRmDnsRecordConfig -IPv4Address "1.2.3.4"
$aRecords += New-AzureRmDnsRecordConfig -IPv4Address "2.3.4.5"
New-AzureRmDnsRecordSet -Name "www" –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -RecordType A -DnsRecords $aRecords
```

### <a name="add-a-record-to-an-existing-record-set"></a>将记录添加到现有记录集

若要将记录添加到现有记录集，请执行以下三个步骤：

1. 获取现有记录集

    ```powershell
    $rs = Get-AzureRmDnsRecordSet -Name "www" –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A
    ```

2. 将新记录添加到本地记录集。 这是一种脱机操作。

    ```powershell
    Add-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address "5.6.7.8"
    ```

3. 将更改提交回 Azure DNS 服务 

    ```powershell
    Set-AzureRmDnsRecordSet -RecordSet $rs
    ```

### <a name="verify-name-resolution"></a>验证名称解析

可使用 DNS 工具（如 nslookup、dig 或 [Resolve-DnsName PowerShell cmdlet](https://technet.microsoft.com/library/jj590781.aspx)）测试 DNS记录是否在 Azure DNS 服务器中。

如果尚未委托域在 Azure DNS 中使用新的区域，则需要[直接向区域的其中一个名称服务器提出 DNS 查询](dns-getstarted-create-dnszone.md#test-name-servers)。 确保将记录区域的正确值替换到以下示例中：

    nslookup
    > set type=A
    > server ns1-01.azure-dns.com
    > www.contoso.com

    Server:  ns1-01.azure-dns.com
    Address:  40.90.4.1

    Name:    www.contoso.com
    Address:  1.2.3.4


## <a name="next-steps"></a>后续步骤

了解如何[将域名委托到 Azure DNS 名称服务器](dns-domain-delegation.md)

了解如何[使用 PowerShell 管理 DNS 区域](dns-operations-dnszones.md)。

了解如何[使用 PowerShell 管理 DNS 记录和记录集](dns-operations-recordsets.md)。





<!--HONumber=Dec16_HO3-->


