---
title: 委派子域-Azure PowerShell-Azure DNS
description: 使用此学习路径，开始使用 Azure PowerShell 委托 Azure DNS 子域。
services: dns
author: asudbring
ms.service: dns
ms.topic: article
ms.date: 2/7/2019
ms.author: allensu
ms.openlocfilehash: b6023adb65469dedeeba664a51917306e634146f
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74212503"
---
# <a name="delegate-an-azure-dns-subdomain-using-azure-powershell"></a>使用 Azure PowerShell 委托 Azure DNS 子域

可以使用 Azure PowerShell 委托 DNS 子域。 例如，如果拥有 contoso.com 域，可将名为 *engineering* 的子域委托给另一个可以独立于 contoso.com 区域进行管理的单独区域。

如果需要，可以使用 [Azure 门户](delegate-subdomain.md)来委托子域。

> [!NOTE]
> 本文通篇都使用 contoso.com 作为示例。 请将 contoso.com 替换为你自己的域名。

如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>先决条件

若要委托 Azure DNS 子域，必须先将公共域委托给 Azure DNS。 有关如何为委托配置名称服务器的说明，请参阅[将域委托给 Azure DNS](./dns-delegate-domain-azure-dns.md)。 将域委托给 Azure DNS 区域后，可以委托子域。

## <a name="create-a-zone-for-your-subdomain"></a>为子域创建区域

首先，为 **engineering** 域创建区域。

`New-AzDnsZone -ResourceGroupName <resource group name> -Name engineering.contoso.com`

## <a name="note-the-name-servers"></a>记下名称服务器

接下来，记下 engineering 子域的四个名称服务器。

`Get-AzDnsRecordSet -ZoneName engineering.contoso.com -ResourceGroupName <resource group name> -RecordType NS`

## <a name="create-a-test-record"></a>创建一条测试记录

在 engineering 区域中创建一条 **A** 记录以用于测试。

   `New-AzDnsRecordSet -ZoneName engineering.contoso.com -ResourceGroupName <resource group name> -Name www -RecordType A -ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address 10.10.10.10)`。

## <a name="create-an-ns-record"></a>创建 NS 记录

接下来，为 contoso.com 区域中的 **engineering** 区域创建一条名称服务器 (NS) 记录。

```azurepowershell
$Records = @()
$Records += New-AzDnsRecordConfig -Nsdname <name server 1 noted previously>
$Records += New-AzDnsRecordConfig -Nsdname <name server 2 noted previously>
$Records += New-AzDnsRecordConfig -Nsdname <name server 3 noted previously>
$Records += New-AzDnsRecordConfig -Nsdname <name server 4 noted previously>
$RecordSet = New-AzDnsRecordSet -Name engineering -RecordType NS -ResourceGroupName <resource group name> -TTL 3600 -ZoneName contoso.com -DnsRecords $Records
```

## <a name="test-the-delegation"></a>测试委托

使用 nslookup 测试委托。

1. 打开 PowerShell 窗口。
2. 在命令提示符下，键入 `nslookup www.engineering.contoso.com.`
3. 应会收到一条非权威回复，其中显示了地址 **10.10.10.10**。

## <a name="next-steps"></a>后续步骤

了解如何[为 Azure 中托管的服务配置反向 DNS](dns-reverse-dns-for-azure-services.md)。