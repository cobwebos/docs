---
title: Azure 应用程序网关侦听器配置
description: 本文介绍如何配置 Azure 应用程序网关侦听器。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: surmb
ms.openlocfilehash: ef2ff8924cd8a92c5d2d2e5dd9da6bb74fad1a14
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/10/2020
ms.locfileid: "89652865"
---
# <a name="application-gateway-listener-configuration"></a>应用程序网关侦听器配置

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

侦听器是一个逻辑实体，它可以使用端口、协议、主机和 IP 地址检查传入的连接请求。 配置侦听器时，必须输入与网关上传入请求中的对应值相匹配的值。

使用 Azure 门户创建应用程序网关时，还可以通过选择侦听器的协议和端口来创建默认的侦听器。 可以选择是否要在侦听器上启用 HTTP2 支持。 创建应用程序网关后，可以编辑该默认侦听器的设置 (*appGatewayHttpListener*) 或创建新的侦听器。

## <a name="listener-type"></a>侦听器类型

创建新侦听器时，可以选择[“基本”或“多站点”](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#types-of-listeners)。 

- 如果你希望自己的所有请求（针对任何域）都能够被接受并转发到后端池，请选择“基本”。 了解[如何创建包含基本侦听器的应用程序网关](https://docs.microsoft.com/azure/application-gateway/quick-create-portal)。

- 如果希望根据主机头或主机名将请求转发到不同的后端池，请选择多站点侦听器，并且还必须在其中指定与传入请求匹配的主机名。 这是因为，应用程序网关需要使用 HTTP 1.1 主机标头才能在相同的公共 IP 地址和端口上托管多个网站。 如需了解详细信息，请参阅[使用应用程序网关托管多个站点](multiple-site-overview.md)。

### <a name="order-of-processing-listeners"></a>侦听器的处理顺序

对于 v1 SKU，请求根据规则顺序和侦听器类型进行匹配。 如果某项使用基本侦听器的规则在顺序上排第一，系统会先处理它，它会接受该端口和 IP 组合的任何请求。 为了避免这种情况，请先使用多站点侦听器配置规则，然后将包含基本侦听器的规则推送到列表中的最后。

对于 v2 SKU，在基本侦听器之前处理多站点侦听器。

## <a name="front-end-ip-address"></a>前端 IP 地址

选择要与此侦听器关联的前端 IP 地址。 侦听器将在此 IP 上侦听传入的请求。

## <a name="front-end-port"></a>前端端口

选择前端端口。 选择现有端口或新建一个端口。 选择[允许的端口范围](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#ports)内的任意值。 不仅可以使用已知的端口（例如 80 和 443），而且还能使用任何适用的且允许的自定义端口。 一个端口可用于公共侦听器或专用侦听器。

## <a name="protocol"></a>协议

选择 HTTP 或 HTTPS：

- 如果选择 HTTP，则客户端与应用程序网关之间的流量将不会加密。

- 如果想要实现 [TLS 终止](features.md#secure-sockets-layer-ssltls-termination)或[端到端 TLS 加密](https://docs.microsoft.com/azure/application-gateway/ssl-overview)，请选择 HTTPS。 客户端与应用程序网关之间的流量将会加密。 TLS 连接将在应用程序网关上终止。 若要实现端到端的 TLS 加密，必须选择 HTTPS，并配置“后端 HTTP”设置。 这可以确保流量在从应用程序网关传输到后端时重新得到加密。


若要配置 TLS 终止和端到端 TLS 加密，必须将一个证书添加到侦听器，使应用程序网关能够派生对称密钥。 派生过程是根据 TLS 协议规范进行的。 使用该对称密钥可以加密和解密发送到网关的流量。 网关证书必须采用个人信息交换 (PFX) 格式。 使用此格式可以导出私钥，供网关用来加密和解密流量。

## <a name="supported-certificates"></a>支持的证书

请参阅 [tls 终止和应用程序网关的端到端 Tls 概述](ssl-overview.md#certificates-supported-for-tls-termination)

## <a name="additional-protocol-support"></a>其他协议支持

### <a name="http2-support"></a>HTTP2 支持

仅针对连接到应用程序网关侦听器的客户端提供 HTTP/2 协议支持。 与后端服务器池的通信是通过 HTTP/1.1 进行的。 默认情况下，HTTP/2 支持处于禁用状态。 以下 Azure PowerShell 代码片段演示如何启用此支持：

```azurepowershell
$gw = Get-AzApplicationGateway -Name test -ResourceGroupName hm

$gw.EnableHttp2 = $true

Set-AzApplicationGateway -ApplicationGateway $gw
```

### <a name="websocket-support"></a>WebSocket 支持

默认已启用 WebSocket 支持。 没有任何用户可配置的设置可以启用或禁用此支持。 可对 HTTP 和 HTTPS 侦听器使用 WebSocket。

## <a name="custom-error-pages"></a>自定义错误页

可以在全局级别以及侦听器级别定义自定义错误。 但是，目前不支持在 Azure 门户中创建全局级别的自定义错误页。 可以在侦听器级别为 403 Web 应用程序防火墙错误或 502 维护页配置自定义错误页。 此外，必须为给定的错误状态代码指定一个可公开访问的 Blob URL。 有关详细信息，请参阅[创建应用程序网关自定义错误页](https://docs.microsoft.com/azure/application-gateway/custom-error)。

![应用程序网关错误代码](https://docs.microsoft.com/azure/application-gateway/media/custom-error/ag-error-codes.png)

若要配置全局自定义错误页，请参阅 [Azure PowerShell 配置](https://docs.microsoft.com/azure/application-gateway/custom-error#azure-powershell-configuration)。

## <a name="tls-policy"></a>TLS 策略

可以集中管理 TLS/SSL 证书，以及减小后端服务器场的加密-解密开销。 采用集中式 TLS 处理还能指定符合安全要求的集中 TLS 策略。 可以选择默认、预定义或自定义的 TLS 策略。  

配置 TLS 策略来控制 TLS 协议版本。 可将应用程序网关配置为使用 TLS1.0、TLS1.1 和 TLS1.2 中适用于 TLS 握手的最低协议版本。 默认情况下，SSL 2.0 和 3.0 已禁用且不可配置。 有关详细信息，请参阅[应用程序网关 TLS 策略概述](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-policy-overview)。

创建侦听器后，请将它关联到某个请求路由规则。 该规则确定如何将侦听器上收到的请求路由到后端。

## <a name="next-steps"></a>后续步骤

- [了解请求路由规则](configuration-request-routing-rules.md)。