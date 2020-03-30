---
title: Azure 前门 - 对通配符域的支持
description: 本文可帮助您了解 Azure 前门如何支持在自定义域列表中映射和管理通配符域
services: frontdoor
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/10/2020
ms.author: sharadag
ms.openlocfilehash: c568c9cc5c57098385cc7399459ec656cdbfc305
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79537435"
---
# <a name="wildcard-domains"></a>通配符域

除了顶点域和子域之外，您还可以将通配符域名映射到前门配置文件的前端主机或自定义域列表。 在前门配置中设置通配符域可简化来自同一路由规则的 API、应用程序或网站的多个子域的流量路由行为，而无需修改配置以单独添加和/或指定每个子域。 例如，`customer1.contoso.com`您可以定义 的路由，`customer2.contoso.com`并通过`customerN.contoso.com`添加通配符域`*.contoso.com`来定义 和使用相同的路由规则。

支持通配符域解决的一些关键方案包括：

- 不再需要将前门的每个子域装上板，然后启用 HTTPS 为每个子域绑定证书。
- 如果应用程序添加了新的子域，则不再需要更改生产前门配置。 否则，它以前需要添加子域，将证书绑定到该子域，附加 Web 应用程序防火墙 （WAF） 策略，将域添加到不同的路由规则中。

> [!NOTE]
> 目前，通配符域仅通过 API、PowerShell 和 CLI 支持。 支持通过 Azure 门户添加管理通配符域不可用。

## <a name="adding-wildcard-domains"></a>添加通配符域

您可以在"前端主机或域"部分下登载通配符域。 与子域类似，前门验证通配符域是否有 CNAME 映射。 此 DNS 映射可以是直接的 CNAME`*.contoso.com`映射，`contoso.azurefd.net`如映射到或通过 afdverify`afdverify.contoso.com`临时映射（`afdverify.contoso.azurefd.net`如映射以验证通配符的 CNAME 映射）（Azure DNS 支持通配符记录）。

如果通配符域未达到最大值，则可以在前端主机中添加尽可能多的通配符域的单级子域。前端主机的限制。 定义子域与域的其余部分（来自通配符域）的不同路由或为特定子域使用不同的 WAF 策略可能需要此功能。 因此，`*.contoso.com`将允许添加`foo.contoso.com`，而无需再次证明域所有权，但不是`foo.bar.contoso.com`因为这不是`*.contoso.com`的单个级别的子域。 要添加`foo.bar.contoso.com`而不进行额外的域所有权验证`*.bar.contosonews.com`，则需要添加。

### <a name="limitations"></a>限制

1. 如果在给定的前门配置文件中添加了通配符域，则无法将相同的域添加到任何其他前门配置文件中。 
2. 如果在给定的前门配置文件中添加了通配符域，则无法将该通配符域的任何子域添加到其他前门或 Microsoft 配置文件中的 Azure CDN
3. 如果通配符域的子域在前门配置文件或 Microsoft 配置文件中的 Azure CDN 中添加，则无法将通配符域添加到任何其他前门配置文件。 
4. 如果两个配置文件（Microsoft 的前门或 Azure CDN）具有根域的各种子域，则无法在任一配置文件上添加通配符域。

## <a name="certificate-binding-for-wildcard-domains-and-its-subdomains"></a>通配符域及其子域的证书绑定

要接受通配符域上的 HTTPS 流量，必须在通配符域上启用 HTTPS。 通配符域的证书绑定需要通配符证书，也就是说，证书的主题名称还应具有通配符域。

> [!NOTE]
> 目前，仅使用您自己的自定义 SSL 证书选项才能为通配符域启用 HTTPS。 前门托管证书不能用于通配符域。 

您可以选择对子域使用密钥保管库中的相同通配符证书，或者支持对子域使用前门托管证书。
如果为通配符域添加了子域，并且通配符域已关联证书，则无法禁用此子域的 HTTPS。 默认情况下，子域将使用通配符域的证书绑定，除非被其他密钥保管库证书或前门托管证书覆盖。

## <a name="web-application-firewall-for-wildcard-domains-and-its-subdomains"></a>通配符域及其子域的 Web 应用程序防火墙

WAF 策略可以附加到与其他域类似的通配符域。 可以将不同的 WAF 策略应用于通配符域的子域。 对于子域，必须显式指定要使用的 WAF 策略，即使它与通配符域的策略相同。 子域**不会**自动从通配符域继承 WAF 策略。

如果方案不希望 WAF 为子域运行，则可以创建没有托管或自定义规则集的空白 WAF 策略。

## <a name="routing-rules-for-wildcard-domains-and-its-subdomains"></a>通配符域及其子域的路由规则

配置路由规则时，可以选择通配符域作为前端主机。 还可以对通配符域与子域具有不同的路由行为。 如[前门如何进行路由匹配](front-door-route-matching.md)，将在运行时选择不同路由规则域的最具体匹配。

> [!WARNING]
> 如果您有两个路由规则，如**路由**1 `*.foo.com/*` ：映射到后端池 A**和路由** `bar.foo.com/somePath/*` 2 ：映射到后端池 B，并且如果请求到达`bar.foo.com/anotherPath/*`，则客户端将看到失败，因为前门将找不到两个路由的任何匹配项。 这是因为根据我们的[路由匹配算法](front-door-route-matching.md)，前门会根据更具体的域匹配选择路由 2，但只能发现没有匹配的路径模式。 


## <a name="next-steps"></a>后续步骤

- 了解如何[创建 Front Door](quickstart-create-front-door.md)。
- 了解如何[在前门添加自定义域](front-door-custom-domain.md)。
- 了解如何[在自定义域上启用 HTTPS。](front-door-custom-domain-https.md)
