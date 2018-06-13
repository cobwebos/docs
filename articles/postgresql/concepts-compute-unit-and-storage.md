---
title: Azure Database for PostgreSQL 中计算单元的介绍 | Microsoft Docs
description: Azure DB for PostgreSQL：本文介绍了计算单元的概念，以及工作负荷达到最大计算单元时会发生什么情况。
services: postgresql
author: kamathsun
ms.author: sukamat
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 09/26/2017
ms.openlocfilehash: dbb9f733455fa0492358b24b178c8c637ff08c71
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2018
ms.locfileid: "29678318"
---
# <a name="explaining-compute-units-in-azure-database-for-postgresql"></a>Azure Database for PostgreSQL 中计算单元的介绍
本主题介绍了计算单元的概念，以及在工作负载达到计算单元上限时会发生什么。

## <a name="what-are-compute-units"></a>什么是计算单元？
计算单元是保证单个 Azure Database for PostgreSQL 服务器可用的 CPU 处理吞吐量的一种度量值。 计算单元是 CPU 和内存资源的混合度量值。 一般来说，50 个计算单元等同于半核。 100 个计算单元等同于单核。 2000 个计算单元等同于有 20 个内核的有保证处理吞吐量可供服务器使用。

针对基本和标准定价层对每个计算单元的内存量进行了优化。 提高性能级别可使计算单元加倍，这等同于使该单个 Azure Database for PostgreSQL 可用的 CPU 和内存量增加一倍。

例如，与标准层 100 个计算单元配置相比，标准层 800 个计算单元提供超过 8 倍的 CPU 吞吐量和内存。 不过，尽管标准层 100 个计算单元与基本层 100 个计算单元提供的 CPU 吞吐量相同，但在标准定价层中预配的内存量是为基本定价层配置的内存量的两倍。 因此，虽然选择的计算单元相同，但与基本定价层相比，标准定价层的工作负载性能更高，事务延迟更低。

## <a name="how-can-i-determine-the-number-of-compute-units-needed-for-my-workload"></a>如何确定工作负荷所需的计算单元数？
如果希望对本地或虚拟机上运行的现有 PostgreSQL 服务器进行迁移，则可通过估算工作负荷处理吞吐量所需的内核数来确定计算单元数。 

如果现有的本地或虚拟机服务器目前使用 4 核（不计 CPU 超线程），则一开始可以为 Azure Database for PostgreSQL 服务器配置 400 个计算单元。 可根据工作负荷需求动态增加或减少计算单元，而几乎不会造成任何应用程序停机时间。 

在 Azure 门户中监视指标图或编写 Azure CLI 命令来度量计算单元。 要监视的相关指标是计算单元百分比和计算单元限制。

>[!IMPORTANT]
> 如果发现未最大程度利用存储 IOPS，则可考虑同时监视计算单元利用率。 减少 CPU 或内存限制导致的性能瓶颈，增加计算单元，从而实现提高的 IO 吞吐量。

## <a name="what-happens-when-i-hit-my-maximum-compute-units"></a>达到最大计算单元时会发生什么情况？
性能级别会得到校准和约束，以提供在选定的定价层和性能级别上最大限度地运行数据库工作负载所需的资源。 

如果工作负载达到计算单元或预配 IOPS 的上限，可继续在允许范围内最大限度地利用资源，但查询延迟可能会增加。 这些限制不会导致任何错误，只会减慢工作负荷，直到负荷严重变慢，以致查询超时。 

如果工作负荷达到连接次数上限，则会出现显式错误。 有关资源限制的详细信息，请参阅 [Azure Database for PostgreSQL 中的限制](concepts-limits.md)。

## <a name="next-steps"></a>后续步骤
有关定价层的详细信息，请参阅 [Azure Database for PostgreSQL 定价层](./concepts-service-tiers.md)。
