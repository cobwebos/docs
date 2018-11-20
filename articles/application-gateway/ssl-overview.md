---
title: 在 Azure 应用程序网关上启用端到端 SSL
description: 本文概述应用程序网关的端到端 SSL 支持。
services: application-gateway
author: amsriva
ms.service: application-gateway
ms.topic: article
ms.date: 10/23/2018
ms.author: amsriva
ms.openlocfilehash: e7020ef5c1f7411c7226e7a2db489112ee6bf0a4
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2018
ms.locfileid: "49945495"
---
# <a name="overview-of-end-to-end-ssl-with-application-gateway"></a>应用程序网关的端到端 SSL 概述

应用程序网关支持在网关上终止 SSL，之后，流量通常会以未加密状态流到后端服务器。 此功能让 Web 服务器不用再负担昂贵的加密和解密开销。 但对于某些客户而言，与后端服务器的未加密通信不是可以接受的选项。 此通信未加密，可能是由于有安全要求、符合性要求，或应用程序可能仅接受安全连接。 对于此类应用程序，应用程序网关支持端到端 SSL 加密。

使用端到端 SSL 可以安全地将敏感数据以加密方式传输到后端，同时仍可利用应用程序网关提供的第 7 层负载均衡功能的优点。 部分功能包括：基于 Cookie 的会话相关性、基于 URL 的路由、基于站点的路由支持，或注入 X-Forwarded-* 标头。

如果配置为端到端 SSL 通信模式，应用程序网关会在网关上终止 SSL 会话，并解密用户流量。 然后，它会应用配置的规则，以选择要将流量路由到的适当后端池实例。 应用程序网关接下来会初始化到后端服务器的新 SSL 连接，并先使用后端服务器的公钥证书重新加密数据，然后再将请求传输到后端。 要启用端到端 SSL，请将 BackendHTTPSetting 中的协议设置设为 HTTPS，然后再将其应用到后端池。 后端池中每个已启用端到端 SSL 的后端服务器都必须配置证书，以便能够进行安全的通信。

![端到端 ssl 方案][1]

在此示例中，使用 TLS1.2 的请求通过端到端 SSL 路由到池 1 中的后端服务器。

## <a name="end-to-end-ssl-and-whitelisting-of-certificates"></a>端到端 SSL 和证书允许列表

应用程序网关只会与已知的后端实例通信，这些实例已将其证书加入应用程序网关的允许列表。 要启用证书允许列表，必须将后端服务器证书的公钥上传到应用程序网关（而不是根证书）。 然后仅允许连接到已知且加入允许列表的后端。 剩余的后端会导致网关错误。 自签名证书仅用于测试目的，不建议用于生产工作负荷。 如前面的步骤中所述，此类证书必须加入应用程序网关的允许列表，才可以使用。

> [!NOTE]
> Azure Web 应用等受信任的 Azure 服务不需要身份验证证书设置。

## <a name="end-to-end-ssl-with-the-v2-sku"></a>端到端 SSL 与 v2 SKU

身份验证证书已弃用，并由应用程序网关 v2 SKU 中的受信任的根证书替换。 它们的功能与身份验证证书类似，但有一些主要区别：

- 由 CN 与 HTTP 后端设置中的主机名匹配的知名 CA 颁发机构签名的证书不需要任何额外的步骤即可使端到端 SSL 工作。 

   例如，如果后端证书由知名 CA 颁发并具有 contoso.com 的 CN，并且后端 http 设置的主机字段也设置为 contoso.com，则不需要其他步骤。 可以将后端 http 设置协议设置为 HTTPS，并且运行状况探测和数据路径都将启用 SSL。 如果使用 Azure Web 应用或其他 Azure Web 服务作为后端，则这些也是隐式受信任的，端到端 SSL 不需要其他步骤。
- 如果证书是自签名证书，或是由未知中介签名的证书，那么，要在 v2 SKU 中启用端到端 SSL，必须定义受信任的根证书。 应用程序网关仅与符合以下条件的后端进行通信：后端的服务器证书的根证书与池关联的后端 http 设置中的受信任根证书列表之一匹配。
- 除了根证书匹配之外，应用程序网关还会验证后端 http 设置中指定的主机设置是否与后端服务器的 SSL 证书提供的公用名 (CN) 的主机设置相匹配。 尝试与后端建立 SSL 连接时，应用程序网关会将服务器名称指示 (SNI) 扩展设置为后端 http 设置中指定的主机。
- 如果已选择**从后端地址选择主机名**而不是选择后端 http 设置中的主机字段，则 SNI 标头始终设置为后端池 FQDN，并且后端服务器 SSL 证书上的 CN 必须与其 FQDN 匹配。 在此方案中不支持具有 IP 的后端池成员。
- 根证书是来自后端服务器证书的 base64 编码的根证书。

## <a name="next-steps"></a>后续步骤

了解端到端 SSL 后，请转到[使用应用程序网关和 PowerShell 配置端到端 SSL](application-gateway-end-to-end-ssl-powershell.md)，以使用端到端 SSL 创建应用程序网关。

<!--Image references-->

[1]: ./media/ssl-overview/scenario.png
