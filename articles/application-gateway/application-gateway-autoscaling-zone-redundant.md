---
title: 自动缩放和区域冗余应用程序网关 v2
description: 本文介绍 Azure 应用程序 Standard_v2 和 WAF_v2 SKU，其中包括自动缩放和区域冗余功能。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 06/06/2020
ms.author: victorh
ms.custom: fasttrack-edit, references_regions
ms.openlocfilehash: 8c989e426faa77025a84515fe0a19424cb6bfa89
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91826635"
---
# <a name="autoscaling-and-zone-redundant-application-gateway-v2"></a>自动缩放和区域冗余应用程序网关 v2 

应用程序网关在 Standard_v2 SKU 下提供。 Web 应用程序防火墙 (WAF) 在 WAF_v2 SKU 下提供。 v2 SKU 提供性能增强，并添加了对自动缩放、区域冗余等关键新功能以及静态 VIP 的支持。 Standard 和 WAF SKU 中的现有功能在新的 v2 SKU 中仍受支持，不过存在几种例外情况，具体请参阅[比较](#differences-from-v1-sku)部分。

新的 v2 SKU 包括以下增强：

- 自动**缩放**：自动缩放 SKU 下的应用程序网关或 WAF 部署可以根据不断变化的流量负载模式进行扩展或缩减。 自动缩放还无需在预配期间要求选择部署大小或实例计数。 此 SKU 提供真正的弹性。 在 Standard_v2 和 WAF_v2 SKU 中，应用程序网关可同时在固定容量（自动缩放已禁用）和已启用自动缩放的模式下运行。 固定容量模式对具有一致性和可预测工作负荷的方案非常有用。 应用程序流量会出现差异的应用程序可以受益于自动缩放模式。
- **区域冗余**：应用程序网关或 WAF 部署可跨多个可用性区域，因此不需要使用流量管理器在每个区域中单独预配应用程序网关实例。 可以选择一个或多个区域来部署应用程序网关实例，以便更灵活地应对区域故障。 应用程序的后端池可以通过类似方式分布在多个可用性区域中。

  仅当 Azure 区域可用时，区域冗余才可用。 在其他区域中，支持所有其他功能。 有关详细信息，请参阅 [Azure 中的区域和可用性区域](../availability-zones/az-overview.md)
- **静态 VIP**：应用程序网关 v2 SKU 支持独占形式的静态 VIP 类型。 这可以确保与应用程序网关关联的 VIP 在部署的整个生命周期内不会更改，即使发生重启。  v1 中没有静态 VIP，因此必须使用应用程序网关 URL（而不是 IP 地址）通过应用程序网关将域名路由到应用服务。
- **标头重写**：应用程序网关允许使用 v2 SKU 添加、删除或更新 HTTP 请求和响应标头。 有关详细信息，请参阅[使用应用程序网关重写 HTTP 标头](rewrite-http-headers.md)
- **Key Vault 集成**：应用程序网关 v2 支持与密钥保管库集成，以获取附加到支持 HTTPS 的侦听器的服务器证书。 有关详细信息，请参阅[使用 Key Vault 证书实现 TLS 终止](key-vault-certs.md)。
- Azure Kubernetes 服务入口控制器：应用程序网关 v2 入口控制器允许将 Azure 应用程序网关用作 Azure Kubernetes 服务 (AKS) 的入口（称为 AKS 群集）。 有关详细信息，请参阅[什么是应用程序网关入口控制器？](ingress-controller-overview.md)。
- **性能增强**：v2 SKU 提供的 TLS 卸载性能比 Standard/WAF SKU 高达 5 倍。
- 缩短部署和更新时间：与标准/WAF SKU 相比，v2 SKU 缩短了部署和更新时间。 这还包括了 WAF 配置更改。

![自动缩放区域的示意图。](./media/application-gateway-autoscaling-zone-redundant/application-gateway-autoscaling-zone-redundant.png)

## <a name="supported-regions"></a>支持的区域

已在以下区域推出 Standard_v2 和 WAF_v2 SKU：美国中北部、美国中南部、美国西部、美国西部 2、美国东部、美国东部 2、美国中部、欧洲北部、欧洲西部、东南亚、法国中部、英国西部、日本东部、日本西部、澳大利亚东部、澳大利亚东南部、巴西南部、加拿大中部、加拿大东部、东亚、韩国中部、韩国南部、英国南部、印度中部、印度西部、印度南部。

## <a name="pricing"></a>定价

使用 v2 SKU 时，定价模型将由消耗量驱动，而不再与实例计数或大小相关。 v2 SKU 定价包括两个部分：

- 固定价格 - 这是用于预配 Standard_v2 或 WAF_v2 网关的每小时（或部分小时）价格。 请注意，0 个额外最小实例仍可确保服务的高可用性，这始终包含在固定价格中。
- **容量单位价格** - 这是在固定价格的基础上按消耗量计收的费用。 容量单位费用也按每小时或部分每小时进行计算。 容量单位有三个维度 - 计算单位、持久连接和吞吐量。 计算单位用于度量消耗的处理器容量。 影响计算单位的因素包括每秒 TLS 连接数、URL 重写计算和 WAF 规则处理。 持久连接用于度量在给定计费间隔内与应用程序网关建立的 TCP 连接数。 吞吐量是在给定计费间隔内，平均每秒由系统处理的兆位数。  对于超过预留实例计数的任何内容，均按容量单位级别进行计费。

每个容量单位最多包括：1 个计算单位，2500 个持久连接和 2.22-Mbps 吞吐量。

若要了解详细信息，请参阅[了解定价](understanding-pricing.md)。

## <a name="scaling-application-gateway-and-waf-v2"></a>缩放应用程序网关和 WAF v2

可将应用程序网关和 WAF 配置为以两种模式进行缩放：

- **自动缩放** - 启用自动缩放后，应用程序网关和 WAF v2 SKU 将会根据应用程序流量要求进行纵向缩放。 此模式可为应用程序提供更好的弹性，无需猜测应用程序网关大小或实例计数。 使用此模式还可以不要求网关为预期的最大流量负载以最大预配容量运行，从而节省成本。 必须指定最小和（可选）最大实例计数。 最小容量可确保应用程序网关和 WAF v2 不低于指定的最小实例计数，即使在没有流量时也是如此。 每个实例大约相当于 10 个额外的预留容量单元。 零表示没有保留容量，在本质上是纯自动缩放。 还可以选择指定最大实例计数，以确保应用程序网关不会缩放到超出指定实例数。 你只需为网关服务的流量付费。 实例计数的范围介于 0 到 125 之间。 如果未指定，最大实例计数的默认值为 20。
- **手动** - 也可以选择“手动”模式，在这种情况下，网关不会自动缩放。 在此模式下，如果流量超过了应用程序网关或 WAF 可以处理的流量，可能会导致流量丢失。 使用手动模式时，必须指定实例计数。 实例计数可以在 1 到 125 个实例间变化。

## <a name="autoscaling-and-high-availability"></a>自动缩放和高可用性

Azure 应用程序网关始终以高度可用的方式部署。 服务由多个实例组成，这些实例是根据配置来创建的（如果禁用了自动缩放）或是为了应用程序加载而创建的（如果启用了自动缩放）。 请注意，从用户的角度来看，你不一定能了解单个实例的情况，只能了解整个应用程序网关服务的总体情况。 如果某个实例出现问题并停止工作，Azure 应用程序网关将以透明方式创建一个新实例。

请注意，即使将自动缩放配置为最小为零的实例数，该服务仍然是高可用的，并且始终按固定价格包含。

但是，创建新实例可能需要一些时间（大约 6 或 7 分钟）。 因此，如果你不想处理这个停机时间，可以将最小实例数配置为 2，理想情况下可使用可用性区域支持。 这样，在正常情况下，你的 Azure 应用网关中至少会有两个实例，因此如果其中一个有问题，另一个将在创建新实例期间尝试处理流量。 请注意，Azure 应用程序网关实例可以支持大约 10 个容量单位，因此根据你通常拥有的流量大小，你可能需要将最小实例自动缩放设置配置为大于 2 的值。

## <a name="feature-comparison-between-v1-sku-and-v2-sku"></a>v1 SKU 与 v2 SKU 之间的功能比较

下表比较了每个 SKU 提供的功能。

| 功能                                           | v1 SKU   | v2 SKU   |
| ------------------------------------------------- | -------- | -------- |
| 自动缩放                                       |          | &#x2713; |
| 区域冗余                                   |          | &#x2713; |
| 静态 VIP                                        |          | &#x2713; |
| Azure Kubernetes 服务 (AKS) 入口控制器 |          | &#x2713; |
| Azure 密钥保管库集成                       |          | &#x2713; |
| 重写 HTTP(S) 标头                           |          | &#x2713; |
| 基于 URL 的路由                                 | &#x2713; | &#x2713; |
| 多站点托管                             | &#x2713; | &#x2713; |
| 流量重定向                               | &#x2713; | &#x2713; |
| Web 应用程序防火墙 (WAF)                    | &#x2713; | &#x2713; |
| WAF 自定义规则                                  |          | &#x2713; |
| 传输层安全性 (TLS)/安全套接字层 (SSL) 终止            | &#x2713; | &#x2713; |
| 端到端 TLS 加密                         | &#x2713; | &#x2713; |
| 会话相关性                                  | &#x2713; | &#x2713; |
| 自定义错误页                                | &#x2713; | &#x2713; |
| WebSocket 支持                                 | &#x2713; | &#x2713; |
| HTTP/2 支持                                    | &#x2713; | &#x2713; |
| 连接清空                               | &#x2713; | &#x2713; |

> [!NOTE]
> 自动缩放 v2 SKU 现在支持使用[默认的运行状况探测](application-gateway-probe-overview.md#default-health-probe)自动监视后端池中所有资源的运行状况，并突出显示那些被视为不正常的后端成员。 对于不使用任何自定义探测配置的后端，系统会自动配置默认的运行状况探测。 若要了解详细信息，请参阅[应用程序网关中的 运行状况探测](application-gateway-probe-overview.md)。

## <a name="differences-from-v1-sku"></a>与 v1 SKU 的差异

此部分介绍 v2 SKU 与 v1 SKU 不同的功能和限制。

|差异|详细信息|
|--|--|
|身份验证证书|不支持。<br>有关详细信息，请参阅[应用程序网关的端到端 TLS 概述](ssl-overview.md#end-to-end-tls-with-the-v2-sku)。|
|在同一子网上混合使用 Standard_v2 和标准应用程序网关|不支持|
|应用程序网关子网上的用户定义路由 (UDR)|支持（特定方案）。 处于预览状态。<br> 有关支持的方案的详细信息，请参阅[应用程序网关配置概述](configuration-infrastructure.md#supported-user-defined-routes)。|
|入站端口范围的 NSG| 对于 Standard_v2 SKU，为 - 65200 到 65535<br>对于标准 SKU，为 - 65503 到 65534<br>有关详细信息，请参阅[常见问题解答](application-gateway-faq.md#are-network-security-groups-supported-on-the-application-gateway-subnet)。|
|Azure 诊断中的性能日志|不支持。<br>应当使用 Azure 指标。|
|计费|我们已安排在 2019 年 7 月 1 日开始计费。|
|FIPS 模式|目前不支持。|
|“仅 ILB”模式|目前不支持。 同时支持公共和 ILB 模式。|
|网络观察程序集成|不支持。|
|Azure 安全中心集成|尚不可用。

## <a name="migrate-from-v1-to-v2"></a>从 v1 迁移到 v2

PowerShell 库中提供了一个 Azure PowerShell 脚本，以帮助你从 v1 应用程序网关/WAF 迁移到 v2 自动缩放 SKU。 此脚本可帮助你从 v1 网关复制配置。 流量迁移仍由你负责。 有关详细信息，请参阅[将 Azure 应用程序网关从 v1 迁移到 v2](migrate-v1-v2.md)。

## <a name="next-steps"></a>后续步骤

- [快速入门：使用 Azure 应用程序网关定向 Web 流量 - Azure 门户](quick-create-portal.md)
- [使用 Azure PowerShell 创建具有预留虚拟 IP 地址的自动缩放区域冗余应用程序网关](tutorial-autoscale-ps.md)
- 了解有关[应用程序网关](overview.md)的详细信息。
- 了解有关 [Azure 防火墙](../firewall/overview.md)的详细信息。
