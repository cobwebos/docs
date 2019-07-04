---
title: 有关使用 Azure 数据库迁移服务的常见问题解答 | Microsoft Docs
description: 了解有关使用 Azure 数据库迁移服务执行数据库迁移的常见问题。
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 07/04/2019
ms.openlocfilehash: f59af0bd0297b0413673fc42b26654c36351b815
ms.sourcegitcommit: d2785f020e134c3680ca1c8500aa2c0211aa1e24
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/04/2019
ms.locfileid: "67566243"
---
# <a name="faq-about-using-azure-database-migration-service"></a>有关使用 Azure 数据库迁移服务的常见问题解答

本文列出了有关使用 Azure 数据库迁移服务和相关解答常见问题。

### <a name="q-what-is-azure-database-migration-service"></a>问： 什么是 Azure 数据库迁移服务？

Azure 数据库迁移服务是完全托管的服务，旨在实现从多个数据库源到 Azure 数据平台停机时间最短的无缝迁移。 该服务目前已正式发布，目前正在进行的开发工作重点是：

* 可靠性和性能。
* 源目标对的迭代添加。
* 无摩擦迁移中的持续投资。

### <a name="q-what-source-target-pairs-does-azure-database-migration-service-currently-support"></a>问： Azure 数据库迁移服务目前支持哪些源-目标对？

该服务目前支持各种迁移方案。 有关每个可用迁移方案的状态的完整列表，请参阅文章 [Azure 数据库迁移服务支持的迁移方案的状态](https://docs.microsoft.com/azure/dms/resource-scenario-status)。 其他迁移方案处于预览状态，需要提交提名通过 DMS 预览站点。 处于预览状态，若要注册参加这些产品/服务的方案的完整列表，请参阅[DMS 预览站点](https://aka.ms/dms-preview/)。

### <a name="q-how-does-azure-database-migration-service-compare-to-other-microsoft-database-migration-tools-such-as-the-database-migration-assistant-dma-or-sql-server-migration-assistant-ssma"></a>问： Azure 数据库迁移服务与数据库迁移助手 (DMA) 或 SQL Server Migration Assistant (SSMA) 等其他 Microsoft 数据库迁移工具相比如何？

Azure 数据库迁移服务是在规模较大的数据库迁移到 Microsoft Azure 的首选的方法。 详细了解 Azure 数据库迁移服务如何与其他 Microsoft 数据库迁移工具和使用服务的各种方案的建议，请参阅博客文章[区分 Microsoft 数据库迁移工具和服务](https://blogs.msdn.microsoft.com/datamigration/2017/10/13/differentiating-microsofts-database-migration-tools-and-services/)。

### <a name="q-how-does-azure-database-migration-service-compare-to-the-azure-migrate-offering"></a>问： Azure 数据库迁移服务与 Azure 迁移产品相比如何？

Azure Migrate 可帮助在本地到 Azure IaaS 虚拟机的迁移。 该服务会评估是否适合迁移以及需要根据性能进行哪些大小调整，并提供在 Azure 中运行本地虚拟机的成本估算。 Azure Migrate 适合用于将基于本地 VM 的工作负荷直接迁移到 Azure IaaS VM。 但是，与不同的 Azure 数据库迁移服务，Azure Migrate 不提供针对 Azure PaaS 关系数据库平台，例如 Azure SQL 数据库或 Azure SQL 数据库托管实例的专用的数据库迁移服务。

### <a name="q-what-versions-of-sql-server-does-azure-database-migration-service-support-as-a-source"></a>问： Azure 数据库迁移服务作为源支持哪些版本的 SQL Server？

当从 SQL Server 进行迁移，Azure 数据库迁移服务将支持 SQL Server 2005 到 SQL Server 2017。

### <a name="q-what-is-a-summary-of-the-steps-required-to-use-azure-database-migration-service-to-perform-a-database-migration"></a>问： 使用 Azure 数据库迁移服务执行数据库迁移所需的步骤摘要是什么？

在典型的简单数据库迁移过程中，需要：

1. 创建目标数据库。
2. 使用[数据库迁移助手](https://www.microsoft.com/en-us/download/details.aspx?id=53595)迁移数据库架构。
3. 创建 Azure 数据库迁移服务的实例。
4. 创建迁移项目，用于指定源数据库、目标数据库和要迁移的表。
5. 启动完整负载。
6. 选择后续验证。
7. 执行从生产环境到新的基于云的数据库的手动切换。

### <a name="q-what-are-the-prerequisites-for-using-azure-database-migration-service"></a>问： 使用 Azure 数据库迁移服务的先决条件是什么？

有多个确保 Azure 数据库迁移服务执行数据库迁移时顺利运行所需的先决条件。 某些先决条件适用于该服务支持的所有方案（源/目标对），而其他先决条件则是特定方案所特有的。

在所有支持的迁移方案中通用的 Azure 数据库迁移服务先决条件包括需要：

* 使用 Azure 资源管理器部署模型，它提供通过使用站点到站点连接到你的本地源服务器的 Azure 数据库迁移服务创建 VNet [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction)或[VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)。
* 确保你的 Azure 虚拟网络 (VNet) 网络安全组规则不会阻止以下通信端口 443、 53、 9354、 445、 12000。 有关 Azure VNet NSG 流量筛选的更多详细信息，请参阅[使用网络安全组筛选网络流量](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)一文。
* 在源数据库的前面使用了防火墙设备时，可能需要添加防火墙规则以允许 Azure 数据库迁移服务访问要迁移的源数据库。

完成使用 Azure 数据库迁移服务的特定迁移方案所需的所有先决条件的列表，请参阅在 Azure 数据库迁移服务的相关的教程[文档](https://docs.microsoft.com/azure/dms/dms-overview)docs.microsoft.com 上。

### <a name="q-how-do-i-find-the-ip-address-for-azure-database-migration-service-so-that-i-can-create-an-allow-list-for-the-firewall-rules-used-to-access-my-source-database-for-migration"></a>问： 如何找到的 IP 地址的 Azure 数据库迁移服务，以便可以创建用于访问迁移的源数据库的防火墙规则的允许列表？

您可能需要添加防火墙规则，以允许 Azure 数据库迁移服务访问源数据库迁移到。 该服务的 IP 地址是动态的，但如果使用 Express Route，则企业网络会专门分配此地址。 标识相应的 IP 地址的最简单方法是查看同一资源组中为你预配的 Azure 数据库迁移服务资源查找关联的网络接口。 通常，网络接口资源的名称以 NIC 前缀开头，后接唯一的字符和序号，例如 NIC-jj6tnztnmarpsskr82rbndyp。 选择此网络接口资源后，可以看到需要包含在 Azure 门户资源概述页上的允许列表中的 IP 地址。

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

### <a name="q-how-do-i-set-up-an-azure-virtual-network"></a>问： 如何设置 Azure 虚拟网络？

有多篇 Microsoft 教程逐步讲解了如何完成设置 Azure VNET 的过程，另外，还可以参阅 [Azure 虚拟网络](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)一文中提到的官方文档。


### <a name="q-im-setting-up-a-migration-project-in-dms-and-im-having-difficulty-connecting-to-my-source-database-what-should-i-do"></a>问： 我将设置迁移项目中 DMS，和我遇到连接到我的源数据库。 我该怎么办？

如果必须连接到源数据库系统时迁移工作时遇到问题，请与您设置 DMS 实例在 VNet 中创建虚拟机。 在虚拟机中，您应能够运行连接测试，例如使用 UDL 文件来测试到 SQL Server 的连接或下载 Robo 3T 测试 MongoDB 连接。 如果连接测试成功，不应具有连接到您的源数据库出现问题。 如果连接测试不成功，请联系网络管理员联系。

### <a name="q-are-there-any-recommendations-for-optimizing-the-performance-of-azure-database-migration-service"></a>问： 是否有任何建议用于优化性能的 Azure 数据库迁移服务？

可以采取几项措施来加快使用该服务迁移数据库的速度：

* 创建服务实例时使用多 CPU 常规用途定价层，使该服务可以利用多个 vCPU 来实现行化和加速数据传输。
* 在数据迁移操作期间，暂时将 Azure SQL 数据库目标实例纵向扩展到高级层 SKU，以尽量减少遇到使用较低级 SKU 时出现的 Azure SQL 数据库限制，从而避免对数据传输活动造成影响。

### <a name="q-why-is-my-azure-database-migration-service-unavailable-or-stopped"></a>问： 为什么 Azure 数据库迁移服务不可用或已停止？

如果用户显式停止 Azure 数据库迁移服务 (DMS)，或者如果服务处于非活动状态已有 24 小时，则服务将处于已停止或自动暂停状态。 在上述每种情况下，服务将不可用并处于已停止状态。  若要恢复活动迁移，请重启该服务。

### <a name="q-where-can-i-leave-feedback-about-azure-database-migration-service"></a>问： 其中留下有关 Azure 数据库迁移服务的反馈？

我们希望收到读者的意见。 将任何反馈发送和 / 有关 Azure 数据库迁移服务通过 User Voice 有想法[这里](https://feedback.azure.com/forums/906100-azure-database-migration-service)，或联系的团队[询问 Azure 数据库迁移](mailto:AskAzureDatabaseMigrations@service.microsoft.com)。

## <a name="next-steps"></a>后续步骤

有关 Azure 数据库迁移服务和区域可用性的概述，请参阅[什么是 Azure 数据库迁移服务](dms-overview.md)一文。
