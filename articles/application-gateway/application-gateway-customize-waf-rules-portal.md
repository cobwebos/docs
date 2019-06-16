---
title: 自定义 Azure 应用程序网关的 Web 应用程序防火墙规则 - Azure 门户
description: 本文将介绍如何使用 Azure 门户自定义应用程序网关的 Web 应用程序防火墙规则。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 2/22/2019
ms.author: victorh
ms.openlocfilehash: f7ffb8d6adfd4afc75618834a3fe82cf9a3d0c9f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "64720382"
---
# <a name="customize-web-application-firewall-rules-through-the-azure-portal"></a>通过 Azure 门户自定义 Web 应用程序防火墙规则

Azure 应用程序网关 Web 应用程序防火墙 (WAF) 可为 Web 应用程序提供保护。 这些保护通过打开 Web 应用程序安全性项目 (OWASP) 核心规则集 (CRS) 来提供。 某些规则可能会导致误报，并会阻止实际流量。 出于此原因，应用程序网关提供了自定义规则组和规则的功能。 有关特定规则组和规则的详细信息，请参阅 [Web 应用程序防火墙 CRS 规则组和规则列表](application-gateway-crs-rulegroups-rules.md)。

>[!NOTE]
> 如果应用程序网关未使用 WAF 层，会在右侧窗格中显示“将应用程序网关升级到 WAF 层”选项。 

![启用 WAF][fig1]

## <a name="view-rule-groups-and-rules"></a>查看规则组和规则

**查看规则组和规则**
   1. 浏览到应用程序网关并选择“Web 应用程序防火墙”  。  
   2. 选择“高级规则配置”  。  
   此视图会在随所选规则集提供的所有规则组页上显示一个表。 已选中所有规则的复选框。

![配置已禁用的规则][1]

## <a name="search-for-rules-to-disable"></a>搜索要禁用的规则

 “Web 应用程序防火墙设置”页面提供了通过文本搜索筛选规则的功能。 结果仅显示包含所搜索的文本的规则组和规则。

![搜索规则][2]

## <a name="disable-rule-groups-and-rules"></a>禁用规则组和规则

> [!IMPORTANT]
> 禁用任何规则组或规则时要格外小心。 这可能会加大你的安全风险。

禁用规则时可以禁用整个规则组，也可以禁用一个或多个规则组下的特定规则。 

**禁用规则组或特定规则**

   1. 搜索想要禁用的规则或规则组。
   2. 取消选中与要禁用的规则对应的复选框。 
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

配置你禁用的规则后，可以了解如何查看 WAF 日志。 有关详细信息，请参阅[应用程序网关诊断](application-gateway-diagnostics.md#diagnostic-logging)。

[fig1]: ./media/application-gateway-customize-waf-rules-portal/1.png
[1]: ./media/application-gateway-customize-waf-rules-portal/figure1.png
[2]: ./media/application-gateway-customize-waf-rules-portal/figure2.png
[3]: ./media/application-gateway-customize-waf-rules-portal/figure3.png
