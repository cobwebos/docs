---
title: "Azure 政府可用服务 | Microsoft Docs"
description: "提供有关 Azure 政府版中可用服务的概述"
services: azure-government
cloud: gov
documentationcenter: 
author: smichelotti
manager: liki
ms.assetid: a453a23c-bc0f-4203-9075-0f579dea7e23
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 02/13/2017
ms.author: stemi
translationtype: Human Translation
ms.sourcegitcommit: ffe3991f68bbd255ff6f4ffedbd6638f32897d1a
ms.openlocfilehash: 914870f59b488716bb283162078535849f732018
ms.lasthandoff: 02/21/2017


---
# <a name="available-services-on-azure-government"></a>Azure 政府上的可用服务
Azure 政府版正在不断扩展可用的服务。  这些服务使用 Azure 公共中使用的相同代码部署。  本部分介绍 Azure 政府上当前可用的服务，包括两种关键类型的信息：

* **变体：**变体产生的原因是因为尚未部署功能，或某些属性在政府版环境中是唯一的（例如 URL）。  
* **注意事项：**特定于政府版的实现详细信息，可用于确保数据不会超出合规性范围。

可在常规文档中找到了解这些服务所需的其他所有内容。

有关服务的最新列表，请参阅[产品（按区域）](https://azure.microsoft.com/regions/services/)。 

在下表中，指定为启用了资源管理器的服务具有可以使用 PowerShell 管理的资源提供程序。 有关资源管理器提供程序、API 版本和架构的详细信息，请参阅 [此处](../azure-resource-manager/resource-manager-supported-services.md)。 指定为在门户中可用的资源提供程序可以在 [Azure 政府门户](https://portal.azure.us/)中进行管理。 


## <a name="computedocumentation-government-computemd"></a>[计算](documentation-government-compute.md)

| 服务 | 已启用资源管理器 | 门户 |
| --- | --- | --- |
| [虚拟机](documentation-government-compute.md#virtual-machines) | 是 | 是 |
| 批处理 | 是 | 是 |
| 云服务 | 是 | 是 |
| Service Fabric | 是 | 是 |
| VM 规模集 | 是 | 是 |


## <a name="networkingdocumentation-government-networkingmd"></a>[网络](documentation-government-networking.md)

| 服务 | 已启用资源管理器 | 门户 |
| --- | --- | --- |
| [ExpressRoute](documentation-government-networking.md#expressroute-private-connectivity) | 是 | 是 |
| 虚拟网络 | 是 | 是 |
| [负载均衡器](documentation-government-networking.md#support-for-load-balancer) | 是 | 是 |
| [流量管理器](documentation-government-networking.md#support-for-traffic-manger) | 是 | 是 |
| [VPN 网关](documentation-government-networking.md#support-for-vpn-gateway) | 是 | 是 |
| 应用程序网关 | 是 | 是 |
| ExpressRoute | 是 | 是 |



## <a name="storagedocumentation-government-services-storagemd"></a>[存储](documentation-government-services-storage.md)

| 服务 | 已启用资源管理器 | 门户 |
| --- | --- | --- |
| [存储 - Blobs](documentation-government-services-storage.md#azure-storage) | 是 | 是 |
| [存储 - 表](documentation-government-services-storage.md#azure-storage) | 是 | 是 |
| [存储 - 队列](documentation-government-services-storage.md#azure-storage) | 是 | 是 |
| [存储 - 文件](documentation-government-services-storage.md#azure-storage) | 是 | 是 |
| [存储 - 磁盘](documentation-government-services-storage.md#azure-storage) | 是 | 是 |
| [StorSimple](documentation-government-services-storage.md) | 是 | 是 |
| [备份](documentation-government-services-storage.md#azure-storage) | 是 | 是 |
| [Site Recovery](documentation-government-services-storage.md#azure-storage) | 是 | 是 |
| [导入/导出](documentation-government-services-storage.md#azure-storage) | 是 | 否 |



## <a name="web--mobiledocumentation-government-services-webandmobilemd"></a>[Web 与移动](documentation-government-services-webandmobile.md)

| 服务 | 已启用资源管理器 | 门户 |
| --- | --- | --- |
| [应用服务 - Web 应用](documentation-government-services-webandmobile.md#app-services) | 是 | 是 |
| [应用服务 - API 应用](documentation-government-services-webandmobile.md#app-services) | 是 | 是 |
| [应用服务 - 移动应用](documentation-government-services-webandmobile.md#app-services) | 是 | 是 |
| 媒体服务 | 是 | 是 |


## <a name="databasesdocumentation-government-services-databasemd"></a>[数据库](documentation-government-services-database.md)

| 服务 | 已启用资源管理器 | 门户 |
| --- | --- | --- |
| [SQL 数据库](documentation-government-services-database.md#sql-database) | 是 | 是 |
| SQL 数据仓库 | 是 | 是 |
| SQL Server Stretch Database | 是 | 是 |
| [Redis 缓存](documentation-government-services-database.md#azure-redis-cache) | 是 | 是 |




## <a name="internet-of-things-iot"></a>物联网 (IoT)

| 服务 | 已启用资源管理器 | 门户 |
| --- | --- | --- |
| 事件中心 | 是 | 是 |
| 通知中心 | 否 | 否（转到[旧门户](https://manage.windowsazure.us/)） |


## <a name="enterprise-integration"></a>企业集成

| 服务 | 已启用资源管理器 | 门户 |
| --- | --- | --- |
| 服务总线 | 是 | 是 |
| [StorSimple](documentation-government-services-storage.md) | 是 | 是 |
| SQL Server Stretch Database | 是 | 是 |



## <a name="security--identitydocumentation-government-services-securityandidentitymd"></a>[安全性 + 标识](documentation-government-services-securityandidentity.md)

| 服务 | 已启用资源管理器 | 门户 |
| --- | --- | --- |
| Azure Active Directory | 是 | 是 |
| [密钥保管库](documentation-government-services-securityandidentity.md#key-vault) | 是 | 否（即将包括） |
| 多重身份验证 | 是 | 是 |
| InTune | 是 | 否 |


## <a name="intelligence--analytics"></a>智能 + 分析

| 服务 | 已启用资源管理器 | 门户 |
| --- | --- | --- |
| Power BI | 是 | 否 |
| HDInsight | 是 | 是 |



## <a name="monitoring--managementdocumentation-government-services-monitoringandmanagementmd"></a>[监视 + 管理](documentation-government-services-monitoringandmanagement.md)

| 服务 | 已启用资源管理器 | 门户 |
| --- | --- | --- |
| [自动化](documentation-government-services-monitoringandmanagement.md#automation) | 是 | 是 |
| [备份](documentation-government-services-backup.md) | 是 | 是 |
| [Log Analytics](documentation-government-services-monitoringandmanagement.md#log-analytics) | 是 | 是 |
| [Site Recovery](documentation-government-services-monitoringandmanagement.md#site-recovery) | 是 | 是 |
| 计划程序 | 是 | 否 |
| 监视和诊断 | 是 | 是 |




## <a name="next-steps"></a>后续步骤
有关补充信息和更新，请订阅 [Microsoft Azure 政府版博客](https://blogs.msdn.microsoft.com/azuregov/)。


