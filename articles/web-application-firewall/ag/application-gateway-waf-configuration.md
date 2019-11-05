---
title: Azure 应用程序网关的 Web 应用程序防火墙请求大小限制和排除列表 - Azure 门户
description: 本文提供了有关在应用程序网关上具有 Azure 门户的 Web 应用程序防火墙请求大小限制和排除列表配置的信息。
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 10/17/2019
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 91e6f08d1dc3dc829dd7c6a58c651d453121e240
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73516911"
---
# <a name="web-application-firewall-request-size-limits-and-exclusion-lists"></a>Web 应用程序防火墙请求大小限制和排除列表

Azure 应用程序网关 Web 应用程序防火墙（WAF）为 Web 应用程序提供保护。 本文介绍了 WAF 请求大小限制和排除列表配置。 这些设置位于与应用程序网关关联的 WAF 策略中。 若要了解有关 WAF 策略的详细信息，请参阅[Azure 应用程序网关上的 Azure Web 应用程序防火墙](ag-overview.md)和[创建应用程序网关的 Web 应用程序防火墙策略](create-waf-policy-ag.md)

## <a name="waf-exclusion-lists"></a>WAF 排除列表

![请求大小限制](../media/application-gateway-waf-configuration/waf-policy.png)

WAF 排除列表允许你忽略 WAF 评估中的某些请求属性。 常见示例是 Active Directory 插入的令牌，这些令牌用于身份验证或密码字段。 此类属性容易在 WAF 规则中包含可能触发误报的特殊字符。 将某个属性添加到 WAF 排除列表后，任何已配置且激活的 WAF 规则都不会考虑该属性。 排除列表的范围具有全局性。

可以按名称向排除列表添加以下属性。 所选字段的值不会根据 WAF 规则进行评估，但其名称仍会这样进行评估（请参阅下面的示例 1，User-Agent 标头的值会从 WAF 评估中排除）。 排除列表删除了对该字段值的检查。

* 请求标头
* 请求 Cookie
* 请求属性名称（参数）可以添加为排除元素，例如：

   * 表单字段名称
   * XML 实体
   * JSON 实体
   * URL 查询字符串参数

可以指定请求标头、正文、cookie 或查询字符串属性的完全匹配项。  也可以选择指定部分匹配项。 排除规则的范围具有全局性，将应用于所有页面和所有规则。

下面是受支持的匹配条件运算符：

- **等于**：此运算符用于完全匹配。 例如，要选择名为“bearerToken”的标头，请结合使用等号运算符和设为“bearerToken”的选择器。
- **开头**：此运算符与以指定选择器值开头的所有字段匹配。
- **结尾**：此运算符与以指定选择器值结尾的所有请求字段匹配。
- **包含**：此运算符与包含指定选择器值的所有请求字段匹配。
- **等于 any**：此运算符与所有请求字段匹配。 * 将是选择器值。

在所有情况下，匹配不区分大小写，并且正则表达式不允许作为选择器。

> [!NOTE]
> 有关详细信息和疑难解答帮助，请参阅[WAF 故障排除](web-application-firewall-troubleshoot.md)。

### <a name="examples"></a>示例

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

以下示例演示如何使用排除。

#### <a name="example-1"></a>示例 1

在此示例中，需排除 user-agent 标头。 user-agent 请求标头包含特征性字符，网络协议对等方可以通过这些字符了解请求软件用户代理的应用程序类型、操作系统、软件供应商或软件版本。 有关详细信息，请参阅 [User-Agent](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/User-Agent)。

在许多情况下，需要禁用对此标头进行评估的功能。 可能存在一个 WAF 所发现的字符串，并认为它是恶意的。 例如，字符串中出现的经典 SQL 攻击“x=x”。 在某些情况下，这可能是合法的流量。 因此，可能需要将此标头从 WAF 评估中排除。

以下 Azure PowerShell cmdlet 从评估中排除 user-agent 标头：

```azurepowershell
$exclusion1 = New-AzApplicationGatewayFirewallExclusionConfig `
   -MatchVariable "RequestHeaderNames" `
   -SelectorMatchOperator "Equals" `
   -Selector "User-Agent"
```
#### <a name="example-2"></a>示例 2

此示例排除通过 URL 在请求中传递的 *user* 参数中的值。 例如，假设在你的环境中，user 字段常常包含某个字符串，而 WAF 会将该字符串视为恶意内容并将其阻止。  在这种情况下，可以排除 user 参数，这样 WAF 就不会评估此字段中的任何内容。

以下 Azure PowerShell cmdlet 从评估中排除 user 参数：

```azurepowershell
$exclusion2 = New-AzApplicationGatewayFirewallExclusionConfig `
   -MatchVariable "RequestArgNames" `
   -SelectorMatchOperator "StartsWith" `
   -Selector "user"
```
因此，如果将 URL **http://www.contoso.com/?user%281%29=fdafdasfda** 传递给 WAF，后者就不会评估字符串 **fdafdasfda**，但仍会评估参数名称 **user%281%29**。 

## <a name="waf-request-size-limits"></a>WAF 请求大小限制



Web 应用程序防火墙允许你在下限和上限内配置请求大小限制。 有以下两个大小限制配置可用：

- 最大请求正文大小字段以 KB 为单位进行指定并控制整个请求大小限制（不包括任何文件上传）。 此字段的最小值可以为 1 KB，最大值可以为 128 KB。 请求正文大小的默认值为 128 KB。
- 文件上传限制字段以 MB 为单位进行指定并控制允许的最大文件上传大小。 对于大型 SKU 实例，此字段的最小值可以为 1 MB，最大值为 500 MB，而中型 SKU 的最大值为 100 MB。 文件上传限制的默认值为 100 MB。

WAF 还提供了可配置的旋钮以打开或关闭请求正文检查。 默认情况下，请求正文检查处于启用状态。 如果请求正文检查处于关闭状态，则 WAF 不会评估 HTTP 消息正文的内容。 在这种情况下，WAF 会继续对标头、cookie 和 URI 强制实施 WAF 规则。 如果请求正文检查处于关闭状态，则最大请求正文大小字段不适用，且无法设置。 关闭请求正文检查允许将大于 128 KB 的消息发送到 WAF，但不会检查消息正文中是否有漏洞。

## <a name="next-steps"></a>后续步骤

配置 WAF 设置后，可以了解如何查看 WAF 日志。 有关详细信息，请参阅[应用程序网关诊断](../../application-gateway/application-gateway-diagnostics.md#diagnostic-logging)。
