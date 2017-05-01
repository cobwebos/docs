---
title: "通过 PowerShell 开始使用 Azure DNS | Microsoft 文档"
description: "了解如何在 Azure DNS 中创建 DNS 区域和记录。 这是有关使用 PowerShell 创建和管理你的第一个 DNS 区域和记录的分步指南。"
services: dns
documentationcenter: na
author: jtuliani
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: fb0aa0a6-d096-4d6a-b2f6-eda1c64f6182
ms.service: dns
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/10/2017
ms.author: jonatul
translationtype: Human Translation
ms.sourcegitcommit: abdbb9a43f6f01303844677d900d11d984150df0
ms.openlocfilehash: 48f7ba325f61b4a91c0208b4c99058da801bee19
ms.lasthandoff: 04/21/2017

---

# <a name="get-started-with-azure-dns-using-powershell"></a>通过 PowerShell 开始使用 Azure DNS

> [!div class="op_single_selector"]
> * [Azure 门户](dns-getstarted-portal.md)
> * [PowerShell](dns-getstarted-powershell.md)
> * [Azure CLI 1.0](dns-getstarted-cli-nodejs.md)
> * [Azure CLI 2.0](dns-getstarted-cli.md)

本文将逐步引导你完成使用 PowerShell 创建你的第一个 DNS 区域和记录的步骤。 也可以使用 Azure 门户或跨平台 Azure CLI 执行这些步骤。

DNS 区域用来托管某个特定域的 DNS 记录。 若要开始在 Azure DNS 中托管域，需要为该域名创建 DNS 区域。 随后将在此 DNS 区域内为每个 DNS 记录创建域。 最后，若要将 DNS 区域发布到 Internet，需要为域配置名称服务器。 以下描述了上述每一个步骤。

这些说明假定你已安装并登录到 Azure PowerShell。 若需帮助，请参阅[如何使用 PowerShell 管理 DNS 区域](dns-operations-dnszones.md)。

## <a name="create-the-resource-group"></a>创建资源组

在创建 DNS 区域之前，创建了包含 DNS 区域的资源组。 以下显示该命令。

```powershell
New-AzureRMResourceGroup -name MyResourceGroup -location "westus"
```

## <a name="create-a-dns-zone"></a>创建 DNS 区域

通过使用 `New-AzureRmDnsZone` cmdlet 创建 DNS 区域。 以下示例在名为 *MyResourceGroup* 的资源组中创建名为 *contoso.com* 的 DNS 区域。 使用该示例创建 DNS 区域，将相应的值替换为你自己的值。

```powershell
New-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyResourceGroup
```

## <a name="create-a-dns-record"></a>创建 DNS 记录

可以使用 `New-AzureRmDnsRecordSet` cmdlet 创建记录集。 下面的示例在资源组“MyResourceGroup”中在 DNS 区域“contoso.com”中创建相对名称为“www”的一个记录集。 记录集的完全限定名称为“www.contoso.com”。 记录类型为“A”，IP 地址为“1.2.3.4”，TTL 为 3600 秒。

```powershell
New-AzureRmDnsRecordSet -Name www -RecordType A -ZoneName contoso.com -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -IPv4Address "1.2.3.4")
```

对于其他记录类型，对于具有一条以上记录的记录集，若要修改现有记录，请参阅[使用 Azure PowerShell 管理 DNS 记录和记录集](dns-operations-recordsets.md)。 


## <a name="view-records"></a>查看记录

若要列出区域中的 DNS 记录，请使用：

```powershell
Get-AzureRmDnsRecordSet -ZoneName contoso.com -ResourceGroupName MyResourceGroup
```


## <a name="update-name-servers"></a>更新名称服务器

正确设置 DNS 区域和记录后，需要将域名配置为使用 Azure DNS 名称服务器。 这使得 Internet 上的其他用户可以发现你的 DNS 记录。

你的区域的名称服务器是通过 `Get-AzureRmDnsZone` cmdlet 指定的：

```powershell
Get-AzureRmDnsZone -ZoneName contoso.com -ResourceGroupName MyResourceGroup

Name                  : contoso.com
ResourceGroupName     : myresourcegroup
Etag                  : 00000003-0000-0000-b40d-0996b97ed101
Tags                  : {}
NameServers           : {ns1-01.azure-dns.com., ns2-01.azure-dns.net., ns3-01.azure-dns.org., ns4-01.azure-dns.info.}
NumberOfRecordSets    : 3
MaxNumberOfRecordSets : 5000
```

这些名称服务器应当配置有域名注册机构（你向其购买域名的机构）。 域名注册机构将提供选项来为域设置名称服务器。 有关详细信息，请参阅[将域委派给 Azure DNS](dns-domain-delegation.md)。

## <a name="delete-all-resources"></a>删除所有资源

若要删除在本文中创建的所有资源，请执行以下步骤：

```powershell
Remove-AzureRMResourceGroup -Name MyResourceGroup
```

## <a name="next-steps"></a>后续步骤

若要了解 Azure DNS 的详细信息，请参阅 [Azure DNS 概述](dns-overview.md)。

若要了解有关管理 Azure DNS 中的 DNS 区域的详细信息，请参阅[使用 PowerShell 管理 Azure DNS 中的 DNS 区域](dns-operations-dnszones.md)。

若要了解有关管理 Azure DNS 中的 DNS 记录的详细信息，请参阅[使用 PowerShell 管理 Azure DNS 中的 DNS 记录和记录集](dns-operations-recordsets.md)。


