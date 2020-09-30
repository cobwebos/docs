---
title: Azure Database for PostgreSQL 灵活服务器
description: 概述了 Azure Database for PostgreSQL 灵活服务器。
author: sunilagarwal
ms.author: sunila
ms.custom: mvc
ms.service: postgresql
ms.topic: overview
ms.date: 09/22/2020
ms.openlocfilehash: 268eedf6f9d64d52539e20006322b6b1dd9964e8
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91439967"
---
# <a name="azure-database-for-postgresql---flexible-server"></a>Azure Database for PostgreSQL 灵活服务器

由 PostgreSQL 社区版提供支持的 [Azure Database for PostgreSQL](../overview.md) 提供三种部署模式：

- [单一服务器](../overview-single-server.md)
- 灵活服务器（预览版）
- 超大规模 (Citus)

本文概述和介绍了灵活服务器部署模型的核心概念。

> [!IMPORTANT]
> Azure Database for PostgreSQL 灵活服务器以预览版提供

## <a name="overview"></a>概述

Azure Database for PostgreSQL 灵活服务器是一种完全托管的数据库服务，旨在针对数据库管理功能和配置设置提供更精细的控制和更大的灵活性。 通常，该服务提供更大的灵活性和基于用户要求的服务器配置自定义。 灵活服务器体系结构允许用户将数据库引擎与客户端层并置以降低延迟，选择单个可用性区域内以及跨多个可用性区域的高可用性。 灵活服务器还提供了更好的成本优化控制，具有停止/启动服务器和可突增计算层的功能，非常适合无需持续全计算容量的工作负载。 该服务当前支持 PostgreSQL 11 和 12 的社区版本。 此服务目前以预览版提供，现已在各种 [Azure 区域](https://azure.microsoft.com/global-infrastructure/services/)中提供。

![灵活服务器 - 概述](./media/overview/overview-flexible-server.png)


灵活服务器最适合用于

- 需要更好的控制和自定义的应用程序开发。
- 区域冗余高可用性
- 托管维护时段
  
## <a name="high-availability"></a>高可用性

灵活服务器部署模型旨在支持单个可用性区域内以及跨多个可用性区域的高可用性。 该体系结构将计算和存储分开。 数据库引擎在 Linux 虚拟机上运行，而数据文件位于 Azure 存储上。 该存储维护数据库文件的三个本地冗余同步副本，以确保数据持续性。

在计划内或计划外的故障转移事件期间，如果服务器发生故障，该服务将使用以下自动化过程来维持服务器的高可用性：

1. 预配新的计算 Linux VM。
2. 具有数据文件的存储映射到新的虚拟机
3. PostgreSQL 数据库引擎在新的虚拟机上联机。

下图显示了 VM 和存储故障的过渡。

 :::image type="content" source="./media/overview/overview-azure-postgres-flex-virtualmachine.png" alt-text="灵活服务器 - VM 和存储故障":::

如果已配置区域冗余高可用性，则该服务将在同一 Azure 区域内的可用性区域中预配并维护热备用服务器。 源服务器上的数据更改将同步复制到备用服务器，以确保没有数据丢失。 借助区域冗余高可用性，一旦触发了计划内或计划外的故障转移事件，备用服务器将立即联机，并可用于处理传入的事务。 这使服务可在支持多个可用性区域的 Azure 区域内从可用性区域故障中复原，如下图所示。

 :::image type="content" source="./media/business-continuity/concepts-zone-redundant-high-availability-architecture.png" alt-text="灵活服务器 - VM 和存储故障":::

 有关详细信息，请参阅[高可用性文档](./concepts-high-availability.md)。

## <a name="automated-patching-with-managed-maintenance-window"></a>具有托管维护时段的自动修补

该服务执行基础硬件、OS 和数据库引擎的自动修补。 修补包括安全更新和软件更新。 对于 PostgreSQL 引擎，次要版本升级也包括在计划内维护版本中。 用户可以将修补计划配置为系统托管或定义自己的自定义计划。 在维护计划期间，将应用补丁，并且在修补过程中，可能需要重启服务器才能完成更新。 使用自定义计划，用户可以使修补周期可预测，并选择对业务影响最小的维护时段。 一般来说，作为持续集成和发布的一部分，该服务遵循每月发布计划。

## <a name="automatic-backups"></a>自动备份

灵活服务器服务会自动创建服务器备份，并将其存储在用户本地配置的区域冗余存储 (ZRS) 中。 备份可用于将服务器还原到备份保持期内的任何时间点。 默认的备份保留期为七天。 保持期可选择配置为最多 35 天。 所有备份都使用 AES 256 位加密进行加密。 有关更多详细信息，请参阅[备份](./concepts-backup-restore.md)。

## <a name="adjust-performance-and-scale-within-seconds"></a>几秒钟内调整性能和规模

灵活服务器服务在三个计算层中提供：“可突增”、“常规用途”和“内存优化”。 可突增层最适合用于低成本开发和不需要持续全计算容量的低并发工作负载。 常规用途和内存优化更适用于需要高并发性、缩放性和可预测性能的生产工作负载。 可以在一个月内花费很少的费用在小型数据库上生成第一个应用程序，然后无缝调整规模以满足解决方案的需求。

## <a name="stopstart-server-to-lower-tco"></a>停止/启动服务器以降低 TCO

灵活服务器服务允许你按需停止和启动服务器以降低 TCO。 服务器停止后，计算层计费会立即停止。 这使你可在开发和测试期间以及针对有时限的可预测生产工作负载，节省大量成本。 服务器将保持停止状态七天（除非提前重启它）。

## <a name="enterprise-grade-security"></a>企业级安全性

灵活服务器服务使用 FIPS 140-2 验证的加密模块对静态数据进行存储加密。 在运行查询时创建的数据（包括备份）和临时文件都会进行加密。 该服务使用包含在 Azure 存储加密中的 AES 256 位密码，并且密钥可由系统进行管理（默认）。 该服务使用默认实施的传输层安全性 (SSL/TLS) 对动态数据进行加密。 该服务仅强制实施和支持 TLS 版本 1.2。

灵活服务器允许使用 Azure 虚拟网络（VNet 集成）对服务器进行完全专用访问。 只能通过专用 IP 地址才能访问和连接 Azure 虚拟网络中的服务器。 使用 VNet 集成，公共访问遭到拒绝，使用公共终结点无法访问服务器。

## <a name="monitoring-and-alerting"></a>监视和警报

灵活服务器服务配备了内置的性能监视和警报功能。 所有 Azure 指标的频率都是一分钟，每个指标提供 30 天的历史记录。 可针对指标配置警报。 该服务公开主机服务器指标来监视资源利用率，并允许配置慢查询日志。 使用这些工具，可快速优化工作负载并配置服务器以获得最佳性能。

## <a name="migration"></a>迁移

该服务运行 PostgreSQL 的社区版本。 这样可以实现完全的应用程序兼容性，并且只需最小的重构成本就能将在 PostgreSQL 引擎上开发的现有应用程序迁移到灵活服务器。 

- 转储和还原 - 对于脱机迁移（在此情况下，用户可承受一定的故障时间），使用社区工具（如 Pg_dump 和 Pg_restore）的转储和还原可以提供最快的迁移方式。 有关详细信息，请参阅[使用转储和还原进行迁移](https://docs.microsoft.com/azure/postgresql/howto-migrate-using-dump-and-restore)。
- Azure 数据库迁移服务 - 为了在最短的故障时间内实现向灵活服务器的无缝简化迁移，可以利用 Azure 数据库迁移服务。 请参阅[通过门户的 DMS](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online-portal) 和[通过 CLI 的 DMS](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online)。 可以从 Azure Database for PostgreSQL 单一服务器迁移到灵活服务器。 有关详细信息，请参阅此 [DMS 文章](https://docs.microsoft.com/azure/dms/tutorial-azure-postgresql-to-azure-postgresql-online-portal)。

## <a name="next-steps"></a>后续步骤

现在，你已阅读 Azure Database for PostgreSQL 灵活服务器部署模式简介，接下来可创建第一个服务器了：[使用 Azure 门户创建 Azure Database for PostgreSQL 灵活服务器](./quickstart-create-server-portal.md)


