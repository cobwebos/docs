---
title: 通过 Azure CLI 2.0 开始使用 Azure DNS 专用区域 | Microsoft Docs
description: 了解如何在 Azure DNS 中创建专用 DNS 区域和记录。 这是有关使用 Azure CLI 2.0 创建和管理第一个 DNS 专用区域和记录的分步指南。
services: dns
documentationcenter: na
author: KumuD
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: fb0aa0a6-d096-4d6a-b2f6-eda1c64f6182
ms.service: dns
ms.devlang: azurecli
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2018
ms.author: kumud
ms.openlocfilehash: d10f3201adb972468d8de7e66a940232ed19562d
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2018
---
# <a name="get-started-with-azure-dns-private-zones-using-azure-cli-20"></a>通过 Azure CLI 2.0 开始使用 Azure DNS 专用区域

> [!div class="op_single_selector"]
> * [PowerShell](private-dns-getstarted-powershell.md)
> * [Azure CLI 2.0](private-dns-getstarted-cli.md)

本文逐步讲解如何使用适用于 Windows、Mac 和 Linux 的跨平台 Azure CLI 2.0 创建第一个 DNS 区域和记录。 也可以使用 Azure PowerShell 执行这些步骤。

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

DNS 区域用来托管某个特定域的 DNS 记录。 若要开始在 Azure DNS 中托管域，需要为该域名创建 DNS 区域。 随后会在此 DNS 区域内为每个 DNS 记录创建域。 若要向虚拟网络发布专用 DNS 区域，请指定一个列表，其中包含允许在区域中解析记录的虚拟网络。  这些虚拟网络称为“解析虚拟网络”。  也可指定一个虚拟网络，让 Azure DNS 在创建 VM、更改 IP 或销毁 VM 时为其保留主机名记录。  这些虚拟网络称为“注册虚拟网络”。

本文中的说明假设读者已安装并登录到 Azure CLI 2.0，另外还安装了支持专用区域的所需 CLI 扩展。 若需帮助，请参阅[如何使用 Azure CLI 2.0 管理 DNS 区域](dns-operations-dnszones-cli.md)。

## <a name="to-installuse-azure-dns-private-zones-feature-public-preview"></a>安装/使用 Azure DNS 专用区域功能（公共预览版）
通过 Azure CLI 的扩展发布 Azure DNS 专用区域功能的公共预览版。 安装“dns”Azure CLI 扩展 

```
az extension add --name dns
``` 

## <a name="create-the-resource-group"></a>创建资源组

在创建 DNS 区域之前，创建了包含 DNS 区域的资源组。 以下显示该命令。

```azurecli
az group create --name MyResourceGroup --location "West US"
```

## <a name="create-a-dns-private-zone"></a>创建 DNS 专用区域

使用 `az network dns zone create` 命令创建 DNS 专用区域。 若要查看此命令的帮助，请键入 `az network dns zone create --help`。

以下示例在名为 *MyResourceGroup* 的资源组中创建名为 *contoso.local* 的 DNS 专用区域，并使用 resolution-vnets 参数将其提供给（链接到）虚拟网络 *MyAzureVnet*。 使用该示例创建 DNS 区域，将相应的值替换成自己的值。

```azurecli
az network dns zone create -g MyResourceGroup -n contoso.local --zone-type Private --resolution-vnets MyAzureVnet
```

注意：在以上示例中，虚拟网络“MyAzureVnet”属于专用区域所在的同一个资源组和订阅。 若要链接属于不同资源组或订阅的虚拟网络，需要为参数 --resolution-vnets 指定完整的 Azure 资源管理器 ID，而不只是虚拟网络名称。 

如果需要 Azure 在区域中自动创建主机名记录，请使用 *registration-vnets* 参数，而不要使用 *resolution-vnets*。  将自动允许注册虚拟网络进行解析。

```azurecli
az network dns zone create -g MyResourceGroup -n contoso.local --zone-type Private --registration-vnets MyAzureVnet
```

## <a name="create-a-dns-record"></a>创建 DNS 记录

若要创建 DNS 记录，请使用 `az network dns record-set [record type] add-record` 命令。 若需帮助，例如需要 A 记录的帮助信息，请参阅 `azure network dns record-set A add-record --help`。

以下示例在 DNS 区域“contoso.local”的资源组“MyResourceGroup”中创建相对名称为“ip1”的记录。 记录集的完全限定名称为“ip1.contoso.local”。 记录类型为“A”，IP 地址为“10.0.0.1”。

```azurecli
az network dns record-set a add-record -g MyResourceGroup -z contoso.local -n ip1 -a 10.0.0.1
```

对于其他记录类型，对于具有一条以上记录的记录集，若要使用其他 TTL 值并修改现有记录，请参阅[使用 Azure CLI 2.0 管理 DNS 记录和记录集](dns-operations-recordsets-cli.md)。

## <a name="view-records"></a>查看记录

若要列出区域中的 DNS 记录，请使用：

```azurecli
az network dns record-set list -g MyResourceGroup -z contoso.com
```

## <a name="get-a-dns-private-zone"></a>获取 DNS 专用区域

若要检索 DNS 专用区域，请使用 `az network dns zone show`。 有关帮助，请参阅 `az network dns zone show --help`。

以下示例从资源组 *MyResourceGroup* 返回 DNS 区域 *contoso.local* 及其关联的数据。 

```azurecli
az network dns zone show --resource-group MyResourceGroup --name contoso.local
```

以下示例是响应。

```json
{
  "etag": "00000002-0000-0000-3d4d-64aa3689d201",
  "id": "/subscriptions/147a22e9-2356-4e56-b3de-1f5842ae4a3b/resourceGroups/MyResourceGroup/providers/Microsoft.Network/dnszones/contoso.local",
  "location": "global",
  "maxNumberOfRecordSets": 5000,
  "name": "contoso.local",
  "nameServers": null,
  "numberOfRecordSets": 1,
  "registrationVirtualNetworks": [],
  "resolutionVirtualNetworks": [
    {
      "additionalProperties": {},
      "id": "/subscriptions/147a22e9-2356-4e56-b3de-1f5842ae4a3b/resourceGroups/MyResourceGroup/providers/Microsoft.Network/virtualNetworks/MyAzureVnet",
      "resourceGroup": "MyResourceGroup"
    }
  ]
  "resourceGroup": "MyResourceGroup",
  "tags": {},
  "type": "Microsoft.Network/dnszones",
  "zoneType": "Private"
}
```

请注意，`az network dns zone show` 不返回 DNS 记录。 若要列出 DNS 记录，请使用 `az network dns record-set list`。


## <a name="list-dns-zones"></a>列出 DNS 区域

若要枚举 DNS 区域，请使用 `az network dns zone list`。 有关帮助，请参阅 `az network dns zone list --help`。

指定资源组仅列出资源组内的区域：

```azurecli
az network dns zone list --resource-group MyResourceGroup
```

省略资源组可列出订阅中的所有区域：

```azurecli
az network dns zone list 
```

## <a name="update-a-dns-zone"></a>更新 DNS 区域

可以使用 `az network dns zone update` 对 DNS 区域资源进行更改。 有关帮助，请参阅 `az network dns zone update --help`。

此命令不会更新区域中的任何 DNS 记录集（请参阅[如何管理 DNS 记录](dns-operations-recordsets-cli.md)）。 它仅用于更新区域资源本身的属性。 对于专用区域，可以更新链接到该区域的“注册”或“解析”虚拟网络。 

以下示例演示如何更新链接到 DNS 专用区域的“解析”虚拟网络。 现有的链接“解析”虚拟网络已由指定的新虚拟网络替换。

```azurecli
az network dns zone update --resource-group MyResourceGroup --name contoso.local --zone-type Private --resolution-vnets MyNewAzureVnet
```

## <a name="delete-a-dns-zone"></a>删除 DNS 区域

可以使用 `az network dns zone delete` 删除 DNS 区域。 有关帮助，请参阅 `az network dns zone delete --help`。

> [!NOTE]
> 删除 DNS 区域也会删除该区域中的所有 DNS 记录。 此操作不可撤消。 如果 DNS 区域在使用中，则使用该区域的服务在区域删除后将无效。
>
>若要防止意外删除区域，请参阅[如何保护 DNS 区域和记录](dns-protect-zones-recordsets.md)。

此命令提示用户进行确认。 可选的 `--yes` 开关禁止显示此提示。

以下示例演示如何从资源组 *MyResourceGroup* 中删除区域 *contoso.local*。

```azurecli
az network dns zone delete --resource-group myresourcegroup --name contoso.local
```

## <a name="delete-all-resources"></a>删除所有资源
 
若要删除在本文中创建的所有资源，请执行以下步骤：

```azurecli
az group delete --name MyResourceGroup
```

## <a name="next-steps"></a>后续步骤

若要了解 Azure DNS 的详细信息，请参阅 [Azure DNS 概述](dns-overview.md)。

若要了解有关管理 Azure DNS 中的 DNS 区域的详细信息，请参阅[使用 Azure CLI 2.0 管理 Azure DNS 中的 DNS 区域](dns-operations-dnszones-cli.md)。

若要了解有关管理 Azure DNS 中的 DNS 记录的详细信息，请参阅[使用 Azure CLI 2.0 管理 Azure DNS 中的 DNS 记录和记录集](dns-operations-recordsets-cli.md)。
