---
title: 使用 Azure CLI 2.0 导入和导出 Azure DNS 的域区域文件 | Microsoft 文档
description: 了解如何通过使用 Azure CLI 2.0 导入和导出 Azure DNS 的 DNS 区域文件
services: dns
documentationcenter: na
author: KumudD
manager: timlt
ms.assetid: f5797782-3005-4663-a488-ac0089809010
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/30/2018
ms.author: kumud
ms.openlocfilehash: 3aee4e20b43d946101e692f0dca76b07e04dbb7a
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/11/2018
ms.locfileid: "34069371"
---
# <a name="import-and-export-a-dns-zone-file-using-the-azure-cli-20"></a>使用 Azure CLI 2.0 导入和导出 DNS 区域文件 

本文介绍如何使用 Azure CLI 2.0 导入和导出 Azure DNS 的 DNS 区域文件。

## <a name="introduction-to-dns-zone-migration"></a>DNS 区域迁移简介

DNS 区域文件是一个文本文件，其中包含区域中每个域名系统 (DNS) 记录的详细信息。 它会遵循标准格式，使其适合在 DNS 系统之间传输 DNS 记录。 使用区域文件是从 Azure DNS 传入或传出 DNS 区域的快速、可靠和方便的方法。

Azure DNS 支持通过使用 Azure 命令行接口 (CLI) 导入和导出区域文件。 当前**不**支持通过 Azure PowerShell 或 Azure 门户导入区域文件。

Azure CLI 2.0 是用于管理 Azure 服务的跨平台命令行工具。 它适用于 Windows、Mac 和 Linux 平台，可以从 [Azure 下载页](https://azure.microsoft.com/downloads/)获取。 跨平台支持对导入和导出区域文件很重要，因为最常见的名称服务器软件 [BIND](https://www.isc.org/downloads/bind/) 通常在 Linux 上运行。


## <a name="obtain-your-existing-dns-zone-file"></a>获取现有的 DNS 区域文件

将 DNS 区域文件导入 Azure DNS 之前，需要获取区域文件的副本。 此文件的来源取决于当前托管 DNS 区域的位置。

* 如果 DNS 区域由合作伙伴服务（如域注册机构、专用的 DNS 托管提供商或备用云提供商）托管，则该服务应提供下载 DNS 区域文件的功能。
* 如果 DNS 区域是在 Windows DNS 上托管的，则区域文件的默认文件夹是 **%systemroot%\system32\dns**。 每个区域文件的完整路径还会显示在 DNS 控制台的“常规”选项卡上。
* 如果 DNS 区域是通过使用 BIND 托管的，则在 BIND 配置文件 **named.conf** 中会指定每个区域的区域文件位置。

> [!NOTE]
> 从 GoDaddy 下载的区域文件会包含一些非标准格式。 需要先更正格式，然后再将这些区域文件导入 Azure DNS。
>
> RDATA 中每个 DNS 记录的 DNS 名称会指定为完全限定名称，但结尾没有“.”这意味着其他 DNS 系统会将它们解释为相对名称。 需要先进行编辑，以将结尾 "." 附加到其名称的区域文件，再将其导入 Azure DNS。
>
> 例如，CNAME 记录“www 3600 IN CNAME contoso.com”应更改为“www 3600 IN CNAME contoso.com.”
> （以“.”结尾）。

## <a name="import-a-dns-zone-file-into-azure-dns"></a>将 DNS 区域文件导入 Azure DNS

导入区域文件时会在 Azure DNS 中新建一个区域（如果区域尚不存在）。 如果区域已存在，则区域文件中的记录集必须与现有的记录集合并。

### <a name="merge-behavior"></a>合并行为

* 默认情况下，将合并现有的和新的记录集。 合并的记录集内的相同记录会执行重复数据删除。
* 合并记录集时，会使用以前存在的记录集的生存时间 (TTL)。
* 起始授权机构 (SOA) 参数（除了 `host`）始终取自导入的区域文件。 同样，对于区域顶点处的名称服务器记录集，TTL 始终取自导入的区域文件。
* 导入的 CNAME 记录不会替换现有的同名 CNAME 记录。  
* CNAME 记录与另一同名但类型不同的记录（无论是现有还是新建记录）发生冲突时，都会保留现有的记录。 

### <a name="additional-information-about-importing"></a>有关导入的其他信息

下面几点提供有关区域导入过程的其他技术详细信息。

* `$TTL` 指令是可选的并受支持。 如未提供 `$TTL` 指令，会导入没有显式 TTL 的记录，且其默认 TTL 设置为 3600 秒。 当同一个记录集的两个记录指定不同的 TTL 时，会使用较低的值。
* `$ORIGIN` 指令是可选的并受支持。 如果未设置 `$ORIGIN`，则使用的默认值是在命令行上指定的区域名称（加上结尾 "."）。
* `$INCLUDE` 和 `$GENERATE` 指令不受支持。
* 支持这些记录类型：A、AAAA、CNAME、MX、NS、SOA、SRV 和 TXT。
* Azure DNS 会在创建区域时，自动创建 SOA 记录。 导入区域文件时，*除了* `host` 参数，所有的 SOA 参数都取自区域文件。 此参数会使用 Azure DNS 提供的值。 这是因为此参数必须引用 Azure DNS 提供的主名称服务器。
* Azure DNS 在创建区域时，也会在区域顶点处自动创建名称服务器记录集。 仅导入此记录集的 TTL。 这些记录包含由 Azure DNS 提供的名称服务器名称。 导入的区域文件中包含的值不会覆盖记录数据。
* 在公开预览版期间，Azure DNS 仅支持单字符串的 TXT 记录。 多字符串 TXT 记录会被连接在一起并截断为 255 个字符。

### <a name="cli-format-and-values"></a>CLI 格式和值

用于导入 DNS 区域的 Azure CLI 命令的格式为：

```azurecli
az network dns zone import -g <resource group> -n <zone name> -f <zone file name>
```

值：

* `<resource group>` 是 Azure DNS 中区域的资源组名称。
* `<zone name>` 是区域的名称。
* `<zone file name>` 是要导入的区域文件的路径/名称。

如果资源组中不存在具有此名称的区域，会为你创建一个。 如果区域已存在，则导入的记录集会与现有的记录集合并。 


### <a name="step-1-import-a-zone-file"></a>步骤 1。 导入区域文件

导入区域 **contoso.com** 的区域文件。

1. 如果还没有 Resource Manager 资源组，则需要创建一个。

    ```azurecli
    az group create --group myresourcegroup -l westeurope
    ```

2. 要将文件 **contoso.com.txt** 中的区域 **contoso.com** 导入到资源组 **myresourcegroup** 中的新 DNS 区域，请运行命令 `az network dns zone import`。<BR>此命令将加载并分析区域文件。 此命令在 Azure DNS 服务上执行一系列命令，以便创建区域和区域中的所有记录集。 此命令在控制台窗口中报告进度，以及任何错误或警告。 由于记录集是以序列方式创建的，导入大型区域文件可能需要几分钟。

    ```azurecli
    az network dns zone import -g myresourcegroup -n contoso.com -f contoso.com.txt
    ```

### <a name="step-2-verify-the-zone"></a>步骤 2. 验证该区域

若要在导入文件后验证 DNS 区域，可以使用以下方法之一：

* 可以通过使用以下 Azure CLI 命令列出记录：

    ```azurecli
    az network dns record-set list -g myresourcegroup -z contoso.com
    ```

* 可以通过使用 PowerShell cmdlet `Get-AzureRmDnsRecordSet` 列出记录。
* 可以使用 `nslookup` 验证记录的名称解析。 由于尚未委派区域，因此需要显式指定正确的 Azure DNS 名称服务器。 下面的示例演示如何检索已分配给该区域的名称服务器的名称。 另外，还会演示如何使用 `nslookup` 查询“www”记录。

    ```azurecli
    az network dns record-set ns list -g myresourcegroup -z  --output json 
    ```

    ```json
    [
      {
       .......
       "name": "@",
        "nsRecords": [
          {
            "additionalProperties": {},
            "nsdname": "ns1-03.azure-dns.com."
          },
          {
            "additionalProperties": {},
            "nsdname": "ns2-03.azure-dns.net."
          },
          {
            "additionalProperties": {},
            "nsdname": "ns3-03.azure-dns.org."
          },
          {
            "additionalProperties": {},
            "nsdname": "ns4-03.azure-dns.info."
          }
        ],
        "resourceGroup": "myresourcegroup",
        "ttl": 86400,
        "type": "Microsoft.Network/dnszones/NS"
      }
    ]
    ```

    ```cmd
    nslookup www.contoso.com ns1-03.azure-dns.com

        Server: ns1-01.azure-dns.com
        Address:  40.90.4.1

        Name:www.contoso.com
        Addresses:  134.170.185.46
        134.170.188.221
    ```

### <a name="step-3-update-dns-delegation"></a>步骤 3. 更新 DNS 委派

验证该区域已正确导入后，需要更新 DNS 委派以指向 Azure DNS 名称服务器。 有关详细信息，请参阅文章[更新 DNS 委派](dns-domain-delegation.md)。

## <a name="export-a-dns-zone-file-from-azure-dns"></a>从 Azure DNS 导出 DNS 区域文件

用于导入 DNS 区域的 Azure CLI 命令的格式为：

```azurecli
az network dns zone export -g <resource group> -n <zone name> -f <zone file name>
```

值：

* `<resource group>` 是 Azure DNS 中区域的资源组名称。
* `<zone name>` 是区域的名称。
* `<zone file name>` 是要导出的区域文件的路径/名称。

和区域导入一样，必须先登录，选择订阅，然后配置 Azure CLI 以使用 Resource Manager 模式。

### <a name="to-export-a-zone-file"></a>导出区域文件

要将资源组 **myresourcegroup** 中现有的 Azure DNS 区域 **contoso.com** 导出至文件 **contoso.com.txt**（在当前文件夹中），请运行 `azure network dns zone export`。 此命令调用 Azure DNS 服务，以枚举该区域中的记录集，并将结果导出到兼容 BIND 的区域文件。

    ```
    az network dns zone export -g myresourcegroup -n contoso.com -f contoso.com.txt
    ```
