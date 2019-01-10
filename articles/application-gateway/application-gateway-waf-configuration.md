---
title: Azure 应用程序网关的 Web 应用程序防火墙请求大小限制和排除列表 - Azure 门户
description: 本文将提供使用 Azure 门户的应用程序网关的 Web 应用程序防火墙请求大小限制和排除列表配置的相关信息。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.workload: infrastructure-services
ms.date: 11/6/2018
ms.author: victorh
ms.openlocfilehash: bed406f90c8da62919337c1fa9f30221b0ba8d90
ms.sourcegitcommit: 7862449050a220133e5316f0030a259b1c6e3004
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/22/2018
ms.locfileid: "53752718"
---
# <a name="web-application-firewall-request-size-limits-and-exclusion-lists"></a>Web 应用程序防火墙请求大小限制和排除列表

Azure 应用程序网关 Web 应用程序防火墙 (WAF) 可为 Web 应用程序提供保护。 本文介绍了 WAF 请求大小限制和排除列表配置。

## <a name="waf-request-size-limits"></a>WAF 请求大小限制

![请求大小限制](media/application-gateway-waf-configuration/waf-requestsizelimit.png)

Web 应用程序防火墙允许用户在下限和上限内配置请求大小限制。 有以下两个大小限制配置可用：

- 最大请求正文大小字段以 KB 为单位进行指定并控制整个请求大小限制（不包括任何文件上传）。 此字段的最小值可以为 1 KB，最大值可以为 128 KB。 请求正文大小的默认值为 128 KB。
- 文件上传限制字段以 MB 为单位进行指定并控制允许的最大文件上传大小。 此字段的最小值可以为 1 MB，最大值可以为 500 MB。 文件上传限制的默认值为 100 MB。

WAF 还提供了可配置的旋钮以打开或关闭请求正文检查。 默认情况下，请求正文检查处于启用状态。 如果请求正文检查处于关闭状态，则 WAF 不会评估 HTTP 消息正文的内容。 在这种情况下，WAF 会继续对标头、cookie 和 URI 强制实施 WAF 规则。 如果请求正文检查处于关闭状态，则最大请求正文大小字段不适用，且无法设置。 关闭请求正文检查允许将大于 128 KB 的消息发送到 WAF，但不会检查消息正文中是否有漏洞。

## <a name="waf-exclusion-lists"></a>WAF 排除列表

![waf-exclusion.png](media/application-gateway-waf-configuration/waf-exclusion.png)

WAF 排除列表允许用户忽略 WAF 评估中的某些请求属性。 常见示例是 Active Directory 插入的令牌，这些令牌用于身份验证或密码字段。 此类属性容易在 WAF 规则中包含可能触发误报的特殊字符。 将某个属性添加到 WAF 排除列表后，任何已配置且激活的 WAF 规则都不会考虑该属性。 排除列表的范围具有全局性。

可以向排除列表添加以下属性：

* 请求标头
* 请求 Cookie
* 请求正文

   * 形成多部分数据
   * XML
   * JSON

可以指定请求标头、正文、cookie 或查询字符串属性的完全匹配项，也可以选择指定部分匹配项。

下面是受支持的匹配条件运算符：

- **等于**：此运算符用于完全匹配。 例如，要选择名为“bearerToken”的标头，请结合使用等号运算符和设为“bearerToken”的选择器。
- **开头为**：此运算符与以指定选择器值开头的所有字段匹配。
- **结尾为**：此运算符与以指定选择器值结尾的所有请求字段匹配。
- **包含**：此运算符与包含指定选择器值的所有请求字段匹配。

在所有情况下，匹配不区分大小写，并且正则表达式不允许作为选择器。

## <a name="next-steps"></a>后续步骤

配置 WAF 设置后，可以了解如何查看 WAF 日志。 有关详细信息，请参阅[应用程序网关诊断](application-gateway-diagnostics.md#diagnostic-logging)。