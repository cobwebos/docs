---
title: "Azure 订阅限制和配额 | Microsoft Docs"
description: "提供常见的 Azure 订阅和服务限制、配额和约束的列表。 这包括有关如何增加限制以及最大值的信息。"
services: 
documentationcenter: 
author: rothja
manager: jeffreyg
editor: 
tags: billing
ms.assetid: 60d848f9-ff26-496e-a5ec-ccf92ad7d125
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/11/2017
ms.author: byvinyal
ms.openlocfilehash: 1ae97b47d306640f09a535975b8e4461f51dea9e
ms.sourcegitcommit: d247d29b70bdb3044bff6a78443f275c4a943b11
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/13/2017
---
# <a name="azure-subscription-and-service-limits-quotas-and-constraints"></a>Azure 订阅和服务限制、配额和约束
本文列出了一些最常见的 Microsoft Azure 限制，有时也称为配额。 本文当前并不涵盖所有 Azure 服务。 随着时间的推移，此列表将得以展开并更新，以便涵盖更多平台。

若要了解有关 Azure 定价的详细信息，请访问 [Azure 定价概述](https://azure.microsoft.com/pricing/)。 在那里，可以使用[定价计算器](https://azure.microsoft.com/pricing/calculator/)或访问某服务（例如，[Windows VM](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows)）的详情页面预估所需的成本。 有关帮助管理成本的提示，请参阅[通过 Azure 计费和成本管理来防止意外成本](billing/billing-getting-started.md)。

> [!NOTE]
> 如果想要提高限制或配额，使其超出**默认限制**，可以[打开免费的联机客户支持请求](azure-supportability/resource-manager-core-quotas-request.md)。 无法将限制提高到超过下表中显示的**最大限制值**。 如果没有**最大限制**列，则资源没有可调整的限制。
>
> [免费试用版订阅](https://azure.microsoft.com/offers/ms-azr-0044p)不符合增加限制或配额的条件。 如果有[免费试用版订阅](https://azure.microsoft.com/offers/ms-azr-0044p)，可将其升级到[即用即付](https://azure.microsoft.com/offers/ms-azr-0003p/)订阅。 有关详细信息，请参阅[将 Azure 免费试用版订阅升级到即用即付订阅](billing/billing-upgrade-azure-subscription.md)和[免费试用版订阅常见问题解答](https://azure.microsoft.com/free/free-account-faq)。
>

## <a name="limits-and-the-azure-resource-manager"></a>限制和 Azure 资源管理器
现在可以将多个 Azure 资源合并到单个 Azure 资源组中。 在使用资源组时，以前针对全局的限制会通过 Azure 资源管理器在区域级别进行管理。 有关 Azure 资源组的详细信息，请参阅 [Azure 资源管理器概述](azure-resource-manager/resource-group-overview.md)。

在下面的限制中，添加了一个新表以反映在使用 Azure 资源管理器时限制中的任何差异。 例如，会存在一个**订阅限制**表和一个**订阅数限制 - Azure 资源管理器**表。 如果某个限制同时适用于这两种方案，它将仅显示在第一个表中。 除非另有说明，否则限制是跨所有区域的全局限制。

> [!NOTE]
> 请务必强调 Azure 资源组中的资源配额是用户的订阅可以访问的每个区域，而不像服务管理配额那样是可以访问的每个订阅。 让我们以 vCPU 配额为例。 如果要根据对 vCPU 的支持请求增加配额，则需要决定要在哪些区域中使用多少 vCPU，然后针对所需的 Azure 资源组 vCPU 配额的数量和区域发出特定请求。 因此，如果需要在西欧使用 30 个 vCPU 以在那里运行应用程序，则应专门在西欧请求 30 个 vCPU。 但这不会增加任何其他区域的 vCPU 配额 - 仅西欧会有 30 个 vCPU 配额。
> <!-- -->
> 因此，你可能会发现考虑决定你在任何一个区域中的工作负荷所需的 Azure 资源组配额数量，以及请求你考虑在其中进行部署的每个区域的数量很有用。 请参阅[部署问题疑难解答](resource-manager-common-deployment-errors.md)，了解有关发现你特定区域的当前配额的更多帮助。
>
>

## <a name="service-specific-limits"></a>服务特定的限制
* [Active Directory](#active-directory-limits)
* [API 管理](#api-management-limits)
* [应用服务](#app-service-limits)
* [应用程序网关](#application-gateway-limits)
* [Application Insights](#application-insights-limits)
* [自动化](#automation-limits)
* [Azure Cosmos DB](#azure-cosmos-db-limits)
* [Azure 事件网格](#azure-event-grid-limits)
* [Azure Redis 缓存](#azure-redis-cache-limits)
* [备份](#backup-limits)
* [批处理](#batch-limits)
* [BizTalk 服务](#biztalk-services-limits)
* [CDN](#cdn-limits)
* [云服务](#cloud-services-limits)
* [容器实例](#container-instances-limits)
* [容器注册表](#container-registry-limits)
* [Data Factory](#data-factory-limits)
* [Data Lake Analytics](#data-lake-analytics-limits)
* [Data Lake Store](#data-lake-store-limits)
* [数据库迁移服务](#database-migration-service-limits)
* [DNS](#dns-limits)
* [事件中心](#event-hubs-limits)
* [IoT 中心](#iot-hub-limits)
* [IoT 中心设备预配服务](#iot-hub-device-provisioning-service-limits)
* [Key Vault](#key-vault-limits)
* [Log Analytics/操作见解](#log-analytics-limits)
* [媒体服务](#media-services-limits)
* [Mobile Engagement](#mobile-engagement-limits)
* [移动服务](#mobile-services-limits)
* [监视](#monitor-limits)
* [多重身份验证](#multi-factor-authentication)
* [网络](#networking-limits)
* [网络观察程序](#network-watcher-limits)
* [通知中心服务](#notification-hub-service-limits)
* [资源组](#resource-group-limits)
* [计划程序](#scheduler-limits)
* [搜索](#search-limits)
* [服务总线](#service-bus-limits)
* [Site Recovery](#site-recovery-limits)
* [SQL 数据库](#sql-database-limits)
* [SQL 数据仓库](#sql-data-warehouse-limits)
* [存储](#storage-limits)
* [StorSimple 系统](#storsimple-system-limits)
* [流分析](#stream-analytics-limits)
* [订阅](#subscription-limits)
* [流量管理器](#traffic-manager-limits)
* [虚拟机](#virtual-machines-limits)
* [虚拟机规模集](#virtual-machine-scale-sets-limits)

### <a name="subscription-limits"></a>订阅限制
#### <a name="subscription-limits"></a>订阅限制
[!INCLUDE [azure-subscription-limits](../includes/azure-subscription-limits.md)]

#### <a name="subscription-limits---azure-resource-manager"></a>订阅限制 - Azure 资源管理器
使用 Azure 资源管理器和 Azure 资源组时，以下限制适用。 未使用 Azure 资源管理器更改的限制不会在下面列出。 请参阅上表了解这些限制。

有关处理资源管理器请求限制的信息，请参阅[限制资源管理器请求](resource-manager-request-limits.md)。

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../includes/azure-subscription-limits-azure-resource-manager.md)]

### <a name="resource-group-limits"></a>资源组限制
[!INCLUDE [azure-resource-groups-limits](../includes/azure-resource-groups-limits.md)]

### <a name="virtual-machines-limits"></a>虚拟机限制
#### <a name="virtual-machine-limits"></a>虚拟机限制
[!INCLUDE [azure-virtual-machines-limits](../includes/azure-virtual-machines-limits.md)]

#### <a name="virtual-machines-limits---azure-resource-manager"></a>虚拟机限制 - Azure 资源管理器
使用 Azure 资源管理器和 Azure 资源组时，以下限制适用。 未使用 Azure 资源管理器更改的限制不会在下面列出。 请参阅上表了解这些限制。

[!INCLUDE [azure-virtual-machines-limits-azure-resource-manager](../includes/azure-virtual-machines-limits-azure-resource-manager.md)]

### <a name="virtual-machine-scale-sets-limits"></a>虚拟机规模集限制
[!INCLUDE [virtual-machine-scale-sets-limits](../includes/azure-virtual-machine-scale-sets-limits.md)]

### <a name="container-instances-limits"></a>容器实例限制
[!INCLUDE [container-instances-limits](../includes/container-instances-limits.md)]

### <a name="container-registry-limits"></a>容器注册表限制
下表详细介绍了基本、标准和高级[服务层](./container-registry/container-registry-skus.md)的功能和限制。

[!INCLUDE [container-registry-limits](../includes/container-registry-limits.md)]

### <a name="networking-limits"></a>网络限制
[!INCLUDE [expressroute-limits](../includes/expressroute-limits.md)]

#### <a name="networking-limits"></a>网络限制
[!INCLUDE [azure-virtual-network-limits](../includes/azure-virtual-network-limits.md)]

#### <a name="application-gateway-limits"></a>应用程序网关限制
[!INCLUDE [application-gateway-limits](../includes/application-gateway-limits.md)]

#### <a name="network-watcher-limits"></a>网络观察程序限制
[!INCLUDE [network-watcher-limits](../includes/network-watcher-limits.md)]

#### <a name="traffic-manager-limits"></a>流量管理器限制
[!INCLUDE [traffic-manager-limits](../includes/traffic-manager-limits.md)]

#### <a name="dns-limits"></a>DNS 限制
[!INCLUDE [dns-limits](../includes/dns-limits.md)]

### <a name="storage-limits"></a>存储限制
有关存储帐户限制的详细信息，请参阅 [Azure 存储可伸缩性和性能目标](storage/common/storage-scalability-targets.md)。

<!--like # storage accts -->
[!INCLUDE [azure-storage-limits](../includes/azure-storage-limits.md)]

[!INCLUDE [azure-storage-limits-azure-resource-manager](../includes/azure-storage-limits-azure-resource-manager.md)]

#### <a name="azure-blob-storage-limits"></a>Azure Blob 存储限制
[!INCLUDE [storage-blob-scale-targets](../includes/storage-blob-scale-targets.md)]

#### <a name="azure-files-limits"></a>Azure 文件限制
有关 Azure 文件限制的其他详细信息，请参阅 [Azure 文件可伸缩性和性能目标](storage/files/storage-files-scale-targets.md)。

[!INCLUDE [storage-files-scale-targets](../includes/storage-files-scale-targets.md)]

#### <a name="azure-file-sync-limits"></a>Azure 文件同步限制
[!INCLUDE [storage-sync-files-scale-targets](../includes/storage-sync-files-scale-targets.md)]

#### <a name="azure-queue-storage-limits"></a>Azure 队列存储限制
[!INCLUDE [storage-queues-scale-targets](../includes/storage-queues-scale-targets.md)]

#### <a name="azure-table-storage-limits"></a>Azure 表存储限制
[!INCLUDE [storage-tables-scale-targets](../includes/storage-tables-scale-targets.md)]

<!-- conceptual info about disk limits -- applies to unmanaged and managed -->
#### <a name="virtual-machine-disk-limits"></a>虚拟机磁盘限制
[!INCLUDE [azure-storage-limits-vm-disks](../includes/azure-storage-limits-vm-disks.md)]

有关其他详细信息，请参阅[虚拟机大小](virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

#### <a name="managed-virtual-machine-disks"></a>托管虚拟机磁盘

[!INCLUDE [azure-storage-limits-vm-disks-managed](../includes/azure-storage-limits-vm-disks-managed.md)]

#### <a name="unmanaged-virtual-machine-disks"></a>非托管虚拟机磁盘

[!INCLUDE [azure-storage-limits-vm-disks-standard](../includes/azure-storage-limits-vm-disks-standard.md)]

[!INCLUDE [azure-storage-limits-vm-disks-premium](../includes/azure-storage-limits-vm-disks-premium.md)]

### <a name="cloud-services-limits"></a>云服务限制
[!INCLUDE [azure-cloud-services-limits](../includes/azure-cloud-services-limits.md)]

### <a name="app-service-limits"></a>应用服务限制
以下应用服务限制包括 Web 应用、移动应用、API 应用和逻辑应用的限制。

[!INCLUDE [azure-websites-limits](../includes/azure-websites-limits.md)]

### <a name="scheduler-limits"></a>计划程序限制
[!INCLUDE [scheduler-limits-table](../includes/scheduler-limits-table.md)]

### <a name="batch-limits"></a>Batch 限制
[!INCLUDE [azure-batch-limits](../includes/azure-batch-limits.md)]

### <a name="biztalk-services-limits"></a>BizTalk 服务限制
下表显示了 Azure BizTalk 服务的限制。

[!INCLUDE [biztalk-services-service-limits](../includes/biztalk-services-service-limits.md)]

### <a name="azure-cosmos-db-limits"></a>Azure Cosmos DB 限制
Azure Cosmos DB 是全局缩放数据库，可对吞吐量和存储进行缩放，以处理应用程序的任何需求。 如果对 Azure Cosmos DB 提供的规模有任何问题，请发送电子邮件到 askcosmosdb@microsoft.com。

### <a name="mobile-engagement-limits"></a>Mobile Engagement 限制
[!INCLUDE [azure-mobile-engagement-limits](../includes/azure-mobile-engagement-limits.md)]

### <a name="search-limits"></a>搜索限制
定价层决定了搜索服务的容量和限制。 层包括：

* 免费层为多租户服务，可与其他 Azure 订阅用户共享，仅用于评估和小型开发项目。
* 基本层为规模较小的生产工作负荷提供专用计算资源，并为高可用查询工作负荷提供最多 3 个副本。
* 标准层（S1、S2、S3、S3 高密度）适用于较大型生产工作负荷。 标准层内有多个级别，以便可以选择与工作负荷配置文件最匹配的资源配置。

**基于订阅的限制**

[!INCLUDE [azure-search-limits-per-subscription](../includes/azure-search-limits-per-subscription.md)]

**基于搜索服务的限制**

[!INCLUDE [azure-search-limits-per-service](../includes/azure-search-limits-per-service.md)]

若要更详细地了解关于限制的详细信息（如文档大小、每秒查询数、密钥、请求和响应），请参阅 [Azure 搜索中的服务限制](search/search-limits-quotas-capacity.md)。

### <a name="media-services-limits"></a>媒体服务限制
[!INCLUDE [azure-mediaservices-limits](../includes/azure-mediaservices-limits.md)]

### <a name="cdn-limits"></a>CDN 限制
[!INCLUDE [cdn-limits](../includes/cdn-limits.md)]

### <a name="mobile-services-limits"></a>移动服务限制
[!INCLUDE [mobile-services-limits](../includes/mobile-services-limits.md)]

### <a name="monitor-limits"></a>监视限制
[!INCLUDE [monitoring-limits](../includes/monitoring-limits.md)]

### <a name="notification-hub-service-limits"></a>通知中心服务限制
[!INCLUDE [notification-hub-limits](../includes/notification-hub-limits.md)]

### <a name="event-hubs-limits"></a>事件中心限制
[!INCLUDE [azure-servicebus-limits](../includes/event-hubs-limits.md)]

### <a name="service-bus-limits"></a>服务总线限制
[!INCLUDE [azure-servicebus-limits](../includes/service-bus-quotas-table.md)]

### <a name="iot-hub-limits"></a>IoT 中心限制
[!INCLUDE [azure-iothub-limits](../includes/iot-hub-limits.md)]

### <a name="iot-hub-device-provisioning-service-limits"></a>IoT 中心设备预配服务限制
[!INCLUDE [azure-iotdps-limits](../includes/iot-dps-limits.md)]

### <a name="data-factory-limits"></a>数据工厂限制
[!INCLUDE [azure-data-factory-limits](../includes/azure-data-factory-limits.md)]

### <a name="data-lake-analytics-limits"></a>Data Lake Analytics 限制
[!INCLUDE [azure-data-lake-analytics-limits](../includes/azure-data-lake-analytics-limits.md)]

### <a name="data-lake-store-limits"></a>Data Lake Store 限制
[!INCLUDE [azure-data-lake-store-limits](../includes/azure-data-lake-store-limits.md)]

### <a name="database-migration-service-limits"></a>数据库迁移服务限制
[!INCLUDE [database-migration-service-limits](../includes/database-migration-service-limits.md)]

### <a name="stream-analytics-limits"></a>流分析限制
[!INCLUDE [stream-analytics-limits-table](../includes/stream-analytics-limits-table.md)]

### <a name="active-directory-limits"></a>Active Directory 限制
[!INCLUDE [AAD-service-limits](../includes/active-directory-service-limits-include.md)]

### <a name="azure-event-grid-limits"></a>Azure 事件网格限制
[!INCLUDE [event-grid-limits](../includes/event-grid-limits.md)]

### <a name="storsimple-system-limits"></a>StorSimple 系统限制
[!INCLUDE [storsimple-limits-table](../includes/storsimple-limits-table.md)]

### <a name="log-analytics-limits"></a>Log Analytics 限制
[!INCLUDE [operational-insights-limits](../includes/operational-insights-limits.md)]

### <a name="backup-limits"></a>备份限制
[!INCLUDE [azure-backup-limits](../includes/azure-backup-limits.md)]

### <a name="site-recovery-limits"></a>站点恢复限制
[!INCLUDE [site-recovery-limits](../includes/site-recovery-limits.md)]

### <a name="application-insights-limits"></a>Application Insights 限制
[!INCLUDE [application-insights-limits](../includes/application-insights-limits.md)]

### <a name="api-management-limits"></a>API 管理限制
[!INCLUDE [api-management-service-limits](../includes/api-management-service-limits.md)]

### <a name="azure-redis-cache-limits"></a>Azure Redis 缓存限制
[!INCLUDE [redis-cache-service-limits](../includes/redis-cache-service-limits.md)]

### <a name="key-vault-limits"></a>密钥保管库限制
[!INCLUDE [key-vault-limits](../includes/key-vault-limits.md)]

### <a name="multi-factor-authentication"></a>多重身份验证
[!INCLUDE [azure-mfa-service-limits](../includes/azure-mfa-service-limits.md)]

### <a name="automation-limits"></a>自动化限制
[!INCLUDE [automation-limits](../includes/azure-automation-service-limits.md)]

### <a name="sql-database-limits"></a>SQL 数据库限制
有关 Azure SQL 数据库限制，请参阅 [SQL 数据库资源限制](sql-database/sql-database-resource-limits.md)。

### <a name="sql-data-warehouse-limits"></a>SQL 数据仓库限制
有关 SQL 数据仓库限制，请参阅 [SQL 数据仓库资源限制](sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md)。

## <a name="see-also"></a>另请参阅
[Understanding Azure Limits and Increases](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)（了解 Azure 的限制和增加）

[Azure 的虚拟机和云服务大小](virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[云服务的大小](cloud-services/cloud-services-sizes-specs.md)
