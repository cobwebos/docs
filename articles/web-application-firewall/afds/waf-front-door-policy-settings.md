---
title: 使用 Azure 前门的 Web 应用程序防火墙的策略设置
description: 了解 Web 应用程序防火墙 （WAF）。
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 08/21/2019
ms.author: victorh
ms.openlocfilehash: 08b21ccd7f7958f00546583f680ecb8cde4a20c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75932617"
---
# <a name="policy-settings-for-web-application-firewall-on-azure-front-door"></a>Azure 前门 Web 应用程序防火墙的策略设置

Web 应用程序防火墙 （WAF） 策略允许您通过一组自定义和托管规则控制对 Web 应用程序的访问。 WAF 策略名称必须是唯一的。 如果尝试使用现有名称，您将收到验证错误。 有多个策略级别设置适用于为该策略指定的所有规则，如本文所述。

## <a name="waf-state"></a>WAF 状态

前门的 WAF 策略可以处于以下两种状态之一：
- **已启用：** 启用策略后，WAF 会主动检查传入请求，并根据规则定义执行相应的操作
- **禁用：** - 禁用策略时，WAF 检查暂停。 传入请求将绕过 WAF，并根据前门路由发送到后端。

## <a name="waf-mode"></a>WAF 模式

WAF 策略可配置为在以下两种模式下运行：

- **检测模式**在检测模式下运行时，WAF 不会执行任何操作，除了监视和将请求及其匹配的 WAF 规则记录到 WAF 日志之外。 打开前门的日志记录诊断（使用门户时，可以通过访问 Azure 门户中的 **"诊断"** 部分来实现这一点）。

- **预防模式**当配置为在预防模式下运行时，如果请求与规则匹配，WAF 将执行指定的操作。 任何匹配的请求也会记录在 WAF 日志中。

## <a name="waf-response-for-blocked-requests"></a>阻止请求的 WAF 响应

默认情况下，当 WAF 由于匹配的规则而阻止请求时，它将返回 403 状态代码 -**请求被阻止**的消息。 还会返回一个引用字符串进行日志记录。

当 WAF 阻止请求时，可以定义自定义响应状态代码和响应消息。 支持以下自定义状态代码：

- 200 OK
- 403 禁止
- 405 方法不允许
- 406 不可接受
- 429 请求太多

自定义响应状态代码和响应消息是策略级别设置。 配置后，所有被阻止的请求都获取相同的自定义响应状态和响应消息。

## <a name="uri-for-redirect-action"></a>用于重定向操作的 URI

如果为 WAF 策略中包含的任何规则选择了**REDIRECT**操作，则需要定义 URI 以将请求重定向到。 此重定向 URI 需要是有效的 HTTP（S） 站点，配置后，所有具有"REDIRECT"操作的请求匹配规则都将重定向到指定的站点。


## <a name="next-steps"></a>后续步骤
- 了解如何定义 WAF[自定义响应](waf-front-door-configure-custom-response-code.md)
