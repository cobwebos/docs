---
title: 在 Azure 应用程序网关中重写 HTTP 标头 | Microsoft Docs
description: 本文介绍在 Azure 应用程序网关中重写 HTTP 标头的功能概述
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 04/11/2019
ms.author: absha
ms.openlocfilehash: 20c484779e7ffe74ae01e33472b4cf8761d81b66
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2019
ms.locfileid: "59682674"
---
# <a name="rewrite-http-headers-with-application-gateway"></a>重写应用程序网关的 HTTP 标头

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

HTTP 标头可让客户端和服务器连同请求或响应一起传递附加的信息。 重写这些 HTTP 标头可以帮助你完成几个重要方案，例如，添加与安全相关的标头字段，例如 HSTS/X XSS 保护，删除响应标头字段的可能会泄露敏感信息，删除中的端口信息X-转发-对于标头，等等。应用程序网关支持的功能来添加、 删除或更新时请求的 HTTP 请求和响应头和响应数据包将客户端和后端池之间移动。 它提供的功能，若要添加条件以确保仅在满足某些条件时重新编写的指定标头。 此功能还支持多个[服务器变量](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers#server-variables)该帮助存储有关请求和响应，从而使你可以进行功能强大的重写规则的其他信息。
> [!NOTE]
>
> HTTP 标头重写支持仅用于 [新的 SKU [Standard_V2\]](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant)

![重写的标头](media/rewrite-http-headers/rewrite-headers.png)

## <a name="headers-supported-for-rewrite"></a>支持的标头重写

功能允许你重写请求和响应不包括主机、 连接和升级标头中的所有标头。 此外可以使用应用程序网关创建自定义标头并将其添加到的请求和响应通过它进行传送。 

## <a name="rewrite-conditions"></a>重写条件

使用条件可以评估该内容的 HTTP (S) 请求和响应，并执行一个标头重写仅在满足一个或多个条件时重写。 应用程序网关使用的以下 3 种类型的变量用于评估的 HTTP (S) 请求和响应内容：

- 在请求中的 HTTP 标头
- 在响应中的 HTTP 标头
- 应用程序网关服务器变量

可以使用条件来评估指定的变量是否存在，是否与指定的变量，完全匹配的特定值，或是否与指定的变量，完全匹配的特定模式。 [Perl 兼容正则表达式 (PCRE) 库](https://www.pcre.org/)用于实现中的条件匹配的正则表达式模式。 若要了解有关正则表达式语法，请参阅[Perl 正则表达式手册页](https://perldoc.perl.org/perlre.html)。

## <a name="rewrite-actions"></a>重写操作

重写操作用来指定你想要重写的请求和响应标头和原始标头需要被重写为的新值。 可以创建新的标头，、 修改现有标头的值或删除现有标头。 新的标头或现有的标头的值可以设置为以下类型的值：

- 文本 
- 请求标头：若要指定请求标头，需要使用语法 {http_req_*headerName*}
- 响应标头：为了指定响应标头，您需要使用语法 {http_resp_*headerName*}
- 服务器变量：为了指定服务器变量，您需要使用语法 {var_*serverVariable*}
- 文本、 请求标头、 响应标头和服务器变量的组合。

## <a name="server-variables"></a>服务器变量

应用程序网关使用服务器变量来存储上的连接，例如客户端的 IP 地址或 web 浏览器类型的服务器与客户端和当前请求的连接有关的有用信息。 这些变量更改动态，如当加载新页面或窗体发送。 这些服务器变量可用于计算重写条件并重新编写标头。 

应用程序网关支持以下服务器变量：

| 支持的服务器变量 | 描述                                                  |
| -------------------------- | :----------------------------------------------------------- |
| add_x_forwarded_for_proxy  | 包含"X-转发-对于"客户端请求标头字段与`client_ip`（下表中所述） 追加到它的格式 (IP1，IP2，IP3，...) 中的变量。如果"X-转发-对于"字段不是客户端请求标头中`add_x_forwarded_for_proxy`变量等于`$client_ip`变量。 此变量是在客户要重写的 X-转发-对于标头设置应用程序网关，以便在标头包含的 IP 地址将不带端口信息的方案中特别有用。 |
| ciphers_supported          | 返回客户端支持的加密列表          |
| ciphers_used               | 返回用于已建立的 SSL 连接的加密字符串 |
| client_ip                  | 从其应用程序网关接收请求的客户端的 IP 地址。 如果然后在应用程序网关和原始客户端之前, 的反向代理*client_ip*将返回反向代理服务器的 IP 地址。 |
| client_port                | 客户端端口                                                  |
| client_tcp_rtt             | 关于客户端 TCP 连接的信息；在支持 TCP_INFO 套接字选项的系统上可用 |
| client_user                | 使用 HTTP 身份验证时，为身份验证提供的用户名 |
| host                       | 按此优先顺序排列：请求行中的主机名，或“主机”请求标头字段中的主机名，或与请求匹配的服务器名 |
| cookie_*name*              | *名称*cookie                                            |
| http_method                | 用于发出 URL 请求的方法。 例如 GET，POST 等。 |
| http_status                | 会话状态，例如：200、400、403 等。                       |
| http_version               | 请求协议，通常为"HTTP/1.0"、"HTTP/1.1"或者"HTTP/2.0" |
| query_string               | 请求 URL 中“?”后面的变量-值对的列表。 |
| received_bytes             | 请求长度（包括请求行、标头和请求正文） |
| request_query              | 请求行中的参数                                |
| request_scheme             | 请求方案，“http”或者“https”                            |
| request_uri                | 完整原始请求 URI（带参数）                   |
| sent_bytes                 | 发送到客户端的字节数                             |
| server_port                | 接受请求的服务器端口                 |
| ssl_connection_protocol    | 返回已建立的 SSL 连接的协议        |
| ssl_enabled                | 如果连接以 SSL 模式运行，则为“开启”，否则为空字符串 |

## <a name="rewrite-configuration"></a>重写配置

若要配置 HTTP 标头重写，将需要：

1. 创建重写 http 标头所需的新对象：

   - **重写操作**： 用于指定请求和你想要重写的请求标头字段和原始标头需要被重写为的新值。 您可以选择使用重写操作将一个或多个重写条件相关联。

   - **重写条件**:它是一项可选配置。 如果添加重写条件，则会评估的 HTTP (S) 请求和响应的内容。 是否使用重写的条件匹配的 HTTP (S) 请求或响应，将基于执行与重写条件关联的重写操作的决策。 

     如果与之关联多个条件将执行操作，则该操作，仅当满足所有条件时，即，将执行逻辑与运算。

   - **重写规则**： 重写规则包含多个重写操作-重写条件组合。

   - **规则序列**： 有助于确定在其中的不同重写规则的顺序执行。 当有多个重写规则集中重写时，这非常有用。 获取首先执行具有较低的规则序列值的重写规则。 如果你提供两个重写规则的相同规则序列的执行顺序将非确定性。

   - **重写集**： 包含多个重写规则将关联到的请求路由规则。

2. 你将需要附加的重写集 (*rewriteRuleSet*) 与路由规则。 这是因为重写配置已附加到源侦听器通过路由规则。 使用基本路由规则时，标头重写配置与源侦听器相关联，并且是全局标头重写。 使用基于路径的路由规则时，将在 URL 路径映射中定义标头重写配置。 因此，它仅适用于站点的特定路径区域。

可以创建多个 http 标头重写集，并且每个重写集可应用于多个侦听器。 不过，您可以应用仅其中一个重写设置为特定的侦听程序。

## <a name="common-scenarios"></a>常见方案

下面介绍了一些常见应用场景需要标头重写。

### <a name="remove-port-information-from-the-x-forwarded-for-header"></a>从 X 转发的标头中删除的端口信息

它将请求转发到后端之前，应用程序网关将插入 X 转发的标头的所有请求。 此标头的格式是逗号分隔列表的 ip: port。 但是，可能是其中的后端服务器需要的标头，使其仅包含 IP 地址的方案。 用于完成这种情况下，可以使用标头重写从 X 转发的标头中删除该端口的信息。 若要执行此操作的一种方法是将该标头设置为 add_x_forwarded_for_proxy 服务器变量。 

![删除端口](media/rewrite-http-headers/remove-port.png)

### <a name="modify-the-redirection-url"></a>修改重定向 URL

当后端应用程序发送重定向响应时，您可能想要将客户端重定向到一个后端应用程序指定不同的 URL。 其中一种方案是应用服务应用程序网关后面托管并要求客户端进行重定向到其相对路径 （从重定向 contoso.azurewebsites.net/path1 到 contoso.azurewebsites.net/path2） 时。 

由于应用服务是多租户服务，它使用主机标头中请求路由到正确终结点。 应用服务具有的默认域名 *。 azurewebsites.net (假设 contoso.azurewebsites.net) 不同于应用程序网关的域名 (例如 contoso.com)。 由于原始请求从客户端应用程序网关的域名 contoso.com 的主机名称，应用程序网关更改为 contoso.azurewebsites.net，主机名，以便应用服务可以将它路由到正确的终结点。 当应用服务发送时的重定向响应时，它使用相同的主机名在其响应的 location 标头中为它接收从应用程序网关的请求中的一个。 因此，客户端将直接对 contoso.azurewebsites.net/path2，而无需通过应用程序网关 (contoso.com/path2) 的请求。 不需要绕过应用程序网关。 

可以通过设置主机名到应用程序网关的域名称 location 标头中解决此问题。 若要执行此操作，可以创建重写规则与条件的计算结果，如果在响应中的 location 标头包含通过输入 azurewebsites.net`(https?):\/\/.*azurewebsites\.net(.*)$`作为模式并执行某个操作来重写应用程序网关的 location 标头通过输入主机名`{http_resp_Location_1}://contoso.com{http_resp_Location_2}`作为标头值。

![修改位置标头](media/rewrite-http-headers/app-service-redirection.png)

### <a name="implement-security-http-headers-to-prevent-vulnerabilities"></a>实现安全 HTTP 标头，以防止出现漏洞

可以通过在应用程序响应中实现必要的标头修复多个安全漏洞。 部分这些安全标头包括 X XSS 保护、 严格传输安全性、 内容安全策略，等等。应用程序网关可用于设置这些标头的所有响应。

![安全标头](media/rewrite-http-headers/security-header.png)

### <a name="delete-unwanted-headers"></a>删除不需要的标头

您可能想要从披露敏感信息，例如后端服务器名称、 操作系统、 库详细信息，等等的 HTTP 响应中删除这些标头。应用程序网关可用于删除这些文字。

![删除标头](media/rewrite-http-headers/remove-headers.png)

### <a name="check-presence-of-a-header"></a>检查存在标头

您可以评估标头或服务器变量存在的 HTTP 请求或响应标头。 若要执行标头重写，仅当存在特定标头时，这很有用。

![检查存在标头](media/rewrite-http-headers/check-presence.png)

## <a name="limitations"></a>限制

- 重写的连接、 升级和主机标头尚不支持。

- 标头名称可以包含任何字母数字字符和 [RFC 7230](https://tools.ietf.org/html/rfc7230#page-27) 中定义的特定符号。 但是，目前不支持标头名称中的“下划线”(\_) 特殊字符。 

## <a name="need-help"></a>需要帮助？

如果需要关于此功能的任何帮助，请通过 [AGHeaderRewriteHelp@microsoft.com](mailto:AGHeaderRewriteHelp@microsoft.com) 与我们联系。

## <a name="next-steps"></a>后续步骤

若要了解如何重写 HTTP 标头，请参阅：

-  [使用 Azure 门户的重写 HTTP 标头](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers-portal)
-  [使用 Azure PowerShell 重写 HTTP 标头](add-http-header-rewrite-rule-powershell.md)
