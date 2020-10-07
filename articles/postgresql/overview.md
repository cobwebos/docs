---
title: 什么是 Azure Database for PostgreSQL
description: 在灵活服务器上下文中概述了 Azure Database for PostgreSQL 关系数据库服务。
author: sunilagarwal
ms.author: sunila
ms.custom: mvc
ms.service: postgresql
ms.topic: overview
ms.date: 09/21/2020
ms.openlocfilehash: 5945f50ada9af6a8d117d3d773ebeae48d5f4085
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "90903761"
---
# <a name="what-is-azure-database-for-postgresql"></a>什么是 Azure Database for PostgreSQL？

Azure Database for PostgreSQL 是 Microsoft 云中的关系数据库服务，基于 [PostgreSQL Community Edition](https://www.postgresql.org/)（在 GPLv2 许可证下提供）数据库引擎。 用于 PostgreSQL 的 Azure 数据库提供以下功能：

- 内置的高可用性。
- 使用自动备份和时间点还原对数据进行长达 35 天的保护。
- 自动维护基础硬件、操作系统和数据库引擎，使服务保持安全和最新状态。
- 使用非独占即用即付定价，实现可预测性能。
- 在几秒钟内实现弹性缩放。
- 具有企业级安全性和行业领先的符合性，可保护静态和动态敏感数据。
- 具有监视和自动化功能，可简化大规模部署的管理和监视。
- 行业领先的支持体验。

 :::image type="content" source="./media/overview/overview-what-is-azure-postgres.png" alt-text="Azure Database for PostgreSQL":::

这些功能几乎都不需要进行任何管理，并且都是在不另外收费的情况下提供的。 借助这些功能，用户可将注意力集中在如何快速进行应用程序开发、加快推向市场，而不需要投入宝贵的时间和资源来管理虚拟机与基础结构。 此外，可以继续使用选择的开源工具和平台来开发应用程序，以提供业务所需的速度和效率，这些都不需要学习新技能。

## <a name="deployment-models"></a>部署模型

由 PostgreSQL 社区版提供支持的 Azure Database for PostgreSQL 提供三种部署模式：

- 单台服务器
- 灵活服务器（预览版）
- 超大规模 (Citus)

### <a name="azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL - 单一服务器

Azure Database for PostgreSQL 单一服务器是一项完全托管的数据库服务，对数据库自定义的要求最低。 单一服务器平台旨在以最少的用户配置和控制来处理大多数数据库管理功能，例如修补、备份、高可用性、安全性。 此体系结构已进行优化，提供内置的高可用性，在单个可用性区域的可用性为 99.99%。 它支持 PostgreSQL 社区版 9.5、9.6、10 和 11。 目前，该服务已在各种 [Azure 区域](https://azure.microsoft.com/global-infrastructure/services/)中正式发布。

“单一服务器”部署选项提供三个定价层：“基本”、“常规用途”和“内存优化”。 每个层提供不同的资源功能以支持数据库工作负荷。 可以在一个月内花费很少的费用基于小型数据库构建第一个应用，然后根据解决方案的需求调整缩放。 动态可伸缩性使得数据库能够以透明方式对不断变化的资源需求做出响应。 只需在需要资源时为所需的资源付费。 有关详细信息，请参阅[定价层](https://docs.microsoft.com/azure/postgresql/concepts-pricing-tiers)。

单一服务器最适合用于云原生应用程序，这些应用程序旨在处理自动修补，而无需对修补计划和自定义 PostgreSQL 配置设置进行精细控制。

有关单一服务器部署模式的详细概述，请参阅[单一服务器概述](./overview-single-server.md)。

### <a name="azure-database-for-postgresql---flexible-server-preview"></a>Azure Database for PostgreSQL - 灵活服务器（预览版）

Azure Database for PostgreSQL 灵活服务器是一种完全托管的数据库服务，旨在为数据库管理功能和配置设置提供更精细的控制和灵活性。 通常，该服务会根据用户要求提供更多的灵活性和自定义。 灵活服务器体系结构允许用户选择单个可用性区域内以及跨多个可用性区域的高可用性。 灵活服务器提供了更好的成本优化控制，能够停止/启动服务器和可突增计算层，非常适合不需要连续的全部计算容量的工作负载。 该服务当前支持 PostgreSQL 社区版 11 和 12，并计划很快添加更新版本。 此服务目前处于公共预览状态，现已在各种 Azure 区域中提供。

灵活服务器最适合用于

- 需要更好的控制和自定义的应用程序开发。
- 通过停止/启动服务器，实现成本优化控制。
- 区域冗余高可用性
- 托管维护时段
  
有关灵活服务器部署模式的详细概述，请参阅[灵活服务器概述](./flexible-server/overview.md)。

### <a name="azure-database-for-postgresql--hyperscale-citus"></a>Azure Database for PostgreSQL - 超大规模 (Citus)

“超大规模(Citus)”选项可以使用分片跨多个计算机来水平缩放查询。 其查询引擎会将这些服务器的传入 SQL 查询并行化，加快大型数据集上的响应。 它为需要更大规模和更高性能的应用程序提供服务，通常情况下工作负荷接近或超过 100 GB 的数据。

“超大规模(Citus)”部署选项提供：

- 使用分片跨多个计算机进行水平缩放
- 将这些服务器的查询并行化，加快大型数据集上的响应
- 为多租户应用程序提供卓越的支持，并支持实时运营分析以及高吞吐量事务工作负荷
  
针对 PostgreSQL 构建的应用程序可以在超大规模 (Citus) 部署的基础上运行分布式查询，使用标准的[连接库](https://docs.microsoft.com/azure/postgresql/concepts-connection-libraries)，基本不需要什么更改。

## <a name="next-steps"></a>后续步骤

详细了解 Azure Database for PostgreSQL 的三种部署模式，并根据需要选择适当的选项。

- [单一服务器](./overview-single-server.md)
- [灵活服务器](./flexible-server/overview.md)
- 超大规模 (Citus)
