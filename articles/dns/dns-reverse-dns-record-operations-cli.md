---
title: "使用 Azure CLI 管理 Azure 服务的反向 DNS 记录 | Microsoft Docs"
description: "如何在 Resource Manager 中使用 Azure CLI 管理 Azure 服务的反向 DNS 记录或 PTR 记录"
services: DNS
documentationcenter: na
author: s-malone
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: c655707e-1156-4893-b163-0b228ffd25d2
ms.service: DNS
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2017
ms.author: smalone
translationtype: Human Translation
ms.sourcegitcommit: 1481fcb070f383d158c5a6ae32504e498de4a66b
ms.openlocfilehash: 1ac7bc232082100cd0f0fe3e337930a6c5e9ede9
ms.lasthandoff: 03/01/2017


---
# <a name="how-to-manage-reverse-dns-records-for-your-azure-services-using-the-azure-cli"></a>如何使用 Azure CLI 管理 Azure 服务的反向 DNS 记录

[!INCLUDE [dns-reverse-dns-record-operations-arm-selectors-include.md](../../includes/dns-reverse-dns-record-operations-arm-selectors-include.md)]


[!INCLUDE [dns-reverse-dns-record-operations-intro-include.md](../../includes/dns-reverse-dns-record-operations-intro-include.md)]

## <a name="cli-versions-to-complete-the-task"></a>用于完成任务的 CLI 版本

可以使用以下 CLI 版本之一完成任务：

* [Azure CLI 1.0](dns-reverse-dns-record-operations-cli-nodejs.md) - 适用于经典部署模型和资源管理部署模型的 CLI。
* [Azure CLI 2.0](dns-reverse-dns-record-operations-cli.md) - 适用于资源管理部署模型的下一代 CLI。

## <a name="introduction"></a>介绍

[!INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)]

有关经典部署模型的详细信息，请参阅[如何使用 Azure PowerShell 管理 Azure 服务（经典）的反向 DNS 记录](dns-reverse-dns-record-operations-classic-ps.md)。


## <a name="validation-of-reverse-dns-records"></a>反向 DNS 记录的验证
为了确保第三方不能创建映射到你的 DNS 域的反向 DNS 记录，Azure 只允许创建符合以下条件之一的反向 DNS 记录：

* “ReverseFqdn”等同于指定它的目标公共 IP 地址资源的“Fqdn”，或等同于同一订阅中的任何公共 IP 地址的“Fqdn”，例如，“ReverseFqdn”是“contosoapp1.northus.cloudapp.azure.com”。
* “ReverseFqdn”继续解析为指定它的目标公共 IP 地址的名称或 IP，或同一订阅中的任何公共 IP 地址“Fqdn”或 IP，例如“ReverseFqdn”是“app1.contoso.com”。 这是“contosoapp1.northus.cloudapp.azure.com”的 CName 别名。

只有当设置或修改公共 IP 地址的反向 DNS 属性时，才会执行验证检查。 不执行定期重新验证。

## <a name="add-reverse-dns-to-existing-public-ip-addresses"></a>将反向 DNS 添加到现有公共 IP 地址
可以使用 Azure 网络 public-ip 集将反向 DNS 添加到现有公共 IP 地址：

```azurecli
az network public-ip update --resource-group NRP-DemoRG-PS --name PublicIp --reverse-fqdn contosoapp1.westus.cloudapp.azure.com.
```

如果想要将反向 DNS 添加到尚不具有 DNS 名称的现有公共 IP 地址，则还必须指定 DNS 名称。 可以使用 Azure 网络 public-ip 集实现此操作：

```azurecli
az network public-ip update --resource-group NRP-DemoRG-PS --name PublicIp --reverse-fqdn contosoapp1.westus.cloudapp.azure.com --dns-name contosoapp1
```

## <a name="create-a-public-ip-address-with-reverse-dns"></a>创建具有反向 DNS 的公共 IP 地址
可以添加具有使用 Azure 网络 public-ip create 指定的反向 DNS 属性的新公共 IP 地址：

```azurecli
az network public-ip create --name PublicIp --resource-group NRP-DemoRG-PS --location westcentralus --dns-name contosoapp1 --reverse-fqdn contosoapp1.westcentralus.cloudapp.azure.com
```

## <a name="view-reverse-dns-for-existing-public-ip-addresses"></a>查看现有公共 IP 地址的反向 DNS
可以使用 Azure 网络 public-ip show 查看现有公共 IP 地址的已配置值：

```azurecli
 az network public-ip show --name PublicIp --resource-group NRP-DemoRG-PS
```

## <a name="remove-reverse-dns-from-existing-public-ip-addresses"></a>从现有公共 IP 地址中删除反向 DNS
可以使用 Azure 网络 public-ip 集从现有公共 IP 地址中删除反向 DNS 属性。 这是通过将 ReverseFqdn 属性值设置为空白完成的：

```azurecli
az network public-ip update --resource-group NRP-DemoRG-PS --name PublicIp --reverse-fqdn ""
```

[!INCLUDE [FAQ1](../../includes/dns-reverse-dns-record-operations-faq-host-own-arpa-zone-include.md)]

[!INCLUDE [FAQ2](../../includes/dns-reverse-dns-record-operations-faq-arm-include.md)]


