---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 02/27/2020
ms.author: ccompy
ms.openlocfilehash: c731e2a7d4da1a66aabb50b335d526fbb6a0a302
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78671542"
---
通过使用区域 VNet 集成，你的应用程序可以访问：

* VNet 中与你集成的同一区域中的资源 
* Vnet 对等互连中的资源位于同一区域中的 VNet
* 服务终结点保护服务
* 跨 ExpressRoute 连接的资源
* 你连接到的 VNet 中的资源
* 跨对等互连连接（包括 ExpressRoute 连接）的资源
* 专用终结点 

在同一区域中使用 VNet 与 Vnet 的集成时，可以使用以下 Azure 网络功能：

* 网络安全组（Nsg）-可以使用放置在集成子网上的网络安全组来阻止出站流量。 入站规则不适用于，因为你无法使用 VNet 集成提供对应用的入站访问权限。
* 路由表（Udr）-可以将路由表放置在集成子网上，以便发送出站流量。 

默认情况下，应用仅将 RFC1918 流量路由到 VNet。 如果要将所有出站流量路由到 VNet，请将应用设置 WEBSITE_VNET_ROUTE_ALL 应用到应用。 配置应用设置：

1. 在应用门户中转到配置 UI。 选择**新应用程序设置**
1. 在 "名称" 字段中键入 " **WEBSITE_VNET_ROUTE_ALL** ，在" 值 "字段中键入**1**

   ![提供应用程序设置][4]

1. 选择“确定”
1. 选择“保存”

如果将所有出站流量路由到 VNet，则会受到应用于集成子网的 Nsg 和 Udr 的限制。 当你将所有出站流量路由到 VNet 中时，你的出站地址仍将是应用程序属性中列出的出站地址，除非你提供将流量发送到其他位置的路由。 

在同一区域中使用 VNet 与 Vnet 的集成存在一些限制：

* 不能跨全局对等连接访问资源
* 此功能仅适用于支持 PremiumV2 应用服务计划的更新的应用服务缩放单位。
* 集成子网仅可由一个应用服务计划使用
* 此功能不能由处于应用服务环境中的独立计划应用使用
* 此功能需要一个不使用的子网，该子网为/27，其中包含32地址或更大的资源管理器 VNet
* 应用和 VNet 必须位于同一区域中
* 不能删除带有集成应用的 VNet。 请在删除 VNet 之前删除集成。 
* 只能与应用程序在同一订阅中集成 Vnet
* 对于每个应用服务计划，只能有一个区域 VNet 集成。 同一应用服务计划中的多个应用可以使用相同的 VNet。 
* 当存在使用区域 VNet 集成的应用时，不能更改应用或计划的订阅

每个计划实例使用一个地址。 如果将应用扩展到五个实例，则使用5个地址。 由于在分配后无法更改子网大小，因此你必须使用足够大的子网来容纳你的应用程序可能会达到的任何规模。 建议大小为/26，其中包含64地址。 带有64地址的/26 将容纳包含30个实例的高级计划。 向上或向下缩放计划时，需要在短时间内使用两个地址。 

如果你希望在其他计划中的应用程序连接到已由其他计划中的应用程序连接的 VNet，则需要选择与预先存在的 VNet 集成所使用的子网不同的子网。  

此功能在 Linux 中处于预览阶段。 此功能的 Linux 格式仅支持调用 RFC 1918 地址（10.0.0.0/8、172.16.0.0/12、192.168.0.0/16）。

### <a name="web--function-app-for-containers"></a>容器的 Web/Function App

如果在 Linux 上托管内置映像的应用，则区域 VNet 集成无需进行其他更改即可工作。 如果为容器使用 Web/Function App，则需要修改 docker 映像才能使用 VNet 集成。 在 docker 映像中，使用端口环境变量作为主 web 服务器的侦听端口，而不是使用硬编码的端口号。 平台会在容器启动时自动设置端口环境变量。 如果你使用的是 SSH，则必须将 SSH 守护程序配置为侦听 SSH_PORT 环境变量在使用区域 VNet 集成时指定的端口号。  在 Linux 上，不支持网关必需的 VNet 集成。 

### <a name="service-endpoints"></a>服务终结点

通过区域 VNet 集成，可以使用服务终结点。  若要将服务终结点与应用程序一起使用，请使用区域 VNet 集成连接到选定的 VNet，然后在用于集成的子网中配置服务终结点。 

### <a name="network-security-groups"></a>网络安全组

网络安全组使你能够阻止到 VNet 中资源的入站和出站流量。 使用区域 VNet 集成的应用可以使用[网络安全组][VNETnsg]来阻止到 VNet 或 internet 中的资源的出站流量。 若要阻止流量发送到公共地址，则必须将应用程序设置 WEBSITE_VNET_ROUTE_ALL 设置为1。 NSG 中的入站规则不适用于应用，因为 VNet 集成仅影响应用的出站流量。 若要控制应用的入站流量，请使用访问限制功能。 适用于你的集成子网的 NSG 将生效，而与任何应用到集成子网的路由无关。 如果 WEBSITE_VNET_ROUTE_ALL 设置为1，并且你的集成子网上没有任何影响公共地址流量的路由，则所有的出站流量仍需服从 Nsg 分配给集成子网。 如果 WEBSITE_VNET_ROUTE_ALL 未设置，则 Nsg 将仅应用于 RFC1918 的流量。

### <a name="routes"></a>路由

使用路由表，你可以将应用程序的出站流量路由到所需的任何位置。 默认情况下，路由表只会影响 RFC1918 目标流量。  如果将 WEBSITE_VNET_ROUTE_ALL 设置为1，则将影响所有出站调用。 在集成子网上设置的路由不会影响对入站应用请求的答复。 常见目标可以包括防火墙设备或网关。 如果要将所有出站流量路由到本地，可以使用路由表将所有出站流量发送到 ExpressRoute 网关。 如果你确实要将流量路由到网关，请确保在外部网络中设置路由以发送任何回复。

边界网关协议（BGP）路由也会影响应用程序流量。 如果你有来自 ExpressRoute 网关之类的 BGP 路由，你的应用程序出站流量将受到影响。 默认情况下，BGP 路由只会影响 RFC1918 目标流量。 如果 WEBSITE_VNET_ROUTE_ALL 设置为1，则可能会受到 BGP 路由影响的所有出站流量。 


<!--Image references-->
[4]: ../includes/media/web-sites-integrate-with-vnet/vnetint-appsetting.png

<!--Links-->
[VNETnsg]: https://docs.microsoft.com/azure/virtual-network/security-overview/