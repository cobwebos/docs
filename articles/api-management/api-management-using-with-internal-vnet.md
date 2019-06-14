---
title: 如何在内部虚拟网络中使用 Azure API 管理 | Microsoft Docs
description: 了解如何在内部虚拟网络中设置和配置 Azure API 管理
services: api-management
documentationcenter: ''
author: vladvino
manager: kjoshi
editor: ''
ms.assetid: dac28ccf-2550-45a5-89cf-192d87369bc3
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/11/2019
ms.author: apimpm
ms.openlocfilehash: a5d8a724a0b4dd6899a71187176b9d444e5fe19c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67051682"
---
# <a name="using-azure-api-management-service-with-an-internal-virtual-network"></a>在内部虚拟网络中使用 Azure API 管理服务
使用 Azure 虚拟网络，Azure API 管理可以管理无法通过 Internet 访问的 API。 可以使用多种 VPN 技术建立连接。 可在虚拟网络中通过两种主要模式部署 API 管理：
* 外部
* 内部

时在内部虚拟网络模式下部署 API 管理，所有服务终结点 （代理网关、 开发人员门户、 直接管理和 Git） 都均只在控制其访问权限的虚拟网络中可见。 这些服务终结点均未在公共 DNS 服务器上注册。

> [!NOTE]
> 因为没有为服务终结点的 DNS 条目，这些终结点将不可访问，直到[DNS 配置](#apim-dns-configuration)为虚拟网络。

在内部模式下使用 API 管理时，可实现以下方案：

* 让 API 安全地托管在专用数据中心，该数据中心可以通过站点到站点连接或 Azure ExpressRoute VPN 连接由外部的第三方访问。
* 通过公共网关公开基于云的 API 和本地 API，以便启用混合云方案。
* 使用单一网关终结点管理托管在多个地理位置的 API。

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>必备组件

若要执行本文中所述的步骤，必须具有：

+ **一个有效的 Azure 订阅**。

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ **一个 Azure API 管理实例**。 有关详细信息，请参阅[创建 Azure API 管理实例](get-started-create-service-instance.md)。
+ 当 API 管理服务部署在虚拟网络中时，将使用[列表中的端口](./api-management-using-with-vnet.md#required-ports)并且需要重新打开它们。 

## <a name="enable-vpn"> </a>在内部虚拟网络中创建 API 管理
内部虚拟网络中的 API 管理服务托管在[内部负载均衡器（经典）](https://docs.microsoft.com/azure/load-balancer/load-balancer-get-started-ilb-classic-cloud)后面。 这是唯一可用的选项，不能更改。

### <a name="enable-a-virtual-network-connection-using-the-azure-portal"></a>使用 Azure 门户启用虚拟网络连接

1. 在 [Azure 门户](https://portal.azure.com/)中浏览到自己的 Azure API 管理实例。
2. 选择“虚拟网络”  。
3. 配置要在虚拟网络内部署的 API 管理实例。

    ![用于在内部虚拟网络中设置 Azure API 管理的菜单][api-management-using-internal-vnet-menu]

4. 选择“保存”。 

部署成功后，应该可以在概览边栏选项卡上看到 API 管理服务的**专用**虚拟 IP 地址和**公共**虚拟 IP 地址。 **专用**虚拟 IP 地址是 API 管理委托的子网中经负载均衡的 IP 地址，可以通过该子网访问 `gateway`、`portal`、`management` 和 `scm` 终结点。 **公共**虚拟 IP 地址**仅**用于经端口 3443 发往 `management` 终结点的控制平面流量，并且可以锁定到 [ApiManagement][ServiceTags] servicetag。

![包含已配置的内部虚拟网络的 Azure API 管理仪表板][api-management-internal-vnet-dashboard]

> [!NOTE]
> Azure 门户上提供的测试控制台不适用于**内部** VNET 部署的服务，因为网关 URL 未在公共 DNS 上注册。 应改用**开发人员门户**上提供的测试控制台。

### <a name="enable-a-virtual-network-connection-by-using-powershell-cmdlets"></a>使用 PowerShell cmdlet 启用虚拟网络连接

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

也可以使用 PowerShell cmdlet 启用虚拟网络连接。

* 在虚拟网络中创建 API 管理服务：使用 cmdlet [New-AzApiManagement](/powershell/module/az.apimanagement/new-azapimanagement) 在虚拟网络中创建 Azure API 管理服务，并将其配置为使用内部虚拟网络类型。

* 在虚拟网络中更新 API 管理服务的现有部署：使用 cmdlet [Update-AzApiManagementRegion](/powershell/module/az.apimanagement/update-azapimanagementregion) 将现有 API 管理服务移到虚拟网络内，并将其配置为使用内部虚拟网络类型。

## <a name="apim-dns-configuration"></a>DNS 配置
如果 API 管理采用外部虚拟网络模式，则 DNS 由 Azure 管理。 使用内部虚拟网络模式时，必须管理自己的路由。

> [!NOTE]
> API 管理服务不会侦听来自 IP 地址的请求， 它只响应到发往其服务终结点上配置的主机名的请求。 这些终结点包括网关、Azure 门户和开发人员门户、直接管理终结点和 Git。

### <a name="access-on-default-host-names"></a>基于默认主机名的访问权限
创建 API 管理服务（例如“contosointernalvnet”）时，将默认配置以下服务终结点：

   * 网关或代理：contosointernalvnet.azure-api.net

   * Azure 门户和开发人员门户：contosointernalvnet.portal.azure-api.net

   * 直接管理终结点：contosointernalvnet.management.azure-api.net

   * Git：contosointernalvnet.scm.azure-api.net

若要访问这些 API 管理服务终结点，可以在连接到虚拟网络（其中部署了 API 管理）的子网中创建虚拟机。 假设服务的内部虚拟 IP 地址为 10.1.0.5，则可映射 hosts 文件 (%SystemDrive%\drivers\etc\hosts)，如下所示：

   * 10.1.0.5     contosointernalvnet.azure-api.net

   * 10.1.0.5     contosointernalvnet.portal.azure-api.net

   * 10.1.0.5     contosointernalvnet.management.azure-api.net

   * 10.1.0.5     contosointernalvnet.scm.azure-api.net

然后即可从创建的虚拟机访问所有服务终结点。
如果在虚拟网络中使用自定义 DNS 服务器，则还可创建 DNS 记录并从虚拟网络中的任何位置访问这些终结点。

### <a name="access-on-custom-domain-names"></a>基于自定义域名的访问权限

1. 如果不想通过默认主机名访问 API 管理服务，则可为所有服务终结点设置自定义域名，如下图所示：

   ![为 API 管理设置自定义域][api-management-custom-domain-name]

2. 然后即可在用于访问终结点的 DNS 服务器中创建记录，这些终结点只能从虚拟网络内部访问。

## <a name="routing"> </a> 路由

* 负载平衡*专用*子网范围中的虚拟 IP 地址将保留，用于访问虚拟网络中的从 API 管理服务终结点。 这*专用*IP 地址可以在 Azure 门户中的服务的概述边栏选项卡上找到。 此地址必须通过使用虚拟网络的 DNS 服务器注册。
* 负载平衡*公共*也将保留 IP 地址 (VIP) 来提供通过端口 3443 访问管理服务终结点。 这*公共*IP 地址可以在 Azure 门户中的服务的概述边栏选项卡上找到。 *公共*IP 地址仅用于控制平面流向`management`终结点通过端口 3443 和可以向下锁定[ApiManagement] [ ServiceTags] servicetag.
* 从子网 IP 范围 (DIP) 的 IP 地址将分配给该服务的每个 VM，并将用于访问虚拟网络中的资源。 公共 IP 地址 (VIP) 将用于访问虚拟网络外部的资源。 如果 IP 限制列表用于保护虚拟网络中的资源，其中 API 管理服务部署必须子网的整个范围指定来授予或限制来自服务的访问。
* 负载均衡公共和专用 IP 地址可以在 Azure 门户中概述边栏选项卡上找到。
* 如果从中删除，然后重新添加到虚拟网络服务，可能会更改为公共和私有访问分配的 IP 地址。 如果发生这种情况，可能需要更新 DNS 注册、 路由规则和虚拟网络中的 IP 限制列表。

## <a name="related-content"></a>相关内容
若要了解更多信息，请参阅下列文章：
* [在虚拟网络中设置 Azure API 管理时的常见网络配置问题][Common network configuration problems]
* [虚拟网络常见问题解答](../virtual-network/virtual-networks-faq.md)
* [在 DNS 中创建记录](/previous-versions/windows/it-pro/windows-2000-server/bb727018(v=technet.10))

[api-management-using-internal-vnet-menu]: ./media/api-management-using-with-internal-vnet/api-management-using-with-internal-vnet.png
[api-management-internal-vnet-dashboard]: ./media/api-management-using-with-internal-vnet/api-management-internal-vnet-dashboard.png
[api-management-custom-domain-name]: ./media/api-management-using-with-internal-vnet/api-management-custom-domain-name.png

[Create API Management service]: get-started-create-service-instance.md
[Common network configuration problems]: api-management-using-with-vnet.md#network-configuration-issues

[ServiceTags]: ../virtual-network/security-overview.md#service-tags

