---
title: 在 SAP 高可用性方案中使用 Azure 标准负载均衡器虚拟机的公共终结点连接
description: 在 SAP 高可用性方案中使用 Azure 标准负载均衡器虚拟机的公共终结点连接
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
ms.date: 10/28/2019
ms.author: radeltch
ms.openlocfilehash: 15abee96f81bca68575d61be1276d4394e9a6f55
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/21/2020
ms.locfileid: "76293804"
---
# <a name="public-endpoint-connectivity-for-virtual-machines-using-azure-standard-load-balancer-in-sap-high-availability-scenarios"></a>在 SAP 高可用性方案中使用 Azure 标准负载均衡器虚拟机的公共终结点连接

本文的作用是介绍配置，这些配置将启用到公共终结点的出站连接。 这些配置主要在高可用性环境中，Pacemaker 用于 SUSE/RHEL。  

如果你在高可用性解决方案中将 Pacemaker 与 Azure 隔离代理配合使用，则 Vm 必须与 Azure 管理 API 建立出站连接。  
本文提供了一些选项，使你能够选择最适合你的方案的选项。  

## <a name="overview"></a>概述

通过群集实现 SAP 解决方案的高可用性时，必需的组件之一是[Azure 负载均衡器](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)。 Azure 提供两个负载均衡器 Sku：标准版和基本版。

标准 Azure 负载均衡器与基本负载均衡器相比具有一些优势。 例如，它跨 Azure 可用性区域工作，它具有更好的监视和日志记录功能，可更轻松地进行故障排除，减少延迟。 "HA 端口" 功能涵盖所有端口，也就是说，不再需要列出所有单个端口。  

Azure 负载均衡器的基本 SKU 和标准 SKU 之间存在一些重要的差异。 其中之一是处理到公共终结点的出站流量。 有关完整的基本和标准 SKU 负载均衡器比较，请参阅[负载均衡器 SKU 比较](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)。  
 
如果没有公共 IP 地址的 Vm 放在内部（无公共 IP 地址）的后端池（无公共 IP 地址）标准 Azure 负载均衡器中，则没有到公共终结点的出站连接，除非完成了其他配置。  

如果为 VM 分配了公共 IP 地址，或者 VM 位于具有公共 IP 地址的负载均衡器的后端池，则它将与公共终结点建立出站连接。  

SAP 系统通常包含敏感的业务数据。 对于托管 SAP 系统的 Vm 具有公共 IP 地址，很少被接受。 同时，还有一些方案需要从 VM 到公共终结点的出站连接。  

需要访问 Azure 公共终结点的方案示例包括：  
- 在 Pacemaker 群集中使用 Azure 隔离代理作为防护机制
- Azure 备份
- Azure 站点恢复  
- 使用公共存储库修补操作系统
- SAP 应用程序数据流可能需要到公共终结点的出站连接

如果 SAP 部署不需要与公共终结点建立出站连接，则无需执行其他配置。 为实现高可用性方案创建内部标准 SKU Azure 负载均衡器已经足够了，前提是也不需要来自公共终结点的入站连接。  

> [!Note]
> 如果没有公共 IP 地址的 Vm 放在内部（无公共 IP 地址）标准 Azure 负载均衡器的后端池中，则不会有出站 internet 连接，除非执行其他配置以允许路由到公共终结点。  
>如果 Vm 具有公共 IP 地址，或已在具有公共 IP 地址的 Azure 负载均衡器的后端池中，则 VM 将已具有到公共终结点的出站连接。


首先阅读以下文章：

* Azure 标准负载均衡器
  * [Azure 标准负载均衡器概述](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview)-Azure 标准负载均衡器、重要原则、概念和教程的全面概述 
  * [Azure 中的出站连接](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#scenarios)-如何实现 azure 中的出站连接的方案
  * [负载均衡器出站规则](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-rules-overview)-说明负载均衡器出站规则的概念以及如何创建出站规则
* Azure 防火墙
  * [Azure 防火墙概述](https://docs.microsoft.com/azure/firewall/overview)-azure 防火墙概述
  * [教程：部署和配置 Azure 防火墙](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)-有关如何配置 azure 防火墙的说明，请 Azure 门户
* [虚拟网络-用户定义的规则](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#user-defined)-Azure 路由概念和规则  
* [安全组服务标记](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags)-如何简化网络安全组和防火墙配置的服务标记

## <a name="additional-external-azure-standard-load-balancer-for-outbound-connections-to-internet"></a>Internet 的出站连接的其他外部 Azure 标准负载均衡器

若要实现到公共终结点的出站连接，而不允许从公共端点到 VM 的入站连接，一种选择是创建具有公共 IP 地址的第二个负载均衡器，将 Vm 添加到第二个负载均衡器的后端池，并仅定义[出站规则](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-rules-overview)。  
使用[网络安全组](https://docs.microsoft.com/azure/virtual-network/security-overview)控制可从 VM 出站调用访问的公共终结点。  
有关详细信息，请参阅文档[出站连接](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#scenarios)中的方案2。  
此配置如下所示：  

![控制与具有网络安全组的公共终结点的连接](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-public.png)

### <a name="important-considerations"></a>重要注意事项

- 对于同一子网中的多个 Vm，可以使用一个额外的公共负载均衡器来实现到公共终结点的出站连接，并优化成本  
- 使用[网络安全组](https://docs.microsoft.com/azure/virtual-network/security-overview)控制哪些公共终结点可从 vm 中访问。 可以将网络安全组分配给子网，也可以分配给每个虚拟机。 如果可能，请使用[服务标记](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags)降低安全规则的复杂性。  
- 使用公共 IP 地址和出站规则的 Azure 标准负载均衡器可直接访问公共终结点。 如果你有公司安全要求，通过集中式公司解决方案实现审核和日志记录，则你可能无法在此方案中满足此要求。  

>[!TIP]
>如果可能，请使用[服务标记](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags)降低网络安全组的复杂性。 

### <a name="deployment-steps"></a>部署步骤

1. 创建负载均衡器  
   1. 在[Azure 门户](https://portal.azure.com)中，单击 "所有资源"、"添加"，然后搜索 "**负载均衡器**"  
   1. 单击“创建” 
   1. 负载均衡器名称**MyPublicILB**  
   1. 选择 "**公共**" 作为类型，"**标准**" 作为 SKU  
   1. 选择 "**创建公共 IP 地址**" 并指定为名称**MyPublicILBFrondEndIP**  
   1. 选择**区域冗余**作为可用性区域  
   1. 单击 "查看和创建"，然后单击 "创建"  
2. 创建后端池**MyBackendPoolOfPublicILB**并添加 vm。  
   1. 选择虚拟网络  
   1. 选择 Vm 及其 IP 地址，并将其添加到后端池  
3. [创建出站规则](https://docs.microsoft.com/azure/load-balancer/configure-load-balancer-outbound-cli#create-outbound-rule)。 目前不能从 Azure 门户创建出站规则。 您可以[Azure CLI](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest)创建出站规则。  

   ```
    az network lb outbound-rule create --address-pool MyBackendPoolOfPublicILB --frontend-ip-configs MyPublicILBFrondEndIP --idle-timeout 30 --lb-name MyPublicILB --name MyOutBoundRules  --outbound-ports 10000 --enable-tcp-reset true --protocol All --resource-group MyResourceGroup
   ```

4. 创建网络安全组规则，以限制对特定公用终结点的访问。 如果存在现有的网络安全组，则可以对其进行调整。 下面的示例演示如何只允许访问 Azure 管理 API： 
   1. 导航到网络安全组
   1. 单击 "出站安全规则"
   1. 添加规则以**拒绝**对**Internet**的所有出站访问。
   1. 添加一个规则以**允许**访问**AzureCloud**，优先级低于规则的优先级，拒绝所有 internet 访问。


   出站安全规则如下所示： 

   ![具有公共 IP 的第二个负载均衡器的出站连接](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-network-security-groups.png)

   有关 Azure 网络安全组的详细信息，请参阅[安全组](https://docs.microsoft.com/azure/virtual-network/security-overview)。 

## <a name="azure-firewall-for-outbound-connections-to-internet"></a>用于与 internet 建立出站连接的 Azure 防火墙

若要实现到公共终结点的出站连接，而不允许从公共终结点到 VM 的入站连接，另一个选项是通过 Azure 防火墙。 Azure 防火墙是一种托管服务，具有内置的高可用性，可跨多个可用性区域。  
还需要部署[用户定义的路由](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#custom-routes)，该路由与部署 Vm 和 azure 负载均衡器的子网相关联，指向 azure 防火墙，通过 azure 防火墙路由流量。  
有关如何部署 Azure 防火墙的详细信息，请参阅[部署和配置 Azure 防火墙](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)。  

体系结构如下所示：

![与 Azure 防火墙的出站连接](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-firewall.png)

### <a name="important-considerations"></a>重要注意事项

- Azure 防火墙是云本机服务，具有内置的高可用性，它支持区域部署。
- 需要其他必须命名为 AzureFirewallSubnet 的子网。 
- 如果将较大的数据集传输到 SAP Vm 所在的虚拟网络的出站、其他虚拟网络中的 VM 或公用终结点，则它可能不是经济高效的解决方案。 其中一个示例是跨虚拟网络复制较大的备份。 有关详细信息，请参阅 Azure 防火墙定价。  
- 如果企业防火墙解决方案不是 Azure 防火墙，并且您有安全要求，使所有出站流量通过集中式公司解决方案，则此解决方案可能不可行。  

>[!TIP]
>如果可能，请使用[服务标记](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags)降低 Azure 防火墙规则的复杂性。  

### <a name="deployment-steps"></a>部署步骤

1. 部署步骤假设已为 Vm 定义虚拟网络和子网。  
2. 在部署 VM 和标准负载均衡器的同一虚拟网络中创建子网**AzureFirewallSubnet** 。  
   1. 在 Azure 门户中，导航到虚拟网络：单击 "所有资源"，搜索虚拟网络，单击虚拟网络，然后选择 "子网"。  
   1. 单击 "添加子网"。 输入**AzureFirewallSubnet**作为名称。 输入相应的地址范围。 保存。  
3. 创建 Azure 防火墙。  
   1. 在 Azure 门户选择 "所有资源"，单击 "添加"、"防火墙"、"创建"。 选择 "资源组" （选择虚拟网络所在的同一资源组）。  
   1. 输入 Azure 防火墙资源的名称。 例如， **MyAzureFirewall**。  
   1. 选择 "区域"，并至少选择两个可用性区域，并将其与部署 Vm 的可用性区域对齐。  
   1. 选择虚拟网络，其中部署了 SAP Vm 和 Azure 标准负载均衡器。  
   1. "公共 IP 地址"：单击 "创建"，然后输入名称。 例如**MyFirewallPublicIP**。  
4. 创建 Azure 防火墙规则以允许到指定的公共终结点的出站连接。 此示例演示如何允许访问 Azure 管理 API 公共终结点。  
   1. 依次选择 "规则"、"网络规则集合" 和 "添加网络规则集合"。  
   1. 名称： **MyOutboundRule**，输入 "优先级"，然后选择 "操作**允许**"。  
   1. 服务：名称**ToAzureAPI**。  协议：选择**任何**。 源地址：输入子网的范围，其中部署了 Vm 和标准负载均衡器实例： **11.97.0.0/24**。 目标端口：输入<b>*</b>。  
   1. 保存
   1. 如果仍位于 Azure 防火墙上，请选择 "概述"。 记下 Azure 防火墙的专用 IP 地址。  
5. 创建到 Azure 防火墙的路由  
   1. 在 Azure 门户选择 "所有资源"，然后单击 "添加"、"路由表"、"创建"。  
   1. 输入名称 Myroutetable-public，选择 "订阅"、"资源组" 和 "位置" （与虚拟网络和防火墙的位置匹配）。  
   1. 保存  

   防火墙规则如下所示： ![与 Azure 防火墙建立出站连接](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-firewall-rule.png)

6. 创建从 Vm 子网到**MyAzureFirewall**专用 IP 的用户定义路由。
   1. 在路由表中，单击 "路由"。 选择“添加”。 
   1. 路由名称： ToMyAzureFirewall，地址前缀： **0.0.0.0/0**。 下一跃点类型：选择 "虚拟设备"。 下一个跃点地址：输入配置的防火墙的专用 IP 地址： **11.97.1.4**。  
   1. 保存

## <a name="using-proxy-for-pacemaker-calls-to-azure-management-api"></a>使用代理进行 Pacemaker 调用 Azure 管理 API

你可以使用代理允许对 Azure 管理 API 公共终结点进行 Pacemaker 调用。  

### <a name="important-considerations"></a>重要注意事项

  - 如果已经有了公司代理，则可以通过它将出站调用路由到公共终结点。 对公共终结点的出站调用将经历公司控制点。  
  - 请确保代理配置允许与 Azure 管理 API 建立出站连接： https://management.azure.com  
  - 请确保有从 Vm 到代理的路由  
  - 代理将仅处理 HTTP/HTTPS 调用。 如果需要对不同协议（如 RFC）的公共终结点进行出站调用，则需要替代解决方案  
  - 代理解决方案必须高度可用，以避免 Pacemaker 群集的不稳定  
  - 根据代理的位置，可能会在从 Azure 隔离代理到 Azure 管理 API 的调用中引入额外的延迟。 如果你的公司代理仍在本地，而你的 Pacemaker 群集在 Azure 中，则需要衡量延迟并考虑，如果此解决方案适合你  
  - 如果尚未部署高可用的企业代理，我们不建议使用此选项，因为客户会产生额外的成本和复杂性。 尽管如此，如果你决定部署附加的代理解决方案，以允许从 Pacemaker 到 Azure 管理公共 API 的出站连接，请确保该代理高度可用，并且 Vm 到代理的延迟较低。  

### <a name="pacemaker-configuration-with-proxy"></a>Pacemaker 配置与代理 

行业中提供了许多不同的代理选项。 代理部署的分步说明不在本文档的讨论范围内。 在下面的示例中，我们假定代理正在响应**MyProxyService** ，并正在侦听端口**MyProxyPort**。  
若要允许 pacemaker 与 Azure 管理 API 通信，请在所有群集节点上执行以下步骤：  

1. 编辑 pacemaker 配置文件/etc/sysconfig/pacemaker 并添加以下行（所有群集节点）：  
   ```
   sudo vi /etc/sysconfig/pacemaker
   # Add the following lines
   http_proxy=http://MyProxyService:MyProxyPort
   https_proxy=http://MyProxyService:MyProxyPort
   ```

2. 在**所有**群集节点上重新启动 pacemaker 服务。  
  - SUSE  
     ```
     # Place the cluster in maintenance mode
     sudo pcs property set maintenance-mode=true
     #Restart on all nodes
     sudo systemctl restart pacemaker
     # Take the cluster out of maintenance mode
     sudo pcs property set maintenance-mode=false
     ```

  - Red Hat  
     ```
     # Place the cluster in maintenance mode
     sudo pcs property set maintenance-mode=true
     #Restart on all nodes
     sudo systemctl restart pacemaker
     # Take the cluster out of maintenance mode
     sudo pcs property set maintenance-mode=false
     ```

## <a name="next-steps"></a>后续步骤

* [了解如何在 Azure 中的 SUSE 上配置 Pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)
* [了解如何在 Azure 中的 Red Hat 上配置 Pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker)
