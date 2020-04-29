---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 04/15/2020
ms.author: ccompy
ms.openlocfilehash: f7208307df51ecefb76f9adaedea59b327cdc19e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81604879"
---
通过使用区域 VNet 集成，你的应用程序可以访问：

* VNet 中与你的应用程序位于同一区域中的资源。
* Vnet 对等互连中的资源与应用集成到的 VNet。
* 服务终结点保护的服务。
* 跨 Azure ExpressRoute 连接的资源。
* 与集成的 VNet 中的资源。
* 跨对等互连连接的资源，其中包括 Azure ExpressRoute 连接。
* 专用终结点 

在同一区域中使用 VNet 与 Vnet 的集成时，可以使用以下 Azure 网络功能：

* **网络安全组（nsg）**：可以使用放置在集成子网上的 NSG 阻止出站流量。 由于无法使用 VNet 集成来提供应用程序的入站访问权限，因此不会应用入站规则。
* **路由表（udr）**：可以将路由表放置在集成子网上，以便发送出站流量。

默认情况下，应用仅将流量路由到 VNet 中。 如果要将所有出站流量路由到 VNet，请将应用设置 WEBSITE_VNET_ROUTE_ALL 应用到应用。 配置应用设置：

1. 在应用门户中转到**配置**UI。 选择“新应用程序设置”****。
1. 在 "**名称**" 框中输入**WEBSITE_VNET_ROUTE_ALL** ，然后在 "**值**" 框中输入**1** 。

   ![提供应用程序设置][4]

1. 选择“确定”  。
1. 选择“保存”。 

如果将所有出站流量路由到 VNet，则会受到应用于集成子网的 Nsg 和 Udr 的限制。 当你将所有出站流量路由到 VNet 中时，你的出站地址仍是你的应用程序属性中列出的出站地址，除非你提供将流量发送到其他位置的路由。

在同一区域中使用 VNet 与 Vnet 的集成存在一些限制：

* 不能跨全局对等互连连接访问资源。
* 此功能仅可用于支持 PremiumV2 应用服务计划的更新的 Azure App Service 缩放单位。
* 集成子网只能由一个应用服务计划使用。
* 此功能不能由处于应用服务环境中的独立计划应用程序使用。
* 此功能需要一个在 Azure 资源管理器 VNet 中具有32地址或更大地址的未使用的子网。
* 应用和 VNet 必须位于同一区域。
* 不能删除具有集成应用的 VNet。 请在删除 VNet 之前删除该集成。
* 只能将与应用程序在同一订阅中的 Vnet 集成。
* 对于每个应用服务计划，只能有一个区域 VNet 集成。 同一应用服务计划中的多个应用可以使用相同的 VNet。
* 当存在使用区域 VNet 集成的应用时，不能更改应用或计划的订阅。
* 应用无法在不进行配置更改的 Azure DNS 专用区域中解析地址

每个计划实例使用一个地址。 如果将应用扩展到五个实例，则使用5个地址。 由于在分配后无法更改子网大小，因此你必须使用足够大的子网来容纳你的应用程序可能会达到的任何规模。 建议大小为/26，其中包含64地址。 带有64地址的/26 适用于具有30个实例的高级计划。 向上或向下缩放计划时，需要在短时间内使用两个地址。

如果你希望你的应用程序在另一个计划中连接到已连接到的 VNet，请选择与预先存在的 VNet 集成所使用的子网不同的子网。

此功能在 Linux 中处于预览阶段。 此功能的 Linux 格式仅支持调用 RFC 1918 地址（10.0.0.0/8、172.16.0.0/12、192.168.0.0/16）。

### <a name="web-or-function-app-for-containers"></a>用于容器的 Web 或 Function App

如果在 Linux 上托管内置映像的应用，则区域 VNet 集成无需进行其他更改即可工作。 如果为容器使用 Web 或 Function App，则必须修改 docker 映像才能使用 VNet 集成。 在 docker 映像中，使用端口环境变量作为主 web 服务器的侦听端口，而不是使用硬编码的端口号。 平台会在容器启动时自动设置端口环境变量。 如果使用 SSH，则必须将 SSH 守护程序配置为在使用区域 VNet 集成时侦听 SSH_PORT 环境变量指定的端口号。 在 Linux 上，不支持网关必需的 VNet 集成。

### <a name="service-endpoints"></a>服务终结点

通过区域 VNet 集成，可以使用服务终结点。 若要将服务终结点与应用程序配合使用，请使用区域 VNet 集成连接到所选的 VNet，然后在用于集成的子网上使用目标服务配置服务终结点。 如果你想要通过服务终结点访问服务：

1. 配置区域 VNet 与你的 web 应用的集成
1. 中转到目标服务，并根据用于集成的子网配置服务终结点

### <a name="network-security-groups"></a>网络安全组

可以使用网络安全组阻止到 VNet 中资源的入站和出站流量。 使用区域 VNet 集成的应用可以使用[网络安全组][VNETnsg]来阻止到 VNet 或 internet 中的资源的出站流量。 若要阻止流量发送到公共地址，则必须将应用程序设置 WEBSITE_VNET_ROUTE_ALL 设置为1。 NSG 中的入站规则不适用于应用，因为 VNet 集成仅影响来自应用的出站流量。

若要控制应用的入站流量，请使用访问限制功能。 应用于集成子网的 NSG 无论应用到集成子网的路由如何都有效。 如果将 WEBSITE_VNET_ROUTE_ALL 设置为1，并且没有任何影响集成子网上的公共地址流量的路由，则所有出站流量仍受 Nsg 分配到集成子网。 如果未设置 WEBSITE_VNET_ROUTE_ALL，则 Nsg 仅适用于 RFC1918 流量。

### <a name="routes"></a>路由

你可以使用路由表将应用中的出站流量路由到所需的任何位置。 默认情况下，路由表仅影响 RFC1918 目标流量。 如果将 WEBSITE_VNET_ROUTE_ALL 设置为1，则将影响所有出站呼叫。 在集成子网上设置的路由不会影响对入站应用请求的答复。 常见目标可以包括防火墙设备或网关。

如果要将所有出站流量路由到本地，可以使用路由表将所有出站流量发送到 ExpressRoute 网关。 如果你确实要将流量路由到网关，请确保在外部网络中设置路由以发送任何回复。

边界网关协议（BGP）路由也会影响应用程序流量。 如果你有来自 ExpressRoute 网关之类的 BGP 路由，你的应用程序出站流量将受到影响。 默认情况下，BGP 路由仅影响 RFC1918 目标流量。 如果 WEBSITE_VNET_ROUTE_ALL 设置为1，则可能会受到 BGP 路由的影响的所有出站流量。

### <a name="azure-dns-private-zones"></a>Azure DNS 专用区域 

在应用程序与 VNet 集成后，它将使用与 VNet 相同的 DNS 服务器。 默认情况下，你的应用程序将不能使用 Azure DNS 专用区域。 若要使用 Azure DNS 专用区域需要添加以下应用设置：

1. 168.63.129.16 值的 WEBSITE_DNS_SERVER 
1. 值为1的 WEBSITE_VNET_ROUTE_ALL

除了允许应用使用 Azure DNS 专用区域外，这些设置还会将你的应用中的所有出站呼叫发送到 VNet。

### <a name="private-endpoints"></a>专用终结点

如果要调用[专用终结点][privateendpoints]，则需要将与 Azure DNS 专用区域集成，或管理应用所使用的 DNS 服务器中的专用终结点。 

<!--Image references-->
[4]: ../includes/media/web-sites-integrate-with-vnet/vnetint-appsetting.png

<!--Links-->
[VNETnsg]: https://docs.microsoft.com/azure/virtual-network/security-overview/
[privateendpoints]: https://docs.microsoft.com/azure/app-service/networking/private-endpoint
