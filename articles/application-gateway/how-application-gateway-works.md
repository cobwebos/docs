---
title: 应用程序网关的工作原理
description: 本文提供了有关应用程序网关的工作原理的信息
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 02/20/2019
ms.author: absha
ms.openlocfilehash: 2f27105aed940f0411abaa534cb09adf0be34bfe
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60830309"
---
# <a name="how-an-application-gateway-works"></a>应用程序网关的工作原理

本文介绍如何应用程序网关接受传入请求并将其路由到后端。

![应用程序网关接受请求的方式](./media/how-application-gateway-works/how-application-gateway-works.png)

## <a name="how-an-application-gateway-accepts-a-request"></a>应用程序网关接受请求的方式

1. 客户端将请求发送到应用程序网关之前，它通过使用域名系统 (DNS) 服务器来解决应用程序网关的域名。 因为所有应用程序网关都在 azure.com 域中，azure 将控制 DNS 条目。

2. Azure DNS 返回到客户端，这是应用程序网关的前端 IP 地址的 IP 地址。

3. 应用程序网关接受一个或多个侦听器上的传入流量。 侦听器是检查连接请求的逻辑实体。 它被配置前端 IP 地址、 协议和客户端连接到应用程序网关的端口号。

4. 如果 web 应用程序防火墙 (WAF) 正在使用中，应用程序网关就会检查请求标头和正文，如果存在，请根据 WAF 规则。 此操作确定该请求是否是有效的请求或安全威胁。 如果请求是有效的它将路由到后端。 如果请求不是有效的它作为一种安全威胁被阻止。

作为内部应用程序负载均衡器或作为面向 internet 的应用程序负载均衡器，可以使用 azure 应用程序网关。 面向 internet 的应用程序网关使用公共 IP 地址。 面向 internet 的应用程序网关的 DNS 名称是可公开解析为其公共 IP 地址。 因此，面向 internet 的应用程序网关可以将客户端请求路由到 internet。

内部应用程序网关使用仅使用专用 IP 地址。 内部应用程序网关的 DNS 名称可公开解析为其专用 IP 地址。 因此，内部负载均衡器可以仅将路由到应用程序网关的虚拟网络具有访问权限的客户端的请求。

面向 internet 的和内部应用程序网关将请求路由到后端服务器使用专用 IP 地址。 后端服务器不需要公共 IP 地址，用于接收来自内部的请求或面向 internet 的应用程序网关。

## <a name="how-an-application-gateway-routes-a-request"></a>应用程序网关的请求的路由

如果请求有效或 WAF 是在未使用，应用程序网关的计算结果与侦听器相关联的请求路由规则。 此操作确定将路由到请求的后端池。

它们在门户中列出的顺序处理规则。 根据请求路由规则，应用程序网关确定是否将所有请求都路由到特定的后端池，在侦听器上都路由请求到不同的后端池根据 URL 路径或将请求重定向到另一个端口或外部站点。

当应用程序网关选择的后端池时，它将请求发送到其中一个正常的后端服务器池 (y.y.y.y) 中。 服务器的运行状况取决于运行状况探测。 如果后端池包含多个服务器，应用程序网关使用轮循机制算法之间正常运行的服务器将请求路由。 此负载平衡的服务器上的请求。

应用程序网关确定后端服务器后，将打开一个新的 TCP 会话与基于 HTTP 设置的后端服务器。 HTTP 设置指定协议、 端口和其他与路由相关的设置所需建立新的会话的后端服务器。

端口和 HTTP 设置中使用的协议确定应用程序网关和后端服务器之间的流量进行加密 （因此完成端到端 SSL） 还是未加密。

当应用程序网关将原始请求发送到后端服务器时，它遵循与重写主机名、 路径和协议相关的 HTTP 设置中所做的任何自定义配置。 此操作将保留基于 cookie 的会话相关性、 连接排出、 主机名所选内容从后端中，依次类推。

内部应用程序网关使用仅使用专用 IP 地址。 内部应用程序网关的 DNS 名称是解析为其专用 IP 地址。 结果是，内部负载均衡器可以仅将路由到应用程序网关的虚拟网络具有访问权限的客户端的请求。

 >[!NOTE]
 >这两个面向 internet 的和内部应用程序网关将请求路由到后端服务器使用专用 IP 地址。 当后端池资源包含专用的 IP 地址、 虚拟机 NIC 配置或在内部解析地址时，会发生此操作。 如果后端池：
> - **是一个公共终结点**，应用程序网关使用其前端公共 IP 连接到服务器。 如果没有前端公共 IP 地址，其中一个分配出站的外部连接。
> - **包含在内部解析的 FQDN 或专用 IP 地址**，应用程序网关将请求路由到后端服务器使用其实例的专用 IP 地址。
> - **包含的外部终结点或外部解析的 FQDN**，应用程序网关将请求路由到后端服务器使用其前端公共 IP 地址。 DNS 解析基于专用 DNS 区域或自定义 DNS 服务器，如果配置，或使用默认 Azure 提供的 DNS。 如果没有前端公共 IP 地址，其中一个分配出站的外部连接。

### <a name="modifications-to-the-request"></a>对请求的修改

应用程序网关将插入到的所有请求的四个其他标头之前它将请求转发到后端。 这些标头是 x-转发-对于、 x 转发 proto、 x-转发的端口和 x 原始主机。 x-forwarded-for 标头的格式是逗号分隔的“IP:端口”列表。

x-forwarded-proto 的有效值为 HTTP 或 HTTPS。 X 转发端口指定，则表明请求到达应用程序网关的端口。 x-original-host 标头包含随请求一起抵达的原始主机标头。 此标头可在 Azure 网站集成、 流量路由到后端之前，在其中修改传入主机标头。 如果作为一个选项启用会话相关性，然后它将添加的网关管理相关性 cookie。

可以配置应用程序网关，若要使用修改标头[重写 HTTP 标头](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers)或若要使用的路径重写设置修改 URI 路径。 但是，除非配置为执行此操作，所有传入请求是通过代理传递到后端。

## <a name="next-steps"></a>后续步骤

[了解有关应用程序网关组件](application-gateway-components.md)
