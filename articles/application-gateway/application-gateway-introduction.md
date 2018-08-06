---
title: Azure 应用程序网关的定义
description: 了解如何使用 Azure 应用程序网关管理应用程序的 Web 流量。
services: application-gateway
author: vhorne
manager: jpconnock
ms.service: application-gateway
ms.topic: overview
ms.custom: mvc
ms.workload: infrastructure-services
ms.date: 4/9/2018
ms.author: victorh
ms.openlocfilehash: aa31bb599f665e8d3a407531d399701a5be35e41
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2018
ms.locfileid: "39364111"
---
# <a name="what-is-azure-application-gateway"></a>什么是 Azure 应用程序网关？

Azure 应用程序网关是一种 Web 流量负载均衡器，可用于管理 Web 应用程序的流量。 

传统负载均衡器在传输层（OSI 层 4 - TCP 和 UDP）进行操作，并基于源 IP 地址和端口将流量路由到目标 IP 地址和端口。 但在使用应用程序网关时，可以实现更具体的操作。 例如，可以基于传入 URL 路由流量。 因此，如果 `/images` 在传入 URL 中，则可将流量路由到为映像配置的一组特定服务器（称为池）中。 如果 `/video` 在 URL 中，则可将该流量路由到为视频优化的另一个池中。

![imageURLroute](./media/application-gateway-url-route-overview/figure1-720.png)

这种类型的路由称为应用程序层（OSI 层 7）负载均衡。 Azure 应用程序网关可以执行基于 URL 的路由等操作。 以下功能是 Azure 应用程序网关附带的： 

## <a name="url-based-routing"></a>基于 URL 的路由

基于 URL 路径的路由用于根据请求的 URL 路径，将流量路由到后端服务器池。 方案之一是将不同内容类型的请求路由到不同的池。

例如，将 `http://contoso.com/video/*` 的请求路由到 VideoServerPool，将 `http://contoso.com/images/*` 的请求路由到 ImageServerPool。 如果没有任何路径模式匹配，则选择 DefaultServerPool。

## <a name="redirection"></a>重定向

为确保应用程序与其用户之间的所有通信都通过加密路径进行，适用于许多 Web 应用程序的常见方案是支持 HTTP 到 HTTPS 自动重定向。 

你可能过去用过创建专用池等技术，其唯一目的是将通过 HTTP 接收的请求重定向到 HTTPS。 应用程序网关可以重定向流量。 这样可以简化应用程序配置、优化资源使用情况，并支持全局重定向和基于路径的重定向等新的重定向方案。 应用程序网关重定向支持并不仅限于 HTTP 到 HTTPS 的重定向。 这是一种通用重定向机制，因此可以针对使用规则定义的任何端口进行双向重定向。 它还支持重定向到外部站点。

应用程序网关重定向支持具有以下功能：

- 在应用程序网关上进行的从一个端口到另一个端口的全局重定向。 这样可实现站点上的 HTTP 到 HTTPS 重定向。
- 基于路径的重定向。 这种类型的重定向只能在特定站点区域（例如 `/cart/*` 表示的购物车区域）中进行 HTTP 到 HTTPS 的重定向。
- 重定向到外部站点。

## <a name="multiple-site-hosting"></a>多站点托管

使用多站点托管可以在同一应用程序网关实例上配置多个网站。 此功能可以将多达 20 个网站添加到一个应用程序网关中，从而为部署配置更有效的拓扑。 每个网站都可以定向到自己的池。 例如，应用程序网关可以通过两个名为 ContosoServerPool 和 FabrikamServerPool 的服务器池分别处理 `contoso.com` 和 `fabrikam.com` 的流量。

对 `http://contoso.com` 的请求路由到 ContosoServerPool，对 `http://fabrikam.com` 的请求路由到 FabrikamServerPool。

同样，可以将同一父域的两个子域托管在同一应用程序网关部署中。 例如，在单个应用程序网关部署中托管的 `http://blog.contoso.com` 和 `http://app.contoso.com` 都是使用子域。

## <a name="session-affinity"></a>会话相关性

需要在同一服务器上保留用户会话时，可以使用基于 Cookie 的会话相关性功能。 借助网关托管的 Cookie，应用程序网关可以将来自用户会话的后续流量定向到同一服务器进行处理。 在用户会话的会话状态在服务器上进行本地保存的情况下，此功能十分重要。

## <a name="secure-sockets-layer-ssl-termination"></a>安全套接字层 (SSL) 终止

应用程序网关支持在网关上终止 SSL，之后，流量通常会以未加密状态流到后端服务器。 此功能让 Web 服务器不用再负担昂贵的加密和解密开销。 但有时，与服务器进行未加密的通信不是可以接受的选项。 这可能是因为安全要求、符合性要求，或者应用程序可能仅接受安全连接。 对于此类应用程序，应用程序网关支持端到端 SSL 加密。

## <a name="web-application-firewall"></a>Web 应用程序防火墙

Web 应用程序防火墙 (WAF) 是应用程序网关的功能，可以对 Web 应用程序进行集中保护，避免其受到常见的攻击和漏洞伤害。 WAF 基于 [OWASP（开放 Web 应用程序安全项目）核心规则集](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3.0 或 2.2.9 中的规则。 

Web 应用程序已逐渐成为利用已知漏洞的恶意攻击的目标。 这些攻击中最常见的攻击是 SQL 注入攻击和跨站点脚本 (XSS) 攻击。 防止应用程序代码中的此类攻击颇具挑战性，可能需要在应用程序拓扑的多个层进行严格的维护、修补和监视。 集中式 Web 应用程序防火墙有助于大幅简化安全管理，为抵卸威胁或入侵的应用程序管理员提供更好的保障。 WAF 解决方案还可在中央位置修补已知漏洞，更快地响应安全威胁，而不必对每个已部署的单独 Web 应用程序进行保护。 可将现有应用程序网关轻松转换为支持 WAF 的应用程序网关。

## <a name="websocket-and-http2-traffic"></a>Websocket 和 HTTP/2 流量

应用程序网关为 WebSocket 和 HTTP/2 协议提供本机支持。 用户无法通过配置设置来选择性地启用或禁用 WebSocket 支持。 可以通过 Azure PowerShell 启用 HTTP/2 支持。
 
WebSocket 和 HTTP/2 协议通过长时间运行的 TCP 连接，在服务器和客户端之间实现全双工通信。 此功能让 Web 服务器和客户端之间能够进行交互性更强的通信。这种通信可以是双向的，而且不像基于 HTTP 的实现那样需要轮询。 不同于 HTTP，这些协议的开销很低，并且可以对多个请求/响应重复使用同一 TCP 连接，从而提高资源利用率。 这些协议设计为通过传统 HTTP 端口 80 和 443 运行。



## <a name="next-steps"></a>后续步骤

可以根据自己的需求和环境，使用 Azure 门户、Azure PowerShell 或 Azure CLI 创建测试性应用程序网关：

- [快速入门：使用 Azure 应用程序网关定向 Web 流量 - Azure 门户](quick-create-portal.md)。
- [快速入门：使用 Azure 应用程序网关定向 Web 流量 - Azure PowerShell](quick-create-powershell.md)
- [快速入门：使用 Azure 应用程序网关定向 Web 流量 - Azure CLI](quick-create-cli.md)
