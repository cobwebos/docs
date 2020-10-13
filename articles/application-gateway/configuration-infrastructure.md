---
title: Azure 应用程序网关基础结构配置
description: 本文介绍如何配置 Azure 应用程序网关基础结构。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: surmb
ms.openlocfilehash: cd1dc953c35233010250bf7f959c94d1de50fe4a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91319786"
---
# <a name="application-gateway-infrastructure-configuration"></a>应用程序网关基础结构配置

应用程序网关基础结构包括虚拟网络、子网、网络安全组和用户定义路由。

## <a name="virtual-network-and-dedicated-subnet"></a>虚拟网络和专用子网

应用程序网关是虚拟网络中的专用部署。 需要在虚拟网络中为应用程序网关配置一个专用子网。 在子网中，可以创建给定应用程序网关部署的多个实例。 还可以在该子网中部署其他应用程序网关。 但不能在应用程序网关子网中部署其他任何资源。 不能在同一子网中混合使用 Standard_v2 和 Standard Azure 应用程序网关。

> [!NOTE]
> 应用程序网关子网中当前不支持[虚拟网络服务终结点策略](../virtual-network/virtual-network-service-endpoint-policies-overview.md)。

### <a name="size-of-the-subnet"></a>子网的大小

如果配置了专用前端 IP，则应用程序网关将使用每个实例的 1 个专用 IP 地址，以及另一个专用 IP 地址。

另外，Azure 会在每个子网中保留 5 个 IP 地址供内部使用：前 4 个 IP 地址和最后一个 IP 地址。 例如，假设有 15 个应用程序网关实例没有专用前端 IP。 至少需要为此子网提供 20 个 IP 地址：5 个 IP 地址供内部使用，15 个 IP 地址供应用程序网关实例使用。

假设某个子网包含 27 个应用程序网关实例，并且包含一个用作专用前端 IP 的 IP 地址。 在这种情况下，需要 33 个 IP 地址：27 个 IP 地址用于应用程序网关实例，1 个 IP 地址用于专用前端，5 个 IP 地址供内部使用。

应用程序网关 (Standard 或 WAF) SKU 最多支持32实例 (32 实例 IP 地址 + 1 个专用前端 IP + 5 Azure 保留) –因此建议使用最小子网大小/26

应用程序网关 (Standard_v2 或 WAF_v2 SKU) 最多可支持125实例 (125 实例 IP 地址 + 1 个专用前端 IP + 5 Azure 保留) –因此建议使用最小子网大小/24

## <a name="network-security-groups"></a>网络安全组

应用程序网关支持网络安全组 (NSG)。 但是，存在一些限制：

- 对于应用程序网关 v1 SKU，必须允许 TCP 端口 65503-65534 上的传入 Internet 流量，对于目标子网为 **Any** 且源为 **GatewayManager** 服务标记的 v2 SKU，必须允许 TCP 端口 65200-65535 上的传入 Internet 流量。 此端口范围是进行 Azure 基础结构通信所必需的。 这些端口受 Azure 证书的保护（处于锁定状态）。 外部实体（包括这些网关的客户）无法在这些终结点上通信。

- 不能阻止出站 Internet 连接。 NSG 中的默认出站规则允许 Internet 连接。 建议：

  - 不要删除默认出站规则。
  - 不要创建拒绝任何出站连接的其他出站规则。

- 必须允许目标子网为“任意”的 AzureLoadBalancer 标记的流量 。

### <a name="allow-access-to-a-few-source-ips"></a>允许访问少量源 IP

对于此方案，请在应用程序网关子网中使用 NSG。 按以下优先顺序对子网施加以下限制：

1. 允许来自源 IP 或 IP 范围的传入流量，其目标为整个应用程序网关子网地址范围，目标端口为入站访问端口，例如，使用端口 80 进行 HTTP 访问。
2. 允许特定的传入请求，这些请求来自采用 **GatewayManager** 服务标记的源，其目标为“任意”，目标端口为 65503-65534（适用于应用程序网关 v1 SKU）或 65200-65535（适用于 v2 SKU），可以进行[后端运行状况通信](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)。 此端口范围是进行 Azure 基础结构通信所必需的。 这些端口受 Azure 证书的保护（处于锁定状态）。 如果没有适当的证书，外部实体将无法对这些终结点做出任何更改。
3. 允许[网络安全组](https://docs.microsoft.com/azure/virtual-network/security-overview)中的传入 Azure 负载均衡器探测（*AzureLoadBalancer* 标记）和入站虚拟网络流量（*VirtualNetwork* 标记）。
4. 使用“全部拒绝”规则阻止其他所有传入流量。
5. 允许所有目的地的 Internet 出站流量。

## <a name="supported-user-defined-routes"></a>支持的用户定义路由 

> [!IMPORTANT]
> 在应用程序网关子网中使用 UDR 可能会导致[后端运行状况视图](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#back-end-health)中的运行状态显示为“未知”。 此外，可能还会导致应用程序网关日志和指标生成失败。 建议不要在应用程序网关子网中使用 UDR，以便能够查看后端运行状况、日志和指标。

- **v1**

   使用 v1 SKU 时，只要用户定义的路由 (UDR) 未更改端到端请求/响应通信，则应用程序网关子网就会支持这些 UDR。 例如，可以在应用程序网关子网中设置一个指向防火墙设备的、用于检查数据包的 UDR。 但是，必须确保数据包在检查后可以访问其预期目标。 否则，可能会导致不正确的运行状况探测或流量路由行为。 这包括已探测到的路由，或者通过 Azure ExpressRoute 或 VPN 网关在虚拟网络中传播的默认 0.0.0.0/0 路由。 V1 不支持需要在本地（强制隧道）重定向 0.0.0.0/0 的任何情况。

- **v2**

   v2 SKU 存在支持和不支持的方案：

   **v2 支持的方案**
   > [!WARNING]
   > 错误配置路由表可能会导致应用程序网关 v2 中出现非对称路由。 确保所有管理平面/控制平面流量直接发送到 Internet，且不通过虚拟设备发送。 日志和指标也可能会受影响。


  **场景 1**：使用 UDR 禁用向应用程序网关子网进行边界网关协议 (BGP) 路由传播

   有时，默认网关路由 (0.0.0.0/0) 会通过与应用程序网关虚拟网络关联的 ExpressRoute 或 VPN 网关进行播发。 这会中断管理平面流量，因此需要 Internet 的直接路径。 在这种情况下，可以使用 UDR 来禁用 BGP 路由传播。 

   若要禁用 BGP 路由传播，请使用以下步骤：

   1. 在 Azure 中创建一个“路由表”资源。
   2. 禁用“虚拟网络网关路由传播”参数。 
   3. 将路由表关联到相应的子网。 

   为此方案启用 UDR 不应会破坏任何现有设置。

  **场景 2**：使用 UDR 将 0.0.0.0/0 定向到 Internet

   可以创建一个 UDR，用于将 0.0.0.0/0 流量直接发送到 Internet。 

  **方案 3**：对 kubenet 中的 Azure Kubernetes 服务使用 UDR

  如果使用包含 Azure Kubernetes 服务 (AKS) 和应用程序网关入口控制器 (AGIC) 的 kubenet，则需要路由表，以允许将发送到 pod 的流量从应用程序网关路由到正确的节点。 如果使用 Azure CNI，则不需要这样做。 

  若要使用路由表以使 kubenet 能够正常工作，请执行以下步骤：

  1. 转到 AKS 创建的资源组（资源组名称应以“MC_”开头）
  2. 在该资源组中查找 AKS 创建的路由表。 路由表中应填充以下信息：
     - 地址前缀应是要在 AKS 中访问的 pod 的 IP 范围。 
     - 下一跃点类型应是“虚拟设备”。 
     - 下一跃点地址应是托管 pod 的节点的 IP 地址。
  3. 将此路由表关联到应用程序网关子网。 
    
  **v2 不支持的方案**

  **场景 1**：对虚拟设备使用 UDR

  V2 不支持需要通过任何虚拟设备、中心辐射型虚拟网络或者在本地（强制隧道）重定向 0.0.0.0/0 的任何方案。

## <a name="next-steps"></a>后续步骤

- [了解前端 IP 地址配置](configuration-front-end-ip.md)。