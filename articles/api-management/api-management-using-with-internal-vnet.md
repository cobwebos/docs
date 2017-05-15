---
title: "如何在内部虚拟网络中使用 Azure API 管理 | Microsoft 文档"
description: "了解如何在内部虚拟网络中设置和配置 Azure API 管理。"
services: api-management
documentationcenter: 
author: solankisamir
manager: kjoshi
editor: 
ms.assetid: dac28ccf-2550-45a5-89cf-192d87369bc3
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: apimpm
ms.translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: b9df2c3e7f49a47bfd714f28c5ab53590ca9a719
ms.contentlocale: zh-cn
ms.lasthandoff: 04/27/2017


---
# <a name="using-azure-api-management-service-with-internal-virtual-network"></a>在内部虚拟网络中使用 Azure API 管理服务
使用 Azure 虚拟网络 (VNET)，API 管理可以管理无法通过 Internet 访问的 API。 可以使用多种 VPN 技术进行连接，并可在 VNET 中通过两种主要模式部署 API 管理：
* 外部
* 内部

## <a name="overview"> </a>概述
在内部虚拟网络模式下部署 API 管理时，所有服务终结点（网关、开发人员门户、发布者门户、直接管理和 GIT）均只在用户可控制其访问权限的虚拟网络中可见。 这些服务终结点均未在公共 DNS 服务器上注册。

在内部模式下使用 API 管理时，可实现以下方案
* 让 API 安全地托管在专用数据中心，该数据中心可以通过站点到站点连接或 ExpressRoute VPN 连接由外部的第三方访问。
* 通过公共网关公开基于云的 API 和本地 API，以便启用混合云方案。
* 使用单一网关终结点管理托管在多个地理位置的 API。 

## <a name="enable-vpn"> </a>在内部虚拟网络中创建 API 管理
内部虚拟网络中的 API 管理服务托管在内部负载均衡器 (ILB) 后面。 ILB 的 IP 地址在 [RFC1918](http://www.faqs.org/rfcs/rfc1918.html) 范围内。  

### <a name="enable-vnet-connection-using-azure-portal"></a>使用 Azure 门户启用 VNET 连接
首先，请按[创建 API 管理服务][Create API Management service]中的步骤创建 API 管理服务。 然后，在虚拟网络中设置要部署的 API 管理。

![用于在内部虚拟网络中设置 APIM 的菜单][api-management-using-internal-vnet-menu]

部署成功后，就会在仪表板上看到服务的内部虚拟 IP 地址。

![配置了内部 VNET 的 API 管理仪表板][api-management-internal-vnet-dashboard]

### <a name="enable-vnet-connection-using-powershell-cmdlets"></a>使用 PowerShell cmdlet 启用 VNET 连接
还可以使用 PowerShell cmdlet 启用 VNET 连接。

* **在 VNET 内创建 API 管理服务**：使用 cmdlet [New-AzureRmApiManagement](/powershell/module/azurerm.apimanagement/new-azurermapimanagement) 在 VNET 内创建 Azure API 管理服务，并将其配置为使用内部 VNET 类型。

* **在 VNET 内部署现有 API 管理服务**：使用 cmdlet [Update-AzureRmApiManagementDeployment](/powershell/module/azurerm.apimanagement/update-azurermapimanagementdeployment) 将现有 Azure API 管理服务移到虚拟网络内，并将其配置为使用内部 VNET 类型。

## <a name="apim-dns-configuration"></a>DNS 配置
在外部虚拟网络模式下使用 API 管理时，由 Azure 托管 DNS。 使用内部虚拟网络模式时，必须自行管理 DNS。

> [!NOTE]
> API 管理服务不会侦听 IP 地址上的传入请求， 而只响应到 Hostname 的请求，该主机在其服务终结点（包括网关、开发人员门户、发布者门户、直接管理终结点，以及 GIT）上配置。

### <a name="access-on-default-host-names"></a>基于默认主机名的访问权限：
在公共 Azure 云（例如“contoso”）中创建 API 管理服务时，将会默认配置以下服务终结点。

>    网关/代理 - contoso.azure-api.net

> 发布者门户和开发人员门户 - contoso.portal.azure-api.net

> 直接管理终结点 - contoso.management.azure-api.net

>    GIT - contoso.scm.azure-api.net

若要访问这些 API 管理服务终结点，可以在连接到虚拟网络（其中部署了 API 管理）的子网中创建虚拟机。 假定服务的内部虚拟 IP 地址为 10.0.0.5，则可执行主机文件映射 (%SystemDrive%\drivers\etc\hosts)，如下所示：

> 10.0.0.5      contoso.azure-api.net

> 10.0.0.5      contoso.portal.azure-api.net

> 10.0.0.5      contoso.management.azure-api.net

> 10.0.0.5      contoso.scm.azure-api.net

然后即可从创建的虚拟机访问所有服务终结点。 如果在虚拟网络中使用自定义 DNS 服务器，则还可创建 DNS 记录并从虚拟网络中的任何位置访问这些终结点。 

### <a name="access-on-custom-domain-names"></a>基于自定义域名的访问权限：
如果不想通过默认主机名访问 API 管理服务，则可为所有服务终结点设置自定义域名，如下所示

![为 API 管理设置自定义域][api-management-custom-domain-name]

然后即可在用于访问这些终结点的 DNS 服务器中创建 A 记录，这些终结点只能从虚拟网络内部访问。

## <a name="related-content"></a>相关内容
* [在 VNET 中设置 APIM 时的常见网络配置问题][Common Network Configuration Issues]
* [虚拟网络常见问题](../virtual-network/virtual-networks-faq.md)
* [在 DNS 中创建 A 记录](https://msdn.microsoft.com/en-us/library/bb727018.aspx)

[api-management-using-internal-vnet-menu]: ./media/api-management-using-with-internal-vnet/api-management-internal-vnet-menu.png
[api-management-internal-vnet-dashboard]: ./media/api-management-using-with-internal-vnet/api-management-internal-vnet-dashboard.png
[api-management-custom-domain-name]: ./media/api-management-using-with-internal-vnet/api-management-custom-domain-name.png

[Create API Management service]: api-management-get-started.md#create-service-instance
[Common Network Configuration Issues]: api-management-using-with-vnet.md#network-configuration-issues

