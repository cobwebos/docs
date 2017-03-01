---
title: "Azure DNS 入门 | Microsoft Docs"
description: "了解如何在 Azure DNS 中创建 DNS 区域。 这是有关使用 Azure PowerShell 创建和管理第一个 DNS 区域的分步指南。"
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.assetid: d78583b7-e669-435c-819b-7605cf791b0e
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/05/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 36fa9cd757b27347c08f80657bab8a06789a3c2f
ms.openlocfilehash: 798717eca5180e445b14351bbfc694ed206ee9dc
ms.lasthandoff: 02/27/2017

---

# <a name="create-a-dns-zone-using-powershell"></a>使用 PowerShell 创建 DNS 区域

> [!div class="op_single_selector"]
> * [Azure 门户](dns-getstarted-create-dnszone-portal.md)
> * [PowerShell](dns-getstarted-create-dnszone.md)
> * [Azure CLI 1.0](dns-getstarted-create-dnszone-cli-nodejs.md)
> * [Azure CLI 2.0](dns-getstarted-create-dnszone-cli.md)

本文将逐步引导完成使用 Azure PowerShell 创建 DNS 区域的步骤。 也可使用跨平台的 [Azure CLI](dns-getstarted-create-dnszone-cli.md) 或 [Azure 门户](dns-getstarted-create-dnszone-portal.md)创建 DNS 记录。

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

[!INCLUDE [dns-powershell-setup](../../includes/dns-powershell-setup-include.md)]


## <a name="create-a-dns-zone"></a>创建 DNS 区域

通过使用 `New-AzureRmDnsZone` cmdlet 创建 DNS 区域。 以下示例在名为 *MyResourceGroup* 的资源组中创建名为 *contoso.com* 的 DNS 区域。 使用该示例创建 DNS 区域，将相应的值替换为你自己的值。

```powershell
New-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
```

## <a name="verify-your-dns-zone"></a>验证 DNS 区域

### <a name="view-records"></a>查看记录

创建 DNS 区域还会创建以下 DNS 记录：

* “*授权起始点*”(SOA) 记录。 此记录位于每个 DNS 区域的根目录。
* 授权名称服务器 (NS) 记录。 这些记录显示托管该区域的名称服务器。 Azure DNS 使用名称服务器池，因此可能会将不同的名称服务器分配给 Azure DNS 中的不同区域。 有关详细信息，请参阅[将域委托给 Azure DNS](dns-domain-delegation.md)。

要查看这些记录，请使用 `Get-AzureRmDnsRecordSet`：

```powershell
Get-AzureRmDnsRecordSet -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup

Name              : @
ZoneName          : contoso.com
ResourceGroupName : MyAzureResourceGroup
Ttl               : 172800
Etag              : f573237b-088c-424a-b53c-08567d87d049
RecordType        : NS
Records           : {ns1-01.azure-dns.com., ns2-01.azure-dns.net., ns3-01.azure-dns.org., ns4-01.azure-dns.info.}
Metadata          : 

Name              : @
ZoneName          : contoso.com
ResourceGroupName : MyAzureResourceGroup
Ttl               : 3600
Etag              : bf88a27d-0eec-4847-ad42-f0c83b9a2c32
RecordType        : SOA
Records           : {[ns1-01.azure-dns.com.,azuredns-hostmaster.microsoft.com,3600,300,2419200,300]}
Metadata          : 
```

> [!NOTE]
> DNS 区域根目录（或 *顶点*）的记录集使用 **@** 作为记录集名称。

### <a name="test-name-servers"></a>测试名称服务器

可使用 DNS 工具（如 nslookup、dig 或 [Resolve-DnsName PowerShell cmdlet](https://technet.microsoft.com/library/jj590781.aspx)）测试 DNS 区域是否在 Azure DNS 名称服务器中。

如果尚未委托域而在 Azure DNS 中使用新的区域，则需要直接向区域的其中一个名称服务器提出 DNS 查询。 区域的名称服务器在 NS 记录中给出，如上面的 `Get-AzureRmDnsRecordSet` 所列。 确保将区域的正确值替换到以下示例中：

```
nslookup
> set type=SOA
> server ns1-01.azure-dns.com
> contoso.com

Server: ns1-01.azure-dns.com
Address:  40.90.4.1

contoso.com
        primary name server = ns1-01.azure-dns.com
        responsible mail addr = azuredns-hostmaster.microsoft.com
        serial  = 1
        refresh = 3600 (1 hour)
        retry   = 300 (5 mins)
        expire  = 2419200 (28 days)
        default TTL = 300 (5 mins)
```

## <a name="next-steps"></a>后续步骤

创建 DNS 区域后，[创建记录集和记录](dns-getstarted-create-recordset.md)为 Internet 域创建 DNS 记录。

