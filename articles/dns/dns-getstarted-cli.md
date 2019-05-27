---
title: 快速入门 - 使用 Azure CLI 创建 Azure DNS 区域和记录
description: 快速入门 - 了解如何在 Azure DNS 中创建 DNS 区域和记录。 这是有关使用 Azure CLI 创建和管理你的第一个 DNS 区域和记录的分步指南。
services: dns
author: vhorne
ms.service: dns
ms.topic: quickstart
ms.date: 3/11/2019
ms.author: victorh
ms.openlocfilehash: 7a2c300e30050e7e46a2b2c724258539df85e410
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "66111338"
---
# <a name="quickstart-create-an-azure-dns-zone-and-record-using-azure-cli"></a>快速入门：使用 Azure CLI 创建 Azure DNS 区域和记录

本文将引导你完成使用 Azure CLI（适用于 Windows、Mac 和 Linux）创建你的第一个 DNS 区域和记录的步骤。 也可以使用 [Azure 门户](dns-getstarted-portal.md)或 [Azure PowerShell](dns-getstarted-powershell.md) 执行这些步骤。

DNS 区域用来托管某个特定域的 DNS 记录。 若要开始在 Azure DNS 中托管域，需要为该域名创建 DNS 区域。 随后会在此 DNS 区域内为每个 DNS 记录创建域。 最后，要将 DNS 区域发布到 Internet，需要为域配置名称服务器。 以下描述了上述每一个步骤。

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

以下示例在 *MyResourceGroup* 资源组中创建名为 *contoso.xyz* 的 DNS 区域。 使用该示例创建 DNS 区域，将相应的值替换成自己的值。

```azurecli
az network dns zone create -g MyResourceGroup -n contoso.xyz
```

## <a name="create-a-dns-record"></a>创建 DNS 记录

若要创建 DNS 记录，请使用 `az network dns record-set [record type] add-record` 命令。 有关 A 记录的帮助，请参阅 `azure network dns record-set A add-record -h`。

以下示例在资源组“MyResourceGroup”中创建在 DNS 区域“contoso.xyz”中具有相对名称为“www”的一个记录。 记录集的完全限定名称为“www.contoso.xyz”。 记录类型为“A”，IP 地址为“10.10.10.10”，并且使用了默认 TTL 3600 秒（1 小时）。

```azurecli
az network dns record-set a add-record -g MyResourceGroup -z contoso.xyz -n www -a 10.10.10.10
```

## <a name="view-records"></a>查看记录

若要列出区域中的 DNS 记录，请运行：

```azurecli
az network dns record-set list -g MyResourceGroup -z contoso.xyz
```

## <a name="test-the-name-resolution"></a>测试名称解析

现在，你已有一个测试 DNS 区域且其中有一个测试“A”记录，可以使用名为 *nslookup* 的工具来测试名称解析了。 

**测试 DNS 名称解析：**

1. 运行以下 cmdlet 以获取区域的名称服务器列表：

   ```azurecli
   az network dns record-set ns show --resource-group MyResourceGroup --zone-name contoso.xyz --name @
   ```

1. 从上一步的输出中复制其中一个名称服务器名称。

1. 打开一个命令提示符，并运行以下命令：

   ```
   nslookup www.contoso.xyz <name server name>
   ```

   例如：

   ```
   nslookup www.contoso.xyz ns1-08.azure-dns.com.
   ```

   应当会看到类似以下屏幕的内容：

   ![nslookup](media/dns-getstarted-portal/nslookup.PNG)

主机名 www\.contoso.xyz 解析为 10.10.10.10，正如你配置的那样。 此结果表明名称解析正常工作。

## <a name="delete-all-resources"></a>删除所有资源

当不再需要时，可以通过删除资源组来删除本快速入门中创建的所有资源：

```azurecli
az group delete --name MyResourceGroup
```

## <a name="next-steps"></a>后续步骤

现在，你已使用 Azure CLI 创建了你的第一个 DNS 区域和记录，可以在自定义域中为 Web 应用创建记录了。

> [!div class="nextstepaction"]
> [在自定义域中为 web 应用创建 DNS 记录](./dns-web-sites-custom-domain.md)
