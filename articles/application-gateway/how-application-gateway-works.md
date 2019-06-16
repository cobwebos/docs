---
title: 应用程序网关的工作原理
description: 本文提供有关应用程序网关工作原理的信息
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 02/20/2019
ms.author: absha
ms.openlocfilehash: a16421182f533f5aa2ad4bcc2e58e910cc7e8ca6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "64702412"
---
# <a name="how-an-application-gateway-works"></a>应用程序网关的工作原理

本文介绍应用程序网关如何接受传入请求并将其路由到后端。

![应用程序网关如何接受请求](./media/how-application-gateway-works/how-application-gateway-works.png)

## <a name="how-an-application-gateway-accepts-a-request"></a>应用程序网关如何接受请求

1. 客户端将请求发送到应用程序网关之前，会使用域名系统 (DNS) 服务器解析应用程序网关的域名。 由于所有应用程序网关都位于 azure.com 域中，因此 DNS 条目受 Azure 的控制。

2. Azure DNS 将 IP 地址返回到客户端，即应用程序网关的前端 IP 地址。

3. 应用程序网关接受一个或多个侦听器上的传入流量。 侦听器是检查连接请求的逻辑实体。 侦听器上为客户端到应用程序网关的连接配置了前端 IP 地址、协议和端口号。

4. 如果正在使用 Web 应用程序防火墙 (WAF)，则应用程序网关会根据 WAF 规则检查请求标头和正文（如果有）。 此操作确定请求是有效的请求还是安全威胁。 如果请求有效，则将请求路由到后端。 如果请求无效，则会将它视为安全威胁，并阻止它。

可以使用 Azure 应用程序网关作为内部应用程序负载均衡器或面向 Internet 的应用程序负载均衡器。 面向 Internet 的应用程序网关使用公共 IP 地址。 面向 Internet 的应用程序网关的 DNS 名称可公开解析为其公共 IP 地址。 因此，面向 Internet 的应用程序网关可将客户端请求路由到 Internet。

内部应用程序网关仅使用专用 IP 地址。 内部应用程序网关的 DNS 名称可公开解析为其专用 IP 地址。 因此，内部负载均衡器只能路由有权访问应用程序网关虚拟网络的客户端发出的请求。

面向 Internet 的应用程序网关和内部应用程序网关都使用专用 IP 地址将请求路由到后端服务器。 后端服务器不需要公共 IP 地址即可接收来自内部应用程序网关或面向 Internet 的应用程序网关的请求。

## <a name="how-an-application-gateway-routes-a-request"></a>应用程序网关如何路由请求

如果请求有效或未使用 WAF，应用程序网关将评估与侦听器关联的请求路由规则。 此操作确定要将请求路由到哪个后端池。

规则将按照门户中的列出顺序进行处理。 根据请求路由规则，应用程序网关确定是要将侦听器上的所有请求路由到特定的后端池、根据 URL 路径将请求路由到不同的后端池，还是将请求重定向到另一个端口或外部站点。

当应用程序网关选择后端池时，会将请求发送到该池中的正常后端服务器之一 (y.y.y.y)。 服务器的运行状况由运行状况探测决定。 如果后端池包含多个服务器，应用程序网关将使用轮循机制算法在正常运行的服务器之间路由请求。 这会对服务器上的请求进行负载均衡。

应用程序网关确定后端服务器之后，会根据 HTTP 设置来与后端服务器建立新的 TCP 会话。 HTTP 设置组件指定与后端服务器建立新会话所需的协议、端口和其他路由相关设置。

HTTP 设置中使用的端口和协议确定应用程序网关与后端服务器之间的流量是要加密（从而可实现端到端的 SSL）还是不加密。

将原始请求发送到后端服务器时，应用程序网关遵循 HTTP 设置中指定的任何自定义配置，这些配置与替代主机名、路径和协议相关。 此操作将保持基于 Cookie 的会话相关性、连接清空、从后端选择主机名的设置，等等。

内部应用程序网关仅使用专用 IP 地址。 内部应用程序网关的 DNS 名称可解析为其专用 IP 地址。 因此，内部负载均衡器只能路由有权访问应用程序网关虚拟网络的客户端发出的请求。

 >[!NOTE]
 >面向 Internet 的应用程序网关和内部应用程序网关都使用专用 IP 地址将请求路由到后端服务器。 如果后端池资源包含专用 IP 地址、VM NIC 配置或内部可解析地址，则会发生此操作。 如果后端池：
> - **是公共终结点**，则应用程序网关会使用其前端公共 IP 来访问服务器。 如果没有前端公共 IP 地址，系统会分配一个公共 IP 地址来建立出站外部连接。
> - **包含可以在内部解析的 FQDN 或专用 IP 地址**，则应用程序网关会使用其实例的专用 IP 地址将请求路由到后端服务器。
> - **包含外部终结点或者可以在外部解析的 FQDN**，则应用程序网关会使用其前端的公共 IP 地址将请求路由到后端服务器。 DNS 解析基于专用 DNS 区域或自定义 DNS 服务器（如果已配置），或者会使用 Azure 提供的默认 DNS。 如果没有前端公共 IP 地址，系统会分配一个公共 IP 地址来建立出站外部连接。

### <a name="modifications-to-the-request"></a>对请求的修改

应用程序网关先在所有请求中插入四个附加的标头，然后再将请求转发到后端。 这些标头为 x-forwarded-for、x-forwarded-proto、x-forwarded-port 和 x-original-host。 x-forwarded-for 标头的格式是逗号分隔的“IP:端口”列表。

x-forwarded-proto 的有效值为 HTTP 或 HTTPS。 x-forwarded-port 指定请求抵达应用程序网关时所在的端口。 x-original-host 标头包含随请求一起抵达的原始主机标头。 此标头在 Azure 网站集成中非常有用，其中，传入的主机标头在流量路由到后端之前会修改。 如果已启用会话相关性作为一个选项，则会添加网关管理的相关性 Cookie。

可以配置应用程序网关，若要使用修改标头[重写 HTTP 标头](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers)或若要使用的路径重写设置修改 URI 路径。 但是，除非配置为执行此操作，所有传入请求是通过代理传递到后端。

## <a name="next-steps"></a>后续步骤

[了解应用程序网关组件](application-gateway-components.md)
