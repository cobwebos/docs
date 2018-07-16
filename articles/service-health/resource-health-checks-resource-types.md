---
title: 通过 Azure 资源运行状况支持的资源类型 | Microsoft Docs
description: 通过 Azure 资源运行状况支持的资源类型
services: Resource health
documentationcenter: ''
author: BernardoAMunoz
manager: ''
editor: ''
ms.assetid: 85cc88a4-80fd-4b9b-a30a-34ff3782855f
ms.service: service-health
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Supportability
ms.date: 10/09/2017
ms.author: BernardoAMunoz
ms.openlocfilehash: 4e1f6cb8a383569b80386e090b15c59988510d0f
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2018
ms.locfileid: "37112837"
---
# <a name="resource-types-and-health-checks-in-azure-resource-health"></a>Azure 资源运行状况中的资源类型和运行状况检查
下面是通过资源运行状况执行的所有检查（按资源类型）的完整列表。

## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers
|执行的检查|
|---|
|<ul><li>服务器是否正常运行？</li><li>服务器是否内存不足？</li><li>服务器是否正在启动？</li><li>服务器是否正在恢复？</li></ul>|

## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service
|执行的检查|
|---|
|<ul><li>API 管理服务是否已启动并运行？</li></ul>|

## <a name="microsoftcacheredisredis"></a>Microsoft.CacheRedis/Redis
|执行的检查|
|---|
|<ul><li>所有缓存节点是否都已启用并正在运行？</li><li>是否可从数据中心内访问缓存？</li><li>缓存是否已达到最大连接数？</li><li> 缓存是否已用完可用内存？ </li><li>缓存是否遇到大量页面错误？</li><li>缓存是否负载过大？</li></ul>|

## <a name="microsoftcdnprofile"></a>Microsoft.CDN/profile
|执行的检查|
|---|
|<ul> <li>是否有终结点已停止、被删除或未正确配置？</li><li>CDN 配置操作是否可访问补充门户？</li><li>CDN 终结点是否存在持续的传送问题？</li><li>用户是否可以更改其 CDN 资源的配置？</li><li>配置更改是否按预期速率传播？</li><li>用户是否可以使用 Azure 门户、PowerShell 或 API 管理 CDN 配置？</li> </ul>|

## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.classiccompute/virtualmachines
|执行的检查|
|---|
|<ul><li>主机服务器是否已启动并正在运行？</li><li>主机 OS 启动是否已完成？</li><li>是否已配置并启动虚拟机容器？</li><li>主机和存储帐户之间是否有网络连接？</li><li>来宾 OS 启动是否已完成？</li><li>是否存在持续的计划内维护？</li></ul>|

## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.cognitiveservices/accounts
|执行的检查|
|---|
|<ul><li>是否可从数据中心内访问帐户？</li><li>认知服务资源提供程序是否可用？</li><li>认知服务在相应区域中是否可用？</li><li>是否可以在保存资源元数据的存储帐户上执行读取操作？</li><li>是否已达到 API 调用配额？</li><li>是否已达到 API 调用读取限制？</li></ul>|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.compute/virtualmachines
|执行的检查|
|---|
|<ul><li>托管此虚拟机的服务器是否已启动并运行？</li><li>主机 OS 启动是否已完成？</li><li>是否已配置并启动虚拟机容器？</li><li>主机和存储帐户之间是否有网络连接？</li><li>来宾 OS 启动是否已完成？</li><li>是否存在持续的计划内维护？</li></ul>|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.datalakeanalytics/accounts
|执行的检查|
|---|
|<ul><li>是否有用户无法提交或列出 Data Lake Analytics 作业？</li><li>是否因系统错误而无法完成 Data Lake Analytics 作业？</li></ul>|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.datalakestore/accounts
|执行的检查|
|---|
|<ul><li>是否有用户无法将数据上传到 Data Lake Store？</li><li>是否有用户无法从 Data Lake Store 下载数据？</li></ul>|

## <a name="microsoftdevicesiothubs"></a>Microsoft.devices/iothubs

|执行的检查|
|---|
|<ul><li>IoT 中心在正常运行吗？</li></ul>|

## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.documentdb/databaseAccounts
|执行的检查|
|---|
|<ul><li>是否存在因 Azure Cosmos DB 服务不可用而未处理的数据库或集合请求？</li><li>是否存在因 Azure Cosmos DB 服务不可用而未处理的文档请求？</li></ul>|

## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults
|执行的检查|
|---|
|<ul><li>向 Key Vault 发出的请求是否因 Azure KeyVault 平台问题而失败？</li><li>向 Key Vault 发出的请求是否因客户发出的请求过多而受限制？</li></ul>|

## <a name="microsoftnetworkconnections"></a>Microsoft.network/connections
|执行的检查|
|---|
|<ul><li>VPN 隧道是否已连接？</li><li>是否存在连接配置冲突？</li><li>是否已正确配置预共享密钥？</li><li>是否可访问 VPN 本地设备？</li><li>IPSec/IKE 安全策略中是否有不匹配内容？</li><li>S2S VPN 连接是设置正确还是处于连接失败状态？</li><li>VNET 到 VNET 连接是设置正确还是处于连接失败状态？</li></ul>|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.network/virtualNetworkGateways
|执行的检查|
|---|
|<ul><li>是否可从 Internet 访问 VPN 网关？</li><li>VPN 网关是否处于备用模式？</li><li>VPN 服务是否正在网关上运行？</li></ul>|

## <a name="microsoftnotificationhubsnamespace"></a>Microsoft.NotificationHubs/namespace
|执行的检查|
|---|
|<ul><li> 是否可以在命名空间上执行注册、安装或发送等运行时操作？</li></ul>|

## <a name="microsoftpowerbiworkspacecollections"></a>Microsoft.PowerBI/workspaceCollections
|执行的检查|
|---|
|<ul><li>主机 OS 是否已启动并正在运行？</li><li>是否可从数据中心外部访问 workspaceCollection？</li><li>PowerBI 资源提供程序是否可用？</li><li>PowerBI 服务在相应区域中是否可用？</li></ul>|

## <a name="microsoftsearchsearchservices"></a>Microsoft.search/searchServices
|执行的检查|
|---|
|<ul><li>是否可在群集上执行诊断操作？</li></ul>|

## <a name="microsoftsqlserverdatabase"></a>Microsoft.SQL/Server/database
|执行的检查|
|---|
|<ul><li> 是否存在到数据库的登录？</li></ul>|

## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts
|执行的检查|
|---|
|<ul><li>读取存储帐户中数据的请求是否因 Azure 存储平台问题而失败？</li><li>将数据写入存储帐户的请求是否因 Azure 存储平台问题而失败？</li><li>存储帐户所在的存储群集是否不可用？</li></ul>|

## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs
|执行的检查|
|---|
|<ul><li>执行作业的所有主机是否都已启动并正在运行？</li><li>是否未能启动作业？</li><li>是否存在持续的运行时升级？</li><li>作业是否处于预期状态（如正在运行或被客户停止）？</li><li>作业是否遇到内存不足异常？</li><li>是否存在持续的计划内计算更新？</li><li>执行管理器（控制计划）是否可用？</li></ul>|

## <a name="microsoftwebserverfarms"></a>Microsoft.web/serverFarms
|执行的检查|
|---|
|<ul><li>主机服务器是否已启动并正在运行？</li><li>Internet 信息服务是否正在运行？</li><li>负载均衡器是否正在运行？</li><li>是否可从数据中心内访问应用服务计划？</li><li>为服务器场托管站点内容的存储帐户是否可用？</li></ul>|

## <a name="microsoftwebsites"></a>Microsoft.web/sites
|执行的检查|
|---|
|<ul><li>主机服务器是否已启动并正在运行？</li><li>Internet 信息服务器是否正在运行？</li><li>负载均衡器是否正在运行？</li><li>是否可从数据中心内访问 Web 应用？</li><li>托管站点内容的存储帐户是否可用？</li></ul>|

# <a name="next-steps"></a>后续步骤
-  请参阅 [Azure 服务运行状况仪表板简介](service-health-overview.md)和 [Azure 资源运行状况简介](resource-health-overview.md)，了解更多相关信息。 
-  [有关 Azure 资源运行状况的常见问题解答](resource-health-faq.md)
- 设置警报，以便收到运行状况问题的通知。 有关详细信息，请参阅[配置服务运行状况事件的警报](../monitoring-and-diagnostics/monitoring-activity-log-alerts-on-service-notifications.md)。 
