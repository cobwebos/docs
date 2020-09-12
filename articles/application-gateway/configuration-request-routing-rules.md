---
title: Azure 应用程序网关请求路由规则配置
description: 本文介绍如何配置 Azure 应用程序网关请求路由规则。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: surmb
ms.openlocfilehash: 20a665eefbb73f062f1f036e17b16da891a43eef
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/10/2020
ms.locfileid: "89652861"
---
# <a name="application-gateway-request-routing-rules"></a>应用程序网关请求路由规则

使用 Azure 门户创建应用程序网关时，将 (*rule1*) 创建默认规则。 此规则会将默认侦听器 (*appGatewayHttpListener*) 绑定到默认后端池 (*appGatewayBackendPool*) 和默认后端 HTTP 设置 (*appGatewayBackendHttpSettings*)。 创建网关后，可以编辑该默认规则的设置，或创建新的规则。

## <a name="rule-type"></a>规则类型

创建规则时，可以选择[“基本”或“基于路径”](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#request-routing-rules)。 

- 若要将关联的侦听器（例如 *blog<i></i>.contoso.com/\** ）上的所有请求转发到单个后端池，请选择“基本”。
- 若要将来自特定 URL 路径的请求路由到特定的后端池，请选择“基于路径”。 路径模式仅应用到 URL 的路径，而不应用到该 URL 的查询参数。

### <a name="order-of-processing-rules"></a>规则的处理顺序

使用 v1 和 v2 SKU 时，将按照路径在基于路径的规则的 URL 路径映射中列出的顺序处理传入请求的模式匹配。 如果某个请求与 URL 路径映射中的两个或更多个路径的模式相匹配，则会匹配最先列出的路径。 请求将转发到与该路径关联的后端。

## <a name="associated-listener"></a>关联的侦听器

将一个侦听器关联到该规则，以评估与该侦听器关联的请求路由规则，从而确定请求要路由到的后端池。

## <a name="associated-back-end-pool"></a>关联的后端池

将规则关联到包含后端目标的后端池，该池为侦听器收到的请求提供服务。

 - 如果使用基本规则，则只允许一个后端池。 关联的侦听器上的所有请求将转发到该后端池。

 - 如果使用基于路径的规则，请添加对应于每个 URL 路径的多个后端池。 与输入的 URL 路径匹配的请求将转发到相应的后端池。 另请添加默认后端池。 与规则中的任何 URL 路径都不匹配的请求将转发到该池。

## <a name="associated-back-end-http-setting"></a>关联的后端 HTTP 设置

为每个规则添加后端 HTTP 设置。 系统使用此设置中指定的端口号、协议和其他信息，将请求从应用程序网关路由到后端目标。

如果使用基本规则，则只允许一个后端 HTTP 设置。 系统会使用此 HTTP 设置将关联的侦听器上的所有请求转发到相应的后端目标。

如果使用基于路径的规则，请添加对应于每个 URL 路径的多个后端 HTTP 设置。 系统使用对应于每个 URL 路径的 HTTP 设置，将与此设置中的 URL 路径匹配的请求转发到相应的后端目标。 另请添加默认 HTTP 设置。 系统会使用默认 HTTP 设置，将与此规则中的任何 URL 路径都不匹配的请求转发到默认后端池。

## <a name="redirection-setting"></a>重定向设置

如果为基本规则配置了重定向，则关联的侦听器上的所有请求将重定向到目标。 此过程称为全局重定向。 如果为基于路径的规则配置了重定向，则只会重定向特定站点区域中的请求。 区域的示例包括 */cart/\** 表示的购物车区域。 此过程称为基于路径的重定向。

有关重定向的详细信息，请参阅[应用程序网关重定向概述](redirect-overview.md)。

### <a name="redirection-type"></a>重定向类型

选择所需的重定向类型：*Permanent(301)* 、*Temporary(307)* 、*Found(302)* 或 *See other(303)* 。

### <a name="redirection-target"></a>重定向目标

选择另一个侦听器或外部站点作为重定向目标。

#### <a name="listener"></a>侦听器

选择侦听器作为重定向目标可将来自网关上的一个侦听器的流量重定向到另一个侦听器。 想要启用 HTTP 到 HTTPS 的重定向时，必须指定此设置。 此设置将来自源侦听器（用于检查 HTTP 请求）的流量重定向到目标侦听器（用于检查传入的 HTTPS 请求）。 还可以选择在转发到重定向目标的请求中包含来自原始请求的查询字符串和路径。

![应用程序网关组件对话框](./media/configuration-overview/configure-redirection.png)

有关 HTTP 到 HTTPS 的重定向的详细信息，请参阅：
- [使用 Azure 门户配置 HTTP 到 HTTPS 的重定向](redirect-http-to-https-portal.md)
- [使用 PowerShell 配置 HTTP 到 HTTPS 的重定向](redirect-http-to-https-powershell.md)
- [使用 Azure CLI 配置 HTTP 到 HTTPS 的重定向](redirect-http-to-https-cli.md)

#### <a name="external-site"></a>外部站点

若要将与此类规则关联的侦听器上的流量重定向到外部站点，请选择外部站点。 可以选择在转发到重定向目标的请求中包含来自原始请求的查询字符串。 无法将原始请求中的路径转发到外部站点。

有关重定向的详细信息，请参阅：
- [使用 PowerShell 将流量重定向到外部站点](redirect-external-site-powershell.md)
- [使用 CLI 将流量重定向到外部站点](redirect-external-site-cli.md)

## <a name="rewrite-http-headers-and-url"></a>重写 HTTP 标头和 URL

通过使用重写规则，当请求和响应数据包通过应用程序网关在客户端和后端池之间移动时，你可以添加、删除或更新 HTTP(S) 请求和响应标头以及 URL 路径和查询字符串参数。

这些标头和 URL 参数可以设置为静态值，也可以设置为其他标头和服务器变量。 这有助于处理重要的用例，例如提取客户端 IP 地址、删除有关后端的敏感信息、添加更多安全性等。
有关详细信息，请参阅：

 - [重写 HTTP 标头和 URL 概述](rewrite-http-headers-url.md)
 - [配置 HTTP 标头重写](rewrite-http-headers-portal.md)
 - [配置 URL 重写](rewrite-url-portal.md)

## <a name="next-steps"></a>后续步骤

- [了解 HTTP 设置](configuration-http-settings.md)