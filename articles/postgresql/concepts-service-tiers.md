---
title: "Azure Database for PosgreSQL 中的服务层 | Microsoft Docs"
description: "Azure Database for PosgreSQL 中的服务层"
services: postgresql
author: kamathsun
ms.author: sukamat
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: postgresql-database
ms.tgt_pltfrm: portal
ms.topic: article
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 23a93060697bc48084658bbbd895d13fd393b07b
ms.contentlocale: zh-cn
ms.lasthandoff: 05/10/2017

---
# <a name="azure-database-for-postgresql-options-and-performance-understand-whats-available-in-each-service-tier"></a>Azure Database for PostgreSQL 选项和性能：了解每个服务层提供的功能

在预览中，Azure Database for PostgreSQL 仅提供基本服务层和标准服务层。 高级服务层尚不可用。

每个服务层具有多个性能级别，可处理不同类型的工作负荷要求。 更高的性能级别提供更多的资源，旨在逐级提高吞吐量。 可在应用程序不中断的情况下动态更改服务层内的性能级别。

将来可能从某一服务层升级或降级到另一服务层。

> [!IMPORTANT]
> 该服务当前仅提供公共预览版，因此尚不提供服务级别协议 (SLA)。

可以使用服务层中具有特定性能级别的专用资源创建单个 PostgreSQL 服务器。 然后可在服务器中创建一个或多个数据库，资源可在此服务器中的多个数据库之间共享。 单个 PostgreSQL 服务器可用的资源以计算单元和存储单位表示。 有关计算单元和存储的详细信息，请参阅[说明计算单元和存储单元](concepts-compute-unit-and-storage.md)

## <a name="choosing-a-service-tier"></a>选择服务层

下表提供了最适用于不同应用程序工作负荷的层的示例。

| 服务层 | 目标工作负荷 |
| :----------- | :----------------|
| 基本 | 最适合需要在无 IOPS 保证下进行可缩放计算和存储的小型工作负荷。 示例包括用于开发或测试的服务器，或不常使用的小型应用程序。 |
| 标准 | 非常适合需要 IOPS 保证，能够独立扩展至更高计算和存储性能以实现高吞吐量的云应用程序。 应用示例包括 Web 或分析应用程序。 |
| 高级 | 最适合事务和 IO 需要很短延迟同时需要高 IO 和工作负荷吞吐量的工作负荷。 对许多并发用户提供最佳支持。 适用于支持关键应用程序的数据库。<br />高级服务层尚未提供预览版。 |
| &nbsp; | &nbsp; |

要确定服务层，首先请确定工作负荷是否需要 IOPS 保证。 然后确定需要的一些最低功能：

| **服务层功能** | **基本** | **标准** | 高级 * |
| :------------------------ | :-------- | :----------- | :------------ |
| 最大计算单元数 | 100 | 2000 | 不适用于预览版 |
| 最大总存储 | 1050 GB | 10000 GB | 不适用于预览版 |
| 存储 IOPS 保证 | 不适用 | 是 | 不适用于预览版 |
| 最大存储 IOPS | 不适用 | 30,000 | 不适用于预览版 |
| 数据库备份保留期 | 7 天 | 35 天 | 35 天 |
| &nbsp; | &nbsp; | &nbsp; | &nbsp; |

> [!NOTE]
> 预览版中的标准服务层当前最多支持 800 个计算单元，最大支持 1000 GB 存储。

确定最低服务层后，即可开始确定 PostgreSQL 服务器的性能级别（计算单元数）。 对于Web 和分析型工作负荷需要更高用户并发的应用程序，标准型 200 和 400 个计算单元通常为适用的起始值。 

但是，可根据工作负荷要求独立于存储单位来增加或减少计算单元数。 如果工作负荷需要调整计算资源，可动态增加或降低计算单元数。 如果工作负荷需要更多 IOPS 或存储，则还可以扩展存储。

> [!NOTE]
> 在预览版中，基本层和标准层当前不支持动态缩放存储。 我们计划在将来添加此功能。

> [!NOTE]
> 标准服务层中，IOPS 相对预配的存储大小按 3:1 的固定比例缩放。 所含的 125 GB 存储保证预配 IOPS 为 375，其中每个 IO 的大小最多可达 256 KB。 如果预配 1000 GB，预配 IOPS 将为 3000。 必须监视服务器计算单元使用情况并进行扩展，以充分利用可用的预配 IOPS。

## <a name="service-tiers-and-performance-levels"></a>服务层和性能级别

Azure Database for PostgreSQL 在每个服务层内提供多个性能级别。 通过使用如下其中一种方式，你可以灵活选择最能满足具体工作负荷需求的级别：

- [Azure 门户](/azure/azure-portal-overview)（网址为 [http://portal.azure.com](http://portal.azure.com)）
- [Azure CLI](quickstart-create-server-database-azure-cli.md)

数据库与每个 PostgreSQL 服务器中托管的数据库数量无关，可获得稳定的一组资源，且服务器预期性能特征不受影响。

基本服务层：

| **性能级别** | **50** | **100** |
| :-------------------- | :----- | :------ |
| 最大计算单元数 | 50 | 100 |
| 所含存储大小 | 50 GB | 50 GB |
| 最大服务器存储大小\* | 1050 GB | 1050 GB |
| 最大并发登录数 | &nbsp; | &nbsp; |
| 最大连接数 | &nbsp; | &nbsp; |
| &nbsp; | &nbsp; | &nbsp; |

标准服务层：

| **性能级别** | **100** | **200** | **400** | **800** |
| :-------------------- | :------ | :------ | :------ | :------ |
| 最大计算单元数 | 100 | 200 | 400 | 800 |
| 所含存储大小和预配 IOPS | 125 GB，375 IOPS | &nbsp; | &nbsp; | &nbsp; |
| 最大服务器存储大小\* | 1 TB | &nbsp; | &nbsp; | &nbsp; |
| 服务器预配 IOPS 的最大值 | 3000 IOPS | &nbsp; | &nbsp; | &nbsp; |
| 服务器预配 IOPS 的最大值/GB | 固定为 3 IOPS/GB | &nbsp; | &nbsp; | &nbsp; |
| 最大并发登录数 | &nbsp; | &nbsp; | &nbsp; | &nbsp; |
| 最大连接数 | &nbsp; | &nbsp; | &nbsp; | &nbsp; |
| &nbsp; | &nbsp; | &nbsp; | &nbsp; | &nbsp; |

\*最大服务器存储大小指服务器的最大预配存储大小。

## <a name="scaling-up-or-down-a-single-server"></a>增加或减少一台服务器

初始选择服务层和性能级别后，可根据工作负荷要求动态地增加或减少服务器。 如果需要进行增减，则可使用 Azure 门户或 Azure CLI 轻松更改数据库的服务层。

更改数据库的服务层和/或性能级别将在新的性能级别创建原始数据库的副本，然后将连接切换到副本。 当我们切换到副本时，在此过程中不会丢失任何数据，但在短暂的瞬间，将禁用与数据库的连接，因此可能回滚某些处于进行状态的事务。 此窗口会有所不同，但平均起伏少于 4 秒，而在超过 99%的情况下为不足 30 秒。 如果有很大数量的事务在连接禁用的瞬间正在进行中，则此窗口停留时间可能会更长。

整个扩展过程的持续时间同时取决于更改前后服务器的大小和服务层。 例如，如果服务器要将计算单元更改为标准服务层、从标准服务层更改计算单元或在标准层内更改计算单元，则应该在几分钟内完成。 更改完成之前不会应用服务器的新属性。

### <a name="documentation-about-the-steps-for-scaling-up-or-down"></a>有关增减步骤的文档

- [在 Azure 门户中管理单个服务器](quickstart-create-server-database-portal.md)
- [使用 Azure CLI 管理单个数据库](quickstart-create-server-database-azure-cli.md)

### <a name="details-about-scaling-up-or-down"></a>有关增减的详细信息

- 若要对数据库进行降级，服务器存储单元应小于目标服务层允许的最大大小。
- 各服务层提供的还原服务各不相同。 如果进行降级，你可能无法再还原到某个时间点，或者备份保留期变短。 有关详细信息，请参阅[如何使用 Azure 门户备份和还原 Azure Database for PostgreSQL 服务器](howto-restore-server-portal.md)
- 更改完成之前不会应用服务器的新属性。

