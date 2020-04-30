---
title: Azure 前门-对通配符域的支持
description: 本文将帮助你了解 Azure 前门如何支持在自定义域列表中映射和管理通配符域。
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81768317"
---
# <a name="wildcard-domains"></a>通配符域

除了顶点域和子域，你还可以将通配符域名映射到你的 Azure 前门配置文件中的前端主机或自定义域列表。 如果在 Azure 前门配置中拥有通配符域，则会为同一路由规则中的 API、应用程序或网站的多个子域简化流量路由行为。 无需修改配置即可单独添加或指定每个子域。 例如，你可以通过使用相同的路由规则`customer1.contoso.com`并`customer2.contoso.com`添加通配符`customerN.contoso.com`域`*.contoso.com`来定义、和的路由。

支持通配符域的主要方案包括：

- 无需在 Azure 前门配置文件中载入每个子域，然后启用 HTTPS 即可为每个子域绑定证书。
- 如果应用程序添加新的子域，则不再需要更改生产 Azure 前门配置。 以前，你必须添加子域，将证书绑定到该子域，附加 web 应用程序防火墙（WAF）策略，然后将该域添加到不同的路由规则。

> [!NOTE]
> 目前仅支持通过 API、PowerShell 和 Azure CLI 来支持通配符域。 对在 Azure 门户中添加和管理通配符域的支持不可用。

## <a name="adding-wildcard-domains"></a>添加通配符域

你可以在前端主机或域的部分下添加通配符域。 类似于子域，Azure 前门验证是否存在通配符域的 CNAME 记录映射。 此 DNS 映射可以是`*.contoso.com`映射到`contoso.azurefd.net`的直接 CNAME 记录映射。 或者，可以使用 afdverify 临时映射。 例如， `afdverify.contoso.com`映射以`afdverify.contoso.azurefd.net`验证通配符的 CNAME 记录映射。

> [!NOTE]
> Azure DNS 支持 通配符记录。

可以在前端主机中添加通配符域的任意多个下级子域，最多可添加到前端主机的限制。 此功能可能是必需的：

- 定义子域的其他路由，而不是域的其余部分（来自通配符域）。

- 针对特定子域具有不同的 WAF 策略。 例如， `*.contoso.com`允许添加`foo.contoso.com` ，而无需再次证明域所有权。 但不允许`foo.bar.contoso.com`这样做，因为它不是的单个`*.contoso.com`级别的子域。 若要`foo.bar.contoso.com`添加而不添加其他域`*.bar.contosonews.com`所有权验证，需要添加。

你可以添加通配符域及其子域，但有一些限制：

- 如果将通配符域添加到 Azure 前门配置文件：
  - 不能将通配符域添加到任何其他 Azure 前门配置文件。
  - 不能将通配符域的第一级子域添加到另一个 Azure 前门配置文件或 Azure 内容分发网络配置文件。
- 如果将通配符域的子域添加到 Azure 前门配置文件或 Azure 内容分发网络配置文件，则不能将通配符域添加到其他 Azure 前门配置文件。
- 如果两个配置文件（Azure 前门或 Azure 内容交付网络）具有根域的不同子域，则不能将通配符域添加到任一配置文件。

## <a name="certificate-binding"></a>证书绑定

若要在通配符域上接受 HTTPS 通信，必须在通配符域上启用 HTTPS。 通配符域的证书绑定需要通配符证书。 也就是说，证书的使用者名称还应包含通配符域。

> [!NOTE]
> 目前，仅使用自己的自定义 SSL 证书选项可为通配符域启用 HTTPS。 Azure 前门托管证书不能用于通配符域。

你可以选择使用 Azure Key Vault 中的相同通配符证书，也可以从子域的 Azure 前门托管证书中选择使用。

如果为已具有关联证书的通配符域添加子域，则不能禁用该子域的 HTTPS。 子域使用通配符域的证书绑定，除非其他 Key Vault 或 Azure 前门托管证书覆盖它。

## <a name="waf-policies"></a>WAF 策略

可以将 WAF 策略附加到通配符域，类似于其他域。 可以将不同的 WAF 策略应用到通配符域的子域。 对于子域，必须指定要使用的 WAF 策略，即使该策略与通配符域相同。 子域*不*会自动从通配符域继承 WAF 策略。

如果你不希望为子域运行 WAF 策略，则可以创建没有托管或自定义规则集的空 WAF 策略。

## <a name="routing-rules"></a>路由规则

配置路由规则时，可以选择通配符域作为前端主机。 对于通配符域和子域，还可以具有不同的路由行为。 如[Azure 前端的路由匹配方式](front-door-route-matching.md)中所述，在运行时选择不同路由规则中的域的最特定匹配。

> [!IMPORTANT]
> 你必须在路由规则中具有匹配的路径模式，否则你的客户端将会看到失败。 例如，有两个路由规则，例如路由1（`*.foo.com/*`映射到后端池 A）和路由2（`bar.foo.com/somePath/*`映射到后端池 B）。 然后，请求到达`bar.foo.com/anotherPath/*`。 Azure 前门根据更具体的域匹配选择路由2，而只查找路由之间没有匹配的路径模式。

## <a name="next-steps"></a>后续步骤

- 了解如何[创建 Azure 前门配置文件](quickstart-create-front-door.md)。
- 了解如何[在 Azure 前门上添加自定义域](front-door-custom-domain.md)。
- 了解如何[在自定义域上启用 HTTPS](front-door-custom-domain-https.md)。
