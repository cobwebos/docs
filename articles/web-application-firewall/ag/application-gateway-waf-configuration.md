---
title: Azure 应用程序网关的 Web 应用程序防火墙请求大小限制和排除列表 - Azure 门户
description: 本文提供了有关在应用程序网关上具有 Azure 门户的 Web 应用程序防火墙请求大小限制和排除列表配置的信息。
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 02/20/2020
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 7244788bbc7431c7f26363b2852babb72d5697e9
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2020
ms.locfileid: "77526784"
---
# <a name="web-application-firewall-request-size-limits-and-exclusion-lists"></a>Web 应用程序防火墙请求大小限制和排除列表

Azure 应用程序网关 Web 应用程序防火墙（WAF）为 Web 应用程序提供保护。 本文介绍了 WAF 请求大小限制和排除列表配置。 这些设置位于与应用程序网关关联的 WAF 策略中。 若要了解有关 WAF 策略的详细信息，请参阅[Azure 应用程序网关上的 Azure Web 应用程序防火墙](ag-overview.md)和[创建应用程序网关的 Web 应用程序防火墙策略](create-waf-policy-ag.md)

## <a name="waf-exclusion-lists"></a>WAF 排除列表

![请求大小限制](../media/application-gateway-waf-configuration/waf-policy.png)

WAF 排除列表允许你忽略 WAF 评估中的某些请求属性。 常见示例是 Active Directory 插入的令牌，这些令牌用于身份验证或密码字段。 此类属性容易在 WAF 规则中包含可能触发误报的特殊字符。 将某个属性添加到 WAF 排除列表后，任何已配置且激活的 WAF 规则都不会考虑该属性。 排除列表的范围具有全局性。

可以按名称将以下属性添加到排除列表。 所选字段的值不针对 WAF 规则进行评估，但其名称仍为（请参阅下面的示例1，用户代理标头的值从 WAF 计算中排除）。 排除列表删除对字段值的检查。

* 请求标头
* 请求 Cookie
* 请求属性名称（args）可以添加为排除元素，例如：

   * 窗体字段名称
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

下面的示例演示如何使用排除项。

#### <a name="example-1"></a>示例 1

在此示例中，你想要排除用户代理标头。 用户代理请求标头包含特性字符串，允许网络协议对等方识别请求软件用户代理的应用程序类型、操作系统、软件供应商或软件版本。 有关详细信息，请参阅[用户代理](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/User-Agent)。

禁用评估此标头的原因有很多。 可能存在一个 WAF 所发现的字符串，并认为它是恶意的。 例如，字符串中的经典 SQL 攻击 "x = x"。 在某些情况下，这可能是合法的通信。 因此，你可能需要从 WAF 评估中排除此标头。

以下 Azure PowerShell cmdlet 从评估中排除用户代理标头：

```azurepowershell
$exclusion1 = New-AzApplicationGatewayFirewallExclusionConfig `
   -MatchVariable "RequestHeaderNames" `
   -SelectorMatchOperator "Equals" `
   -Selector "User-Agent"
```
#### <a name="example-2"></a>示例 2

此示例在通过 URL 传入请求的*user*参数中排除该值。 例如，假设您的环境中的常见情况是，用户字段包含一个字符串，该字符串 WAF 查看为恶意内容，因此会阻止它。  在这种情况下，你可以排除 user 参数，使 WAF 不会计算字段中的任何内容。

以下 Azure PowerShell cmdlet 从计算中排除 user 参数：

```azurepowershell
$exclusion2 = New-AzApplicationGatewayFirewallExclusionConfig `
   -MatchVariable "RequestArgNames" `
   -SelectorMatchOperator "StartsWith" `
   -Selector "user"
```
因此，如果 URL `http://www.contoso.com/?user%281%29=fdafdasfda` 传递到 WAF，则它不会计算**fdafdasfda**字符串，但仍会计算参数名称**user %281 %29**。 

## <a name="waf-request-size-limits"></a>WAF 请求大小限制



Web 应用程序防火墙允许你在下限和上限内配置请求大小限制。 有以下两个大小限制配置可用：

- "最大请求正文大小" 字段以 kb 指定，并控制总请求大小限制（排除任何文件上传）。 此字段的最小值可以为 1 KB，最大值可以为 128 KB。 请求正文大小的默认值为 128 KB。
- 文件上传限制字段以 MB 为单位进行指定并控制允许的最大文件上传大小。 此字段的最小值为 1 MB，最大值为：

   - v1 中型 WAF 网关 100 MB
   - v1 大型 WAF 网关 500 MB
   - 对于 v2 WAF 网关为 750 MB 

 文件上传限制的默认值为 100 MB。

WAF 还提供了可配置的旋钮以打开或关闭请求正文检查。 默认情况下，请求正文检查处于启用状态。 如果请求正文检查处于关闭状态，WAF 将不会评估 HTTP 消息正文的内容。 在这种情况下，WAF 会继续对标头、cookie 和 URI 强制实施 WAF 规则。 如果请求正文检查处于关闭状态，则最大请求正文大小字段不适用，且无法设置。 关闭请求正文检查允许将大于 128 KB 的消息发送到 WAF，但不会检查消息正文中是否有漏洞。

## <a name="next-steps"></a>后续步骤

配置 WAF 设置后，可以了解如何查看 WAF 日志。 有关详细信息，请参阅[应用程序网关诊断](../../application-gateway/application-gateway-diagnostics.md#diagnostic-logging)。
