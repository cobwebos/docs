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
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/28/2016
ms.author: smalone
translationtype: Human Translation
ms.sourcegitcommit: 0f8bc125855bc5a5b67fde5b0b742c73b5da7610
ms.openlocfilehash: b7c91d7c13884a00bb06a7cfed55a59b357f7d18


---
# <a name="how-to-manage-reverse-dns-records-for-your-azure-services-using-the-azure-cli"></a>如何使用 Azure CLI 管理 Azure 服务的反向 DNS 记录

[!INCLUDE [dns-reverse-dns-record-operations-arm-selectors-include.md](../../includes/dns-reverse-dns-record-operations-arm-selectors-include.md)]


[!INCLUDE [dns-reverse-dns-record-operations-intro-include.md](../../includes/dns-reverse-dns-record-operations-intro-include.md)]


[!INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)]

有关经典部署模型的详细信息，请参阅[如何使用 Azure PowerShell 管理 Azure 服务（经典）的反向 DNS 记录](dns-reverse-dns-record-operations-classic-ps.md)。

## <a name="validation-of-reverse-dns-records"></a>反向 DNS 记录的验证
为了确保第三方不能创建映射到你的 DNS 域的反向 DNS 记录，Azure 只允许创建符合以下条件之一的反向 DNS 记录：

* “ReverseFqdn”等同于指定它的目标公共 IP 地址资源的“Fqdn”，或等同于同一订阅中的任何公共 IP 地址的“Fqdn”，例如，“ReverseFqdn”是“contosoapp1.northus.cloudapp.azure.com”。
* “ReverseFqdn”继续解析为指定它的目标公共 IP 地址的名称或 IP，或同一订阅中的任何公共 IP 地址“Fqdn”或 IP，例如“ReverseFqdn”是“app1.contoso.com”。 这是“contosoapp1.northus.cloudapp.azure.com”的 CName 别名。

只有当设置或修改公共 IP 地址的反向 DNS 属性时，才会执行验证检查。 不执行定期重新验证。

## <a name="add-reverse-dns-to-existing-public-ip-addresses"></a>将反向 DNS 添加到现有公共 IP 地址
可以使用 Azure 网络 public-ip 集将反向 DNS 添加到现有公共 IP 地址：

    azure network public-ip set -n PublicIp -g NRP-DemoRG-PS -f contosoapp1.westus.cloudapp.azure.com.

如果想要将反向 DNS 添加到尚不具有 DNS 名称的现有公共 IP 地址，则还必须指定 DNS 名称。 可以使用 Azure 网络 public-ip 集实现此操作：

    azure network public-ip set -n PublicIp -g NRP-DemoRG-PS -d contosoapp1 -f contosoapp1.westus.cloudapp.azure.com.

## <a name="create-a-public-ip-address-with-reverse-dns"></a>创建具有反向 DNS 的公共 IP 地址
可以添加具有使用 Azure 网络 public-ip create 指定的反向 DNS 属性的新公共 IP 地址：

    azure network public-ip create -n PublicIp3 -g NRP-DemoRG-PS -l westus -d contosoapp3 -f contosoapp3.westus.cloudapp.azure.com.

## <a name="view-reverse-dns-for-existing-public-ip-addresses"></a>查看现有公共 IP 地址的反向 DNS
可以使用 Azure 网络 public-ip show 查看现有公共 IP 地址的已配置值：

    azure network public-ip show -n PublicIp3 -g NRP-DemoRG-PS

## <a name="remove-reverse-dns-from-existing-public-ip-addresses"></a>从现有公共 IP 地址中删除反向 DNS
可以使用 Azure 网络 public-ip 集从现有公共 IP 地址中删除反向 DNS 属性。 这是通过将 ReverseFqdn 属性值设置为空白完成的：

    azure network public-ip set -n PublicIp3 -g NRP-DemoRG-PS –f “”

[!INCLUDE [FAQ1](../../includes/dns-reverse-dns-record-operations-faq-host-own-arpa-zone-include.md)]

[!INCLUDE [FAQ2](../../includes/dns-reverse-dns-record-operations-faq-arm-include.md)]




<!--HONumber=Nov16_HO3-->


