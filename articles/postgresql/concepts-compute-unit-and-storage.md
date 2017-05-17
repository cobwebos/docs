---
title: "Azure DB for PostgreSQL 计算单元和存储单元的说明 | Microsoft Docs"
description: "Azure DB for PostgreSQL：说明计算单元和存储单元，以及达到最大计算单元或存储单元时会发生什么情况。"
services: postgresql
author: 
ms.author: 
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: postgresql - database
ms.tgt_pltfrm: portal
ms.topic: article
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 0cf1f0cf3d93dac49e75f558dcba8af002a492da
ms.contentlocale: zh-cn
ms.lasthandoff: 05/10/2017

---
# <a name="explaining-compute-unit-and-storage-unit"></a>说明计算单元和存储单元
本文介绍计算单元和存储单元的概念，以及达到最大计算单元或存储单元时会发生什么情况。

## <a name="what-are-compute-units"></a>什么是计算单元？
计算单元是保证单个 Azure Database for PostgreSQL 服务器可用的 CPU 处理吞吐量的一种度量值。 计算单元是 CPU 和内存资源的混合度量值。 通常，50 个计算单元等同于半个核心，100 个计算单元等同于 1 个核心，2000 个计算单元等同于保证服务器可用处理吞吐量的 20 个核心。 

每个计算单元的内存量针对基本、标准和高级服务层进行了优化。 提高性能级别可使计算单元加倍，这等同于使该单个 Azure Database for PostgreSQL 可用的资源集增加一倍。 

例如，比标准 100 个计算单元相比，标准 2000 个计算单元提供超过 20 倍的 CPU 吞吐量和内存。 但是，尽管标准 100 个计算单元与基本 100 个计算单元提供相同的 CPU 吞吐量，但在标准层中预配置的内存量是为基本层配置的内存量的两倍，因此可以提供更好的工作负荷性能和更低的事务延迟。

>[!IMPORTANT]
>对于可预测的工作负荷性能吞吐量和高用户并发性，强烈建议选择标准服务层。

可随时更改[服务层](concepts-service-tiers.md)，而几乎不会造成任何应用程序停机时间。 对于许多企业和应用来说，能够在每个单个 Azure Database for PostgreSQL 服务器中创建一对多数据库，以及按需提升或降低性能提供了管理成本所需的灵活性。

>[!IMPORTANT]
>目前，我们支持在服务层内增加/降低性能级别。 例如，可以从标准 100 个计算单元增加到标准 400 个计算单元。 同样，可以从标准 400 个计算单元降低为标准 100 个计算单元。 能够在服务层之间（例如在基本层和标准层之间）进行增加或降低的功能将在以后提供。

## <a name="what-are-storage-units"></a>什么是存储单元？
存储单元是保证单个 Azure Database for PostgreSQL 服务器可用的已预配存储容量的一种度量值。 每个服务层都有固定的已预配存储量，它包括在所选服务层的价格中。

另外，可以独立于计算单元以 125 GB 的增量扩展存储单元，可增加到下表中所述的最大存储量。
| **服务层功能** | **基本** | **标准** | 高级\* |
|---------------------------|-----------|--------------|---------------|
| 包含的存储单元 | 50 GB | 125 GB | 250 GB |
| 最大总存储 | 1050 GB | 10000 GB | 4000 GB |
| 存储 IOPS 保证 | 不适用 | 是 | 是 |
| 最大存储 IOPS | 不适用 | 30,000 | 40,000 |
标准和高级服务层还提供预配的 IOPS 保证。 可用的预配 IOPS 量取决于服务层和配置的存储容量。 对于在标准层中部署的服务器，IOPS 固定为预配存储量的 3 倍。 

例如，如果具有 125 GB 的预配存储量，则服务器可用的预配 IOPS 为 375。 高级层提供非常低的延迟和高 IOPS 存储。 对于在高级层中部署的服务器，预配的低延迟 IOPS 可扩展为预配存储量的五至十倍。
>[!IMPORTANT]
>如果工作负荷由于配置的计算单元而遭遇瓶颈，则可能无法完全实现可用的预配 IOPS。 如果无法充分利用可用的预配 IOPS，则建议监视计算单元，并考虑缩放计算单元。

通过递增预配的存储量增加存储单元相当于按比例增加标准和高级层的预配 IOPS。

>[!IMPORTANT]
>基本层不提供 IOPS 保证。

## <a name="how-can-i-determine-the-number-of-compute-units-needed-for-my-workload"></a>如何确定工作负荷所需的计算单元数？
如果希望迁移本地现有 PostgreSQL 或在虚拟机上运行的 PostgreSQL，则可以通过估算工作负荷所需的处理吞吐量核心数来确定计算单元数。 

如如果本地或虚拟机当前使用 4 个核心（不计 CPU 超线程），则一开始可以为 Azure Database for PostgreSQL 配置 400 个计算单元。 可以根据工作负荷需求动态增加或减少计算单元，而几乎不会造成任何应用程序停机时间。 还可以通过门户或 CLI 监视计算单元的消耗，以将资源调整为适合 PostgreSQL 服务器的大小。

## <a name="how-can-i-determine-the-number-of-storage-units-needed-for-my-workload"></a>如何确定工作负荷所需的存储单元数？
可以通过首先确定所需的存储容量来估计所需的存储单元数。 基本、标准和高级层附带内置在 SKU 中的所包含的存储。 

第二个重要因素是确定所需的 IOPS。 基本层提供变量 IOPS，而没有任何保证。 标准层以固定比率（三个 IOPS/预配存储的每 GB）缩放，并提供 IOPS 保证。 还可以通过门户或 CLI 来监视预配 IOPS 的消耗，从而确定使用情况。

>[!IMPORTANT]
>预配存储单元后，便无法动态减少存储单元。

## <a name="what-happens-when-i-hit-my-maximum-compute-units-andor-storage-units"></a>达到最大计算单元和/或存储单元时会发生什么情况？
会校准并控制性能级别，以便在选定服务层/性能级别所允许的最大限制范围内提供所需的资源来运行数据库工作负荷。 

如果工作负荷达到了计算单元/预配 IOPS 限制中的其中一个，会继续在所允许的最大级别接收资源，但是，可能会发现查询的延迟在不断增加。 这些限制不会导致任何错误，而只会减慢工作负荷，直到严重变慢，以致于查询开始超时。 

如果已达到允许的最大连接数限制，则将看到显式错误。 请参阅 [Azure Database for PostgreSQL 资源限制](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits)了解有关资源限制的详细信息。 <Need to write about the behavior if a user reaches the storage capacity limits>

## <a name="next-steps"></a>后续步骤
- 请参阅 [Azure Database for PostgreSQL 服务层](./concepts-service-tiers.md)了解有关可用于单个服务器的计算单元和存储单元的信息。


