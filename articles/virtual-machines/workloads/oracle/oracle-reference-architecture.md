---
title: Azure 上的 Oracle 数据库的参考体系结构 |Microsoft Docs
description: 引用用于在 Microsoft Azure 虚拟机上运行 Oracle Database Enterprise Edition 数据库的体系结构。
services: virtual-machines-linux
author: romitgirdhar
manager: gwallace
tags: ''
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 12/13/2019
ms.author: rogirdh
ms.custom: ''
ms.openlocfilehash: 235482f5d44877e5c4e47aed4d7eaf2baea5c3fd
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/31/2019
ms.locfileid: "75560328"
---
# <a name="reference-architectures-for-oracle-database-enterprise-edition-on-azure"></a>Azure 上的 Oracle Database Enterprise Edition 的参考体系结构

本指南详细介绍了如何在 Azure 上部署高可用性 Oracle 数据库。 此外，本指南还深入灾难恢复注意事项。 这些体系结构是基于客户部署创建的。 本指南仅适用于 Oracle Database Enterprise Edition。

如果有兴趣了解有关最大程度地提高 Oracle 数据库性能的详细信息，请参阅[结构设计师 Oracle DB](oracle-design.md)。

## <a name="assumptions"></a>假设

- 你了解 Azure 的不同概念，如[可用性区域](../../../availability-zones/az-overview.md)
- 正在运行 Oracle Database Enterprise Edition 12c 或更高版本
- 使用本文中的解决方案时，你会注意到并确认许可意义

## <a name="high-availability-for-oracle-databases"></a>Oracle 数据库的高可用性

在云中实现高可用性是每个组织规划和设计的重要组成部分。 Microsoft Azure 提供[可用性区域](../../../availability-zones/az-overview.md)和可用性集（在可用性区域不可用的区域中使用）。 详细了解如何[管理虚拟机的可用性](../../../virtual-machines/linux/manage-availability.md)，以便为云设计。

除了云本机工具和产品/服务，Oracle 还提供了高可用性解决方案，如[Oracle 数据防护](https://docs.oracle.com/en/database/oracle/oracle-database/18/sbydb/introduction-to-oracle-data-guard-concepts.html#GUID-5E73667D-4A56-445E-911F-1E99092DD8D7)、具有可在 Azure 上设置的 FSFO、[分片](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/admin/sharding-overview.html)和[GoldenGate](https://www.oracle.com/middleware/technologies/goldengate.html)的[数据防护](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dgbkr/index.html)。 本指南介绍其中每个解决方案的参考体系结构。

最后，在为云迁移或创建应用程序时，请务必调整应用程序代码以添加云本机模式，如[重试模式](https://docs.microsoft.com/azure/architecture/patterns/retry)和[断路器模式](https://docs.microsoft.com/azure/architecture/patterns/circuit-breaker)。 [云设计模式指南](https://docs.microsoft.com/azure/architecture/patterns/)中定义的其他模式可帮助您的应用程序更具弹性。

### <a name="oracle-rac-in-the-cloud"></a>云中的 Oracle RAC

Oracle 真正应用程序群集（RAC）是 Oracle 的一种解决方案，可帮助客户通过多个实例访问一个数据库存储（共享-所有体系结构模式）来实现高吞吐量。 尽管 Oracle RAC 还可以用于本地高可用性，但不能单独使用 Oracle RAC 来实现云中的高可用性，因为它仅针对实例级别的故障，而不是针对机架级别或数据中心级故障提供保护。 出于此原因，Oracle 建议将 Oracle 数据防护与数据库（无论是单一实例还是 RAC）配合使用以实现高可用性。 客户通常需要使用高 SLA 来运行其任务关键型应用程序。 Oracle RAC 当前未通过 Azure 的 Oracle 认证或支持。 但是，Azure 提供诸如 Azure 等功能可用性区域和计划内维护时段，以帮助防范实例级故障。 除此之外，客户还可以使用 Oracle 数据防护、Oracle GoldenGate 和 Oracle 分片等技术来实现高性能和 resiliancy，方法是保护其数据库不受机架级别的影响，以及数据中心级别和异地政治故障。

当跨多个[可用性区域](https://docs.microsoft.com/azure/availability-zones/az-overview)和 Oracle Data Guard 或 GoldenGate 运行 oracle 数据库时，客户可以获得99.99% 的运行时间 SLA。 在可用性区域尚不存在的 Azure 区域中，客户可使用[可用性集](https://docs.microsoft.com/azure/virtual-machines/linux/manage-availability#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy)并获得99.95% 的运行时间 SLA。

>注意：你的运行时间目标可能远远高于 Microsoft 提供的运行时间 SLA。

## <a name="disaster-recovery-for-oracle-databases"></a>Oracle 数据库的灾难恢复

在云中托管任务关键型应用程序时，为实现高可用性和灾难恢复设计至关重要。

对于 Oracle Database Enterprise Edition，Oracle Data Guard 是用于灾难恢复的一项有用功能。 可以在[配对的 Azure 区域](/azure/best-practices-availability-paired-regions)中设置备用数据库实例，并设置数据防护故障转移以实现灾难恢复。 对于零数据丢失，建议除活动数据防护外，还部署 Oracle Data Guard 同步实例。 

如果你的应用程序允许延迟，请考虑在不同的可用性区域（如果你的应用程序允许彻底的测试）内设置数据防护远同步实例。 使用**最大可用性**模式来设置重做文件到最远同步实例的同步传输。 然后，这些文件将异步传输到备用数据库。 

如果你的应用程序在**最大可用性**模式（同步）的另一个可用性区域中设置远端同步实例时不允许性能降低，你可以在与主数据库相同的可用性区域中设置一个远同步实例。 为了提高可用性，请考虑将多个远距离的同步实例设置为接近于主数据库，并将至少一个实例设置到备用数据库附近（如果角色转换）。 阅读此[Oracle 活动数据防护远同步白皮书](https://www.oracle.com/technetwork/database/availability/farsync-2267608.pdf)中的有关 Oracle 数据防护的详细信息。

使用 Oracle Standard Edition 数据库时，可以使用 DBVisit 备用这样的 ISV 解决方案来设置高可用性和灾难恢复。

## <a name="reference-architectures"></a>参考体系结构

### <a name="oracle-data-guard"></a>Oracle 数据防护

Oracle Data Guard 确保了企业数据的高可用性、数据保护和灾难恢复功能。 数据防护维护备用数据库作为主数据库的事务一致性副本。 根据主数据库和辅助数据库之间的距离以及应用程序对延迟的容差，可以设置同步或异步复制。 然后，如果主数据库因计划内或计划外的故障而不可用，则数据防护可以将任何备用数据库切换为主要角色，从而最大程度地减少停机时间。 

使用 Oracle 数据防护时，还可以打开辅助数据库以实现只读目的。 此配置称为活动数据防护。 Oracle Database 12c 引入了一项称为 "数据防护远同步" 实例的功能。 此实例允许您设置 Oracle 数据库的零数据丢失配置，而不会影响性能。

> [!NOTE]
> 活动数据防护需要额外的许可。 使用 Far 同步功能也需要此许可证。 请与您的 Oracle 代表联系以讨论许可的含义。

#### <a name="oracle-data-guard-with-fsfo"></a>Oracle Data Guard with FSFO
使用快速启动故障转移（FSFO）的 Oracle Data Guard 可以通过在单独的计算机上设置代理来提供额外的复原能力。 Data Guard broker 和辅助数据库都运行观察程序，并观察主数据库的停机时间。 这也允许在 Data Guard 观察程序设置中提供冗余。 

在 Oracle Database 版本12.2 及更高版本中，还可以使用单个 Oracle 数据防护代理配置来配置多个观察程序。 如果一个观察程序和辅助数据库遇到停机时间，则此设置可提供额外的可用性。 Data Guard Broker 是轻型的，可以托管在相对较小的虚拟机上。 若要详细了解 Data Guard Broker 及其优点，请访问本主题的[Oracle 文档](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dgbkr/oracle-data-guard-broker-concepts.html)。

下图是在 Azure 上使用具有可用性区域的 Oracle 数据防护的建议体系结构。 此体系结构允许获得99.99% 的 VM 运行时间 SLA。

![Oracle Database 将可用性区域与 Data Guard Broker 一起使用-FSFO](./media/oracle-reference-architecture/oracledb_dg_fsfo_az.png)

在上图中，客户端系统通过 web 向 Oracle 后端访问自定义应用程序。 Web 前端是在负载均衡器中配置的。 Web 前端向适当的应用程序服务器进行调用以处理工作。 应用程序服务器查询主 Oracle 数据库。 Oracle 数据库已使用超线程的[内存优化虚拟机](../../../virtual-machines/windows/sizes-memory.md)进行配置，该虚拟机具有[受约束的核心个 vcpu](../../../virtual-machines/windows/constrained-vcpu.md) ，可节省许可成本并最大限度地提高性能。 多个高级磁盘或超磁盘（托管磁盘）用于性能和高可用性。

Oracle 数据库放置在多个可用性区域中以实现高可用性。 每个区域由一个或多个数据中心组成，这些数据中心配备了独立电源、冷却和网络。 为了确保复原能力，在所有启用的区域中至少设置了三个单独的区域。 区域内可用性区域的物理分离可防止数据发生数据中心故障。 此外，在两个可用性区域之间设置两个 FSFO 观察程序，以便在发生故障时启动数据库并将数据库故障转移到辅助区域。 

您可以在不同的可用性区域（本例中为 AZ 1，在本例中）中设置其他观察者和/或备用数据库，而不是在前面的体系结构中显示的区域。 最后，oracle 数据库监视 Oracle Enterprise Manager （OEM）的运行时间和性能。 使用 OEM 可以生成多种性能和使用情况报告。

在可用性区域不受支持的区域中，你可以使用可用性集以高度可用的方式部署你的 Oracle Database。 可用性集允许你实现99.95% 的 VM 运行时间。 下图是此用法的参考体系结构：

![Oracle Database 将可用性集与 Data Guard Broker 一起使用-FSFO](./media/oracle-reference-architecture/oracledb_dg_fsfo_as.png)

> [!NOTE]
> * 不需要将 Oracle Enterprise Manager VM 放置在可用性集中，因为只会部署 OEM 的一个实例。
> * 当前不支持在可用性集配置中使用超磁盘。

#### <a name="oracle-data-guard-far-sync"></a>Oracle 数据防护远同步

Oracle 数据防护远同步为 Oracle 数据库提供零数据丢失保护功能。 此功能允许您在数据库计算机出现故障时防止数据丢失。 Oracle 数据防护远同步需要安装在单独的 VM 上。 远同步是一种轻型 Oracle 实例，只包含一个控制文件、密码文件、spfile 和备用日志。 没有数据文件或 rego 日志文件。 

如果没有数据丢失保护，则主数据库和远端同步实例之间必须存在同步通信。 远同步实例以同步方式从主副本接收重做，并以异步方式将其立即转发到所有备用数据库。 此设置还将减少主数据库上的开销，因为它只需将重做发送到远端同步实例，而不是所有备用数据库。 如果同步实例发生故障，数据防护会自动使用从主数据库到辅助数据库的异步传输，以维持近乎零的数据丢失保护。 为了增加复原能力，客户可以为每个数据库实例（主数据库和辅助数据库）部署多个远同步实例。

下图是使用 Oracle Data Guard 同步的高可用性体系结构：

![使用可用性区域和数据防护 & Broker 进行同步的 Oracle 数据库 Broker-FSFO](./media/oracle-reference-architecture/oracledb_dg_fs_az.png)

在前面的体系结构中，将在与数据库实例相同的可用性区域中部署一个远距离的同步实例，以减少这两者之间的延迟。 在应用程序受延迟影响的情况下，请考虑在[邻近的放置组](../../../virtual-machines/linux/proximity-placement-groups.md)中部署数据库和远距离的实例。

下图是使用 Oracle Data Guard FSFO 和 Far 同步实现高可用性和灾难恢复的体系结构：

![Oracle Database 使用可用性区域进行灾难恢复，使用数据防护远同步 & Broker-FSFO](./media/oracle-reference-architecture/oracledb_dg_fs_az_dr.png)

### <a name="oracle-goldengate"></a>Oracle GoldenGate

GoldenGate 在企业的多个异类平台之间实现数据的交换和操作。 它将提交的事务的事务完整性与现有基础结构的开销降至最低。 它的模块化体系结构使您可以灵活地在各种拓扑中提取并复制所选的数据记录、事务性更改和对 DDL （数据定义语言）的更改。

Oracle GoldenGate 允许您通过提供双向复制来配置数据库的高可用性。 这允许设置**多主** **配置或主动-主动配置**。 以下关系图是 Azure 上 Oracle GoldenGate 主动-主动设置的建议体系结构。 在下面的体系结构中，Oracle 数据库已使用超线程的[内存优化虚拟机](../../../virtual-machines/windows/sizes-memory.md)进行了配置，其中包含[受约束的核心个 vcpu](../../../virtual-machines/windows/constrained-vcpu.md) ，可节省许可成本并最大限度地提高性能。 使用多个高级磁盘或超磁盘（托管磁盘）来实现性能和可用性。

![Oracle Database 将可用性区域与 Data Guard Broker 一起使用-FSFO](./media/oracle-reference-architecture/oracledb_gg_az.png)

> [!NOTE]
> 在可用性区域当前不可用的区域中，可以使用可用性集设置相似的体系结构。

Oracle GoldenGate 的进程（例如提取、抽取和 Replicat）可帮助你将数据从一个 Oracle 数据库服务器异步复制到另一个服务器。 这些进程允许设置双向复制，以确保数据库的高可用性，前提是存在可用性区域级别的停机时间。 在上面的关系图中，提取过程与 Oracle 数据库在同一服务器上运行，而数据抽取和 Replicat 进程在同一可用性区域的单独服务器上运行。 Replicat 进程用于接收其他可用性区域中的数据库中的数据，并将数据提交到其可用性区域中的 Oracle 数据库。 同样，数据抽取过程会将提取进程提取的数据发送到另一个可用性区域中的 Replicat 进程。 

虽然前面的体系结构关系图显示了在单独的服务器上配置的数据抽取和 Replicat 进程，但你可以根据服务器的容量和使用情况在同一服务器上设置所有 Oracle GoldenGate 进程。 请始终查阅 AWR 报表和 Azure 中的指标，以了解服务器的使用模式。

在不同的可用性区域或不同区域中设置 Oracle GoldenGate 双向复制时，务必确保不同组件之间的延迟可用于你的应用程序。 可用性区域和区域之间的延迟可能会有所不同，具体取决于多个因素。 建议你在应用程序层和/或区域中的数据库层之间设置性能测试，以确认它符合应用程序性能要求。

应用层可以在其自身的子网中设置，数据库层可以分隔到其自己的子网中。 如果可能，请考虑使用[Azure 应用程序网关](../../../application-gateway/overview.md)对应用程序服务器之间的流量进行负载均衡。 Azure 应用程序网关是一种强大的 web 流量负载均衡器。 它提供了基于 cookie 的会话相关性，可在同一服务器上保留用户会话，从而最大程度地减少数据库上的冲突。 应用程序网关的替代项为[Azure 负载均衡](../../../load-balancer/load-balancer-overview.md)[器和 Azure 流量管理器](../../../traffic-manager/traffic-manager-overview.md)。

### <a name="oracle-sharding"></a>Oracle 分片

分片是 Oracle 12.2 中引入的一种数据层模式。 它可让你在独立数据库之间对数据进行水平分区和缩放。 这是一个不共享的体系结构，其中每个数据库都托管在专用虚拟机上，这使得除复原能力和可用性增加以外，还可以实现较高的读取和写入吞吐量。 此模式消除了单一故障点，提供故障隔离，并支持滚动升级而无需停机。 一分片或数据中心级故障的停机时间不会影响其他数据中心中其他分片的性能和可用性。 

分片适用于不能承受停机时间的高吞吐量 OLTP 应用程序。 始终保证具有相同分片键的所有行都位于同一分片上，从而提高性能，从而提高性能。 使用分片的应用程序必须具有定义良好的数据模型和数据分发策略（一致的哈希、范围、列表或复合），该策略主要使用分片键（例如*customerId*或*accountNum*）访问数据。 分片还允许存储特定的数据集，使其更接近最终客户，从而帮助你满足性能和合规性要求。

建议复制分片以实现高可用性和灾难恢复。 可以使用 oracle 数据防护或 Oracle GoldenGate 等 Oracle 技术来完成此设置。 复制单元可以是分片、分片的一部分或一组分片。 分片数据库的可用性不受一个或多个分片的中断或减速的影响。 为实现高可用性，可以将备用分片放置在主分片所在的同一可用性区域中。 对于灾难恢复，备用分片可以位于另一个区域。 你还可以在多个区域中部署分片以提供这些区域中的流量。 阅读有关在[Oracle 分片文档](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-high-availability.html)中配置分片数据库的高可用性和复制的详细信息。

Oracle 分片主要包含以下组件。 有关这些组件的详细信息，请参阅[Oracle 分片文档](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-overview.html)：

- **分片目录**-专用 Oracle 数据库，它是所有分片数据库配置数据的持久存储。 所有配置更改（如添加或删除分片、数据的映射和分片数据库中的 Ddl）都在分片目录中启动。 分片目录还包含 SDB 中所有复制表的主副本。 

  分片目录使用具体化视图自动将更改复制到所有分片中的重复表。 分片目录数据库还充当查询协调器，用于处理未指定分片键的多分片查询和查询。 
  
  建议的最佳做法是将 Oracle 数据防护与可用性区域或可用性集配合使用来实现分片目录的高可用性。 分片目录的可用性不会影响分片数据库的可用性。 分片目录中的停机时间只会影响数据防护故障转移完成期间的维护操作和多分片查询。 联机事务继续由 SDB 进行路由和执行，不受目录中断的影响。

- **分片总监**-需要部署在分片所在的每个区域/可用性区域中的轻型服务。 分片控制器是部署在 Oracle 分片的环境中的全局服务管理器。 为实现高可用性，建议在每个可用性区域中部署至少一个分片控制器，其中分片存在于中。 

  最初连接到数据库时，路由信息由分片控制器设置，并为后续请求缓存，绕过分片 director。 使用分片建立会话后，所有 SQL 查询和 Dml 都受支持，并在给定的分片范围内执行。 此路由速度快，并用于执行分片内事务的所有 OLTP 工作负荷。 建议将直接路由用于需要最高性能和可用性的所有 OLTP 工作负荷。 当分片变得不可用时，将自动刷新路由缓存，或对分片拓扑进行更改。 
  
  对于高性能、数据相关的路由，Oracle 建议在访问分片数据库中的数据时使用连接池。 Oracle 连接池、特定于语言的库和驱动程序支持 Oracle 分片。 有关更多详细信息，请参阅[Oracle 分片文档](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-overview.html#GUID-3D41F762-BE04-486D-8018-C7A210D809F9)。

- **全局服务**-全局服务与常规数据库服务类似。 除了数据库服务的所有属性外，全局服务还具有分片数据库的属性，例如客户端与分片之间的区域相关性和复制滞后容差。 只需创建一个全局服务即可在分片数据库中读取/写入数据。 使用 Active Data Guard 并设置分片的只读副本时，可以为只读工作负荷创建另一个 gGobal 服务。 客户端可以使用这些全局服务连接到数据库。

- **分片数据库**-分片数据库是 Oracle 数据库。 每个数据库都是在启用了快速启动故障转移（FSFO）的 Broker 配置中使用 Oracle Data Guard 复制的。 无需在每个分片上设置 Data Guard 故障转移和复制。 这是在创建共享数据库时自动配置和部署的。 如果特定的分片失败，Oracle 共享会自动将数据库连接从主数据库连接到备用数据库。

可以部署和管理具有两个接口的 Oracle 分片数据库： Oracle 企业管理器云控制 GUI 和/或 `GDSCTL` 命令行实用程序。 你甚至可以使用云控制来监视不同的分片的可用性和性能。 `GDSCTL DEPLOY` 命令自动创建分片及其相应的侦听器。 此外，此命令会自动部署管理员指定的用于分片级高可用性的复制配置。

分片数据库的方式有不同：

* 系统管理的分片-使用分区跨分片自动分发
* 用户定义的分片-允许你指定将数据映射到分片，这在存在法规或数据本地化要求时很好地工作
* 复合分片-针对不同_shardspaces_的系统托管和用户定义的分片的组合
* 表子分区-类似于常规分区表。

阅读有关 Oracle 文档中不同[分片方法](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-methods.html)的详细信息。

尽管分片数据库可能会像应用程序和开发人员一样使用单个数据库，但当从非分片数据库迁移到分片数据库时，需要仔细规划，以确定哪些表是重复的，而不是分片。 

重复表存储在所有分片上，而分片表分布在不同的分片中。 建议复制小型和维度表，并为事实数据表分配/分片。 可以使用分片目录作为中心协调器或在每个分片上运行数据抽取，将数据加载到分片数据库中。 详细了解如何[将数据迁移到 Oracle 文档中的分片数据库](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-loading-data.html)。

#### <a name="oracle-sharding-with-data-guard"></a>带有数据保护的 Oracle 分片

Oracle 数据防护可用于分片与系统管理的用户定义的分片方法。

下图是用于 Oracle 分片的参考体系结构，它具有用于每个分片的高可用性的 Oracle Data Guard。 体系结构关系图显示了一个_复合分片方法_。 对于具有不同的数据区域、负载均衡、高可用性、灾难恢复等要求的应用程序，体系结构关系图可能会有所不同，并且可能会使用不同的分片方法。 Oracle 分片可让你满足这些要求，并可通过提供这些选项来进行水平和高效地缩放。 甚至可以使用 Oracle GoldenGate 部署类似的体系结构。

![使用可用性区域与 Data Guard Broker Oracle Database 分片-FSFO](./media/oracle-reference-architecture/oracledb_dg_sh_az.png)

尽管系统管理的分片是最容易配置和管理的，但用户定义的分片或复合分片非常适用于你的数据和应用程序在地理上分散或需要控制复制的情况。每个分片。 

在前面的体系结构中，复合分片用于对数据进行地理分布，并横向扩展应用层。 复合分片是系统管理的分片和用户定义的的组合，因此提供了这两种方法的优点。 在上述方案中，数据首先分片跨区域分隔的多个 shardspaces。 然后，在 shardspace 中的多个分片上通过一致的哈希对数据进行进一步分区。 每个 shardspace 包含多个 shardgroups。 在这种情况下，每个 shardgroup 都有多个分片，是复制的 "单元"。 每个 shardgroup 都包含 shardspace 中的所有数据。 Shardgroups A1 和 B1 是主要 Shardgroups，而 Shardgroups A2 和 B2 均为待机。 您可以选择将单个分片作为复制单元，而不是 shardgroup。

在前面的体系结构中，将在每个可用性区域中部署一个 GSM/分片控制器以实现高可用性。 建议为每个数据中心/区域至少部署一个 GSM/分片控制器。 此外，还会在包含 shardgroup 的每个可用性区域中部署应用程序服务器的实例。 此设置允许应用程序使应用程序服务器和数据库/shardgroup 之间的延迟保持较低。 如果数据库发生故障，则在发生数据库角色转换后，与备用数据库位于同一区域中的应用程序服务器可以处理请求。 Azure 应用程序网关和分片控制器将跟踪请求和响应延迟并相应地路由请求。

从应用程序的角度来看，客户端系统向 Azure 应用程序的网关（或 Azure 中的其他负载平衡技术）发出请求，将请求重定向到客户端最近的区域。 Azure 应用程序网关还支持粘滞会话，因此来自同一客户端的任何请求都将路由到同一应用程序服务器。 应用程序服务器在数据访问驱动程序中使用连接池。 此功能在 JDBC、ODP.NET、OCI 等驱动程序中可用。驱动程序可以识别指定为请求的一部分的分片密钥。 JDBC 客户端的[Oracle 通用连接池（UCP）](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/jjucp/ucp-database-sharding-overview.html)可以使非 oracle 应用程序客户端（如 Apache TOMCAT 和 IIS）能够与 Oracle 分片一起工作。 

在初始请求期间，应用程序服务器将连接到其区域中的分片控制器，以获取需要路由到请求的分片的路由信息。 根据传递的分片键，控制器会将应用程序服务器路由到各自的分片。 应用程序服务器通过构建映射来缓存此信息，对于后续请求，绕过分片 director 并直接将请求路由到分片。

#### <a name="oracle-sharding-with-goldengate"></a>Oracle 分片 with GoldenGate

下图是用于 Oracle 分片的参考体系结构，其中包含 Oracle GoldenGate，适用于每个分片的区域内高可用性。 与上述体系结构不同，此体系结构仅描述单个 Azure 区域（多个可用性区域）中的高可用性。 使用 Oracle GoldenGate 可以部署多区域高可用性分片数据库（类似于前面的示例）。

![使用可用性区域与 GoldenGate Oracle Database 分片](./media/oracle-reference-architecture/oracledb_gg_sh_az.png)

上述参考体系结构使用_系统管理_的分片方法来分片数据。 由于 Oracle GoldenGate 复制是在区块级别完成的，因此，复制到一个分片的数据可以复制到另一个分片。 另一半可以复制到不同的分片。 

复制数据的方式取决于复制因素。 如果复制系数为2，则会在 shardgroup 中的三个分片之间提供每个数据块的两个副本。 同样，在 shardgroup 中，将每个分片中的所有数据复制到 shardgroup 中的每个分片。 Shardgroup 中的每个分片都有不同的复制系数。 此设置可帮助你在 shardgroup 内以及跨多个 shardgroups 有效地定义高可用性和灾难恢复设计。

在前面的体系结构中，shardgroup A 和 shardgroup B 都包含相同的数据，但驻留在不同的可用性区域中。 如果 shardgroup A 和 shardgroup B 的复制因子均为3，则分片表的每个行/块将在两个 shardgroups 之间复制6次。 如果 shardgroup A 的复制因子为3，shardgroup B 的复制因子为2，则每个行/块区将在两个 shardgroups 之间复制5次。

如果发生了实例级或可用性区域级故障，此设置可防止数据丢失。 应用程序层能够读取和写入每个分片。 为了最大限度地减少冲突，Oracle 分片为每个哈希值范围指定一个 "主块"。 此功能可确保将特定块区的写入请求定向到相应的区块。 此外，Oracle GoldenGate 提供自动冲突检测和解决方法来处理可能出现的冲突。 有关使用 Oracle 分片实现 GoldenGate 的详细信息和限制，请参阅 Oracle 相关文档，了解如何[将 Oracle GoldenGate 与分片数据库配合](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-high-availability.html#GUID-4FC0AC46-0B8B-4670-BBE4-052228492C72)使用。

在前面的体系结构中，将在每个可用性区域中部署一个 GSM/分片控制器以实现高可用性。 建议为每个数据中心或区域至少部署一个 GSM/分片控制器。 此外，还会在包含 shardgroup 的每个可用性区域中部署应用程序服务器的实例。 此设置允许应用程序使应用程序服务器和数据库/shardgroup 之间的延迟保持较低。 如果数据库发生故障，则在数据库角色转换后，与备用数据库位于同一区域中的应用程序服务器可以处理请求。 Azure 应用程序网关和分片控制器将跟踪请求和响应延迟并相应地路由请求。

从应用程序的角度来看，客户端系统向 Azure 应用程序的网关（或 Azure 中的其他负载平衡技术）发出请求，将请求重定向到客户端最近的区域。 Azure 应用程序网关还支持粘滞会话，因此来自同一客户端的任何请求都将路由到同一应用程序服务器。 应用程序服务器在数据访问驱动程序中使用连接池。 此功能在 JDBC、ODP.NET、OCI 等驱动程序中可用。驱动程序可以识别指定为请求的一部分的分片密钥。 JDBC 客户端的[Oracle 通用连接池（UCP）](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/jjucp/ucp-database-sharding-overview.html)可以使非 oracle 应用程序客户端（如 Apache TOMCAT 和 IIS）能够与 Oracle 分片一起工作。 

在初始请求期间，应用程序服务器将连接到其区域中的分片控制器，以获取需要路由到请求的分片的路由信息。 根据传递的分片键，控制器会将应用程序服务器路由到各自的分片。 应用程序服务器通过构建映射来缓存此信息，对于后续请求，绕过分片 director 并直接将请求路由到分片。

## <a name="patching-and-maintenance"></a>修补和维护

将 Oracle 工作负荷部署到 Azure 时，Microsoft 将负责所有主机操作系统级修补。 任何计划的操作系统级别的维护将提前传达给客户，以允许客户进行此计划内维护。 从不同时修补两个不同可用性区域的两个服务器。 有关 VM 维护和修补的详细信息，请参阅[管理虚拟机的可用性](../../../virtual-machines/linux/manage-availability.md)。 

使用[Azure 自动化](../../../automation/automation-tutorial-update-management.md)可以自动修补虚拟机操作系统。 修补和维护 Oracle 数据库可以使用[Azure Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines?view=azure-devops)或[Azure 自动化](../../../automation/automation-tutorial-update-management.md)进行自动和计划，以最大程度地减少停机时间。 若要了解如何在 Oracle 数据库的上下文中使用，请参阅[持续交付和蓝色/绿色部署](/azure/devops/learn/what-is-continuous-delivery)。

## <a name="architecture-and-design-considerations"></a>体系结构和设计注意事项

- 请考虑对 Oracle Database VM 使用[受约束核心个 vcpu](../../../virtual-machines/windows/constrained-vcpu.md)的超线程[内存优化虚拟机](../../../virtual-machines/windows/sizes-memory.md)，以节省许可成本，并最大限度地提高性能。 使用多个高级或超磁盘（托管磁盘）实现性能和可用性。
- 使用托管磁盘时，磁盘/设备名称可能会在重新启动时更改。 建议你使用设备 UUID 而不是名称来确保在重新启动后保留你的安装。 可在[此处](../../../virtual-machines/linux/configure-raid.md#add-the-new-file-system-to-etcfstab)找到更多信息。
- 使用可用性区域在区域中实现高可用性。
- 请考虑使用适用于 Oracle 数据库的超磁盘（如果可用）或高级磁盘。
- 请考虑使用 Oracle Data Guard 在另一个 Azure 区域中设置备用 Oracle 数据库。
- 请考虑使用[邻近的放置组](../../../virtual-machines/linux/co-location.md#proximity-placement-groups)来减少应用程序层和数据库层之间的延迟。
- 设置[Oracle 企业管理器](https://docs.oracle.com/en/enterprise-manager/)进行管理、监视和日志记录。
- 请考虑使用 Oracle 自动存储管理（ASM）来简化数据库的存储管理。
- 使用[Azure Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines)可以在不停机的情况下管理对数据库的修补和更新。
- 调整应用程序代码以添加云本机模式，如[重试模式](/azure/architecture/patterns/retry)、[断路器模式](/azure/architecture/patterns/circuit-breaker)，以及可帮助应用程序更具弹性的[云设计模式指南](/azure/architecture/patterns/)中定义的其他模式。

## <a name="next-steps"></a>后续步骤

查看适用于你的方案的以下 Oracle 参考文章。

- [Oracle 数据防护简介](https://docs.oracle.com/en/database/oracle/oracle-database/18/sbydb/introduction-to-oracle-data-guard-concepts.html#GUID-5E73667D-4A56-445E-911F-1E99092DD8D7)
- [Oracle Data Guard Broker 概念](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dgbkr/oracle-data-guard-broker-concepts.html)
- [为主动-主动高可用性配置 Oracle GoldenGate](https://docs.oracle.com/goldengate/1212/gg-winux/GWUAD/wu_bidirectional.htm#GWUAD282)
- [Oracle 分片概述](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-overview.html)
- [Oracle 活动数据防护远无距离同步零个数据](https://www.oracle.com/technetwork/database/availability/farsync-2267608.pdf)
