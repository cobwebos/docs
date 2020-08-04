---
title: Azure 应用程序网关的新增功能
description: 了解 Azure 应用程序网关的新增功能，例如最新的发行说明、已知问题、bug 修复、已弃用的功能和即将发生的更改。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: overview
ms.date: 06/10/2020
ms.author: victorh
ms.openlocfilehash: 6ea0b28f5e0f23e0f8bb30e8fe2b0f792bce0709
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/28/2020
ms.locfileid: "87287445"
---
# <a name="whats-new-in-azure-application-gateway"></a>Azure 应用程序网关有哪些新增功能？

Azure 应用程序网关会不断更新。 为了让大家随时了解最新的开发成果，本文将提供以下方面的信息：

- 最新版本
- 已知问题
- Bug 修复
- 已弃用的功能

## <a name="new-features"></a>新增功能

|功能  |说明  |添加日期  |
|---------|---------|---------|
| 侦听器中的通配符主机名（预览） | 现在可以在多站点侦听器中定义通配符主机名，每个侦听器最多可以定义 5 个主机名。 若要了解详细信息，请参阅[侦听器中的通配符主机名（预览）](multiple-site-overview.md#wildcard-host-names-in-listener-preview) | 2020 年 7 月 |
| URL 重写（预览） | 现在可以使用 URL 重写功能来重写 URL 路径和查询字符串参数。 请参阅[重写 HTTP 标头和 URL](rewrite-http-headers-url.md) 以了解更多信息，并参阅[使用 Azure 门户在应用程序网关中重写 URL](rewrite-url-portal.md)以获取分步指南。 | 2020 年 7 月 |
| 运行状况探测的自定义端口 | 现可通过应用程序网关 v2 SKU 在运行状况探测配置中提供自定义端口。 有关详细信息，请参阅[运行状况探测概述](application-gateway-probe-overview.md) | 2020 年 7 月 |
| 应用程序网关入口控制器 (AGIC) AKS 附加产品（预览版） |现可通过 Azure CLI 将应用程序网关入口控制器单行部署为本机 AKS 附加产品。 作为 AKS 附加产品，AGIC 可成为一项完全托管服务，同时仍在客户的 AKS 群集中运行。 有关详细信息，请参阅 [AGIC 附加产品差异](ingress-controller-overview.md#difference-between-helm-deployment-and-aks-add-on)。 |2020 年 6 月 |
| v2（预览版）上的用户定义路由 (UDR) |在某些情况下，应用程序网关 v2 SKU 现在支持用户定义的路由。 有关详细信息，请参阅[应用程序网关配置概述](configuration-overview.md#user-defined-routes-supported-on-the-application-gateway-subnet)。 |2020 年 3 月 |
|关联 cookie 更改 |启用基于 Cookie 的关联时，除了现有的 ApplicationGatewayAffinity Cookie 外，应用程序网关还会注入另一个名为“ApplicationGatewayAffinityCORS”的相同 Cookie。 ApplicationGatewayAffinityCORS 又添加了两个属性 (SameSite=None; Secure)，这样即使对于跨域请求也可以保持粘性会话。 有关详细信息，请参阅[基于应用程序网关 Cookie 的关联](configuration-overview.md#cookie-based-affinity)。 |2020 年 2 月 |
|探测增强功能 |借助应用程序网关 v2 SKU 中的自定义探测增强功能，我们简化了[探测配置](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-probe-portal#create-probe-for-application-gateway-v2-sku)，加速了[按需后端运行状况测试](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-probe-portal#test-backend-health-with-the-probe)，并添加了[更多诊断信息](https://docs.microsoft.com/azure/application-gateway/application-gateway-backend-health-troubleshooting#error-messages)，以帮助你解决后端运行状况问题。  |2019 年 10 月 |
|更多指标 |我们添加了以下新指标以帮助你监视应用程序网关 v2 SKU：[与计时相关的指标](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics#timing-metrics)、后端响应状态、接收的字节数、发送的字节数、客户端 TLS 协议和当前计算单元。 请参阅[应用程序网关 V2 SKU 支持的指标](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics#metrics-supported-by-application-gateway-v2-sku)。 |2019 年 8 月 |
|WAF 自定义规则 |应用程序网关 WAF_v2 现在支持创建自定义规则。 请参阅[应用程序网关自定义规则](custom-waf-rules-overview.md)。 |2019 年 6 月 |
|自动缩放、区域冗余、静态 VIP 支持 GA |v2 SKU 正式版，支持自动缩放、区域冗余、增强性能、静态 VIP、Key Vault、标头重写。 请参阅[应用程序网关自动缩放文档](application-gateway-autoscaling-zone-redundant.md)。 |2019 年 4 月 |
|密钥保管库集成 |应用程序网关现在支持与密钥保管库（公共预览版）集成，以获取附加到支持 HTTPS 的侦听器的服务器证书。 请参阅[使用 Key Vault 证书进行 TLS 终止](key-vault-certs.md)。 |2019 年 4 月 |
|标头 CRUD/重写     |现在可以重写 HTTP 标头。 请参阅[教程：创建应用程序网关和重写 HTTP 标头](tutorial-http-header-rewrite-powershell.md)。|2018 年 12 月|
|WAF 配置和排除列表     |我们添加了更多选项来帮助你配置 WAF 和减少误报。 有关详细信息，请参阅 [Web 应用程序防火墙请求大小限制和排除列表](application-gateway-waf-configuration.md)。|2018 年 12 月|
|自动缩放、区域冗余、静态 VIP 支持      |v2 SKU 有了多方面的改进，例如自动缩放、性能提高，等等。 有关详细信息，请参阅[什么是 Azure 应用程序网关？](overview.md)。|2018 年 9 月|
|连接清空     |使用连接清空能够正常删除后端池中的成员。 有关详细信息，请参阅[连接清空](features.md#connection-draining)。|2018 年 9 月|
|自定义错误页     |使用自定义错误页可在剩余网站的格式中创建错误页。 若要启用此功能，请参阅[创建应用程序网关自定义错误页](custom-error.md)。|2018 年 9 月|
|指标增强     |可以更好地查看应用程序网关的状态和增强的指标。 若要在应用程序网关中启用指标，请参阅[应用程序网关的后端运行状况、诊断日志和指标](application-gateway-diagnostics.md)。|2018 年 6 月|

## <a name="next-steps"></a>后续步骤

有关 Azure 应用程序网关的详细信息，请参阅[什么是 Azure 应用程序网关？](overview.md)
