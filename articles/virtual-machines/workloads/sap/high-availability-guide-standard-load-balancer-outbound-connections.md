---
title: SAP HA 方案中为 Azure VM&标准 ILB 的公共终结点连接
description: 在 SAP 高可用性方案中使用 Azure 标准负载均衡器的虚拟机的公共终结点连接
services: virtual-machines-windows,virtual-network,storage,
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 02/07/2020
ms.author: radeltch
ms.openlocfilehash: 4fd01764c183098a8bd78d502eea7ab173fa22cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80293917"
---
# <a name="public-endpoint-connectivity-for-virtual-machines-using-azure-standard-load-balancer-in-sap-high-availability-scenarios"></a>在 SAP 高可用性方案中使用 Azure 标准负载均衡器的虚拟机的公共终结点连接

本文的范围是描述配置，这些配置将启用到公共终结点的出站连接。 配置主要在高可用性的情况下与起搏器为SUSE / RHEL。  

如果在高可用性解决方案中使用起搏器与 Azure 围栏代理，则 VM 必须具有与 Azure 管理 API 的出站连接。  
本文提供了几个选项，使您能够选择最适合您的方案的选项。  

## <a name="overview"></a>概述

通过群集实现 SAP 解决方案的高可用性时，必要的组件之一是[Azure 负载均衡器](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)。 Azure 提供两个负载均衡器 SKU：标准和基本负载。

标准 Azure 负载均衡器具有基本负载均衡器的一些优势。 例如，它跨 Azure 可用性区域工作，它具有更好的监视和日志记录功能，以便更轻松地进行故障排除，减少延迟。 "HA 端口"功能涵盖所有端口，即不再需要列出所有单个端口。  

Azure 负载均衡器的基本 SKU 和标准 SKU 之间存在一些重要区别。 其中之一是处理到公共终点的出站流量。 有关完全基本与标准 SKU 负载均衡器的比较，请参阅[负载均衡器 SKU 比较](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)。  
 
当没有公共 IP 地址的 VM 放置在内部（无公共 IP 地址）标准 Azure 负载均衡器的后端池中时，除非执行其他配置，否则不会与公共终结点进行出站连接。  

如果为 VM 分配了公共 IP 地址，或者 VM 位于具有公共 IP 地址的负载均衡器的后端池中，则 VM 将具有与公共终结点的出站连接。  

SAP 系统通常包含敏感的业务数据。 托管 SAP 系统的 VM 很少接受具有公共 IP 地址。 同时，在某些情况下，需要从 VM 到公共终结点的出站连接。  

需要访问 Azure 公共终结点的方案示例包括：  
- 在起搏器群集中使用 Azure 围栏代理作为隔离机制
- Azure 备份
- Azure Site Recovery  
- 使用公共存储库修补操作系统
- SAP 应用程序数据流可能需要与公共端点进行出站连接

如果您的 SAP 部署不需要与公共终结点的出站连接，则不需要实现其他配置。 假定也不需要从公共终结点进行入站连接，因此为高可用性方案创建内部标准 SKU Azure 负载均衡器就足够了。  

> [!Note]
> 当没有公共 IP 地址的 VM 放置在内部（无公共 IP 地址）标准 Azure 负载均衡器的后端池中时，将没有出站互联网连接，除非执行其他配置以允许路由到公共终结点。  
>如果 VM 具有公共 IP 地址，或者已在具有公共 IP 地址的 Azure 负载均衡器的后端池中，则 VM 已具有与公共终结点的出站连接。


请先阅读以下论文：

* Azure 标准负载均衡器
  * [Azure 标准负载均衡器概述](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview)- Azure 标准负载均衡器的全面概述、重要原则、概念和教程 
  * [Azure 中的出站连接](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#scenarios)- 有关如何在 Azure 中实现出站连接的方案
  * [负载均衡器出站规则](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-rules-overview)- 解释负载均衡器出站规则的概念以及如何创建出站规则
* Azure 防火墙
  * [Azure 防火墙概述](https://docs.microsoft.com/azure/firewall/overview)- Azure 防火墙概述
  * [教程：部署和配置 Azure 防火墙](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)- 有关如何通过 Azure 门户配置 Azure 防火墙的说明
* [虚拟网络 - 用户定义的规则](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#user-defined)- Azure 路由概念和规则  
* [安全组服务标记](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags)- 如何使用服务标记简化网络安全组和防火墙配置

## <a name="additional-external-azure-standard-load-balancer-for-outbound-connections-to-internet"></a>用于向外站连接到 Internet 的其他外部 Azure 标准负载均衡器

实现与公共端点的出站连接（不允许从公共端点与 VM 的入站连接）的一个选项是创建具有公共 IP 地址的第二个负载均衡器，将 VM 添加到第二个负载均衡器的后端池，并仅定义[出站规则](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-rules-overview)。  
使用[网络安全组](https://docs.microsoft.com/azure/virtual-network/security-overview)控制公共终结点，这些终结点可从 VM 的出站呼叫访问。  
有关详细信息，请参阅文档[出站连接](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#scenarios)中的方案 2。  
配置如下所示：  

![使用网络安全组控制与公共端点的连接](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-public.png)

### <a name="important-considerations"></a>重要注意事项

- 您可以为同一子网中的多个 VM 使用一个额外的公共负载均衡器来实现与公共端点的出站连接并优化成本  
- 使用[网络安全组](https://docs.microsoft.com/azure/virtual-network/security-overview)控制可从 VM 访问哪些公共终结点。 您可以将网络安全组分配给子网或每个 VM。 在可能的情况下，使用[服务标记](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags)来降低安全规则的复杂性。  
- 具有公共 IP 地址和出站规则的 Azure 标准负载均衡器允许直接访问公共终结点。 如果您有通过集中式公司解决方案通过所有出站流量进行审核和日志记录的公司安全要求，则可能无法通过此方案满足该要求。  

>[!TIP]
>在可能的情况下，使用[服务标记](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags)来降低网络安全组的复杂性。 

### <a name="deployment-steps"></a>部署步骤

1. 创建负载均衡器  
   1. 在[Azure 门户](https://portal.azure.com)中，单击"所有资源"，添加，然后搜索**负载均衡器**  
   1. 单击 **"创建"** 
   1. 负载均衡器名称**MyPublicILB**  
   1. 选择 **"公共**"作为类型，**标准**为 SKU  
   1. 选择 **"创建公共 IP 地址**"，并将名称指定为 **"MyPublicILBFroendIP"**  
   1. 选择 **"区域冗余**"作为可用性区域  
   1. 单击"审阅并创建"，然后单击"创建"  
2. 创建后端池**MyBackend 公共ILB池**并添加 VM。  
   1. 选择虚拟网络  
   1. 选择 VM 及其 IP 地址并将其添加到后端池  
3. [创建出站规则](https://docs.microsoft.com/azure/load-balancer/configure-load-balancer-outbound-cli#create-outbound-rule)。 目前无法从 Azure 门户创建出站规则。 您可以使用[Azure CLI](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest)创建出站规则。  

   ```azurecli
    az network lb outbound-rule create --address-pool MyBackendPoolOfPublicILB --frontend-ip-configs MyPublicILBFrondEndIP --idle-timeout 30 --lb-name MyPublicILB --name MyOutBoundRules  --outbound-ports 10000 --enable-tcp-reset true --protocol All --resource-group MyResourceGroup
   ```

4. 创建网络安全组规则以限制对特定公共终结点的访问。 如果存在现有的网络安全组，则可以调整它。 下面的示例演示如何启用对 Azure 管理 API 的访问： 
   1. 导航到网络安全组
   1. 单击出站安全规则
   1. 添加规则以**拒绝**所有出站访问**互联网**。
   1. 添加**允许**访问**AzureCloud**的规则，优先级低于拒绝所有 Internet 访问的优先级。


   出站安全规则如下所示： 

   ![与具有公共 IP 的第二负载均衡器的出站连接](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-network-security-groups.png)

   有关 Azure 网络安全组的详细信息，请参阅[安全组](https://docs.microsoft.com/azure/virtual-network/security-overview)。 

## <a name="azure-firewall-for-outbound-connections-to-internet"></a>用于向外站连接到 Internet 的 Azure 防火墙

实现与公共终结点的出站连接（不允许从公共终结点与 VM 的入站连接）的另一个选项是使用 Azure 防火墙。 Azure 防火墙是一个托管服务，具有内置的高可用性，它可以跨越多个可用性区域。  
您还需要部署[用户定义的路由](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#custom-routes)（与部署 VM 和 Azure 负载均衡器的子网关联的子网，指向 Azure 防火墙），以便通过 Azure 防火墙路由流量。  
有关如何部署 Azure 防火墙的详细信息，请参阅[部署和配置 Azure 防火墙](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)。  

体系结构如下所示：

![与 Azure 防火墙的出站连接](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-firewall.png)

### <a name="important-considerations"></a>重要注意事项

- Azure 防火墙是云原生服务，具有内置的高可用性，并且支持地区部署。
- 需要必须命名为 Azure 防火墙子网的其他子网。 
- 如果将 SAP VM 所在的虚拟网络的大型数据集出站转移到另一个虚拟网络中的 VM 或公共端点，则可能不具有成本效益的解决方案。 例如，跨虚拟网络复制大型备份。 有关详细信息，请参阅 Azure 防火墙定价。  
- 如果公司防火墙解决方案不是 Azure 防火墙，并且具有通过集中式公司解决方案通过所有出站流量的安全要求，则此解决方案可能不实用。  

>[!TIP]
>在可能的情况下，使用[服务标记](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags)来降低 Azure 防火墙规则的复杂性。  

### <a name="deployment-steps"></a>部署步骤

1. 部署步骤假定您已经为 VM 定义了虚拟网络和子网。  
2. 在同一虚拟网络中创建子**网 Azure 防火墙子网**，其中部署了 VMS 和标准负载均衡器。  
   1. 在 Azure 门户中，导航到虚拟网络：单击所有资源，搜索虚拟网络，单击虚拟网络，选择子网。  
   1. 单击"添加子网"。 输入**Azure 防火墙子网**作为名称。 输入适当的地址范围。 保存。  
3. 创建 Azure 防火墙。  
   1. 在 Azure 门户中选择"所有资源"，单击"添加、防火墙、创建"。 选择资源组（选择虚拟网络所在的同一资源组）。  
   1. 输入 Azure 防火墙资源的名称。 例如 **，MyAzure 防火墙**。  
   1. 选择"区域"并选择至少两个可用区域，与部署 VM 的可用性区域保持一致。  
   1. 选择虚拟网络，部署 SAP VM 和 Azure 标准负载均衡器。  
   1. 公共 IP 地址：单击"创建并输入名称"。 例如 **，我的防火墙公共IP**。  
4. 创建 Azure 防火墙规则以允许出站连接到指定的公共终结点。 该示例演示如何允许访问 Azure 管理 API 公共终结点。  
   1. 选择规则，网络规则集合，然后单击"添加网络规则集合"。  
   1. 名称：**我的出站规则**，输入优先级，选择操作**允许**。  
   1. 服务：名称**到 AzureAPI**。  协议：选择**任意**。 源地址：输入子网的范围，其中例如，VM 和标准负载均衡器的部署范围为 **：11.97.0.0/24**。 目标端口：输入<b>*</b>。  
   1. 保存
   1. 当您仍位于 Azure 防火墙上时，请选择"概述"。 记下 Azure 防火墙的专用 IP 地址。  
5. 创建 Azure 防火墙路由  
   1. 在 Azure 门户中选择"所有资源"，然后单击"添加、路由表、创建"。  
   1. 输入名称 MyRouteTable，选择订阅、资源组和位置（匹配虚拟网络和防火墙的位置）。  
   1. 保存  

   防火墙规则如下所示：![与 Azure 防火墙的出站连接](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-firewall-rule.png)

6. 从 VM 的子网创建用户定义的路由到**MyAzure 防火墙**的专用 IP。
   1. 当您定位在路线表上时，单击"路线"。 选择“添加”。 
   1. 路由名称：到 MyAzure 防火墙，地址前缀： **0.0.0.0/0**。 下一个跃点类型：选择虚拟设备。 下一个跃点地址：输入您配置的防火墙的专用 IP 地址 **：11.97.1.4**。  
   1. 保存

## <a name="using-proxy-for-pacemaker-calls-to-azure-management-api"></a>使用代表对起搏器调用 Azure 管理 API

您可以使用代理来允许起搏器调用 Azure 管理 API 公共终结点。  

### <a name="important-considerations"></a>重要注意事项

  - 如果已有公司代理，则可以通过该代理将出站呼叫路由到公共终结点。 对公共终结点的出站呼叫将经过公司控制点。  
  - 确保代理配置允许向外站连接到 Azure 管理 API：`https://management.azure.com`  
  - 确保从 VM 到代理的路由  
  - 代理将仅处理 HTTP/HTTPS 调用。 如果需要通过不同的协议（如 RFC）对公共端点进行出站调用，则需要替代解决方案  
  - 代理解决方案必须高度可用，以避免起搏器群集中的不稳定性  
  - 根据代理的位置，它可能会在从 Azure 围栏代理到 Azure 管理 API 的调用中引入额外的延迟。 如果公司代理仍在内部，而 Pacemaker 群集位于 Azure 中，请测量延迟并考虑，如果此解决方案适合您  
  - 如果还没有高度可用的公司代理，我们不推荐此选项，因为客户将产生额外的成本和复杂性。 但是，如果您决定部署其他代理解决方案，以便允许从 Pacemaker 到 Azure 管理公共 API 的出站连接，请确保代理高度可用，并且从 VM 到代理的延迟较低。  

### <a name="pacemaker-configuration-with-proxy"></a>使用代理的起搏器配置 

行业中有多种不同的代理选项。 代理部署的分步说明不在本文档的范围之内。 在下面的示例中，我们假定您的代理正在响应**MyProxyService 服务**并侦听 Port **MyProxyPort**。  
要允许心脏起搏器与 Azure 管理 API 通信，请对所有群集节点执行以下步骤：  

1. 编辑起搏器配置文件 /etc/系统配置/起搏器并添加以下行（所有群集节点）：

   ```console
   sudo vi /etc/sysconfig/pacemaker
   # Add the following lines
   http_proxy=http://MyProxyService:MyProxyPort
   https_proxy=http://MyProxyService:MyProxyPort
   ```

2. 在所有**群集节点**上重新启动心脏起搏器服务。  
  - SUSE
 
     ```console
     # Place the cluster in maintenance mode
     sudo crm configure property maintenance-mode=true
     #Restart on all nodes
     sudo systemctl restart pacemaker
     # Take the cluster out of maintenance mode
     sudo crm configure property maintenance-mode=true
     ```

  - Red Hat  

     ```console
     # Place the cluster in maintenance mode
     sudo pcs property set maintenance-mode=true
     #Restart on all nodes
     sudo systemctl restart pacemaker
     # Take the cluster out of maintenance mode
     sudo pcs property set maintenance-mode=false
     ```

## <a name="next-steps"></a>后续步骤

* [了解如何在 Azure 中的 SUSE 上配置起搏器](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)
* [了解如何在 Azure 中的红帽上配置起搏器](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker)
