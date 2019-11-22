---
title: Azure 应用程序网关的新增功能
description: 了解 Azure 应用程序网关的新增功能，例如最新的发行说明、已知问题、bug 修复、已弃用的功能和即将发生的更改。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: overview
ms.date: 4/30/2019
ms.author: victorh
ms.openlocfilehash: b2e7b9f28c84b769fe64be82f91418cc2a7d634f
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73044373"
---
# <a name="whats-new-in-azure-application-gateway"></a>Azure 应用程序网关有哪些新增功能？

Azure 应用程序网关会不断更新。 为了让大家随时了解最新的开发成果，本文将提供以下方面的信息：

- 最新版本
- 已知问题
- Bug 修复
- 已弃用的功能

## <a name="new-features"></a>新增功能

|Feature  |说明  |添加日期  |
|---------|---------|---------|
|探测增强功能 |借助应用程序网关 v2 SKU 中的自定义探测增强功能，我们简化了[探测配置](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-probe-portal#create-probe-for-application-gateway-v2-sku)，加速了[按需后端运行状况测试](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-probe-portal#test-backend-health-with-the-probe)，并添加了[更多诊断信息](https://docs.microsoft.com/azure/application-gateway/application-gateway-backend-health-troubleshooting#error-messages)，以帮助你解决后端运行状况问题。  |2019 年 10 月 |
|更多指标 |我们添加了以下新指标，可帮助你监视应用程序网关 v2 SKU：[与计时相关的指标](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics#timing-metrics)、后端响应状态、接收的字节数、发送的字节数、客户端 TLS 协议和当前计算单元。 请参阅[应用程序网关 V2 SKU 支持的指标](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics#metrics-supported-by-application-gateway-v2-sku)。 |2019 年 8 月 |
|WAF 自定义规则 |应用程序网关 WAF_v2 现在支持创建自定义规则。 请参阅[应用程序网关自定义规则](custom-waf-rules-overview.md)。 |2019 年 7 月 |
|自动缩放、区域冗余、静态 VIP 支持 GA |v2 SKU 正式版，支持自动缩放、区域冗余、增强性能、静态 VIP、密钥保管库、标头重写。 请参阅[应用程序网关自动缩放文档](application-gateway-autoscaling-zone-redundant.md)。 |2019 年 4 月 |
|密钥保管库集成 |应用程序网关现在支持与密钥保管库（公共预览版）集成，以获取附加到支持 HTTPS 的侦听器的服务器证书。 请参阅[使用密钥保管库证书实现 SSL 终止](key-vault-certs.md)。 |2019 年 4 月 |
|标头 CRUD/重写     |现在可以重写 HTTP 标头。 有关分步说明，请参阅[教程：创建应用程序网关和重写 HTTP 标头](tutorial-http-header-rewrite-powershell.md)。|2018 年 12 月|
|WAF 配置和排除列表     |我们添加了更多选项来帮助你配置 WAF 和减少误报。 有关详细信息，请参阅 [Web 应用程序防火墙请求大小限制和排除列表](application-gateway-waf-configuration.md)。|2018 年 12 月|
|自动缩放、区域冗余、静态 VIP 支持      |v2 SKU 有了多方面的改进，例如自动缩放、性能提高，等等。 有关详细信息，请参阅[什么是 Azure 应用程序网关？](overview.md)。|2018 年 9 月|
|连接清空     |使用连接清空能够正常删除后端池中的成员。 有关详细信息，请参阅[连接清空](overview.md#connection-draining)。|2018 年 9 月|
|自定义错误页     |使用自定义错误页可在剩余网站的格式中创建错误页。 若要启用此功能，请参阅[创建应用程序网关自定义错误页](custom-error.md)。|2018 年 9 月|
|指标增强     |可以更好地查看应用程序网关的状态和增强的指标。 若要在应用程序网关中启用指标，请参阅[应用程序网关的后端运行状况、诊断日志和指标](application-gateway-diagnostics.md)。|2018 年 6 月|

## <a name="next-steps"></a>后续步骤

有关 Azure 应用程序网关的详细信息，请参阅[什么是 Azure 应用程序网关？](overview.md)
