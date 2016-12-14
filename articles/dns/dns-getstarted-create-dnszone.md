---
title: "Azure DNS 入门 | Microsoft Docs"
description: "了解如何为 Azure DNS 创建 DNS 区域。逐步介绍如何首先使用 PowerShell 创建 DNS 区域，然后开始托管 DNS 域的过程。"
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
ms.date: 08/16/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 02d720a04fdc0fa302c2cb29b0af35ee92c14b3b
ms.openlocfilehash: 9f162115f2f080bd498085d5cb92b34452132ad8

---

# <a name="create-a-dns-zone-using-powershell"></a>使用 PowerShell 创建 DNS 区域

> [!div class="op_single_selector"]
> * [Azure 门户](dns-getstarted-create-dnszone-portal.md)
> * [PowerShell](dns-getstarted-create-dnszone.md)
> * [Azure CLI](dns-getstarted-create-dnszone-cli.md)

本文将逐步引导完成使用 PowerShell 创建 DNS 区域的过程。 还可以使用 CLI 或 Azure 门户创建 DNS 区域。

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

## <a name="a-nametagetagaabout-etags-and-tags"></a><a name="tagetag"></a>关于 Etag 和标记

### <a name="a-nameetagsaetags"></a><a name="etags"></a>Etag

假设两个人或两个进程尝试同时修改一条 DNS 记录。 哪一个占先？ 占先方是否知道他们/它们刚刚覆盖了其他人/进程创建的更改？

Azure DNS 使用 Etag 来安全地处理对同一资源的并发更改。 每个 DNS 资源（区域或记录集）都有与其相关联的 Etag。 只要检索资源，就会检索其 Etag。 当更新资源时，可以选择传递回 Etag 的选项以便 Azure DNS 可以验证服务器上的 Etag 是否匹配。 由于对资源的每次更新都会导致重新生成 Etag，Etag 不匹配表示发生了并发更改。 当创建新的资源时也会使用 Etag，以确保该资源不存在。

默认情况下，Azure DNS PowerShell 使用 Etag 来阻止对区域和记录集的并发更改。 可选 *-Overwrite* 开关可用于取消 Etag 检查，这种情况下将覆盖发生的所有并发更改。

Etag 是在 Azure DNS REST API 级别使用 HTTP 标头指定的。  下表给出了它们的行为：

| 标头 | 行为 |
| --- | --- |
| 无 |PUT 始终成功（没有 Etag 检查） |
| If-match <etag> |只有当资源存在并且 Etag 匹配时，PUT 才会成功 |
| If-match * |只有当资源存在时，PUT 才会成功 |
| If-none-match * |只有当资源不存在时，PUT 才会成功 |

### <a name="a-nametagsatags"></a><a name="tags"></a>标记

标记不同于 Etag。 标记是名称/值列表，Azure Resource Manager 利用它们来标记资源，进行计费和分组。 有关标记的详细信息，请参阅 [使用标记来组织 Azure 资源](../resource-group-using-tags.md)。

Azure DNS PowerShell 支持在通过选项 `-Tag` 参数指定的区域和记录集上使用标记。

## <a name="before-you-begin"></a>开始之前

在开始配置之前，请确认你具有以下各项。

* Azure 订阅。 如果你还没有 Azure 订阅，可以激活 [MSDN 订户权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或注册获取[免费帐户](https://azure.microsoft.com/pricing/free-trial/)。
* 你需要安装最新版本的 Azure Resource Manager PowerShell cmdlet（1.0 或更高）。 有关安装 PowerShell cmdlet 的详细信息，请参阅 [如何安装和配置 Azure PowerShell](../powershell-install-configure.md) 。

## <a name="step-1---sign-in"></a>步骤 1 - 登录

打开 PowerShell 控制台并连接到你的帐户。 有关详细信息，请参阅 [将 Windows PowerShell 与资源管理器配合使用](../powershell-azure-resource-manager.md)。

使用下面的示例来帮助你连接：

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

## <a name="step-2---create-a-resource-group"></a>步骤 2 - 创建资源组

Azure 资源管理器要求所有资源组指定一个位置。 此位置将用作该资源组中的资源的默认位置。 但是，由于所有 DNS 资源都是全局性而非区域性的，因此资源组位置的选择不会影响 Azure DNS。

如果使用现有资源组，可跳过此步骤。

```powershell
New-AzureRmResourceGroup -Name MyAzureResourceGroup -location "West US"
```

## <a name="step-3---register"></a>步骤 3 - 注册

Azure DNS 服务由 Microsoft.Network 资源提供程序管理。 使用 Azure DNS 前，需要将 Azure 订阅注册为使用此资源提供程序。 对每个订阅而言，这都是一次性操作。

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
```

## <a name="step-4---create-a-dns-zone"></a>步骤 4 - 创建 DNS 区域

通过使用 `New-AzureRmDnsZone` cmdlet 创建 DNS 区域。 下面是使用或不使用标记创建 DNS 区域的示例。 有关标记的详细信息，请参阅本文中有关[标记](#tags)的部分。

> [!NOTE]
> 在 Azure DNS 中，区域名称的指定不应以指“.”结尾。 例如，“**contoso.com**”是正确的，而“**contoso.com.**”是错误的。

### <a name="to-create-a-dns-zone"></a>创建 DNS 区域

下面的示例在名为“*MyResourceGroup*”的资源组中创建了一个名为“*contoso.com*”的 DNS 区域。 使用该示例创建 DNS 区域，将相应的值替换为你自己的值。

```powershell
New-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
```

### <a name="to-create-a-dns-zone-with-tags"></a>使用标记创建 DNS 区域

下面的示例演示了如何使用两个标记（*project = demo* 和 *env = test*）来创建 DNS 区域。 使用该示例创建 DNS 区域，将相应的值替换为你自己的值。

```powershell
New-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup -Tag @( @{ Name="project"; Value="demo" }, @{ Name="env"; Value="test" } )
```

## <a name="view-records"></a>查看记录

创建 DNS 区域还会创建以下 DNS 记录：

* “*授权起始点*”(SOA) 记录。 此记录位于每个 DNS 区域的根目录。
* 授权名称服务器 (NS) 记录。 此记录显示托管该区域的名称服务器。 Azure DNS 使用名称服务器池，因此 Azure DNS 中的不同区域可以分配不同的名称服务器。 有关详细信息，请参阅[向 Azure DNS 委托域](dns-domain-delegation.md)。

要查看这些记录，请使用 `Get-AzureRmDnsRecordSet`：

    Get-AzureRmDnsRecordSet -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup

    Name              : @
    ZoneName          : contoso.com
    ResourceGroupName : MyResourceGroup
    Ttl               : 3600
    Etag              : 2b855de1-5c7e-4038-bfff-3a9e55b49caf
    RecordType        : SOA
    Records           : {[ns1-01.azure-dns.com,msnhst.microsoft.com,900,300,604800,300]}
    Tags              : {}

    Name              : @
    ZoneName          : contoso.com
    ResourceGroupName : MyResourceGroup
    Ttl               : 3600
    Etag              : 5fe92e48-cc76-4912-a78c-7652d362ca18
    RecordType        : NS
    Records           : {ns1-01.azure-dns.com, ns2-01.azure-dns.net, ns3-01.azure-dns.org,
                  ns4-01.azure-dns.info}
    Tags              : {}


DNS 区域根目录（或 *顶点*）的记录集使用 **@** 作为记录集名称。

## <a name="test"></a>测试

可以使用 nslookup、DIG 或 [Resolve-DnsName PowerShell cmdlet](https://technet.microsoft.com/library/jj590781.aspx) 等 DNS 工具测试 DNS 区域。

如果尚未委托域在 Azure DNS 中使用新的区域，则需要直接向区域的其中一个名称服务器提出 DNS 查询。 区域的名称服务器在 NS 记录中给出，如上面的 `Get-AzureRmDnsRecordSet` 所列。 请确保在下面的命令中将相应的值替换为区域的正确值。

    nslookup
    > set type=SOA
    > server ns1-01.azure-dns.com
    > contoso.com

    Server: ns1-01.azure-dns.com
    Address:  208.76.47.1

    contoso.com
            primary name server = ns1-01.azure-dns.com
            responsible mail addr = msnhst.microsoft.com
            serial  = 1
            refresh = 900 (15 mins)
            retry   = 300 (5 mins)
            expire  = 604800 (7 days)
            default TTL = 300 (5 mins)

## <a name="next-steps"></a>后续步骤

创建 DNS 区域后，创建 [记录集和记录](dns-getstarted-create-recordset.md) ，开始解析 Internet 域的名称。



<!--HONumber=Nov16_HO3-->


