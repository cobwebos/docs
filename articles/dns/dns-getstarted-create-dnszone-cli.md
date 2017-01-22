---
title: "使用 CLI 创建 DNS 区域 | Microsoft 文档"
description: "了解如何在 Azure DNS 中创建 DNS 区域。 这是有关使用 Azure CLI 创建和管理第一个 DNS 区域的分步指南。"
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.assetid: 1514426a-133c-491a-aa27-ee0962cea9dc
ms.service: dns
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/21/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: f156e4d4c5ffddb7e93ebf21baa75864e0e260e9
ms.openlocfilehash: d00792a4bb19e194dbbcee8b9c11e4a744891388

---

# <a name="create-an-azure-dns-zone-using-cli"></a>使用 CLI 创建 Azure DNS 区域

> [!div class="op_single_selector"]
> * [Azure 门户](dns-getstarted-create-dnszone-portal.md)
> * [PowerShell](dns-getstarted-create-dnszone.md)
> * [Azure CLI](dns-getstarted-create-dnszone-cli.md)

本文将介绍使用跨平台的 Azure CLI（在 Windows、Mac 和 Linux 中可用）创建 DNS 区域的步骤。 也可以使用 [Azure PowerShell](dns-getstarted-create-dnszone.md) 或 [Azure 门户](dns-getstarted-create-dnszone-portal.md)创建 DNS 区域。

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

[!INCLUDE [dns-cli-setup](../../includes/dns-cli-setup-include.md)]


## <a name="create-a-dns-zone"></a>创建 DNS 区域

使用 `azure network dns zone create` 命令创建 DNS 区域。 若要查看此命令的帮助，请键入 `azure network dns zone create -h`。

以下示例在名为 *MyResourceGroup* 的资源组中创建名为 *contoso.com* 的 DNS 区域。 使用该示例创建 DNS 区域，将相应的值替换为你自己的值。

```azurecli
azure network dns zone create MyResourceGroup contoso.com
```

## <a name="verify-your-dns-zone"></a>验证 DNS 区域

### <a name="view-records"></a>查看记录

创建 DNS 区域还会创建以下 DNS 记录：

* “*授权起始点*”(SOA) 记录。 此记录位于每个 DNS 区域的根目录。
* 授权名称服务器 (NS) 记录。 这些记录显示托管该区域的名称服务器。 Azure DNS 使用名称服务器池，因此可能会将不同的名称服务器分配给 Azure DNS 中的不同区域。 有关详细信息，请参阅[将域委托给 Azure DNS](dns-domain-delegation.md)。

要查看这些记录，请使用 `azure network dns-record-set list`：

```azurecli
azure network dns record-set list MyResourceGroup contoso.com

info:    Executing command network dns record-set list
+ Looking up the DNS Record Sets
data:    Name                            : @
data:    Type                            : NS
data:    TTL                             : 172800
data:    Records:
data:      ns1-01.azure-dns.com.
data:      ns2-01.azure-dns.net.
data:      ns3-01.azure-dns.org.
data:      ns4-01.azure-dns.info.
data:
data:    Name                            : @
data:    Type                            : SOA
data:    TTL                             : 3600
data:    Email                           : azuredns-hostmaster.microsoft.com
data:    Host                            : ns1-01.azure-dns.com.
data:    Serial Number                   : 2
data:    Refresh Time                    : 3600
data:    Retry Time                      : 300
data:    Expire Time                     : 2419200
data:    Minimum TTL                     : 300
data:
info:    network dns record-set list command OK
```

> [!NOTE]
> DNS 区域根目录（或 *顶点*）的记录集使用 **@** 作为记录集名称。

### <a name="test-name-servers"></a>测试名称服务器

可使用 DNS 工具（如 nslookup、dig 或 `Resolve-DnsName` PowerShell cmdlet）测试 DNS 区域是否在 Azure DNS 服务器中。

如果尚未委托域而在 Azure DNS 中使用新的区域，则需要直接向区域的其中一个名称服务器提出 DNS 查询。 区域的名称服务器在 NS 记录中指定，如 `azure network dns record-set list` 所列。

以下示例使用分配给 DNS 区域的名称服务器，通过“dig”来查询域 contoso.com。 请确保将相应的值替换为区域的正确值。

     > dig @ns1-01.azure-dns.com contoso.com
     
     <<>> DiG 9.10.2-P2 <<>> @ns1-01.azure-dns.com contoso.com
    (1 server found)
    global options: +cmd
     Got answer:
    ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 60963
     flags: qr aa rd; QUERY: 1, ANSWER: 0, AUTHORITY: 1, ADDITIONAL: 1
     WARNING: recursion requested but not available

     OPT PSEUDOSECTION:
     EDNS: version: 0, flags:; udp: 4000
      QUESTION SECTION:
    contoso.com.                        IN      A

     AUTHORITY SECTION:
    contoso.com.         3600     IN      SOA     ns1-01.azure-dns.com. azuredns-hostmaster.microsoft.com. 1 3600 300 2419200 300

    Query time: 93 msec
    SERVER: 208.76.47.5#53(208.76.47.5)
    WHEN: Tue Jul 21 16:04:51 Pacific Daylight Time 2015
    MSG SIZE  rcvd: 120

## <a name="next-steps"></a>后续步骤

创建 DNS 区域后，请在区域中[创建 DNS 记录集和记录](dns-getstarted-create-recordset-cli.md)。




<!--HONumber=Dec16_HO3-->


