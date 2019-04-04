---
title: Azure 应用程序网关的工作原理
description: 本文提供有关应用程序网关工作原理的信息
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 02/20/2019
ms.author: absha
ms.openlocfilehash: bbaf651233d4cebad3f45e5cf3823bcaf6ce38b6
ms.sourcegitcommit: 9f4eb5a3758f8a1a6a58c33c2806fa2986f702cb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2019
ms.locfileid: "58905777"
---
# <a name="how-application-gateway-works"></a>应用程序网关的工作原理

本文介绍应用程序网关如何接受传入请求并将其路由到后端。

![how-application-gateway-works](./media/how-application-gateway-works/how-application-gateway-works.png)

## <a name="how-a-request-is-accepted"></a>请求接受方式

客户端将请求发送到应用程序网关之前，会使用域名系统 (DNS) 服务器解析应用程序网关的域名。 由于应用程序网关位于 azure.com 域中，因此 DNS 条目受 Azure 的控制。 Azure DNS 将 IP 地址返回到客户端，即应用程序网关的前端 IP 地址。 应用程序网关接受一个或多个侦听器上的传入流量。 侦听器是检查连接请求的逻辑实体。 侦听器上为客户端到应用程序网关的连接配置了前端 IP 地址、协议和端口号。 如果启用了 Web 应用程序防火墙 (WAF)，则应用程序网关会根据 WAF 规则检查请求标头和正文（如果有），以确定请求是有效的请求（在这种情况下，会将请求路由到后端）还是安全威胁（在这种情况下，将阻止请求）。  

可以使用应用程序网关作为内部应用程序负载均衡器或面向 Internet 的应用程序负载均衡器。 面向 Internet 的应用程序网关使用公共 IP 地址。 面向 Internet 的应用程序网关的 DNS 名称可公开解析为其公共 IP 地址。 因此，面向 Internet 的应用程序网关可以通过 Internet 路由来自客户端的请求。 内部应用程序网关仅使用专用 IP 地址。 内部应用程序网关的 DNS 名称可公开解析为其专用 IP 地址。 因此，内部负载均衡器只能路由有权访问应用程序网关 VNET 的客户端发出的请求。 面向 Internet 的应用程序网关和内部应用程序网关都使用专用 IP 地址将请求路由到后端服务器。 因此，后端服务器不需要公共 IP 地址即可接收来自内部应用程序网关或面向 Internet 的应用程序网关的请求。

## <a name="how-a-request-is-routed"></a>请求路由方式

如果发现请求有效（或如果未启用 WAF），则评估与侦听器关联的请求路由规则，以确定请求要路由到的后端池。 规则将按照门户中的列出顺序进行处理。 根据请求路由规则配置，应用程序网关确定是要将侦听器上的所有请求路由到特定的后端池、根据 URL 路径路由到不同的后端池，还是将请求重定向到另一个端口或外部站点

选择后端池后，应用程序网关将请求发送到该池中配置的正常后端服务器之一 (y.y.y.y)。 服务器的运行状况由运行状况探测决定。 如果后端池包含多个服务器，则应用程序网关会使用轮循机制算法在正常运行的服务器之间路由请求，因此将对服务器上的请求进行负载均衡。

确定后端服务器之后，应用程序网关会根据 HTTP 设置中的配置，与后端服务器建立新的 TCP 会话。 HTTP 设置组件指定与后端服务器建立新会话所需的协议、端口和其他路由相关设置。 HTTP 设置中使用的端口和协议确定应用程序网关与后端服务器之间的流量是要加密（从而可实现端到端的 SSL）还是不加密。 将原始请求发送到后端服务器时，应用程序网关遵循 HTTP 设置中指定的任何自定义配置，这些配置与以下操作相关：替代主机名、路径和协议；保持基于 Cookie 的会话相关性、连接清空、从后端选取主机名，等等。

内部应用程序网关仅使用专用 IP 地址。 内部应用程序网关的 DNS 名称可在内部解析为其专用 IP 地址。 因此，内部负载均衡器只能路由有权访问应用程序网关 VNET 的客户端发出的请求。

如果后端池包含在内部解析的 FQDN 或专用 IP 地址，应用程序网关将请求路由到后端服务器使用其实例的专用 IP 地址。 如果后端池包含的外部终结点或外部解析的 FQDN，应用程序网关将请求路由到后端服务器使用其前端公共 IP 地址。 DNS 解析基于专用 DNS 区域或自定义 DNS 服务器上，如果配置，或者采用 Azure 提供的 DNS 的默认值。 如果尚未预配前端公共 IP 地址，系统会分配一个公共 IP 地址来建立出站外部连接。

### <a name="modifications-to-the-request"></a>对请求的修改

应用程序网关先在所有请求中插入 4 个附加的标头，然后再将请求转发到后端。 这些标头为 X-forwarded-for、X-forwarded-proto、X-forwarded-port 和 X-original-host。 x-forwarded-for 标头的格式是逗号分隔的“IP:端口”列表。 x-forwarded-proto 的有效值为 HTTP 或 HTTPS。 x-forwarded-port 指定请求抵达应用程序网关时所在的端口。 x-original-host 标头包含随请求一起抵达的原始主机标头。 此标头在 Azure 网站集成之类的场景中比较有用，在这类场景中，传入的主机标头在流量路由到后端之前会修改。 （可选）如果已启用会话相关性，则会插入网关管理的相关性 Cookie。 

此外，可以配置应用程序网关，若要修改使用的标头[重写 HTTP 标头](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers)或修改使用设置的路径重写 URI 路径。 但是，除非配置为执行此操作，所有传入请求将按原样向后端代理。


## <a name="next-steps"></a>后续步骤

了解应用程序网关的工作原理后，请参阅[应用程序网关组件](application-gateway-components.md)以更详细地了解其组件。
