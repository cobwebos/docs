---
title: 操作员最佳做法 - Azure Kubernetes 服务 (AKS) 中的高可用性和灾难恢复
description: 了解群集操作员的最佳做法，以最大化应用程序提供高可用性的正常运行时间，并为 Azure Kubernetes 服务 (AKS) 中的灾难恢复情况做好准备
services: container-service
author: lastcoolnameleft
ms.service: container-service
ms.topic: conceptual
ms.date: 11/28/2018
ms.author: lastcoolnameleft
ms.openlocfilehash: 622cdd36a1ecf582c4cdb883b12753ee2a75d50e
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2018
ms.locfileid: "52853979"
---
# <a name="best-practices-for-business-continuity-and-disaster-recovery-in-azure-kubernetes-service-aks"></a>Azure Kubernetes 服务 (AKS) 中实现业务连续性和灾难恢复的最佳做法

在 Azure Kubernetes 服务 (AKS) 中管理群集时，应用程序的正常运行时间变得非常重要。 AKS 通过在可用性集中使用多个节点来提供高可用性。 这些多个节点不能保护你免受区域故障的影响。 为了最大化正常运行时间，请实现一些业务连续性和灾难恢复功能。

本篇最佳做法文章重点介绍帮助你计划在 AKS 中实现业务连续性和灾难恢复的注意事项。 学习如何：

> [!div class="checklist"]
* [在多个区域规划 AKS 群集](#region-planning)
* [使用 Azure 流量管理器跨多个群集路由流量](#ingress-traffic)
* [为容器映像注册表使用异地复制](#container-registry)
* [跨多个群集计划应用程序状态](#managing-application-state)
* [跨多个区域复制存储](#storage)

## <a name="plan-for-multi-region-deployment"></a>多区域部署计划

**最佳实践指南** - 在部署多个 AKS 群集时，选择提供 AKS 的区域并使用配对区域。

一个 AKS 群集部署到单个区域中。 为防止区域故障，可以跨不同区域将应用程序部署到多个 AKS 群集中。 当计划在哪些区域部署 AKS 群集时，应注意以下事项：

* [AKS 区域可用性](https://docs.microsoft.com/azure/aks/container-service-quotas#region-availability)
  * 选择靠近用户的区域。 AKS 正不断向新区域扩展。
* [Azure 配对区域](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)
  * 对于你的地理区域，选择两个相互配对的区域。 这些区域协调平台更新，并在需要时确定恢复工作的优先级。
* 服务可用性级别（热/热、热/暖、热/冷）
  * 是否希望同时运行两个区域，一个区域已准备好开始提供流量，一个区域需要时间来准备好提供流量。

AKS 区域可用性和配对区域是共同考虑的因素。 将 AKS 群集部署到配对区域中，这些区域旨在一起管理区域灾难恢复。 例如，AKS 现已在美国东部和美国西部推出。 这些区域也是配对的。 创建 AKS 业务连续性/灾难恢复策略时，建议使用这两个区域。

在部署应用程序时，还必须到向 CI/CD 管道添加另一个步骤以部署到这些多个 AKS 群集中。 如果未更新部署管道，应用程序部署可能只能部署到其中一个区域和 AKS 群集。 定向到次要区域的客户流量不会收到最新代码更新。

## <a name="use-azure-traffic-manager-to-route-traffic"></a>使用 Azure 流量管理器路由流量

**最佳实践指南** - Azure 流量管理器可以将客户定向到最近的 AKS 群集和应用程序实例。 为获得最佳性能和冗余，在进入 AKS 群集之前，通过流量管理器来定向所有应用程序流量。

对于位于不同区域的多个 AKS 群集，需要控制如何将流量定向到在每个群集中运行的应用程序。 [Azure 流量管理器](https://docs.microsoft.com/azure/traffic-manager/)是可以在区域间分布网络流量的基于 DNS 的流量负载均衡器。 可以根据群集响应时间或地理位置路由用户。

![AKS 和 Azure 流量管理器](media/operator-best-practices-bc-dr/aks-azure-traffic-manager.png)

在单个 AKS 群集中，客户通常连接到给定应用程序的服务 IP 或 DNS 名称。 在多群集部署中，客户应连接到指向每个 AKS 群集上的服务的流量管理器 DNS 名称。 使用流量管理器终结点定义这些服务。 每个终结点都是服务负载均衡器 IP。 此配置允许将网络流量从一个区域的流量管理器终结点定向到另一个区域的终结点。

![使用 Azure 流量管理器进行地理路由](media/operator-best-practices-bc-dr/traffic-manager-geographic-routing.png)

流量管理器用于执行 DNS 查找，并为用户返回最适当的终结点。 可以使用嵌套的配置文件，并为主位置指定优先级。 例如，用户应主要连接到最近的地理区域。 如果该区域有问题，流量管理器会将其定向到次要区域。 此方式确保客户始终可以连接到应用程序实例，即使最近的地理区域不可用。

有关如何设置这些终结点和路由的步骤，请参阅[使用流量管理器配置地理流量路由方法](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-configure-geographic-routing-method)。

### <a name="layer-7-application-routing-with-azure-front-door"></a>第 7 层应用程序路由与 Azure Front Door

Azure 流量管理器使用 DNS（第 3 层）来塑造流量。 [Azure Front Door（预览版）](https://docs.microsoft.com/azure/frontdoor/front-door-overview)提供了一个 HTTP/HTTPS（第 7 层）路由选项。 Front Door 的附加功能包括 SSL 终止、自定义域、Web 应用程序防火墙、URL 重写和会话相关性。

查看应用程序流量的需求，以了解哪种解决方案是最合适。

## <a name="enable-geo-replication-for-container-images"></a>为容器映像启用异地复制

**最佳实践指南** - 将容器映像存储在 Azure 容器注册表，并将注册表异地复制到每个 AKS 区域。

若要在 AKS 中部署和运行应用程序，需要一种方法来存储和提取容器映像。 Azure 容器注册表 (ACR) 可以与 AKS 集成来安全存储容器映像或 Helm 图表。 ACR 支持多主数据库异地复制来自动将映像复制到世界各地的 Azure 区域。 若要提高性能和可用性，请使用 ACR 异地复制在拥有 AKS 群集的每个区域创建一个注册表。 然后每个 AKS 群集将从同一区域的本地 ACR 注册表中拉取容器映像：

![用于容器映像的 Azure 容器注册表异地复制](media/operator-best-practices-bc-dr/acr-geo-replication.png)

使用 ACR 异地复制的好处包括：

* 从同一区域提取映像速度更快。 可以从同一个 Azure 区域内的高速、低延迟网络连接中提取映像。
* **从同一区域提取映像更可靠。** 如果一个区域不可用，AKS 群集将从另一个仍然可用的 ACR 注册表中提取映像。
* **从同一区域提取映像更经济实惠。** 数据中心之间没有任何网络出口费用。

异地复制是高级 SKU ACR 注册表的一项功能。 有关如何配置复制的步骤，请参阅 [Azure 容器注册表异地复制](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication)

## <a name="remove-service-state-from-inside-containers"></a>从容器内删除服务状态

**最佳实践指南** - 在可能的情况下，不要将服务状态存储在容器中。 相反，使用支持多区域复制的 Azure PaaS 服务。

服务状态指的是服务正常运行所需的内存或磁盘数据。 状态包括服务读取和写入的数据结构和成员变量。 状态可能还包括存储在磁盘上的文件或其他资源，具体取决于服务的体系结构。 例如，数据库用于存储数据和事务日志的文件。

状态可以外部化或与操作状态的代码共存。 通常使用一个数据库或（在网络中不同计算机上运行或同一计算机进程外运行的）其他数据存储空间来实现状态的外部化。

当容器和微服务中运行的进程不保持状态时，它们最有弹性。 由于应用程序几乎总是包含一些状态，因此请使用平台即服务解决方案，例如 Azure Database for MySQL/Postgres 或 Azure SQL。

有关如何构建更易于移植的应用程序的详细信息，请参阅以下准则：

* [十二因素应用方法](https://12factor.net/)。
* [在多个 Azure 区域中运行 Web 应用程序](https://docs.microsoft.com/azure/architecture/reference-architectures/app-service-web-app/multi-region)

## <a name="create-a-storage-migration-plan"></a>创建存储迁移计划

**最佳实践指南** - 如果使用 Azure 存储，准备和测试如何将存储从主区域迁移到备份区域。

应用程序可能会为其数据使用 Azure 存储。 由于应用程序分布在不同区域的多个 AKS 群集中，因此需要保持存储同步。 复制存储的两种常用方法包括以下方法：

* 基于应用程序的异步复制
* 基于基础结构的异步复制

### <a name="infrastructure-based-asynchronous-replication"></a>基于基础结构的异步复制

即使删除了 pod，应用程序也可能需要持久存储。 在 Kubernetes 中，可以使用永久性卷来持久保存数据存储。 这些持久卷会装载到节点 VM，然后公开给 pod。 永久性卷遵循 pod，即使 pod 被移动到同一群集内的其他节点也是如此。

根据存储解决方案的使用情况，复制策略可能不同。 常见的存储解决方案（如 [Gluster](https://docs.gluster.org/en/latest/Administrator%20Guide/Geo%20Replication/)、[CEPH](http://docs.ceph.com/docs/master/cephfs/disaster-recovery/)、[Rook](https://rook.io/docs/rook/master/disaster-recovery.html) 和 [Portworx](https://docs.portworx.com/scheduler/kubernetes/going-production-with-k8s.html#disaster-recovery-with-cloudsnaps)都有其自己的指南。

中心方法是应用程序写入数据的常见存储点。 然后跨区域复制此数据，在本地访问。

![基于基础结构的异步复制](media/operator-best-practices-bc-dr/aks-infra-based-async-repl.png)

如果使用 Azure 托管磁盘，可用复制和 DR 解决方案将包括使用以下方法之一：

* [Azure 上的 Ark](https://github.com/heptio/ark/blob/master/docs/azure-config.md)
* [Azure Site Recovery](https://azure.microsoft.com/blog/asr-managed-disks-between-azure-regions/)

### <a name="application-based-asynchronous-replication"></a>基于应用程序的异步复制

目前还没有针对基于应用程序的异步复制的 Kubernetes 本机实现。 由于容器和 Kubernetes 的松散耦合特性，任何传统应用程序或语言方法都应适用。 中心方法是让应用程序本身复制存储请求，然后将这些存储请求写入每个群集的基础数据存储中。

![基于应用程序的异步复制](media/operator-best-practices-bc-dr/aks-app-based-async-repl.png)

## <a name="next-steps"></a>后续步骤

本文重点介绍 AKS 群集中的业务连续性和灾难恢复注意事项。 有关 AKS 中的群集操作的详细信息，请参阅以下最佳做法：

* [多租户和群集隔离][aks-best-practices-cluster-isolation]
* [基本 Kubernetes 计划程序功能][aks-best-practices-scheduler]

<!-- INTERNAL LINKS -->
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
