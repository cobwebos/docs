---
title: 使用 Azure 防火墙保护 Azure Kubernetes 服务 (AKS) 部署
description: 了解如何使用 Azure 防火墙保护 Azure Kubernetes 服务 (AKS) 部署
author: vhorne
ms.service: firewall
services: firewall
ms.topic: how-to
ms.date: 09/03/2020
ms.author: victorh
ms.openlocfilehash: 43755b312a64c429b38a07c8c4fad8c85b08342a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89437847"
---
# <a name="use-azure-firewall-to-protect-azure-kubernetes-service-aks-deployments"></a>使用 Azure 防火墙保护 Azure Kubernetes 服务 (AKS) 部署

Azure Kubernetes 服务 (AKS) 提供 Azure 上的托管 Kubernetes 群集。 它通过将大量管理工作量卸载到 Azure，来降低管理 Kubernetes 所产生的复杂性和操作开销。 AKS 可以处理关键任务（例如运行状况监视和维护），并提供受到辅助治理的企业级安全群集。

Kubernetes 根据虚拟机的可用计算资源和每个容器的资源要求，协调虚拟机群集并安排容器在这些虚拟机上运行。 容器将分组到 Pod（Kubernetes 的基本操作单位）中，这些 Pod 可以缩放到你所需的状态。

为了便于管理和操作，AKS 群集中的节点需要访问特定的端口和完全限定的域名 (FQDN)。 这些操作可以是与 API 服务器通信，或者下载并安装核心 Kubernetes 群集组件和节点安全更新。 Azure 防火墙可以帮助你锁定环境并筛选出站流量。

请参阅本文中的指南，使用 Azure 防火墙为 Azure Kubernetes 群集提供额外保护。

## <a name="prerequisites"></a>先决条件

- 已部署的 Azure Kubernetes 群集，其中包含正在运行的应用程序。

   有关详细信息，请参阅[教程：部署 Azure Kubernetes 服务 (AKS) 群集](../aks/tutorial-kubernetes-deploy-cluster.md)和[教程：在 Azure Kubernetes 服务 (AKS) 中运行应用程序](../aks/tutorial-kubernetes-deploy-application.md)。


## <a name="securing-aks"></a>保护 AKS

Azure 防火墙提供 AKS FQDN 标记以简化此配置。 使用以下步骤允许出站 AKS 平台流量：

- 使用 Azure 防火墙限制出站流量并创建用户定义的路由 (UDR) 来引导所有出站流量时，请确保在防火墙中创建适当的 DNAT 规则，以正确允许入站流量。 

   结合使用 Azure 防火墙和 UDR 时，会因不对称路由而破坏入站设置。 如果 AKS 子网具有指向防火墙专用 IP 地址的默认路由，而你使用公共负载均衡器，就会出现此问题。 例如类型为 LoadBalancer 的入站或 Kubernetes 服务。

   在这种情况下，将通过负载均衡器的公共 IP 地址接收传入的负载均衡器流量，但返回路径将通过防火墙的专用 IP 地址。 由于防火墙是有状态的，并且无法识别已建立的会话，因此会丢弃返回的数据包。 若要了解如何将 Azure 防火墙与入口或服务负载均衡器集成，请参阅[将 Azure 防火墙与 Azure 标准负载均衡器集成](integrate-lb.md)。
- 创建应用程序规则集合并添加启用 AzureKubernetesService FQDN 标记的规则。 源 IP 地址范围为主机池虚拟网络，协议为 https，目标为 AzureKubernetesService。
- 以下出站端口/网络规则对于 AKS 群集是必需的：

   - TCP 端口 443
   - 如果有应用需要与 API 服务器通信，则需要 TCP [IPAddrOfYourAPIServer]:443。 创建群集后，可以设置此更改。
   - TCP 端口 9000 和 UDP 端口 1194，使隧道前端 pod 与 API 服务器上的隧道后端进行通信。

      若要更具体地了解，请参阅 **hcp. <location> 。* 下表中的 azmk8s.io 和地址：

   | 目标终结点                                                             | 协议 | 端口    | 用途  |
   |----------------------------------------------------------------------------------|----------|---------|------|
   | **`*:1194`** <br/> *Or* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - `AzureCloud.<Region>:1194` <br/> *Or* <br/> [区域 CIDR](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - `RegionCIDRs:1194` <br/> *Or* <br/> **`APIServerIP:1194`** `(only known after cluster creation)`  | UDP           | 1194      | 用于节点与控制平面之间的隧道安全通信。 |
   | **`*:9000`** <br/> *Or* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - `AzureCloud.<Region>:9000` <br/> *Or* <br/> [区域 CIDR](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - `RegionCIDRs:9000` <br/> *Or* <br/> **`APIServerIP:9000`** `(only known after cluster creation)`  | TCP           | 9000      | 用于节点与控制平面之间的隧道安全通信。 |

   - UDP 端口 123，用于网络时间协议 (NTP) 时间同步（Linux 节点）。
   - 如果你有可直接访问 API 服务器的 pod，则还必须具有用于 DNS 的 UDP 端口 53。

   有关详细信息，请参阅[控制 Azure Kubernetes 服务 (AKS) 中群集节点的出口流量](../aks/limit-egress-traffic.md)。
- 配置 AzureMonitor 和存储服务标记。 Azure Monitor 接收日志分析数据。

   还可以单独允许工作区 URL：`<worksapceguid>.ods.opinsights.azure.com` 和 `<worksapceguid>.oms.opinsights.azure.com`。 可以通过以下方式之一来解决此问题：

    - 允许从主机池子网到 `*. ods.opinsights.azure.com` 和 `*.oms. opinsights.azure.com` 的 https 访问。 这些通配符 FQDN 会允许所需的访问，但限制更少。
    - 使用以下日志分析查询列出所需的确切 FQDN，然后在防火墙应用程序规则中显式允许这些 FQDN：
   ```
   AzureDiagnostics 
   | where Category == "AzureFirewallApplicationRule" 
   | search "Allow" 
   | search "*. ods.opinsights.azure.com" or "*.oms. opinsights.azure.com"
   | parse msg_s with Protocol " request from " SourceIP ":" SourcePort:int " to " FQDN ":" * 
   | project TimeGenerated,Protocol,FQDN 
   ```


## <a name="next-steps"></a>后续步骤

- 若要详细了解 Azure Kubernetes 服务，请参阅[适用于 Azure Kubernetes 服务 (AKS) 的 Kubernetes 核心概念](../aks/concepts-clusters-workloads.md)。
