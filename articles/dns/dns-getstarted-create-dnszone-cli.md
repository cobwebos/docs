---
title: "使用 Azure CLI 2.0 创建 DNS 区域 | Microsoft Docs"
description: "了解如何在 Azure DNS 中创建 DNS 区域。 这是有关使用 Azure CLI 2.0 创建和管理第一个 DNS 区域的分步指南。"
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
ms.date: 02/27/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 1481fcb070f383d158c5a6ae32504e498de4a66b
ms.openlocfilehash: 00a4dfbe070129ce6dc84f006d793007d55dba3f
ms.lasthandoff: 03/01/2017

---

# <a name="create-an-azure-dns-zone-using-azure-cli-20"></a>使用 Azure CLI 2.0 创建 Azure DNS 区域

> [!div class="op_single_selector"]
> * [Azure 门户](dns-getstarted-create-dnszone-portal.md)
> * [PowerShell](dns-getstarted-create-dnszone.md)
> * [Azure CLI 1.0](dns-getstarted-create-dnszone-cli-nodejs.md)
> * [Azure CLI 2.0](dns-getstarted-create-dnszone-cli.md)

本文将介绍使用跨平台的 Azure CLI（在 Windows、Mac 和 Linux 中可用）创建 DNS 区域的步骤。 也可以使用 [Azure PowerShell](dns-getstarted-create-dnszone.md) 或 [Azure 门户](dns-getstarted-create-dnszone-portal.md)创建 DNS 区域。

## <a name="cli-versions-to-complete-the-task"></a>用于完成任务的 CLI 版本

可以使用以下 CLI 版本之一完成任务：

* [Azure CLI 1.0](dns-getstarted-create-dnszone-cli-nodejs.md) - 适用于经典部署模型和资源管理部署模型的 CLI。
* [Azure CLI 2.0](dns-getstarted-create-dnszone-cli.md) - 适用于资源管理部署模型的下一代 CLI。

## <a name="introduction"></a>介绍

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

## <a name="set-up-azure-cli-20-for-azure-dns"></a>设置适用于 Azure DNS 的 Azure CLI 2.0

### <a name="before-you-begin"></a>开始之前

在开始配置之前，请确认你具有以下各项。

* Azure 订阅。 如果你还没有 Azure 订阅，可以激活 [MSDN 订户权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或注册获取[免费帐户](https://azure.microsoft.com/pricing/free-trial/)。

* 安装最新版本的 Azure CLI（在 Windows、Linux 或 MAC 中可用）。 有关详细信息，请参阅[安装 Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/install-az-cli2)。

### <a name="sign-in-to-your-azure-account"></a>登录到 Azure 帐户

打开控制台窗口并使用你的凭据进行身份验证。 有关详细信息，请参见“从 Azure CLI 登录 Azure”

```azurecli
az login
```

### <a name="select-the-subscription"></a>选择订阅

检查该帐户的订阅。

```azurecli
az account list
```

### <a name="choose-which-of-your-azure-subscriptions-to-use"></a>选择要使用的 Azure 订阅。

```azurecli
az account set --subscription "subscription name"
```

### <a name="create-a-resource-group"></a>创建资源组

Azure 资源管理器要求所有资源组指定一个位置。 此位置将用作该资源组中的资源的默认位置。 但是，由于所有 DNS 资源都是全局性而非区域性的，因此资源组位置的选择不会影响 Azure DNS。

如果使用现有资源组，可跳过此步骤。

```azurecli
az group create --name myresourcegroup --location "West US"
```

## <a name="create-a-dns-zone"></a>创建 DNS 区域

使用 `az network dns zone create` 命令创建 DNS 区域。 若要查看此命令的帮助，请键入 `az network dns zone create --help`。

以下示例在名为 *MyResourceGroup* 的资源组中创建名为 *contoso.com* 的 DNS 区域。 使用该示例创建 DNS 区域，将相应的值替换为你自己的值。

```azurecli
az network dns zone create --resource-group myresourcegroup --name contoso.com
```

## <a name="verify-your-dns-zone"></a>验证 DNS 区域

### <a name="view-records"></a>查看记录

创建 DNS 区域还会创建以下 DNS 记录：

* “*授权起始点*”(SOA) 记录。 此记录位于每个 DNS 区域的根目录。
* 授权名称服务器 (NS) 记录。 这些记录显示托管该区域的名称服务器。 Azure DNS 使用名称服务器池，因此可能会将不同的名称服务器分配给 Azure DNS 中的不同区域。 有关详细信息，请参阅[将域委托给 Azure DNS](dns-domain-delegation.md)。

要查看这些记录，请使用 `az network dns record-set list`：

```azurecli
az network dns record-set list --resource-group MyResourceGroup --zone-name contoso.com
```

`az network dns record-set list` 的响应如下所示：

```json
[
  {
    "etag": "510f4711-8b7f-414e-b8d5-c0383ea3d62e",
    "id": "/subscriptions/147a22e9-2356-4e56-b3de-1f5842ae4a3b/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/NS/@",
    "metadata": null,
    "name": "@",
    "nsRecords": [
      {
        "nsdname": "ns1-04.azure-dns.com."
      },
      {
        "nsdname": "ns2-04.azure-dns.net."
      },
      {
        "nsdname": "ns3-04.azure-dns.org."
      },
      {
        "nsdname": "ns4-04.azure-dns.info."
      }
    ],
    "resourceGroup": "myresourcegroup",
    "ttl": 172800,
    "type": "Microsoft.Network/dnszones/NS"
  },
  {
    "etag": "0e48e82f-f194-4d3e-a864-7e109a95c73a",
    "id": "/subscriptions/147a22e9-2356-4e56-b3de-1f5842ae4a3b/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/SOA/@",
    "metadata": null,
    "name": "@",
    "resourceGroup": "myresourcegroup",
    "soaRecord": {
      "email": "azuredns-hostmaster.microsoft.com",
      "expireTime": 2419200,
      "host": "ns1-04.azure-dns.com.",
      "minimumTtl": 300,
      "refreshTime": 3600,
      "retryTime": 300,
      "serialNumber": 1
    },
    "ttl": 3600,
    "type": "Microsoft.Network/dnszones/SOA"
  }
]
```

> [!NOTE]
> DNS 区域根目录（或 *顶点*）的记录集使用 **@** 作为记录集名称。

### <a name="test-name-servers"></a>测试名称服务器

可使用 DNS 工具（如 nslookup、dig 或 `Resolve-DnsName` PowerShell cmdlet）测试 DNS 区域是否在 Azure DNS 服务器中。

如果尚未委托域而在 Azure DNS 中使用新的区域，则需要直接向区域的其中一个名称服务器提出 DNS 查询。 区域的名称服务器在 NS 记录中指定，如 `az network dns record-set list` 所列。

以下示例使用分配给 DNS 区域的名称服务器，通过“dig”来查询域 contoso.com。 请确保将相应的值替换为区域的正确值。

```
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
```

## <a name="next-steps"></a>后续步骤

创建 DNS 区域后，请在区域中[创建 DNS 记录集和记录](dns-getstarted-create-recordset-cli.md)。


