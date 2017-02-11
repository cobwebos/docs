---
title: "Microsoft Azure 订阅和服务限制、配额以及约束"
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
ms.date: 08/29/2016
ms.author: byvinyal
translationtype: Human Translation
ms.sourcegitcommit: 63cf1a5476a205da2f804fb2f408f4d35860835f
ms.openlocfilehash: c98484a754943ad0ea5b1098a9a6c06cbf5f8814


---
# <a name="azure-subscription-and-service-limits-quotas-and-constraints"></a>Azure 订阅和服务限制、配额和约束
## <a name="overview"></a>概述
本文档指定一些最常见的 Microsoft Azure 限制。 本文档目前未涵盖所有 Azure 服务。 一段时间后，将展开并更新这些限制以包含多个平台。

若要了解有关 Azure 定价的详细信息，请访问 [Azure 定价概述](https://azure.microsoft.com/pricing/)。 在那里，你可以使用[定价计算器](https://azure.microsoft.com/pricing/calculator/)或访问某服务（例如，[Windows VM](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows)）的详情页面预估所需的成本。

> [!NOTE]
> 如果想要将限制提高到超过**默认限制**，可以[打开免费的联机客户支持请求](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 无法将这些限制提高到超过下表中的“最大限制”值。 如果没有“最大限制”列，则指定的资源将不具有可调整的限制。
> 
> 

## <a name="limits-and-the-azure-resource-manager"></a>限制和 Azure 资源管理器
现在可以将多个 Azure 资源合并到单个 Azure 资源组中。 在使用资源组时，以前针对全局的限制会通过 Azure 资源管理器在区域级别进行管理。 有关 Azure 资源组的详细信息，请参阅 [Azure Resource Manager 概述](azure-resource-manager/resource-group-overview.md)。

在下面的限制中，添加了一个新表以反映在使用 Azure 资源管理器时限制中的任何差异。 例如，会存在一个**订阅限制**表和一个**订阅数限制 - Azure Resource Manager** 表。 如果某个限制同时适用于这两种方案，它将仅显示在第一个表中。 除非另有说明，否则限制是跨所有区域的全局限制。

> [!NOTE]
> 请务必强调 Azure 资源组中的资源配额是用户的订阅可以访问的每个区域，而不像服务管理配额那样是可以访问的每个订阅。 我们来使用核心配额作为示例。 如果您需要根据对核心的支持请求增加配额，则需要决定您想要在哪个区域中使用多少核心，然后针对您希望的 Azure 资源组核心配额的数量和区域进行特定请求。 因此，如果您需要在西欧使用 30 个核心以在那里运行您的应用程序，则应专门在西欧请求 30 个核心。 但这不会增加您在任何其他区域的核心配额 -- 仅西欧会有 30 个核心配额。
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
* [Azure Redis Cache](#azure-redis-cache-limits)
* [Azure RemoteApp](#azure-remoteapp-limits)
* [备份](#backup-limits)
* [批处理](#batch-limits)
* [BizTalk 服务](#biztalk-services-limits)
* [CDN](#cdn-limits)
* [云服务](#cloud-services-limits)
* [Data Factory](#data-factory-limits)
* [Data Lake Analytics](#data-lake-analytics-limits)
* [DNS](#dns-limits)
* [DocumentDB](#documentdb-limits)
* [事件中心](#event-hubs-limits)
* [IoT 中心](#iot-hub-limits)
* [密钥保管库](#key-vault-limits)
* [媒体服务](#media-services-limits)
* [Mobile Engagement](#mobile-engagement-limits)
* [移动服务](#mobile-services-limits)
* [监视](#monitoring-limits)
* [多重身份验证](#multi-factor-authentication)
* [网络](#networking-limits)
* [通知中心服务](#notification-hub-service-limits)
* [操作见解](#operational-insights-limits)
* [资源组](#resource-group-limits)
* [计划程序](#scheduler-limits)
* [搜索](#search-limits)
* [服务总线](#service-bus-limits)
* [Site Recovery](#site-recovery-limits)
* [SQL 数据库](#sql-database-limits)
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

#### <a name="subscription-limits---azure-resource-manager"></a>订阅限制 - Azure Resource Manager
使用 Azure 资源管理器和 Azure 资源组时，以下限制适用。 未使用 Azure 资源管理器更改的限制不会在下面列出。 请参阅上表了解这些限制。

有关处理资源管理器请求限制的信息，请参阅[限制资源管理器请求](resource-manager-request-limits.md)。

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../includes/azure-subscription-limits-azure-resource-manager.md)]

### <a name="resource-group-limits"></a>资源组限制
[!INCLUDE [azure-resource-groups-limits](../includes/azure-resource-groups-limits.md)]

### <a name="virtual-machines-limits"></a>虚拟机限制
#### <a name="virtual-machine-limits"></a>虚拟机限制
[!INCLUDE [azure-virtual-machines-limits](../includes/azure-virtual-machines-limits.md)]

#### <a name="virtual-machines-limits---azure-resource-manager"></a>虚拟机限制 - Azure Resource Manager
使用 Azure 资源管理器和 Azure 资源组时，以下限制适用。 未使用 Azure 资源管理器更改的限制不会在下面列出。 请参阅上表了解这些限制。

[!INCLUDE [azure-virtual-machines-limits-azure-resource-manager](../includes/azure-virtual-machines-limits-azure-resource-manager.md)]

### <a name="virtual-machine-scale-sets-limits"></a>虚拟机规模集限制
[!INCLUDE [virtual-machine-scale-sets-limits](../includes/azure-virtual-machine-scale-sets-limits.md)]

### <a name="networking-limits"></a>网络限制
[!INCLUDE [expressroute-limits](../includes/expressroute-limits.md)]

#### <a name="networking-limits"></a>网络限制
[!INCLUDE [azure-virtual-network-limits](../includes/azure-virtual-network-limits.md)]

#### <a name="application-gateway-limits"></a>应用程序网关限制
[!INCLUDE [application-gateway-limits](../includes/application-gateway-limits.md)]

#### <a name="traffic-manager-limits"></a>流量管理器限制
[!INCLUDE [traffic-manager-limits](../includes/traffic-manager-limits.md)]

#### <a name="dns-limits"></a>DNS 限制
[!INCLUDE [dns-limits](../includes/dns-limits.md)]

### <a name="storage-limits"></a>存储限制
有关存储帐户限制的详细信息，请参阅 [Azure 存储可伸缩性和性能目标](storage/storage-scalability-targets.md)。

#### <a name="storage-service-limits"></a>存储服务限制
[!INCLUDE [azure-storage-limits](../includes/azure-storage-limits.md)]

#### <a name="virtual-machine-disk-limits"></a>虚拟机磁盘限制
[!INCLUDE [azure-storage-limits-vm-disks](../includes/azure-storage-limits-vm-disks.md)]

有关其他详细信息，请参阅[虚拟机大小](virtual-machines/virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

**标准存储帐户**

[!INCLUDE [azure-storage-limits-vm-disks-standard](../includes/azure-storage-limits-vm-disks-standard.md)]

**高级存储帐户**

[!INCLUDE [azure-storage-limits-vm-disks-premium](../includes/azure-storage-limits-vm-disks-premium.md)]

#### <a name="storage-resource-provider-limits"></a>存储资源提供程序限制
[!INCLUDE [azure-storage-limits-azure-resource-manager](../includes/azure-storage-limits-azure-resource-manager.md)]

### <a name="cloud-services-limits"></a>云服务限制
[!INCLUDE [azure-cloud-services-limits](../includes/azure-cloud-services-limits.md)]

### <a name="app-service-limits"></a>App Service 限制
以下 App Service 限制包括 Web Apps、Mobile Apps、API Apps 和 Logic Apps 的限制。

[!INCLUDE [azure-websites-limits](../includes/azure-websites-limits.md)]

### <a name="scheduler-limits"></a>计划程序限制
[!INCLUDE [scheduler-limits-table](../includes/scheduler-limits-table.md)]

### <a name="batch-limits"></a>Batch 限制
[!INCLUDE [azure-batch-limits](../includes/azure-batch-limits.md)]

### <a name="biztalk-services-limits"></a>BizTalk 服务限制
下表显示了 Azure BizTalk 服务的限制。

[!INCLUDE [biztalk-services-service-limits](../includes/biztalk-services-service-limits.md)]

### <a name="documentdb-limits"></a>DocumentDB 限制
[!INCLUDE [azure-documentdb-limits](../includes/azure-documentdb-limits.md)]

[可联系 Azure 支持部门调整](documentdb/documentdb-increase-limits.md)带星号 (*) 的配额。

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

### <a name="monitoring-limits"></a>监视限制
[!INCLUDE [monitoring-limits](../includes/monitoring-limits.md)]

### <a name="notification-hub-service-limits"></a>通知中心服务限制
[!INCLUDE [notification-hub-limits](../includes/notification-hub-limits.md)]

### <a name="event-hubs-limits"></a>事件中心限制
[!INCLUDE [azure-servicebus-limits](../includes/event-hubs-limits.md)]

### <a name="service-bus-limits"></a>服务总线限制
[!INCLUDE [azure-servicebus-limits](../includes/service-bus-quotas-table.md)]

### <a name="iot-hub-limits"></a>IoT 中心限制
[!INCLUDE [azure-iothub-limits](../includes/iot-hub-limits.md)]

### <a name="data-factory-limits"></a>数据工厂限制
[!INCLUDE [azure-data-factory-limits](../includes/azure-data-factory-limits.md)]

### <a name="data-lake-analytics-limits"></a>数据湖分析限制
[!INCLUDE [azure-data-lake-analytics-limits](../includes/azure-data-lake-analytics-limits.md)]

### <a name="stream-analytics-limits"></a>流分析限制
[!INCLUDE [stream-analytics-limits-table](../includes/stream-analytics-limits-table.md)]

### <a name="active-directory-limits"></a>Active Directory 限制
[!INCLUDE [AAD-service-limits](../includes/active-directory-service-limits-include.md)]

### <a name="azure-remoteapp-limits"></a>Azure RemoteApp 限制
[!INCLUDE [azure-remoteapp-limits](../includes/azure-remoteapp-limits.md)]

### <a name="storsimple-system-limits"></a>StorSimple 系统限制
[!INCLUDE [storsimple-limits-table](../includes/storsimple-limits-table.md)]

### <a name="operational-insights-limits"></a>Operational Insights 限制
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

### <a name="multi-factor-authentication"></a>Multi-Factor Authentication
[!INCLUDE [azure-mfa-service-limits](../includes/azure-mfa-service-limits.md)]

### <a name="automation-limits"></a>自动化限制
[!INCLUDE [automation-limits](../includes/azure-automation-service-limits.md)]

### <a name="sql-database-limits"></a>SQL 数据库限制
有关 Azure SQL 数据库限制，请参阅 [SQL 数据库资源限制](sql-database/sql-database-resource-limits.md)。

## <a name="see-also"></a>另请参阅
[Understanding Azure Limits and Increases](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)（了解 Azure 的限制和增加）

[Azure 的虚拟机和云服务大小](virtual-machines/virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[云服务的大小](cloud-services/cloud-services-sizes-specs.md)




<!--HONumber=Nov16_HO3-->


