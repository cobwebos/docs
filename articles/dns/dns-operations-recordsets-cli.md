---
title: "使用 Azure CLI 管理 Azure DNS 上的 DNS 记录集和记录 | Microsoft Docs"
description: "当在 Azure DNS 上托管域时在 Azure DNS 上管理 DNS 记录集和记录。 记录集和记录上的操作的所有 CLI 命令。"
services: dns
documentationcenter: na
author: jtuliani
manager: carmonm
ms.assetid: 5356a3a5-8dec-44ac-9709-0c2b707f6cb5
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/22/2016
ms.author: jonatul
translationtype: Human Translation
ms.sourcegitcommit: 02d720a04fdc0fa302c2cb29b0af35ee92c14b3b
ms.openlocfilehash: 2bc18d618cf8838209bea9f8a2d323e3b1042709

---

# <a name="manage-dns-records-and-record-sets-by-using-cli"></a>使用 CLI 管理 DNS 记录和记录集

> [!div class="op_single_selector"]
> * [Azure 门户](dns-operations-recordsets-portal.md)
> * [Azure CLI](dns-operations-recordsets-cli.md)
> * [PowerShell](dns-operations-recordsets.md)

本文演示如何通过使用跨平台的 Azure 命令行界面 (CLI) 来管理 DNS 区域的记录集和记录。

请务必了解 DNS 记录集与单独的 DNS 记录之间的差异。 记录集是某个区域中具有相同名称、相同类型的记录的集合。 有关详细信息，请参阅[了解记录集和记录](dns-getstarted-create-recordset-cli.md)。

## <a name="configure-the-cross-platform-azure-cli"></a>配置跨平台 Azure CLI

Azure DNS 是一项仅适用于 Azure 资源管理器的服务。 它没有 Azure 服务管理 API。 通过使用 `azure config mode arm` 命令，确保 Azure CLI 已配置为使用资源管理器模式。

如果你看到**错误：“dns”不是 Azure 命令**，则很可能是因为你在 Azure 服务管理模式（而不是资源管理器模式）下使用 Azure CLI。

## <a name="create-a-new-record-set-and-record"></a>创建新的记录集和记录

若要在 Azure 门户中创建新记录集，请参阅[创建记录集和记录](dns-getstarted-create-recordset-cli.md)。

## <a name="retrieve-a-record-set"></a>检索记录集

若要检索现有的记录集，请使用 `azure network dns record-set show`。 指定资源组、区域名称、记录集相对名称和记录类型。 使用下面的示例，并将值替换为你自己的值。

```azurecli
azure network dns record-set show myresourcegroup contoso.com www A
```

## <a name="list-record-sets"></a>列出记录集

可以通过使用 `azure network dns record-set list` 命令列出 DNS 区域中的所有记录。 你需要指定资源组名称和区域名称。

### <a name="to-list-all-record-sets"></a>列出所有记录集

此示例返回所有记录集，而不考虑名称或记录类型：

```azurecli
azure network dns record-set list myresourcegroup contoso.com
```

### <a name="to-list-record-sets-of-a-given-type"></a>列出给定类型的记录集

此示例返回与给定的记录类型（在此例中为“A”记录）匹配的所有记录集：

```azurecli
azure network dns record-set list myresourcegroup contoso.com A
```

## <a name="add-a-record-to-a-record-set"></a>将记录添加到记录集

通过使用 `azure network dns record-set add-record` 命令将记录添加到记录集。 用于将记录添加到记录集的参数会因设置的记录集类型而有所变化。 例如，在使用类型“A”记录集时，将只能使用参数 `-a <IPv4 address>` 指定记录。

若要创建记录集，请使用 `azure network dns record-set create` 命令。 指定资源组、区域名称、记录集相对名称、记录类型和存活时间 (TTL)。 如果未定义 `--ttl` 参数，则该值默认为 4（以秒为单位）。

```azurecli
azure network dns record-set create myresourcegroup  contoso.com "test-a"  A --ttl 300
```

创建“A”记录集后，通过使用 `azure network dns record-set add-record` 命令添加 IPv4 地址。

```azurecli
azure network dns record-set add-record myresourcegroup contoso.com "test-a" A -a 192.168.1.1
```

以下示例演示如何创建每个记录类型的记录集。 每个记录集只包含一个单一记录。

[!INCLUDE [dns-add-record-cli-include](../../includes/dns-add-record-cli-include.md)]

## <a name="update-a-record-in-a-record-set"></a>更新记录集中的记录

### <a name="to-add-another-ip-address-1234-to-an-existing-a-record-set-www"></a>将另一个 IP 地址 (1.2.3.4) 添加到现有“A”记录设置 ("www")：

    azure network dns record-set add-record  myresourcegroup contoso.com  A
    -a 1.2.3.4
    info:    Executing command network dns record-set add-record
    Record set name: www
    + Looking up the dns zone "contoso.com"
    + Looking up the DNS record set "www"
    + Updating DNS record set "www"
    data:    Id                              : /subscriptions/################################/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/a/www
    data:    Name                            : www
    data:    Type                            : Microsoft.Network/dnszones/a
    data:    Location                        : global
    data:    TTL                             : 4
    data:    A records:
    data:        IPv4 address                : 192.168.1.1
    data:        IPv4 address                : 1.2.3.4
    data:
    info:    network dns record-set add-record command OK

### <a name="to-remove-an-existing-value-from-a-record-set"></a>从记录集中删除现有值

使用 `azure network dns record-set delete-record`。

    azure network dns record-set delete-record myresourcegroup contoso.com www A -a 1.2.3.4
    info:    Executing command network dns record-set delete-record
    + Looking up the DNS record set "www"
    Delete DNS record? [y/n] y
    + Updating DNS record set "www"
    data:    Id                              : /subscriptions/################################/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/A/www
    data:    Name                            : www
    data:    Type                            : Microsoft.Network/dnszones/A
    data:    Location                        : global
    data:    TTL                             : 4
    data:    A records:
    data:    IPv4 address                    : 192.168.1.1
    data:
    info:    network dns record-set delete-record command OK

## <a name="remove-a-record-from-a-record-set"></a>从记录集中删除记录

可通过使用 `azure network dns record-set delete-record` 从记录集中删除记录。 要删除的记录必须与所有参数中的现有记录完全匹配。

从记录集中删除最后一条记录不会删除记录集。 有关详细信息，请参阅本文的[删除记录集](#delete)部分。

```azurecli
azure network dns record-set delete-record myresourcegroup contoso.com www A -a 192.168.1.1

azure network dns record-set delete myresourcegroup contoso.com www A
```

### <a name="remove-an-aaaa-record-from-a-record-set"></a>从记录集中删除 AAAA 记录

```azurecli
azure network dns record-set delete-record myresourcegroup contoso.com test-aaaa  AAAA -b "2607:f8b0:4009:1803::1005"
```

### <a name="remove-a-cname-record-from-a-record-set"></a>从记录集中删除 CNAME 记录

```azurecli
azure network dns record-set delete-record myresourcegroup contoso.com test-cname CNAME -c www.contoso.com
```

### <a name="remove-an-mx-record-from-a-record-set"></a>从记录集中删除 MX 记录

```azurecli
azure network dns record-set delete-record myresourcegroup contoso.com "@" MX -e "mail.contoso.com" -f 5
```

### <a name="remove-an-ns-record-from-record-set"></a>从记录集中删除 NS 记录

```azurecli
azure network dns record-set delete-record myresourcegroup contoso.com  "test-ns" NS -d "ns1.contoso.com"
```

### <a name="remove-a-ptr-record-from-a-record-set"></a>从记录集中删除 PTR 记录

在此例中，“my-arpa-zone.com”表示代表你的 IP 范围的 ARPA 区域。  此区域中的每个 PTR 记录集对应于此 IP 范围内的一个 IP 地址。

```azurecli
azure network dns record-set delete-record myresourcegroup my-arpa-zone.com "10" PTR -P "myservice.contoso.com"
```

### <a name="remove-an-srv-record-from-a-record-set"></a>从记录集中删除 SRV 记录

```azurecli
azure network dns record-set delete-record myresourcegroup contoso.com  "_sip._tls" SRV -p 0 -w 5 -o 8080 -u "sip.contoso.com"
```

### <a name="remove-a-txt-record-from-a-record-set"></a>从记录集中删除 TXT 记录

```azurecli
azure network dns record-set delete-record myresourcegroup contoso.com  "test-TXT" TXT -x "this is a TXT record"
```

## <a name="a-namedeleteadelete-a-record-set"></a><a name="delete"></a>删除记录集

可以通过使用 `Remove-AzureRmDnsRecordSet` cmdlet 删除记录集。 不能在创建区域时自动创建的区域顶点处（名称 = "@")）删除 SOA 和 NS 记录集。 如果删除区域，它们将被自动删除。

在下面的示例中，将从“contoso.com”DNS 区域中删除“A”记录集“test-a”：

```azurecli
azure network dns record-set delete myresourcegroup contoso.com  "test-a" A
```

可以使用可选的 `-q` 开关来取消此确认提示。

## <a name="next-steps"></a>后续步骤

有关 Azure DNS 的详细信息，请参阅 [Azure DNS 概述](dns-overview.md)。 有关自动执行 DNS 的信息，请参阅[使用 .NET SDK 创建 DNS 区域和记录集](dns-sdk.md)。

如果想要使用反向 DNS 记录，请参阅[如何使用 Azure CLI 管理服务的反向 DNS 记录](dns-reverse-dns-record-operations-cli.md)。



<!--HONumber=Nov16_HO3-->


