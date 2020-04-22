---
title: Azure 前门 - 对通配符域的支持
description: 本文可帮助您了解 Azure 前门如何支持在自定义域列表中映射和管理通配符域。
services: frontdoor
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/10/2020
ms.author: sharadag
ms.openlocfilehash: 6d8a6d6f0b05b9b7fd0144959c82b6a2c9e659a3
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2020
ms.locfileid: "81768317"
---
# <a name="wildcard-domains"></a>通配符域

除了顶点域和子域之外，您可以将通配符域名映射到 Azure 前门配置文件中的前端主机或自定义域列表。 在 Azure 前门配置中设置通配符域可简化来自同一路由规则的 API、应用程序或网站的多个子域的流量路由行为。 您无需修改配置就可以单独添加或指定每个子域。 `customer1.contoso.com`例如，您可以定义 的路由，`customer2.contoso.com`并使用`customerN.contoso.com`相同的路由规则并添加通配符域。 `*.contoso.com`

通过支持通配符域而改进的关键方案包括：

- 您无需将 Azure 前门配置文件中的每个子域装上板，然后启用 HTTPS 为每个子域绑定证书。
- 如果应用程序添加新子域，则不再需要更改生产 Azure 前门配置。 以前，您必须添加子域，将证书绑定到该子域，附加 Web 应用程序防火墙 （WAF） 策略，然后将域添加到不同的路由规则。

> [!NOTE]
> 目前，通配符域仅通过 API、PowerShell 和 Azure CLI 支持。 在 Azure 门户中添加和管理通配符域的支持不可用。

## <a name="adding-wildcard-domains"></a>添加通配符域

您可以在前端主机或域的节下添加通配符域。 与子域类似，Azure 前门验证通配符域是否有 CNAME 记录映射。 此 DNS 映射可以是直接的 CNAME`*.contoso.com`记录映射，`contoso.azurefd.net`就像映射到 。 或者，您可以使用 fd 验证临时映射。 例如，`afdverify.contoso.com`映射以`afdverify.contoso.azurefd.net`验证通配符的 CNAME 记录映射。

> [!NOTE]
> Azure DNS 支持 通配符记录。

您可以在前端主机中添加通配符域的尽可能多的单级子域，最高达前端主机的限制。 此功能可能需要用于：

- 为子域定义与域的其余部分（来自通配符域）不同的路由。

- 为特定子域使用不同的 WAF 策略。 例如，`*.contoso.com`允许添加`foo.contoso.com`，而无需再次证明域所有权。 但它不允许`foo.bar.contoso.com`，因为它不是`*.contoso.com`的单个级别子域。 要添加`foo.bar.contoso.com`而不进行额外的域所有权验证`*.bar.contosonews.com`，需要添加。

您可以添加具有某些限制的通配符域及其子域：

- 如果将通配符域添加到 Azure 前门配置文件：
  - 无法将通配符域添加到任何其他 Azure 前门配置文件中。
  - 无法将通配符域的第一级子域添加到其他 Azure 前门配置文件或 Azure 内容交付网络配置文件。
- 如果将通配符域的子域添加到 Azure 前门配置文件或 Azure 内容传递网络配置文件中，则无法将通配符域添加到其他 Azure 前门配置文件中。
- 如果两个配置文件（Azure 前门或 Azure 内容传递网络）具有根域的各种子域，则通配符域无法添加到任一配置文件中。

## <a name="certificate-binding"></a>证书绑定

要接受通配符域上的 HTTPS 流量，必须在通配符域上启用 HTTPS。 通配符域的证书绑定需要通配符证书。 也就是说，证书的主题名称还应具有通配符域。

> [!NOTE]
> 目前，仅使用您自己的自定义 SSL 证书选项才能为通配符域启用 HTTPS。 Azure 前门托管证书不能用于通配符域。

您可以选择使用 Azure 密钥保管库中的相同通配符证书，也可以从子域的 Azure 前门托管证书中使用相同的通配符证书。

如果为已具有与其关联的证书的通配符域添加了子域，则无法禁用子域的 HTTPS。 子域使用通配符域的证书绑定，除非其他密钥保管库或 Azure 前门托管证书覆盖它。

## <a name="waf-policies"></a>WAF 策略

WAF 策略可以附加到通配符域，类似于其他域。 可以将不同的 WAF 策略应用于通配符域的子域。 对于子域，必须指定要使用的 WAF 策略，即使它与通配符域的策略相同。 子域*不会*自动从通配符域继承 WAF 策略。

如果不希望 WAF 策略为子域运行，则可以创建没有托管或自定义规则集的空 WAF 策略。

## <a name="routing-rules"></a>路由规则

配置路由规则时，可以选择通配符域作为前端主机。 还可以对通配符域和子域具有不同的路由行为。 如[Azure 前门如何进行路由匹配](front-door-route-matching.md)中所述，在运行时选择跨不同路由规则的域的最具体匹配。

> [!IMPORTANT]
> 您必须跨路由规则具有匹配的路径模式，否则客户端将看到失败。 例如，您有两个路由规则，如路由`*.foo.com/*`1（映射到后端池 A）和路由 2（`bar.foo.com/somePath/*`映射到后端池 B）。 然后，请求到达`bar.foo.com/anotherPath/*`。 Azure 前门根据更具体的域匹配选择路由 2，但找不到跨路径的匹配路径模式。

## <a name="next-steps"></a>后续步骤

- 了解如何创建[Azure 前门配置文件](quickstart-create-front-door.md)。
- 了解如何在[Azure 前门添加自定义域](front-door-custom-domain.md)。
- 了解如何[在自定义域上启用 HTTPS。](front-door-custom-domain-https.md)
