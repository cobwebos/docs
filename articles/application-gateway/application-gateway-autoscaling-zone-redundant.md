---
title: Azure 中的自动缩放和区域冗余应用程序网关（公共预览版）
description: 本文介绍 Azure 应用程序 v2 SKU，其中包括自动缩放和区域冗余功能。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 3/6/2019
ms.author: victorh
ms.openlocfilehash: f7d1c5bc54d909d1a948123839d95e1ee1158a5c
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/26/2019
ms.locfileid: "58444826"
---
# <a name="autoscaling-and-zone-redundant-application-gateway-public-preview"></a>自动缩放和区域冗余应用程序网关（公共预览版）

应用程序网关和 Web 应用程序防火墙 (WAF) 现依据新 v2 SKU 提供可用的公共预览版，可增强性能并添加对关键新功能（如自动缩放、区域冗余）的支持，并支持静态 VIP。 新的 v2 SKU 将继续支持通用版 SKU 的现有功能，并且已知限制部分几乎未列出异常。 新的 v2 SKU 包括以下增强功能：

- 自动缩放：凭借自动缩放 SKU，应用程序网关或 WAF 部署可根据变化中的流量负载模式增加或减少。 自动缩放还无需在预配期间要求选择部署大小或实例计数。 此 SKU 提供，则返回 true 的弹性。 在新 SKU 中，应用程序网关可同时在固定容量（自动缩放已禁用）和已启用自动缩放的模式下进行操作。 固定容量模式对具有一致性和可预测工作负荷的方案非常有用。 自动缩放模式有利于在应用程序流量中出现大量变化的应用程序中。

- **区域冗余**：应用程序网关或 WAF 部署可跨多个可用性区域，因此不需使用流量管理器在每个区域预配和固定单独的应用程序网关实例。 可以选择一个区域或多个区域来部署应用程序网关实例，以便确保区域故障复原。 应用程序的后端池可以通过类似方式分布在多个可用性区域中。
- **性能增强**：与通用版 SKU 相比，自动缩放 SKU 可提供高达 5 倍的 SSL 卸载性能。
- 缩短部署和更新时间：与通用版 SKU 相比，自动缩放 SKU 缩短了部署和更新时间。
- **静态 VIP**：应用程序网关 VIP 现在支持独占形式的静态 VIP 类型。 这可确保即使重新启动后不会更改关联应用程序网关的 VIP。

> [!IMPORTANT]
> 自动缩放和区域冗余应用程序网关 SKU 目前处于公共预览状态。 此预览版在提供时没有附带服务级别协议，不建议用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

![](./media/application-gateway-autoscaling-zone-redundant/application-gateway-autoscaling-zone-redundant.png)

## <a name="feature-comparison-between-v1-sku-and-v2-sku"></a>SKU v1 和 v2 SKU 之间的功能比较

下表比较了每个 SKU 提供的功能。

|                                                   | v1 SKU   | v2 SKU   |
| ------------------------------------------------- | -------- | -------- |
| 自动缩放                                       |          | &#x2713; |
| 区域冗余                                   |          | &#x2713; |
| &nbsp;静态 VIP&nbsp;&nbsp;                      |          | &#x2713; |
| 基于 URL 的路由                                 | &#x2713; | &#x2713; |
| 多站点托管                             | &#x2713; | &#x2713; |
| 流量重定向                               | &#x2713; | &#x2713; |
| Web 应用程序防火墙 (WAF)                    | &#x2713; | &#x2713; |
| 安全套接字层 (SSL) 终止            | &#x2713; | &#x2713; |
| 端到端 SSL 加密                         | &#x2713; | &#x2713; |
| 会话相关性                                  | &#x2713; | &#x2713; |
| 自定义错误页                                | &#x2713; | &#x2713; |
| 重写 HTTP (S) 标头                           |          | &#x2713; |
| WebSocket 支持                                 | &#x2713; | &#x2713; |
| HTTP/2 支持                                    | &#x2713; | &#x2713; |
| 连接清空                               | &#x2713; | &#x2713; |
| Azure Kubernetes 服务 (AKS) 入口控制器 |          | &#x2713; |

## <a name="supported-regions"></a>支持的区域

已在以下区域推出自动缩放 SKU：美国中北部、美国中南部、美国西部、美国西部 2、美国东部、美国东部 2、美国中部、欧洲北部、欧洲西部、亚洲东南部、法国中部、英国西部、日本东部、日本西部。

## <a name="pricing"></a>定价

在预览期间，不收费。 应用程序网关，如 Key Vault，虚拟机以外的资源付费，依此类推。

## <a name="known-issues-and-limitations"></a>已知问题和限制

|问题|详细信息|
|--|--|
|身份验证证书|不支持。<br>有关详细信息，请参阅[应用程序网关的端到端 SSL 概述](ssl-overview.md#end-to-end-ssl-with-the-v2-sku)。|
|在同一子网上混合使用 Standard_v2 和标准应用程序网关|不支持|
|应用程序网关子网上的用户定义路由 (UDR)|不支持|
|入站端口范围的 NSG| 对于 Standard_v2 SKU，为 - 65200 到 65535<br>对于标准 SKU，为 - 65503 到 65534<br>有关详细信息，请参阅[常见问题](application-gateway-faq.md#are-network-security-groups-supported-on-the-application-gateway-subnet)。|
|Azure 诊断中的性能日志|不支持。<br>应当使用 Azure 指标。|
|计费|目前不收费。|
|FIPS 模式|目前不支持。|
|“仅 ILB”模式|目前不支持。 同时支持公共和 ILB 模式。|
|Netwatcher 集成|在公共预览版中不受支持。|

## <a name="next-steps"></a>后续步骤
- [使用 Azure PowerShell 创建具有预留虚拟 IP 地址的自动缩放区域冗余应用程序网关](tutorial-autoscale-ps.md)
- 了解有关[应用程序网关](overview.md)的详细信息。
- 了解有关 [Azure 防火墙](../firewall/overview.md)的详细信息。
