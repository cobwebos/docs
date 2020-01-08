---
title: Azure Kubernetes Service （AKS）中的高可用性和灾难恢复
description: 了解群集操作员的最佳实践，以实现应用程序的最大运行时间、在 Azure Kubernetes 服务中提供高可用性和准备灾难恢复（AKS）。
services: container-service
author: lastcoolnameleft
ms.service: container-service
ms.topic: conceptual
ms.date: 11/28/2018
ms.author: thfalgou
ms.custom: fasttrack-edit
ms.openlocfilehash: ff55257891ff379bea9ff44aa5136195ced44354
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/02/2020
ms.locfileid: "75613102"
---
# <a name="best-practices-for-business-continuity-and-disaster-recovery-in-azure-kubernetes-service-aks"></a>Azure Kubernetes 服务 (AKS) 中实现业务连续性和灾难恢复的最佳做法

在 Azure Kubernetes 服务 (AKS) 中管理群集时，应用程序的正常运行时间变得非常重要。 AKS 通过在可用性集中使用多个节点来提供高可用性。 但这些多个节点不会保护你的系统免受区域故障的问题。 为了最大限度地延长运行时间，请提前计划维护业务连续性并为灾难恢复做准备。

本文重点介绍如何在 AKS 中规划业务连续性和灾难恢复。 学习如何：

> [!div class="checklist"]
> * 规划多个区域中的 AKS 群集。
> * 使用 Azure 流量管理器将流量路由到多个群集。
> * 使用容器映像注册表的异地复制。
> * 规划跨多个群集的应用程序状态。
> * 跨多个区域复制存储。

## <a name="plan-for-multiregion-deployment"></a>规划多区域部署

**最佳做法**：部署多个 AKS 群集时，请选择可用的区域，并使用配对区域。

一个 AKS 群集部署到单个区域中。 若要防止系统发生区域故障，请将应用程序部署到跨不同区域的多个 AKS 群集。 在计划部署 AKS 群集的位置时，请考虑以下事项：

* [**AKS 地区可用性**](https://docs.microsoft.com/azure/aks/quotas-skus-regions#region-availability)：选择靠近用户的区域。 AKS 不断扩展到新区域。
* [**Azure 配对区域**](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)：对于地理区域，请选择两个相互配对的区域。 配对区域协调平台更新，并在需要时确定恢复工作量的优先级。
* **服务可用性**：决定配对区域应该是热热、热/温还是热/冷。 是否要同时运行这两个区域，其中一个区域*准备好*开始服务通信？ 或者您是否希望一个区域有时间准备好提供流量？

AKS 区域可用性和配对区域是共同考虑因素。 将 AKS 群集部署到配对区域中，这些区域旨在一起管理区域灾难恢复。 例如，AKS 在美国东部和美国西部提供。 这些区域成对出现。 在创建 AKS BC/DR 策略时，选择这两个区域。

部署应用程序时，请将另一个步骤添加到 CI/CD 管道以部署到这些多个 AKS 群集。 如果你不更新你的部署管道，则应用程序可能只部署到你的某个区域和 AKS 群集。 定向到次要区域的客户流量不会接收到最新的代码更新。

## <a name="use-azure-traffic-manager-to-route-traffic"></a>使用 Azure 流量管理器路由流量

**最佳做法**： Azure 流量管理器可将客户定向到最近的 AKS 群集和应用程序实例。 为了获得最佳性能和冗余，请在应用程序流量进入 AKS 群集之前，直接通过流量管理器进行所有应用程序流量。

如果在不同区域有多个 AKS 群集，请使用流量管理器控制流量如何流向每个群集中运行的应用程序。 [Azure 流量管理器](https://docs.microsoft.com/azure/traffic-manager/)是可以在区域间分布网络流量的基于 DNS 的流量负载均衡器。 使用流量管理器根据群集响应时间或根据地域来路由用户。

![与流量管理器 AKS](media/operator-best-practices-bc-dr/aks-azure-traffic-manager.png)

具有单个 AKS 群集的客户通常会连接到给定应用程序的服务 IP 或 DNS 名称。 在 multicluster 部署中，客户应连接到流量管理器 DNS 名称，该名称指向每个 AKS 群集上的服务。 使用流量管理器终结点定义这些服务。 每个终结点都是*服务负载均衡器 IP*。 使用此配置可将网络流量从一个区域中的流量管理器终结点定向到不同区域中的终结点。

![通过流量管理器的地理路由](media/operator-best-practices-bc-dr/traffic-manager-geographic-routing.png)

流量管理器执行 DNS 查找，并返回用户最适当的终结点。 嵌套式配置文件可以设置主位置的优先级。 例如，用户通常应连接到最靠近的地理区域。 如果该区域出现问题，流量管理器会将用户定向到次要区域。 此方法可确保客户可以连接到应用程序实例，即使其最接近的地理区域不可用。

有关设置终结点和路由的详细信息，请参阅[使用流量管理器配置地理流量路由方法](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-configure-geographic-routing-method)。

### <a name="layer-7-application-routing-with-azure-front-door-service"></a>第7层应用程序通过 Azure 前门服务进行路由

流量管理器使用 DNS （第3层）来整形流量。 [Azure 前门服务](https://docs.microsoft.com/azure/frontdoor/front-door-overview)提供 HTTP/HTTPS （第7层）路由选项。 Azure 前门服务的其他功能包括 SSL 终止、自定义域、web 应用程序防火墙、URL 重写和会话相关性。 查看应用程序流量的需求，以了解哪种解决方案是最合适。

### <a name="interconnect-regions-with-global-virtual-network-peering"></a>具有全局虚拟网络对等互连的互连区域

如果群集需要彼此通信，则可以通过[虚拟网络对等互连](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)来实现两个虚拟网络之间的连接。 这种技术将虚拟网络相互互连，同时跨 Microsoft 主干网络提供高带宽，甚至跨不同地理区域。

对等互连虚拟网络（运行 AKS 群集的虚拟网络）的先决条件是在 AKS 群集中使用标准负载均衡器，以便可通过虚拟网络对等互连访问 kubernetes 服务。

## <a name="enable-geo-replication-for-container-images"></a>为容器映像启用异地复制

**最佳做法**：在 Azure 容器注册表中存储容器映像，并将注册表异地复制到每个 AKS 区域。

若要在 AKS 中部署和运行应用程序，需要一种方法来存储和提取容器映像。 容器注册表与 AKS 集成，因此它可以安全地存储容器映像或 Helm 图表。 容器注册表支持多主机地域复制，以自动将映像复制到世界各地的 Azure 区域。 

若要提高性能和可用性，请使用容器注册表异地复制在具有 AKS 群集的每个区域中创建注册表。 然后，每个 AKS 群集从同一区域的本地容器注册表中提取容器映像：

![容器映像的容器注册表异地复制](media/operator-best-practices-bc-dr/acr-geo-replication.png)

使用容器注册表异地复制从同一区域中提取映像时，结果为：

* **更快**：从同一 Azure 区域内的高速、低延迟网络连接提取映像。
* **更可靠**：如果某个区域不可用，AKS 群集将从可用容器注册表中提取映像。
* 更**便宜**：数据中心之间没有网络出口费用。

异地复制是*高级*SKU 容器注册表的一项功能。 有关如何配置异地复制的信息，请参阅[容器注册表异地复制](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication)。

## <a name="remove-service-state-from-inside-containers"></a>从容器内删除服务状态

**最佳做法**：在可能的情况下，不要在容器中存储服务状态。 改为使用支持多区域复制的 Azure 平台即服务（PaaS）。

*服务状态*是指服务运行所需的内存中或磁盘上的数据。 状态包括服务读取和写入的数据结构和成员变量。 根据服务的构建方式，状态还可能包括存储在磁盘上的文件或其他资源。 例如，状态可能包含数据库用于存储数据和事务日志的文件。

状态可以外部化或与操作状态的代码协同定位。 通常，通过使用在网络上不同的计算机上运行或在同一台计算机上运行的数据库或其他数据存储来外部化状态。

当容器和微服务不会保留状态时，容器和的灵活性最高。 由于应用程序几乎始终包含某种状态，因此请使用 PaaS 解决方案，如 Azure Database for MySQL、Azure Database for PostgreSQL 或 Azure SQL 数据库。

若要构建可移植应用程序，请参阅以下指南：

* [12因素应用方法](https://12factor.net/)
* [在多个 Azure 区域中运行 web 应用程序](https://docs.microsoft.com/azure/architecture/reference-architectures/app-service-web-app/multi-region)

## <a name="create-a-storage-migration-plan"></a>创建存储迁移计划

**最佳做法**：如果使用 Azure 存储，请准备并测试如何将存储从主要区域迁移到备份区域。

应用程序可以使用 Azure 存储来存储其数据。 由于你的应用程序分布在不同区域的多个 AKS 群集中，因此需要保持存储同步。 下面是复制存储的两种常用方法：

* 基于基础结构的异步复制
* 基于应用程序的异步复制

### <a name="infrastructure-based-asynchronous-replication"></a>基于基础结构的异步复制

即使在删除 pod 后，应用程序也可能需要持久存储。 在 Kubernetes 中，可以使用永久卷来持久保存数据存储。 永久性卷装载到节点 VM，然后公开到 pod。 即使将 pod 移至同一群集内的其他节点，永久卷也会跟随盒。

你使用的复制策略取决于你的存储解决方案。 常见的存储解决方案（例如[gluster storage](https://docs.gluster.org/en/latest/Administrator%20Guide/Geo%20Replication/)、 [Ceph](https://docs.ceph.com/docs/master/cephfs/disaster-recovery/)、[车](https://rook.io/docs/rook/v1.2/ceph-disaster-recovery.html)和[Portworx](https://docs.portworx.com/scheduler/kubernetes/going-production-with-k8s.html#disaster-recovery-with-cloudsnaps) ）提供了自己的有关灾难恢复和复制的指导。

典型的策略是提供公共存储点，应用程序可在其中写入数据。 然后跨区域复制此数据，在本地访问。

![基于基础结构的异步复制](media/operator-best-practices-bc-dr/aks-infra-based-async-repl.png)

如果使用 Azure 托管磁盘，可以选择以下复制和 DR 解决方案：

* [Azure 上的 Velero](https://github.com/vmware-tanzu/velero-plugin-for-microsoft-azure/blob/master/README.md)
* [Azure Site Recovery](https://azure.microsoft.com/blog/asr-managed-disks-between-azure-regions/)

### <a name="application-based-asynchronous-replication"></a>基于应用程序的异步复制

Kubernetes 当前不提供基于应用程序的异步复制的本机实现。 由于容器和 Kubernetes 是松散耦合的，因此任何传统的应用程序或语言方法都应该有效。 通常，应用程序本身会复制存储请求，然后将这些请求写入每个群集的基础数据存储。

![基于应用程序的异步复制](media/operator-best-practices-bc-dr/aks-app-based-async-repl.png)

## <a name="next-steps"></a>后续步骤

本文重点介绍 AKS 群集的业务连续性和灾难恢复注意事项。 有关 AKS 中的群集操作的详细信息，请参阅以下有关最佳实践的文章：

* [多租户和群集隔离][aks-best-practices-cluster-isolation]
* [基本 Kubernetes 计划程序功能][aks-best-practices-scheduler]

<!-- INTERNAL LINKS -->
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
