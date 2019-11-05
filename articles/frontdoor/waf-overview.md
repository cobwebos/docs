---
title: 什么是适用于 Azure 前门的 Azure web 应用程序防火墙？ （预览版）
description: 了解 azure 前端服务的 Azure web 应用程序防火墙如何防止 web 应用程序受到恶意攻击。
services: frontdoor
documentationcenter: ''
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/31/2019
ms.author: kumud
ms.reviewer: tyao
ms.openlocfilehash: 624a5af59a39cb0ebf647f549f3a40ed56f78b9e
ms.sourcegitcommit: d47a30e54c5c9e65255f7ef3f7194a07931c27df
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73549218"
---
# <a name="what-is-azure-web-application-firewall-for-azure-front-door"></a>什么是适用于 Azure 前门的 Azure web 应用程序防火墙？

Azure Web 应用程序防火墙 (WAF) 为那些使用 Azure Front Door 进行全球交付的 Web 应用程序提供集中保护。 设计和运行此防火墙的目的是保护 Web 服务免受常见的攻击和漏洞的危害，使服务对你的用户高度可用，另外还帮助你满足符合性要求。


Web 应用程序越来越多地成为恶意攻击的目标，例如拒绝服务泛滥、SQL 注入攻击和跨站点脚本攻击。 这些恶意攻击可能会导致服务中断和数据丢失，对 web 应用程序所有者造成严重威胁。

防止应用程序代码遭受此类攻击颇具挑战性，并且可能需要对应用程序拓扑的多个层进行严格的维护、修补和监视。 集中式 Web 应用程序防火墙有助于大幅简化安全管理，为抵卸威胁或入侵的应用程序管理员提供更好的保障。 此外，通过在中央位置修补已知漏洞而不是保护每个单独的 web 应用程序，WAF 解决方案可以更快地响应安全威胁。

前门的 WAF 是一个全球和集中式解决方案。 它部署在全球的 Azure 网络边缘位置，并在网络边缘检查每个按前门提供的启用了 WAF 的 web 应用程序的传入请求。 这样，在用户进入虚拟网络并提供大规模的全局保护而不牺牲性能的情况下，WAF 可防止恶意攻击进入攻击来源。 可以轻松地将 WAF 策略链接到订阅中的任何前门配置文件，并且可以在几分钟内部署新规则，从而使你能够快速响应不断变化的威胁模式。

![Azure web 应用程序防火墙](./media/waf-overview/web-application-firewall-overview.png)

还可以通过应用程序网关启用 Azure WAF。 有关详细信息，请参阅[Web 应用程序防火墙](../application-gateway/waf-overview.md)。

## <a name="waf-policy-and-rules"></a>WAF 策略和规则

可以配置 WAF 策略，并将该策略关联到一个或多个前门前端以进行保护。 WAF 策略包含两种类型的安全规则：

- 由客户创作的自定义规则。

- 托管规则集，它是 Azure 托管的预配置规则集的集合。

如果两者都存在，则在处理托管规则集中的规则之前处理自定义规则。 规则由匹配条件、优先级和操作组成。 支持的操作类型包括： ALLOW、BLOCK、LOG 和重定向。 你可以通过组合托管规则和自定义规则来创建满足特定应用程序保护要求的完全自定义策略。

策略中的规则按优先顺序进行处理，优先级为唯一的整数，用于定义要处理的规则的顺序。 较小的整数值表示较高的优先级，并在具有更高整数值的规则之前计算。 匹配规则后，规则中定义的相应操作将应用于该请求。 处理此类匹配项后，不会进一步处理优先级较低的规则。

前门提供的 web 应用程序一次只能有一个与之关联的 WAF 策略。 但是，你可以使用前门配置，而无需任何关联的 WAF 策略。 如果存在 WAF 策略，则会将其复制到所有边缘位置，以确保全球安全策略的一致性。

## <a name="waf-modes"></a>WAF 模式

可以将 WAF 策略配置为在以下两种模式下运行：

- **检测模式：** 在检测模式下运行时，WAF 不会执行任何其他操作，而是监视请求，并将其匹配的 WAF 规则记录到 WAF 日志。 你可以为前门启用日志记录诊断（使用门户时，可以通过转到 Azure 门户中的 "**诊断**" 部分来实现）。

- **防护模式：** 当配置为在阻止模式下运行时，如果请求与规则匹配，则 WAF 将执行指定的操作，如果找到匹配项，则不会评估更低优先级的规则。 任何匹配的请求也会记录在 WAF 日志中。

## <a name="waf-actions"></a>WAF 操作

当请求满足规则条件时，WAF 客户可以选择从某个操作运行：

- **允许：** 请求通过 WAF，并转发到后端。 没有更低优先级的规则可阻止此请求。
- **块：** 请求被阻止，WAF 将响应发送到客户端，而不会将请求转发到后端。
- **日志：** 请求记录在 WAF 日志中，WAF 继续评估低优先级规则。
- **重定向：** WAF 将请求重定向到指定的 URI。 指定的 URI 是一个策略级别设置。 配置后，与**重定向**操作匹配的所有请求都将发送到该 URI。

## <a name="waf-rules"></a>WAF 规则

WAF 策略可能包含两种类型的安全规则-自定义规则，由客户和托管规则集编写，由 Azure 管理的预配置规则集组成。

### <a name="custom-authored-rules"></a>自定义创作规则

可以按如下所示配置自定义规则 WAF：

- **IP 允许列表和阻止列表：** 你可以根据客户端 IP 地址或 IP 地址范围的列表配置自定义规则，以控制对你的 web 应用程序的访问。 支持 IPv4 和 IPv6 地址类型。 此列表可配置为阻止或允许源 IP 与列表中的 IP 匹配的请求。 客户端 IP 地址可能不同于 IP 地址 WAF，例如，当客户端通过代理访问 WAF 时。 你可以基于客户端 IP 地址（RemoteAddr）或 WAF （SocketAddr）显示的 IP 地址创建[ip 限制规则](https://docs.microsoft.com/azure/frontdoor/waf-front-door-configure-ip-restriction)。 目前支持使用 Powershell 和 Azure CLI 配置 SocketAddr IP 限制规则。

- **基于地理位置的访问控制：** 你可以配置自定义规则，以便根据与客户端的 IP 地址关联的国家/地区代码来控制对你的 web 应用程序的访问。

- **基于 HTTP 参数的访问控制：** 可以基于字符串匹配 HTTP/HTTPS 请求参数（如查询字符串、POST 参数、请求 URI、请求标头和请求正文）来配置自定义规则。

- **请求基于方法的访问控制：** 您可以基于请求的 HTTP 请求方法（如 GET、PUT 或 HEAD）配置自定义规则。

- **大小约束：** 您可以基于请求的特定部分的长度（例如查询字符串、Uri 或请求正文）配置自定义规则。

- **速率限制规则：** 速率控制规则用于限制来自任何客户端 IP 的异常高流量。 你可以在一分钟的时间内配置从客户端 IP 允许的 web 请求数的阈值。 请注意，超过阈值的其他请求可能会在更新请求计数时获得。 速率限制可与其他匹配条件组合，如用于粒度速率控制的 HTTP （S）参数匹配。

### <a name="azure-managed-rule-sets"></a>Azure 托管规则集

Azure 托管规则集提供了一种简单的方法来根据一组常见的安全威胁来部署保护。 由于此类规则集由 Azure 管理，因此这些规则会根据需要进行更新以防止新的攻击签名。 在公共预览版中，Azure 托管的默认规则集包含以下威胁类别的规则：

- 跨站点脚本
- Java 攻击
- 本地文件包含
- PHP 注入攻击
- 远程命令执行
- 远程文件包含
- 会话固定
- SQL 注入保护
- 协议攻击者

将新的攻击签名添加到规则集时，默认规则集的版本号将递增。
默认情况下，在 WAF 策略中的检测模式下启用默认规则集。 您可以禁用或启用默认规则集内的各个规则以满足您的应用程序要求。 还可以为每个规则设置特定操作（允许/阻止/重定向/记录）。 默认操作是 "阻止"。 此外，如果想要跳过默认规则集中任何预配置的规则，则可以在相同的 WAF 策略中配置自定义规则。
自定义规则始终在计算默认规则集中的规则前应用。 如果某个请求与某个自定义规则匹配，则将应用相应的规则操作，并且该请求将被阻止或传递到后端，而不会调用任何其他自定义规则或默认规则集中的规则。 此外，你还可以选择从 WAF 策略中删除默认规则集。


### <a name="bot-protection-rule-preview"></a>机器人保护规则（预览）

可以为 WAF 启用托管机器人保护规则集，以便对来自已知机器人类别的请求执行自定义操作。
支持3个 bot 类别：错误的 bot、良好的机器人和未知机器人。 在 "不良 bot" 类别中，一个规则根据 clieny 地址的 IP 信誉检测恶意 bot。 IP 信誉源自 Microsoft 威胁情报源。 [Intelligent Security Graph](https://www.microsoft.com/security/operations/intelligence)为 Microsoft 威胁智能提供技术支持，由多种服务（包括 Azure 安全中心）使用。 此外，假设为已知搜索引擎的请求也被检测为恶意。
良好的机器人是 seacrh 引擎的验证。 未知类别包括请求被标识为 bot，但具有未知意向。 你可以将自定义操作设置为针对不同类别的 Bot 阻止、允许、记录或重定向。

![机器人保护规则集](./media/waf-front-door-configure-bot-protection/BotProtect2.png)

> [!IMPORTANT]
> 机器人保护规则集当前以公共预览版提供，并随预览版服务级别协议一起提供。 某些功能可能不受支持或者受限。  有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

如果启用了机器人保护，则在 FrontdoorWebApplicationFirewallLog 日志中记录与机器人规则匹配的传入请求。 可以从存储帐户、事件中心或 log analytics 访问 WAF 日志。 

## <a name="configuration"></a>配置

使用 Azure 门户、REST Api、Azure 资源管理器模板和 Azure PowerShell 完全支持配置和部署所有 WAF 规则类型。

## <a name="monitoring"></a>监视

在前门上监视 WAF 与 Azure Monitor 集成，以跟踪警报并轻松监视流量趋势。

## <a name="next-steps"></a>后续步骤

- 了解如何[使用 Azure 门户为前门配置 WAF 策略](waf-front-door-create-portal.md)
