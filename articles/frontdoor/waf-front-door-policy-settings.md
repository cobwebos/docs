---
title: Web 应用程序防火墙与 Azure 第一道防线的策略设置
description: 了解 web 应用程序防火墙 (WAF)。
services: frontdoor
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/08/2019
ms.author: tyao;kumud
ms.openlocfilehash: 4c2f070e9b3c972f063008df8880b196ddb069cc
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/09/2019
ms.locfileid: "59362935"
---
# <a name="policy-settings-for-web-application-firewall-with-azure-front-door"></a>Web 应用程序防火墙与 Azure 第一道防线的策略设置

Web 应用程序防火墙 (WAF) 策略，可通过一组自定义和托管的规则来控制对 web 应用程序。 WAF 策略名称必须是唯一的。 如果你尝试使用现有的名称，将收到验证错误。 有多个级别的策略设置适用于在本文中所述，为该策略指定的所有规则。

## <a name="waf-state"></a>WAF 状态

第一道防线的 WAF 策略可以采用以下两种状态之一：
- **已启用：** WAF 启用策略后，主动检查传入的请求，并执行相应操作根据规则定义
- **已禁用：** -当策略被禁用时，WAF 检查已暂停。 传入的请求将绕过 WAF，发送到后端根据第一道防线路由。

## <a name="waf-mode"></a>WAF 模式

WAF 策略可以配置为在以下两种模式中运行：

- **检测模式**在检测模式下运行时，WAF 不采取任何措施之外监视器和请求和其匹配的 WAF 规则记录到 WAF 日志。 启用日志记录诊断的第一道防线 (使用门户时，这可以通过转到**诊断**在 Azure 门户中的部分)。

- **阻止模式下**WAF 配置为在阻止模式下运行时，采用指定的操作，如果某个请求与规则匹配。 此外在 WAF 日志中记录任何匹配的请求。

## <a name="waf-response-for-blocked-requests"></a>阻止的请求的 WAF 响应

默认情况下，WAF 因为匹配规则，阻止请求时它将返回 403 状态代码-**请求被阻塞**消息。 用于日志记录，也会返回引用字符串。

请求被阻止的 WAF 时，可以定义自定义响应状态代码和响应消息。 支持以下自定义状态代码：

- 200 确定
- 403    Forbidden
- 不允许使用 405 方法
- 406 不可接受
- 429 太多请求

自定义响应状态代码和响应消息是一个策略级别设置。 它在配置后，所有阻止的请求获取的相同自定义响应状态和响应消息。

## <a name="uri-for-redirect-action"></a>重定向操作的 URI

需要定义用于在将请求重定向 URI**重定向**操作选择的任何 WAF 策略中包含的规则。 此重定向 URI 必须是有效的 HTTP (S) 站点和配置完成后，使用"重定向"操作的匹配规则将重定向到指定站点的所有请求。


## <a name="next-steps"></a>后续步骤
- 了解如何定义 WAF[自定义响应](waf-front-door-configure-custom-response-code.md)
