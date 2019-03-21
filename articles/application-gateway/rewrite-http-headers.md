---
title: 在 Azure 应用程序网关中重写 HTTP 标头 | Microsoft Docs
description: 本文介绍在 Azure 应用程序网关中重写 HTTP 标头的功能概述
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 12/20/2018
ms.author: absha
ms.openlocfilehash: aedd81af8b5821b1f8032faad1896790804df2a0
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2019
ms.locfileid: "58119286"
---
# <a name="rewrite-http-headers-with-application-gateway-public-preview"></a>使用应用程序网关（公共预览版）重写 HTTP 标头

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

HTTP 标头可让客户端和服务器连同请求或响应一起传递附加的信息。 重写这些 HTTP 标头可帮助实现多个重要方案，例如，添加安全相关的标头字段（如 HSTS/ X-XSS-Protection），或者删除可能会透露敏感信息（如后端服务器名称）的响应标头字段。

应用程序网关现在支持重写传入 HTTP 请求和传出 HTTP 响应的标头。 当请求/响应数据包在客户端与后端池之间移动时，你可以添加、删除或更新 HTTP 请求和响应标头。 可以重写标准和非标准标头字段。

> [!NOTE]
> 
> HTTP 标头重写支持仅用于 [新的 SKU [Standard_V2\]](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant)

应用程序网关标头重写支持提供：

- **全局标头重写**：可以为与站点相关的所有请求和响应重写特定标头。
- **基于路径的标头重写**： 重写此类型可使标头重写这些请求和响应适用于仅在特定站点区域中，例如购物车区域表示由 /cart/\*。

根据这个更改，你需要：

1. 创建重写 http 标头所需的新对象： 
   - **RequestHeaderConfiguration**：此对象用于指定要重写的请求标头字段以及需要重写的原始标头的新值。
   - **ResponseHeaderConfiguration**：此对象用于指定要重写的响应标头字段以及需要重写的原始标头的新值。
   - **ActionSet**：此对象包含上面指定的请求和响应标头的配置。 
   - **RewriteRule**：此对象包含上面指定的所有“actionSets”。 
   - **RewriteRuleSet** - 此对象包含所有 rewriteRules，并且需要附加到（基本或基于路径的）请求路由规则。
2. 然后，需要使用路由规则附加重写规则集。 创建此重写配置后，会通过路由规则将其附加到源侦听器。 使用基本路由规则时，标头重写配置与源侦听器相关联，并且是全局标头重写。 使用基于路径的路由规则时，将在 URL 路径映射中定义标头重写配置。 因此，它仅适用于站点的特定路径区域。

可以创建多个 http 标头重写规则集，并且每个重写规则集可以应用于多个侦听器。 但是，对特定的侦听器只能应用一个 http 重写规则集。

可以把标头中的值重写为：

- 文本值。 

  *示例：* 

  ```azurepowershell-interactive
  $responseHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "Strict-Transport-Security" -  HeaderValue "max-age=31536000")
  ```

- 从另一个标头获取的值。 

  *示例 1：* 

  ```azurepowershell-interactive
  $requestHeaderConfiguration= New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "X-New-RequestHeader" -HeaderValue {http_req_oldHeader}
  ```

  > [!Note] 
  > 为了指定请求标头，需要使用语法：{http_req_headerName}

  *示例 2*：

  ```azurepowershell-interactive
  $responseHeaderConfiguration= New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "X-New-ResponseHeader" -HeaderValue {http_resp_oldHeader}
  ```

  > [!Note] 
  > 为了指定响应标头，需要使用语法：{http_resp_headerName}

- 从支持的服务器变量获取的值。

  *示例：* 

  ```azurepowershell-interactive
  $requestHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "Ciphers-Used" -HeaderValue "{var_ciphers_used}"
  ```

  > [!Note] 
  > 为了指定服务器变量，需要使用语法：{var_serverVariable}

- 上述的组合。

## <a name="server-variables"></a>服务器变量

服务器变量在 Web 服务器上存储有用的信息。 这些变量提供有关服务器、与客户端的连接以及连接上的当前请求（例如，客户端的 IP 地址或 Web 浏览器类型）的信息。 这些信息在加载新页面或发布表单等时会动态更改。  使用这些变量，用户可以设置请求标头和响应标头。 

此功能支持将标头重写为以下服务器变量：

| 支持的服务器变量 | 描述                                                  |
| -------------------------- | :----------------------------------------------------------- |
| ciphers_supported          | 返回客户端支持的加密列表          |
| ciphers_used               | 返回用于已建立的 SSL 连接的加密字符串 |
| client_port                | 客户端端口                                                  |
| client_tcp_rtt             | 关于客户端 TCP 连接的信息；在支持 TCP_INFO 套接字选项的系统上可用 |
| client_user                | 使用 HTTP 身份验证时，为身份验证提供的用户名 |
| host                       | 按此优先顺序排列：请求行中的主机名，或“主机”请求标头字段中的主机名，或与请求匹配的服务器名 |
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

## <a name="limitations"></a>限制

- 这种重写 HTTP 标头的功能目前只能通过 Azure PowerShell、Azure API 和 Azure SDK 获取。 不久即可通过门户和 Azure CLI 获取支持。

- 仅 新的 SKU [Standard_V2](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant) 支持 HTTP 标头重写支持。 旧的 SKU 将不支持此功能。

- 尚不支持重写 Connect、Upgrade 和 Host 标头。

- 尚不支持两个重要的服务器变量 client_ip（发出请求的客户端的 IP 地址）和 cookie_name（“名称”cookie）。 client_ip 服务器变量对于客户打算重写应用程序网关设置的 x-forwarding-for 标头的情况特别有用，这样此标头将仅包含客户端的 IP 地址，而不包含端口信息。

  即将支持这两个服务器变量。

- 不久后会推出获取有条件地重写 http 标头的功能。

- 标头名称可以包含任何字母数字字符和 [RFC 7230](https://tools.ietf.org/html/rfc7230#page-27) 中定义的特定符号。 但是，目前不支持标头名称中的“下划线”(\_) 特殊字符。 

## <a name="need-help"></a>需要帮助？

如果需要关于此功能的任何帮助，请通过 [AGHeaderRewriteHelp@microsoft.com](mailto:AGHeaderRewriteHelp@microsoft.com) 与我们联系。

## <a name="next-steps"></a>后续步骤

了解重写 HTTP 标头的功能后，请转到[创建重写 HTTP 标头的自动缩放和区域冗余应用程序网关](tutorial-http-header-rewrite-powershell.md)，或[在现有的自动缩放和区域冗余应用程序网关中重写 HTTP 标头](add-http-header-rewrite-rule-powershell.md)
