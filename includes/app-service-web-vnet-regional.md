---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 04/15/2020
ms.author: ccompy
ms.openlocfilehash: f7208307df51ecefb76f9adaedea59b327cdc19e
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2020
ms.locfileid: "81604879"
---
使用区域 VNet 集成使应用可以访问：

* 与应用位于同一区域的 VNet 中的资源。
* VNet 中的资源与应用集成的 VNet 进行对等。
* 服务终结点保护服务。
* 跨 Azure 快速路由连接的资源。
* 您要集成的 VNet 中的资源。
* 跨对等连接的资源，其中包括 Azure 快速路由连接。
* 专用终结点 

在同一区域中使用 VNet 集成时，可以使用以下 Azure 网络功能：

* **网络安全组 （NSG）：** 您可以使用放置在集成子网上的 NSG 来阻止出站流量。 入站规则不适用，因为无法使用 VNet 集成来提供对应用的入站访问权限。
* **路由表 （UDR）：** 您可以在集成子网上放置路由表，以便将出站流量发送到所需的位置。

默认情况下，你的应用仅将 RFC1918 流量路由到 VNet 中。 如果要将所有出站流量路由到 VNet 中，请应用应用应用WEBSITE_VNET_ROUTE_ALL应用。 要配置应用设置：

1. 转到应用门户中的 **"配置**UI"。 选择“新应用程序设置”****。
1. 在 **"名称**"框中输入**WEBSITE_VNET_ROUTE_ALL，** 并在 **"值"** 框中输入**1。**

   ![提供应用程序设置][4]

1. 选择“确定”  。
1. 选择“保存”。 

如果将所有出站流量路由到 VNet，则受应用于集成子网的 NSG 和 UDR 的约束。 当您将所有出站流量路由到 VNet 时，您的出站地址仍然是应用属性中列出的出站地址，除非您提供路由将流量发送到其他位置。

在同一区域中使用 VNet 集成与 VNet 存在一些限制：

* 无法跨全局对等互连连接到达资源。
* 此功能仅从支持高级 V2 应用服务计划的较新的 Azure 应用服务扩展单位提供。
* 集成子网只能由一个应用服务计划使用。
* 此功能不能由应用服务环境中的隔离计划应用使用。
* 此功能需要一个未使用的子网，该子网是具有 32 个地址或 Azure 资源管理器 VNet 中更大的 /27。
* 应用和 VNet 必须位于同一区域中。
* 不能删除具有集成应用的 VNet。 删除 VNet 之前删除集成。
* 您只能与 VNet 集成在与应用相同的订阅中。
* 每个应用服务计划只能有一个区域 VNet 集成。 同一应用服务计划中的多个应用可以使用相同的 VNet。
* 当有一个应用使用区域 VNet 集成时，不能更改应用或计划的订阅。
* 如果不进行配置更改，你的应用无法解决 Azure DNS 专用区域中的地址

每个计划实例使用一个地址。 如果将应用缩放为五个实例，则使用五个地址。 由于子网大小在分配后无法更改，因此必须使用足够大的子网，以适应应用可能达到的任何比例。 建议的尺寸是带 64 个地址的 /26。 带 64 个地址的 /26 适合包含 30 个实例的高级计划。 向上或向下扩展计划时，需要在短时间内使用两倍的地址。

如果希望其他计划中的应用访问已由另一个计划中的应用连接到的 VNet，请选择与预先存在的 VNet 集成所使用的子网不同的子网。

此功能在 Linux 预览中。 该功能的 Linux 形式仅支持对 RFC 1918 地址（10.0.0.0/8、172.16.0.0/12、192.168.0.0/16） 进行调用。

### <a name="web-or-function-app-for-containers"></a>容器的 Web 或功能应用

如果您在 Linux 上使用内置映像托管应用，则区域 VNet 集成无需其他更改即可工作。 如果对容器使用 Web 或函数应用，则必须修改 Docker 映像才能使用 VNet 集成。 在 docker 映像中，使用 PORT 环境变量作为主 Web 服务器的侦听端口，而不是使用硬编码端口号。 PORT 环境变量由平台在容器启动时自动设置。 如果使用 SSH，则必须将 SSH 守护进程配置为在使用区域 VNet 集成时侦听 SSH_PORT 环境变量指定的端口号。 在 Linux 上不支持网关要求的 VNet 集成。

### <a name="service-endpoints"></a>服务终结点

区域 VNet 集成使您能够使用服务终结点。 要将服务终结点与应用一起使用，请使用区域 VNet 集成连接到选定的 VNet，然后使用用于集成的子网上的目标服务配置服务终结点。 如果然后想要通过服务终结点访问服务：

1. 配置与 Web 应用的区域 VNet 集成
1. 转到目标服务，并针对用于集成的子网配置服务终结点

### <a name="network-security-groups"></a>网络安全组

您可以使用网络安全组来阻止 VNet 中资源的入站和出站流量。 使用区域 VNet 集成的应用可以使用[网络安全组][VNETnsg]来阻止向外出流量到 VNet 或 Internet 中的资源。 要阻止公共地址的流量，必须将应用程序设置WEBSITE_VNET_ROUTE_ALL设置为 1。 NSG 中的入站规则不适用于你的应用，因为 VNet 集成仅影响应用中的出站流量。

要控制应用的入站流量，请使用"访问限制"功能。 应用于集成子网的 NSG 都有效，无论应用于集成子网的任何路由如何。 如果WEBSITE_VNET_ROUTE_ALL设置为 1，并且集成子网上没有任何影响公共地址流量的路由，则所有出站流量仍受分配给集成子网的 NSG 的约束。 如果未设置WEBSITE_VNET_ROUTE_ALL，则 NSG 仅应用于 RFC1918 流量。

### <a name="routes"></a>路由

您可以使用路由表将出站流量从应用路由到所需的任何位置。 默认情况下，路由表仅影响 RFC1918 目标流量。 如果将WEBSITE_VNET_ROUTE_ALL设置为 1，则所有出站呼叫都受到影响。 在集成子网上设置的路由不会影响对入站应用请求的回复。 常见目标可以包括防火墙设备或网关。

如果要路由本地所有出站流量，可以使用路由表将所有出站流量发送到 ExpressRoute 网关。 如果确实将流量路由到网关，请确保在外部网络中设置路由以发送回任何答复。

边境网关协议 （BGP） 路由也会影响应用流量。 如果您有来自快速路由网关等内容的 BGP 路由，则应用出站流量将受到影响。 默认情况下，BGP 路由仅影响您的 RFC1918 目标流量。 如果WEBSITE_VNET_ROUTE_ALL设置为 1，则所有出站流量都可能受 BGP 路由的影响。

### <a name="azure-dns-private-zones"></a>Azure DNS 专用区域 

应用与 VNet 集成后，它将使用 VNet 配置的相同 DNS 服务器。 默认情况下，你的应用将不能与 Azure DNS 专用区域配合使用。 要使用 Azure DNS 专用区域，您需要添加以下应用设置：

1. 值 168.63.129.16 的WEBSITE_DNS_SERVER 
1. 值 1 的WEBSITE_VNET_ROUTE_ALL

除了使应用能够使用 Azure DNS 专用区域外，这些设置还将将应用的所有出站呼叫发送到 VNet。

### <a name="private-endpoints"></a>专用终结点

如果要对[专用终结点][privateendpoints]进行调用，则需要与 Azure DNS 专用区域集成，或者管理应用使用的 DNS 服务器中的专用终结点。 

<!--Image references-->
[4]: ../includes/media/web-sites-integrate-with-vnet/vnetint-appsetting.png

<!--Links-->
[VNETnsg]: https://docs.microsoft.com/azure/virtual-network/security-overview/
[privateendpoints]: https://docs.microsoft.com/azure/app-service/networking/private-endpoint
