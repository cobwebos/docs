---
title: 将域委托给 Azure DNS | Microsoft 文档
description: 了解如何更改域委托并使用 Azure DNS 名称服务器提供域托管。
services: dns
documentationcenter: na
author: KumudD
manager: jeconnoc
ms.assetid: 257da6ec-d6e2-4b6f-ad76-ee2dde4efbcc
ms.service: dns
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/18/2017
ms.author: kumud
ms.openlocfilehash: a13d9b360e2c43aa2a3d4f62215e4570ce42cd5b
ms.sourcegitcommit: 28178ca0364e498318e2630f51ba6158e4a09a89
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2018
ms.locfileid: "28030512"
---
# <a name="delegate-a-domain-to-azure-dns"></a>将域委托给 Azure DNS

可以使用 Azure DNS 托管 DNS 区域，以及管理 Azure 中域的 DNS 记录。 要使域的 DNS 查询抵达 Azure DNS，必须将该域从其父域委托给 Azure DNS。 请记住，Azure DNS 不是域注册机构。 本文说明如何将域委派给 Azure DNS。

如果域是从注册机构购买的，注册机构将提供设置 NS 记录的选项。 不需要拥有一个域即可在 Azure DNS 中以该域名创建 DNS 区域。 但是，必须拥有域才能在注册机构中设置对 Azure DNS 的委托。

例如，假设购买了域 contoso.net，并在 Azure DNS 中创建了名为 contoso.net 的区域。 由于你是域的所有者，注册机构将为你提供选项来设置域的名称服务器地址（即 NS 记录）。 注册机构将这些 NS 记录存储在父域（即 .net）。 然后，世界各地的客户端在尝试解析 contoso.net 中的 DNS 记录时，会被定向到 Azure DNS 区域中的域。

## <a name="create-a-dns-zone"></a>创建 DNS 区域

1. 登录到 Azure 门户。
1. 在“中心”菜单中选择“新建” > “网络” > “DNS 区域”，打开“创建 DNS 区域”页。

   ![DNS 区域](./media/dns-domain-delegation/dns.png)

1. 在“创建 DNS 区域”页上输入以下值，然后选择“创建”：

   | **设置** | **值** | **详细信息** |
   |---|---|---|
   |**Name**|contoso.net|提供 DNS 区域的名称。|
   |**订阅**|[订阅]|选择要在其中创建应用程序网关的订阅。|
   |**资源组**|**新建：** contosoRG|创建资源组。 资源组名称必须在所选订阅中唯一。 若要详细了解资源组，请阅读 [Azure 资源管理器](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fdns%2ftoc.json#resource-groups)这篇概述文章。|
   |**位置**|美国西部||

> [!NOTE]
> 资源组的位置对 DNS 区域没有影响。 DNS 区域位置始终是“全局”，并且不会显示。

## <a name="retrieve-name-servers"></a>检索名称服务器

在将 DNS 区域委托给 Azure DNS 之前，需要知道区域的名称服务器。 每次创建区域时，Azure DNS 都会分配某个池中的名称服务器。

1. 创建 DNS 区域以后，在 Azure 门户的“收藏夹”窗格中选择“所有资源”。 在“所有资源”页中选择“contoso.net”DNS 区域。 如果所选订阅中已包含多个资源，则可在“按名称筛选”框中输入“contoso.net”，轻松访问应用程序网关。 

1. 从“DNS 区域”页中检索名称服务器。 在本示例中，为区域 contoso.net 分配了名称服务器 ns1-01.azure-dns.com、ns2-01.azure-dns.net、ns3-01.azure-dns.org 和 ns4-01.azure-dns.info：

   ![名称服务器的列表](./media/dns-domain-delegation/viewzonens500.png)

Azure DNS 自动在区域中创建要包含所分配名称服务器的权威 NS 记录。 检索这些记录即可通过 Azure PowerShell 或 Azure CLI 查看名称服务器。

以下示例提供了相关步骤，允许通过 PowerShell 和 Azure CLI 在 Azure DNS 中检索某个区域的名称服务器。

### <a name="powershell"></a>PowerShell

```powershell
# The record name "@" is used to refer to records at the top of the zone.
$zone = Get-AzureRmDnsZone -Name contoso.net -ResourceGroupName contosoRG
Get-AzureRmDnsRecordSet -Name "@" -RecordType NS -Zone $zone
```

以下示例是响应：

```
Name              : @
ZoneName          : contoso.net
ResourceGroupName : contosorg
Ttl               : 172800
Etag              : 03bff8f1-9c60-4a9b-ad9d-ac97366ee4d5
RecordType        : NS
Records           : {ns1-07.azure-dns.com., ns2-07.azure-dns.net., ns3-07.azure-dns.org.,
                    ns4-07.azure-dns.info.}
Metadata          :
```

### <a name="azure-cli"></a>Azure CLI

```azurecli
az network dns record-set list --resource-group contosoRG --zone-name contoso.net --type NS --name @
```

以下示例是响应：

```json
{
  "etag": "03bff8f1-9c60-4a9b-ad9d-ac97366ee4d5",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contosoRG/providers/Microsoft.Network/dnszones/contoso.net/NS/@",
  "metadata": null,
  "name": "@",
  "nsRecords": [
    {
      "nsdname": "ns1-07.azure-dns.com."
    },
    {
      "nsdname": "ns2-07.azure-dns.net."
    },
    {
      "nsdname": "ns3-07.azure-dns.org."
    },
    {
      "nsdname": "ns4-07.azure-dns.info."
    }
  ],
  "resourceGroup": "contosoRG",
  "ttl": 172800,
  "type": "Microsoft.Network/dnszones/NS"
}
```

## <a name="delegate-the-domain"></a>委托域

创建 DNS 区域且有了名称服务器以后，需使用 Azure DNS 名称服务器更新父域。 每个注册机构都有自身的 DNS 管理工具，可以更改域的名称服务器记录。 在注册机构的 DNS 管理页中，可以编辑 NS 记录并将 NS 记录替换为 Azure DNS 创建的记录。

将域委托给 Azure DNS 时，必须使用 Azure DNS 提供的名称服务器。 建议使用所有 4 个名称服务器，不管域名是什么。 域委托不需要名称服务器即可使用相同的顶级域作为域。

不要使用粘性记录指向 Azure DNS 名称服务器 IP 地址，因为这些 IP 地址今后可能会更改。 Azure DNS 目前不支持使用你自己区域中的名称服务器的委托（有时称为“虚构名称服务器”）。

## <a name="verify-that-name-resolution-is-working"></a>验证名称解析是否正常工作

完成委托后，可以使用 nslookup 等工具来查询区域的 SOA 记录，验证名称解析是否正常工作。 （SOA 记录是在创建区域时自动创建的。）

无需指定 Azure DNS 名称服务器。 如果正确设置了委托，正常的 DNS 解析过程会自动找到名称服务器。

```
nslookup -type=SOA contoso.com
```

下面是来自前一命令的示例响应：

```
Server: ns1-04.azure-dns.com
Address: 208.76.47.4

contoso.com
primary name server = ns1-04.azure-dns.com
responsible mail addr = msnhst.microsoft.com
serial = 1
refresh = 900 (15 mins)
retry = 300 (5 mins)
expire = 604800 (7 days)
default TTL = 300 (5 mins)
```

## <a name="delegate-subdomains-in-azure-dns"></a>在 Azure DNS 中委托子域

如果想要设置独立的子区域，可以在 Azure DNS 中委托子域。 例如，假设已在 Azure DNS 中设置和委托了 contoso.net。 现在需设置单独的自区域：partners.contoso.net。

1. 在 Azure DNS 中创建子区域 partners.contoso.net。
2. 查找子区域中的权威 NS 记录，获取在 Azure DNS 中托管子区域的名称服务器。
3. 通过在指向子区域的父区域中配置 NS 记录来委托子区域。

### <a name="create-a-dns-zone"></a>创建 DNS 区域

1. 登录到 Azure 门户。
1. 在“中心”菜单中选择“新建” > “网络” > “DNS 区域”，打开“创建 DNS 区域”页。

   ![DNS 区域](./media/dns-domain-delegation/dns.png)

1. 在“创建 DNS 区域”页上输入以下值，然后选择“创建”：

   | **设置** | **值** | **详细信息** |
   |---|---|---|
   |**Name**|partners.contoso.net|提供 DNS 区域的名称。|
   |**订阅**|[订阅]|选择要在其中创建应用程序网关的订阅。|
   |**资源组**|**使用现有：** contosoRG|创建资源组。 资源组名称必须在所选订阅中唯一。 若要详细了解资源组，请阅读 [Resource Manager](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fdns%2ftoc.json#resource-groups) 这篇概述文章。|
   |**位置**|美国西部||

> [!NOTE]
> 资源组的位置对 DNS 区域没有影响。 DNS 区域位置始终是“全局”，并且不会显示。

### <a name="retrieve-name-servers"></a>检索名称服务器

1. 创建 DNS 区域以后，在 Azure 门户的“收藏夹”窗格中选择“所有资源”。 在“所有资源”页中选择“partners.contoso.net”DNS 区域。 如果所选订阅中已包含多个资源，则可在“按名称筛选”框中输入“partners.contoso.net”，轻松访问 DNS 区域。

1. 从“DNS 区域”页中检索名称服务器。 在本示例中，为区域 contoso.net 分配了名称服务器 ns1-01.azure-dns.com、ns2-01.azure-dns.net、ns3-01.azure-dns.org 和 ns4-01.azure-dns.info：

   ![名称服务器的列表](./media/dns-domain-delegation/viewzonens500.png)

Azure DNS 自动在区域中创建要包含所分配名称服务器的权威 NS 记录。  检索这些记录即可通过 Azure PowerShell 或 Azure CLI 查看名称服务器。

### <a name="create-a-name-server-record-in-the-parent-zone"></a>在父区域中创建名称服务器记录

1. 在 Azure 门户中浏览到 **contoso.net** DNS 区域。
1. 选择“+ 记录集”。
1. 在“添加记录集”页中输入以下值，然后选择“确定”：

   | **设置** | **值** | **详细信息** |
   |---|---|---|
   |**Name**|合作伙伴|输入子 DNS 区域的名称。|
   |**类型**|NS|将 NS 用户名称服务器记录。|
   |**TTL**|1|输入生存时间。|
   |**TTL 单位**|小时|将生存时间单位设置为小时。|
   |**NAME SERVER**|{partners.contoso.net 区域中的名称服务器}|输入来自 partners.contoso.net 区域的所有 4 个名称服务器。 |

   ![名称服务器记录的值](./media/dns-domain-delegation/partnerzone.png)


### <a name="delegate-subdomains-in-azure-dns-by-using-other-tools"></a>在 Azure DNS 中使用其他工具来委托子域

以下示例提供了相关步骤，说明了如何使用 PowerShell 和 Azure CLI 在 Azure DNS 中委托子域。

#### <a name="powershell"></a>PowerShell

以下 PowerShell 示例演示委托子域的工作原理。 也可以通过 Azure 门户或跨平台 Azure CLI 工具完成相同的步骤。

```powershell
# Create the parent and child zones. These can be in the same resource group or different resource groups, because Azure DNS is a global service.
$parent = New-AzureRmDnsZone -Name contoso.net -ResourceGroupName contosoRG
$child = New-AzureRmDnsZone -Name partners.contoso.net -ResourceGroupName contosoRG

# Retrieve the authoritative NS records from the child zone as shown in the next example. This information contains the name servers assigned to the child zone.
$child_ns_recordset = Get-AzureRmDnsRecordSet -Zone $child -Name "@" -RecordType NS

# Create the corresponding NS record set in the parent zone to complete the delegation. The record set name in the parent zone matches the child zone name (in this case, "partners").
$parent_ns_recordset = New-AzureRmDnsRecordSet -Zone $parent -Name "partners" -RecordType NS -Ttl 3600
$parent_ns_recordset.Records = $child_ns_recordset.Records
Set-AzureRmDnsRecordSet -RecordSet $parent_ns_recordset
```

使用 `nslookup`，通过查找子区域的 SOA 记录来验证所有设置是否正确。

```
nslookup -type=SOA partners.contoso.com
```

```
Server: ns1-08.azure-dns.com
Address: 208.76.47.8

partners.contoso.com
    primary name server = ns1-08.azure-dns.com
    responsible mail addr = msnhst.microsoft.com
    serial = 1
    refresh = 900 (15 mins)
    retry = 300 (5 mins)
    expire = 604800 (7 days)
    default TTL = 300 (5 mins)
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli
#!/bin/bash

# Create the parent and child zones. These can be in the same resource group or different resource groups, because Azure DNS is a global service.
az network dns zone create -g contosoRG -n contoso.net
az network dns zone create -g contosoRG -n partners.contoso.net
```

从输出中检索 `partners.contoso.net` 区域的名称服务器。

```
{
  "etag": "00000003-0000-0000-418f-250de2b2d201",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contosorg/providers/Microsoft.Network/dnszones/partners.contoso.net",
  "location": "global",
  "maxNumberOfRecordSets": 5000,
  "name": "partners.contoso.net",
  "nameServers": [
    "ns1-09.azure-dns.com.",
    "ns2-09.azure-dns.net.",
    "ns3-09.azure-dns.org.",
    "ns4-09.azure-dns.info."
  ],
  "numberOfRecordSets": 2,
  "resourceGroup": "contosorg",
  "tags": {},
  "type": "Microsoft.Network/dnszones"
}
```

为每个名称服务器创建记录集和 NS 记录。

```azurecli
#!/bin/bash

# Create the record set
az network dns record-set ns create --resource-group contosorg --zone-name contoso.net --name partners

# Create an NS record for each name server.
az network dns record-set ns add-record --resource-group contosorg --zone-name contoso.net --record-set-name partners --nsdname ns1-09.azure-dns.com.
az network dns record-set ns add-record --resource-group contosorg --zone-name contoso.net --record-set-name partners --nsdname ns2-09.azure-dns.net.
az network dns record-set ns add-record --resource-group contosorg --zone-name contoso.net --record-set-name partners --nsdname ns3-09.azure-dns.org.
az network dns record-set ns add-record --resource-group contosorg --zone-name contoso.net --record-set-name partners --nsdname ns4-09.azure-dns.info.
```

## <a name="delete-all-resources"></a>删除所有资源

若要删除在本文中创建的所有资源，请完成以下步骤：

1. 在 Azure 门户的“收藏夹”窗格中，单击“所有资源”。 在“所有资源”页中选择“contosorg”资源组。 如果所选订阅中已包含多个资源，则可在“按名称筛选”框中输入“contosorg”，轻松访问资源组。
1. 在“contosorg”页中，选择“删除”按钮。
1. 门户会要求用户键入资源组的名称，以便确认用户需要删除它。 键入“contosorg”作为资源组名称，然后选择“删除”。 

删除资源组会删除该资源组中包含的所有资源。 在删除资源组之前，请务必确认其中的内容。 门户先删除资源组中的所有资源，然后删除资源组本身。 此过程需要几分钟。

## <a name="next-steps"></a>后续步骤

[Manage DNS zones](dns-operations-dnszones.md)

[Manage DNS records](dns-operations-recordsets.md)
