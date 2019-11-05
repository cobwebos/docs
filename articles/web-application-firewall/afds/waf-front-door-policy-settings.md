---
title: 具有 Azure 前门的 Web 应用程序防火墙的策略设置
description: 了解 Web 应用程序防火墙（WAF）。
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 08/21/2019
ms.author: victorh
ms.openlocfilehash: f763ad9afd2238ebe63bf878882011eb3745df5c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73517106"
---
# <a name="policy-settings-for-web-application-firewall-on-azure-front-door"></a>Azure 前门上的 Web 应用程序防火墙的策略设置

使用 Web 应用程序防火墙（WAF）策略，可以通过一组自定义规则和托管规则来控制对 Web 应用程序的访问。 WAF 策略名称必须是唯一的。 如果尝试使用现有名称，将收到验证错误。 如本文所述，有多个策略级别设置适用于为该策略指定的所有规则。

## <a name="waf-state"></a>WAF 状态

前门的 WAF 策略可处于以下两种状态之一：
- **已启用：** 启用策略后，WAF 将主动检查传入的请求，并根据规则定义采取相应的操作
- **Disabled：** -禁用策略后，WAF 检查将暂停。 传入的请求将绕过 WAF，并根据前门路由将其发送到后端。

## <a name="waf-mode"></a>WAF 模式

可以将 WAF 策略配置为在以下两种模式下运行：

- **检测模式**在检测模式下运行时，WAF 不会执行任何其他操作，而不是监视请求，并将请求及其匹配的 WAF 规则记录到 WAF 日志。 为前门启用日志记录诊断（使用门户时，可通过转到 Azure 门户中的 "**诊断**" 部分来实现）。

- **防护模式**配置为在阻止模式下运行时，如果请求与规则匹配，WAF 将采用指定的操作。 任何匹配的请求也会记录在 WAF 日志中。

## <a name="waf-response-for-blocked-requests"></a>阻止的请求的 WAF 响应

默认情况下，当 WAF 由于匹配规则而阻止请求时，它将返回403状态代码，**请求被阻止**。 还会为日志记录返回引用字符串。

当 WAF 阻止请求时，可以定义自定义响应状态代码和响应消息。 支持以下自定义状态代码：

- 200正常
- 403禁止
- 不允许使用405方法
- 406不可接受
- 429请求太多

自定义响应状态代码和响应消息是一种策略级别设置。 配置完成后，所有被阻止的请求均获得相同的自定义响应状态和响应消息。

## <a name="uri-for-redirect-action"></a>重定向操作的 URI

如果为 WAF 策略中包含的任何规则选择了**重定向**操作，则需要定义将请求重定向到的 URI。 此重定向 URI 必须是有效的 HTTP （S）站点，一旦配置后，将会重定向到指定站点的所有与规则匹配的请求。


## <a name="next-steps"></a>后续步骤
- 了解如何定义 WAF[自定义响应](waf-front-door-configure-custom-response-code.md)
