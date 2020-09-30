---
title: Azure 前门-对通配符域的支持
description: 本文将帮助你了解 Azure 前门如何支持在自定义域列表中映射和管理通配符域。
services: frontdoor
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/29/2020
ms.author: duau
ms.openlocfilehash: 5194e088ce2bd35208a92c5295457e6c34cd2cc1
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91570318"
---
# <a name="wildcard-domains"></a>通配符域

除了顶点域和子域以外，还可以将通配符域映射到你的 Azure 前门配置文件的前端主机或自定义域。 如果在 Azure 前门配置中拥有通配符域，则会为同一路由规则中的 API、应用程序或网站的多个子域简化流量路由行为。 无需修改配置即可单独添加或指定每个子域。 例如，你可以 `customer1.contoso.com` `customer2.contoso.com` `customerN.contoso.com` 通过使用相同的路由规则并添加通配符域来定义、和的路由 `*.contoso.com` 。

支持通配符域的主要方案包括：

- 无需在 Azure 前门配置文件中载入每个子域，然后启用 HTTPS 即可为每个子域绑定证书。
- 如果应用程序添加新的子域，则不再需要更改生产 Azure 前门配置。 以前，你必须添加子域，将证书绑定到它，将 web 应用程序防火墙 (WAF) 策略，然后将该域添加到不同的路由规则。

> [!NOTE]
> 目前仅支持通过 API、PowerShell 和 Azure CLI 来支持通配符域。 对在 Azure 门户中添加和管理通配符域的支持不可用。

## <a name="adding-wildcard-domains"></a>添加通配符域

你可以在前端主机或域的部分下添加通配符域。 类似于子域，Azure 前门验证是否有通配符域的 CNAME 记录映射。 此 DNS 映射可以是映射到的直接 CNAME 记录映射 `*.contoso.com` `contoso.azurefd.net` 。 或者，可以使用 afdverify 临时映射。 例如， `afdverify.contoso.com` 映射以 `afdverify.contoso.azurefd.net` 验证通配符的 CNAME 记录映射。

> [!NOTE]
> Azure DNS 支持 通配符记录。

可以在前端主机中添加通配符域的任意多个下级子域，最多可添加到前端主机的限制。 此功能可能是必需的：

- 为子域定义不同于通配符域) 的其他路由 (。

- 针对特定子域具有不同的 WAF 策略。 例如， `*.contoso.com` 允许添加， `foo.contoso.com` 而无需再次证明域所有权。 但不允许这样做， `foo.bar.contoso.com` 因为它不是的单个级别的子域 `*.contoso.com` 。 若要添加 `foo.bar.contoso.com` 而不添加其他域所有权验证， `*.bar.contosonews.com` 需要添加。

你可以添加通配符域及其子域，但有一些限制：

- 如果将通配符域添加到 Azure 前门配置文件：
  - 不能将通配符域添加到任何其他 Azure 前门配置文件。
  - 不能将通配符域的第一级子域添加到另一个 Azure 前门配置文件或 Azure 内容分发网络配置文件。
- 如果已将通配符域的子域添加到 Azure 前门配置文件或 Azure 内容分发网络配置文件，则通配符域不能用于其他 Azure 前门配置文件。
- 如果两个配置文件 (Azure 前门或 Azure 内容分发网络) 具有根域的不同子域，则不能将通配符域添加到任一配置文件。

## <a name="certificate-binding"></a>证书绑定

若要在通配符域上接受 HTTPS 通信，必须在通配符域上启用 HTTPS。 通配符域的证书绑定需要通配符证书。 也就是说，证书的使用者名称还应包含通配符域。

> [!NOTE]
> 目前，仅使用自己的自定义 SSL 证书选项可为通配符域启用 HTTPS。 Azure 前门托管证书不能用于通配符域。

你可以选择使用 Azure Key Vault 中的相同通配符证书，也可以从子域的 Azure 前门托管证书中选择使用。

如果为已具有关联证书的通配符域添加子域，则不能为子域禁用 HTTPS。 子域使用通配符域的证书绑定，除非其他 Key Vault 或 Azure 前门托管证书覆盖它。

## <a name="waf-policies"></a>WAF 策略

可以将 WAF 策略附加到通配符域，类似于其他域。 可以将不同的 WAF 策略应用到通配符域的子域。 对于子域，必须指定要使用的 WAF 策略，即使该策略与通配符域相同。 子域 *不* 会自动从通配符域继承 WAF 策略。

如果你不希望为子域运行 WAF 策略，则可以创建没有托管或自定义规则集的空 WAF 策略。

## <a name="routing-rules"></a>路由规则

配置路由规则时，可以选择通配符域作为前端主机。 对于通配符域和子域，还可以具有不同的路由行为。 如 [Azure 前端的路由匹配方式](front-door-route-matching.md)中所述，在运行时选择不同路由规则中的域的最特定匹配。

> [!IMPORTANT]
> 你必须在路由规则中具有匹配的路径模式，否则你的客户端将会看到失败。 例如，有两个路由规则（例如路由 1 (`*.foo.com/*` 映射到后端池）) 和路由 2 (`/bar.foo.com/somePath/*` 映射到后端池 B) 。 然后，请求到达 `bar.foo.com/anotherPath/*` 。 Azure 前门根据更具体的域匹配选择路由2，而只查找路由之间没有匹配的路径模式。

## <a name="next-steps"></a>后续步骤

- 了解如何 [创建 Azure 前门配置文件](quickstart-create-front-door.md)。
- 了解如何 [在 Azure 前门上添加自定义域](front-door-custom-domain.md)。
- 了解如何 [在自定义域上启用 HTTPS](front-door-custom-domain-https.md)。
