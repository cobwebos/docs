---
title: "Azure 服务的反向 DNS | Microsoft Docs"
description: "了解如何为 Azure 中托管的服务配置反向 DNS 查找"
services: dns
documentationcenter: na
author: jtuliani
manager: timlt
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/29/2017
ms.author: jonatul
ms.translationtype: Human Translation
ms.sourcegitcommit: a643f139be40b9b11f865d528622bafbe7dec939
ms.openlocfilehash: 63701e1ce0c1c6dcf2ce02ebce272b8280395e7f
ms.contentlocale: zh-cn
ms.lasthandoff: 05/31/2017

---

# <a name="configure-reverse-dns-for-services-hosted-in-azure"></a>为 Azure 中托管的服务配置反向 DNS

本文介绍如何为 Azure 中托管的服务配置反向 DNS 查找。

Azure 中的服务使用 Azure 分配的、Microsoft 所有的 IP 地址。 必须在相应的 Microsoft 拥有的反向 DNS 查找区域中创建这些反向 DNS 记录（PTR 记录）。 本文介绍如何执行此操作。

不要将这种情况与[在 Azure DNS 中托管分配的 IP 范围的反向 DNS 查找区域](dns-reverse-dns-hosting.md)的能力相混淆。 在这种情况下，必须将由反向查找区域表示的 IP 范围分配给组织（通常通过 ISP 执行）。

阅读本文之前，应已熟悉此[反向 DNS 和 Azure 支持概述](dns-reverse-dns-overview.md)。

Azure 提供两个不同的部署模型用于创建和处理资源：[Resource Manager 和经典模型](../azure-resource-manager/resource-manager-deployment-model.md)。
* 在 Resource Manager 部署模型中，通过 PublicIpAddress 资源公开计算资源（如虚拟机、虚拟机规模集或 Service Fabric 群集）。 使用 PublicIpAddress 的“ReverseFqdn”属性配置反向 DNS 查找。
* 在经典部署模型中，使用云服务公开计算资源。 使用云服务的“ReverseDnsFqdn”属性配置反向 DNS 查找。

Azure App Service 目前不支持反向 DNS。

## <a name="validation-of-reverse-dns-records"></a>反向 DNS 记录的验证

第三方不应具备这样的能力，即为其映射到 DNS 域的 Azure 服务创建反向 DNS 记录。 为防止这种情况，Azure 仅允许创建满足以下条件的反向 DNS 记录，即反向 DNS 记录中指定的域名必须同于或可解析为同一 Azure 订阅中 PublicIpAddress 或云服务的 DNS 名称或 IP 地址。

仅在设置或修改反向 DNS 记录时执行此验证。 不执行定期重新验证。

例如：假定 PublicIpAddress 资源的 DNS 名称为 contosoapp1.northus.cloudapp.azure.com，IP 地址为 23.96.52.53。 PublicIpAddress 的 ReverseFqdn 可指定为：
* PublicIpAddress 的 DNS 名称 contosoapp1.northus.cloudapp.azure.com
* 同一订阅中另一个 PublicIpAddress 的 DNS 名称，如 contosoapp2.westus.cloudapp.azure.com
* 虚 DNS 名称（如 app1.contoso.com），只要此名称已提前配置为指向 contosoapp1.northus.cloudapp.azure.com 或同一订阅中另一个 PublicIpAddress 的 CNAME。
* 虚 DNS 名称（如 app1.contoso.com），只要此名称已提前配置为指向 IP 地址 23.96.52.53 或同一订阅中另一个 PublicIpAddress 的 IP 地址的 A 记录。

相同的约束也适用于云服务的反向 DNS。


## <a name="reverse-dns-for-publicipaddress-resources"></a>PublicIpAddress 资源的反向 DNS

本部分提供有关如何使用 Azure PowerShell、Azure CLI 1.0 或 Azure CLI 2.0 在 Resource Manager 部署模型中配置 PublicIpAddress 资源的反向 DNS 的详细说明。 目前不支持通过 Azure 门户配置 PublicIpAddress 资源的反向 DNS。

Azure 当前仅支持为 IPv4 PublicIpAddress 资源配置反向 DNS。 IPv6 不支持此操作。

### <a name="add-reverse-dns-to-an-existing-publicipaddresses"></a>将反向 DNS 添加到现有 PublicIpAddresses

#### <a name="powershell"></a>PowerShell

若要将反向 DNS 添加到现有 PublicIpAddress，请执行以下操作：

```powershell
$pip = Get-AzureRmPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup"
$pip.DnsSettings.ReverseFqdn = "contosoapp1.westus.cloudapp.azure.com."
Set-AzureRmPublicIpAddress -PublicIpAddress $pip
```

若要将反向 DNS 添加到尚不具有 DNS 名称的现有 PublicIpAddress，则还必须指定 DNS 名称：

```powershell
$pip = Get-AzureRmPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup"
$pip.DnsSettings = New-Object -TypeName "Microsoft.Azure.Commands.Network.Models.PSPublicIpAddressDnsSettings"
$pip.DnsSettings.DomainNameLabel = "contosoapp1"
$pip.DnsSettings.ReverseFqdn = "contosoapp1.westus.cloudapp.azure.com."
Set-AzureRmPublicIpAddress -PublicIpAddress $pip
```

#### <a name="azure-cli-10"></a>Azure CLI 1.0

若要将反向 DNS 添加到现有 PublicIpAddress，请执行以下操作：

```azurecli
azure network public-ip set -n PublicIp -g MyResourceGroup -f contosoapp1.westus.cloudapp.azure.com.
```

若要将反向 DNS 添加到尚不具有 DNS 名称的现有 PublicIpAddress，则还必须指定 DNS 名称：

```azurecli
azure network public-ip set -n PublicIp -g MyResourceGroup -d contosoapp1 -f contosoapp1.westus.cloudapp.azure.com.
```

#### <a name="azure-cli-20"></a>Azure CLI 2.0

若要将反向 DNS 添加到现有 PublicIpAddress，请执行以下操作：

```azurecli
az network public-ip update --resource-group MyResourceGroup --name PublicIp --reverse-fqdn contosoapp1.westus.cloudapp.azure.com.
```

若要将反向 DNS 添加到尚不具有 DNS 名称的现有 PublicIpAddress，则还必须指定 DNS 名称：

```azurecli
az network public-ip update --resource-group MyResourceGroup --name PublicIp --reverse-fqdn contosoapp1.westus.cloudapp.azure.com --dns-name contosoapp1
```

### <a name="create-a-public-ip-address-with-reverse-dns"></a>创建具有反向 DNS 的公共 IP 地址

若要创建具有已指定的反向 DNS 属性的新 PublicIpAddress，请执行以下操作：

#### <a name="powershell"></a>PowerShell

```powershell
New-AzureRmPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup" -Location "WestUS" -AllocationMethod Dynamic -DomainNameLabel "contosoapp2" -ReverseFqdn "contosoapp2.westus.cloudapp.azure.com."
```

#### <a name="azure-cli-10"></a>Azure CLI 1.0

```azurecli
azure network public-ip create -n PublicIp -g MyResourceGroup -l westus -d contosoapp3 -f contosoapp3.westus.cloudapp.azure.com.
```

#### <a name="azure-cli-20"></a>Azure CLI 2.0

```azurecli
az network public-ip create --name PublicIp --resource-group MyResourceGroup --location westcentralus --dns-name contosoapp1 --reverse-fqdn contosoapp1.westcentralus.cloudapp.azure.com
```

### <a name="view-reverse-dns-for-an-existing-publicipaddress"></a>查看现有 PublicIpAddress 的反向 DNS

若要查看现有 PublicIpAddress 的配置值，请执行以下操作：

#### <a name="powershell"></a>PowerShell

```powershell
Get-AzureRmPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup"
```

#### <a name="azure-cli-10"></a>Azure CLI 1.0

```azurecli
azure network public-ip show -n PublicIp -g MyResourceGroup
```

#### <a name="azure-cli-20"></a>Azure CLI 2.0

```azurecli
az network public-ip show --name PublicIp --resource-group MyResourceGroup
```

### <a name="remove-reverse-dns-from-existing-public-ip-addresses"></a>从现有公共 IP 地址中删除反向 DNS

若要从现有 PublicIpAddress 中删除反向 DNS 属性，请执行以下操作：

#### <a name="powershell"></a>PowerShell

```powershell
$pip = Get-AzureRmPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup"
$pip.DnsSettings.ReverseFqdn = ""
Set-AzureRmPublicIpAddress -PublicIpAddress $pip
```

#### <a name="azure-cli-10"></a>Azure CLI 1.0

```azurecli
azure network public-ip set -n PublicIp -g MyResourceGroup –f ""
```

#### <a name="azure-cli-20"></a>Azure CLI 2.0

```azurecli
az network public-ip update --resource-group MyResourceGroup --name PublicIp --reverse-fqdn ""
```


## <a name="configure-reverse-dns-for-cloud-services"></a>配置云服务的反向 DNS

本部分提供有关如何使用 Azure PowerShell 在经典部署模型中配置云服务的反向 DNS 的详细说明。 不支持通过 Azure 门户、Azure CLI 1.0 或 Azure CLI 2.0 配置云服务的反向 DNS。

### <a name="add-reverse-dns-to-existing-cloud-services"></a>将反向 DNS 添加到现有云服务

若要将反向 DNS 记录添加到现有云服务，请执行以下操作：

```powershell
Set-AzureService –ServiceName "contosoapp1" –Description "App1 with Reverse DNS" –ReverseDnsFqdn "contosoapp1.cloudapp.net."
```

### <a name="create-a-cloud-service-with-reverse-dns"></a>使用反向 DNS 创建云服务

若要创建具有已指定的反向 DNS 属性的新云服务，请执行以下操作：

```powershell
New-AzureService –ServiceName "contosoapp1" –Location "West US" –Description "App1 with Reverse DNS" –ReverseDnsFqdn "contosoapp1.cloudapp.net."
```

### <a name="view-reverse-dns-for-existing-cloud-services"></a>查看现有云服务的反向 DNS

若要查看现有云服务的反向 DNS 属性，请执行以下操作：

```powershell
Get-AzureService "contosoapp1"
```

### <a name="remove-reverse-dns-from-existing-cloud-services"></a>从现有云服务中删除反向 DNS

若要从现有云服务中删除反向 DNS 属性，请执行以下操作：

```powershell
Set-AzureService –ServiceName "contosoapp1" –Description "App1 with Reverse DNS" –ReverseDnsFqdn ""
```

## <a name="faq"></a>常见问题

### <a name="how-much-do-reverse-dns-records-cost"></a>反向 DNS 记录的费用如何？

完全免费！  反向 DNS 记录或查询不额外收取费用。

### <a name="will-my-reverse-dns-records-resolve-from-the-internet"></a>反向 DNS 记录是否从 Internet 解析？

是的。 为 Azure 服务设置反向 DNS 属性后，Azure 将管理所有必要的 DNS 委托和 DNS 区域，确保可为所有 Internet 用户解析反向 DNS 记录。

### <a name="are-default-reverse-dns-records-created-for-my-azure-services"></a>是否为 Azure 服务创建默认的反向 DNS 记录？

不能。 反向 DNS 是可选功能。 如果选择不配置反向 DNS，则不会创建任何默认的反向 DNS 记录。

### <a name="what-is-the-format-for-the-fully-qualified-domain-name-fqdn"></a>完全限定的域名 (FQDN) 的格式是什么？

FQDN 以正向顺序指定，且必须以点号结尾（例如“app1.contoso.com.”）。

### <a name="what-happens-if-the-validation-check-for-the-reverse-dns-ive-specified-fails"></a>如果指定的反向 DNS 验证检查失败，会发生什么情况？

如果反向 DNS 验证检查失败，则配置反向 DNS 记录的操作也会失败。 请根据需要更正反向 DNS 值，然后重试。

### <a name="can-i-configure-reverse-dns-for-azure-app-service"></a>是否可以为 Azure App Service 配置反向 DNS？

不能。 Azure App Service 不支持反向 DNS。

### <a name="can-i-configure-multiple-reverse-dns-records-for-my-azure-service"></a>是否可以为 Azure 服务配置多个反向 DNS 记录？

不能。 Azure 仅支持为每个 Azure 云服务或 PublicIpAddress 配置一个反向 DNS 记录。

### <a name="can-i-configure-reverse-dns-for-ipv6-publicipaddress-resources"></a>是否可以为 IPv6 PublicIpAddress 资源配置反向 DNS？

不能。 Azure 当前仅支持为 IPv4 PublicIpAddress 资源和云服务配置反向 DNS。

### <a name="can-i-send-emails-to-external-domains-from-my-azure-compute-services"></a>是否可以从 Azure 计算服务将电子邮件发送到外部域？

不能。 [Azure 计算服务不支持向外部域发送电子邮件](https://blogs.msdn.microsoft.com/mast/2016/04/04/sending-e-mail-from-azure-compute-resource-to-external-domains/)

## <a name="next-steps"></a>后续步骤

有关反向 DNS 的详细信息，请参阅[反向 DNS 查找](http://en.wikipedia.org/wiki/Reverse_DNS_lookup)。
<br>
了解如何[在 Azure DNS 中为 ISP 分配的 IP 范围托管反向查找区域](dns-reverse-dns-for-azure-services.md)。


