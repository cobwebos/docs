---
title: 在 Azure 应用程序网关上启用端到端 TLS
description: 本文概述应用程序网关的端到端 TLS 支持。
services: application-gateway
author: amsriva
ms.service: application-gateway
ms.topic: conceptual
ms.date: 5/13/2020
ms.author: victorh
ms.openlocfilehash: 1986955c7135cb9296937392b23635ae62d8d9f7
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/05/2020
ms.locfileid: "85962095"
---
# <a name="overview-of-tls-termination-and-end-to-end-tls-with-application-gateway"></a>应用程序网关的 TLS 终止和端到端 TLS 概述

传输层安全性 (TLS) 以前称为安全套接字层 (SSL)，是一种用于在 Web 服务器和浏览器之间建立加密链接的标准安全技术。 该链接可确保在 Web 服务器和浏览器之间传递的所有数据都会得到保密和加密。 应用程序网关支持网关上的 TLS 终止以及端到端 TLS 加密。

## <a name="tls-termination"></a>TLS 终止

应用程序网关支持在网关上终止 TLS，之后，流量通常会以未加密状态流到后端服务器。 在应用程序网关上执行 TLS 终止可以带来诸多优势：

- **提高性能** - 执行 TLS 解密时，初次握手最容易造成性能下降。 为了提高性能，执行解密的服务器会缓存 TLS 会话 ID 并管理 TLS 会话票证。 如果在应用程序网关上完成此操作，则来自同一客户端的所有请求都可以使用缓存值。 如果在后端服务器上完成此操作，那么每当客户端的请求转到其他服务器时，客户端都必须重新进行身份验证。 使用 TLS 票证有助于缓解此问题，但并非所有客户端都支持 TLS 票证，并且配置和管理这些票证可能有难度。
- **更好地利用后端服务器** - SSL/TLS 处理非常占用 CPU，并且密钥越大，占用的 CPU 就越多。 从后端服务器中解除此任务可使它们专注于以最有效的方式提供内容。
- **智能路由** - 解密流量后，应用程序网关便可以访问标头、URI 等请求内容，并可以使用此数据来路由请求。
- **证书管理** - 只需在应用程序网关上购买并安装证书，所有后端服务器都不需要证书。 这可以节省时间和开支。

若要配置 TLS 终止，需将一个 TLS/SSL 证书添加到侦听器，使应用程序网关能够根据 TLS/SSL 协议规范派生对称密钥。 然后，可以使用该对称密钥来加密和解密发送到网关的流量。 TLS/SSL 证书需采用个人信息交换 (PFX) 格式。 此文件格式适用于导出私钥，后者是应用程序网关对流量进行加解密所必需的。

> [!IMPORTANT] 
> 请注意，侦听器上的证书要求上传整个证书链。 


> [!NOTE] 
>
> 应用程序网关不提供任何用于创建新证书，或者将证书请求发送到证书颁发机构的功能。

若要建立 TLS 连接，需要确保 TLS/SSL 证书符合以下条件：

- 当前日期和时间处于证书上的“生效日期”和“失效日期”日期范围内。
- 证书的“公用名”(CN) 与请求中的主机头匹配。 例如，如果客户端向 `https://www.contoso.com/` 发出请求，则 CN 必须为 `www.contoso.com`。

### <a name="certificates-supported-for-tls-termination"></a>支持用于 TLS 终止的证书

应用程序网关支持以下类型的证书：

- CA（证书颁发机构）证书：CA 证书是证书颁发机构 (CA) 颁发的数字证书
- EV（扩展验证）证书：EV 证书是符合行业标准证书准则的证书。 使用此类证书会使浏览器地址栏变为绿色，并发布公司名称。
- 通配符证书：此证书支持任意数量的基于 *.site.com 的子域（其中的 * 需替换为你的子域）。 但是，它不支持 site.com，因此，如果用户在不键入前导“www”的情况下访问你的网站，通配符证书不会反映这一点。
- 自签名证书：客户端浏览器不信任这些证书，并且会警告用户，指出虚拟服务的证书不是信任链的一部分。 自签名证书适合用于测试，或者管理员会在其中控制客户端并且可以安全绕过浏览器安全警报的环境。 切勿将自签名证书用于生产工作负荷。

有关详细信息，请参阅[配置应用程序网关的 TLS 终止](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal)。

### <a name="size-of-the-certificate"></a>证书大小
查看[应用程序网关限制](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#application-gateway-limits)部分，了解支持的最大 TLS/SSL 证书大小。

## <a name="end-to-end-tls-encryption"></a>端到端 TLS 加密

你可能不希望与后端服务器进行未加密的通信。 你可能有安全要求、符合性要求，或者应用程序可能仅接受安全连接。 Azure 应用程序网关通过端到端 TLS 加密来支持这些要求。

当你使用应用程序网关的第 7 层负载均衡功能时，端到端 TLS 允许你加密敏感数据并将其安全地传输到后端。 这些功能包括：基于 Cookie 的会话相关性、基于 URL 的路由、基于站点的路由支持、能够重写或注入 X-Forwarded-* 标头，等等。

如果配置为端到端 TLS 通信模式，应用程序网关会在网关上终止 TLS 会话，并解密用户流量。 然后，它会应用配置的规则，以选择要将流量路由到的适当后端池实例。 应用程序网关接下来会初始化到后端服务器的新 TLS 连接，并先使用后端服务器的公钥证书重新加密数据，然后再将请求传输到后端。 来自 Web 服务器的任何响应都会经历相同的过程返回最终用户。 若要启用端到端 TLS，请将[后端 HTTP 设置](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http-settings)中的协议设置设为 HTTPS，然后再将其应用到后端池。

对于应用程序网关和 WAF v1 SKU，将同时向前端和后端流量应用 TLS 策略。 在前端，应用程序网关充当服务器并强制实施该策略。 在后端，应用程序网关充当客户端，并在 TLS 握手期间将协议/密码信息作为首选项发送。

对于应用程序网关和 WAF v2 SKU，仅向前端流量应用 TLS 策略，并向后端服务器提供所有密码，后端服务器可以控制在握手期间选择特定的密码和 TLS 版本。

应用程序网关仅与那些允许使用应用程序网关列出其证书的后端服务器进行通信，或使用其证书由已知 CA 颁发机构进行签名，证书的 CN 与 HTTP 后端设置中的主机名匹配。 这些服务器包括受信任的 Azure 服务，例如 Azure 应用服务/Web 应用和 Azure API 管理。

如果后端池成员的证书未由已知 CA 颁发机构签名，则必须为后端池中已启用端到端 TLS 的每个实例配置一个证书，这样才能进行安全通信。 添加证书后，可确保应用程序网关仅与已知后端实例通信。 从而进一步保护端到端通信。

> [!NOTE] 
>
> 添加到“后端 HTTP 设置”中用于对后端服务器进行身份验证的证书可以是添加到侦听器中用于在应用程序网关上实现 TLS 终止的同一个证书；为了增强安全性，也可以要求两者不同。

![端到端 TLS 方案][1]

此示例通过端到端 TLS 将使用 TLS1.2 的请求路由到 Pool1 中的后端服务器。

## <a name="end-to-end-tls-and-allow-listing-of-certificates"></a>端到端 TLS 和允许列出证书

应用程序网关仅与允许使用应用程序网关列出其证书的已知后端实例通信。 根据所用应用程序网关版本的不同，端到端 TLS 的设置过程也存在一些差异。 以下部分将分别对它们进行说明。

## <a name="end-to-end-tls-with-the-v1-sku"></a>v1 SKU 的端到端 TLS

若要为后端服务器启用端到端 TLS，并让应用程序网关将请求路由到后端服务器，运行状况探测必须成功并返回运行正常响应。

对于 HTTPS 运行状况探测，应用程序网关 v1 SKU 使用与要上传到 HTTP 设置的身份验证证书（后端服务器证书的公钥，而不是根证书）完全匹配的证书。

只允许连接到已知的和允许的后端。 运行状况探测将其余后端均视为运行不正常。 自签名证书仅用于测试目的，不建议用于生产工作负荷。 如前面的步骤中所述，此类证书必须允许使用应用程序网关列出，然后才能使用这些证书。

> [!NOTE]
> Azure 应用服务等受信任的 Azure 服务不需要进行身份验证和受信任的根证书设置。 默认情况下，它们被认为是可信的。

## <a name="end-to-end-tls-with-the-v2-sku"></a>v2 SKU 的端到端 TLS

身份验证证书已弃用，并由应用程序网关 v2 SKU 中的受信任的根证书替换。 它们的功能与身份验证证书类似，但有一些主要区别：

- 由 CN 与 HTTP 后端设置中的主机名匹配的已知 CA 颁发机构签名的证书不需要任何额外的步骤即可使端到端 TLS 工作。 

   例如，如果后端证书由知名 CA 颁发并具有 contoso.com 的 CN，并且后端 http 设置的主机字段也设置为 contoso.com，则不需要其他步骤。 可以将后端 http 设置协议设为 HTTPS，运行状况探测和数据路径都将启用 TLS。 如果使用 Azure 应用服务或其他 Azure Web 服务作为后端，则这些服务也是受隐式信任的，不需要对端到端 TLS 执行其他步骤。
   
> [!NOTE] 
>
> 为了使 TLS/SSL 证书受信任，后端服务器的证书必须由已知 CA 颁发。 如果证书不是由受信任的 CA 颁发的，应用程序网关会检查发证 CA 的证书是否由受信任的 CA 颁发，依此类推，直到找到受信任的 CA（此时会建立受信任的安全连接），或者直到找不到受信任的 CA（此时，应用程序网关会将后端标记为“运行不正常”）。 因此，建议后端服务器证书同时包含根 CA 和中间 CA。

- 如果证书是自签名证书，或是由未知中介签名的证书，那么，要在 v2 SKU 中启用端到端 TLS，必须定义受信任的根证书。 应用程序网关仅与符合以下条件的后端通信：其服务器证书的根证书与池关联的后端 http 设置中的受信任根证书列表之一匹配。

- 除了根证书匹配之外，应用程序网关 v2 还会验证后端 http 设置中指定的主机设置是否与后端服务器的 TLS/SSL 证书提供的公用名 (CN) 的主机设置相匹配。 尝试与后端建立 TLS 连接时，应用程序网关 v2 会将服务器名称指示 (SNI) 扩展设置为后端 http 设置中指定的主机。

- 如果在后端 http 设置中选择了 "**从后端目标选择主机名**" 而不是 "主机" 字段，则 SNI 标头始终设置为后端池 FQDN，而后端服务器 TLS/SSL 证书上的 CN 必须与它的 fqdn 匹配。 此方案不支持具有 IP 的后端池成员。

- 根证书是来自后端服务器证书的 base64 编码的根证书。

## <a name="sni-differences-in-the-v1-and-v2-sku"></a>v1 和 v2 SKU 中的 SNI 差异

如前所述，应用程序网关在应用程序网关侦听器处终止来自客户端的 TLS 流量（我们称其为前端连接），对流量进行解密，应用必要的规则来确定必须将请求转发到的后端服务器，并与后端服务器建立新的 TLS 会话（我们称其为后端连接）。

下表概述了 v1 和 v2 SKU 在前端和后端连接方面的 SNI 差异。

### <a name="frontend-tls-connection-client-to-application-gateway"></a>前端 TLS 连接（客户端到应用程序网关）

---
场景 | v1 | v2 |
| --- | --- | --- |
| 如果客户端指定了 SNI 标头，并且所有多站点侦听器都启用了“需要 SNI”标志 | 返回相应的证书；如果站点不存在（根据 server_name），则重置连接。 | 如果有相应的证书，则返回该证书；否则，返回配置的第一个 HTTPS 侦听器的证书（按顺序）|
| 如果客户端未指定 SNI 标头，并且所有多站点标头都启用了“需要 SNI” | 重置连接 | 返回配置的第一个 HTTPS 侦听器的证书（按顺序）
| 如果客户端未指定 SNI 标头，并且有配置了证书的基本侦听器 | 将基本侦听器中配置的证书返回给客户端（默认或备用证书） | 返回配置的第一个 HTTPS 侦听器的证书（按顺序） |

### <a name="backend-tls-connection-application-gateway-to-the-backend-server"></a>后端 TLS 连接（应用程序网关到后端服务器）

#### <a name="for-probe-traffic"></a>对于探测流量

---
场景 | v1 | v2 |
| --- | --- | --- |
| 将 TLS 握手期间的 SNI (server_name) 标头设置为 FQDN | 设置为后端池中的 FQDN。 根据 [RFC 6066](https://tools.ietf.org/html/rfc6066)，SNI 主机名中不允许使用原义 IPv4 和 IPv6 地址。 <br> **注意：** 后端池中的 FQDN 应通过 DNS 解析为后端服务器的 IP 地址（公共或专用） | 将 SNI 标头 (server_name) 设置为附加到 HTTP 设置的自定义探测中的主机名（如果已配置）；如果没有，则设置为 HTTP 设置中提到的主机名；如果前两项都没有，则设置为后端池中提到的 FQDN。 优先顺序为自定义探测 > HTTP 设置 > 后端池。 <br> **注意：** 如果在 HTTP 设置和自定义探测中配置的主机名不同，则根据优先级，SNI 将设置为自定义探测中的主机名。
| 如果后端池地址是 IP 地址 (v1) 或者自定义探测主机名配置为 IP 地址 (v2) | 不设置 SNI (server_name)。 <br> **注意：** 在这种情况下，后端服务器应该能够返回默认/备用证书，这应该允许在 "身份验证证书" 下的 "HTTP 设置" 中列出。 如果后端服务器中未配置默认/备用证书，但需要提供 SNI，则服务器可能会重置连接并导致探测失败 | 按照前面提到的优先顺序，如果它们的主机名为 IP 地址，则不设置 SNI（根据 [RFC 6066](https://tools.ietf.org/html/rfc6066)）。 <br> **注意：** 如果未配置自定义探测，并且未在 HTTP 设置或后端池中设置主机名，则也不会在 v2 探测中设置 SNI |

> [!NOTE] 
> 如果未配置自定义探测，应用程序网关将按以下格式（ \<protocol\> ：//127.0.0.1：/）发送默认探测 \<port\> 。 例如，对于默认的 HTTPS 探测，它将以 https://127.0.0.1:443/ 的形式发送。 请注意，此处提到的 127.0.0.1 仅用作 HTTP 主机标头，而不用作 SNI 标头（根据 RFC 6066）。 有关运行状况探测错误的详细信息，请查看[后端运行状况故障排除指南](application-gateway-backend-health-troubleshooting.md)。

#### <a name="for-live-traffic"></a>对于实时流量

---
场景 | v1 | v2 |
| --- | --- | --- |
| 将 TLS 握手期间的 SNI (server_name) 标头设置为 FQDN | 设置为后端池中的 FQDN。 根据 [RFC 6066](https://tools.ietf.org/html/rfc6066)，SNI 主机名中不允许使用原义 IPv4 和 IPv6 地址。 <br> **注意：** 后端池中的 FQDN 应通过 DNS 解析为后端服务器的 IP 地址（公共或专用） | 将 SNI 标头 (server_name) 设置为 HTTP 设置中的主机名；如果选择了“PickHostnameFromBackendAddress”选项，或者未提及主机名，则将其设置为后端池配置中的 FQDN
| 如果后端池地址是 IP 地址或者未在 HTTP 设置中设置主机名 | 根据 [RFC 6066](https://tools.ietf.org/html/rfc6066)，如果后端池条目不是 FQDN，则不设置 SNI | SNI 将设置为客户端的输入 FQDN 中的主机名，并且后端证书的 CN 必须与此主机名匹配。

## <a name="next-steps"></a>后续步骤

了解端到端 TLS 后，请转到[使用 PowerShell 通过应用程序网关配置端到端 TLS](application-gateway-end-to-end-ssl-powershell.md)，以创建使用端到端 TLS 的应用程序网关。

<!--Image references-->

[1]: ./media/ssl-overview/scenario.png
