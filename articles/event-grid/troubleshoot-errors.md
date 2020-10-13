---
title: Azure 事件网格 - 故障排除指南
description: 本文提供错误代码列表、错误消息、说明和建议的措施。
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: ab52cea6ab43763cf2d9dc2b57b7f369072a399e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86119032"
---
# <a name="troubleshoot-azure-event-grid-errors"></a>排查 Azure 事件网格错误
本故障排除指南提供 Azure 事件网格错误代码列表、错误消息、说明以及在收到这些错误时应采取的建议措施。 

## <a name="error-code-400"></a>错误代码：400
| 错误代码 | 错误消息 | 说明 | 建议 |
| ---------- | ------------- | ----------- | -------------- | 
| HttpStatusCode.BadRequest<br/>400 | 主题名称的长度必须为 3 到 50 个字符。 | 自定义主题名称的长度应为 3 到 50 个字符。 主题名称中只允许字母数字字符、数字和“-”字符。 此外，名称的开头不能是以下保留字： <ul><li>Microsoft</li><li>EventGrid</li><li>系统</li></ul> | 请选择符合主题名称要求的其他主题名称。 |
| HttpStatusCode.BadRequest<br/>400 | 域名的长度必须为 3 到 50 个字符。 | 域名的长度应为 3 到 50 个字符。 主题名称中只允许字母数字字符、数字和“-”字符。 此外，名称的开头不能是以下保留字：<ul><li>Microsoft</li><li>EventGrid</li><li>系统</li> | 请选择符合域名要求的其他域名。 |
| HttpStatusCode.BadRequest<br/>400 | 过期时间无效。 | 事件订阅的过期时间决定了事件订阅何时停用。 此值应是将来的有效日期时间值。| 确保事件订阅过期时间采用有效的日期时间格式，并设置为将来的时间。 |

## <a name="error-code-409"></a>错误代码：409
| 错误代码 | 错误消息 | 说明 | 建议的操作 |
| ---------- | ------------- | ----------- | -------------- | 
| HttpStatusCode.Conflict <br/>409 | 已存在具有指定名称的主题。 请选择其他主题名称。   | 自定义主题名称在单个 Azure 区域中应保持唯一，以确保正常完成发布操作。 同一名称可在不同的 Azure 区域中使用。 | 请为主题选择其他名称。 |
| HttpStatusCode.Conflict <br/> 409 | 已存在具有指定名称的域。 请选择其他域名。 | 域名在单个 Azure 区域中应保持唯一，以确保正常完成发布操作。 同一名称可在不同的 Azure 区域中使用。 | 请为该域选择其他名称。 |
| HttpStatusCode.Conflict<br/>409 | 已达配额限制。 有关这些限制的详细信息，请参阅 [Azure 事件网格限制](../azure-resource-manager/management/azure-subscription-service-limits.md#event-grid-limits)。  | 每个 Azure 订阅可使用的 Azure 事件网格资源数量有限制。 已超过部分或全部配额，无法创建更多的资源。 |    请检查当前的资源用量，并删除任何不需要的资源。 如果仍需提高配额，请向 [aeg@microsoft.com](mailto:aeg@microsoft.com) 发送电子邮件并在其中指出所需的确切资源数。 |

## <a name="error-code-403"></a>错误代码：403

| 错误代码 | 错误消息 | 说明 | 建议的操作 |
| ---------- | ------------- | ----------- | ------------------ |
| HttpStatusCode。禁止 <br/>403 | 由于 IpAddress 筛选规则，客户端 {IpAddress} 发布到 {主题/域} 已被拒绝。 | 主题或域配置了 IP 防火墙规则，访问仅限于配置的 IP 地址。 | 向 IP 防火墙规则添加 IP 地址，请参阅 [配置 ip 防火墙](configure-firewall.md) |
| HttpStatusCode。禁止 <br/> 403 | 由于请求来自专用终结点，并且没有为资源找到匹配的专用终结点连接，因此，客户端发布到 {主题/域} 时被拒绝。 | 主题或域已配置专用终结点，并且发布请求来自未配置/批准的专用终结点。 | 配置主题/域的专用终结点。 [配置专用终结点](configure-private-endpoints.md) |

## <a name="troubleshoot-event-subscription-validation"></a>排查事件订阅验证问题

在创建事件订阅的过程中，如果看到诸如 `The attempt to validate the provided endpoint https://your-endpoint-here failed. For more details, visit https://aka.ms/esvalidation` 之类的错误消息，则表明验证握手失败。 若要解决此错误，请验证以下各方面：

- 使用 Postman 或 curl 或类似工具，通过一个[示例 SubscriptionValidationEvent](webhook-event-delivery.md#validation-details) 请求正文向 Webhook URL 发出 HTTP POST 请求。
- 如果 Webhook 实现了同步验证握手机制，请验证 ValidationCode 是否作为响应的一部分返回。
- 如果 Webhook 实现了异步验证握手机制，请验证 HTTP POST 是否返回了“200 正常”。
- 如果 Webhook 在响应中返回了“403 (禁止访问)”，请检查 Webhook 是否位于 Azure 应用程序网关或 Web 应用程序防火墙后面。 如果是，则需要禁用这些防火墙规则，然后重新执行 HTTP POST：

  920300（请求缺少 Accept 标头，我们可以解决此问题）

  942430（受限 SQL 字符异常情况检测 (args)：已超出特殊字符数 (12)）

  920230（检测到多个 URL 编码）

  942130（SQL 注入攻击：检测到 SQL 同义反复。）

  931130（可能的远程文件包含 (RFI) 攻击 = 域外引用/链接）


## <a name="next-steps"></a>后续步骤
如需更多帮助，请在 [Stack Overflow 论坛](https://stackoverflow.com/questions/tagged/azure-eventgrid)中发布问题，或开具[支持票证](https://azure.microsoft.com/support/options/)。 
