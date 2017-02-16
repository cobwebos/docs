---
title: "使用 PowerShell 管理 Azure DNS 中的反向 DNS 记录 | Microsoft Docs"
description: "Azure DNS 可让用户在资源管理器中使用 PowerShell 管理 Azure 服务的反向 DNS 记录或 PTR 记录"
services: DNS
documentationcenter: na
author: s-malone
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: b95703c5-e94e-4009-ab37-0c3f7908783c
ms.service: DNS
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/28/2016
ms.author: smalone
translationtype: Human Translation
ms.sourcegitcommit: efa52b5f30cab16bfde4202dbfe2c95f4464e2c4
ms.openlocfilehash: 730321ccacb211ec82e69f8ebc69ee84cbf46a01


---
# <a name="manage-reverse-dns-records-for-your-azure-services-using-powershell"></a>使用 PowerShell 管理 Azure 服务的反向 DNS 记录

[!INCLUDE [dns-reverse-dns-record-operations-arm-selectors-include.md](../../includes/dns-reverse-dns-record-operations-arm-selectors-include.md)]


[!INCLUDE [DNS-reverse-dns-record-operations-intro-include.md](../../includes/dns-reverse-dns-record-operations-intro-include.md)]


[!INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)]

有关经典部署模型的详细信息，请参阅[如何使用 Azure PowerShell 管理 Azure 服务（经典）的反向 DNS 记录](dns-reverse-dns-record-operations-classic-ps.md)。

## <a name="validation-of-reverse-dns-records"></a>反向 DNS 记录的验证
为了确保第三方不能创建映射到你的 DNS 域的反向 DNS 记录，Azure 只允许创建符合以下条件之一的反向 DNS 记录：

* “ReverseFqdn”等同于指定它的目标公共 IP 地址资源的“Fqdn”，或等同于同一订阅中的任何公共 IP 地址的“Fqdn”，例如，“ReverseFqdn”是“contosoapp1.northus.cloudapp.azure.com”。
* “ReverseFqdn”继续解析为指定它的目标公共 IP 地址的名称或 IP，或同一订阅中的任何公共 IP 地址“Fqdn”或 IP，例如“ReverseFqdn”是“app1.contoso.com”。 这是“contosoapp1.northus.cloudapp.azure.com”的 CName 别名。

只有当设置或修改公共 IP 地址的反向 DNS 属性时，才会执行验证检查。 不执行定期重新验证。

## <a name="add-reverse-dns-to-existing-public-ip-addresses"></a>将反向 DNS 添加到现有公共 IP 地址
可以使用“Set-AzureRmPublicIpAddress”cmdlet 将反向 DNS 添加到现有公共 IP 地址：

    PS C:\> $pip = Get-AzureRmPublicIpAddress -Name PublicIP -ResourceGroupName NRP-DemoRG-PS
    PS C:\> $pip.DnsSettings.ReverseFqdn = "contosoapp1.westus.cloudapp.azure.com."
    PS C:\> Set-AzureRmPublicIpAddress -PublicIpAddress $pip

如果想要将反向 DNS 添加到尚不具有 DNS 名称的现有公共 IP 地址，则还必须指定 DNS 名称。 使用“Set-AzureRmPublicIpAddress”cmdlet 实现此操作：

    PS C:\> $pip = Get-AzureRmPublicIpAddress -Name PublicIP -ResourceGroupName NRP-DemoRG-PS
    PS C:\> $pip.DnsSettings = New-Object -TypeName Microsoft.Azure.Commands.Network.Models.PSPublicIpAddressDnsSettings
    PS C:\> $pip.DnsSettings.DomainNameLabel = "contosoapp1"
    PS C:\> $pip.DnsSettings.ReverseFqdn = "contosoapp1.westus.cloudapp.azure.com."
    PS C:\> Set-AzureRmPublicIpAddress -PublicIpAddress $pip

## <a name="create-a-public-ip-address-with-reverse-dns"></a>创建具有反向 DNS 的公共 IP 地址
可以添加具有使用“New-AzureRmPublicIpAddress”cmdlet 指定的反向 DNS 属性的新公共 IP 地址：

    PS C:\> New-AzureRmPublicIpAddress -Name PublicIP2 -ResourceGroupName NRP-DemoRG-PS -Location WestUS -AllocationMethod Dynamic -DomainNameLabel "contosoapp2" -ReverseFqdn "contosoapp2.westus.cloudapp.azure.com."

## <a name="view-reverse-dns-for-existing-public-ip-addresses"></a>查看现有公共 IP 地址的反向 DNS
可以使用“Get-AzureRmPublicIpAddress”cmdlet 查看现有公共 IP 地址的已配置值：

    PS C:\> Get-AzureRmPublicIpAddress -Name PublicIP2 -ResourceGroupName NRP-DemoRG-PS

## <a name="remove-reverse-dns-from-existing-public-ip-addresses"></a>从现有公共 IP 地址中删除反向 DNS
可以使用“Set-AzureRmPublicIpAddress”cmdlet 从现有公共 IP 地址中删除反向 DNS 属性。 这是通过将 ReverseFqdn 属性值设置为空白完成的：

    PS C:\> $pip = Get-AzureRmPublicIpAddress -Name PublicIP -ResourceGroupName NRP-DemoRG-PS
    PS C:\> $pip.DnsSettings.ReverseFqdn = ""
    PS C:\> Set-AzureRmPublicIpAddress -PublicIpAddress $pip


[!INCLUDE [FAQ1](../../includes/dns-reverse-dns-record-operations-faq-host-own-arpa-zone-include.md)]

[!INCLUDE [FAQ2](../../includes/dns-reverse-dns-record-operations-faq-arm-include.md)]




<!--HONumber=Jan17_HO4-->


