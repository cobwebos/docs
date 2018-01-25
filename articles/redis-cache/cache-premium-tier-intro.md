---
title: "Azure Redis 缓存高级层简介 | Microsoft Docs"
description: "了解如何为高级层 Azure Redis 缓存实例创建和管理 Redis 持久性、Redis 群集和 VNET 支持"
services: redis-cache
documentationcenter: 
author: wesmc7777
manager: cfowler
editor: 
ms.assetid: 30f46f9f-e6ec-4c38-a8cc-f9d4444856e5
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: wesmc
ms.openlocfilehash: 38a43756678a3628040b1b995966eff6dd9fb363
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/19/2018
---
# <a name="introduction-to-the-azure-redis-cache-premium-tier"></a>Azure Redis 缓存高级层简介
Azure Redis 缓存是一种分布式托管缓存，可提供对数据的超快访问，有助于生成高度可缩放且响应速度快的应用程序。 

新的高级层可供企业立即使用，除了标准层的所有功能以外，它还包括其他优势，例如更好的性能、更大的工作负荷、灾难恢复、导入/导出和增强的安全性。 请继续阅读，以深入了解高级缓存层的其他功能。

## <a name="better-performance-compared-to-standard-or-basic-tier"></a>与标准或基本层相比性能更佳
**性能优于标准或基本层。** 相对于基本级别或标准级别，高级层的缓存部署在处理器速度更快且性能更高的硬件上。 高级级别缓存的吞吐量更高，延迟更低。 

**如果缓存大小相同，高级层中的吞吐量高于标准层。** 例如，就 53 GB 的吞吐量而言，P4（高级层）的缓存是每秒 250K 个请求，相比之下，C6（标准层）只有 150K 个。

有关高级缓存大小、吞吐量和带宽的详细信息，请参阅 [Azure Redis 缓存常见问题](cache-faq.md#what-redis-cache-offering-and-size-should-i-use)

## <a name="redis-data-persistence"></a>Redis 数据持久性
高级层允许将缓存数据暂留在 Azure 存储帐户中。 在基本/标准缓存中，所有数据只存储在内存中。 如果底层基础结构出现问题，可能会导致数据丢失。 我们建议使用高级级别中的 Redis 数据暂留功能来增加灵活性，防止数据丢失。 Azure Redis 缓存提供可在 [Redis 暂留](http://redis.io/topics/persistence)中使用的 RDB 和 AOF（即将推出）选项。 

有关配置暂留的说明，请参阅[如何为高级 Azure Redis 缓存配置暂留](cache-how-to-premium-persistence.md)。

## <a name="redis-cluster"></a>Redis 群集
如果想要创建大于 53 GB 的缓存，或者想要将数据通过分片的方式分散到多个 Redis 节点中，则可以使用在高级层中提供的 Redis 群集功能。 每个节点都包含一个由 Azure 管理的主/副缓存对，目的是提高可用性。 

**Redis 群集可提供最大的缩放能力和吞吐量。** 增加群集中分片（节点）的数量会导致吞吐量线性提高。 例如 如果创建了一个包含 10 个分片的 P4 群集，则可用吞吐量为 250K * 10 = 每秒 250 万个请求。 有关高级缓存大小、吞吐量和带宽的更多详细信息，请参阅 [Azure Redis 缓存常见问题](cache-faq.md#what-redis-cache-offering-and-size-should-i-use)。

若要开始使用群集，请参阅[如何为高级 Azure Redis 缓存配置群集功能](cache-how-to-premium-clustering.md)。

## <a name="enhanced-security-and-isolation"></a>增强的安全性和隔离性
可通过公共 Internet 访问基本或标准层中创建的缓存。 根据访问密钥来限制对缓存的访问。 使用高级层可以进一步确保只有指定网络中的客户端可以访问缓存。 可以在 [Azure 虚拟网络 (VNet)](https://azure.microsoft.com/services/virtual-network/) 中部署 Redis 缓存。 可以使用 VNet 的所有功能，例如子网、访问控制策略和其他功能，进一步限制对 Redis 的访问。

有关详细信息，请参阅[如何为高级 Azure Redis 缓存配置虚拟网络支持](cache-how-to-premium-vnet.md)。

## <a name="importexport"></a>导入/导出
导入/导出是一种 Azure Redis 缓存数据管理操作，可用于通过从高级缓存导入 Redis 缓存数据库 (RDB) 快照以及将 Redis 缓存数据库 (RDB) 快照导出到 Azure 存储帐户中的页 blob 来相应地将数据导入到 Azure Redis 缓存以及从 Azure Redis 缓存导出数据。 这会允许在不同 Azure Redis 缓存实例之间进行迁移，或者在使用之前使用数据填充缓存。

导入可用于从任何云或环境中运行的任何 Redis 服务器引入与 Redis 兼容的 RDB 文件，包括在 Linux、Windows 上运行的 Redis 或任何云提供程序（如 Amazon Web Services 等）。 导入数据是使用预先填充的数据创建缓存的简单方式。 在导入过程中，Azure Redis 缓存从 Azure 存储将 RDB 文件加载到内存中，然后再将密钥插入到缓存中。

使用导出可以将 Azure Redis 缓存中存储的数据导出到与 Redis 兼容的 RDB 文件。 可以使用此功能将一个 Azure Redis 缓存实例中的数据移到另一个 Azure Redis 缓存实例或另一个 Redis 服务器。 在导出过程中，会在托管 Azure Redis 缓存服务器实例的 VM 上创建临时文件，并将该文件上传到指定的存储帐户。 导出操作完成后，无论状态为成功还是失败，都会删除临时文件。

有关详细信息，请参阅[如何在 Azure Redis 缓存中导入和导出数据](cache-how-to-import-export-data.md)。

## <a name="reboot"></a>重新启动
可以根据需要通过高级层重新启动缓存的一个或多个节点。 这可以测试应用程序在故障时的还原能力。 可以重新启动下列节点。

* 缓存的主节点
* 缓存的从节点
* 缓存的主节点和从节点
* 使用高级缓存来执行群集功能时，可以针对缓存中的各个分片重新启动主节点和/或从节点

有关详细信息，请参阅[重新启动](cache-administration.md#reboot)和[重新启动常见问题](cache-administration.md#reboot-faq)。

>[!NOTE]
>现已为所有 Azure Redis 缓存层启动“重新启动”功能。
>
>

## <a name="schedule-updates"></a>计划更新
可以通过计划更新功能指定缓存的维护时段。 指定维护时段后，会在此时段内进行任何 Redis 服务器更新。 要指定维护时段，请选择合适的日期，并指定每天的维护时段开始时间。 请注意，维护时段使用 UTC 时间。 

有关详细信息，请参阅[计划更新](cache-administration.md#schedule-updates)和[计划更新常见问题](cache-administration.md#schedule-updates-faq)。

> [!NOTE]
> 仅在计划的维护时段进行 Redis 服务器更新。 维护时段不适用于 Azure 更新或 VM 操作系统更新。
> 
> 

## <a name="geo-replication"></a>异地复制

异地复制提供一种用于链接两个高级层 Azure Redis 缓存实例的机制。 一个缓存指定为主链接缓存，另一个缓存指定为辅助链接缓存。 辅助链接缓存将变为只读，写入主缓存的数据将复制到辅助链接缓存。 此功能可用于跨 Azure 区域复制缓存。

有关详细信息，请参阅[如何为 Azure Redis 缓存配置异地复制功能](cache-how-to-geo-replication.md)。


## <a name="to-scale-to-the-premium-tier"></a>伸缩到高级层
若要伸缩到高级层，请直接在“更改定价层”边栏选项卡中选择一个高级层。 也可使用 PowerShell 和 CLI 将缓存伸缩到高级层。 有关分步说明，请参阅[如何缩放 Azure Redis 缓存](cache-how-to-scale.md)和[如何自动执行缩放操作](cache-how-to-scale.md#how-to-automate-a-scaling-operation)。

## <a name="next-steps"></a>后续步骤
创建缓存并探索高级层的新功能。

* [如何为高级 Azure Redis 缓存配置暂留](cache-how-to-premium-persistence.md)
* [如何为高级 Azure Redis 缓存配置虚拟网络支持](cache-how-to-premium-vnet.md)
* [如何为高级 Azure Redis 缓存配置群集功能](cache-how-to-premium-clustering.md)
* [如何在 Azure Redis 缓存中导入和导出数据](cache-how-to-import-export-data.md)
* [如何管理 Azure Redis 缓存](cache-administration.md)

