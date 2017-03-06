---
title: "在 Azure DNS 中管理 DNS 区域 - Azure CLI 1.0 | Microsoft 文档"
description: "可以使用 Azure CLI 1.0 管理 DNS 区域。 本文介绍如何在 Azure DNS 上更新、删除和创建 DNS 区域。"
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
ms.date: 12/21/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 36fa9cd757b27347c08f80657bab8a06789a3c2f
ms.openlocfilehash: 9b545937f3e375dfcef815a66263a57bd5042f69
ms.lasthandoff: 02/27/2017

---

# <a name="how-to-manage-dns-zones-in-azure-dns-using-the-azure-cli-10"></a>如何使用 Azure CLI 1.0 管理 Azure DNS 中的 DNS 区域

> [!div class="op_single_selector"]
> * [Azure CLI](dns-operations-dnszones-cli.md)
> * [PowerShell](dns-operations-dnszones.md)

本指南介绍如何通过使用适用于 Windows、Mac 和 Linux 的跨平台 Azure CLI 1.0 管理 DNS 区域。 也可以使用 [Azure PowerShell](dns-operations-dnszones.md) 或 Azure 门户管理 DNS 记区域。

## <a name="cli-versions-to-complete-the-task"></a>用于完成任务的 CLI 版本

可以使用以下 CLI 版本之一完成任务：

* [Azure CLI 1.0](dns-operations-dnszones-cli-nodejs.md) - 适用于经典部署模型和资源管理部署模型的 CLI。
* [Azure CLI 2.0](dns-operations-dnszones-cli.md) - 适用于资源管理部署模型的下一代 CLI。

## <a name="introduction"></a>介绍

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

[!INCLUDE [dns-cli-setup](../../includes/dns-cli-setup-include.md)]

## <a name="getting-help"></a>获取帮助

与 Azure DNS 相关的所有 CLI 1.0 命令都以 `azure network dns` 开头。 可使用 `--help` 选项（缩写形式 `-h`）获取有关每个命令的帮助。  例如：

```azurecli
azure network dns -h
azure network dns zone -h
azure network dns zone create -h
```

## <a name="create-a-dns-zone"></a>创建 DNS 区域

使用 `azure network dns zone create` 命令创建 DNS 区域。 有关帮助，请参阅 `azure network dns zone create -h`。

以下示例在名为 *MyResourceGroup* 的资源组中创建名为 *contoso.com* 的 DNS 区域：

```azurecli
azure network dns zone create MyResourceGroup contoso.com
```

### <a name="to-create-a-dns-zone-with-tags"></a>使用标记创建 DNS 区域。

下面的示例演示如何通过 `--tags` 参数（缩写形式 `-t`）使用两个 [Azure Resource Manager 标记](dns-zones-records.md#tags)、project = demo 和 env = test创建 DNS 区域：

```azurecli
azure network dns zone create MyResourceGroup contoso.com -t "project=demo";"env=test"
```

## <a name="get-a-dns-zone"></a>获取 DNS 区域

若要检索 DNS 区域，请使用 `azure network dns zone show`。 有关帮助，请参阅 `azure network dns zone show -h`。

以下示例从资源组 *MyResourceGroup* 返回 DNS 区域 *contoso.com* 及其关联数据。 

```azurecli
azure network dns zone show MyResourceGroup contoso.com
```

以下示例是响应。

```
info:    Executing command network dns zone show
+ Looking up the dns zone "contoso.com"
data:    Id                              : /subscriptions/.../contoso.com
data:    Name                            : contoso.com
data:    Type                            : Microsoft.Network/dnszones
data:    Location                        : global
data:    Number of record sets           : 2
data:    Max number of record sets       : 5000
data:    Name servers:
data:        ns1-01.azure-dns.com.
data:        ns2-01.azure-dns.net.
data:        ns3-01.azure-dns.org.
data:        ns4-01.azure-dns.info.
data:    Tags                            : project=demo;env=test
info:    network dns zone show command OK
```

请注意，`azure network dns zone show` 不返回 DNS 记录。 若要列出 DNS 记录，请使用 `azure network dns record-set list`。


## <a name="list-dns-zones"></a>列出 DNS 区域

若要枚举 DNS 区域，请使用 `azure network dns zone list`。 有关帮助，请参阅 `azure network dns zone list -h`。

指定资源组仅列出资源组内的区域：

```azurecli
azure network dns zone list MyResourceGroup
```

省略资源组可列出订阅中的所有区域：

```azurecli
azure network dns zone list 
```

## <a name="update-a-dns-zone"></a>更新 DNS 区域

可以使用 `azure network dns zone set` 对 DNS 区域资源进行更改。 有关帮助，请参阅 `azure network dns zone set -h`。

此命令不会更新区域中的任何 DNS 记录集（请参阅[如何管理 DNS 记录](dns-operations-recordsets-cli-nodejs.md)）。 它仅用于更新区域资源本身的属性。 这些属性当前仅限于区域资源的 [Azure Resource Manager“标记”](dns-zones-records.md#tags)。

以下示例演示如何更新 DNS 区域上的标记。 现有标记替换为指定值。

```azurecli
azure network dns zone set MyResourceGroup contoso.com -t "team=support"
```

## <a name="delete-a-dns-zone"></a>删除 DNS 区域

可以使用 `azure network dns zone delete` 删除 DNS 区域。 有关帮助，请参阅 `azure network dns zone delete -h`。

> [!NOTE]
> 删除 DNS 区域也会删除该区域中的所有 DNS 记录。 此操作不可撤消。 如果 DNS 区域在使用中，则使用该区域的服务在区域删除后将无效。
>
>若要防止意外删除区域，请参阅[如何保护 DNS 区域和记录](dns-protect-zones-recordsets.md)。

此命令提示用户进行确认。 可选的 `--quiet` 开关（缩写形式 `-q`）禁止显示此提示。

以下示例演示如何从资源组 *MyResourceGroup* 中删除区域 *contoso.com*。

```azurecli
azure network dns zone delete MyResourceGroup contoso.com
```

## <a name="next-steps"></a>后续步骤

了解如何在 DNS 区域中[管理记录集和记录](dns-getstarted-create-recordset-cli-nodejs.md)。

了解如何[将域委派给 Azure DNS](dns-domain-delegation.md)。


