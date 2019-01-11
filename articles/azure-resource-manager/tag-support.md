---
title: 针对资源的 Azure 资源管理器标记支持
description: 显示支持标记的 Azure资源类型。 提供所有 Azure 服务的详细信息。
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 01/02/2019
ms.author: tomfitz
ms.openlocfilehash: 50ea7a2446b5560bd208b2da128fa877068ce452
ms.sourcegitcommit: da69285e86d23c471838b5242d4bdca512e73853
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/03/2019
ms.locfileid: "54000285"
---
# <a name="tag-support-for-azure-resources"></a>Azure 资源的标记支持
本文介绍某一资源类型是否支持[标记](resource-group-using-tags.md)。

## <a name="aad-domain-services"></a>AAD 域服务
| 资源类型 | 支持标记 |
| ------------- | ----------- |
| domains | 否 | 

## <a name="ad-hybrid-health-service"></a>AD 混合运行状况服务
| 资源类型 | 支持标记 |
| ------------- | ----------- |
| addsservices | 否 |
| aadsupportcases | 否 | 
| 代理 | 否 | 
| anonymousapiusers | 否 | 
| 配置 | 否 | 
| 日志 | 否 | 
| 报表 | 否 | 
| services | 否 | 
| servicehealthmetrics | 否 | 

## <a name="aks"></a>AKS
| 资源类型 | 支持标记 |
| ------------- | ----------- |
| managedClusters | 是 | 

## <a name="analysis-services"></a>Analysis Services
| 资源类型 | 支持标记 |
| ------------- | ----------- |
| servers | 是 | 

## <a name="api-hubs"></a>API 中心
| 资源类型 | 支持标记 |
| ------------- | ----------- |
| apiManagementAccounts | 否 | 
| apiManagementAccounts/apis | 否 | 
| apiManagementAccounts/connectionAcls | 否 | 
| apiManagementAccounts/connectionProviders | 否 | 
| apiManagementAccounts/connectionProviderAcls | 否 | 
| apiManagementAccounts/connections | 否 | 

## <a name="api-management"></a>API 管理
| 资源类型 | 支持标记 |
| ------------- | ----------- |
| 服务 | 是 | 

## <a name="automation"></a>自动化
| 资源类型 | 支持标记 |
| ------------- | ----------- |
| automationAccounts | 是 | 
| automationAccounts/configurations | 是 | 
| automationAccounts/jobs | 否 | 
| automationAccounts/runbooks | 是 | 
| automationAccounts/softwareUpdateConfigurations | 否 | 
| automationAccounts/webhooks | 否 | 

## <a name="azure-database-for-mariadb"></a>Azure Database for MariaDB
| 资源类型 | 支持标记 |
| ------------- | ----------- |
| servers | 是 | 
| servers/configurations | 否 |
| servers/databases | 否 |
| servers/firewallRules | 否 |
| servers/recoverableServers | 否 | 
| servers/securityAlertPolicies | 否 |
| servers/virtualNetworkRules | 否 | 

## <a name="azure-database-for-mysql"></a>Azure Database for MySQL
| 资源类型 | 支持标记 |
| ------------- | ----------- |
| servers | 是 | 
| servers/configurations | 否 |
| servers/databases | 否 |
| servers/firewallRules | 否 |
| servers/recoverableServers | 否 | 
| servers/securityAlertPolicies | 否 |
| servers/virtualNetworkRules | 否 | 

## <a name="azure-database-for-postgresql"></a>Azure Database for PostgreSQL
| 资源类型 | 支持标记 |
| ------------- | ----------- |
| servers | 是 | 
| servers/advisors | 否 | 
| servers/configurations | 否 |
| servers/databases | 否 |
| servers/firewallRules | 否 |
| servers/queryTexts | 否 | 
| servers/recoverableServers | 否 | 
| servers/securityAlertPolicies | 否 |
| servers/topQueryStatistics | 否 | 
| servers/virtualNetworkRules | 否 | 
| servers/waitStatistics | 否 | 

## <a name="batch"></a>Batch
| 资源类型 | 支持标记 |
| ------------- | ----------- |
| batchAccounts | 是 | 

## <a name="bing-maps"></a>必应地图
| 资源类型 | 支持标记 |
| ------------- | ----------- |
| mapApis | 是 | 

## <a name="biztalk-services"></a>BizTalk 服务
| 资源类型 | 支持标记 |
| ------------- | ----------- |
| BizTalk | 是 | 

## <a name="cache"></a>缓存
| 资源类型 | 支持标记 |
| ------------- | ----------- |
| Redis | 是 | 

## <a name="cdn"></a>CDN
| 资源类型 | 支持标记 |
| ------------- | ----------- |
| edgenodes | 否 | 
| 配置文件 | 是 | 
| profiles/endpoints | 是 | 
| profiles/endpoints/customdomains | 否 | 
| profiles/endpoints/origins | 否 | 
| validateProbe | 否 | 

## <a name="classic-compute"></a>经典计算
| 资源类型 | 支持标记 |
| ------------- | ----------- |
| domainNames | 否 | 
| domainNames/slots | 否 | 
| domainNames/slots/roles | 否 | 
| virtualMachines | 否 | 
| virtualMachines/diagnosticSettings | 否 | 
| virtualMachines/metricDefinitions | 否 | 
| virtualMachines/metrics | 否 | 

## <a name="classic-infrastructure-migrate"></a>经典的基础结构迁移
| 资源类型 | 支持标记 |
| ------------- | ----------- |
| classicInfrastructureResources | 否 | 

## <a name="classic-network"></a>经典网络
| 资源类型 | 支持标记 |
| ------------- | ----------- |
| virtualNetworks | 否 | 
| virtualNetworks/remoteVirtualNetworkPeeringProxies | 否 | 
| virtualNetworks/virtualNetworkPeerings | 否 | 

## <a name="classic-storage"></a>经典存储
| 资源类型 | 支持标记 |
| ------------- | ----------- |
| storageAccounts/services | 否 | 
| storageAccounts/services/diagnosticSettings | 否 | 

## <a name="compute"></a>计算
| 资源类型 | 支持标记 |
| ------------- | ----------- |
| availabilitySets | 是 | 
| disks | 是 | 
| images | 是 | 
| restorePointCollections | 是 | 
| restorePointCollections/restorePoints | 否 | 
| sharedVMImages | 是 | 
| sharedVMImages/versions | 是 | 
| snapshots | 是 | 
| virtualMachines | 是 | 
| virtualMachines/diagnosticSettings | 否 | 
| virtualMachines/extensions | 是 | 
| virtualMachines/metricDefinitions | 否 | 
| virtualMachineScaleSets | 是 | 
| virtualMachineScaleSets/extensions | 否 | 
| virtualMachineScaleSets/networkInterfaces | 否 | 
| virtualMachineScaleSets/publicIPAddresses | 否 | 
| virtualMachineScaleSets/virtualMachines | 否 | 
| virtualMachineScaleSets/virtualMachines/networkInterfaces | 否 | 

## <a name="container"></a>容器
| 资源类型 | 支持标记 |
| ------------- | ----------- |
| containerGroups | 是 | 

## <a name="container-instance"></a>容器实例
| 资源类型 | 支持标记 |
| ------------- | ----------- |
| containerGroups | 是 | 
| serviceAssociationLinks | 否 | 

## <a name="container-registry"></a>容器注册表
| 资源类型 | 支持标记 |
| ------------- | ----------- |
| registries | 是 | 
| registries/replications | 是 |
| registries/tasks | 是 |
| registries/webhooks | 是 |

## <a name="container-service"></a>容器服务
| 资源类型 | 支持标记 |
| ------------- | ----------- |
| containerServices | 是 | 

## <a name="cortana-analytics"></a>Cortana Analytics
| 资源类型 | 支持标记 |
| ------------- | ----------- |
| accounts | 是 | 

## <a name="cosmos-db"></a>Cosmos DB
| 资源类型 | 支持标记 |
| ------------- | ----------- |
| databaseAccounts | 是 | 
| databaseAccountNames | 否 | 

## <a name="cost-management"></a>成本管理
| 资源类型 | 支持标记 |
| ------------- | ----------- |
| 连接器 | 是 | 

## <a name="data-box"></a>Data Box
| 资源类型 | 支持标记 |
| ------------- | ----------- |
| jobs | 是 | 

## <a name="data-box-edge"></a>Data Box Edge
| 资源类型 | 支持标记 |
| ------------- | ----------- |
| DataBoxEdgeDevices | 是 | 

## <a name="data-catalog"></a>数据目录
| 资源类型 | 支持标记 |
| ------------- | ----------- |
| catalogs | 是 | 

## <a name="data-connect"></a>数据连接
| 资源类型 | 支持标记 |
| ------------- | ----------- |
| connectionManagers | 是 | 

## <a name="data-factory"></a>数据工厂
| 资源类型 | 支持标记 |
| ------------- | ----------- |
| dataFactories | 是 | 
| dataFactories/diagnosticSettings | 否 | 
| dataFactories/metricDefinitions | 否 | 
| dataFactorySchema | 否 | 
| factories | 是 | 
| factories/integrationRuntimes | 否 | 

## <a name="devices"></a>设备
| 资源类型 | 支持标记 |
| ------------- | ----------- |
| IotHubs | 是 | 
| IotHubs/eventGridFilters | 否 | 
| ProvisioningServices | 是 | 

## <a name="devspaces"></a>Devspaces
| 资源类型 | 支持标记 |
| ------------- | ----------- |
| 控制器 | 是 | 

## <a name="devtest-lab"></a>开发测试实验室
| 资源类型 | 支持标记 |
| ------------- | ----------- |
| labs | 是 | 
| labs/artifactsources | 是 |
| labs/costs | 是 |
| labs/customimages | 是 |
| labs/formulas | 是 |
| labs/notificationchannels | 是 |
| labs/policysets/policies | 是 |
| labs/schedules | 是 |
| labs/serviceRunners | 是 | 
| labs/users | 是 |
| labs/users/disks | 是 |
| labs/users/environments | 是 |
| labs/users/secrets | 是 |
| labs/users/servicefabrics | 是 |
| labs/users/servicefabrics/schedules | 是 |
| labs/virtualMachines | 是 | 
| labs/virtualmachines/schedules | 是 |
| labs/virtualnetworks | 是 |
| schedules | 是 | 

## <a name="dynamics-lcs"></a>Dynamics LCS
| 资源类型 | 支持标记 |
| ------------- | ----------- |
| lcsprojects | 否 | 
| lcsprojects/connectors | 否 | 
| lcsprojects/clouddeployments | 否 | 

## <a name="event-grid"></a>事件网格
| 资源类型 | 支持标记 |
| ------------- | ----------- |
| domains | 是 | 
| 域/主题 | 否 | 
| eventSubscriptions | 否 | 
| extensionTopics | 否 | 
| topics | 是 | 
| topicTypes | 否 | 

## <a name="event-hub"></a>事件中心
| 资源类型 | 支持标记 |
| ------------- | ----------- |
| clusters | 是 | 
| namespaces | 是 | 
| namespaces/AuthorizationRules | 否 |
| namespaces/disasterRecoveryConfigs | 否 |
| namespaces/eventhubs | 否 |
| namespaces/eventhubs/authorizationRules | 否 |
| namespaces/eventhubs/consumergroups | 否 |

## <a name="hana-on-azure"></a>Azure 上的 Hana
| 资源类型 | 支持标记 |
| ------------- | ----------- |
| hanainstances | 是 | 

## <a name="hdinsight"></a>HDInsight
| 资源类型 | 支持标记 |
| ------------- | ----------- |
| clusters | 是 | 
| clusters/applications | 否 | 

## <a name="import-export"></a>导入/导出
| 资源类型 | 支持标记 |
| ------------- | ----------- |
| jobs | 是 | 

## <a name="insights"></a>洞察力
| 资源类型 | 支持标记 |
| ------------- | ----------- |
| actionGroups | 是 |
| activityLogAlerts | 是 |
| alertrules | 是 |
| automatedExportSettings | 否 | 
| components | 是 | 
| components/events | 否 | 
| components/metrics | 否 | 
| components/pricingPlans | 否 | 
| components/query | 否 | 
| 日志 | 否 | 
| metricAlerts | 是 |
| migrateToNewPricingModel | 否 | 
| myWorkbooks | 否 | 
| 查询 | 否 | 
| rollbackToLegacyPricingModel | 否 | 
| scheduledqueryrules | 是 | 
| webtests | 是 | 
| workbooks | 是 | 

## <a name="key-vault"></a>Key Vault
| 资源类型 | 支持标记 |
| ------------- | ----------- |
| deletedVaults | 否 | 
| vaults | 是 | 
| vaults/accessPolicies | 否 | 
| vaults/secrets | 否 | 

## <a name="log-analytics"></a>Log Analytics
| 资源类型 | 支持标记 |
| ------------- | ----------- |
| 日志 | 否 | 

## <a name="logic"></a>逻辑
| 资源类型 | 支持标记 |
| ------------- | ----------- |
| integrationAccounts | 是 | 
| workflows | 是 | 

## <a name="machine-learning-services"></a>机器学习服务
| 资源类型 | 支持标记 |
| ------------- | ----------- |
| workspaces | 是 | 
| workspaces/computes | 否 | 

## <a name="managed-identity"></a>托管标识
| 资源类型 | 支持标记 |
| ------------- | ----------- |
| 标识 | 否 | 
| userAssignedIdentities | 是 | 

## <a name="marketplace-apps"></a>市场应用
| 资源类型 | 支持标记 |
| ------------- | ----------- |
| classicDevServices | 是 | 

## <a name="marketplace-ordering"></a>市场订购
| 资源类型 | 支持标记 |
| ------------- | ----------- |
| 协议 | 否 | 
| offertypes | 否 | 

## <a name="media"></a>媒体
| 资源类型 | 支持标记 |
| ------------- | ----------- |
| mediaservices | 是 | 
| mediaservices/accountFilters | 否 | 
| mediaservices/assets | 否 | 
| mediaservices/assets/assetFilters | 否 | 
| mediaservices/contentKeyPolicies | 否 | 
| mediaservices/eventGridFilters | 否 | 
| mediaservices/liveEventOperations | 否 | 
| mediaservices/liveEvents | 是 | 
| mediaservices/liveEvents/liveOutputs | 否 | 
| mediaservices/liveOutputOperations | 否 | 
| mediaservices/streamingEndpoints | 是 | 
| mediaservices/streamingEndpointOperations | 否 | 
| mediaservices/streamingLocators | 否 | 
| mediaservices/streamingPolicies | 否 | 
| mediaservices/transforms | 否 | 
| mediaservices/transforms/jobs | 否 | 

## <a name="network"></a>网络
| 资源类型 | 支持标记 |
| ------------- | ----------- |
| applicationGateways | 是 | 
| applicationSecurityGroups | 是 | 
| azureFirewalls | 是 | 
| connections | 是 | 
| ddosProtectionPlans | 是 | 
| expressRouteCircuits | 是 | 
| frontdoors | 是 | 
| frontdoorWebApplicationFirewallPolicies | 是 | 
| interfaceEndpoints | 是 | 
| loadBalancers | 是 | 
| localNetworkGateways | 是 | 
| networkIntentPolicies | 是 | 
| networkInterfaces | 是 | 
| networkProfiles | 是 | 
| networkSecurityGroups | 是 | 
| networkWatchers | 是 | 
| networkWatchers/connectionMonitors | 是 | 
| networkWatchers/lenses | 是 | 
| networkWatchers/pingMeshes | 是 | 
| privateLinkServices | 是 | 
| publicIPAddresses | 是 | 
| publicIPPrefixes | 是 | 
| routeFilters | 是 | 
| routeTables | 是 | 
| serviceEndpointPolicies | 是 | 
| virtualHubs | 是 | 
| virtualNetworks | 是 | 
| virtualNetworkGateways | 是 | 
| virtualNetworkTaps | 是 | 
| virtualWans | 是 | 
| vpnGateways | 是 | 
| vpnSites | 是 | 
| webApplicationFirewallPolicies | 是 | 

## <a name="notification-hubs"></a>通知中心
| 资源类型 | 支持标记 |
| ------------- | ----------- |
| namespaces | 是 | 
| namespaces/notificationHubs | 是 | 

## <a name="operational-insights"></a>操作见解
| 资源类型 | 支持标记 |
| ------------- | ----------- |
| workspaces | 是 |
| workspaces/dataSources | 是 |
| workspaces/linkedServices | 是 |
| workspaces/savedSearches | 否 |
| workspaces/storageInsightConfigs | 是 |

## <a name="operations-management"></a>操作管理
| 资源类型 | 支持标记 |
| ------------- | ----------- |
| solutions | 否 |

## <a name="portal"></a>门户
| 资源类型 | 支持标记 |
| ------------- | ----------- |
| dashboards | 是 | 

## <a name="portal-sdk"></a>门户 SDK
| 资源类型 | 支持标记 |
| ------------- | ----------- |
| rootResources | 是 | 

## <a name="power-bi"></a>Power BI
| 资源类型 | 支持标记 |
| ------------- | ----------- |
| workspaceCollections | 是 | 

## <a name="recovery-services"></a>恢复服务
| 资源类型 | 支持标记 |
| ------------- | ----------- |
| backupProtectedItems | 否 | 
| vaults | 是 | 

## <a name="relay"></a>中继
| 资源类型 | 支持标记 |
| ------------- | ----------- |
| namespaces | 是 | 

## <a name="resources"></a>资源
| 资源类型 | 支持标记 |
| ------------- | ----------- |
| resourceGroups | 是 | 
| subscriptions/resourceGroups | 是 | 

## <a name="scheduler"></a>计划程序
| 资源类型 | 支持标记 |
| ------------- | ----------- |
| jobcollections | 是 | 
| flows | 是 | 

## <a name="search"></a>搜索
| 资源类型 | 支持标记 |
| ------------- | ----------- |
| resourceHealthMetadata | 否 | 
| searchServices | 是 | 

## <a name="security"></a>安全
| 资源类型 | 支持标记 |
| ------------- | ----------- |
| dataCollectionAgents | 否 | 

## <a name="service-bus"></a>服务总线
| 资源类型 | 支持标记 |
| ------------- | ----------- |
| namespaces | 是 | 
| namespaces/eventgridfilters | 否 | 

## <a name="service-fabric"></a>Service Fabric
| 资源类型 | 支持标记 |
| ------------- | ----------- |
| clusters | 是 | 
| clusters/applications | 否 | 

## <a name="service-fabric-mesh"></a>Service Fabric 网格
| 资源类型 | 支持标记 |
| ------------- | ----------- |
| 服务 | 是 | 
| networks | 是 | 
| volumes | 是 | 

## <a name="signalr-service"></a>SignalR 服务
| 资源类型 | 支持标记 |
| ------------- | ----------- |
| SignalR | 是 | 

## <a name="site-recovery"></a>Site Recovery
| 资源类型 | 支持标记 |
| ------------- | ----------- |
| SiteRecoveryVault | 是 | 

## <a name="solutions"></a>解决方案
| 资源类型 | 支持标记 |
| ------------- | ----------- |
| 服务 | 是 | 
| applicationDefinitions | 是 | 
| jitRequests | 是 | 

## <a name="sql"></a>SQL
| 资源类型 | 支持标记 |
| ------------- | ----------- |
| locations/instanceFailoverGroups | 否 |
| managedInstances | 是 |
| managedInstances/databases | 是 |
| managedInstances/databases/backupShortTermRetentionPolicies | 否 |
| managedInstances/databases/schemas/tables/columns/sensitivityLabels | 否 |
| managedInstances/databases/vulnerabilityAssessments | 否 |
| managedInstances/databases/vulnerabilityAssessments/rules/baselines | 否 |
| managedInstances/encryptionProtector | 否 |
| managedInstances/keys | 否 |
| managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies | 否 |
| managedInstances/vulnerabilityAssessments | 否 |
| servers | 是 |
| servers/administrators | 否 |
| servers/advisors | 否 |
| servers/auditingSettings | 否 |
| servers/backupLongTermRetentionVaults | 否 |
| servers/communicationLinks | 否 |
| servers/connectionPolicies | 否 |
| servers/databases | 是 |
| servers/databases/advisors | 否 |
| servers/databases/auditingSettings | 否 |
| servers/databases/backupLongTermRetentionPolicies | 否 |
| servers/databases/backupShortTermRetentionPolicies | 否 |
| servers/databases/connectionPolicies | 否 |
| servers/databases/dataMaskingPolicies | 否 |
| servers/databases/dataMaskingPolicies/rules | 否 |
| servers/databases/extendedAuditingSettings | 否 |
| servers/databases/extensions | 否 |
| servers/databases/geoBackupPolicies | 否 |
| servers/databases/schemas/tables/columns/sensitivityLabels | 否 |
| servers/databases/securityAlertPolicies | 否 |
| servers/databases/syncGroups | 否 |
| servers/databases/syncGroups/syncMembers | 否 |
| servers/databases/transparentDataEncryption | 否 |
| servers/databases/vulnerabilityAssessments | 否 |
| servers/databases/vulnerabilityAssessments/rules/baselines | 否 |
| servers/disasterRecoveryConfiguration | 否 |
| servers/dnsAliases | 否 |
| servers/elasticPools | 是 |
| servers/encryptionProtector | 否 |
| servers/extendedAuditingSettings | 否 |
| servers/failoverGroups | 是 |
| servers/firewallRules | 否 |
| servers/jobAgents | 是 |
| servers/jobAgents/credentials | 否 |
| servers/jobAgents/jobs | 否 |
| servers/jobAgents/jobs/executions | 否 |
| servers/jobAgents/jobs/steps | 否 |
| servers/jobAgents/targetGroups | 否 |
| servers/keys | 否 |
| servers/securityAlertPolicies | 否 |
| servers/syncAgents | 否 |
| servers/virtualNetworkRules | 否 |
| servers/vulnerabilityAssessments | 否 |

## <a name="sql-virtual-machine"></a>SQL 虚拟机
| 资源类型 | 支持标记 |
| ------------- | ----------- |
| DWVM | 是 | 

## <a name="storage"></a>存储
| 资源类型 | 支持标记 |
| ------------- | ----------- |
| storageAccounts | 是 | 
| storageAccounts/blobServices | 否 | 
| storageAccounts/fileServices | 否 | 
| storageAccounts/queueServices | 否 | 
| storageAccounts/services | 否 | 
| storageAccounts/services/metricDefinitions | 否 | 
| storageAccounts/tableServices | 否 | 

## <a name="storage-sync"></a>存储同步
| 资源类型 | 支持标记 |
| ------------- | ----------- |
| storageSyncServices | 是 | 
| storageSyncServices/registeredServers | 否 | 
| storageSyncServices/syncGroups | 否 | 
| storageSyncServices/syncGroups/cloudEndpoints | 否 | 
| storageSyncServices/syncGroups/serverEndpoints | 否 | 
| storageSyncServices/workflows | 否 | 

## <a name="storsimple"></a>Storsimple
| 资源类型 | 支持标记 |
| ------------- | ----------- |
| managers | 是 | 

## <a name="stream-analytics"></a>流分析
| 资源类型 | 支持标记 |
| ------------- | ----------- |
| streamingjobs | 是 | 
| streamingjobs/diagnosticSettings | 否 | 
| streamingjobs/metricDefinitions | 否 | 

## <a name="subscription"></a>订阅
| 资源类型 | 支持标记 |
| ------------- | ----------- |
| SubscriptionDefinitions | 否 | 
| SubscriptionOperations | 否 | 

## <a name="support"></a>支持
| 资源类型 | 支持标记 |
| ------------- | ----------- |
| supporttickets | 否 | 

## <a name="visual-studio"></a>Visual Studio
| 资源类型 | 支持标记 |
| ------------- | ----------- |
| 帐户 | 是 | 
| account/extension | 是 | 
| account/project | 是 | 

## <a name="web"></a>Web
| 资源类型 | 支持标记 |
| ------------- | ----------- |
| apiManagementAccounts | 否 | 
| apiManagementAccounts/apiAcls | 否 | 
| apiManagementAccounts/apis | 否 | 
| apiManagementAccounts/apis/apiAcls | 否 | 
| apiManagementAccounts/apis/connectionAcls | 否 | 
| apiManagementAccounts/apis/connections | 否 | 
| apiManagementAccounts/apis/connections/connectionAcls | 否 | 
| apiManagementAccounts/apis/localizedDefinitions | 否 | 
| apiManagementAccounts/connectionAcls | 否 | 
| apiManagementAccounts/connections | 否 | 
| billingMeters | 否 | 
| certificates | 是 | 
| connectionGateways | 是 | 
| connections | 是 | 
| customApis | 是 | 
| deletedSites | 否 | 
| functions | 否 | 
| hostingEnvironments | 是 | 
| hostingEnvironments/metrics | 否 | 
| hostingEnvironments/multiRolePools | 否 | 
| hostingEnvironments/workerPools | 否 | 
| publishingUsers | 否 | 
| serverFarms | 是 | 
| serverFarms/workers | 否 | 
| sites | 是 | 
| sites/domainOwnershipIdentifiers | 否 | 
| sites/hostNameBindings | 否 | 
| sites/instances | 否 | 
| sites/instances/extensions | 否 | 
| sites/metrics | 否 | 
| sites/premieraddons | 是 | 
| sites/slots | 是 | 
| sites/slots/hostNameBindings | 否 | 
| sites/slots/instances | 否 | 
| sites/slots/instances/extensions | 否 | 
| sites/slots/metrics | 否 | 
| sourceControls | 否 | 
| validate | 否 | 
| verifyHostingEnvironmentVnet | 否 | 

## <a name="xrm"></a>XRM
| 资源类型 | 支持标记 |
| ------------- | ----------- |
| 组织 | 否 | 

## <a name="next-steps"></a>后续步骤
若要了解如何将标记应用于资源，请参见[使用标记来组织 Azure 资源](resource-group-using-tags.md)。
