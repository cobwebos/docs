---
title: 在 Azure 应用程序网关上自定义 Web 应用程序防火墙规则-Azure 门户
description: 本文提供了有关如何在应用程序网关上利用 Azure 门户自定义 Web 应用程序防火墙规则的信息。
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 10/24/2019
ms.author: victorh
ms.topic: article
ms.openlocfilehash: ab2722ed4a4aa4fe95be15cd536bfd5d959f9139
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73516924"
---
# <a name="customize-web-application-firewall-rules-using-the-azure-portal"></a>使用 Azure 门户自定义 Web 应用程序防火墙规则

Azure 应用程序网关 Web 应用程序防火墙（WAF）为 Web 应用程序提供保护。 这些保护通过打开 Web 应用程序安全性项目 (OWASP) 核心规则集 (CRS) 来提供。 某些规则可能会导致误报，并会阻止实际流量。 出于此原因，应用程序网关提供了自定义规则组和规则的功能。 有关特定规则组和规则的详细信息，请参阅[Web 应用程序防火墙 CRS 规则组和规则列表](application-gateway-crs-rulegroups-rules.md)。

>[!NOTE]
> 如果应用程序网关未使用 WAF 层，会在右侧窗格中显示“将应用程序网关升级到 WAF 层”选项。 

![启用 WAF][fig1]

## <a name="view-rule-groups-and-rules"></a>查看规则组和规则

**查看规则组和规则**
1. 浏览到应用程序网关并选择“Web 应用程序防火墙”。  
2. 选择**WAF 策略**。
2. 选择 "**托管规则**"。

   此视图会在随所选规则集提供的所有规则组页上显示一个表。 已选中所有规则的复选框。

## <a name="disable-rule-groups-and-rules"></a>禁用规则组和规则

> [!IMPORTANT]
> 禁用任何规则组或规则时要格外小心。 这可能会加大你的安全风险。

禁用规则时可以禁用整个规则组，也可以禁用一个或多个规则组下的特定规则。 

**禁用规则组或特定规则**

   1. 搜索想要禁用的规则或规则组。
   2. 选中要禁用的规则对应的复选框。 
   3. 选择页面顶部的 "操作" （启用/禁用）作为所选规则。
   2. 选择“保存”。 

![保存更改][3]

## <a name="mandatory-rules"></a>强制性规则

以下列表包含导致 WAF 在防护模式下阻止请求的条件。 在检测模式下，它们将记录为异常。

无法配置或禁用这些规则：

* 除非关闭正文检查（XML、JSON、表单数据），否则无法分析请求正文会导致请求被阻止
* 请求正文（不带文件）数据长度大于配置的限制
* 请求正文（包括文件）大于限制
* WAF 引擎发生内部错误

CRS 3.x 特定：

* 入站异常分数超出阈值

## <a name="next-steps"></a>后续步骤

配置你禁用的规则后，可以了解如何查看 WAF 日志。 有关详细信息，请参阅[应用程序网关诊断](../../application-gateway/application-gateway-diagnostics.md#diagnostic-logging)。

[fig1]: ../media/application-gateway-customize-waf-rules-portal/1.png
[3]: ../media/application-gateway-customize-waf-rules-portal/figure3.png
