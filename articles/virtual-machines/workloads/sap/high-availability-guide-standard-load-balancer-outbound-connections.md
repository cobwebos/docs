---
title: SAP HA 方案中的 Azure VM 公共终结点连接和标准 ILB
description: SAP 高可用性方案中使用 Azure 标准负载均衡器的虚拟机的公共终结点连接
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
ms.date: 06/16/2020
ms.author: radeltch
ms.openlocfilehash: a0dc9f673abcac549fffc7291b8ac376c297da6b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87836116"
---
# <a name="public-endpoint-connectivity-for-virtual-machines-using-azure-standard-load-balancer-in-sap-high-availability-scenarios"></a>SAP 高可用性方案中使用 Azure 标准负载均衡器的虚拟机的公共终结点连接

本文的范围是介绍配置，这些配置启用出站连接到一个或多个公共终结点。 这些配置主要位于使用适用于 SUSE/RHEL 的 Pacemaker 的高可用性环境中。  

如果在高可用性解决方案中结合使用 Pacemaker 与 Azure 围墙代理，VM 必须出站连接到 Azure 管理 API。  
本文介绍了多种方式，可便于你选择最适合自己方案的方式。  

## <a name="overview"></a>概述

通过群集实现 SAP 高可用性解决方案时，一个必要的组件是 [Azure 负载均衡器](../../../load-balancer/load-balancer-overview.md)。 Azure 提供了两种负载均衡器 SKU：标准 SKU 和基本 SKU。

与基本负载均衡器相比，标准 Azure 负载均衡器具有一些优势。 例如，它可以跨 Azure 可用性区域工作，它有更好的监视和日志记录功能，可便于更轻松地进行故障排除，从而减少了延迟。 “HA 端口”功能涵盖所有端口；也就是说，不再需要列出所有单独的端口。  

Azure 负载均衡器的基本 SKU 和标准 SKU 之间有一些重要的区别。 其中之一是处理流向公共终结点的出站流量。 有关基本与标准 SKU 负载均衡器的完整比较，请参阅[负载均衡器 SKU 比较](../../../load-balancer/load-balancer-overview.md)。  
 
如果没有公共 IP 地址的 VM 被放在内部（无公共 IP 地址）标准 Azure 负载均衡器的后端池中，就不会出站连接到公共终结点，除非完成了额外的配置。  

如果 VM 分配有公共 IP 地址，或者 VM 位于具有公共 IP 地址的负载均衡器的后端池中，它就会出站连接到公共终结点。  

SAP 系统通常包含敏感的业务数据。 托管 SAP 系统的 VM 具有公共 IP 地址是很难接受的。 同时，有一些方案需要从 VM 出站连接到公共终结点。  

需要访问 Azure 公共终结点的方案示例包括：  
- 在 Pacemaker 群集中使用 Azure 围墙代理作为围墙机制
- Azure 备份
- Azure Site Recovery  
- 使用公共存储库修补操作系统
- SAP 应用程序数据流可能需要出站连接到公共终结点

如果 SAP 部署不需要出站连接到公共终结点，则无需实现额外的配置。 假设也不需要来自公共终结点的入站连接，那么为高可用性方案创建内部标准 SKU Azure 负载均衡器就足够了。  

> [!Note]
> 如果没有公共 IP 地址的 VM 被放在内部（无公共 IP 地址）标准 Azure 负载均衡器的后端池中，就不会有出站 Internet 连接，除非执行额外的配置来允许路由到公共终结点。  
>如果 VM 具有公共 IP 地址，或者 VM 已位于具有公共 IP 地址的负载均衡器的后端池中，那么 VM 就已出站连接到公共终结点。


请先阅读以下文章：

* Azure 标准负载均衡器
  * [Azure 标准负载均衡器概述](../../../load-balancer/load-balancer-overview.md) - 全面概述了 Azure 标准负载均衡器、重要原则、概念和教程 
  * [Azure 中的出站连接](../../../load-balancer/load-balancer-outbound-connections.md#scenarios) - 关于如何在 Azure 中实现出站连接的方案
  * [负载均衡器出站规则](../../../load-balancer/load-balancer-outbound-connections.md#outboundrules) - 解释了负载均衡器出站规则的概念，以及如何创建出站规则
* Azure 防火墙
  * [Azure 防火墙概述](../../../firewall/overview.md) - 概述了 Azure 防火墙
  * [教程：部署和配置 Azure 防火墙](../../../firewall/tutorial-firewall-deploy-portal.md) - 介绍了如何通过 Azure 门户配置 Azure 防火墙
* [虚拟网络 - 用户定义规则](../../../virtual-network/virtual-networks-udr-overview.md#user-defined) - Azure 路由概念和规则  
* [安全组服务标记](../../../virtual-network/security-overview.md#service-tags) - 如何使用服务标记来简化网络安全组和防火墙配置

## <a name="additional-external-azure-standard-load-balancer-for-outbound-connections-to-internet"></a>用于出站连接到 Internet 的其他外部 Azure 标准负载均衡器

若要在不允许从公共终结点入站连接到 VM 的情况下实现出站连接到公共终结点，一种方式是创建具有公共 IP 地址的第二个负载均衡器，将 VM 添加到第二个负载均衡器的后端池，并且只定义[出站规则](../../../load-balancer/load-balancer-outbound-connections.md#outboundrules)。  
使用[网络安全组](../../../virtual-network/security-overview.md)来控制可供来自 VM 的出站调用进行访问的公共终结点。  
有关详细信息，请参阅[出站连接](../../../load-balancer/load-balancer-outbound-connections.md#scenarios)文档中的“方案 2”。  
配置如下所示：  

![使用网络安全组控制与公共终结点的连接](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-public.png)

### <a name="important-considerations"></a>重要注意事项

- 可以为同一子网中的多个 VM 使用一个额外的公共负载均衡器，以实现出站连接到公共终结点并优化成本  
- 使用[网络安全组](../../../virtual-network/security-overview.md)来控制可以从 VM 访问哪些公共终结点。 可以将网络安全组分配给子网，也可以分配给每个 VM。 尽可能使用[服务标记](../../../virtual-network/security-overview.md#service-tags)，以降低安全规则的复杂度。  
- 使用具有公共 IP 地址和出站规则的 Azure 标准负载均衡器，可以直接访问公共终结点。 如果你有企业安全要求，即所有出站流量都必须通过集中式企业解决方案以供进行审核和日志记录，那么此方案可能无法满足要求。  

>[!TIP]
>尽可能使用[服务标记](../../../virtual-network/security-overview.md#service-tags)，以降低网络安全组的复杂度。 

### <a name="deployment-steps"></a>部署步骤

1. 创建负载均衡器  
   1. 在 [Azure 门户](https://portal.azure.com)中，依次单击“所有资源”和“添加”，然后搜索“负载均衡器”  
   1. 单击“创建”  
   1. 负载均衡器名称 MyPublicILB  
   1. 选择“公共”作为类型，并选择“标准”作为 SKU  
   1. 选择“创建公共 IP 地址”，并将名称指定为“MyPublicILBFrondEndIP”  
   1. 选择“区域冗余”作为可用性区域  
   1. 依次单击“审阅 + 创建”和“创建”  
2. 创建后端池 MyBackendPoolOfPublicILB，然后添加 VM。  
   1. 选择虚拟网络  
   1. 选择 VM 及其 IP 地址，然后将它们添加到后端池  
3. [创建出站规则](../../../load-balancer/quickstart-load-balancer-standard-public-cli.md?tabs=option-1-create-load-balancer-standard%3ftabs%3doption-1-create-load-balancer-standard#create-outbound-rule-configuration)。 暂无法在 Azure 门户中创建出站规则。 可以使用 [Azure CLI](../../../cloud-shell/overview.md?view=azure-cli-latest) 创建出站规则。  

   ```azurecli
    az network lb outbound-rule create --address-pool MyBackendPoolOfPublicILB --frontend-ip-configs MyPublicILBFrondEndIP --idle-timeout 30 --lb-name MyPublicILB --name MyOutBoundRules  --outbound-ports 10000 --enable-tcp-reset true --protocol All --resource-group MyResourceGroup
   ```

4. 创建网络安全组规则，以限制对特定公共终结点的访问。 如果现有网络安全组，可以对它进行调整。 下面的示例展示了如何启用对 Azure 管理 API 的访问： 
   1. 转到“网络安全组”
   1. 单击“出站安全规则”
   1. 添加规则来拒绝所有对 Internet 的出站访问。
   1. 添加规则来允许对 AzureCloud 的访问，此规则的优先级低于拒绝所有对 Internet 的访问的规则。


   出站安全规则如下所示： 

   ![使用具有公共 IP 的第二个负载均衡器进行出站连接](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-network-security-groups.png)

   若要详细了解 Azure 网络安全组，请参阅[安全组](../../../virtual-network/security-overview.md)。 

## <a name="azure-firewall-for-outbound-connections-to-internet"></a>用于出站连接到 Internet 的 Azure 防火墙

若要在不允许从公共终结点入站连接到 VM 的情况下实现出站连接到公共终结点，另一种方式是使用 Azure 防火墙。 Azure 防火墙是一种托管服务，具有内置的高可用性，可以跨越多个可用性区域。  
还需要部署[用户定义路由](../../../virtual-network/virtual-networks-udr-overview.md#custom-routes)，它与其中部署 VM 和 Azure 负载均衡器的子网相关联，同时指向 Azure 防火墙，以便通过 Azure 防火墙路由流量。  
若要详细了解如何部署 Azure 防火墙，请参阅[部署和配置 Azure 防火墙](../../../firewall/tutorial-firewall-deploy-portal.md)。  

体系结构如下所示：

![使用 Azure 防火墙进行出站连接](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-firewall.png)

### <a name="important-considerations"></a>重要注意事项

- Azure 防火墙是云原生服务，具有内置的高可用性，并且支持区域部署。
- 需要其他子网，子网必须命名为 AzureFirewallSubnet。 
- 若要将大型数据集从 SAP VM 所在虚拟网络出站传输到另一个虚拟网络中的 VM 或公共终结点，那么这可能不是一种经济高效的解决方案。 其中一个例子就是跨虚拟网络复制大型备份。 有关详细信息，请参阅“Azure 防火墙定价”。  
- 如果企业防火墙解决方案不是 Azure 防火墙，并且你有安全要求（即所有出站流量都必须通过集中式企业解决方案），那么此解决方案可能不可行。  

>[!TIP]
>尽可能使用[服务标记](../../../virtual-network/security-overview.md#service-tags)，以降低 Azure 防火墙规则的复杂度。  

### <a name="deployment-steps"></a>部署步骤

1. 执行部署步骤的前提是，你已为 VM 定义虚拟网络和子网。  
2. 在其中部署 VM 和标准负载均衡器的同一虚拟网络中，创建子网 AzureFirewallSubnet。  
   1. 在 Azure 门户中，转到“虚拟网络”：单击“所有资源”，搜索“虚拟网络”，单击“虚拟网络”，然后选择“子网”。  
   1. 单击“添加子网”。 输入“AzureFirewallSubnet”作为名称。 输入相应的地址范围。 保存。  
3. 创建 Azure 防火墙。  
   1. 在 Azure 门户中，选择“所有资源”，然后依次单击“添加”、“防火墙”和“创建”。 选择“资源组”（选择虚拟网络所在的同一资源组）。  
   1. 输入 Azure 防火墙资源的名称。 例如，“MyAzureFirewall”。  
   1. 选择“区域”，并至少选择两个可用性区域（与其中部署 VM 的可用性区域保持一致）。  
   1. 选择其中部署 SAP VM 和 Azure 标准负载均衡器的虚拟网络。  
   1. 公共 IP 地址：单击“创建”，然后输入名称。 例如，“MyFirewallPublicIP”。  
4. 创建 Azure 防火墙规则，以允许出站连接到指定的公共终结点。 下面的示例展示了如何允许对 Azure 管理 API 公共终结点的访问。  
   1. 依次选择“规则”和“网络规则集合”，然后单击“添加网络规则集合”。  
   1. 姓名：“MyOutboundRule”，输入“优先级”，选择操作“允许”。  
   1. 服务：命名“ToAzureAPI”。  协议：选择“任何”。 源地址：输入其中部署 VM 和标准负载均衡器的子网的范围；例如，“11.97.0.0/24”。 目标端口：输入“*”<b></b>。  
   1. 保存
   1. 当你仍在“Azure 防火墙”上时，选择“概述”。 记下 Azure 防火墙的专用 IP 地址。  
5. 创建指向 Azure 防火墙的路由  
   1. 在 Azure 门户中，选择“所有资源”，然后依次单击“添加”、“路由表”和“创建”。  
   1. 输入名称“MyRouteTable”，依次选择“订阅”、“资源组”和“位置”（与虚拟网络和防火墙的位置匹配）。  
   1. 保存  

   防火墙规则如下所示：![使用 Azure 防火墙进行出站连接](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-firewall-rule.png)

6. 创建从 VM 的子网指向 MyAzureFirewall 的专用 IP 的用户定义路由。
   1. 当你在“路由表”上时，单击“路由”。 选择“添加”。 
   1. 路由名称：ToMyAzureFirewall，地址前缀：0.0.0.0/0。 下一个跃点类型：选择“虚拟设备”。 下一个跃点地址：输入你配置的防火墙的专用 IP 地址，即11.97.1.4。  
   1. 保存

## <a name="using-proxy-for-pacemaker-calls-to-azure-management-api"></a>使用代理实现对 Azure 管理 API 的 Pacemaker 调用

可以使用代理允许对 Azure 管理 API 公共终结点进行 Pacemaker 调用。  

### <a name="important-considerations"></a>重要注意事项

  - 如果已有企业代理，可以通过它将出站调用路由到公共终结点。 对公共终结点的出站调用将通过企业控制点。  
  - 请确保代理配置允许出站连接到 Azure 管理 API：`https://management.azure.com` 和 `https://login.microsoftonline.com`  
  - 请确保有从 VM 指向代理的路由  
  - 代理只会处理 HTTP/HTTPS 调用。 如果需要通过不同的协议（如 RFC）对公共终结点进行出站调用，则需要使用替换解决方案  
  - 代理解决方案必须高度可用，以避免 Pacemaker 群集中的不稳定性  
  - 可能会在从 Azure 围墙代理到 Azure 管理 API 的调用中引入额外的延迟，具体视代理位置而定。 如果企业代理仍在本地，而 Pacemaker 群集在 Azure 中，则需要度量延迟，并考虑此解决方案是否适合你  
  - 如果还没有高度可用的企业代理，不建议使用这种方式，因为这样会给客户带来额外的成本和复杂度。 然而，如果你决定部署附加的代理解决方案，以允许从 Pacemaker 出站连接到 Azure 管理公共 API，请确保代理是高度可用的，并且从 VM 到代理的延迟低。  

### <a name="pacemaker-configuration-with-proxy"></a>包含代理的 Pacemaker 配置 

行业中有许多不同的代理选项。 关于代理部署的分步说明超出了本文档的范围。 在下面的示例中，假设代理正在响应 MyProxyService，并侦听端口 MyProxyPort。  
若要允许 Pacemaker 与 Azure 管理 API 进行通信，请在所有群集节点上执行以下步骤：  

1. 编辑 Pacemaker 配置文件 /etc/sysconfig/pacemaker，并添加以下行（所有群集节点）：

   ```console
   sudo vi /etc/sysconfig/pacemaker
   # Add the following lines
   http_proxy=http://MyProxyService:MyProxyPort
   https_proxy=http://MyProxyService:MyProxyPort
   ```

2. 在所有群集节点上，重启 Pacemaker 服务。  
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

## <a name="other-solutions"></a>其他解决方案

如果通过第三方防火墙路由出站流量：

- 如果使用 Azure 隔离代理，请确保防火墙配置允许与 Azure 管理 API 建立出站连接： `https://management.azure.com` 和 `https://login.microsoftonline.com`   
- 如果使用 SUSE 的 Azure 公有云更新基础结构来应用更新和修补程序，请参阅 [Azure 公有云更新基础结构 101](https://suse.com/c/azure-public-cloud-update-infrastructure-101/)

## <a name="next-steps"></a>后续步骤

* [了解如何在 Azure 中配置 SUSE 上的 Pacemaker](./high-availability-guide-suse-pacemaker.md)
* [了解如何在 Azure 中配置 Red Hat 上的 Pacemaker](./high-availability-guide-rhel-pacemaker.md)