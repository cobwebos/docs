---
title: "使用 Azure CLI 1.0 创建 DNS 记录 | Microsoft Docs"
description: "如何为 Azure DNS 创建主机记录。 使用 Azure CLI 1.0 设置记录集和记录"
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.assetid: 02b897d3-e83b-4257-b96d-5c29aa59e843
ms.service: dns
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/21/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 36fa9cd757b27347c08f80657bab8a06789a3c2f
ms.openlocfilehash: 45599ea87b536b74cc652ef28f91a6058c7c8e4a
ms.lasthandoff: 02/27/2017

---

# <a name="create-dns-records-using-the-azure-cli-10"></a>使用 Azure CLI 1.0 创建 DNS 记录

> [!div class="op_single_selector"]
> * [Azure 门户](dns-getstarted-create-recordset-portal.md)
> * [PowerShell](dns-getstarted-create-recordset.md)
> * [Azure CLI 1.0](dns-getstarted-create-recordset-cli-nodejs.md)
> * [Azure CLI 2.0](dns-getstarted-create-recordset-cli.md)

本文逐步讲解如何使用 Azure CLI 1.0 完成创建记录和记录集的过程。

## <a name="cli-versions-to-complete-the-task"></a>用于完成任务的 CLI 版本

可以使用以下 CLI 版本之一完成任务：

* [Azure CLI 1.0](dns-getstarted-create-recordset-cli-nodejs.md) - 适用于经典部署模型和资源管理部署模型的 CLI。
* [Azure CLI 2.0](dns-getstarted-create-recordset-cli.md) - 适用于资源管理部署模型的下一代 CLI。

## <a name="introduction"></a>介绍

在 Azure DNS 中创建 DNS 记录之前，首先需了解 Azure DNS 如何将 DNS 记录组织到 DNS 记录集中。

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

有关 Azure DNS 中的 DNS 记录的详细信息，请参阅 [DNS 区域和记录](dns-zones-records.md)。

## <a name="create-a-record-set-and-record"></a>创建记录集和记录

本部分介绍如何在 Azure DNS 中创建 DNS 记录。 示例假设你已经[安装并登录 Azure CLI 1.0 以及创建了 DNS 区域](dns-getstarted-create-dnszone-cli-nodejs.md)。

此页上所有示例都使用“A”DNS 记录类型。 有关其他记录类型和有关如何管理 DNS 记录和记录集的详细信息，请参阅[使用 Azure CLI 1.0 管理 DNS 记录和记录集](dns-operations-recordsets-cli-nodejs.md)。

## <a name="create-a-dns-record"></a>创建 DNS 记录

若要创建 DNS 记录，请使用 `azure network dns record-set add-record` 命令。 有关帮助，请参阅 `azure network dns record-set add-record -h`。

创建记录时，需指定资源组名称、区域名称、记录集名称、记录类型，以及要创建的记录的详细信息。

如果记录集不存在，此命令将会创建。 如果记录集已存在，此命令会将指定的记录添加到现有记录集。 

如果创建了新记录集，将使用默认生存时间 (TTL) 3600。 有关如何使用不同 TTL 的说明，请参阅[使用 Azure CLI 1.0 管理 Azure DNS 中的 DNS 记录](dns-operations-recordsets-cli-nodejs.md)。

以下示例在区域 *contoso.com* 中的资源组 *MyResourceGroup* 内创建名为 *www* 的 A 记录。 该 A 记录的 IP 地址为 *1.2.3.4*。

```azurecli
azure network dns record-set add-record MyResourceGroup contoso.com www A -a 1.2.3.4
```

若要在区域顶点中创建记录集（在这种情况下为“contoso.com”），使用记录名称“@”，包括引号：

```azurecli
azure network dns record-set add-record MyResourceGroup contoso.com "@" A -a 1.2.3.4
```

用于指定记录数据的参数根据记录类型的不同而异。 例如，对于“A”类型的记录，应使用参数 `-a <IPv4 address>` 指定 IPv4 地址。 若要列出其他记录类型的参数，请参阅 `azure network dns record-set add-record -h`。 有关每种记录类型的示例，请参阅[使用 Azure CLI 1.0 管理 DNS 记录和记录集](dns-operations-recordsets-cli-nodejs.md)。


## <a name="verify-name-resolution"></a>验证名称解析

可使用 DNS 工具（如 nslookup、dig 或 [Resolve-DnsName PowerShell cmdlet](https://technet.microsoft.com/library/jj590781.aspx)）测试 DNS记录是否在 Azure DNS 服务器中。

如果尚未委托域在 Azure DNS 中使用新的区域，则需要[直接向区域的其中一个名称服务器提出 DNS 查询](dns-getstarted-create-dnszone.md#test-name-servers)。 确保将记录区域的正确值替换到下面命令中。

```
nslookup
> set type=A
> server ns1-01.azure-dns.com
> www.contoso.com

Server:  ns1-01.azure-dns.com
Address:  40.90.4.1

Name:    www.contoso.com
Address:  1.2.3.4
```

## <a name="next-steps"></a>后续步骤

了解如何[将域名委托到 Azure DNS 名称服务器](dns-domain-delegation.md)

了解如何[使用 Azure CLI 1.0 管理 DNS 区域](dns-operations-dnszones-cli-nodejs.md)。

了解如何[使用 Azure CLI 1.0 管理 DNS 记录和记录集](dns-operations-recordsets-cli-nodejs.md)。


