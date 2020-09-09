---
title: 使用规则引擎添加安全性标头 - Azure Front Door
description: 本文介绍如何通过 Azure Front Door 上的规则引擎配置安全性标头
services: frontdoor
documentationcenter: ''
author: duongau
editor: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 6/22/2020
ms.author: duau
ms.openlocfilehash: ad1e8a8a2162ece69af9904d76a394d4bad5de23
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/02/2020
ms.locfileid: "89399134"
---
# <a name="add-security-headers-with-rules-engine"></a>使用规则引擎添加安全性标头

实现安全性标头以阻止基于浏览器的漏洞攻击，例如 HTTP Strict-Transport-Security (HSTS)、X-XSS-Protection、Content-Security-Policy 或 X-Frame-Options。 基于安全性的属性也可以用 Cookie 来定义。

下例演示了如何将 Content-Security-Policy 标头添加到所有传入请求中，这些请求与规则引擎配置关联的路由中定义的路径匹配。 在这里，我们仅允许来自受信任的站点 https://apiphany.portal.azure-api.net 的脚本在我们的应用程序上运行。

## <a name="add-a-content-security-policy-header-in-azure-portal"></a>在 Azure 门户中添加 Content-Security-Policy 标头

1. 创建此特定规则之前，如果是第一次使用 AFD 或规则引擎功能，请先了解如何[创建 Front Door](quickstart-create-front-door.md) 或者如何[创建规则引擎](front-door-tutorial-rules-engine.md)。

2. 单击“添加”以添加新规则。 为规则提供一个名称，然后单击“添加操作” > “响应标头” 。

3. 将运算符设置为“追加”，将此标头添加为对此路由的所有传入请求的响应。

4. 添加标头名称：“Content-Security-Policy”，并定义此标头应接受的值。 在此方案中，我们选择“script-src 'self' https://apiphany.portal.azure-api.net”。

5. 添加了所有要配置的规则后，请不要忘记转到首选路由，并将规则引擎配置关联到路由规则。 若要使规则正常运行，需要执行此步骤。 

![门户示例](./media/front-door-rules-engine/rules-engine-security-header-example.png)

> [!NOTE]
> 在此示例中，我们没有向规则添加[匹配条件](front-door-rules-engine-match-conditions.md)。 所有与路由规则中定义的路径匹配的传入请求都将应用此规则。 如果希望它仅应用于这些请求的一个子集，请确保将特定的匹配条件添加到此规则。


## <a name="next-steps"></a>后续步骤

- 详细了解 [AFD 规则引擎](front-door-rules-engine.md)。 
- 了解如何[创建 Front Door](quickstart-create-front-door.md)。
- 了解 [Front Door 的工作原理](front-door-routing-architecture.md)。
- 详细了解规则引擎[匹配条件](front-door-rules-engine-match-conditions.md)
- 在 AFD 规则引擎 [CLI 参考](https://docs.microsoft.com/cli/azure/ext/front-door/network/front-door/rules-engine?view=azure-cli-latest)中查看详细信息。 
- 在 AFD 规则引擎 [PowerShell 参考](https://docs.microsoft.com/powershell/module/az.frontdoor/?view=azps-3.8.0)中查看详细信息。 
