---
title: 快速入门 - 使用 Azure CLI 创建 Azure DNS 区域和记录
description: 快速入门 - 了解如何在 Azure DNS 中创建 DNS 区域和记录。 这是有关使用 Azure CLI 创建和管理你的第一个 DNS 区域和记录的分步指南。
services: dns
author: vhorne
ms.service: dns
ms.topic: quickstart
ms.date: 7/16/2018
ms.author: victorh
ms.openlocfilehash: 3fb39558ff99c35786dedc133a9d1d1a450b5928
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/17/2018
ms.locfileid: "39090116"
---
# <a name="quickstart-create-an-azure-dns-zone-and-record-using-azure-cli"></a>快速入门：使用 Azure CLI 创建 Azure DNS 区域和记录

本文将引导你完成使用 Azure CLI（适用于 Windows、Mac 和 Linux）创建你的第一个 DNS 区域和记录的步骤。 也可以使用 [Azure 门户](dns-getstarted-portal.md)或 [Azure PowerShell](dns-getstarted-powershell.md) 执行这些步骤。

DNS 区域用来托管某个特定域的 DNS 记录。 若要开始在 Azure DNS 中托管域，需要为该域名创建 DNS 区域。 随后会在此 DNS 区域内为每个 DNS 记录创建域。 最后，若要将 DNS 区域发布到 Internet，需要为域配置名称服务器。 以下描述了上述每一个步骤。

Azure DNS 现在还支持专用 DNS 区域（当前为公开预览版）。 若要详细了解专用 DNS 区域，请参阅[将 Azure DNS 用于专用域](private-dns-overview.md)。 有关如何创建专用 DNS 区域的示例，请参阅[通过 CLI 开始使用 Azure DNS 专用区域](./private-dns-getstarted-cli.md)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-the-resource-group"></a>创建资源组

在创建 DNS 区域之前，创建一个资源组来包含 DNS 区域：

```azurecli
az group create --name MyResourceGroup --location "East US"
```

## <a name="create-a-dns-zone"></a>创建 DNS 区域

使用 `az network dns zone create` 命令创建 DNS 区域。 若要查看此命令的帮助，请键入 `az network dns zone create -h`。

以下示例在 *MyResourceGroup* 资源组中创建名为 *contoso.com* 的 DNS 区域。 使用该示例创建 DNS 区域，将相应的值替换成自己的值。

```azurecli
az network dns zone create -g MyResourceGroup -n contoso.com
```

## <a name="create-a-dns-record"></a>创建 DNS 记录

若要创建 DNS 记录，请使用 `az network dns record-set [record type] add-record` 命令。 若需帮助，例如需要 A 记录的帮助信息，请参阅 `azure network dns record-set A add-record -h`。

下面的示例在资源组“MyResourceGroup”中在 DNS 区域“contoso.com”中创建相对名称为“www”的一个记录集。 记录集的完全限定名称为“www.contoso.com”。 记录类型为“A”，IP 地址为“1.2.3.4”，并且使用了默认 TTL（3600 秒，1 小时）。

```azurecli
az network dns record-set a add-record -g MyResourceGroup -z contoso.com -n www -a 1.2.3.4
```

## <a name="view-records"></a>查看记录

若要列出区域中的 DNS 记录，请使用：

```azurecli
az network dns record-set list -g MyResourceGroup -z contoso.com
```

## <a name="update-name-servers"></a>更新名称服务器

正确设置 DNS 区域和记录后，需要将域名配置为使用 Azure DNS 名称服务器。 这样，Internet 上的其他用户便可以找到 DNS 记录。

区域的名称服务器是通过 `az network dns zone show` 命令指定的。 若要查看名称服务器名称，请使用 JSON 输出，如以下示例中所示。

```azurecli
az network dns zone show -g MyResourceGroup -n contoso.com -o json

{
  "etag": "00000003-0000-0000-b40d-0996b97ed101",
  "id": "/subscriptions/a385a691-bd93-41b0-8084-8213ebc5bff7/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com",
  "location": "global",
  "maxNumberOfRecordSets": 5000,
  "name": "contoso.com",
  "nameServers": [
    "ns1-01.azure-dns.com.",
    "ns2-01.azure-dns.net.",
    "ns3-01.azure-dns.org.",
    "ns4-01.azure-dns.info."
  ],
  "numberOfRecordSets": 3,
  "resourceGroup": "myresourcegroup",
  "tags": {},
  "type": "Microsoft.Network/dnszones"
}
```

这些名称服务器应当配置有域名注册机构（向其购买域名的机构）。 域名注册机构将提供选项来为域设置名称服务器。 有关详细信息，请参阅[将域委派给 Azure DNS](dns-domain-delegation.md)。

## <a name="delete-all-resources"></a>删除所有资源
 
当不再需要时，可以通过删除资源组来删除本快速入门中创建的所有资源：

```azurecli
az group delete --name MyResourceGroup
```

## <a name="next-steps"></a>后续步骤

现在，你已使用 Azure CLI 创建了你的第一个 DNS 区域和记录，可以在自定义域中为 Web 应用创建记录了。

> [!div class="nextstepaction"]
> [在自定义域中为 web 应用创建 DNS 记录](./dns-web-sites-custom-domain.md)
