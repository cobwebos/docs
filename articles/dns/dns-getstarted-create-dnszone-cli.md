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
ms.date: 12/05/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: bfbffe7843bc178cdf289c999925c690ab82e922
ms.openlocfilehash: 5bbd490925e5e25f10044af55af49daa494ee026

---

# <a name="create-an-azure-dns-zone-using-cli"></a>使用 CLI 创建 Azure DNS 区域

> [!div class="op_single_selector"]
> * [Azure 门户](dns-getstarted-create-dnszone-portal.md)
> * [PowerShell](dns-getstarted-create-dnszone.md)
> * [Azure CLI](dns-getstarted-create-dnszone-cli.md)

本文将介绍使用跨平台的 Azure CLI（在 Windows、Mac 和 Linux 中可用）创建 DNS 区域的步骤。 还可以使用 PowerShell 或 Azure 门户创建 DNS 区域。

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]


## <a name="before-you-begin"></a>开始之前

在开始配置之前，请确认你具有以下各项。

* Azure 订阅。 如果你还没有 Azure 订阅，可以激活 [MSDN 订户权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或注册获取[免费帐户](https://azure.microsoft.com/pricing/free-trial/)。
* 需要安装最新版本的 Azure CLI（在 Windows、Linux 或 MAC 中可用）。 有关详细信息，请参阅 [安装 Azure CLI](../xplat-cli-install.md)。

## <a name="step-1---sign-in-and-create-a-resource-group"></a>步骤 1 - 登录并创建资源组

### <a name="switch-cli-mode"></a>切换 CLI 模式

Azure DNS 使用 Azure Resource Manager。 确保切换 CLI 模式，使用 ARM 命令。

```azurecli
azure config mode arm
```

### <a name="sign-in-to-your-azure-account"></a>登录到 Azure 帐户

系统将提示你使用凭据进行身份验证。 记住，只能使用 OrgID 帐户。

```azurecli
azure login
```

### <a name="select-the-subscription"></a>选择订阅

检查该帐户的订阅。

```azurecli
azure account list
```

选择要使用的 Azure 订阅。

```azurecli
azure account set "subscription name"
```

### <a name="create-a-resource-group"></a>创建资源组

Azure 资源管理器要求所有资源组指定一个位置。 此位置将用作该资源组中的资源的默认位置。 但是，由于所有 DNS 资源都是全局性而非区域性的，因此资源组位置的选择不会影响 Azure DNS。

如果使用现有资源组，可跳过此步骤。

```azurecli
azure group create -n myresourcegroup --location "West US"
```

### <a name="register-resource-provider"></a>注册资源提供程序

Azure DNS 服务由 Microsoft.Network 资源提供程序管理。 使用 Azure DNS 前，需要将 Azure 订阅注册为使用此资源提供程序。 对每个订阅而言，这都是一次性操作。

```azurecli
azure provider register --namespace Microsoft.Network
```

## <a name="step-2---create-a-dns-zone"></a>步骤 2 - 创建 DNS 区域

使用 `azure network dns zone create` 命令创建 DNS 区域。 若要查看此命令的帮助，请键入 `azure network dns zone create -h`。

下面的示例在名为“*MyResourceGroup*”的资源组中创建了一个名为“*contoso.com*”的 DNS 区域。 使用该示例创建 DNS 区域，将相应的值替换为你自己的值。

```azurecli
azure network dns zone create MyResourceGroup contoso.com
```

## <a name="step-3---verify"></a>步骤 3 - 验证

### <a name="view-records"></a>查看记录

创建 DNS 区域还会创建以下 DNS 记录：

* “授权起始点”(SOA) 记录。 此记录位于每个 DNS 区域的根目录。
* 授权名称服务器 (NS) 记录。 此记录显示托管该区域的名称服务器。 Azure DNS 使用名称服务器池，因此 Azure DNS 中的不同区域可以分配不同的名称服务器。 有关详细信息，请参阅 [向 Azure DNS 委托域](dns-domain-delegation.md) 。

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

如果尚未委托域而在 Azure DNS 中使用新的区域，则需要直接向区域的其中一个名称服务器提出 DNS 查询。 区域的名称服务器在 NS 记录中给出，如上面的“azure network dns record-set show”所列。 请确保在下面的命令中将相应的值替换为区域的正确值。

以下示例使用分配给 DNS 区域的名称服务器，通过“dig”来查询域 contoso.com。 该查询必须通过 DIG 指向使用 *@\<区域的名称服务器\>* 的名称服务器和区域名称。

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

创建 DNS 区域后，[创建记录集和记录](dns-getstarted-create-recordset-cli.md)为 Internet 域创建 DNS 记录。




<!--HONumber=Dec16_HO2-->


