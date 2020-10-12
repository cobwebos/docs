---
title: 常见问题解答 - Azure 数据库迁移服务
description: 有关使用 Azure 数据库迁移服务执行数据库迁移的常见问题解答。
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: troubleshooting
ms.date: 02/20/2020
ms.openlocfilehash: b8f570c2cd5f3939bab0df0c4fefe1becc3c81da
ms.sourcegitcommit: b437bd3b9c9802ec6430d9f078c372c2a411f11f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91893435"
---
# <a name="faq-about-using-azure-database-migration-service"></a>有关使用 Azure 数据库迁移服务的常见问题解答

本文列出了有关使用 Azure 数据库迁移服务的常见问题和相关解答。

## <a name="overview"></a>概述

**问：什么是 Azure 数据库迁移服务？**
Azure 数据库迁移服务是一项完全托管的服务，旨在实现从多个数据库源到 Azure 数据平台的无缝迁移，并且最小化停机时间。 该服务目前已正式发布，目前正在进行的开发工作重点是：

* 可靠性和性能。
* 源目标对的迭代添加。
* 无摩擦迁移中的持续投资。

**问：Azure 数据库迁移服务目前支持哪些源-目标对？**
该服务目前支持各种源/目标对或迁移方案。 有关每个可用迁移方案的状态的完整列表，请参阅文章 [Azure 数据库迁移服务支持的迁移方案的状态](https://docs.microsoft.com/azure/dms/resource-scenario-status)。

其他迁移方案处于预览状态，需要通过 DMS 预览站点提交提名。 有关预览版中的方案的完整列表以及如何注册以参与其中一个产品/服务，请参阅 [DMS 预览站点](https://aka.ms/dms-preview/)。

**问：Azure 数据库迁移服务支持将什么版本的 SQL Server 用作源？**
从 SQL Server 迁移时，Azure 数据库迁移服务支持的源为 SQL Server 2005 至 SQL Server 2019。

**问：使用 Azure 数据库迁移服务时，脱机迁移与联机迁移的区别是什么？**
可以使用 Azure 数据库迁移服务执行脱机和联机迁移。 使用脱机迁移时，应用程序停机时间从迁移开始时算起。** 使用联机迁移时，停机时间仅限在迁移结束时进行转换的那段时间。** 建议对脱机迁移进行测试，以便确定其停机时间是否可以接受；如果不能接受，请进行联机迁移。

> [!NOTE]
> 使用 Azure 数据库迁移服务执行联机迁移需要基于“高级”定价层创建实例。 有关详细信息，请参阅 Azure 数据库迁移服务[定价](https://azure.microsoft.com/pricing/details/database-migration/)页。

**问：Azure 数据库迁移服务与其他 Microsoft 数据库迁移工具（例如数据库迁移助手 (DMA) 或 SQL Server 迁移助手 (SSMA)）有何差别？**
Azure 数据库迁移服务是用于大规模 Microsoft Azure 数据库迁移的首选方法。 有关 Azure 数据库迁移服务与其他 Microsoft 数据库迁移工具的差别，以及对各种方案使用不同服务的建议，请参阅博客文章 [Differentiating Microsoft’s Database Migration Tools and Services](https://techcommunity.microsoft.com/t5/microsoft-data-migration/differentiating-microsoft-s-database-migration-tools-and/ba-p/368529)（Microsoft 数据库迁移工具和服务的差别）。

**问：Azure 数据库迁移服务与 Azure 迁移产品/服务有何差别？**
Azure 迁移服务可以帮助将本地虚拟机迁移到 Azure IaaS。 该服务会评估是否适合迁移以及需要根据性能进行哪些大小调整，并提供在 Azure 中运行本地虚拟机的成本估算。 Azure Migrate 适合用于将基于本地 VM 的工作负荷直接迁移到 Azure IaaS VM。 但是，与 Azure 数据库迁移服务不同，Azure Migrate 不是适用于 Azure SQL 数据库或 Azure SQL 托管实例等 Azure PaaS 关系数据库平台的专用数据库迁移服务产品。

## <a name="setup"></a>设置

**问：使用 Azure 数据库迁移服务的先决条件是什么？**
若要确保 Azure 数据库迁移服务在执行数据库迁移时顺利运行，需要满足几个先决条件。 某些先决条件适用于该服务支持的所有方案（源/目标对），而其他先决条件则是特定方案所特有的。

在所有支持的迁移方案中通用的 Azure 数据库迁移服务先决条件包括需要：

* 使用 Azure 资源管理器部署模型创建适合 Azure 数据库迁移服务的 Microsoft Azure 虚拟网络，它将使用 [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) 或 [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) 为本地源服务器提供站点到站点连接。
* 确保虚拟网络安全组规则不会阻止以下通信端口443、53、5671-5672、9350-9354、445、12000。 有关虚拟网络 NSG 流量筛选的更多详细信息，请参阅[使用网络安全组筛选网络流量](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)一文。
* 在源数据库的前面使用了防火墙设备时，可能需要添加防火墙规则以允许 Azure 数据库迁移服务访问要迁移的源数据库。

有关使用 Azure 数据库迁移服务来竞争特定迁移方案所需的所有先决条件的列表，请参阅 docs.microsoft.com 上的 Azure 数据库迁移服务 [文档](https://docs.microsoft.com/azure/dms/dms-overview) 中的相关教程。

**问：如何查找 Azure 数据库迁移服务的 IP 地址，以便可以创建防火墙规则的允许列表用于访问要迁移的源数据库？**
可能需要添加防火墙规则，以允许 Azure 数据库迁移服务访问要迁移的源数据库。 该服务的 IP 地址是动态的，但如果你使用 ExpressRoute，则企业网络会专门分配此地址。 标识相应 IP 地址的最简单方法是查看预配的 Azure 数据库迁移服务资源所在的同一个资源组，找到关联的网络接口。 通常，网络接口资源的名称以 NIC 前缀开头，后接唯一的字符和序号，例如 NIC-jj6tnztnmarpsskr82rbndyp。 选择此网络接口资源后，可以看到需要包含在 Azure 门户资源概述页上的允许列表中的 IP 地址。

可能还需要在允许列表中包含 SQL Server 侦听的端口源。 默认情况下为端口 1433，但源 SQL Server 可能也配置为侦听其他端口。 在这种情况下，也需要在允许列表中包含这些端口。 可以使用动态管理视图查询来确定 SQL Server 侦听的端口：

```sql
    SELECT DISTINCT
        local_tcp_port
    FROM sys.dm_exec_connections
    WHERE local_tcp_port IS NOT NULL
```

也可以通过查询 SQL Server 错误日志来确定 SQL Server 侦听的端口：

```sql
    USE master
    GO
    xp_readerrorlog 0, 1, N'Server is listening on'
    GO
```

**：.如何实现设置 Microsoft Azure 虚拟网络？**
尽管有多个 Microsoft 教程可以指导你完成设置虚拟网络的过程，但官方文档出现在 [Azure 虚拟网络](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)一文中。

## <a name="usage"></a>使用情况

**问：使用 Azure 数据库迁移服务执行数据库迁移所要执行的步骤摘要是什么？**
在典型的简单数据库迁移过程中，需要：

1. 创建目标数据库。
2. 评估源数据库。
    * 对于同构迁移，使用 [DMA](https://www.microsoft.com/download/details.aspx?id=53595) 评估现有数据库。
    * 对于异构迁移（从竞争源进行），使用 [SSMA](https://aka.ms/get-ssma) 评估现有数据库。 还可以使用 SSMA 来转换数据库对象，以及将架构迁移到目标平台。
3. 创建 Azure 数据库迁移服务的实例。
4. 创建迁移项目，用于指定源数据库、目标数据库和要迁移的表。
5. 启动完全加载。
6. 选择后续验证。
7. 执行从生产环境到新的基于云的数据库的手动切换。

## <a name="troubleshooting-and-optimization"></a>故障排除和优化

**问：我正在 DMS 中设置一个迁移项目，在连接到源数据库时遇到问题。**
如果在迁移过程中连接到源数据库系统时遇到问题，请在虚拟网络中创建一个虚拟机，通过其设置 DMS 实例。 在该虚拟机中，应该能够运行连接测试，例如，使用 UDL 文件测试连接到 SQL Server，或下载 Robo 3T 以测试 MongoDB 连接。 如果连接测试成功，则在连接到源数据库时应该不会遇到问题。 如果连接测试失败，请与网络管理员联系。

**问：为什么 Azure 数据库迁移服务不可用或已停止？**
如果用户显式停止 Azure 数据库迁移服务 (DMS)，或者如果服务处于非活动状态已有 24 小时，则服务将处于已停止或自动暂停状态。 在上述每种情况下，服务将不可用并处于已停止状态。  若要恢复活动迁移，请重启该服务。

**问：在优化 Azure 数据库迁移服务的性能方面，你们是否提供任何建议？**
可以采取几项措施来加快使用该服务迁移数据库的速度：

* 创建服务实例时使用多 CPU 常规用途定价层，使该服务可以利用多个 vCPU 来实现行化和加速数据传输。
* 在数据迁移操作期间，暂时将 Azure SQL 数据库目标实例纵向扩展到高级层 SKU，以尽量减少遇到使用较低级 SKU 时出现的 Azure SQL 数据库限制，从而避免对数据传输活动造成影响。

## <a name="next-steps"></a>后续步骤

有关 Azure 数据库迁移服务和区域可用性的概述，请参阅[什么是 Azure 数据库迁移服务](dms-overview.md)一文。
