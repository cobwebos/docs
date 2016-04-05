<properties
	pageTitle="Azure 订阅和服务限制、配额和约束"
	description="提供常见的 Azure 订阅和服务限制、配额和约束的列表。这包括有关如何增加限制以及最大值的信息。"
	services=""
	documentationCenter=""
	authors="rothja"
	manager="jeffreyg"
	editor="monicar"/>

<tags
	ms.service="multiple"
	ms.date="03/07/2016"
	wacn.date=""/>

# Azure 订阅和服务限制、配额和约束

## 概述

本文档指定一些最常见的 Microsoft Azure 限制。请注意，本文档目前未涵盖所有 Azure 服务。一段时间后，将展开并更新这些限制以包含多个平台。

> [AZURE.NOTE] 如果想要提高**默认限制**之上的限制，可以[打开免费的联机客户支持请求](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。无法将这些限制提高到超过下表中的**最大限制**值。如果没有任何**最大限制**列，则指定的资源不具有可调整的限制。

## 限制和 Azure 资源管理器

现在可以将多个 Azure 资源合并到单个 Azure 资源组中。在使用资源组时，以前针对全局的限制会通过 Azure 资源管理器在区域级别进行管理。有关 Azure 资源组的详细信息，请参阅[使用资源组管理 Azure 资源](/documentation/articles/resource-group-portal)。

在下面的限制中，添加了一个新表以反映在使用 Azure 资源管理器时限制中的任何差异。例如，会存在一个**订阅限制**表和一个**订阅数限制 - Azure 资源管理器**表。如果某个限制同时适用于这两种方案，它将仅显示在第一个表中。除非另有说明，否则限制是跨所有区域的全局限制。

> [AZURE.NOTE] 请务必强调 Azure 资源组中的资源配额是您的订阅可以访问的每个区域，而不像服务管理配额那样是可以访问的每个订阅。我们来使用核心配额作为示例。如果您需要根据对核心的支持请求增加配额，则需要决定您想要在哪个区域中使用多少核心，然后针对您希望的 Azure 资源组核心配额的数量和区域进行特定请求。因此，如果您需要在西欧使用 30 个核心以在那里运行您的应用程序，则应专门在西欧请求 30 个核心。但这不会增加您在任何其他区域的核心配额 -- 仅西欧会有 30 个核心配额。
<!-- -->
因此，你可能会发现考虑决定你在任何一个区域中的工作负荷所需的 Azure 资源组配额数量，以及请求你考虑在其中进行部署的每个区域的数量很有用。请参阅[部署问题疑难解答](/documentation/articles/resource-group-deploy-debug##authentication-subscription-role-and-quota-issues)，了解有关发现你特定区域的当前配额的更多帮助。


## 服务特定的限制

- [Active Directory](#active-directory-limits)
- [API 管理](#api-management-limits)
- [App Service](#app-service-limits)
- [Application Insights](#application-insights-limits)
- [Azure Redis Cache](#azure-redis-cache-limits)
- [Azure RemoteApp](#azure-remoteapp-limits)
- [备份](#backup-limits)
- [批处理](#batch-limits)
- [BizTalk 服务](#biztalk-services-limits)
- [CDN](#cdn-limits)
- [云服务](#cloud-services-limits)
- [Data Factory](#data-factory-limits)
- [DNS](#dns-limits)
- [DocumentDB](#documentdb-limits)
- [IoT 中心](#iot-hub-limits)
- [密钥保管库](#key-vault-limits)
- [媒体服务](#media-services-limits)
- [Mobile Engagement](#mobile-engagement-limits)
- [移动服务](#mobile-services-limits)
- [Multi-Factor Authentication](#multi-factor-authentication)
- [联网](#networking-limits)
- [通知中心服务](#notification-hub-service-limits)
- [操作见解](#operational-insights-limits)
- [资源组](#resource-group-limits)
- [计划程序](#scheduler-limits)
- [搜索](#search-limits)
- [服务总线](#service-bus-limits)
- [站点恢复](#site-recovery-limits)
- [SQL 数据库](#sql-database-limits)
- [存储](#storage-limits)
- [StorSimple 系统](#storsimple-system-limits)
- [流分析](#stream-analytics-limits)
- [订阅](#subscription-limits)
- [流量管理器](#traffic-manager-limits)
- [虚拟机](#virtual-machines-limits)


### 订阅限制
#### 订阅限制
[AZURE.INCLUDE [azure-subscription-limits](../includes/azure-subscription-limits.md)]

#### 订阅限制 - Azure Resource Manager

使用 Azure 资源管理器和 Azure 资源组时，以下限制适用。未使用 Azure 资源管理器更改的限制不会在下面列出。请参阅上表了解这些限制。

[AZURE.INCLUDE [azure-subscription-limits-azure-resource-manager](../includes/azure-subscription-limits-azure-resource-manager.md)]


### 资源组限制

[AZURE.INCLUDE [azure-resource-groups-limits](../includes/azure-resource-groups-limits.md)]


### 虚拟机限制
#### 虚拟机限制
[AZURE.INCLUDE [azure-virtual-machines-limits](../includes/azure-virtual-machines-limits.md)]


#### 虚拟机限制 - Azure Resource Manager

使用 Azure 资源管理器和 Azure 资源组时，以下限制适用。未使用 Azure 资源管理器更改的限制不会在下面列出。请参阅上表了解这些限制。

[AZURE.INCLUDE [azure-virtual-machines-limits-azure-resource-manager](../includes/azure-virtual-machines-limits-azure-resource-manager.md)]


### 网络限制

[AZURE.INCLUDE [expressroute-limits](../includes/expressroute-limits.md)]

#### 网络限制
[AZURE.INCLUDE [azure-virtual-network-limits](../includes/azure-virtual-network-limits.md)]

#### 流量管理器限制

[AZURE.INCLUDE [traffic-manager-limits](../includes/traffic-manager-limits.md)]

#### DNS 限制

[AZURE.INCLUDE [dns-limits](../includes/dns-limits.md)]

### 存储限制

有关存储帐户限制的详细信息，请参阅 [Azure 存储空间可伸缩性和性能目标](/documentation/articles/storage-scalability-targets)。

#### 存储服务限制

[AZURE.INCLUDE [azure-storage-limits](../includes/azure-storage-limits.md)]

#### 虚拟机磁盘限制

[AZURE.INCLUDE [azure-storage-limits-vm-disks](../includes/azure-storage-limits-vm-disks.md)]

有关其他详细信息，请参阅[虚拟机大小](/documentation/articles/virtual-machines-size-specs)。

**标准存储帐户**

[AZURE.INCLUDE [azure-storage-limits-vm-disks-standard](../includes/azure-storage-limits-vm-disks-standard.md)]

**高级存储帐户**

[AZURE.INCLUDE [azure-storage-limits-vm-disks-premium](../includes/azure-storage-limits-vm-disks-premium.md)]

#### 存储资源提供程序限制

[AZURE.INCLUDE [azure-storage-limits-azure-resource-manager](../includes/azure-storage-limits-azure-resource-manager.md)]


### 云服务限制

[AZURE.INCLUDE [azure-cloud-services-limits](../includes/azure-cloud-services-limits.md)]


### App Service 限制
以下 App Service 限制包括 Web Apps、Mobile Apps、API Apps 和 Logic Apps 的限制。

[AZURE.INCLUDE [azure-websites-limits](../includes/azure-websites-limits.md)]

### 计划程序限制

[AZURE.INCLUDE [scheduler-limits-table](../includes/scheduler-limits-table.md)]

### Batch 限制

[AZURE.INCLUDE [azure-batch-limits](../includes/azure-batch-limits.md)]

###BizTalk 服务限制
下表显示了 Azure BizTalk 服务的限制。

[AZURE.INCLUDE [biztalk-services-service-limits](../includes/biztalk-services-service-limits.md)]


### DocumentDB 限制

[AZURE.INCLUDE [azure-documentdb-limits](../includes/azure-documentdb-limits.md)]


### Mobile Engagement 限制

[AZURE.INCLUDE [azure-mobile-engagement-limits](../includes/azure-mobile-engagement-limits.md)]


### 搜索限制

定价层决定了搜索服务的容量和限制。层包括：

- **免费**：多租户服务，与其他 Azure 订户共享，仅用于评估和小型开发项目。
- **基本（预览）**：为小规模生产工作负荷提供专用计算资源。此层目前处于预览期，以优惠价提供。
- **标准（S1 和 S2）**：面向生产工作负荷。我们可根据要求提供更大的容量版本 (S2)，如有需要，请向 azuresearch_contact@microsoft.com) 发送电子邮件）。

[AZURE.INCLUDE [azure-search-limits-all](../includes/azure-search-limits-all.md)]

若要了解其他限制，如文档大小、密钥数、请求数和响应数，请参阅 [Azure 搜索中的服务限制](/documentation/articles/search-limits-quotas-capacity)。

### 媒体服务限制

[AZURE.INCLUDE [azure-mediaservices-limits](../includes/azure-mediaservices-limits.md)]

### CDN 限制

[AZURE.INCLUDE [cdn-limits](../includes/cdn-limits.md)]

### 移动服务限制

[AZURE.INCLUDE [mobile-services-limits](../includes/mobile-services-limits.md)]

### 通知中心服务限制

[AZURE.INCLUDE [notification-hub-limits](../includes/notification-hub-limits.md)]


### 服务总线限制

[AZURE.INCLUDE [azure-servicebus-limits](../includes/service-bus-quotas-table.md)]

### IoT 中心限制

[AZURE.INCLUDE [azure-iothub-limits](../includes/iot-hub-limits.md)]

### 数据工厂限制

[AZURE.INCLUDE [azure-data-factory-limits](../includes/azure-data-factory-limits.md)]

### 数据湖分析限制
[AZURE.INCLUDE [azure-data-lake-analytics-limits](../includes/azure-data-lake-analytics-limits.md)]

### 流分析限制

[AZURE.INCLUDE [stream-analytics-limits-table](../includes/stream-analytics-limits-table.md)]

### Active Directory 限制

[AZURE.INCLUDE [AAD-service-limits](../includes/active-directory-service-limits-include.md)]


### Azure RemoteApp 限制

[AZURE.INCLUDE [azure-remoteapp-limits](../includes/azure-remoteapp-limits.md)]

### StorSimple 系统限制

[AZURE.INCLUDE [storsimple-limits-table](../includes/storsimple-limits-table.md)]


### Operational Insights 限制

[AZURE.INCLUDE [operational-insights-limits](../includes/operational-insights-limits.md)]

### 备份限制

[AZURE.INCLUDE [azure-backup-limits](../includes/azure-backup-limits.md)]

### 站点恢复限制

[AZURE.INCLUDE [site-recovery-limits](../includes/site-recovery-limits.md)]

### Application Insights 限制

[AZURE.INCLUDE [application-insights-limits](../includes/application-insights-limits.md)]

### API 管理限制

[AZURE.INCLUDE [api-management-service-limits](../includes/api-management-service-limits.md)]

### Azure Redis 缓存限制

[AZURE.INCLUDE [redis-cache-service-limits](../includes/redis-cache-service-limits.md)]

### 密钥保管库限制

[AZURE.INCLUDE [key-vault-limits](../includes/key-vault-limits.md)]

### Multi-Factor Authentication
[AZURE.INCLUDE [azure-mfa-service-limits](../includes/azure-mfa-service-limits.md)]

### SQL 数据库限制

有关 Azure SQL 数据库限制，请参阅 [SQL 数据库资源限制](/documentation/articles/sql-database-resource-limits)。

## 另请参阅

[了解 Azure 限制和增加](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)

[Azure 的虚拟机和云服务大小](/documentation/articles/virtual-machines/virtual-machines-size-specs)

[云服务的大小](/documentation/articles/cloud-services-sizes-specs)

<!---HONumber=Mooncake_0328_2016-->