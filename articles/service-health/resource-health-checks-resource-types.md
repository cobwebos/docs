---
title: 通过 Azure 资源运行状况支持的资源类型 | Microsoft Docs
description: 通过 Azure 资源运行状况支持的资源类型
ms.topic: conceptual
ms.date: 01/29/2019
ms.openlocfilehash: ec194287e8a8e06d3bb14c4b202cc2f11063d1dd
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2020
ms.locfileid: "78925659"
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

## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts
|执行的检查|
|---|
|<ul><li>批处理帐户是否已启动并正在运行？</li><li>此批处理帐户是否超出了池配额？</li></ul>|

## <a name="microsoftcacheredis"></a>Microsoft.Cache/Redis
|执行的检查|
|---|
|<ul><li>所有缓存节点是否都已启用并正在运行？</li><li>是否可从数据中心内访问缓存？</li><li>缓存是否已达到最大连接数？</li><li> 缓存是否已用完可用内存？ </li><li>缓存是否遇到大量页面错误？</li><li>缓存是否负载过大？</li></ul>|

## <a name="microsoftcdnprofile"></a>Microsoft.CDN/profile
|执行的检查|
|---|
|<ul> <li>CDN 配置操作是否可访问补充门户？</li><li>CDN 终结点是否存在持续的传送问题？</li><li>用户是否可以更改其 CDN 资源的配置？</li><li>配置更改是否按预期速率传播？</li><li>用户是否可以使用 Azure 门户、PowerShell 或 API 管理 CDN 配置？</li> </ul>|

## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.classiccompute/virtualmachines
|执行的检查|
|---|
|<ul><li>主机服务器是否已启动并正在运行？</li><li>主机 OS 启动是否已完成？</li><li>是否已配置并启动虚拟机容器？</li><li>主机和存储帐户之间是否有网络连接？</li><li>来宾 OS 启动是否已完成？</li><li>是否存在持续的计划内维护？</li><li>主机硬件是否降级并预测到很快会失败？</li></ul>|

## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.cognitiveservices/accounts
|执行的检查|
|---|
|<ul><li>是否可从数据中心内访问帐户？</li><li>认知服务资源提供程序是否可用？</li><li>认知服务在相应区域中是否可用？</li><li>是否可以在保存资源元数据的存储帐户上执行读取操作？</li><li>是否已达到 API 调用配额？</li><li>是否已达到 API 调用读取限制？</li></ul>|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.compute/virtualmachines
|执行的检查|
|---|
|<ul><li>托管此虚拟机的服务器是否已启动并运行？</li><li>主机 OS 启动是否已完成？</li><li>是否已配置并启动虚拟机容器？</li><li>主机和存储帐户之间是否有网络连接？</li><li>来宾 OS 启动是否已完成？</li><li>是否存在持续的计划内维护？</li><li>主机硬件是否降级并预测到很快会失败？</li></ul>|

## <a name="microsoftdatafactoryfactories"></a>datafactory/工厂
|执行的检查|
|---|
|<ul><li>是否有管道运行失败？</li><li>承载数据工厂的群集是否正常？</li></ul>|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.datalakeanalytics/accounts
|执行的检查|
|---|
|<ul><li>是否有用户无法提交或列出 Data Lake Analytics 作业？</li><li>Data Lake Analytics 作业是否因系统错误而无法完成？</li></ul>|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.datalakestore/accounts
|执行的检查|
|---|
|<ul><li>是否有用户无法将数据上传到 Data Lake Store？</li><li>是否有用户无法从 Data Lake Store 下载数据？</li></ul>|

## <a name="microsoftdatamigrationservices"></a>Microsoft.datamigration/services
|执行的检查|
|---|
|<ul><li>数据库迁移服务是否无法预配？</li><li>数据库迁移服务是否由于不活动或用户请求而停止？</li></ul>|

## <a name="microsoftdatashareaccounts"></a>DataShare/帐户
|执行的检查|
|---|
|<ul><li>数据共享帐户是否已启动并正在运行？</li><li>托管数据共享的群集是否可用？</li></ul>|

## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servers
|执行的检查|
|---|
|<ul><li>服务器是否因维护而不可用？</li><li>由于重新配置，服务器是否不可用？</li></ul>|

## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers
|执行的检查|
|---|
|<ul><li>服务器是否因维护而不可用？</li><li>由于重新配置，服务器是否不可用？</li></ul>|

## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers
|执行的检查|
|---|
|<ul><li>服务器是否因维护而不可用？</li><li>由于重新配置，服务器是否不可用？</li></ul>|

## <a name="microsoftdevicesiothubs"></a>Microsoft.devices/iothubs
|执行的检查|
|---|
|<ul><li>IoT 中心在正常运行吗？</li></ul>|

## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.documentdb/databaseAccounts
|执行的检查|
|---|
|<ul><li>是否存在因 Azure Cosmos DB 服务不可用而未处理的数据库或集合请求？</li><li>是否存在因 Azure Cosmos DB 服务不可用而未处理的文档请求？</li></ul>|

## <a name="microsofteventhubnamespaces"></a>node.js/命名空间
|执行的检查|
|---|
|<ul><li>事件中心命名空间是否遇到用户生成的错误？</li><li>当前是否正在升级事件中心命名空间？</li></ul>|

## <a name="microsofthdinsightclusters"></a>microsoft hdinsight/群集
|执行的检查|
|---|
|<ul><li>在 HDInsight 群集上是否提供核心服务？</li><li>HDInsight 群集是否可以访问静态 BYOK 加密的密钥？</li></ul>|

## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults
|执行的检查|
|---|
|<ul><li>向 Key Vault 发出的请求是否因 Azure KeyVault 平台问题而失败？</li><li>向 Key Vault 发出的请求是否因客户发出的请求过多而受限制？</li></ul>|

## <a name="microsoftmachinelearningwebservices"></a>Microsoft.MachineLearning/webServices
|执行的检查|
|---|
|<ul><li>Web 服务是否已启动并正在运行？</li></ul>|

## <a name="microsoftmediamediaservices"></a>Microsoft. Media/windowsazure.mediaservices
|执行的检查|
|---|
|<ul><li>媒体服务是否已启动并正在运行？</li></ul>|

## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.network/applicationgateways
|执行的检查|
|---|
|<ul><li>应用程序网关性能是否下降？</li><li>应用程序网关是否可用？</li></ul>|

## <a name="microsoftnetworkconnections"></a>Microsoft.network/connections
|执行的检查|
|---|
|<ul><li>VPN 隧道是否已连接？</li><li>是否存在连接配置冲突？</li><li>是否已正确配置预共享密钥？</li><li>是否可访问 VPN 本地设备？</li><li>IPSec/IKE 安全策略中是否有不匹配内容？</li><li>S2S VPN 连接是设置正确还是处于连接失败状态？</li><li>VNET 到 VNET 连接是设置正确还是处于连接失败状态？</li></ul>|

## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.network/expressroutecircuits
|执行的检查|
|---|
|<ul><li>ExpressRoute 线路是否正常？</li></ul>|

## <a name="microsoftnetworkfrontdoors"></a>Microsoft.network/frontdoors
|执行的检查|
|---|
|<ul><li>前门后端是否响应了运行状况探测的错误？</li><li>配置更改是否延迟？</li></ul>|

## <a name="microsoftnetworkloadbalancers"></a>LoadBalancers/网络
|执行的检查|
|---|
|<ul><li>负载平衡终结点是否可用？</li></ul>|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.network/virtualNetworkGateways
|执行的检查|
|---|
|<ul><li>是否可从 Internet 访问 VPN 网关？</li><li>VPN 网关是否处于备用模式？</li><li>VPN 服务是否正在网关上运行？</li></ul>|

## <a name="microsoftnotificationhubsnamespace"></a>Microsoft.NotificationHubs/namespace
|执行的检查|
|---|
|<ul><li>是否可以在命名空间上执行注册、安装或发送等运行时操作？</li></ul>|

## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft.operationalinsights/workspaces
|执行的检查|
|---|
|<ul><li>工作区是否存在索引延迟？</li></ul>|

## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/Capacities
|执行的检查|
|---|
|<ul><li>容量资源是否已启动且正在运行？</li><li>所有工作负荷是否都已启动且正在运行？</li></ul>|

## <a name="microsoftpowerbiworkspacecollections"></a>Microsoft.PowerBI/workspaceCollections
|执行的检查|
|---|
|<ul><li>主机 OS 是否已启动并正在运行？</li><li>是否可从数据中心外部访问 workspaceCollection？</li><li>Power BI 的资源提供程序是否可用？</li><li>Power BI 服务在相应区域中是否可用？</li></ul>|

## <a name="microsoftsearchsearchservices"></a>Microsoft.search/searchServices
|执行的检查|
|---|
|<ul><li>是否可在群集上执行诊断操作？</li></ul>|

## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces
|执行的检查|
|---|
|<ul><li>客户是否遇到用户生成的服务总线错误？</li><li>用户是否遇到由于服务总线命名空间升级导致的暂时性错误增加？</li></ul>|

## <a name="microsoftsqlmanagedinstancesdatabases"></a>ManagedInstances/数据库
|执行的检查|
|---|
|<ul><li>数据库是否已启动并正在运行？</li></ul>|

## <a name="microsoftsqlserverdatabases"></a>Microsoft .SQL/服务器/数据库
|执行的检查|
|---|
|<ul><li>是否存在到数据库的登录？</li></ul>|

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

## <a name="next-steps"></a>后续步骤
-  请参阅 [Azure 服务运行状况仪表板简介](service-health-overview.md)和 [Azure 资源运行状况简介](resource-health-overview.md)，了解更多相关信息。 
-  [有关 Azure 资源运行状况的常见问题解答](resource-health-faq.md)
- 设置警报，以便收到运行状况问题的通知。 有关详细信息，请参阅[配置服务运行状况事件的警报](../azure-monitor/platform/alerts-activity-log-service-notifications.md)。 
