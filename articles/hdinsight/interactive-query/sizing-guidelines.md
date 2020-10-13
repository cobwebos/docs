---
title: Azure HDInsight 中的 Interactive Query 群集大小调整指南
description: Azure HDInsight 中的 Interactive Query 大小调整指南
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/08/2020
ms.openlocfilehash: a81eff1dcf48996c319933aa4dd46170043b943b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "83663645"
---
# <a name="interactive-query-cluster-sizing-guide-in-azure-hdinsight"></a>Azure HDInsight 中的 Interactive Query 群集大小调整指南

本文档介绍如何调整典型工作负荷的 HDInsight Interactive Query 群集 (LLAP) 大小，以实现合理的性能。 本文档中提供的建议是通用准则，特定的工作负荷可能需要进行特定的调整。

## <a name="default-vm-types-for-interactive-query"></a>Interactive Query 的默认 VM 类型

| 节点类型 | 实例 | 大小 |
|---|---|---|
| Head | D13 v2 | 8 个 VCPU，56-GB RAM，400 GB SSD |
| 辅助角色 | D14 v2 | 16 个 VCPU，112-GB RAM，800 GB SSD |
| ZooKeeper | A4 v2 | 4 个 VCPU，8-GB RAM，40 GB SSD |

## <a name="recommended-configurations"></a>建议的配置

建议的配置值基于 D14 v2 类型的工作器节点。

| 密钥 | 值 | 说明 |
|---|---|---|
| yarn.nodemanager.resource.memory-mb | 102400 (MB) | 为节点上的所有 YARN 容器提供的总内存，以 MB 为单位。 |
| yarn.scheduler.maximum-allocation-mb | 102400 (MB) | RM 上每个容器请求的最大分配 (MB)。 高于此值的内存请求不会生效。 |
| yarn.scheduler.maximum-allocation-vcores | 12 |资源管理器中每个容器请求的最大 CPU 核心数。 高于此值的请求不会生效。 |
| yarn.scheduler.capacity.root.llap.capacity | 90% | LLAP 队列的 YARN 容量分配。  |
| hive.server2.tez.sessions.per.default.queue | number_of_worker_nodes |hive.server2.tez.default.queues 中命名的每个队列的会话数。 此数字对应于查询协调程序 (Tez AM) 的数量。 |
| tez.am.resource.memory.mb | 4096 (MB) | tez AppMaster 使用的内存量 (MB)。 |
| hive.tez.container.size | 4096 (MB) | 指定的 Tez 容器大小 (MB)。 |
| hive.llap.daemon.num.executors | 12 | LLAP 守护程序的执行程序数。 |
| hive.llap.io.threadpool.size | 12 | 执行程序的线程池大小。 |
| hive.llap.daemon.yarn.container.mb | 86016 (MB) | 单个 LLAP 守护程序使用的总内存（每个守护程序的内存），以 MB 为单位。|
| hive.llap.io.memory.size | 409600 (MB) | 在启用了 SSD 缓存的情况下每个 LLAP 守护程序的缓存大小 (MB)。 |
| hive.auto.convert.join.noconditionaltask.size | 2048 (MB) | 用于执行 Map Join 的内存大小，以 MB 为单位。 |

## <a name="llap-daemon-size-estimations"></a>LLAP 守护程序大小估算  

### <a name="yarnnodemanagerresourcememory-mb"></a>yarn.nodemanager.resource.memory-mb

此值表示每个节点上的 YARN 容器使用的最大内存总和，以 MB 为单位。 它指定 YARN 可在此节点上使用的内存量，因此，此值应小于该节点上的内存总量。

此值 = [节点上的物理内存总量] – [OS 和其他服务的内存]。

建议将此值设置为可用 RAM 的大约 90%。 对于 D14 v2，建议的值为 **102400 MB**。

### <a name="yarnschedulermaximum-allocation-mb"></a>yarn.scheduler.maximum-allocation-mb

此值表示资源管理器中每个容器请求的最大分配 (MB)。 高于此指定值的内存请求不会生效。 资源管理器只能以 `yarn.scheduler.minimum-allocation-mb` 为增量为容器分配内存，它不能超过 `yarn.scheduler.maximum-allocation-mb` 指定的大小。 此值不应超过节点的给定内存总量（由 `yarn.nodemanager.resource.memory-mb` 指定）。

对于 D14 v2 工作器节点，建议的值为 **102400 MB**

### <a name="yarnschedulermaximum-allocation-vcores"></a>yarn.scheduler.maximum-allocation-vcores

此配置表示资源管理器中每个容器请求的最大虚拟 CPU 核心数。 如果请求的值高于此配置的值，则请求不会生效。 此配置是 YARN 计划程序的全局属性。 对于 LLAP 守护程序容器，此值可以设置为可用虚拟核心 (VCORE) 总数的 75%。 剩余的 25% 应保留给在工作器节点上运行的 NodeManager、DataNode 和其他服务。  

D14 v2 工作器节点有 16 个 VCORE，可以分配 16 个 VCORE 中的 75%。 因此，对于 LLAP 守护程序容器，建议的值是 **12**。

### <a name="hiveserver2tezsessionsperdefaultqueue"></a>hive.server2.tez.sessions.per.default.queue

此配置值决定了应该针对 `hive.server2.tez.default.queues` 所指定的每个队列并行启动的 Tez 会话数。 该值对应于 Tez AM（查询协调程序）的数量。 建议使用与工作器节点数目相同的值，使每个节点有一个 Tez AM。 Tez AM 数量可以大于 LLAP 守护程序节点的数量。 Tez AM 的主要责任是协调查询执行，并将查询计划片段分配给相应的 LLAP 守护程序来执行。 建议将其数量保留为 LLAP 守护程序节点数量的倍数，以实现更高的吞吐量。  

默认 HDInsight 群集中有四个 LLAP 守护程序在四个工作器节点上运行，因此建议的值为 **4**。  

### <a name="tezamresourcememorymb-hivetezcontainersize"></a>tez.am.resource.memory.mb、hive.tez.container.size

`tez.am.resource.memory.mb` 定义 Tez 应用程序主机大小。  
建议的值为 **4096 MB**。

`hive.tez.container.size` 定义分配给 Tez 容器的内存量。 此值必须设置为 YARN 最小容器大小 (`yarn.scheduler.minimum-allocation-mb`) 与 YARN 最大容器大小 (`yarn.scheduler.maximum-allocation-mb`) 之间。  
建议将其设置为 **4096 MB**。  

考虑到每个容器有一个处理器，通常会使此值小于每个处理器的内存量。 在为 LLAP 守护程序分配内存之前，根据节点上的 Tez AM 数量 `Rreserve` 内存。 例如，如果你为每个节点使用两个 Tez AM（各有 4 GB 内存），请为 LLAP 守护程序分配 90 GB 中的 82 GB，并将剩余的 8 GB 保留给两个 Tez AM。

### <a name="yarnschedulercapacityrootllapcapacity"></a>yarn.scheduler.capacity.root.llap.capacity

此值表示分配给 LLAP 队列的容量百分比。 HDInsights Interactive Query 群集为 LLAP 队列分配总容量的 90%，剩余的 10% 设置给默认队列以进行其他容器分配。  
对于 D14v2 工作器节点，对 LLAP 队列建议的值为 **90**。

### <a name="hivellapdaemonyarncontainermb"></a>hive.llap.daemon.yarn.container.mb

LLAP 守护程序的总内存大小取决于以下组件：

* YARN 容器大小配置（`yarn.scheduler.maximum-allocation-mb`、`yarn.scheduler.maximum-allocation-mb`、`yarn.nodemanager.resource.memory-mb`）

* 执行程序使用的堆内存 (Xmx)

    除去余留空间大小后，该守护程序的可用 RAM 量。  
    对于 D14 v2，HDI 4.0 - 此值为 (86 GB - 6 GB) = 80 GB  
    对于 D14 v2，HDI 3.6 - 此值为 (84 GB - 6 GB) = 78 GB

* 每个守护程序的堆外内存中缓存 (hive.llap.io.memory.size)

* 余留空间

    有一部分堆外内存用于 Java VM 开销（元空间、线程堆栈、GC 数据结构等）。 我们观察到，此内存部分大约为堆大小 (Xmx) 的 6%。 为了安全起见，此值可计算为总 LLAP 守护程序内存大小的 6%。 由于在启用 SSD 缓存后可以做到这一点，因此允许 LLAP 守护程序使用仅供堆使用的所有可用内存中空间。  
    对于 D14 v2，建议的值为 ceil(86 GB x 0.06) ~= **6 GB**。  

每个守护程序的内存 = [内存中缓存大小] + [堆大小] + [余留空间]。

计算公式如下：

每个节点的 Tez AM 内存 = [(Tez AM 数量/LLAP 守护程序节点数量) * Tez AM 大小]。
LLAP 守护程序容器大小 = [YARN 最大容器内存的 90%] – [每个节点的 Tez AM 内存]。

对于 D14 v2 工作器节点，HDI 4.0 - 建议的值为 (90 - (1/1 * 4 GB)) = **86 GB**。
（对于 HDI 3.6，建议的值为 **84 GB**，因为应该为滑块 AM 保留大约 2 GB。）  

### <a name="hivellapiomemorysize"></a>hive.llap.io.memory.size

此配置是可用作 LLAP 守护程序缓存的内存量。 LLAP 守护程序可以使用 SSD 作为缓存。 将 `hive.llap.io.allocator.mmap` 设置为 true 会启用 SSD 缓存。 D14 v2 随附大约 800 GB 的 SSD，默认已为 Interactive Query 群集 (LLAP) 启用了 SSD 缓存。 LLAP 配置为使用 SSD 空间的 50% 作为堆外缓存。

对于 D14 v2，建议的值为 **409600 MB**。  

对于其他 VM，如果未启用 SSD 缓存，分配一部分可用 RAM 作为 LLAP 缓存将有利于提高性能。 按如下所述调整 LLAP 守护程序的总内存大小：  

LLAP 守护程序总内存 = [LLAP 缓存大小] + [堆大小] + [余留空间]。

建议调整缓存大小和堆大小，使其最适合你的工作负荷。  

### <a name="hivellapdaemonnumexecutors"></a>hive.llap.daemon.num.executors

此配置控制每个 LLAP 守护程序可以并行执行任务的执行程序数。 此值是可用 VCORE 数量、为每个执行程序分配的内存量，以及每个 LLAP 守护程序可用的内存总量相减后的余量。 通常，我们希望此值尽可能接近核心数。

对于 D14 v2，有 16 个可用 VCORE，但不可以分配所有 VCORE。 工作器节点还会运行其他服务，例如 NodeManager、DataNode 和指标监视器，这些服务需要一些可用的 VCORE。 此值最大可配置为该节点上可用 VCORE 总数的 75%。

对于 D14 v2，建议的值为 (.75 X 16) = **12**

建议为每个执行程序保留大约 6 GB 的堆空间。 根据可用的 LLAP 守护程序大小和每个节点的可用 VCORE 数量调整执行程序数量。  

### <a name="hivellapiothreadpoolsize"></a>hive.llap.io.threadpool.size

此值指定执行程序的线程池大小。 由于根据指定，执行程序数量是固定的，因此此值与每个 LLAP 守护程序的执行程序数量相同。

对于 D14 v2，建议将此值设置为 **12**。

此配置不能超过 `yarn.nodemanager.resource.cpu-vcores` 值。

### <a name="hiveautoconvertjoinnoconditionaltasksize"></a>hive.auto.convert.join.noconditionaltask.size

请确保启用 `hive.auto.convert.join.noconditionaltask`，使此参数生效。 此配置允许用户指定适合装入内存以执行 Map Join 的表的大小。 如果在 n 向联接中，n-1 个 `tables/partitions` 的大小之和小于配置的值，则会选择 Map Join。 应使用 LLAP 执行程序内存大小来计算要自动转换为 Map Join 的阈值。

对于 D14 v2，建议将此值设置为 **2048 MB**。

我们建议根据你的工作负荷调整此值，因为将此值设置得太小可能无法使用自动转换功能。 将它设置得太大可能会导致 GC 暂停，从而对查询性能产生负面影响。

## <a name="next-steps"></a>后续步骤

* [网关指导原则](gateway-best-practices.md)
* [Apache Tez 内存优化揭秘 - 分步指南](https://community.cloudera.com/t5/Community-Articles/Demystify-Apache-Tez-Memory-Tuning-Step-by-Step/ta-p/245279)
* [LLAP 的 Map Join 内存大小调整](https://community.cloudera.com/t5/Community-Articles/Map-Join-Memory-Sizing-For-LLAP/ta-p/247462)
* [LLAP - 单页体系结构概述](https://community.cloudera.com/t5/Community-Articles/LLAP-a-one-page-architecture-overview/ta-p/247439)
* [深入了解 Hive LLAP](https://community.cloudera.com/t5/Community-Articles/Hive-LLAP-deep-dive/ta-p/248893)
