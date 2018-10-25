---
title: Azure 应用程序网关的 Web 应用程序防火墙请求大小限制和排除列表 - Azure 门户
description: 本文将提供使用 Azure 门户的应用程序网关的 Web 应用程序防火墙请求大小限制和排除列表配置的相关信息。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.workload: infrastructure-services
ms.date: 10/11/2018
ms.author: victorh
ms.openlocfilehash: 9306280d00ec901633585aba2f23ed06b25b4e1e
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2018
ms.locfileid: "49115448"
---
# <a name="web-application-firewall-request-size-limits-and-exclusion-lists-public-preview"></a>Web 应用程序防火墙请求大小限制和排除列表（公共预览版）

Azure 应用程序网关 Web 应用程序防火墙 (WAF) 可为 Web 应用程序提供保护。 本文介绍了 WAF 请求大小限制和排除列表配置。

> [!IMPORTANT]
> WAF 请求大小限制和排除列表配置当前处于公共预览状态。 此预览版在提供时没有附带服务级别协议，不建议用于生产工作负载。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="waf-request-size-limits"></a>WAF 请求大小限制
Web 应用程序防火墙允许用户在下限和上限内配置请求大小限制。 有以下两个大小限制配置可用：

- 最大请求正文大小字段以 KB 为单位进行指定并控制整个请求大小限制（不包括任何文件上传）。 此字段的最小值可以为 1 KB，最大值可以为 128 KB。 请求正文大小的默认值为 128 KB。
- 文件上传限制字段以 MB 为单位进行指定并控制允许的最大文件上传大小。 此字段的最小值可以为 1 MB，最大值可以为 500 MB。 文件上传限制的默认值为 100 MB。

WAF 还提供了可配置的旋钮以打开或关闭请求正文检查。 默认情况下，请求正文检查处于启用状态。 如果请求正文检查处于关闭状态，则 WAF 不会评估 HTTP 消息正文的内容。 在这种情况下，WAF 会继续对标头、cookie 和 URI 强制实施 WAF 规则。 如果请求正文检查处于关闭状态，则最大请求正文大小字段不适用，且无法设置。 关闭请求正文检查允许将大于 128 KB 的消息发送到 WAF。 但是，不会检查消息正文是否存在漏洞。

## <a name="waf-exclusion-lists"></a>WAF 排除列表

WAF 排除列表允许用户忽略 WAF 评估中的某些请求属性。 常见示例是 Active Directory 插入的令牌，这些令牌用于身份验证或密码字段。 此类属性容易在 WAF 规则中包含可能触发误报的特殊字符。 将某个属性添加到 WAF 排除列表后，任何已配置且激活的 WAF 规则都不会考虑该属性。 排除列表的范围具有全局性。
可以将请求标头、请求正文、请求 cookie 或请求查询字符串参数添加到 WAF 排除列表。 如果正文包含窗体数据或 XML/JSON（键/值对），则可以使用请求属性排除类型。

可以指定请求标头、正文、cookie 或查询字符串属性的完全匹配项，也可以选择指定部分匹配项。

下面是受支持的匹配条件运算符：

- **等于**：此运算符用于完全匹配。 例如，要选择名为“bearerToken”的标头，请结合使用等号运算符和选择器集作为 bearerToken。
- **开头**：此运算符与以指定的选择器值开头的所有字段匹配。 
- **结尾**：此运算符与以指定的选择器值结尾的所有请求字段匹配。 
- **包含**：此运算符与包含指定的选择器值的所有请求字段匹配。

在所有情况下，匹配不区分大小写，并且正则表达式不允许作为选择器。

## <a name="next-steps"></a>后续步骤

配置 WAF 设置后，可以了解如何查看 WAF 日志。 有关详细信息，请参阅[应用程序网关诊断](application-gateway-diagnostics.md#diagnostic-logging)。