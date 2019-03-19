---
title: Azure 应用程序网关的工作原理
description: 本文提供有关应用程序网关工作原理的信息
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 02/20/2019
ms.author: absha
ms.openlocfilehash: ef07def377b74fb74d57372f471efcf48fcf7aa2
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2019
ms.locfileid: "57881089"
---
# <a name="how-application-gateway-works"></a>应用程序网关的工作原理

本文介绍了应用程序网关如何接受传入请求，并将其路由到后端。

![how-application-gateway-works](./media/how-application-gateway-works/how-application-gateway-works.png)

## <a name="how-a-request-is-accepted"></a>如何接受的请求

客户端将请求发送到应用程序网关之前，它将使用域名系统 (DNS) 服务器的应用程序网关的域名解析。 由于应用程序网关处于 azure.com 域由 Azure 中，控制 DNS 条目。 Azure DNS 将 IP 地址返回到客户端，即应用程序网关的前端 IP 地址。 应用程序网关接受一个或多个传入的流量*侦听器*。 侦听器是检查连接请求的逻辑实体。 它被配置前端 IP 地址、 协议和客户端连接到应用程序网关的端口号。 如果启用了 Web 应用程序防火墙 (WAF)，应用程序网关检查请求标头和正文 （如果有） 根据*WAF 规则*若要确定请求是否为有效的请求-在这种情况下它将路由到后端-或安全威胁，在该情况下请求会遭到阻止。  

应用程序网关可以用作内部应用程序负载均衡器或面向 Internet 的应用程序负载均衡器。 面向 Internet 的应用程序网关具有公共 IP 地址。 面向 Internet 的应用程序网关的 DNS 名称是可公开解析为其公共 IP 地址。 因此，面向 Internet 的应用程序网关可以通过 Internet 路由来自客户端请求。 内部应用程序网关分配专用 IP 地址。 内部应用程序网关的 DNS 名称是可公开解析到其专用 IP 地址。 因此，内部负载均衡器可以仅将路由到应用程序网关的 VNET 具有访问权限的客户端的请求。 面向 Internet 的和内部应用程序网关将请求路由到后端服务器使用专用 IP 地址。 因此，后端服务器不需要公共 IP 地址即可接收来自内部应用程序网关或面向 Internet 的应用程序网关的请求。

## <a name="how-a-request-is-routed"></a>如何路由请求

如果发现请求有效（或如果未启用 WAF），则评估与侦听器关联的请求路由规则，以确定请求要路由到的后端池。 规则将按照门户中的列出顺序进行处理。 基于*请求路由规则*配置，应用程序网关确定是将所有请求都路由到特定的后端池侦听器上还是将它们都路由到不同的后端池根据 URL 路径或设置为*将请求重定向*到另一个端口或外部站点

一次*后端**池*选择后，应用程序网关将请求发送到后端服务器处于正常状态在池中配置 (y.y.y.y) 之一。 服务器的运行状况由运行状况探测决定。 如果后端池包含多个服务器，应用程序网关使用轮循机制算法之间正常运行的服务器将请求路由，因此负载平衡的服务器上的请求。

应用程序网关的后端服务器已确定后，打开与后端服务器中的配置的新 TCP 会话*HTTP 设置*。 HTTP 设置组件指定与后端服务器建立新会话所需的协议、端口和其他路由相关设置。 端口和 HTTP 设置中使用的协议确定是否已加密的应用程序网关和后端服务器之间的流量，从而完成端到端 SSL，或以未加密状态。 应用程序网关时将原始请求发送到后端服务器，遵循与重写主机名、 路径、 协议; 相关 HTTP 设置中所做的任何自定义配置维护基于 cookie 的会话相关性，连接清空，选取的主机名从后端，等等。

内部的应用程序网关具有专用 IP 地址。 内部的应用程序网关的 DNS 名称是在内部解析为其专用 IP 地址。 因此，内部负载均衡器只能路由有权访问应用程序网关 VNET 的客户端发出的请求。

请注意，面向 Internet 的和内部应用程序网关将路由到后端服务器使用专用 IP 地址，如果你的后端池资源包含专用 IP 地址、 虚拟机 NIC 配置或在内部解析地址，并且请求你后端池是一个公共终结点，应用程序网关将使用其前端公共 IP 用于访问服务器。 如果尚未预配的前端公共 IP 地址，其中一个分配出站的外部连接。

### <a name="modifications-to-the-request"></a>对请求进行修改

应用程序网关将插入到的所有请求的 4 个其他标头之前它将请求转发到后端。 这些标头是 X-转发-对于、 X 转发 proto、 X 转发端口和 X 原始主机。 X 转发的标头的格式是逗号分隔列表的 ip: port。 X 转发 proto 的有效值为 HTTP 或 HTTPS。 x-forwarded-port 指定请求抵达应用程序网关时所在的端口。 X 原始主机标头包含请求到达与其原始主机标头。 此标头在 Azure 网站集成之类的场景中比较有用，在这类场景中，传入的主机标头在流量路由到后端之前会修改。 （可选） 如果已启用会话相关性，则会插入一个网关托管的相关性 cookie。 

此外，可以配置应用程序网关，若要修改使用的标头[重写 HTTP 标头](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers)或修改使用设置的路径重写 URI 路径。 但是，除非配置为执行此操作，所有传入请求将按原样向后端代理。


## <a name="next-steps"></a>后续步骤

了解后应用程序网关的工作原理，请参阅[应用程序网关组件](application-gateway-components.md)若要了解更多详细信息及其组件。
