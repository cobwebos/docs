---
title: 重写 Azure 应用程序网关的 HTTP 标头 | Microsoft Docs
description: 本文概述如何重写 Azure 应用程序网关中的 HTTP 标头
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 04/27/2020
ms.author: absha
ms.openlocfilehash: 421c1f4d1abe9be5f5081235e78ebe77b1813e6e
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82562230"
---
# <a name="rewrite-http-headers-with-application-gateway"></a>重写应用程序网关的 HTTP 标头

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

HTTP 标头可让客户端和服务器连同请求或响应一起传递附加的信息。 重写这些标头可以完成重要的任务，例如，添加安全相关的标头字段（如 HSTS/ X-XSS-Protection）、删除可能透露敏感信息的响应标头字段，以及从 X-Forwarded-For 标头中删除端口信息。

当请求和响应数据包在客户端与后端池之间移动时，可以通过应用程序网关添加、删除或更新 HTTP 请求和响应标头。 还可以通过它来添加条件，确保只有在满足特定条件的情况下才能重写指定标头。

应用程序网关还支持多个[服务器变量](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers#server-variables)，这些变量可帮助你存储有关请求和响应的附加信息。 这样，你就可以更轻松地创建强大的重写规则。

> [!NOTE]
>
> HTTP 标头重写支持仅适用于 [Standard_V2 和 WAF_v2 SKU](application-gateway-autoscaling-zone-redundant.md)。

![重写标头](media/rewrite-http-headers/rewrite-headers.png)

## <a name="supported-headers"></a>支持的标头

可以重写请求和响应中的所有标头，但 Host、Connection 和 Upgrade 标头除外。 还可以使用应用程序网关创建自定义标头，并将其添加到通过该网关路由的请求和响应。

## <a name="rewrite-conditions"></a>重写条件

可以使用重写条件来评估 HTTP(S) 请求和响应的内容，仅当满足其中一个或多个条件时，才执行标头重写。 应用程序网关使用以下类型的变量来评估 HTTP(S) 请求和响应的内容：

- 请求中的 HTTP 标头。
- 响应中的 HTTP 标头。
- 应用程序网关服务器变量。

可以使用条件来评估指定的变量是否存在、指定的变量是否与特定的值匹配，或指定的变量是否与特定的模式匹配。 使用 [Perl 兼容的正则表达式 (PCRE) 库](https://www.pcre.org/)来设置条件中的正则表达式模式匹配。 若要了解正则表达式语法，请参阅 [Perl 正则表达式主页](https://perldoc.perl.org/perlre.html)。

## <a name="rewrite-actions"></a>重写操作

使用重写操作指定要重写的请求和响应标头，以及标头的新值。 可以创建新标头、修改现有标头的值，或删除现有标头。 新标头或现有标头的值可设置为以下类型的值：

- 文本。
- 请求标头。 若要指定请求标头，需使用语法 {http_req_*headerName*}。
- 响应标头。 若要指定响应标头，需使用语法 {http_resp_*headerName*}。
- 服务器变量。 若要指定服务器变量，需使用语法 {var_*serverVariable*}。
- 文本、请求标头、响应标头和服务器变量的组合。

## <a name="server-variables"></a>服务器变量

应用程序网关使用服务器变量来存储有关服务器、与客户端建立的连接以及对连接的当前请求的有用信息。 例如，存储的信息包括客户端的 IP 地址和 Web 浏览器类型。 服务器变量会动态更改，例如，加载新页或发布表单时就会更改。 可以使用这些变量来评估重写条件和重写标头。 若要使用服务器变量的值重写标头，需要在语法 {var_*serverVariable*} 中指定这些变量

应用程序网关支持以下服务器变量：

| 变量名称 | 说明                                                  |
| -------------------------- | :----------------------------------------------------------- |
| add_x_forwarded_for_proxy  | X-Forwarded-For 客户端请求标头字段，其中追加了 IP1、IP2、IP3 等格式的 `client_ip` 变量（请参阅此表后面的解释）。 如果 X-Forwarded-For 字段不在客户端请求标头中，则 `add_x_forwarded_for_proxy` 变量等于 `$client_ip` 变量。 若要重写应用程序网关设置的 X-Forwarded-For 标头，使该标头仅包含 IP 地址而不包含端口信息，则此变量特别有用。 |
| ciphers_supported          | 客户端支持的加密法列表。          |
| ciphers_used               | 用于建立的 TLS 连接的密码字符串。 |
| client_ip                  | 客户端的 IP 地址，应用程序网关从中接收请求。 如果应用程序网关和发起方客户端的前面有反向代理，则 *client_ip* 将返回该反向代理的 IP 地址。 |
| client_port                | 客户端端口。                                                  |
| client_tcp_rtt             | 有关客户端 TCP 连接的信息。 在支持 TCP_INFO 套接字选项的系统上可用。 |
| client_user                | 使用 HTTP 身份验证时提供用于身份验证的用户名。 |
| host                       | 按此优先顺序排列：请求行中的主机名、Host 请求标头字段中的主机名，或与请求匹配的服务器名称。 示例：在请求*http://contoso.com:8080/article.aspx?id=123&title=fabrikam*中，主机值将为*contoso.com* |
| cookie_*name*              | *name* Cookie。                                            |
| http_method                | 用于发出 URL 请求的方法。 例如 GET 或 POST。 |
| http_status                | 会话状态。 例如 200、400 或 403。                       |
| http_version               | 请求协议。 通常为 HTTP/1.0、HTTP/1.1 或 HTTP/2.0。 |
| query_string               | 请求的 URL 中“?”后面的变量/值对列表。 示例：在请求*http://contoso.com:8080/article.aspx?id=123&title=fabrikam*中，query_string 值将为*id = 123&title = fabrikam* |
| received_bytes             | 请求的长度（包括请求行、标头和请求正文）。 |
| request_query              | 请求行中的参数。                                |
| request_scheme             | 请求方案：http 或 https。                            |
| request_uri                | 完整的原始请求 URI（带参数）。 示例：在请求*http://contoso.com:8080/article.aspx?id=123&title=fabrikam*中，request_uri 值将为 */article.aspx？ id = 123&title = fabrikam*   |
| sent_bytes                 | 发送到客户端的字节数。                             |
| server_port                | 接受请求的服务器端口。                 |
| ssl_connection_protocol    | 已建立 TLS 连接的协议。        |
| ssl_enabled                | 如果连接在 TLS 模式下运行，则为 "开"。 否则为空字符串。 |
| uri_path                   | 标识宿主中 web 客户端要访问的特定资源。 这是请求 URI 中没有参数的部分。 示例：在请求*http://contoso.com:8080/article.aspx?id=123&title=fabrikam*中，uri_path 值将为 */article.aspx*  |

## <a name="rewrite-configuration"></a>重写配置

若要配置 HTTP 标头重写，需要完成以下步骤。

1. 创建 HTTP 标头重写所需的对象：

   - **重写操作**：用于指定要重写的请求和请求标头字段，以及标头的新值。 可将一个或多个重写条件关联到一个重写操作。

   - **重写条件**：可选配置。 重写条件评估 HTTP(S) 请求和响应的内容。 如果 HTTP(S) 请求或响应与重写条件匹配，则会发生重写操作。

     如果将多个条件关联到一个操作，仅当满足所有条件时，才会发生该操作。 换言之，操作属于逻辑 AND 运算。

   - **重写规则**：包含多个重写操作/重写条件组合。

   - **规则序列**：帮助确定重写规则的执行顺序。 在一个重写集中使用多个重写规则时，此配置非常有用。 规则顺序值较小的重写规则最先运行。 如果为两个重写规则分配了相同的规则顺序，则执行顺序是不确定的。

   - **重写集**：包含将与请求路由规则关联的多个重写规则。

2. 将重写集 (*rewriteRuleSet*) 附加到路由规则。 重写配置将通过路由规则附加到源侦听器。 使用基本路由规则时，标头重写配置与源侦听器相关联，并且是全局标头重写。 使用基于路径的路由规则时，将在 URL 路径映射中定义标头重写配置。 在这种情况下，该规则只会应用到站点的特定路径区域。
   > [!NOTE]
   > URL 重写会改变标头，而不会更改路径的 URL。

可以创建多个 HTTP 标头重写集，并将每个重写集应用到多个侦听器。 但是，对于一个特定的侦听器，只能应用一个重写集。

## <a name="common-scenarios"></a>常见方案

下面是标头重写的一些常见使用方案。

### <a name="remove-port-information-from-the-x-forwarded-for-header"></a>从 X-Forwarded-For 标头中删除端口信息

应用程序网关先在所有请求中插入 X-Forwarded-For 标头，然后将请求转发到后端。 此标头是 IP 端口的逗号分隔列表。 在某些情况下，后端服务器只需在标头中包含 IP 地址。 你可以使用标头重写从 X-Forwarded-For 标头中删除端口信息。 为此，一种做法是将该标头设置为 add_x_forwarded_for_proxy 服务器变量：

![删除端口](media/rewrite-http-headers/remove-port.png)

### <a name="modify-a-redirection-url"></a>修改重定向 URL

当后端应用程序发送重定向响应时，你可能希望将客户端重定向到不同的 URL，而不是后端应用程序指定的 URL。 例如，当应用服务托管在应用程序网关后面，并要求客户端重定向到其相对路径时，你可能希望这样做。 （例如，从 contoso.azurewebsites.net/path1 重定向到 contoso.azurewebsites.net/path2。）

由于应用服务是多租户服务，因此它会使用请求中的主机标头将请求路由到正确的终结点。 应用服务的默认域名为 *. azurewebsites.net （例如 contoso.azurewebsites.net），不同于应用程序网关的域名（例如，contoso.com）。 因为来自客户端的原始请求将应用程序网关的域名（contoso.com）作为主机名，所以，应用程序网关会将主机名更改为 contoso.azurewebsites.net。 做出此更改的目的是使应用服务能够将请求路由到正确的终结点。

当应用服务发送重定向响应时，它会在其响应的位置标头中，使用它从应用程序网关收到的请求中的相同主机名。 因此，客户端将直接向 contoso.azurewebsites.net/path2 发出请求，而不是通过应用程序网关（contoso.com/path2）。 不应该绕过应用程序网关。

将 location 标头中的主机名设置为应用程序网关的域名即可解决此问题。

下面是替换主机名的步骤：

1. 创建一个包含条件的重写规则，该规则评估响应中的 location 标头是否包含 azurewebsites.net。 输入模式 `(https?):\/\/.*azurewebsites\.net(.*)$`。
1. 执行相应的操作来重写 location 标头，使其包含应用程序网关的主机名。 为此，请输入 `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` 作为标头值。

![修改 location 标头](media/rewrite-http-headers/app-service-redirection.png)

### <a name="implement-security-http-headers-to-prevent-vulnerabilities"></a>实现安全 HTTP 标头以防止漏洞

在应用程序响应中实现必要的标头可以修复多个安全漏洞。 这些安全标头包括 X-XSS-Protection、Strict-Transport-Security 和 Content-Security-Policy。 可以使用应用程序网关为所有响应设置这些标头。

![安全标头](media/rewrite-http-headers/security-header.png)

### <a name="delete-unwanted-headers"></a>删除不需要的标头

你可能想要从 HTTP 响应中删除透露敏感信息的标头。 例如，你可能想要删除后端服务器名称、操作系统或库详细信息等信息。 可以使用应用程序网关删除以下标头：

![删除标头](media/rewrite-http-headers/remove-headers.png)

### <a name="check-for-the-presence-of-a-header"></a>检查某个标头是否存在

可以在 HTTP 请求或响应标头中评估某个标头或服务器变量是否存在。 如果你希望只有存在特定的标头时执行标头重写，则此评估非常有用。

![检查某个标头是否存在](media/rewrite-http-headers/check-presence.png)

## <a name="limitations"></a>限制

- 如果响应中包含多个同名的标头，则重写其中某个标头的值会导致删除该响应中的其他标头。 这种情况往往出现于 Set-Cookie 标头，因为在一个响应中可以包含多个 Set-Cookie 标头。 例如，如果将应用服务与应用程序网关一起使用，并在应用程序网关上配置了基于 Cookie 的会话关联，则就会出现此类情况。 在这种情况下，响应将包含两个 Set-Cookie 标头：一个用于应用服务（例如 `Set-Cookie: ARRAffinity=ba127f1caf6ac822b2347cc18bba0364d699ca1ad44d20e0ec01ea80cda2a735;Path=/;HttpOnly;Domain=sitename.azurewebsites.net`），另一个用于应用程序网关关联（例如 `Set-Cookie: ApplicationGatewayAffinity=c1a2bd51lfd396387f96bl9cc3d2c516; Path=/`）。 在此情况下重写其中一个 Set-Cookie 标头可能会导致从响应中删除另一个 Set-Cookie 标头。

- 当应用程序网关配置为重定向请求或显示自定义错误页面时，不支持重写。

- 目前不支持重写 Connection、Upgrade 和 Host 标头。

- 标头名称可以包含任何字母数字字符和 [RFC 7230](https://tools.ietf.org/html/rfc7230#page-27) 中定义的特定符号。 目前不支持在标头名称中包含下划线 (\_) 特殊字符。

## <a name="next-steps"></a>后续步骤

若要了解如何重写 HTTP 标头，请参阅：

- [使用 Azure 门户重写 HTTP 标头](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers-portal)
- [使用 Azure PowerShell 重写 HTTP 标头](add-http-header-rewrite-rule-powershell.md)
