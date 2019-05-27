---
title: 高可用性和灾难恢复在 Azure Kubernetes 服务 (AKS)
description: 了解群集操作员的最佳实践，以实现应用程序，从而提供高可用性和灾难恢复 Azure Kubernetes 服务 (AKS) 中准备的最大运行时间。
services: container-service
author: lastcoolnameleft
ms.service: container-service
ms.topic: conceptual
ms.date: 11/28/2018
ms.author: lastcoolnameleft
ms.openlocfilehash: 93af2e4c373701383a674c694f7799ba890414dd
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2019
ms.locfileid: "65887427"
---
# <a name="best-practices-for-business-continuity-and-disaster-recovery-in-azure-kubernetes-service-aks"></a>Azure Kubernetes 服务 (AKS) 中实现业务连续性和灾难恢复的最佳做法

在 Azure Kubernetes 服务 (AKS) 中管理群集时，应用程序的正常运行时间变得非常重要。 AKS 通过在可用性集中使用多个节点来提供高可用性。 但这些多个节点不从区域故障保护你的系统。 若要最大化运行时间，请提前计划以保持业务连续性和灾难恢复准备。

本文重点介绍如何规划业务连续性和灾难恢复在 AKS 中。 学习如何：

> [!div class="checklist"]
> * 规划多个区域中 AKS 群集。
> * 跨多个群集使用 Azure 流量管理器路由流量。
> * 使用异地复制来实现在容器映像注册表。
> * 规划应用程序状态在多个群集。
> * 跨多个区域复制存储。

## <a name="plan-for-multiregion-deployment"></a>规划多区域部署

**最佳做法**：当部署多个 AKS 群集时，选择提供，AKS 的区域，并使用配对的区域。

一个 AKS 群集部署到单个区域中。 若要从区域故障保护您的系统，可跨不同区域部署到 AKS 群集中多个应用程序。 当你计划部署 AKS 群集的位置时，请考虑：

* [**AKS 区域可用性**](https://docs.microsoft.com/azure/aks/quotas-skus-regions#region-availability):选择靠近用户的区域。 AKS 不断地扩展到新区域。
* [**Azure 配对区域**](https://docs.microsoft.com/azure/best-practices-availability-paired-regions):对于你的地理区域，选择两个相互配对的区域。 配对的区域协调平台更新和确定恢复工作的优先级在需要时。
* **服务可用性**:决定是否在配对的区域应是热/热、 热/热，热/冷。 你想要在同一时间，具有一个区域中运行这两个区域*准备*开始提供流量？ 或是否想要有时间来准备好处理流量的一个区域？

AKS 区域可用性和配对的区域的联合的考虑因素。 将 AKS 群集部署到配对区域中，这些区域旨在一起管理区域灾难恢复。 例如，AKS 目前在美国东部和美国西部。 这些区域配对。 创建 AKS 业务连续性/灾难恢复策略时，请选择这两个区域。

在部署你的应用程序时，添加到你的 CI/CD 管道将部署到这些多个 AKS 群集的另一个步骤。 如果未更新部署管道，应用程序可能会部署到的区域和 AKS 群集中只有一个。 客户流量将定向到次要区域不会收到最新的代码更新。

## <a name="use-azure-traffic-manager-to-route-traffic"></a>使用 Azure 流量管理器路由流量

**最佳做法**：Azure 流量管理器可定向到其最近的 AKS 群集和应用程序实例的客户。 为获得最佳性能和冗余，直接通过流量管理器中的所有应用程序流量之前进入 AKS 群集。

如果在不同区域有多个 AKS 群集，使用流量管理器来控制流量如何传送到每个群集中运行的应用程序。 [Azure 流量管理器](https://docs.microsoft.com/azure/traffic-manager/)是可以在区域间分布网络流量的基于 DNS 的流量负载均衡器。 使用流量管理器将用户路由基于群集响应时间或根据地理位置。

![AKS 使用流量管理器](media/operator-best-practices-bc-dr/aks-azure-traffic-manager.png)

通常具有单个 AKS 群集的客户连接到给定的应用程序的服务 IP 或 DNS 名称。 在 multicluster 部署中，客户应连接到流量管理器 DNS 名称指向每个 AKS 群集上的服务。 使用流量管理器终结点定义这些服务。 每个终结点是*服务负载均衡器 IP*。 使用此配置将定向到不同的区域中的终结点的网络流量从一个区域中的流量管理器终结点。

![地理路由通过流量管理器](media/operator-best-practices-bc-dr/traffic-manager-geographic-routing.png)

流量管理器执行 DNS 查找，并返回用户的最适当的终结点。 嵌套式配置文件可以设置主位置的优先级。 例如，用户通常应连接到其最靠近的地理区域。 如果该区域有问题，流量管理器而是将用户定向到次要区域。 此方法可确保即使其最靠近的地理区域不可用的客户可以连接到应用程序实例。

有关如何设置终结点和路由的信息，请参阅[使用流量管理器配置地理流量路由方法](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-configure-geographic-routing-method)。

### <a name="layer-7-application-routing-with-azure-front-door-service"></a>第 7 层应用程序与 Azure 第一道防线服务路由

流量管理器使用 DNS （第 3 层） 到形状的流量。 [Azure 的第一道防线服务](https://docs.microsoft.com/azure/frontdoor/front-door-overview)提供了一个 HTTP/HTTPS （第 7 层） 路由选项。 Azure 第一道防线服务的附加功能包括 SSL 终止、 自定义的域、 web 应用程序防火墙、 URL 重写，和会话相关性。 查看应用程序流量的需求，以了解哪种解决方案是最合适。

## <a name="enable-geo-replication-for-container-images"></a>为容器映像启用异地复制

**最佳做法**：在 Azure 容器注册表和异地复制到每个 AKS 区域的注册表中存储你的容器映像。

若要在 AKS 中部署和运行应用程序，需要一种方法来存储和提取容器映像。 容器注册表与 AKS，集成，因此它可以安全地存储你的容器映像或 Helm 图表。 容器注册表支持多主机异地复制来自动将你的映像复制到世界各地的 Azure 区域。 

若要提高性能和可用性，使用容器注册表异地复制必须 AKS 群集的每个区域中创建注册表。 每个 AKS 群集然后将从同一区域中的本地容器注册表拉取容器映像：

![容器注册表异地复制容器映像](media/operator-best-practices-bc-dr/acr-geo-replication.png)

当您所在区域中拉取映像使用容器注册表异地复制时，其结果是：

* **更快地**:在同一 Azure 区域中拉取映像从高速、 低延迟网络连接。
* **更可靠**:如果一个区域不可用，在 AKS 群集从可用容器注册表拉取映像。
* **更便宜**:数据中心之间没有任何网络出口费用。

异地复制是一项功能*高级*SKU 容器注册表。 有关如何配置异地复制的信息，请参阅[容器注册表异地复制](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication)。

## <a name="remove-service-state-from-inside-containers"></a>从容器内删除服务状态

**最佳做法**：在可能的情况下，不会存储在容器内的服务状态。 相反，使用 Azure 平台即服务 (PaaS) 支持多区域复制。

*服务状态*指的是服务正常运行所需的内存中或磁盘上的数据。 状态包括服务读取和写入的数据结构和成员变量。 具体取决于该服务的体系结构，状态可能还包括文件或存储在磁盘其他资源。 例如，状态可能包括数据库用于存储数据和事务日志文件。

状态可以外部化或共置操作的状态代码。 通常情况下，外部状态化使用数据库或其他数据存储在不同计算机上运行通过网络或进程外运行在同一台计算机上。

容器和微服务是最有弹性时在其内部运行的进程不保留状态。 由于应用程序几乎总是包含一些状态，因此用于 Azure 数据库的 PaaS 解决方案 MySQL、 Azure Database for PostgreSQL 或 Azure SQL 数据库。

若要生成可移植应用程序，请参阅以下指南：

* [十二因素应用方法](https://12factor.net/)
* [在多个 Azure 区域中运行的 web 应用程序](https://docs.microsoft.com/azure/architecture/reference-architectures/app-service-web-app/multi-region)

## <a name="create-a-storage-migration-plan"></a>创建存储迁移计划

**最佳做法**：如果使用 Azure 存储，准备和测试如何将你的存储从主要区域迁移到备份区域。

你的应用程序可能会为其数据使用 Azure 存储。 因为您的应用程序分布在不同区域中的多个 AKS 群集上，您需要保持同步的存储。 下面是复制存储两种常见方式：

* 基于基础结构的异步复制
* 基于应用程序的异步复制

### <a name="infrastructure-based-asynchronous-replication"></a>基于基础结构的异步复制

即使在删除一个 pod 后，你的应用程序可能需要持久性存储区。 在 Kubernetes 中，您可以使用永久性卷来持久保存数据存储。 永久性卷会装载到节点 VM，然后公开到 pod。 永久性卷按照 pod，即使 pod 移到同一群集内的其他节点。

您使用的复制策略取决于你的存储解决方案。 常见的存储解决方案，如[Gluster](https://docs.gluster.org/en/latest/Administrator%20Guide/Geo%20Replication/)， [Ceph](http://docs.ceph.com/docs/master/cephfs/disaster-recovery/)，[车](https://rook.io/docs/rook/master/disaster-recovery.html)，以及[Portworx](https://docs.portworx.com/scheduler/kubernetes/going-production-with-k8s.html#disaster-recovery-with-cloudsnaps)提供有关灾难恢复他们自己的指导和复制。

典型策略是提供一个公共存储路径，应用程序可以在其中编写他们的数据。 然后跨区域复制此数据，在本地访问。

![基于基础结构的异步复制](media/operator-best-practices-bc-dr/aks-infra-based-async-repl.png)

如果使用 Azure 托管磁盘，则可以选择复制和灾难恢复解决方案，例如这些：

* [Azure 上的 Ark](https://github.com/heptio/ark/blob/master/docs/azure-config.md)
* [Azure Site Recovery](https://azure.microsoft.com/blog/asr-managed-disks-between-azure-regions/)

### <a name="application-based-asynchronous-replication"></a>基于应用程序的异步复制

目前，Kubernetes 提供基于应用程序的异步复制没有本机实现。 容器和 Kubernetes 松散耦合的因为任何传统的应用程序或语言方法也适用。 通常情况下，应用程序本身进行复制的存储请求，然后写入每个群集的基础数据存储。

![基于应用程序的异步复制](media/operator-best-practices-bc-dr/aks-app-based-async-repl.png)

## <a name="next-steps"></a>后续步骤

本文重点介绍业务连续性和灾难恢复注意事项 AKS 群集。 有关在 AKS 中的群集操作的详细信息，请参阅以下有关的最佳做法的文章：

* [多租户和群集的隔离][aks-best-practices-cluster-isolation]
* [基本 Kubernetes 计划程序功能][aks-best-practices-scheduler]

<!-- INTERNAL LINKS -->
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
