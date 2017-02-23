---
title: "使用 PowerShell 管理经典 Azure 服务的反向 DNS 记录 | Microsoft Docs"
description: "Azure DNS 使用户能够在经典部署模型中使用 PowerShell 管理 Azure 服务的反向 DNS 记录或 PTR 记录。"
services: DNS
documentationcenter: na
author: s-malone
manager: carmonm
editor: 
tags: azure-service-management
ms.assetid: 9c24d176-6bce-4277-a14c-80fe44a20a87
ms.service: DNS
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/28/2016
ms.author: smalone
translationtype: Human Translation
ms.sourcegitcommit: dd020bf625510eb90af2e1ad19c155831abd7e75
ms.openlocfilehash: 399cfcad5c17cdb12a4063e11e1ff353e88e56a7


---
# <a name="how-to-manage-reverse-dns-records-for-your-azure-services-classic-using-azure-powershell"></a>如何使用 Azure PowerShell 管理 Azure 服务（经典）的反向 DNS 记录

[!INCLUDE [dns-reverse-dns-record-operations-arm-selectors-include.md](../../includes/dns-reverse-dns-record-operations-arm-selectors-include.md)]


[!INCLUDE [DNS-reverse-dns-record-operations-intro-include.md](../../includes/dns-reverse-dns-record-operations-intro-include.md)]


> [!IMPORTANT]
> Azure 提供两个不同的部署模型用于创建和处理资源：[Resource Manager 和经典模型](../azure-resource-manager/resource-manager-deployment-model.md)。 本文介绍如何使用经典部署模型。 Microsoft 建议大多数新部署使用资源管理器模型。 了解如何[使用 Resource Manager 模型执行这些步骤](dns-reverse-dns-record-operations-ps.md)。

## <a name="validation-of-reverse-dns-records"></a>反向 DNS 记录的验证
为了确保第三方不能创建映射到你的 DNS 域的反向 DNS 记录，Azure 只允许创建符合以下条件之一的反向 DNS 记录：

* 反向 DNS FQDN 是指定它的目标云服务的名称，或同一订阅中的任何云服务名称（例如反向 DNS 为“contosoapp1.cloudapp.net”）。
* 反向 DNS FQDN 继续解析为指定它的目标云服务的名称或 IP，或同一订阅中的任何云服务名称或 IP（例如反向 DNS 为“app1.contoso.com”）。 这是 contosoapp1.cloudapp.net 的 CName 别名。

只有当设置或修改云服务的反向 DNS 属性时，才会执行验证检查。 不执行定期重新验证。

## <a name="add-reverse-dns-to-existing-cloud-services"></a>将反向 DNS 添加到现有云服务
可以使用“Set-AzureService”cmdlet 将反向 DNS 记录添加到现有云服务：

```powershell
Set-AzureService –ServiceName "contosoapp1" –Description "App1 with Reverse DNS" –ReverseDnsFqdn "contosoapp1.cloudapp.net."
```

## <a name="create-a-cloud-service-with-reverse-dns"></a>使用反向 DNS 创建云服务
可以添加一个包含使用“Set-AzureService”cmdlet 指定的反向 DNS 属性的新云服务：

```powershell
New-AzureService –ServiceName "contosoapp1" –Location "West US" –Description "App1 with Reverse DNS" –ReverseDnsFqdn "contosoapp1.cloudapp.net."
```

## <a name="view-reverse-dns-for-existing-cloud-services"></a>查看现有云服务的反向 DNS
可以使用“Get-AzureService”cmdlet 查看现有云服务的配置值：

```powershell
Get-AzureService "contosoapp1"
```

## <a name="remove-reverse-dns-from-existing-cloud-services"></a>从现有云服务中删除反向 DNS
可以使用“Set-AzureService”cmdlet 从现有云服务中删除反向 DNS 属性。 这是通过将反向 DNS 属性值设置为空白完成的：

```powershell
Set-AzureService –ServiceName "contosoapp1" –Description "App1 with Reverse DNS" –ReverseDnsFqdn ""
```

[!INCLUDE [FAQ1](../../includes/dns-reverse-dns-record-operations-faq-host-own-arpa-zone-include.md)]

[!INCLUDE [FAQ2](../../includes/dns-reverse-dns-record-operations-faq-asm-include.md)]




<!--HONumber=Feb17_HO2-->


