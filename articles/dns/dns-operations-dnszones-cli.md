---
title: "使用 CLI 管理 DNS 区域 | Microsoft Docs"
description: "可以使用 Azure CLI 管理 DNS 区域。 如何在 Azure DNS 上创建、更新和删除 DNS 区域"
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.assetid: 8ab63bc4-5135-4ed8-8c0b-5f0712b9afed
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/16/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 02d720a04fdc0fa302c2cb29b0af35ee92c14b3b
ms.openlocfilehash: 6fe10238adea0cbe1a47c05767930a363645028b

---

# <a name="how-to-manage-dns-zones-using-cli"></a>如何使用 CLI 管理 DNS 区域

> [!div class="op_single_selector"]
> * [Azure CLI](dns-operations-dnszones-cli.md)
> * [PowerShell](dns-operations-dnszones.md)

本指南将演示如何使用跨平台 Azure CLI 管理 DNS 区域资源。

这些说明使用 Microsoft Azure CLI。 请确保更新至最新的 Azure CLI（0.9.8 或更高版本），使用 Azure DNS 命令。 键入 `azure -v` 检查计算机中当前安装的 Azure CLI 版本。 可以在 Windows、Linux 或 Mac 上安装 Azure CLI。 有关详细信息，请参阅 [安装 Azure CLI](../xplat-cli-install.md)。

Azure DNS 是一项仅适用于 Azure Resource Manager 的服务。 它没有 ASM API。 你需要确保 Azure CLI 已配置为使用 Resource Manager 模式。 可以通过使用 `azure config mode arm` 命令完成此操作。

如果你看到消息“*错误：‘dns’不是 Azure 命令*”，则很可能是因为你在 ASM 模式（而不是 Resource Manager 模式）下使用 Azure CLI。

## <a name="create-a-new-dns-zone"></a>创建新的 DNS 区域

若要创建新的 DNS 区域以托管你的域，请参阅[使用 CLI 创建 Azure DNS 区域](dns-getstarted-create-dnszone-cli.md)。

## <a name="get-a-dns-zone"></a>获取 DNS 区域

若要检索 DNS 区域，请使用 `azure network dns zone show`：

```azurecli
azure network dns zone show myresourcegroup contoso.com
```

该操作返回 DNS 区域及其 ID、记录集数和标记。

## <a name="list-dns-zones"></a>列出 DNS 区域

若要检索资源组中的 DNS 区域，请使用 `azure network dns zone list`。

```azurecli
azure network dns zone list myresourcegroup
```

## <a name="update-a-dns-zone"></a>更新 DNS 区域

可以使用 `azure network dns zone set` 对 DNS 区域资源进行更改。 这不会更新区域中的任何 DNS 记录集（请参阅[如何管理 DNS 记录](dns-operations-recordsets.md)）。 该操作仅可用于更新区域资源本身的属性。 这目前仅限于区域资源的 Azure Resource Manager“标记”。 有关详细信息，请参阅 [Etag 和标记](dns-getstarted-create-dnszone.md#tagetag)。

```azurecli
azure network dns zone set myresourcegroup contoso.com -t prod=value2
```

## <a name="delete-a-dns-zone"></a>删除 DNS 区域

可以使用 `azure network dns zone delete` 删除 DNS 区域。 此操作具有可选 *-q* 开关，这样会禁止提示你确认要删除 DNS 区域。

在 Azure DNS 中删除 DNS 区域之前，需要删除所有记录集（创建区域时自动创建的区域根目录下的 NS 和 SOA 记录除外）。

```azurecli
azure network dns zone delete myresourcegroup contoso.com
```

## <a name="next-steps"></a>后续步骤

创建 DNS 区域后，创建 [记录集和记录](dns-getstarted-create-recordset-cli.md) ，开始解析 Internet 域的名称。




<!--HONumber=Nov16_HO3-->


