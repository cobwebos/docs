---
title: Azure 应用程序网关中的 WebSocket 支持 | Microsoft Docs
description: 此页概述了应用程序网关的 WebSocket 支持。
author: vhorne
ms.author: amsriva
ms.service: application-gateway
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 03/18/2019
ms.openlocfilehash: a48f1b6e4410820d40ba6563d431c690ab791ff0
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/18/2019
ms.locfileid: "71097243"
---
# <a name="overview-of-websocket-support-in-application-gateway"></a>应用程序网关中的 WebSocket 支持概述

应用程序网关跨所有网关大小为 WebSocket 提供本机支持。 用户无法通过配置设置来选择性地启用或禁用 WebSocket 支持。 

以 [RFC6455](https://tools.ietf.org/html/rfc6455) 进行标准化的 WebSocket 协议通过长时间运行的 TCP 连接，让服务器和客户端之间实现全双工通信。 此功能让 Web 服务器和客户端之间能够进行交互性更强的通信。这种通信可以是双向的，而且不像基于 HTTP 的实现那样需要轮询。 不同于 HTTP，WebSocket 的开销很低，并且可以对多个请求/响应重复使用同一 TCP 连接，进而提高资源利用率。 WebSocket 协议设计为通过传统 HTTP 端口 80 和 443 运行。

可以在端口 80 或 443 上继续使用标准 HTTP 侦听器来接收 WebSocket 流量。 随后会使用应用程序网关规则中指定的相应后端池，将 WebSocket 流量定向到已启用 WebSocket 的后端服务器。 后端服务器必须响应应用程序网关探测，如[运行状况探测概述](application-gateway-probe-overview.md)部分中所述。 应用程序网关运行状况探测仅适用于 HTTP/HTTPS。 每个后端服务器都必须响应 HTTP 探测器，以便应用程序网关将 WebSocket 流量路由到服务器。

它用在受益于快速实时通信的应用（例如聊天、仪表板和游戏应用）中。

## <a name="how-does-websocket-work"></a>WebSocket 工作原理

若要建立 WebSocket 连接，需在客户端和服务器之间交换特定的基于 HTTP 的握手。 如果成功，则应用程序层协议会使用之前建立的 TCP 连接从 HTTP“升级”为 WebSocket。 然后就完全不使用 HTTP；两个终结点可以使用 WebSocket 协议来发送或接收数据，直至 WebSocket 连接关闭。 

![websocket](./media/application-gateway-websocket/websocket.png)

### <a name="listener-configuration-element"></a>侦听器配置元素

现有的 HTTP 侦听器可用于支持 WebSocket 流量。 以下是示例模板文件中 httpListeners 元素的代码片段。 需要同时拥有 HTTP 和 HTTPS 侦听器才能支持 WebSocket 并保护 WebSocket 流量。 同样，可以使用门户或 Azure PowerShell 在端口 80/443 上创建具有侦听器的应用程序网关，以支持 WebSocket 通信。

```json
"httpListeners": [
        {
            "name": "appGatewayHttpsListener",
            "properties": {
                "FrontendIPConfiguration": {
                    "Id": "/subscriptions/{subscriptionId/resourceGroups/{resourceGroupName/providers/Microsoft.Network/applicationGateways/{applicationGatewayName/frontendIPConfigurations/DefaultFrontendPublicIP"
                },
                "FrontendPort": {
                    "Id": "/subscriptions/{subscriptionId/resourceGroups/{resourceGroupName/providers/Microsoft.Network/applicationGateways/{applicationGatewayName/frontendPorts/appGatewayFrontendPort443'"
                },
                "Protocol": "Https",
                "SslCertificate": {
                    "Id": "/subscriptions/{subscriptionId/resourceGroups/{resourceGroupName/providers/Microsoft.Network/applicationGateways/{applicationGatewayName/sslCertificates/appGatewaySslCert1'"
                },
            }
        },
        {
            "name": "appGatewayHttpListener",
            "properties": {
                "FrontendIPConfiguration": {
                    "Id": "/subscriptions/{subscriptionId/resourceGroups/{resourceGroupName/providers/Microsoft.Network/applicationGateways/{applicationGatewayName/frontendIPConfigurations/appGatewayFrontendIP'"
                },
                "FrontendPort": {
                    "Id": "/subscriptions/{subscriptionId/resourceGroups/{resourceGroupName/providers/Microsoft.Network/applicationGateways/{applicationGatewayName/frontendPorts/appGatewayFrontendPort80'"
                },
                "Protocol": "Http",
            }
        }
    ],
```

## <a name="backendaddresspool-backendhttpsetting-and-routing-rule-configuration"></a>BackendAddressPool、BackendHttpSetting 和路由规则配置

如果后端池具有已启用 WebSocket 的服务器，那么使用 BackendAddressPool 对其进行定义。 使用后端端口 80 和 443 定义 BackendHttpSetting。 HTTP 设置中的请求超时值还适用于 WebSocket 会话。 路由规则中不需要进行任何更改，这种规则用于将相应的侦听器绑定到相应的后端地址池。 

```json
"requestRoutingRules": [{
    "name": "<ruleName1>",
    "properties": {
        "RuleType": "Basic",
        "httpListener": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/httpListeners/appGatewayHttpsListener')]"
        },
        "backendAddressPool": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/ContosoServerPool')]"
        },
        "backendHttpSettings": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
        }
    }

}, {
    "name": "<ruleName2>",
    "properties": {
        "RuleType": "Basic",
        "httpListener": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/httpListeners/appGatewayHttpListener')]"
        },
        "backendAddressPool": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/ContosoServerPool')]"
        },
        "backendHttpSettings": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
        }

    }
}]
```

## <a name="websocket-enabled-backend"></a>已启用 WebSocket 的后端

后端必须具有在已配置端口（通常为 80/443）上运行的 HTTP/HTTPS Web 服务器，WebSocket 才能运行。 此要求是因为 WebSocket 协议要求初始握手是 HTTP，且标头字段为升级到 WebSocket 协议。 下面是一个标头示例：

```
    GET /chat HTTP/1.1
    Host: server.example.com
    Upgrade: websocket
    Connection: Upgrade
    Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ==
    Origin: https://example.com
    Sec-WebSocket-Protocol: chat, superchat
    Sec-WebSocket-Version: 13
```

另一个原因是该应用程序网关后端运行状况探测仅支持 HTTP 和 HTTPS 协议。 如果后端服务器未响应 HTTP 或 HTTPS 探测器，则将其从后端池中除去。

## <a name="next-steps"></a>后续步骤

了解 WebSocket 支持后，请转到[创建应用程序网关](quick-create-powershell.md)，开始使用已启用 WebSocket 的 Web 应用程序。