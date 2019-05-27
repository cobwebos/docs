---
title: 重写通过 Azure 应用程序网关的 HTTP 标头 |Microsoft Docs
description: 本文提供重写 HTTP 标头中 Azure 应用程序网关的概述
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 04/29/2019
ms.author: absha
ms.openlocfilehash: ebb14d97273851585e491e3bcd36f776ec9b61b4
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/22/2019
ms.locfileid: "66000972"
---
# <a name="rewrite-http-headers-with-application-gateway"></a>重写应用程序网关的 HTTP 标头

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

HTTP 标头允许客户端和服务器将与请求或响应的附加信息传递。 可以通过重写这些标头，完成重要任务，例如添加如 HSTS / X XSS 保护的与安全相关的标头字段中，删除可能会披露敏感信息的响应标头字段和删除中的端口信息X-转发-对于标头。

应用程序网关，可添加、 删除或更新时请求的 HTTP 请求和响应头和响应数据包的客户端和后端池之间移动。 它允许您添加条件以确保仅在满足某些条件时重新编写的指定标头。

应用程序网关还支持多个[服务器变量](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers#server-variables)，可以帮助您将存储有关请求和响应的其他信息。 这使你更轻松地创建功能强大的重写规则。

> [!NOTE]
>
> HTTP 标头重写支持功能仅适用于[Standard_V2 和 WAF_v2 SKU](application-gateway-autoscaling-zone-redundant.md)。

![重写的标头](media/rewrite-http-headers/rewrite-headers.png)

## <a name="supported-headers"></a>受支持的标头

您可以重写请求和响应，除了主机、 连接和升级标头中的所有标头。 此外可以使用应用程序网关创建自定义标头并将其添加到的请求和响应通过它进行传送。

## <a name="rewrite-conditions"></a>重写条件

重写条件可用于评估的 HTTP (S) 请求和响应的内容并执行标头重写之一时仅或满足更多条件。 应用程序网关使用这些类型的变量来评估的 HTTP (S) 请求和响应内容：

- 在请求中的 HTTP 标头。
- 在响应中的 HTTP 标头。
- 应用程序网关服务器变量。

可以使用条件来评估指定的变量是否存在，是指定的变量匹配特定值，还是指定的变量与特定模式匹配。 您使用[Perl 兼容正则表达式 (PCRE) 库](https://www.pcre.org/)设置中的条件匹配的正则表达式模式。 若要了解有关正则表达式语法，请参阅[Perl 的正则表达式主页](https://perldoc.perl.org/perlre.html)。

## <a name="rewrite-actions"></a>重写操作

重写操作用于指定你想要重写的请求和响应标头和标头的新值。 可以创建新的标头，、 修改现有标头的值或删除现有标头。 可以对这些类型的值设置新的标头或现有的标头的值：

- 文本。
- 请求标头。 若要指定请求标头，您需要使用语法 {http_req_*headerName*}。
- 响应标头。 若要指定响应标头，需要使用语法 {http_resp_*headerName*}。
- 服务器变量。 若要指定服务器变量，您需要使用语法 {var_*serverVariable*}。
- 文本、 请求标头、 响应标头和服务器变量的组合。

## <a name="server-variables"></a>服务器变量

应用程序网关使用服务器变量来存储有关服务器的有用信息、 与客户端和当前请求的连接的连接上。 存储信息的示例包括客户端的 IP 地址和 web 浏览器类型。 服务器变量动态更改，例如，新的页面加载时或发布窗体时。 这些变量可用于计算重写条件并重新编写标头。

应用程序网关支持这些服务器变量：

| 变量名称 | 描述                                                  |
| -------------------------- | :----------------------------------------------------------- |
| add_x_forwarded_for_proxy  | X-转发-适用于客户端请求标头字段与`client_ip`变量 （请参阅此表中的说明更高版本） 追加到该日志中的格式 IP1，IP2、 IP3，依此类推。 如果 X-转发-对于字段不在客户端请求标头`add_x_forwarded_for_proxy`变量等于`$client_ip`变量。 当你想要重写的 X-转发-对于标头设置应用程序网关，以便在标头包含的 IP 地址将不带端口信息时，此变量是特别有用。 |
| ciphers_supported          | 客户端支持的密码的列表。          |
| ciphers_used               | 用于建立 SSL 连接的密码的字符串。 |
| client_ip                  | 从其应用程序网关接收请求客户端的 IP 地址。 如果应用程序网关和原始客户端之前, 的反向代理*client_ip*将返回反向代理服务器的 IP 地址。 |
| client_port                | 客户端端口。                                                  |
| client_tcp_rtt             | 有关客户端 TCP 连接的信息。 支持 TCP_INFO 套接字选项的系统上可用。 |
| client_user                | 当使用 HTTP 身份验证时，进行身份验证提供用户名。 |
| host                       | 在以下优先顺序： 请求行中的主机名称、 主机请求标头字段中，来自的主机名或服务器名称匹配的请求。 |
| cookie_*name*              | *名称*cookie。                                            |
| http_method                | 用来发出 URL 请求的方法。 例如，GET 或 POST。 |
| http_status                | 会话状态中。 例如，200、 400 或 403。                       |
| http_version               | 请求的协议。 通常 HTTP/1.0、 HTTP/1.1 或 HTTP/2.0。 |
| query_string               | 后面的变量/值对的列表"？"中所请求的 URL。 |
| received_bytes             | 请求 （包括请求行、 标题和请求正文） 的长度。 |
| request_query              | 请求行中的自变量。                                |
| request_scheme             | 请求方案： http 或 https。                            |
| request_uri                | 完整原始请求 URI （与自变量）。                   |
| sent_bytes                 | 向客户端发送的字节数。                             |
| server_port                | 已接受请求的服务器的端口。                 |
| ssl_connection_protocol    | 建立 SSL 连接的协议。        |
| ssl_enabled                | "On"如果连接处于 SSL 模式下运行。 否则为为空字符串。 |

## <a name="rewrite-configuration"></a>重写配置

若要配置 HTTP 标头重写时，需要完成以下步骤。

1. 创建所需的 HTTP 标头重写的对象：

   - **重写操作**:用于指定请求和你想要重写的请求标头字段和标头的新值。 您可以将一个相关联或详细信息重写与重写操作的条件。

   - **重写条件**:一项可选配置。 重写条件评估结果的 HTTP (S) 请求和响应的内容。 如果 HTTP (S) 请求或响应与重写条件匹配时，将发生的重写操作。

     如果将多个条件与操作相关联，仅当满足所有条件时，则会发生操作。 换而言之，该操作是逻辑 AND 运算。

   - **重写规则**:包含多个重写操作 / 重写条件组合。

   - **规则序列**:有助于确定重写规则的执行的顺序。 重写集中有多个重写规则时，此配置非常有用。 首先运行具有较低的规则顺序值的重写规则。 如果将相同的规则序列分配到两个重写规则，请执行顺序是不确定。

   - **重写集**:包含将请求路由规则与相关联的多个重写规则。

2. 附加重写设置 (*rewriteRuleSet*) 到路由规则。 重写配置附加到源侦听器通过路由规则。 当您使用基本的路由规则时，标头重写配置与源侦听器相关联，并全局标头重写。 当使用基于路径的路由规则时，URL 路径映射定义标头重写配置。 在这种情况下，它仅适用于站点的特定路径区域。

您可以创建多个 HTTP 标头重写集和应用设置多个侦听器为每个重写。 但您可以将应用仅其中一个重写设置为特定的侦听程序。

## <a name="common-scenarios"></a>常见方案

以下是使用标头重写的一些常见方案。

### <a name="remove-port-information-from-the-x-forwarded-for-header"></a>从 X 转发的标头中删除的端口信息

应用程序网关将 X 转发的标头插入到的所有请求，它将请求转发到后端之前。 此标头是以逗号分隔列表的 IP 端口。 可能的后端服务器只需要标头以包含 IP 地址的方案。 可以使用标头重写从 X 转发的标头中删除该端口的信息。 若要执行此操作的一种方法是将该标头设置为 add_x_forwarded_for_proxy 服务器变量：

![删除端口](media/rewrite-http-headers/remove-port.png)

### <a name="modify-a-redirection-url"></a>修改重定向 URL

当后端应用程序发送重定向响应时，你可能想要将客户端重定向到一个后端应用程序指定不同的 URL。 例如，你可能想要执行此操作时应用服务应用程序网关后面托管并要求客户端执行操作的重定向到其相对路径。 （例如，从 contoso.azurewebsites.net/path1 重定向到 contoso.azurewebsites.net/path2。）

由于应用服务是多租户服务，它使用主机标头在请求中将请求路由到正确的终结点。 应用服务具有的默认域名 *。 azurewebsites.net (假设 contoso.azurewebsites.net) 不同于应用程序网关的域名 (例如 contoso.com)。 由于原始请求从客户端应用程序网关的域名 (contoso.com) 作为主机名，应用程序网关将更改为 contoso.azurewebsites.net 的主机名。 它使此更改，以便应用服务可以将请求路由到正确的终结点。

当应用服务发送时的重定向响应时，它使用相同的主机名在其响应的 location 标头中为它接收从应用程序网关的请求中的一个。 因此在客户端将直接对 contoso.azurewebsites.net/path2 而无需通过应用程序网关 (contoso.com/path2) 的请求。 绕过应用程序网关时，不是很理想。

可以通过设置主机名到应用程序网关的域名称 location 标头中来解决此问题。

下面是用于替换主机名的步骤：

1. 如果在响应中的 location 标头包含 azurewebsites.net 计算结果的条件创建重写规则。 输入模式`(https?):\/\/.*azurewebsites\.net(.*)$`。
1. 执行某个操作才能重写的 location 标头，以便它具有应用程序网关的主机名。 执行此操作通过输入`{http_resp_Location_1}://contoso.com{http_resp_Location_2}`作为标头值。

![修改位置标头](media/rewrite-http-headers/app-service-redirection.png)

### <a name="implement-security-http-headers-to-prevent-vulnerabilities"></a>实现安全 HTTP 标头，以防止出现漏洞

可以通过在应用程序响应中实现必要的标头来解决几个安全漏洞。 这些安全标头包括 X XSS 保护、 严格传输安全性和内容安全策略。 应用程序网关可用于设置这些标头的所有响应。

![安全标头](media/rewrite-http-headers/security-header.png)

### <a name="delete-unwanted-headers"></a>删除不需要的标头

您可能想要删除披露敏感信息的 HTTP 响应中的标头。 例如，你可能想要删除以下信息： 后端服务器名称、 操作系统或库详细信息。 应用程序网关可用于删除这些标头：

![删除标头](media/rewrite-http-headers/remove-headers.png)

### <a name="check-for-the-presence-of-a-header"></a>检查存在的一个标头

您可以评估的 HTTP 请求或响应头的标头或服务器变量存在。 当你想要执行仅当存在时某些标头的标头重写时，此评估非常有用。

![检查存在标头](media/rewrite-http-headers/check-presence.png)

## <a name="limitations"></a>限制

- 重写连接、 升级和主机标头目前不支持。

- 标头名称可以包含任何字母数字字符和特定符号中定义[RFC 7230](https://tools.ietf.org/html/rfc7230#page-27)。 我们暂不支持下划线字符 (\_) 标头名称中的特殊字符。

- 如果响应具有多个具有相同名称的标头，然后重写这些标头之一的值将导致在响应中删除其他标头。

## <a name="next-steps"></a>后续步骤

若要了解如何重写 HTTP 标头，请参阅：

- [使用 Azure 门户的重写 HTTP 标头](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers-portal)
- [使用 Azure PowerShell 重写 HTTP 标头](add-http-header-rewrite-rule-powershell.md)
