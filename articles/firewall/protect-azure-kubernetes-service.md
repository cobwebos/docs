---
title: 使用 Azure 防火墙保护 Azure Kubernetes 服务（AKS）部署
description: 了解如何使用 Azure 防火墙保护 Azure Kubernetes 服务（AKS）部署
author: vhorne
ms.service: firewall
services: firewall
ms.topic: how-to
ms.date: 07/02/2020
ms.author: victorh
ms.openlocfilehash: 81d65954197c0ebe0de77dc2fea63239d4c3f17b
ms.sourcegitcommit: bcb962e74ee5302d0b9242b1ee006f769a94cfb8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2020
ms.locfileid: "86056661"
---
# <a name="use-azure-firewall-to-protect-azure-kubernetes-service-aks-deployments"></a>使用 Azure 防火墙保护 Azure Kubernetes 服务（AKS）部署

Azure Kubernetes Service （AKS）在 Azure 上提供托管的 Kubernetes 群集。 它通过将很多责任分担到 Azure，降低了管理 Kubernetes 的复杂性和操作开销。 AKS 可以处理关键任务，如运行状况监视和维护，并通过集中管理提供企业级安全群集。

Kubernetes 根据虚拟机的可用计算资源和每个容器的资源要求，协调虚拟机群集并计划容器在这些虚拟机上运行。 容器组合到 pod 中，即 Kubernetes 的基本操作单元，而这些箱将扩展到所需的状态。

出于管理和操作目的，AKS 群集中的节点需要访问某些端口和完全限定的域名 (FQDN)。 这些操作可以是与 API 服务器通信，或者下载并安装核心 Kubernetes 群集组件和节点安全更新。 Azure 防火墙可以帮助你锁定环境并筛选出站流量。

遵循本文中的指导原则，使用 Azure 防火墙为你的 Azure Kubernetes 群集提供额外的保护。

## <a name="prerequisites"></a>先决条件

- 已部署的 Azure Kubernetes 群集，其中包含运行的应用程序。

   有关详细信息，请参阅[教程：部署 Azure Kubernetes 服务（AKS）群集](../aks/tutorial-kubernetes-deploy-cluster.md)和[教程：在 azure KUBERNETES 服务（AKS）中运行应用程序](../aks/tutorial-kubernetes-deploy-application.md)。


## <a name="securing-aks"></a>保护 AKS

Azure 防火墙提供 AKS FQDN 标记以简化配置。 使用以下步骤可允许出站 AKS 平台流量：

- 使用 Azure 防火墙限制出站流量并创建用户定义的路由（UDR）以指示所有出站流量时，请确保在防火墙中创建适当的 DNAT 规则，以正确允许入站流量。 

   使用带有 UDR 的 Azure 防火墙会中断入站安装，因为存在非对称路由。 如果 AKS 子网具有指向防火墙专用 IP 地址的默认路由，但使用的是公共负载均衡器，则会出现此问题。 例如，类型为*LoadBalancer*的入站或 Kubernetes 服务。

   在这种情况下，将通过负载均衡器的公共 IP 地址接收传入的负载均衡器流量，但返回路径将通过防火墙的专用 IP 地址。 由于防火墙是有状态的，并且无法识别已建立的会话，因此会丢弃返回的数据包。 若要了解如何将 Azure 防火墙与入口或服务负载均衡器集成，请参阅[将 Azure 防火墙与 Azure 标准负载均衡器集成](integrate-lb.md)。
- 创建应用程序规则集合并添加规则以启用*AzureKubernetesService* FQDN 标记。 源 IP 地址范围为主机池虚拟网络，协议为 https，目标为 AzureKubernetesService。
- AKS 群集需要以下出站端口/网络规则：

   - TCP 端口 443
   - TCP [*IPAddrOfYourAPIServer*]：如果你的应用需要与 API 服务器通信，则需要443。 此更改可以在创建群集后设置。
   - TCP 端口9000和 UDP 端口1194，用于隧道前端盒与 API 服务器上的隧道通信。

      若要更具体地了解，请参阅 **hcp. <location> 。* 下表中的 azmk8s.io 和地址。
   - 用于网络时间协议 (NTP) 时间同步 (Linux 节点) 的 UDP 端口 123。
   - 如果你有直接访问 API 服务器的 Pod，则还需要用于 DNS 的 UDP 端口 53。
- 配置 AzureMonitor 和存储服务标记。 Azure Monitor 接收 log analytics 数据。 

   你还可以分别允许工作区 URL： `<worksapceguid>.ods.opinsights.azure.com` 、和 `<worksapceguid>.oms.opinsights.azure.com` 。 可以通过以下方式之一来解决此操作：

    - 允许从主机池子网到、和的 https 访问 `*. ods.opinsights.azure.com` `*.oms. opinsights.azure.com` 。 这些通配符 Fqdn 启用了所需的访问权限，但限制更少。
    - 使用以下 log analytics 查询列出确切要求的 Fqdn，然后在防火墙应用程序规则中显式允许它们：
   ```
   AzureDiagnostics 
   | where Category == "AzureFirewallApplicationRule" 
   | search "Allow" 
   | search "*. ods.opinsights.azure.com" or "*.oms. opinsights.azure.com"
   | parse msg_s with Protocol " request from " SourceIP ":" SourcePort:int " to " FQDN ":" * 
   | project TimeGenerated,Protocol,FQDN 
   ```


## <a name="next-steps"></a>后续步骤

- 了解有关 Azure Kubernetes 服务的详细信息，请参阅[Azure Kubernetes 服务（AKS）的 Kubernetes 核心概念](../aks/concepts-clusters-workloads.md)。
