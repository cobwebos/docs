---
title: Azure Web 应用程序防火墙 （WAF） 策略概述
description: 本文概述了 Web 应用程序防火墙 （WAF） 全局、每个站点和每个 URI 策略。
services: web-application-firewall
ms.topic: article
author: winthrop28
ms.service: web-application-firewall
ms.date: 02/01/2020
ms.author: victorh
ms.openlocfilehash: fff4fb5707c07098fd7fac261a36909224365cdf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060268"
---
# <a name="azure-web-application-firewall-waf-policy-overview"></a>Azure Web 应用程序防火墙 （WAF） 策略概述

Web 应用程序防火墙策略包含所有 WAF 设置和配置。 这包括排除项、自定义规则、托管规则等。 然后，这些策略将关联到应用程序网关（全局）、侦听器（每个站点）或基于路径的规则（每个 URI），以便它们生效。

> [!NOTE]
> 每个站点和每个 URI 策略的 Azure Web 应用程序防火墙 （WAF） 处于公共预览版中。
> 
> 此公共预览版在提供时没有附带服务级别协议，不应该用于生产工作负荷。 某些功能可能不受支持或受到约束，或者不一定在所有 Azure 位置都可用。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

您可以创建的策略数量没有限制。 创建策略时，必须将其与应用程序网关关联才能生效。 它可以与应用程序网关、侦听器和基于路径的规则的任意组合相关联。

## <a name="global-waf-policy"></a>全球 WAF 策略

当您全局关联 WAF 策略时，应用程序网关 WAF 后面的每个站点都使用相同的托管规则、自定义规则、排除和任何其他配置的设置进行保护。

如果希望单个策略应用于所有站点，则可以将策略与应用程序网关相关联。 有关详细信息，请参阅[为应用程序网关创建 Web 应用程序防火墙策略](create-waf-policy-ag.md)，以便使用 Azure 门户创建和应用 WAF 策略。 

## <a name="per-site-waf-policy"></a>每个站点 WAF 策略

使用每个站点 WAF 策略，您可以使用每个站点策略保护单个 WAF 背后的具有不同安全需求的多个站点。 例如，如果 WAF 后面有五个站点，则可以有五个单独的 WAF 策略（每个侦听器一个策略），以自定义每个站点的排除项、自定义规则、托管规则集和所有其他 WAF 设置。

假设应用程序网关已应用全局策略。 然后，对该应用程序网关上的侦听器应用不同的策略。 侦听器的策略现在仅对该侦听器生效。 应用程序网关的全局策略仍适用于所有其他侦听器和基于路径的规则，这些规则没有为其分配特定的策略。

## <a name="per-uri-policy"></a>每个 URI 策略

对于更多自定义（下为 URI 级别），您可以将 WAF 策略与基于路径的规则相关联。 如果单个站点中的某些页面需要不同的策略，则可以对仅影响给定 URI 的 WAF 策略进行更改。 这可能适用于付款或登录页面，或任何其他需要比 WAF 背后的其他站点更具体的 WAF 策略的 URI。

与每个站点 WAF 策略一样，更具体的策略将覆盖较不具体的策略。 这意味着 URL 路径映射上的每个 URI 策略将覆盖其上方的任何每个站点或全局 WAF 策略。

## <a name="example"></a>示例

假设您有三个站点：contoso.com、fabrikam.com和adatum.com都位于同一应用程序网关后面。 您希望将 WAF 应用于所有三个站点，但您需要通过adatum.com增加安全性，因为客户可以访问、浏览和购买产品。

您可以将全局策略应用于 WAF，并在必要时使用一些基本设置、排除或自定义规则来阻止某些误报阻塞流量。 在这种情况下，无需运行全局 SQL 注入规则，因为fabrikam.com和contoso.com是静态页，没有 SQL 后端。 因此，您可以在全局策略中禁用这些规则。

此全球政策适用于contoso.com和fabrikam.com，但您需要更加小心处理登录信息和付款adatum.com。 您可以将每个站点策略应用于基准侦听器，并保留 SQL 规则运行。 还假设有一个 Cookie 阻止某些流量，因此您可以为该 Cookie 创建排除项以阻止误报。 

ADATUM.COM/PAYMENTS URI 是您需要小心的地方。 因此，对该 URI 应用另一个策略，并保留所有规则，并删除所有排除项。

在此示例中，您有一个适用于两个站点的全局策略。 您有一个适用于一个站点的每个站点策略，然后是应用于一个基于路径的特定规则的每个 URI 策略。 请参阅此示例（如果存在链接时在此处插入链接）如何创建每个站点和每个 URI 策略。

## <a name="existing-waf-configurations"></a>现有 WAF 配置

所有新的 Web 应用程序防火墙的 WAF 设置（自定义规则、托管规则集配置、排除等）都存在于 WAF 策略中。 如果您有现有的 WAF，这些设置可能仍存在于 WAF 配置中。 有关迁移到新的 WAF 策略的详细信息，将[WAF 配置迁移到 WAF 策略](https://docs.microsoft.com/azure/web-application-firewall/ag/migrate-policy)。 


## <a name="next-steps"></a>后续步骤

使用 Azure PowerShell 创建每个站点和每个 URI 策略。
