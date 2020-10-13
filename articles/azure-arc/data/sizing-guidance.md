---
title: 大小调整指南
description: 规划启用了 Azure Arc 的数据服务部署的大小。
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 3bd54d8a23aca7e493cd3c0ddb7f057a6e1f5362
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91761475"
---
# <a name="sizing-guidance"></a>大小调整指南

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="overview-of-sizing-guidance"></a>大小调整指南概述

在规划 Azure Arc 数据服务的部署时，应规划运行 Azure Arc 数据控制器所需的计算、内存和存储的正确量，以及要部署的 SQL 托管实例和 PostgreSQL 超大规模服务器组的数目。  由于启用了 Azure Arc 的数据服务部署在 Kubernetes 上，因此你可以通过添加其他计算节点或存储灵活地在一段时间内将额外容量添加到 Kubernetes 群集。  本指南将提供最低要求指导，并提供有关某些常见要求的推荐大小的指导。

## <a name="general-sizing-requirements"></a>一般大小要求

> [!NOTE]
> 如果你不熟悉本文中的概念，可以阅读有关 [Kubernetes 资源调控](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/) 和 [Kubernetes 大小表示法](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/#resource-units-in-kubernetes)的详细信息。

内核数必须是大于或等于1的整数值。

使用 azdata 进行部署时，应使用两个数字的幂来指定内存值：即使用后缀： Ki、Mi 或 Gi。

如果指定，限制值必须始终大于请求值。

内核的限制值是 SQL 托管实例和 PostgreSQL 超大规模服务器组的计费指标。

## <a name="minimum-deployment-requirements"></a>最低部署要求

支持 Azure Arc 的最小数据服务部署可视为 Azure Arc 数据控制器和一个 SQL 托管实例，以及一个包含两个工作节点的 PostgreSQL 超大规模服务器组。  对于此配置，你需要在 Kubernetes 群集上至少有 16 GB RAM 和4核 _可用_ 容量。  应该确保最小 Kubernetes 节点大小为 8 GB RAM 和4个内核，并为所有 Kubernetes 节点提供 16 GB RAM 的总容量。  例如，你可能有1个位于 32 GB RAM 和4个内核的节点，或者可以有2个节点，每个节点都有一个 16 GB RAM 和4个内核。

有关存储大小的详细信息，请参阅 [存储配置一](storage-configuration.md) 文。

## <a name="data-controller-sizing-details"></a>数据控制器调整大小详细信息

数据控制器是部署到 Kubernetes 群集的 pod 的集合，用于提供 API、控制器服务、引导程序以及监视数据库和仪表板。  下表描述了内存和 CPU 请求和限制的默认值。

|Pod 名称|CPU 请求|内存请求|CPU 限制|内存限制|注意|
|---|---|---|---|---|---|
|**引导程序**|100m|100Mi|200m|200Mi||
|**control**|400m|2Gi|1800m|2Gi||
|**controldb**|200m|3Gi|800m|6Gi||
|**controlwd**|10m|100Mi|100m|200Mi||
|**logsdb**|200m|1600Mi|2|1600Mi||
|**logsui**|100m|500Mi|2|2Gi||
|**metricsdb**|200m|800Mi|400m|2Gi||
|**metricsdc**|100m|200Mi|200m|300Mi|Metricsdc 是在群集中的每个 Kubernetes 节点上创建的 daemonset。  表中的数字 _按节点_列出。 如果在创建数据控制器之前在部署配置文件文件中设置 allowNodeMetricsCollection = false，则不会创建 metricsdc daemonset。|
|**metricsui**|20m|200Mi|500m|200Mi||
|**mgmtproxy**|200m|250Mi|500m|500Mi||

你可以在部署配置文件或 datacontroller YAML 文件中替代 controldb 和控制盒的默认设置。  例如：

```yaml
  resources:
    controller:
      limits:
        cpu: "1000m"
        memory: "3Gi"
      requests:
        cpu: "800m"
        memory: "2Gi"
    controllerDb:
      limits:
        cpu: "800m"
        memory: "8Gi"
      requests:
        cpu: "200m"
        memory: "4Gi"
```

有关存储大小的详细信息，请参阅 [存储配置一](storage-configuration.md) 文。

## <a name="sql-managed-instance-sizing-details"></a>SQL 托管实例大小调整详细信息

每个 SQL 托管实例都必须具有以下最低资源请求：
- 内存：2Gi
- 核心数：1

创建的每个 SQL 托管实例盒都有三个容器：
|容器名称|CPU 请求|内存请求|CPU 限制|内存限制|注意|
|---|---|---|---|---|---|
|fluentbit|100m|100Mi|未指定|未指定|除了为 SQL 托管实例指定的请求 _外_ ，fluentbit 容器资源请求。||
|弧形-sqlmi|已指定或未指定用户。|已指定或未指定用户。|已指定或未指定用户。|已指定或未指定用户。||
|collectd|未指定|未指定|未指定|未指定||

所有永久性卷的默认卷大小为5Gi。

## <a name="postgresql-hyperscale-server-group-sizing-details"></a>PostgreSQL 超大规模服务器组大小详细信息

每个 PostgreSQL 超大规模服务器组节点必须具有以下最低资源请求：
- 内存：256Mi
- 核心数：1

创建的每个 PostgreSQL 超大规模服务器组协调器或辅助角色盒都有三个容器：
|容器名称|CPU 请求|内存请求|CPU 限制|内存限制|注意|
|---|---|---|---|---|---|
|fluentbit|100m|100Mi|未指定|未指定|Fluentbit 容器资源请求是针对 PostgreSQL 超大规模服务器组节点指定的请求的 _补充_ 。|
|postgres|已指定或未指定用户。|用户指定或 256Mi (默认) 。|已指定或未指定用户。|已指定或未指定用户。||
|telegraf|未指定|未指定|未指定|未指定||

## <a name="cumulative-sizing"></a>累计大小调整

启用 Azure Arc 的数据服务所需的环境的总大小主要是要创建的数据库实例的数量和大小的函数。  整体大小可能很难提前预测，知道实例数将增加和减少，每个数据库实例所需的资源量将更改。

给定的启用了 Azure Arc 的数据服务环境的基线大小是需要4个内核和 16 GB RAM 的数据控制器的大小。  从此处可以添加数据库实例所需的核心和内存的累计总计。  对于 SQL 托管实例，pod 的数目等于创建的 SQL 托管实例的数目。  对于 PostgreSQL 超大规模服务器组，pod 数等效于辅助角色节点数和协调器节点1。  例如，如果你有一个具有3个工作节点的 PostgreSQL 服务器组，则盒总人数将为4。

除了你为每个数据库实例请求的内核和内存之外，还应为代理容器添加250m 核心和 250Mi RAM。

下面是一个示例调整大小计算。

要求：

- **"SQL1"**：1个具有 16 GB RAM 的 SQL 托管实例，4个内核
- **"SQL2"**：1个包含 256 GB RAM 的 SQL 托管实例，16个内核
- **"Postgres1"**： 1 PostgreSQL 超大规模服务器组，其中4个辅助角色位于 12 GB RAM，4核

大小调整计算：

- "SQL1" 的大小为： 1 pod * ( [16 Gi RAM，4个内核] + [250Mi RAM，250m 个核心] ) 对于每个 pod 的代理 = 16.25 Gi RAM，4.25 核。
- "SQL2" 的大小为： 1 pod * ( [256 Gi RAM，16个内核] + + [250Mi RAM，250m 个核心] ) 用于每个 pod 的代理数 = 256.25 Gi RAM，16.25 核。
- SQL 1 和 SQL 2 的总大小为： (16.25 GB + 256.25 Gi) = 272.5 GB RAM， (4.25 内核 + 16.25 内核) = 20.5 内核。

- "Postgres1" 的大小为： (4 个工作线程箱 + 1 协调器 pod) * ( [12 GB RAM，4个内核] + [250Mi RAM，250m 个内核] ) 对于每个 pod 的代理 = 61.25 GB RAM，21.25 核。

- 数据库实例所需的总容量为： 272.5 GB RAM、20.5 核（适用于 SQL + 61.25 GB RAM）、21.25 核心 for PostgreSQL 超大规模 = 333.75 GB RAM、42.5 内核。

- 数据库实例和数据控制器所需的总容量为： 333.75 GB RAM，42.5 核用于数据库实例 + 16 GB RAM，4核用于数据控制器 = 349.75 GB RAM，46.5 核心。

有关存储大小的详细信息，请参阅 [存储配置一](storage-configuration.md) 文。

## <a name="other-considerations"></a>其他注意事项

请记住，对于核心或 RAM，给定的数据库实例大小请求不能超过群集中 Kubernetes 节点的可用容量。  例如，如果 Kubernetes 群集中的最大 Kubernetes 节点为 256 GB RAM 和24个内核，将无法创建请求 512 GB RAM 和48内核的数据库实例。  

最好在 Kubernetes 节点中保持至少25% 的可用容量，以允许 Kubernetes 高效地计划要创建的 pod，并允许弹性缩放和需要更长的长期增长。  

在调整大小的计算中，请不要忘记添加 Kubernetes 系统箱的资源要求，以及可能在同一 Kubernetes 群集上与启用了 Azure Arc 的数据服务共享容量的任何其他工作负荷。

若要在计划的维护和灾难持续性期间保持高可用性，你应该规划群集中至少有一个 Kubernetes 节点在任何给定时间点都无法使用。  Kubernetes 将尝试重新计划在指定节点上运行的、用于维护或由于故障而停止的 pod。  如果剩余的节点上没有可用的容量，则在重新创建可用容量之前，将不会重新计划创建此类。  大数据库实例应特别小心。  例如，如果只有一个 Kubernetes 节点大到足以满足大型数据库实例的资源需求，并且该节点发生故障，则 Kubernetes 将无法将该数据库实例 pod 计划到另一个 Kubernetes 节点。

请记住 [Kubernetes 群集大小的最大限制](https://kubernetes.io/docs/setup/best-practices/cluster-large/) 。

你的 Kubernetes 管理员可能已在命名空间/项目上设置了 [资源配额](https://kubernetes.io/docs/concepts/policy/resource-quotas/) 。  在计划数据库实例大小时，请记住这一点。
