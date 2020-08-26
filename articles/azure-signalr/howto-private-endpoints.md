---
title: 使用专用终结点
titleSuffix: Azure SignalR Service
description: 用于从虚拟网络安全访问 Azure SignalR 服务的专用终结点的概述。
services: signalr
author: ArchangelSDY
ms.service: signalr
ms.topic: article
ms.date: 05/06/2020
ms.author: dayshen
ms.openlocfilehash: 645b2c643c1c1d4fe82eb5998a35ccc48536603e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84302139"
---
# <a name="use-private-endpoints-for-azure-signalr-service"></a>使用 Azure SignalR 服务的专用终结点

你可以使用 Azure SignalR 服务的[专用终结点](../private-link/private-endpoint-overview.md)，以允许虚拟网络（VNet）上的客户端通过[专用链接](../private-link/private-link-overview.md)安全地访问数据。 专用终结点使用 Azure SignalR 服务的 VNet 地址空间中的 IP 地址。 VNet 和 Azure SignalR Service 上的客户端之间的网络流量通过 Microsoft 主干网络上的专用链接进行遍历，从而消除了公共 internet 的泄露。

使用 Azure SignalR 服务的专用终结点，可以：

- 使用网络访问控制保护 Azure SignalR 服务，阻止 Azure SignalR 服务的公共终结点上的所有连接。
- 通过使你能够阻止渗透来自 VNet 的数据，提高虚拟网络（VNet）的安全性。
- 从本地网络安全连接到 Azure SignalR 服务，这些网络使用[VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md)或[ExpressRoutes](../expressroute/expressroute-locations.md)与专用对等互连连接到 VNet。

## <a name="conceptual-overview"></a>概念概述

![Azure SignalR 服务的专用终结点概述](media/howto-private-endpoints/private-endpoint-overview.png)

专用终结点是[虚拟网络](../virtual-network/virtual-networks-overview.md)（VNet）中 Azure 服务的特殊网络接口。 当你创建 Azure SignalR 服务的专用终结点时，它会在 VNet 和你的服务上的客户端之间提供安全连接。 从 VNet 的 IP 地址范围为专用终结点分配 IP 地址。 专用终结点与 Azure SignalR 服务之间的连接使用安全的专用链接。

VNet 中的应用程序可以**使用相同的连接字符串和要使用的授权机制**，以无缝方式通过专用终结点连接到 Azure SignalR 服务。 专用终结点可用于 Azure SignalR 服务支持的所有协议，其中包括 REST API。

在 VNet 中创建 Azure SignalR 服务的专用终结点时，会将同意请求发送到 Azure SignalR 服务所有者进行审批。 如果请求创建专用终结点的用户也是 Azure SignalR 服务的所有者，则会自动批准此同意请求。

Azure SignalR 服务所有者可以通过[Azure 门户](https://portal.azure.com)中 Azure SignalR 服务的 "*专用终结点*" 选项卡来管理同意请求和专用终结点。

> [!TIP]
> 如果要仅通过专用终结点限制对 Azure SignalR 服务的访问，请[将网络访问控制配置](howto-network-access-control.md#managing-network-access-control)为拒绝或通过公共终结点控制访问权限。

### <a name="connecting-to-private-endpoints"></a>连接到专用终结点

当客户端连接到公共终结点时，使用专用终结点的 VNet 中的客户端应使用与 Azure SignalR 服务相同的连接字符串。 我们依赖 DNS 解析，通过专用链接自动将来自 VNet 的连接路由到 Azure SignalR 服务。

> [!IMPORTANT]
> 使用相同的连接字符串连接到使用专用终结点的 Azure SignalR 服务，否则将使用此连接字符串。 请不要使用其子域 URL 连接到 Azure SignalR 服务 `privatelink` 。

默认情况下，我们创建附加到 VNet 的[专用 DNS 区域](../dns/private-dns-overview.md)，其中包含专用终结点的必要更新。 但是，如果使用自己的 DNS 服务器，则可能需要对 DNS 配置进行其他更改。 以下[DNS 更改](#dns-changes-for-private-endpoints)部分介绍了专用终结点所需的更新。

## <a name="dns-changes-for-private-endpoints"></a>专用终结点的 DNS 更改

创建专用终结点时，会将 Azure SignalR 服务的 DNS CNAME 资源记录更新为具有前缀的子域中的别名 `privatelink` 。 默认情况下，我们还会创建一个[专用 dns 区域](../dns/private-dns-overview.md)，该区域对应于 `privatelink` 子域，其中包含用于专用终结点的 DNS a 资源记录。

当你通过专用终结点从 VNet 外部解析 Azure SignalR 服务域名时，它将解析为 Azure SignalR 服务的公共终结点。 当从承载专用终结点的 VNet 解析时，域名解析为专用终结点的 IP 地址。

对于上面所示的示例，Azure SignalR 服务 "foobar" 的 DNS 资源记录在托管专用终结点的 VNet 之外进行解析时将为：

| 名称                                                  | 类型  | Value                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``foobar.service.signalr.net``                        | CNAME | ``foobar.privatelink.service.signalr.net``            |
| ``foobar.privatelink.service.signalr.net``            | A     | \<Azure SignalR Service public IP address\>           |

如前所述，你可以通过使用网络访问控制的公共终结点，拒绝或控制对 VNet 外部客户端的访问。

"Foobar" 的 DNS 资源记录（由承载专用终结点的 VNet 中的客户端解析时）将为：

| 名称                                                  | 类型  | Value                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``foobar.service.signalr.net``                        | CNAME | ``foobar.privatelink.service.signalr.net``            |
| ``foobar.privatelink.service.signalr.net``            | A     | 10.1.1.5                                              |

此方法可让你**使用相同的连接字符串**访问 Azure SignalR 服务，该客户端位于承载专用终结点的 VNet 和 vnet 外部的客户端。

如果在网络上使用自定义 DNS 服务器，则客户端必须能够将 Azure SignalR 服务终结点的 FQDN 解析到专用终结点 IP 地址。 应将 DNS 服务器配置为将专用链接子域委托给 VNet 的专用 DNS 区域，或 `foobar.privatelink.service.signalr.net` 使用专用终结点 IP 地址来配置 A 记录。

> [!TIP]
> 使用自定义或本地 DNS 服务器时，应将 DNS 服务器配置为将子域中的 Azure SignalR 服务名称解析 `privatelink` 到专用终结点 IP 地址。 为此，可以将子域委托 `privatelink` 给 VNet 的专用 DNS 区域，或在 dns 服务器上配置 dns 区域并添加 Dns A 记录。

建议用于 Azure SignalR 服务的专用终结点的 DNS 区域名称是： `privatelink.service.signalr.net` 。

有关配置自己的 DNS 服务器以支持专用终结点的详细信息，请参阅以下文章：

- [Azure 虚拟网络中资源的名称解析](/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#name-resolution-that-uses-your-own-dns-server)
- [专用终结点的 DNS 配置](/azure/private-link/private-endpoint-overview#dns-configuration)

## <a name="create-a-private-endpoint"></a>创建专用终结点

### <a name="create-a-private-endpoint-along-with-a-new-azure-signalr-service-in-the-azure-portal"></a>在 Azure 门户中创建专用终结点以及新的 Azure SignalR 服务

1. 创建新的 Azure SignalR 服务时，选择 "**网络**" 选项卡。选择**专用终结点**作为连接方法。

    ![创建 Azure SignalR 服务-网络选项卡](media/howto-private-endpoints/portal-create-blade-networking-tab.png)

1. 单击“添加” 。 填写新专用终结点的订阅、资源组、位置和名称。 选择虚拟网络和子网。

    ![创建 Azure SignalR 服务-添加专用终结点](media/howto-private-endpoints/portal-create-blade-add-private-endpoint.png)

1. 单击“查看 + 创建”。

### <a name="create-a-private-endpoint-for-an-existing-azure-signalr-service-in-the-azure-portal"></a>在 Azure 门户中创建现有 Azure SignalR 服务的专用终结点

1. 请参阅 Azure SignalR 服务。

1. 单击名为 "**专用终结点连接**" 的 "设置" 菜单。

1. 单击顶部的 " **+ 专用终结点**" 按钮。

    ![专用终结点连接边栏选项卡](media/howto-private-endpoints/portal-private-endpoint-connections-blade.png)

1. 填写新专用终结点的订阅、资源组、资源名称和区域。
    
    ![创建专用终结点-基础](media/howto-private-endpoints/portal-create-private-endpoint-basics.png)

1. 选择 "目标 Azure SignalR 服务资源"。

    ![创建专用终结点-资源](media/howto-private-endpoints/portal-create-private-endpoint-resource.png)

1. 选择目标虚拟网络

    ![创建专用终结点-配置](media/howto-private-endpoints/portal-create-private-endpoint-configuration.png)

1. 单击“查看 + 创建”。

### <a name="create-a-private-endpoint-using-azure-cli"></a>使用 Azure CLI 创建专用终结点

1. 登录到 Azure CLI
    ```console
    az login
    ```
1. 选择 Azure 订阅
    ```console
    az account set --subscription {AZURE SUBSCRIPTION ID}
    ```
1. 创建新的资源组
    ```console
    az group create -n {RG} -l {AZURE REGION}
    ```
1. 将 SignalRService 注册为提供商
    ```console
    az provider register -n Microsoft.SignalRService
    ```
1. 创建新的 Azure SignalR 服务
    ```console
    az signalr create --name {NAME} --resource-group {RG} --location {AZURE REGION} --sku Standard_S1
    ```
1. 创建虚拟网络
    ```console
    az network vnet create --resource-group {RG} --name {vNet NAME} --location {AZURE REGION}
    ```
1. 添加子网
    ```console
    az network vnet subnet create --resource-group {RG} --vnet-name {vNet NAME} --name {subnet NAME} --address-prefixes {addressPrefix}
    ```
1. 禁用虚拟网络策略
    ```console
    az network vnet subnet update --name {subnet NAME} --resource-group {RG} --vnet-name {vNet NAME} --disable-private-endpoint-network-policies true
    ```
1. 添加专用 DNS 区域
    ```console
    az network private-dns zone create --resource-group {RG} --name privatelink.service.signalr.net
    ```
1. 将专用 DNS 区域链接到虚拟网络
    ```console
    az network private-dns link vnet create --resource-group {RG} --virtual-network {vNet NAME} --zone-name privatelink.service.signalr.net --name {dnsZoneLinkName} --registration-enabled true
    ```
1. 创建专用终结点（自动批准）
    ```console
    az network private-endpoint create --resource-group {RG} --vnet-name {vNet NAME} --subnet {subnet NAME} --name {Private Endpoint Name}  --private-connection-resource-id "/subscriptions/{AZURE SUBSCRIPTION ID}/resourceGroups/{RG}/providers/Microsoft.SignalRService/SignalR/{NAME}" --group-ids signalr --connection-name {Private Link Connection Name} --location {AZURE REGION}
    ```
1. 创建专用终结点（手动请求批准）
    ```console
    az network private-endpoint create --resource-group {RG} --vnet-name {vNet NAME} --subnet {subnet NAME} --name {Private Endpoint Name}  --private-connection-resource-id "/subscriptions/{AZURE SUBSCRIPTION ID}/resourceGroups/{RG}/providers/Microsoft.SignalRService/SignalR/{NAME}" --group-ids signalr --connection-name {Private Link Connection Name} --location {AZURE REGION} --manual-request
    ```
1. 显示连接状态
    ```console
    az network private-endpoint show --resource-group {RG} --name {Private Endpoint Name}
    ```

## <a name="pricing"></a>定价

有关定价详细信息，请参阅 [Azure 专用链接定价](https://azure.microsoft.com/pricing/details/private-link)。

## <a name="known-issues"></a>已知问题

请记住以下有关 Azure SignalR 服务专用终结点的已知问题

### <a name="free-tier"></a>免费层

无法为免费层 Azure SignalR 服务创建任何专用终结点。

### <a name="access-constraints-for-clients-in-vnets-with-private-endpoints"></a>具有专用终结点的 Vnet 中客户端的访问约束

Vnet 中具有现有专用终结点的客户端在访问具有专用终结点的其他 Azure SignalR 服务实例时面临约束。 例如，假设 VNet N1 具有 Azure SignalR 服务实例 S1 的专用终结点。 如果 Azure SignalR Service S2 在 VNet N2 中有专用终结点，则 VNet N1 中的客户端还必须使用专用终结点访问 Azure SignalR 服务 S2。 如果 Azure SignalR 服务 S2 没有任何专用终结点，则 VNet N1 中的客户端无需专用终结点即可访问该帐户中的 Azure SignalR 服务。

此约束是在 Azure SignalR Service S2 创建专用终结点时所做的 DNS 更改的结果。

### <a name="network-security-group-rules-for-subnets-with-private-endpoints"></a>专用终结点所在子网的网络安全组规则

目前，无法为专用终结点配置[网络安全组](../virtual-network/security-overview.md)（NSG）规则和用户定义的路由。 应用于托管专用终结点的子网的 NSG 规则应用到专用终结点。 此问题的一种有限的解决方法是在源子网中实现专用终结点的访问规则，不过，这种方法可能需要更高的管理开销。

## <a name="next-steps"></a>后续步骤

- [配置网络访问控制](howto-network-access-control.md)
