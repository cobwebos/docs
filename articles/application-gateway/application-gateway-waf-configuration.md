---
title: Azure 应用程序网关的 Web 应用程序防火墙请求大小限制和排除列表 - Azure 门户
description: 本文将提供使用 Azure 门户的应用程序网关的 Web 应用程序防火墙请求大小限制和排除列表配置的相关信息。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 5/15/2019
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 5ddcdeca41e2f21fa27db25f7e0721c7ef87e491
ms.sourcegitcommit: 3675daec6c6efa3f2d2bf65279e36ca06ecefb41
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/14/2019
ms.locfileid: "65620273"
---
# <a name="web-application-firewall-request-size-limits-and-exclusion-lists"></a>Web 应用程序防火墙请求大小限制和排除列表

Azure 应用程序网关 Web 应用程序防火墙 (WAF) 可为 Web 应用程序提供保护。 本文介绍了 WAF 请求大小限制和排除列表配置。

## <a name="waf-request-size-limits"></a>WAF 请求大小限制

![请求大小限制](media/application-gateway-waf-configuration/waf-requestsizelimit.png)

Web 应用程序防火墙允许你在下限和上限内配置请求大小限制。 有以下两个大小限制配置可用：

- 在整个请求大小限制不包括任何文件上传的千字节为单位和控件中指定最大请求正文大小字段。 此字段的最小值可以为 1 KB，最大值可以为 128 KB。 请求正文大小的默认值为 128 KB。
- 文件上传限制字段以 MB 为单位进行指定并控制允许的最大文件上传大小。 对于大型 SKU 实例，此字段的最小值可以为 1 MB，最大值为 500 MB，而中型 SKU 的最大值为 100 MB。 文件上传限制的默认值为 100 MB。

WAF 还提供了可配置的旋钮以打开或关闭请求正文检查。 默认情况下，请求正文检查处于启用状态。 如果请求正文检查处于关闭状态的 WAF 不评估 HTTP 消息正文的内容。 在这种情况下，WAF 会继续对标头、cookie 和 URI 强制实施 WAF 规则。 如果请求正文检查处于关闭状态，则最大请求正文大小字段不适用，且无法设置。 关闭请求正文检查允许将大于 128 KB 的消息发送到 WAF，但不会检查消息正文中是否有漏洞。

## <a name="waf-exclusion-lists"></a>WAF 排除列表

![waf-exclusion.png](media/application-gateway-waf-configuration/waf-exclusion.png)

WAF 排除列表允许你忽略 WAF 评估中的某些请求属性。 常见示例是 Active Directory 插入的令牌，这些令牌用于身份验证或密码字段。 此类属性容易在 WAF 规则中包含可能触发误报的特殊字符。 将某个属性添加到 WAF 排除列表后，任何已配置且激活的 WAF 规则都不会考虑该属性。 排除列表的范围具有全局性。

可以向排除列表添加以下属性：

* 请求标头
* 请求 Cookie
* 请求属性名称 (args)

   * 形成多部分数据
   * XML
   * JSON
   * URL 查询参数

可以指定请求标头、正文、cookie 或查询字符串属性的完全匹配项。  也可以选择指定部分匹配项。 排除始终应用于标头字段，而不应用于其值。 排除规则的范围具有全局性，将应用于所有页面和所有规则。

下面是受支持的匹配条件运算符：

- **等于**：此运算符用于完全匹配。 例如，要选择名为“bearerToken”的标头，请结合使用等号运算符和设为“bearerToken”的选择器。
- **开头为**：此运算符与以指定选择器值开头的所有字段匹配。
- **结尾为**：此运算符与以指定选择器值结尾的所有请求字段匹配。
- **包含**：此运算符与包含指定选择器值的所有请求字段匹配。
- **等于任何值**：此运算符与所有请求字段匹配。 * 将是选择器值。

在所有情况下，匹配不区分大小写，并且正则表达式不允许作为选择器。

### <a name="examples"></a>示例

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

下面的示例演示使用排除项。

### <a name="example-1"></a>示例 1

在此示例中，你想要排除的用户代理标头。 用户代理请求标头包含允许网络协议对等方，以确定应用程序类型、 操作系统、 软件供应商联系或请求的软件用户代理的软件版本的特性字符串。 有关详细信息，请参阅[用户代理](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/User-Agent)。

可以有任意数量的原因，若要禁用评估此标头。 可能有此 WAF 将看到，并假设它是恶意的字符串。 例如，经典 SQL 攻击"x = x"在字符串中。 在某些情况下，这可能是合法的流量。 因此，您可能需要从 WAF 评估中排除此标头。

以下 Azure PowerShell cmdlet 从评估中排除的用户代理标头：

```azurepowershell
$exclusion1 = New-AzApplicationGatewayFirewallExclusionConfig `
   -MatchVariable "RequestHeaderNames" `
   -SelectorMatchOperator "Equals" `
   -Selector "User-Agent"
```

### <a name="example-2"></a>示例 2

此示例将排除中的值*用户*通过 URL 请求中传递的参数。 例如，假设它是用户字段包含一个字符串，WAF 视作恶意内容，因此它会阻止它在环境中经常。  在这种情况下，以便此 WAF 不会评估该字段中的任何内容，可以排除用户参数。

以下 Azure PowerShell cmdlet 从评估版不包括用户参数：

```azurepowershell
$exclusion2 = New-AzApplicationGatewayFirewallExclusionConfig `
   -MatchVariable "RequestArgNames" `
   -SelectorMatchOperator "Equals" `
   -Selector "user"
```
因此，如果 URL **http://www.contoso.com/?user=fdafdasfda**传递到 WAF，它不会计算该字符串**fdafdasfda**。

## <a name="next-steps"></a>后续步骤

配置 WAF 设置后，可以了解如何查看 WAF 日志。 有关详细信息，请参阅[应用程序网关诊断](application-gateway-diagnostics.md#diagnostic-logging)。
