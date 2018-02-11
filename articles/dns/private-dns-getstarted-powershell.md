---
title: "Azure DNS 专用区域入门（使用 PowerShell）| Microsoft Docs"
description: "了解如何在 Azure DNS 中创建专用 DNS 区域和记录。 这是有关使用 PowerShell 创建和管理第一个专用 DNS 区域和记录的分步指南。"
services: dns
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: dns
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/20/2017
ms.author: kumud
ms.openlocfilehash: 4613e152336eda7ce7cdc4c44b0c6b5e96abac10
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/03/2018
---
# <a name="get-started-with-azure-dns-private-zones-using-powershell"></a>Azure DNS 专用区域入门（使用 PowerShell）

本文将逐步引导你完成使用 PowerShell 创建第一个专用 DNS 区域和记录的步骤。

[!INCLUDE [private-dns-preview-notice](../../includes/private-dns-preview-notice.md)]

DNS 区域用来托管某个特定域的 DNS 记录。 若要开始在 Azure DNS 中托管域，需要为该域名创建 DNS 区域。 随后会在此 DNS 区域内为每个 DNS 记录创建域。 若要向虚拟网络发布专用 DNS 区域，请指定一个列表，其中包含允许在区域中解析记录的虚拟网络。  这些虚拟网络称为“解析网络”。  也可指定一组虚拟网络，让 Azure DNS 在创建 VM、更改 IP 或销毁 VM 时为其保留主机名记录。  这些虚拟网络称为“注册网络”。

由于该功能当前为托管预览版，因此当你向 [AzureDNS-PrivateZone@microsoft.com](mailto:AzureDNS-PrivateZone@microsoft.com) 发送电子邮件时，会向你提供预览版 PowerShell 模块。

## <a name="get-the-preview-powershell-module"></a>获取预览版 PowerShell 模块

若要使用 PowerShell 创建专用 DNS 区域，请向 [AzureDNS-PrivateZone@microsoft.com](mailto:AzureDNS-PrivateZone@microsoft.com) 发送电子邮件，以便获取预览版 PowerShell 模块。

## <a name="create-the-resource-group"></a>创建资源组

在创建 DNS 区域之前，创建了包含 DNS 区域的资源组。 以下显示该命令。

```powershell
New-AzureRMResourceGroup -name MyResourceGroup -location "westus"
```

## <a name="create-a-dns-zone"></a>创建 DNS 区域

通过使用 `New-AzureRmDnsZone` cmdlet 创建 DNS 区域。 以下示例在名为“MyResourceGroup”的资源组中创建名为“contoso.local”的 DNS 区域，并将 DNS 区域提供给名为“MyAzureVnet”的虚拟网络。 使用该示例创建 DNS 区域，将相应的值替换成自己的值。

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

## <a name="delete-all-resources"></a>删除所有资源

若要删除在本文中创建的所有资源，请执行以下步骤：

```powershell
Remove-AzureRMResourceGroup -Name MyResourceGroup
```

## <a name="next-steps"></a>后续步骤

若要详细了解专用 DNS 区域，请参阅[将 Azure DNS 用于专用域](private-dns-overview.md)。

若要了解有关管理 Azure DNS 中的 DNS 记录的详细信息，请参阅[使用 PowerShell 管理 Azure DNS 中的 DNS 记录和记录集](dns-operations-recordsets.md)。

