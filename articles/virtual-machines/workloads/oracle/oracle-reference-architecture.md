---
title: Azure 上的 Oracle 数据库的参考体系结构 |微软文档
description: 引用用于在 Microsoft Azure 虚拟机上运行 Oracle 数据库企业版数据库的体系结构。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75560328"
---
# <a name="reference-architectures-for-oracle-database-enterprise-edition-on-azure"></a>Azure 上的 Oracle 数据库企业版的参考体系结构

本指南详细介绍了有关在 Azure 上部署高可用性 Oracle 数据库的信息。 此外，本指南还深入探讨了灾难恢复注意事项。 这些体系结构是根据客户部署创建的。 本指南仅适用于 Oracle 数据库企业版。

如果您有兴趣了解有关最大化 Oracle 数据库性能的详细信息，请参阅[架构师 Oracle 数据库](oracle-design.md)。

## <a name="assumptions"></a>假设

- 您了解 Azure 的不同概念，如[可用性区域](../../../availability-zones/az-overview.md)
- 您正在运行 Oracle 数据库企业版 12c 或更高版本
- 在使用本文中的解决方案时，您了解并承认了许可的含义

## <a name="high-availability-for-oracle-databases"></a>Oracle 数据库的高可用性

实现云的高可用性是每个组织规划和设计的重要组成部分。 Microsoft Azure 提供[可用性区域](../../../availability-zones/az-overview.md)和可用性集（用于不可用的区域）。 详细了解[如何管理虚拟机的可用性](../../../virtual-machines/linux/manage-availability.md)，以便为云进行设计。

除了云原生工具和产品外，Oracle 还提供可在 Azure 上设置的高可用性解决方案，如[Oracle 数据防护](https://docs.oracle.com/en/database/oracle/oracle-database/18/sbydb/introduction-to-oracle-data-guard-concepts.html#GUID-5E73667D-4A56-445E-911F-1E99092DD8D7)、[带 FSFO 的数据防护](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dgbkr/index.html)、[分片](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/admin/sharding-overview.html)和[金门](https://www.oracle.com/middleware/technologies/goldengate.html)。 本指南介绍每个解决方案的参考体系结构。

最后，在为云迁移或创建应用程序时，调整应用程序代码以添加云原生模式（如[重试模式](https://docs.microsoft.com/azure/architecture/patterns/retry)和[断路器模式](https://docs.microsoft.com/azure/architecture/patterns/circuit-breaker)）非常重要。 [云设计模式指南](https://docs.microsoft.com/azure/architecture/patterns/)中定义的其他模式可以帮助您的应用程序更具弹性。

### <a name="oracle-rac-in-the-cloud"></a>云中的 Oracle RAC

Oracle 实际应用程序群集 （RAC） 是 Oracle 的解决方案，通过让多个实例访问一个数据库存储（共享所有体系结构模式）来帮助客户实现高吞吐量。 虽然 Oracle RAC 还可用于本地的高可用性，但仅 Oracle RAC 不能单独用于云中的高可用性，因为它仅可防止实例级故障，而不是机架级或数据中心级故障。 因此，Oracle 建议将 Oracle 数据防护与数据库（无论是单个实例还是 RAC）一起使用，以获得高可用性。 客户通常需要高 SLA 来运行其关键任务应用程序。 Oracle RAC 目前未在 Azure 上获得 Oracle 认证或支持。 但是，Azure 提供可用性区域和计划维护窗口等功能，以帮助防止实例级故障。 除此之外，客户还可以使用 Oracle 数据保护、Oracle 金门和 Oracle 分片等技术，通过保护其数据库免受机架级以及数据中心级和地缘政治故障的影响，从而获得高性能和弹性。

当与 Oracle 数据保护或金门一起跨多个[可用性区域](https://docs.microsoft.com/azure/availability-zones/az-overview)运行 Oracle 数据库时，客户可获得 99.99% 的正常运行时间 SLA。 在尚未存在可用性区域的 Azure 区域中，客户可以使用[可用性集](https://docs.microsoft.com/azure/virtual-machines/linux/manage-availability#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy)，实现 99.95% 的停机时间 SLA。

>注：您可以有一个比 Microsoft 提供的停机时间 SLA 高得多的停机时间目标。

## <a name="disaster-recovery-for-oracle-databases"></a>Oracle 数据库的灾难恢复

在云中托管任务关键型应用程序时，设计高可用性和灾难恢复非常重要。

对于 Oracle 数据库企业版，Oracle 数据防护是灾难恢复的有用功能。 您可以在配对的[Azure 区域](/azure/best-practices-availability-paired-regions)中设置备用数据库实例，并为灾难恢复设置数据防护故障转移。 对于零数据丢失，建议您除了使用活动数据防护之外，还部署 Oracle 数据防护远同步实例。 

如果应用程序允许延迟（需要彻底测试），请考虑在不同于 Oracle 主数据库的可用性区域中设置 Data Guard 远同步实例。 使用**最大可用性**模式设置重做文件的同步传输到远同步实例。 然后，这些文件将异步传输到备用数据库。 

如果应用程序在**最大可用性**模式（同步）中在另一个可用性区域中设置 Far Sync 实例时不允许性能损失，则可以在与主数据库相同的可用性区域中设置 Far Sync 实例。 为了增加可用性，请考虑设置靠近主数据库的多个 Far Sync 实例，以及至少一个靠近备用数据库的实例（如果角色转换）。 在此[Oracle 主动数据卫士远同步白皮书](https://www.oracle.com/technetwork/database/availability/farsync-2267608.pdf)中阅读更多有关 Oracle 数据保护远同步的白皮书。

使用 Oracle 标准版数据库时，有 ISV 解决方案，如 DBVisit 待机，允许您设置高可用性和灾难恢复。

## <a name="reference-architectures"></a>参考体系结构

### <a name="oracle-data-guard"></a>Oracle 数据防护

Oracle 数据防护可确保企业数据的高可用性、数据保护和灾难恢复。 Data Guard 将备用数据库维护为主数据库的事务一致性副本。 根据主数据库和辅助数据库之间的距离以及应用程序对延迟的容差，可以设置同步或异步复制。 然后，如果主数据库由于计划内或计划外停机而不可用，Data Guard 可以将任何备用数据库切换到主角色，从而最大限度地减少停机时间。 

使用 Oracle 数据防护时，您还可以出于只读目的打开辅助数据库。 此配置称为活动数据防护。 Oracle 数据库 12c 引入了一个称为数据卫士远同步实例的功能。 此实例允许您设置 Oracle 数据库的零数据丢失配置，而无需在性能上妥协。

> [!NOTE]
> 活动数据防护需要额外的许可。 使用此许可证也需要使用远同步功能。 请与您的 Oracle 代表联系，讨论许可问题。

#### <a name="oracle-data-guard-with-fsfo"></a>使用 FSFO 的 Oracle 数据防护
具有快速启动故障转移 （FSFO） 的 Oracle 数据防护可以通过在单独的计算机上设置代理来提供额外的恢复能力。 数据防护代理和辅助数据库都运行观察者并观察主数据库的停机时间。 这还允许在数据防护观察者设置中冗余。 

使用 Oracle 数据库版本 12.2 及以上，还可以使用单个 Oracle 数据保护代理配置配置多个观察者。 此设置提供其他可用性，以防一个观察者和辅助数据库遇到停机。 数据防护代理是轻量级的，可以托管在相对较小的虚拟机上。 要了解有关数据卫士代理及其优势的更多信息，请访问有关本主题的[Oracle 文档](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dgbkr/oracle-data-guard-broker-concepts.html)。

下图是一个建议体系结构，用于在 Azure 上使用具有可用性区域的 Oracle 数据防护。 此体系结构允许您获得 99.99% 的 VM 超时 SLA。

![使用可用性区域与数据保护代理 - FSFO 的 Oracle 数据库](./media/oracle-reference-architecture/oracledb_dg_fsfo_az.png)

在上图中，客户端系统通过 Web 使用 Oracle 后端访问自定义应用程序。 Web 前端配置在负载均衡器中。 Web 前端调用相应的应用程序服务器来处理该工作。 应用程序服务器查询主 Oracle 数据库。 Oracle 数据库已使用超线程[内存优化虚拟机](../../../virtual-machines/windows/sizes-memory.md)配置，该虚拟机[具有受限的核心 vCPU，](../../../virtual-machines/windows/constrained-vcpu.md)以节省许可成本并最大限度地提高性能。 多个高级磁盘或超级磁盘（托管磁盘）用于性能和高可用性。

Oracle 数据库被放置在多个可用性区域中，以便获得高可用性。 每个区域由一个或多个数据中心组成，这些数据中心配备独立的电源、冷却和网络。 为确保恢复能力，在所有启用的区域中至少设置了三个单独的区域。 区域内可用性区域的物理分离可保护数据免受数据中心故障的影响。 此外，在两个可用性区域中设置两个 FSFO 观察者，以便在发生中断时启动数据库并将其故障转移到辅助区域。 

您可以在与前一体系结构中显示的区域不同的可用性区域（本例中为 AZ 1）中设置其他观察点和/或备用数据库。 最后，Oracle 企业管理器 （OEM） 监视 Oracle 数据库的停机时间和性能。 OEM 还允许您生成各种性能和使用情况报告。

在不支持可用性区域的区域，您可以使用可用性集以高可用性的方式部署 Oracle 数据库。 可用性集允许您实现 99.95% 的 VM 停机时间。 下图是此用途的参考体系结构：

![使用可用性集与数据保护代理 - FSFO 的 Oracle 数据库](./media/oracle-reference-architecture/oracledb_dg_fsfo_as.png)

> [!NOTE]
> * Oracle 企业管理器 VM 无需放置在可用性集中，因为只有一个正在部署 OEM 的实例。
> * 可用性集配置当前不支持超磁盘。

#### <a name="oracle-data-guard-far-sync"></a>Oracle 数据防护远同步

Oracle 数据防护远同步为 Oracle 数据库提供零数据丢失保护功能。 此功能允许您在数据库计算机发生故障时防止数据丢失。 Oracle 数据防护远同步需要安装在单独的 VM 上。 Far Sync 是一个轻量级的 Oracle 实例，它只有控制文件、密码文件、spfile 和备用日志。 没有数据文件或重新进入日志文件。 

要实现零数据丢失保护，主数据库和远同步实例之间必须同步通信。 Far Sync 实例以同步方式从主数据库接收重做，并立即以异步方式转发到所有备用数据库。 此设置还减少了主数据库的开销，因为它只需将重做发送到远同步实例，而不是所有备用数据库。 如果 Far Sync 实例失败，Data Guard 会自动使用从主数据库到辅助数据库的异步传输来维护接近零的数据丢失保护。 为了增加弹性，客户可以为每个数据库实例（主实例和辅助数据库）部署多个 Far Sync 实例。

下图是使用 Oracle 数据防护远同步的高可用性体系结构：

![使用可用性区域与数据保护远同步&代理 - FSFO 的 Oracle 数据库](./media/oracle-reference-architecture/oracledb_dg_fs_az.png)

在前面的体系结构中，有一个 Far Sync 实例部署在同一可用性区域，以减少两者之间的延迟。 如果应用程序对延迟敏感，请考虑在[接近放置组中](../../../virtual-machines/linux/proximity-placement-groups.md)部署数据库和远同步实例或实例。

下图是利用 Oracle 数据保护 FSFO 和远同步实现高可用性和灾难恢复的体系结构：

![Oracle 数据库使用可用性区域进行灾难恢复，数据卫士远同步&代理 - FSFO](./media/oracle-reference-architecture/oracledb_dg_fs_az_dr.png)

### <a name="oracle-goldengate"></a>Oracle GoldenGate

金门允许在整个企业中多个异构平台之间在交易级别交换和操作数据。 它以事务完整性和现有基础架构上最少的开销移动已提交的事务。 其模块化架构使您能够灵活地跨各种拓扑提取和复制选定的数据记录、事务性更改和对 DDL（数据定义语言）的更改。

Oracle 金门允许您通过提供双向复制来配置数据库，使其具有高可用性。 这允许您设置**多主机**或**主动-主动配置**。 下图是 Azure 上的 Oracle 金门主动设置的建议体系结构。 在以下体系结构中，Oracle 数据库已使用具有[受限核心 vCPU](../../../virtual-machines/windows/constrained-vcpu.md)的超线程[内存优化虚拟机](../../../virtual-machines/windows/sizes-memory.md)进行配置，以节省许可成本并最大限度地提高性能。 多个高级磁盘或超级磁盘（托管磁盘）用于性能和可用性。

![使用可用性区域与数据保护代理 - FSFO 的 Oracle 数据库](./media/oracle-reference-architecture/oracledb_gg_az.png)

> [!NOTE]
> 在可用性区域当前不可用的区域中，可以使用可用性集设置类似的体系结构。

Oracle GoldenGate 具有提取、泵和复制器等进程，可帮助您将数据从一个 Oracle 数据库服务器异步复制到另一台。 这些进程允许您设置双向复制，以确保如果存在可用性区域级停机时间，数据库的高可用性。 在上图中，提取过程与 Oracle 数据库在同一服务器上运行，而数据泵和复制副本进程在同一可用性区域中的单独服务器上运行。 复制进程用于从其他可用性区域中的数据库接收数据，并将数据提交到其可用性区域中的 Oracle 数据库。 同样，数据泵进程将提取过程提取的数据发送到其他可用性区域中的复制过程。 

虽然前面的体系结构图显示了在单独的服务器上配置的数据泵和复制程序进程，但您可以根据服务器的容量和使用情况在同一服务器上设置所有 Oracle GoldenGate 进程。 始终查阅 AWR 报表和 Azure 中的指标，以了解服务器的使用模式。

在不同可用性区域或不同区域中设置 Oracle GoldenGate 双向复制时，请务必确保应用程序可以接受不同组件之间的延迟。 可用性区域和区域之间的延迟可能会有所不同，并取决于多种因素。 建议您在不同可用性区域和/或区域的应用程序层和数据库层之间设置性能测试，以确认它满足应用程序性能要求。

应用程序层可以在自己的子网中设置，数据库层可以分隔成自己的子网。 如果可能，请考虑使用[Azure 应用程序网关](../../../application-gateway/overview.md)来负载应用程序服务器之间的流量。 Azure 应用程序网关是一个健壮的 Web 流量负载均衡器。 它提供基于 Cookie 的会话相关性，使用户会话保留在同一服务器上，从而最大限度地减少数据库上的冲突。 应用程序网关的替代方案是[Azure 负载均衡器](../../../load-balancer/load-balancer-overview.md)和[Azure 流量管理器](../../../traffic-manager/traffic-manager-overview.md)。

### <a name="oracle-sharding"></a>甲骨文分片

分片是 Oracle 12.2 中引入的数据层模式。 它允许您跨独立数据库对数据进行水平分区和缩放。 它是一种无共享的体系结构，每个数据库都托管在专用虚拟机上，除了恢复能力和可用性之外，还可实现高读取和写入吞吐量。 此模式消除了单点故障，提供故障隔离，并支持滚动升级，而不会停机。 一个分片的停机时间或数据中心级故障不会影响其他数据中心中其他分片的性能或可用性。 

分片适用于无法承受任何停机时间的高吞吐量 OLTP 应用程序。 始终保证具有相同分片键的所有行都位于同一分片上，从而提高了提供高一致性的性能。 使用分片的应用程序必须具有定义良好的数据模型和数据分发策略（一致的哈希、范围、列表或复合），这些策略主要使用分片键（例如 *，customerId*或*accountnum）* 访问数据。 分片还允许您将特定数据集存储在更靠近最终客户的位置，从而帮助您满足性能和合规性要求。

建议您复制分片以实现高可用性和灾难恢复。 此设置可以使用 Oracle 数据卫士或 Oracle 金门等 Oracle 技术完成。 复制单元可以是分片、分片的一部分或一组分片。 分片数据库的可用性不受一个或多个分片中断或减速的影响。 为获得高可用性，备用分片可以放置在放置主分片的同一可用性区域中。 对于灾难恢复，备用分片可以位于另一个区域。 您还可以在多个区域部署分片，以为这些区域的流量提供服务。 阅读有关在[Oracle 分片文档中](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-high-availability.html)配置高可用性和复制分片数据库的详细信息。

Oracle 分片主要由以下组件组成。 有关这些组件的详细信息，请参阅 Oracle[分片文档](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-overview.html)：

- **分片目录**- 特殊用途的 Oracle 数据库，是所有分片数据库配置数据的持久存储。 所有配置更改（如添加或删除分片、映射数据）和分片数据库中的 DSL 都在分片目录中启动。 分片目录还包含 SDB 中所有重复表的主副本。 

  分片目录使用具体化视图自动将更改复制到所有分片中的重复表。 分片目录数据库还充当查询协调器，用于处理不指定分片键的多分片查询和查询。 
  
  建议将 Oracle 数据防护与可用性区域或可用性集结合使用，以便分片目录高可用性，这是推荐的最佳做法。 分片目录的可用性不会影响分片数据库的可用性。 分片目录中的停机时间仅影响数据防护故障转移完成的短暂时间段内的维护操作和多分片查询。 联机事务继续由 SDB 路由和执行，并且不受目录中断的影响。

- **分片控制器**- 需要部署在分片驻留在的每个区域/可用性区域的轻量级服务。 分片控制器是在 Oracle 分片上下文中部署的全球服务管理器。 为获得高可用性，建议您在分片存在的每个可用性区域中部署至少一个分片控制器。 

  最初连接到数据库时，路由信息由分片控制器设置，并缓存用于后续请求，绕过分片控制器。 使用分片建立会话后，所有 SQL 查询和 DLL 将支持并在给定分片的范围内执行。 此路由速度很快，用于执行分片内事务的所有 OLTP 工作负载。 建议对所有需要最高性能和可用性的 OLTP 工作负载使用直接路由。 当分片不可用或分片拓扑发生更改时，路由缓存会自动刷新。 
  
  对于高性能、与数据相关的路由，Oracle 建议在访问分片数据库中的数据时使用连接池。 Oracle 连接池、特定于语言的库和驱动程序支持 Oracle 分片。 有关详细信息，请参阅[Oracle 分片文档](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-overview.html#GUID-3D41F762-BE04-486D-8018-C7A210D809F9)。

- **全局服务**- 全局服务类似于常规数据库服务。 除了数据库服务的所有属性外，全局服务还具有分片数据库的属性，如客户端和分片之间的区域相关性以及复制延迟容差。 只需创建一个全局服务，才能将数据从分片数据库读取/写入。 使用活动数据防护并设置分片的只读副本时，可以为只读工作负载创建另一个 gGobal 服务。 客户端可以使用这些全局服务连接到数据库。

- **分片数据库**- 分片数据库是您的 Oracle 数据库。 每个数据库都使用 Oracle 数据保护在启用快速启动故障转移 （FSFO） 的 Broker 配置中进行复制。 您无需在每个分片上设置数据防护故障转移和复制。 创建共享数据库时，将自动配置和部署此数据库。 如果特定分片失败，Oracle 共享会自动通过从主数据库到备用数据库的连接失败。

您可以使用两个接口部署和管理 Oracle 分片数据库：Oracle 企业管理器云控制 GUI 和/`GDSCTL`或命令行实用程序。 您甚至可以使用云控件监视不同分片的可用性和性能。 该`GDSCTL DEPLOY`命令会自动创建分片及其各自的侦听器。 此外，此命令会自动部署用于管理员指定的分片级高可用性的复制配置。

分片数据库的方法不同：

* 系统管理的分片 - 使用分区自动分布在分片之间
* 用户定义的分片 - 允许您指定数据映射到分片，当有法规或数据本地化要求时，这很有效）
* 复合分片 - 不同_分片空间_的系统管理和用户定义的分片的组合
* 表子分区 - 类似于常规分区表。

阅读有关 Oracle 文档中不同[分片方法](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-methods.html)的更多内容。

虽然分片数据库可能看起来像一个数据库的应用程序和开发人员，当从非分片数据库迁移到分片数据库，需要仔细规划，以确定哪些表将被复制或分片。 

重复的表存储在所有分片上，而分片表分布在不同的分片上。 建议复制小表和维度表，并分发/分片实数表。 数据可以使用分片目录作为中央协调器加载到分片数据库中，也可以在每个分片上运行数据泵。 阅读有关将数据迁移到 Oracle 文档中[分片数据库](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-loading-data.html)的详细信息。

#### <a name="oracle-sharding-with-data-guard"></a>使用数据防护的 Oracle 分片

Oracle 数据防护可用于使用系统管理、用户定义和复合分片方法分片。

下图是 Oracle 分片的参考体系结构，用于 Oracle 数据防护，用于每个分片的高可用性。 体系结构图显示了一种_复合分片方法_。 对于对数据局部性、负载平衡、高可用性、灾难恢复等有不同要求的应用程序，体系结构图可能会有所不同，并且可能使用不同的分片方法。 Oracle 分片允许您满足这些要求，并通过提供这些选项水平高效地扩展。 使用 Oracle 金门甚至可以部署类似的体系结构。

![使用可用性区域与数据保护代理 - FSFO 进行 Oracle 数据库分片处理](./media/oracle-reference-architecture/oracledb_dg_sh_az.png)

虽然系统管理的分片是最容易配置和管理的，但用户定义的分片或复合分片非常适合数据和应用程序是地理分布的情况，或者需要控制复制的情况每个分片。 

在前面的体系结构中，复合分片用于地理分布数据并水平横向横向横向扩展应用程序层。 复合分片是系统管理和用户定义的分片的组合，因此提供了这两种方法的好处。 在前面的方案中，数据首先分片跨多个分片空间分隔的区域。 然后，数据通过在分片空间中的多个分片上的一致哈希进一步分区。 每个分片空间包含多个分片组。 每个分片组有多个分片，是复制的"单元"，在这种情况下。 每个分片组都包含分片空间中的所有数据。 分片组 A1 和 B1 是主分片组，而分片组 A2 和 B2 是备用。 您可以选择将单个分片作为复制单元，而不是分片组。

在前面的体系结构中，GSM/分片控制器部署在每个可用性区域，以获得高可用性。 建议每个数据中心/区域至少部署一个 GSM/分片控制器。 此外，应用程序服务器的实例部署在包含分片组的每个可用性区域中。 此设置允许应用程序将应用程序服务器和数据库/分片组之间的延迟保持在较低水平。 如果数据库发生故障，与备用数据库位于同一区域的应用程序服务器可以在数据库角色转换发生后处理请求。 Azure 应用程序网关和分片控制器跟踪请求和响应延迟，并相应地路由请求。

从应用程序的角度来看，客户端系统向 Azure 应用程序网关（或 Azure 中的其他负载平衡技术）发出请求，后者将请求重定向到最接近客户端的区域。 Azure 应用程序网关还支持粘滞会话，因此来自同一客户端的任何请求都路由到同一应用程序服务器。 应用程序服务器在数据访问驱动程序中使用连接池。 此功能在 JDBC、ODP.NET、OCI 等驱动程序中可用。驱动程序可以识别作为请求的一部分指定的分片键。 适用于 JDBC 客户端的[Oracle 通用连接池 （UCP）](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/jjucp/ucp-database-sharding-overview.html)可以使非 Oracle 应用程序客户端（如 Apache Tomcat 和 IIS）与 Oracle 分片配合使用。 

在初始请求期间，应用程序服务器连接到其区域中的分片控制器，以获取有关请求需要路由到的分片的路由信息。 根据传递的分片键，控制器将应用程序服务器路由到相应的分片。 应用程序服务器通过构建地图来缓存此信息，对于后续请求，绕过分片控制器并将请求直接路由到分片。

#### <a name="oracle-sharding-with-goldengate"></a>甲骨文与金门分片

下图是 Oracle 分片与 Oracle 金门的参考体系结构，用于区域内每个分片的高可用性。 与前面的体系结构相反，此体系结构仅描述单个 Azure 区域（多个可用性区域）中的高可用性。 可以使用 Oracle GoldenGate 部署多区域高可用性分片数据库（类似于前面的示例）。

![使用可用性区域与金门的 Oracle 数据库分片](./media/oracle-reference-architecture/oracledb_gg_sh_az.png)

前面的引用体系结构使用_系统管理的_分片方法对数据进行分片。 由于 Oracle GoldenGate 复制是在块级别完成的，因此复制到一个分片的数据的一半可以复制到另一个分片。 另一半可以复制到不同的分片。 

数据复制的方式取决于复制因子。 复制因子为 2 时，将在分片组中的三个分片上具有每个数据块的两个副本。 同样，在分片组中复制因子为 3 和 3 分片时，每个分片中的所有数据都将复制到分片组中的每个其他分片。 分片组中的每个分片可以具有不同的复制因子。 此设置可帮助您在分片组内和跨多个分片组中高效地定义高可用性和灾难恢复设计。

在前面的体系结构中，分片组 A 和分片组 B 都包含相同的数据，但驻留在不同的可用性区域。 如果分片组 A 和分片组 B 具有相同的复制因子 3，则分片表的每个行/块将在两个分片组中复制 6 次。 如果分片组 A 的复制系数为 3，而分片组 B 的复制系数为 2，则每行/块将在两个分片组中复制 5 次。

如果发生实例级或可用性区域级别故障，此设置可防止数据丢失。 应用程序层能够读取和写入每个分片。 为了尽量减少冲突，Oracle 分片为每个哈希值范围指定一个"主块"。 此功能可确保特定块的写入请求定向到相应的块。 此外，Oracle GoldenGate 还提供自动冲突检测和解决，以处理可能出现的任何冲突。 有关使用 Oracle 分片实现金门的更多信息和限制，请参阅 Oracle 有关使用 Oracle[金门与分片数据库](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-high-availability.html#GUID-4FC0AC46-0B8B-4670-BBE4-052228492C72)的文档。

在前面的体系结构中，GSM/分片控制器部署在每个可用性区域，以获得高可用性。 建议每个数据中心或区域至少部署一个 GSM/分片控制器。 此外，应用程序服务器的实例部署在包含分片组的每个可用性区域中。 此设置允许应用程序将应用程序服务器和数据库/分片组之间的延迟保持在较低水平。 如果数据库发生故障，与备用数据库位于同一区域的应用程序服务器可以在数据库角色转换后处理请求。 Azure 应用程序网关和分片控制器跟踪请求和响应延迟，并相应地路由请求。

从应用程序的角度来看，客户端系统向 Azure 应用程序网关（或 Azure 中的其他负载平衡技术）发出请求，后者将请求重定向到最接近客户端的区域。 Azure 应用程序网关还支持粘滞会话，因此来自同一客户端的任何请求都路由到同一应用程序服务器。 应用程序服务器在数据访问驱动程序中使用连接池。 此功能在 JDBC、ODP.NET、OCI 等驱动程序中可用。驱动程序可以识别作为请求的一部分指定的分片键。 适用于 JDBC 客户端的[Oracle 通用连接池 （UCP）](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/jjucp/ucp-database-sharding-overview.html)可以使非 Oracle 应用程序客户端（如 Apache Tomcat 和 IIS）与 Oracle 分片配合使用。 

在初始请求期间，应用程序服务器连接到其区域中的分片控制器，以获取有关请求需要路由到的分片的路由信息。 根据传递的分片键，控制器将应用程序服务器路由到相应的分片。 应用程序服务器通过构建地图来缓存此信息，对于后续请求，绕过分片控制器并将请求直接路由到分片。

## <a name="patching-and-maintenance"></a>修补和维护

将 Oracle 工作负荷部署到 Azure 时，Microsoft 会负责所有主机操作系统级修补。 任何计划的 OS 级维护都提前通知客户，以便客户进行此计划维护。 两个不同的可用区域的两台服务器永远不会同时修补。 有关 VM 维护和修补的更多详细信息[，请参阅管理虚拟机的可用性](../../../virtual-machines/linux/manage-availability.md)。 

修补虚拟机操作系统可以使用[Azure 自动化](../../../automation/automation-tutorial-update-management.md)自动进行。 可以使用[Azure 管道](/azure/devops/pipelines/get-started/what-is-azure-pipelines?view=azure-devops)或[Azure 自动化](../../../automation/automation-tutorial-update-management.md)自动修补和维护 Oracle 数据库，以最大限度地减少停机时间。 请参阅[连续交付和蓝色/绿色部署](/azure/devops/learn/what-is-continuous-delivery)，了解如何在 Oracle 数据库的上下文中使用它。

## <a name="architecture-and-design-considerations"></a>架构和设计注意事项

- 请考虑将超线程[内存优化虚拟机](../../../virtual-machines/windows/sizes-memory.md)与 Oracle 数据库 VM[受限的核心 vCPU](../../../virtual-machines/windows/constrained-vcpu.md)一起使用，以节省许可成本并最大限度地提高性能。 使用多个高级磁盘或超级磁盘（托管磁盘）来提高性能和可用性。
- 使用托管磁盘时，磁盘/设备名称可能会在重新启动时更改。 建议您使用设备 UUID 而不是名称，以确保安装在重新启动期间持续。 可在[此处](../../../virtual-machines/linux/configure-raid.md#add-the-new-file-system-to-etcfstab)找到详细信息。
- 使用可用性区域实现区域内的高可用性。
- 请考虑为 Oracle 数据库使用超磁盘（如果可用）或高级磁盘。
- 请考虑使用 Oracle 数据防护在另一个 Azure 区域中设置备用 Oracle 数据库。
- 请考虑使用[邻近放置组](../../../virtual-machines/linux/co-location.md#proximity-placement-groups)来减少应用程序和数据库层之间的延迟。
- 设置[Oracle 企业管理器](https://docs.oracle.com/en/enterprise-manager/)以进行管理、监视和日志记录。
- 请考虑使用 Oracle 自动存储管理 （ASM） 简化数据库的存储管理。
- 使用[Azure 管道](/azure/devops/pipelines/get-started/what-is-azure-pipelines)管理数据库的修补和更新，而不会有任何停机时间。
- 调整应用程序代码以添加云原生模式，如[重试模式](/azure/architecture/patterns/retry)、[断路器模式](/azure/architecture/patterns/circuit-breaker)，以及[云设计模式指南](/azure/architecture/patterns/)中定义的其他模式，这些模式可能有助于应用程序更具弹性。

## <a name="next-steps"></a>后续步骤

查看以下适用于您的方案的 Oracle 参考文章。

- [Oracle 数据防护简介](https://docs.oracle.com/en/database/oracle/oracle-database/18/sbydb/introduction-to-oracle-data-guard-concepts.html#GUID-5E73667D-4A56-445E-911F-1E99092DD8D7)
- [Oracle 数据保护代理概念](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dgbkr/oracle-data-guard-broker-concepts.html)
- [配置 Oracle 金门，以获得主动-主动高可用性](https://docs.oracle.com/goldengate/1212/gg-winux/GWUAD/wu_bidirectional.htm#GWUAD282)
- [甲骨文分片概述](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-overview.html)
- [Oracle 主动数据防护在任何距离上远同步零数据丢失](https://www.oracle.com/technetwork/database/availability/farsync-2267608.pdf)
