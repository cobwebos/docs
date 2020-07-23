---
title: 概念 - OpenShift 4 上的 Azure Red Hat 的网络图
description: Azure Red Hat OpenShift 网络图及概述
author: sakthi-vetrivel
ms.author: suvetriv
ms.topic: tutorial
ms.service: container-service
ms.date: 06/22/2020
ms.openlocfilehash: fa39fe3e065c230f7e06ee443d2aa56227dc6f31
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/05/2020
ms.locfileid: "85965309"
---
# <a name="networking-in-azure-red-hat-on-openshift-4"></a>OpenShift 4 上 Azure Red Hat 中的网络

本指南概述 OpenShift 4 群集上的 Azure Red Hat 中的网络，并提供重要终结点的图示和列表。

若要详细了解核心 OpenShift 网路概念，请参阅 [Azure Red Hat OpenShift 4 网络文档](https://docs.openshift.com/aro/4/networking/understanding-networking.html)。

## <a name="networking-concepts-in-azure-red-hat-openshift"></a>Azure Red Hat OpenShift 中的网络概念

![Azure Red Hat OpenShift 4 网络示意图](./media/concepts-networking/aro4-networking-diagram.png)

部署 OpenShift 4 上的 Azure Red Hat 时，整个群集都包含在虚拟网络中。 在此虚拟网络中，主节点和工作器节点都位于各自的子网中。 每个子网都使用公共和内部负载均衡器。

## <a name="explanation-of-endpoints"></a>终结点说明

以下列表涵盖 Azure Red Hat OpenShift 群集中的重要终结点。

* **aro-pls**
    * 这是一个 Azure 专用链接终结点，由 Microsoft 和 Red Hat 站点的可靠性工程师用于帮助管理群集。
* **aro-internal-lb**
    * 此终结点对流往 api 服务器的流量进行均衡。 对于此负载均衡器，主节点位于后端池中。
* **aro-public-lb**
    * 如果是公共 api，此终结点会将流量路由到 api 服务器并对其进行均衡。 此终结点分配一个公共传出 IP，以便主节点可以访问 Azure 资源管理器并发回有关群集运行状况的报告。
* **aro-internal**
    * 此终结点均衡内部服务流量。 对于此负载均衡器，工作器节点位于后端池中。
    * 默认情况下不创建此负载均衡器。 创建具有正确注释的 LoadBalancer 类型的服务后，将随即创建此负载均衡器。 例如：service.beta.kubernetes.io/azure-load-balancer-internal: "true"。
* **网络策略（入口）**
    * 作为 OpenShift SDN 的一部分受支持
    * 默认情况下启用，由客户强制执行
    * 符合 V1 NetworkPolicy，但还不支持“Egress 和 IPBlock”类型
    * **aro**
    * 此终结点对流往 api 服务器的流量进行均衡。 对于此负载均衡器，主节点位于后端池中。
  * **aro-internal-lb**
    * 此终结点用于公共流量。 创建应用程序和路由时，这是入口流量的路径。
    * 此负载均衡器还通过 Azure 负载均衡器出站规则应用于来自工作器节点中运行的任何 Pod 的出口 Internet 连接。
        * 当前出站规则不可配置。 这些规则为每个节点分配 1,024 个 TCP 端口。
        * LB 规则中未配置 DisableOutboundSnat，因此 Pod 可以获取此 ALB 中配置的任何公共 IP 作为出口 IP。
        * 作为前两点的结果，添加临时 SNAT 端口的唯一方法就是将公共 LoadBalanceer 类型的服务添加到 ARO。
* **网络策略（出口）**
    * 通过使用 OpenShift 中的出口防火墙功能来支持出口策略。
    * 该策略每个命名空间/项目只能有一个。
    * “默认”命名空间不支持出口策略。
    * 出口策略规则按顺序（从第一个到最后一个）计算。
    * **aro-outbound-pip**
        * 此终结点充当工作器节点的公共 IP (PIP)。
        * 此终结点使服务能够将来自 Azure Red Hat OpenShift 群集的特定 IP 添加到允许列表。
* **aro-node-nsg**
    * 公开服务时，api 将在此网络安全组中创建一个规则，以便流量流经并到达节点。
    * 默认情况下，此网络安全组允许所有出站流量。 目前，只能将出站流量限制到 Azure Red Hat OpenShift 控制平面。
* **aro-controlplane-nsg**
    * 此终结点只允许流量通过主节点的端口 6443 进入。
* **Azure 容器注册表**
    * 这是一个由 Microsoft 在内部提供和使用的容器注册表。
        * 此注册表提供主机平台映像和群集组件。 例如监视用或记录用容器。
        * 不适合 Azure Red Hat OpenShift 客户使用。  
        * 只读。
        * 与此注册表的连接是经由服务终结点建立的（Azure 服务之间的内部连接）。
        * 默认情况下，此内部注册表在群集之外不可用。
* **专用链接**
    * 允许管理平面到群集内部的网络连接，用于实现群集管理。
    * Microsoft 和 Red Hat 站点可靠性工程师会帮助你管理群集。

## <a name="networking-basics-in-openshift"></a>OpenShift 中的网络基础知识

OpenShift 软件定义的网络 (SDN) 用于通过 Open vSwitch (OVS) 配置叠加网络，这是一种基于容器网络接口 (CNI) 规范的 OpenFlow 实现。 SDN 支持不同的插件，网络策略是在 OpenShift 4 上的 Azure Red Hat 中使用的插件。 所有网络通信都由 SDN 管理，因此虚拟网络上不需要额外的路由来实现 Pod 到 Pod 通信。

## <a name="azure-red-hat-openshift-networking-specifics"></a>Azure Red Hat OpenShift 网络详细信息

以下功能特定于 Azure Red Hat OpenShift：
* 支持自带虚拟网络。
* Pod 和服务网络 CIDR 是可配置的。
* 节点和主节点位于不同的子网中。
* 节点和主节点虚拟网络子网的最小值应设为 /27。
* Pod CIDR 的最小值应设为 /18（Pod 网络是不可路由的 IP，并且仅在 OpenShift SDN 内使用）。
* 每个节点都为其 Pod 分配 /23 子网 （512 个 IP）。 此值不能更改。
* 不能将 Pod 附加到多个网络。
* 不能配置出口静态 IP。 （这是一种 OpenShift 功能。 有关信息，请参阅[配置出口 IP](https://docs.openshift.com/aro/4/networking/openshift_sdn/assigning-egress-ips.html)）。

## <a name="network-settings"></a>网络设置

以下网络设置在 Azure Red Hat OpenShift 4 中可用：

* “API 可见性”- 设置当运行 [az aro create command](tutorial-create-cluster.md#create-the-cluster) 时的 API 可见性。
    * “公共”- 外部网络可访问 API 服务器。
    * “专用”- 从主节点子网向 API 服务器分配了专用 IP，只能使用连接的网络（对等虚拟网络、群集中的其他子网）访问该服务器。 将代表客户创建专用 DNS 区域。
* “入口可见性”- 设置当运行 [az aro create command](tutorial-create-cluster.md#create-the-cluster) 时的 API 可见性。
    * “公共”路由将默认为公共 Azure 标准负载均衡器（可以更改）。
    * “专用”路由将默认为内部负载均衡器（可以更改）。

## <a name="network-security-groups"></a>网络安全组
将在节点的资源组中创建网络安全组，该资源组已锁定。 直接向不在节点 NIC 上的子网分配网络安全组。 网络安全组是不可变的，这意味着你没有更改它们的权限。 

但是，如果 API 服务器公开可见，就无法创建网络安全组并将其分配给 NIC。

## <a name="domain-forwarding"></a>域转发
Azure Red Hat OpenShift 使用 CoreDNS。 可以配置域转发（请参阅有关[使用 DNS 转发](https://docs.openshift.com/aro/4/networking/dns-operator.html#nw-dns-forward_dns-operator)的文档了解详细信息）。

目前，你无法将自己的 DNS 引入虚拟网络。


详细了解出站流量和 Azure Red Hat OpenShift 的出口支持，请参阅[支持策略](support-policies-v4.md)文档。
