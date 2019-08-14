---
title: Azure 应用程序网关重写 HTTP 标头 |Microsoft Docs
description: 本文概述如何在 Azure 应用程序网关中重写 HTTP 标头
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 08/08/2019
ms.author: absha
ms.openlocfilehash: b6f26eca0592017306eaefd3f5fecb544dc6fb36
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/09/2019
ms.locfileid: "68932193"
---
# <a name="rewrite-http-headers-with-application-gateway"></a>用应用程序网关重写 HTTP 标头

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

通过 HTTP 标头, 客户端和服务器可以通过请求或响应传递附加信息。 通过重写这些标头, 可以完成重要任务, 如添加安全相关标头字段 (如 HSTS/X-XSS 保护)、删除可能泄露敏感信息的响应标头字段, 以及删除中的端口信息。X-转发的标头。

当请求和响应数据包在客户端与后端池之间移动时，可以通过应用程序网关添加、删除或更新 HTTP 请求和响应标头。 还可以通过它来添加条件，确保只有在满足特定条件的情况下才能重写指定标头。

应用程序网关还支持多个[服务器变量](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers#server-variables), 可帮助你存储有关请求和响应的其他信息。 这使你可以更轻松地创建功能强大的重写规则。

> [!NOTE]
>
> HTTP 标头重写支持仅适用于[Standard_V2 和 WAF_V2 SKU](application-gateway-autoscaling-zone-redundant.md)。

![重写标头](media/rewrite-http-headers/rewrite-headers.png)

## <a name="supported-headers"></a>支持的标头

可以重写请求和响应中的所有标头, 但不包括主机、连接和升级标头。 你还可以使用应用程序网关创建自定义标头, 并将其添加到通过它路由的请求和响应。

## <a name="rewrite-conditions"></a>重写条件

您可以使用重写条件来评估 HTTP (S) 请求和响应的内容, 并且仅在满足一个或多个条件时才执行标头重写。 应用程序网关使用以下类型的变量来计算 HTTP (S) 请求和响应的内容:

- 请求中的 HTTP 标头。
- 响应中的 HTTP 标头。
- 应用程序网关服务器变量。

您可以使用条件来评估指定的变量是否存在、指定的变量是否与特定的值匹配, 或指定的变量是否与特定的模式相匹配。 使用[Perl 兼容的正则表达式 (PCRE) 库](https://www.pcre.org/)来设置条件中的正则表达式模式匹配。 若要了解正则表达式语法, 请参阅[Perl 正则表达式主页](https://perldoc.perl.org/perlre.html)。

## <a name="rewrite-actions"></a>重写操作

使用重写操作指定要重写的请求和响应标头以及标头的新值。 您可以创建新的标头、修改现有标头的值或删除现有的标头。 新标头或现有标头的值可以设置为以下类型的值:

- 全文.
- 请求标头。 若要指定请求标头, 需使用语法 {http_req_*headerName*}。
- 响应标头。 若要指定响应标头, 需使用语法 {http_resp_*headerName*}。
- 服务器变量。 若要指定服务器变量, 需要使用语法 {var_*serverVariable*}。
- 文本、请求标头、响应标头和服务器变量的组合。

## <a name="server-variables"></a>服务器变量

应用程序网关使用服务器变量来存储有关服务器、与客户端的连接以及连接上的当前请求的有用信息。 存储的信息的示例包括客户端的 IP 地址和 web 浏览器类型。 服务器变量会动态更改, 例如, 当加载新页面或发送表单时。 您可以使用这些变量来计算重写条件和重写标头。

应用程序网关支持以下服务器变量:

| 变量名称 | 描述                                                  |
| -------------------------- | :----------------------------------------------------------- |
| add_x_forwarded_for_proxy  | 带有`client_ip`变量 (请参阅此表后面的说明) 的 X 转发的客户端请求标头字段的格式为 IP1、IP2、IP3 等。 如果的 X 转发的字段不在客户端请求标头中, 则`add_x_forwarded_for_proxy`该变量等于`$client_ip`变量。 如果要重写应用程序网关设置的 X 转发的标头, 使标头只包含没有端口信息的 IP 地址, 则此变量特别有用。 |
| ciphers_supported          | 客户端支持的密码列表。          |
| ciphers_used               | 用于建立的 SSL 连接的密码字符串。 |
| client_ip                  | 应用程序网关从中接收请求的客户端的 IP 地址。 如果在应用程序网关和发起方的客户端之前有反向代理, 则*client_ip*将返回反向代理的 ip 地址。 |
| client_port                | 客户端端口。                                                  |
| client_tcp_rtt             | 有关客户端 TCP 连接的信息。 在支持 TCP_INFO 套接字选项的系统上可用。 |
| client_user                | 使用 HTTP 身份验证时, 提供用于身份验证的用户名。 |
| host                       | 此优先级顺序如下: 请求行中的主机名、主机请求标头字段中的主机名或与请求匹配的服务器名称。 |
| cookie_*name*              | *名称*cookie。                                            |
| http_method                | 用于发出 URL 请求的方法。 例如, GET 或 POST。 |
| http_status                | 会话状态。 例如, 200、400或403。                       |
| http_version               | 请求协议。 通常为 HTTP/1.0、HTTP/1.1 或 HTTP/2.0。 |
| query_string               | 请求的 URL 中的 "？" 后面的变量/值对的列表。 |
| received_bytes             | 请求的长度 (包括请求行、标头和请求正文)。 |
| request_query              | 请求行中的参数。                                |
| request_scheme             | 请求方案: http 或 https。                            |
| request_uri                | 完整的原始请求 URI (包含参数)。                   |
| sent_bytes                 | 发送到客户端的字节数。                             |
| server_port                | 接受请求的服务器的端口。                 |
| ssl_connection_protocol    | 已建立 SSL 连接的协议。        |
| ssl_enabled                | 如果连接在 SSL 模式下运行, 则为 "开"。 否则为空字符串。 |

## <a name="rewrite-configuration"></a>重写配置

若要配置 HTTP 标头重写, 需要完成以下步骤。

1. 创建 HTTP 标头重写所需的对象:

   - **重写操作**:用于指定要重写的请求和请求标头字段, 以及标头的新值。 可以将一个或多个重写条件与重写操作相关联。

   - **重写条件**:可选配置。 重写条件对 HTTP 请求和响应的内容进行评估。 如果 HTTP (S) 请求或响应与重写条件匹配, 则会发生重写操作。

     如果将多个条件与某个操作相关联, 则该操作仅在满足所有条件时才会发生。 换句话说, 操作是一个逻辑 AND 运算。

   - **重写规则**:包含多个重写操作/重写条件组合。

   - **规则序列**:帮助确定重写规则的执行顺序。 当重写集中有多个重写规则时, 此配置非常有用。 首先运行具有较低规则序列值的重写规则。 如果将同一规则序列分配给两个重写规则, 则执行顺序是不确定的。

   - **重写集**:包含将与请求路由规则关联的多个重写规则。

2. 将重写集 (*rewriteRuleSet*) 附加到路由规则。 重写配置通过路由规则附加到源侦听器。 使用基本路由规则时, 标头重写配置与源侦听器相关联, 并且是全局标头重写。 使用基于路径的路由规则时, 将在 URL 路径映射上定义标头重写配置。 在这种情况下, 它仅应用于站点的特定路径区。
   > [!NOTE]
   > URL 重写更改标头;它不会更改路径的 URL。

可以创建多个 HTTP 标头重写集, 并将每个重写集应用于多个侦听器。 但你只能将一个重写集应用于特定侦听器。

## <a name="common-scenarios"></a>常见方案

下面是使用标头重写的一些常见方案。

### <a name="remove-port-information-from-the-x-forwarded-for-header"></a>从 X 转发的标头中删除端口信息

在将请求转发到后端之前, 应用程序网关会在所有请求中插入一个 X 转发的标头。 此标头是一个逗号分隔的 IP 端口列表。 在某些情况下, 后端服务器只需要标头来包含 IP 地址。 您可以使用标头重写从 X 转发的标头中删除端口信息。 执行此操作的一种方法是将标头设置为 add_x_forwarded_for_proxy 服务器变量:

![删除端口](media/rewrite-http-headers/remove-port.png)

### <a name="modify-a-redirection-url"></a>修改重定向 URL

当后端应用程序发送重定向响应时, 你可能需要将客户端重定向到与后端应用程序指定的 URL 不同的 URL。 例如, 你可能想要在应用程序网关后托管应用服务时执行此操作, 并要求客户端执行到其相对路径的重定向。 (例如, 从 contoso.azurewebsites.net/path1 重定向到 contoso.azurewebsites.net/path2。)

由于应用服务是多租户服务, 因此它使用请求中的主机标头将请求路由到正确的终结点。 应用服务的默认域名为 *. azurewebsites.net (例如 contoso.azurewebsites.net), 不同于应用程序网关的域名 (例如, contoso.com)。 因为来自客户端的原始请求将应用程序网关的域名 (contoso.com) 作为主机名, 所以, 应用程序网关会将主机名更改为 contoso.azurewebsites.net。 它进行了此更改, 以便应用服务可以将请求路由到正确的终结点。

当应用服务发送重定向响应时, 它会在其响应的 location 标头中使用与从应用程序网关接收的主机名相同的主机名。 因此, 客户端将直接向 contoso.azurewebsites.net/path2 发出请求, 而不是通过应用程序网关 (contoso.com/path2)。 不需要跳过应用程序网关。

要解决此问题, 可以将 location 标头中的主机名设置为应用程序网关的域名。

下面是用于替换主机名的步骤:

1. 创建一个包含条件的重写规则, 该规则评估响应中的 location 标头是否包含 azurewebsites.net。 输入模式`(https?):\/\/.*azurewebsites\.net(.*)$`。
1. 执行操作以重写位置标头, 以使其具有应用程序网关的主机名。 输入`{http_resp_Location_1}://contoso.com{http_resp_Location_2}`作为标头值即可执行此操作。

![修改位置标头](media/rewrite-http-headers/app-service-redirection.png)

### <a name="implement-security-http-headers-to-prevent-vulnerabilities"></a>实现安全 HTTP 标头以防止漏洞

可以通过在应用程序响应中实现必需的标头来修复几个安全漏洞。 这些安全标头包括 X-XSS 保护、严格传输安全和内容安全策略。 可以使用应用程序网关为所有响应设置这些标头。

![安全标头](media/rewrite-http-headers/security-header.png)

### <a name="delete-unwanted-headers"></a>删除不需要的标头

你可能想要删除从 HTTP 响应中公开敏感信息的标头。 例如, 你可能想要删除后端服务器名称、操作系统或库详细信息等信息。 可以使用应用程序网关删除以下标头:

![删除标头](media/rewrite-http-headers/remove-headers.png)

### <a name="check-for-the-presence-of-a-header"></a>检查标头是否存在

您可以为存在标头或服务器变量的 HTTP 请求或响应标头进行评估。 如果要仅在存在特定标头时执行标头重写, 则此计算非常有用。

![检查标头是否存在](media/rewrite-http-headers/check-presence.png)

## <a name="limitations"></a>限制

- 如果响应具有多个具有相同名称的标头, 则重新编写其中一个标头的值将导致删除响应中的其他标头。 这种情况通常发生在设置 Cookie 标头中, 因为在一个响应中可以有多个设置 Cookie 标头。 这种情况的一种情况是, 将应用服务与应用程序网关一起使用, 并在应用程序网关上配置了基于 cookie 的会话相关性。 在这种情况下, 响应将包含两个设置 Cookie 的标头: 一个用于应用服务, `Set-Cookie: ARRAffinity=ba127f1caf6ac822b2347cc18bba0364d699ca1ad44d20e0ec01ea80cda2a735;Path=/;HttpOnly;Domain=sitename.azurewebsites.net`例如, 另一个用于应用程序网关关联, `Set-Cookie: ApplicationGatewayAffinity=c1a2bd51lfd396387f96bl9cc3d2c516; Path=/`例如。 在此方案中, 重写其中一个集 Cookie 标头可能会导致从响应中删除其他设置 Cookie 标头。

- 当前不支持重写连接、升级和主机标头。

- 标头名称可以包含[RFC 7230](https://tools.ietf.org/html/rfc7230#page-27)中定义的任何字母数字字符和特定符号。 当前不支持标头名称中\_有下划线 () 特殊字符。

## <a name="next-steps"></a>后续步骤

若要了解如何重写 HTTP 标头, 请参阅:

- [使用 Azure 门户重写 HTTP 标头](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers-portal)
- [使用 Azure PowerShell 重写 HTTP 标头](add-http-header-rewrite-rule-powershell.md)
