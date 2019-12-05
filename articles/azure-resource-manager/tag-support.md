---
title: 资源的标记支持
description: 显示支持标记的 Azure资源类型。 提供所有 Azure 服务的详细信息。
ms.topic: conceptual
ms.date: 11/22/2019
ms.openlocfilehash: 5b3e26d914887496eedde609404eaf0b380dbcc0
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/23/2019
ms.locfileid: "74422143"
---
# <a name="tag-support-for-azure-resources"></a>Azure 资源的标记支持
本文介绍某一资源类型是否支持[标记](resource-group-using-tags.md)。 标记为“支持标记”的列指示资源类型是否具有标记的属性。 标记为“在成本报表中标记”的列指示该资源类型是否将标记传递给成本报表。 你可以在[成本管理成本分析](../cost-management/quick-acm-cost-analysis.md#understanding-grouping-and-filtering-options)和[Azure 计费发票和每日使用数据](../billing/billing-download-azure-invoice-daily-usage-date.md)中按标记查看成本。

若要以逗号分隔值文件的形式获取同一数据，请下载 [tag-support.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/tag-support.csv)。

跳转到资源提供程序命名空间：
> [!div class="op_single_selector"]
> - [Microsoft AAD](#microsoftaad)
> - [加载项](#microsoftaddons)
> - [ADHybridHealthService](#microsoftadhybridhealthservice)
> - [Microsoft.Advisor](#microsoftadvisor)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [AppConfiguration](#microsoftappconfiguration)
> - [AppPlatform](#microsoftappplatform)
> - [Microsoft. 证明](#microsoftattestation)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [Azconfig](#microsoftazconfig)
> - [Geneva](#microsoftazuregeneva)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [AzureData](#microsoftazuredata)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.Batch](#microsoftbatch)
> - [微软帐单](#microsoftbilling)
> - [BingMaps](#microsoftbingmaps)
> - [区块链](#microsoftblockchain)
> - [Microsoft 蓝图](#microsoftblueprint)
> - [BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Microsoft. 容量](#microsoftcapacity)
> - [Microsoft.Cdn](#microsoftcdn)
> - [CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft.ClassicInfrastructureMigrate](#microsoftclassicinfrastructuremigrate)
> - [Microsoft.ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft 商业](#microsoftcommerce)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft. 消耗](#microsoftconsumption)
> - [ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [CortanaAnalytics](#microsoftcortanaanalytics)
> - [CostManagement](#microsoftcostmanagement)
> - [CustomerLockbox](#microsoftcustomerlockbox)
> - [CustomProviders](#microsoftcustomproviders)
> - [DataBox](#microsoftdatabox)
> - [DataBoxEdge](#microsoftdataboxedge)
> - [Databricks](#microsoftdatabricks)
> - [Microsoft.datacatalog](#microsoftdatacatalog)
> - [Microsoft.DataFactory](#microsoftdatafactory)
> - [Microsoft.DataLakeAnalytics](#microsoftdatalakeanalytics)
> - [Microsoft.DataLakeStore](#microsoftdatalakestore)
> - [Microsoft.DataMigration](#microsoftdatamigration)
> - [DataShare](#microsoftdatashare)
> - [Microsoft.DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft.DBforMySQL](#microsoftdbformysql)
> - [Microsoft.DBforPostgreSQL](#microsoftdbforpostgresql)
> - [DeploymentManager](#microsoftdeploymentmanager)
> - [DesktopVirtualization](#microsoftdesktopvirtualization)
> - [Microsoft.Devices](#microsoftdevices)
> - [DevOps](#microsoftdevops)
> - [DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [DomainRegistration](#microsoftdomainregistration)
> - [DynamicsLcs](#microsoftdynamicslcs)
> - [EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft.Features](#microsoftfeatures)
> - [Microsoft. 库](#microsoftgallery)
> - [基因组学](#microsoftgenomics)
> - [](#microsoftguestconfiguration)Microsoft.GuestConfiguration
> - [HanaOnAzure](#microsofthanaonazure)
> - [Microsoft.hardwaresecuritymodules](#microsofthardwaresecuritymodules)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [HealthcareApis](#microsofthealthcareapis)
> - [](#microsofthybridcompute)Microsoft.HybridCompute
> - [HybridData](#microsofthybriddata)
> - [Hydra](#microsofthydra)
> - [Microsoft.ImportExport](#microsoftimportexport)
> - [Microsoft Intune](#microsoftintune)
> - [IoTCentral](#microsoftiotcentral)
> - [IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Microsoft.Kusto](#microsoftkusto)
> - [LabServices](#microsoftlabservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [Default-machinelearning-southcentralus](#microsoftmachinelearning)
> - [MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft.ManagedIdentity](#microsoftmanagedidentity)
> - [ManagedServices](#microsoftmanagedservices)
> - [Microsoft.Management](#microsoftmanagement)
> - [Microsoft. Maps](#microsoftmaps)
> - [Microsoft Marketplace](#microsoftmarketplace)
> - [MarketplaceApps](#microsoftmarketplaceapps)
> - [MarketplaceOrdering](#microsoftmarketplaceordering)
> - [Microsoft.Media](#microsoftmedia)
> - [Microservices4Spring](#microsoftmicroservices4spring)
> - [Microsoft. 迁移](#microsoftmigrate)
> - [MixedReality](#microsoftmixedreality)
> - [Microsoft NetApp](#microsoftnetapp)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [ObjectStore](#microsoftobjectstore)
> - [OffAzure](#microsoftoffazure)
> - [Microsoft.OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft.OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft。对等互连](#microsoftpeering)
> - [Microsoft.PolicyInsights](#microsoftpolicyinsights)
> - [Microsoft.Portal](#microsoftportal)
> - [Microsoft.PowerBI](#microsoftpowerbi)
> - [Microsoft.PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft RemoteApp](#microsoftremoteapp)
> - [Microsoft.ResourceGraph](#microsoftresourcegraph)
> - [Microsoft.ResourceHealth](#microsoftresourcehealth)
> - [Microsoft.Resources](#microsoftresources)
> - [Microsoft SaaS](#microsoftsaas)
> - [Microsoft.Scheduler](#microsoftscheduler)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [SecurityGraph](#microsoftsecuritygraph)
> - [SecurityInsights](#microsoftsecurityinsights)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft. 服务](#microsoftservices)
> - [SignalRService](#microsoftsignalrservice)
> - [Microsoft.SiteRecovery](#microsoftsiterecovery)
> - [SoftwarePlan](#microsoftsoftwareplan)
> - [Microsoft. 解决方案](#microsoftsolutions)
> - [Microsoft .SQL](#microsoftsql)
> - [SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.Storage](#microsoftstorage)
> - [StorageCache](#microsoftstoragecache)
> - [StorageReplication](#microsoftstoragereplication)
> - [Storagesync.sys](#microsoftstoragesync)
> - [StorageSyncDev](#microsoftstoragesyncdev)
> - [StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft.Subscription](#microsoftsubscription)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft.Web](#microsoftweb)
> - [WindowsDefenderATP](#microsoftwindowsdefenderatp)
> - [WindowsIoT](#microsoftwindowsiot)
> - [WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | DomainServices | 是 | 是 |
> | DomainServices/oucontainer | 否 | 否 |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | supportProviders | 否 | 否 |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | aadsupportcases | 否 | 否 |
> | addsservices | 否 | 否 |
> | 代理 | 否 | 否 |
> | anonymousapiusers | 否 | 否 |
> | 配置 | 否 | 否 |
> | 日志 | 否 | 否 |
> | 报表 | 否 | 否 |
> | servicehealthmetrics | 否 | 否 |
> | services | 否 | 否 |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | 配置 | 否 | 否 |
> | generateRecommendations | 否 | 否 |
> | metadata | 否 | 否 |
> | 建议 | 否 | 否 |
> | 禁止显示 | 否 | 否 |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | actionRules | 是 | 是 |
> | alerts | 否 | 否 |
> | alertsList | 否 | 否 |
> | alertsMetaData | 否 | 否 |
> | alertsSummary | 否 | 否 |
> | alertsSummaryList | 否 | 否 |
> | 反馈 | 否 | 否 |
> | smartDetectorAlertRules | 是 | 是 |
> | smartDetectorRuntimeEnvironments | 否 | 否 |
> | smartGroups | 否 | 否 |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | servers | 是 | 是 |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | reportFeedback | 否 | 否 |
> | 服务 | 是 | 是 |
> | validateServiceName | 否 | 否 |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | configurationStores | 是 | 是 |
> | configurationStores / eventGridFilters | 否 | 否 |

## <a name="microsoftappplatform"></a>AppPlatform

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | Spring | 是 | 是 |

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | attestationProviders | 否 | 否 |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | classicAdministrators | 否 | 否 |
> | dataAliases | 否 | 否 |
> | denyAssignments | 否 | 否 |
> | elevateAccess | 否 | 否 |
> | findOrphanRoleAssignments | 否 | 否 |
> | 锁定 | 否 | 否 |
> | 权限 | 否 | 否 |
> | policyAssignments | 否 | 否 |
> | policyDefinitions | 否 | 否 |
> | policySetDefinitions | 否 | 否 |
> | providerOperations | 否 | 否 |
> | roleAssignments | 否 | 否 |
> | roleDefinitions | 否 | 否 |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | automationAccounts | 是 | 是 |
> | automationAccounts/配置 | 是 | 是 |
> | automationAccounts/作业 | 否 | 否 |
> | automationAccounts/runbook | 是 | 是 |
> | automationAccounts/softwareUpdateConfigurations | 否 | 否 |
> | automationAccounts/webhook | 否 | 否 |

## <a name="microsoftazconfig"></a>Azconfig

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | configurationStores | 是 | 是 |
> | configurationStores / eventGridFilters | 否 | 否 |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | environments | 否 | 否 |
> | 环境/帐户 | 否 | 否 |
> | 环境/帐户/命名空间 | 否 | 否 |
> | 环境/帐户/命名空间/配置 | 否 | 否 |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | b2cDirectories | 是 | 否 |
> | b2ctenants | 否 | 否 |

## <a name="microsoftazuredata"></a>AzureData

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | hybridDataManagers | 是 | 是 |
> | postgresInstances | 是 | 是 |
> | sqlBigDataClusters | 是 | 是 |
> | sqlInstances | 是 | 是 |
> | sqlServerRegistrations | 是 | 是 |
> | sqlServerRegistrations/sqlServers | 否 | 否 |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | registrations | 是 | 是 |
> | 注册/customerSubscriptions | 否 | 否 |
> | 注册/产品 | 否 | 否 |
> | verificationKeys | 否 | 否 |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | batchAccounts | 是 | 是 |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | billingAccounts | 否 | 否 |
> | billingAccounts/协议 | 否 | 否 |
> | billingAccounts / billingPermissions | 否 | 否 |
> | billingAccounts / billingProfiles | 否 | 否 |
> | billingAccounts / billingProfiles / billingPermissions | 否 | 否 |
> | billingAccounts / billingProfiles / billingRoleAssignments | 否 | 否 |
> | billingAccounts / billingProfiles / billingRoleDefinitions | 否 | 否 |
> | billingAccounts / billingProfiles / billingSubscriptions | 否 | 否 |
> | billingAccounts / billingProfiles / createBillingRoleAssignment | 否 | 否 |
> | billingAccounts/billingProfiles/customers | 否 | 否 |
> | billingAccounts/billingProfiles/发票 | 否 | 否 |
> | billingAccounts/billingProfiles/发票/价目表 | 否 | 否 |
> | billingAccounts / billingProfiles / invoiceSections | 否 | 否 |
> | billingAccounts / billingProfiles / invoiceSections / billingPermissions | 否 | 否 |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleAssignments | 否 | 否 |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleDefinitions | 否 | 否 |
> | billingAccounts / billingProfiles / invoiceSections / billingSubscriptions | 否 | 否 |
> | billingAccounts / billingProfiles / invoiceSections / createBillingRoleAssignment | 否 | 否 |
> | billingAccounts / billingProfiles / invoiceSections / initiateTransfer | 否 | 否 |
> | billingAccounts/billingProfiles/invoiceSections/products | 否 | 否 |
> | billingAccounts/billingProfiles/invoiceSections/products/transfer | 否 | 否 |
> | billingAccounts/billingProfiles/invoiceSections/products/updateAutoRenew | 否 | 否 |
> | billingAccounts/billingProfiles/invoiceSections/事务 | 否 | 否 |
> | billingAccounts/billingProfiles/invoiceSections/传输 | 否 | 否 |
> | billingAccounts / BillingProfiles / patchOperations | 否 | 否 |
> | billingAccounts / billingProfiles / paymentMethods | 否 | 否 |
> | billingAccounts/billingProfiles/策略 | 否 | 否 |
> | billingAccounts/billingProfiles/价目表 | 否 | 否 |
> | billingAccounts / billingProfiles / pricesheetDownloadOperations | 否 | 否 |
> | billingAccounts/billingProfiles/products | 否 | 否 |
> | billingAccounts/billingProfiles/事务 | 否 | 否 |
> | billingAccounts / billingRoleAssignments | 否 | 否 |
> | billingAccounts / billingRoleDefinitions | 否 | 否 |
> | billingAccounts / billingSubscriptions | 否 | 否 |
> | billingAccounts/billingSubscriptions/发票 | 否 | 否 |
> | billingAccounts / createBillingRoleAssignment | 否 | 否 |
> | billingAccounts / createInvoiceSectionOperations | 否 | 否 |
> | billingAccounts/客户 | 否 | 否 |
> | billingAccounts/customers/billingPermissions | 否 | 否 |
> | billingAccounts/customers/billingSubscriptions | 否 | 否 |
> | billingAccounts/customers/initiateTransfer | 否 | 否 |
> | billingAccounts/客户/策略 | 否 | 否 |
> | billingAccounts/客户/产品 | 否 | 否 |
> | billingAccounts/客户/事务 | 否 | 否 |
> | billingAccounts/客户/转移 | 否 | 否 |
> | billingAccounts/部门 | 否 | 否 |
> | billingAccounts / enrollmentAccounts | 否 | 否 |
> | billingAccounts/发票 | 否 | 否 |
> | billingAccounts / invoiceSections | 否 | 否 |
> | billingAccounts / invoiceSections / billingSubscriptionMoveOperations | 否 | 否 |
> | billingAccounts / invoiceSections / billingSubscriptions | 否 | 否 |
> | billingAccounts/invoiceSections/billingSubscriptions/transfer | 否 | 否 |
> | billingAccounts/invoiceSections/提升 | 否 | 否 |
> | billingAccounts / invoiceSections / initiateTransfer | 否 | 否 |
> | billingAccounts / invoiceSections / patchOperations | 否 | 否 |
> | billingAccounts / invoiceSections / productMoveOperations | 否 | 否 |
> | billingAccounts/invoiceSections/products | 否 | 否 |
> | billingAccounts/invoiceSections/products/transfer | 否 | 否 |
> | billingAccounts/invoiceSections/products/updateAutoRenew | 否 | 否 |
> | billingAccounts/invoiceSections/事务 | 否 | 否 |
> | billingAccounts/invoiceSections/传输 | 否 | 否 |
> | billingAccounts / lineOfCredit | 否 | 否 |
> | billingAccounts / patchOperations | 否 | 否 |
> | billingAccounts / paymentMethods | 否 | 否 |
> | billingAccounts/products | 否 | 否 |
> | billingAccounts/事务 | 否 | 否 |
> | billingPeriods | 否 | 否 |
> | billingPermissions | 否 | 否 |
> | billingProperty | 否 | 否 |
> | billingRoleAssignments | 否 | 否 |
> | billingRoleDefinitions | 否 | 否 |
> | createBillingRoleAssignment | 否 | 否 |
> | departments | 否 | 否 |
> | enrollmentAccounts | 否 | 否 |
> | invoices | 否 | 否 |
> | transfers | 否 | 否 |
> | 传输/acceptTransfer | 否 | 否 |
> | 传输/declineTransfer | 否 | 否 |
> | 传输/operationStatus | 否 | 否 |
> | 传输/validateTransfer | 否 | 否 |
> | validateAddress | 否 | 否 |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | mapApis | 是 | 是 |
> | updateCommunicationPreference | 否 | 否 |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | blockchainMembers | 是 | 是 |
> | cordaMembers | 是 | 是 |
> | 观看者 | 是 | 是 |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | blueprintAssignments | 否 | 否 |
> | blueprintAssignments / assignmentOperations | 否 | 否 |
> | blueprintAssignments/操作 | 否 | 否 |
> | blueprints | 否 | 否 |
> | 蓝图/项目 | 否 | 否 |
> | 蓝图/版本 | 否 | 否 |
> | 蓝图/版本/项目 | 否 | 否 |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | botServices | 是 | 是 |
> | botServices/通道 | 否 | 否 |
> | botServices/连接 | 否 | 否 |
> | 语言 | 否 | 否 |
> | 模板 | 否 | 否 |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | Redis | 是 | 是 |
> | RedisConfigDefinition | 否 | 否 |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | appliedReservations | 否 | 否 |
> | calculateExchange | 否 | 否 |
> | calculatePrice | 否 | 否 |
> | calculatePurchasePrice | 否 | 否 |
> | catalogs | 否 | 否 |
> | commercialReservationOrders | 否 | 否 |
> | 交易所 | 否 | 否 |
> | placePurchaseOrder | 否 | 否 |
> | reservationOrders | 否 | 否 |
> | reservationOrders / calculateRefund | 否 | 否 |
> | reservationOrders/merge | 否 | 否 |
> | reservationOrders/预订 | 否 | 否 |
> | reservationOrders/保留/修订 | 否 | 否 |
> | reservationOrders/return | 否 | 否 |
> | reservationOrders/split | 否 | 否 |
> | reservationOrders/swap | 否 | 否 |
> | reservations | 否 | 否 |
> | 资源 | 否 | 否 |
> | validateReservationOrder | 否 | 否 |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | CdnWebApplicationFirewallManagedRuleSets | 否 | 否 |
> | CdnWebApplicationFirewallPolicies | 是 | 是 |
> | edgenodes | 否 | 否 |
> | 配置文件 | 是 | 是 |
> | profiles/endpoints | 是 | 是 |
> | 配置文件/终结点/customdomains | 否 | 否 |
> | 配置文件/终结点/源 | 否 | 否 |
> | validateProbe | 否 | 否 |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | certificateOrders | 是 | 是 |
> | Certificateorder/证书 | 否 | 否 |
> | validateCertificateRegistrationInformation | 否 | 否 |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | capabilities | 否 | 否 |
> | domainNames | 否 | 否 |
> | domainNames/功能 | 否 | 否 |
> | domainNames / internalLoadBalancers | 否 | 否 |
> | domainNames / serviceCertificates | 否 | 否 |
> | domainNames/槽 | 否 | 否 |
> | domainNames/插槽/角色 | 否 | 否 |
> | domainNames/槽位/role/metricDefinitions | 否 | 否 |
> | domainNames/插槽/角色/指标 | 否 | 否 |
> | moveSubscriptionResources | 否 | 否 |
> | operatingSystemFamilies | 否 | 否 |
> | operatingSystems | 否 | 否 |
> | quotas | 否 | 否 |
> | resourceTypes | 否 | 否 |
> | validateSubscriptionMoveAvailability | 否 | 否 |
> | virtualMachines | 否 | 否 |
> | virtualMachines/diagnosticSettings | 否 | 否 |
> | virtualMachines/metricDefinitions | 否 | 否 |
> | virtualMachines/指标 | 否 | 否 |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | classicInfrastructureResources | 否 | 否 |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | capabilities | 否 | 否 |
> | expressRouteCrossConnections | 否 | 否 |
> | expressRouteCrossConnections/对等互连 | 否 | 否 |
> | gatewaySupportedDevices | 否 | 否 |
> | networkSecurityGroups | 否 | 否 |
> | quotas | 否 | 否 |
> | reservedIps | 否 | 否 |
> | virtualNetworks | 否 | 否 |
> | virtualNetworks/remoteVirtualNetworkPeeringProxies | 否 | 否 |
> | virtualNetworks/virtualNetworkPeerings | 否 | 否 |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | capabilities | 否 | 否 |
> | disks | 否 | 否 |
> | images | 否 | 否 |
> | osImages | 否 | 否 |
> | osPlatformImages | 否 | 否 |
> | publicImages | 否 | 否 |
> | quotas | 否 | 否 |
> | storageAccounts | 否 | 否 |
> | storageAccounts/blobServices | 否 | 否 |
> | storageAccounts/fileServices | 否 | 否 |
> | storageAccounts/metricDefinitions | 否 | 否 |
> | storageAccounts/指标 | 否 | 否 |
> | storageAccounts/queueServices | 否 | 否 |
> | storageAccounts/服务 | 否 | 否 |
> | storageAccounts/services/diagnosticSettings | 否 | 否 |
> | storageAccounts/services/metricDefinitions | 否 | 否 |
> | storageAccounts/服务/指标 | 否 | 否 |
> | storageAccounts/tableServices | 否 | 否 |
> | storageAccounts/vmImages | 否 | 否 |
> | vmImages | 否 | 否 |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | accounts | 是 | 是 |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | RateCard | 否 | 否 |
> | UsageAggregates | 否 | 否 |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | availabilitySets | 是 | 是 |
> | diskEncryptionSets | 是 | 是 |
> | disks | 是 | 是 |
> | galleries | 是 | 是 |
> | 库/应用程序 | 否 | 否 |
> | 库/应用程序/版本 | 否 | 否 |
> | galleries/images | 否 | 否 |
> | galleries/images/versions | 否 | 否 |
> | hostGroups | 是 | 是 |
> | hostGroups/主机 | 是 | 是 |
> | images | 是 | 是 |
> | proximityPlacementGroups | 是 | 是 |
> | restorePointCollections | 是 | 是 |
> | restorePointCollections / restorePoints | 否 | 否 |
> | sharedVMImages | 是 | 是 |
> | sharedVMImages/版本 | 否 | 否 |
> | snapshots | 是 | 是 |
> | virtualMachines | 是 | 是 |
> | virtualMachines/extensions | 是 | 是 |
> | virtualMachines/metricDefinitions | 否 | 否 |
> | virtualMachineScaleSets | 是 | 是 |
> | virtualMachineScaleSets/extensions | 否 | 否 |
> | virtualMachineScaleSets/networkInterfaces | 否 | 否 |
> | virtualMachineScaleSets/publicIPAddresses | 否 | 否 |
> | virtualMachineScaleSets/virtualMachines | 否 | 否 |
> | virtualMachineScaleSets/virtualMachines/networkInterfaces | 否 | 否 |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | AggregatedCost | 否 | 否 |
> | 余额 | 否 | 否 |
> | 预算 | 否 | 否 |
> | Charges | 否 | 否 |
> | CostTags | 否 | 否 |
> | credits | 否 | 否 |
> | events | 否 | 否 |
> | 预测 | 否 | 否 |
> | lots | 否 | 否 |
> | 市场 | 否 | 否 |
> | Pricesheets | 否 | 否 |
> | products | 否 | 否 |
> | ReservationDetails | 否 | 否 |
> | ReservationRecommendations | 否 | 否 |
> | ReservationSummaries | 否 | 否 |
> | ReservationTransactions | 否 | 否 |
> | 标记 | 否 | 否 |
> | tenants | 否 | 否 |
> | 术语 | 否 | 否 |
> | UsageDetails | 否 | 否 |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | containerGroups | 是 | 是 |
> | serviceAssociationLinks | 否 | 否 |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | registries | 是 | 是 |
> | 注册表/生成 | 否 | 否 |
> | 注册表/生成/取消 | 否 | 否 |
> | 注册表/生成/getLogLink | 否 | 否 |
> | 注册表/buildTasks | 是 | 是 |
> | 注册表/buildTasks/步骤 | 否 | 否 |
> | 注册表/eventGridFilters | 否 | 否 |
> | 注册表/generateCredentials | 否 | 否 |
> | 注册表/getBuildSourceUploadUrl | 否 | 否 |
> | 注册表/System.web.clientservices.providers.iclientformsauthenticationcredentialsprovider.getcredentials | 否 | 否 |
> | 注册表/importImage | 否 | 否 |
> | 注册表/queueBuild | 否 | 否 |
> | 注册表/regenerateCredential | 否 | 否 |
> | 注册表/regenerateCredentials | 否 | 否 |
> | registries/replications | 是 | 是 |
> | 注册表/运行 | 否 | 否 |
> | 注册表/运行/取消 | 否 | 否 |
> | 注册表/scheduleRun | 否 | 否 |
> | 注册表/scopeMaps | 否 | 否 |
> | 注册表/taskRuns | 是 | 是 |
> | registries/tasks | 是 | 是 |
> | 注册表/标记 | 否 | 否 |
> | 注册表/updatePolicies | 否 | 否 |
> | registries/webhooks | 是 | 是 |
> | 注册表/webhook/getCallbackConfig | 否 | 否 |
> | 注册表/webhook/ping | 否 | 否 |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | containerServices | 是 | 是 |
> | managedClusters | 是 | 是 |
> | openShiftManagedClusters | 是 | 是 |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | accounts | 是 | 是 |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | 警报 | 否 | 否 |
> | BillingAccounts | 否 | 否 |
> | 预算 | 否 | 否 |
> | CloudConnectors | 否 | 否 |
> | 连接器 | 是 | 是 |
> | Departments | 否 | 否 |
> | 维度 | 否 | 否 |
> | EnrollmentAccounts | 否 | 否 |
> | 导出 | 否 | 否 |
> | ExternalBillingAccounts | 否 | 否 |
> | ExternalBillingAccounts/警报 | 否 | 否 |
> | ExternalBillingAccounts/维度 | 否 | 否 |
> | ExternalBillingAccounts/预测 | 否 | 否 |
> | ExternalBillingAccounts/查询 | 否 | 否 |
> | ExternalSubscriptions | 否 | 否 |
> | ExternalSubscriptions/警报 | 否 | 否 |
> | ExternalSubscriptions/维度 | 否 | 否 |
> | ExternalSubscriptions/预测 | 否 | 否 |
> | ExternalSubscriptions/查询 | 否 | 否 |
> | 预测 | 否 | 否 |
> | 查询 | 否 | 否 |
> | register | 否 | 否 |
> | Reportconfigs | 否 | 否 |
> | 报告 | 否 | 否 |
> | 设置 | 否 | 否 |
> | showbackRules | 否 | 否 |
> | 视图 | 否 | 否 |

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | 请求 | 否 | 否 |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | 相关性 | 否 | 否 |
> | resourceProviders | 是 | 是 |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | jobs | 是 | 是 |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | DataBoxEdgeDevices | 是 | 是 |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | workspaces | 是 | 否 |
> | 工作区/virtualNetworkPeerings | 否 | 否 |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | catalogs | 是 | 是 |
> | datacatalogs | 是 | 是 |
> | datacatalogs/数据源 | 否 | 否 |
> | datacatalogs/数据源/扫描 | 否 | 否 |
> | datacatalogs/数据源/扫描/数据集 | 否 | 否 |
> | datacatalogs/数据源/扫描/触发器 | 否 | 否 |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | dataFactories | 是 | 否 |
> | Microsoft.azure.management.datafactories/diagnosticSettings | 否 | 否 |
> | Microsoft.azure.management.datafactories/metricDefinitions | 否 | 否 |
> | dataFactorySchema | 否 | 否 |
> | factories | 是 | 否 |
> | 工厂/integrationRuntimes | 否 | 否 |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | accounts | 是 | 是 |
> | 帐户/dataLakeStoreAccounts | 否 | 否 |
> | 帐户/storageAccounts | 否 | 否 |
> | 帐户/storageAccounts/容器 | 否 | 否 |
> | 帐户/transferAnalyticsUnits | 否 | 否 |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | accounts | 是 | 是 |
> | 帐户/eventGridFilters | 否 | 否 |
> | 帐户/firewallRules | 否 | 否 |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | services | 否 | 否 |
> | services/projects | 否 | 否 |

## <a name="microsoftdatashare"></a>DataShare

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | accounts | 是 | 是 |
> | 帐户/共享 | 否 | 否 |
> | 帐户/共享/数据集 | 否 | 否 |
> | 帐户/共享/邀请 | 否 | 否 |
> | 帐户/共享/providersharesubscriptions | 否 | 否 |
> | 帐户/共享/synchronizationSettings | 否 | 否 |
> | 帐户/sharesubscriptions | 否 | 否 |
> | accounts/sharesubscriptions/consumerSourceDataSets | 否 | 否 |
> | accounts/sharesubscriptions/datasetmappings | 否 | 否 |
> | 帐户/sharesubscriptions/触发器 | 否 | 否 |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | servers | 是 | 是 |
> | 服务器/顾问 | 否 | 否 |
> | 服务器/privateEndpointConnectionProxies | 否 | 否 |
> | 服务器/privateEndpointConnections | 否 | 否 |
> | 服务器/privateLinkResources | 否 | 否 |
> | 服务器/queryTexts | 否 | 否 |
> | 服务器/recoverableServers | 否 | 否 |
> | 服务器/topQueryStatistics | 否 | 否 |
> | 服务器/virtualNetworkRules | 否 | 否 |
> | 服务器/waitStatistics | 否 | 否 |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | servers | 是 | 是 |
> | 服务器/顾问 | 否 | 否 |
> | 服务器/privateEndpointConnectionProxies | 否 | 否 |
> | 服务器/privateEndpointConnections | 否 | 否 |
> | 服务器/privateLinkResources | 否 | 否 |
> | 服务器/queryTexts | 否 | 否 |
> | 服务器/recoverableServers | 否 | 否 |
> | 服务器/topQueryStatistics | 否 | 否 |
> | 服务器/virtualNetworkRules | 否 | 否 |
> | 服务器/waitStatistics | 否 | 否 |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | serverGroups | 是 | 是 |
> | servers | 是 | 是 |
> | 服务器/顾问 | 否 | 否 |
> | 服务器/密钥 | 否 | 否 |
> | 服务器/privateEndpointConnectionProxies | 否 | 否 |
> | 服务器/privateEndpointConnections | 否 | 否 |
> | 服务器/privateLinkResources | 否 | 否 |
> | 服务器/queryTexts | 否 | 否 |
> | 服务器/recoverableServers | 否 | 否 |
> | 服务器/topQueryStatistics | 否 | 否 |
> | 服务器/virtualNetworkRules | 否 | 否 |
> | 服务器/waitStatistics | 否 | 否 |
> | serversv2 | 是 | 是 |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | artifactSources | 是 | 是 |
> | rollouts | 是 | 是 |
> | serviceTopologies | 是 | 是 |
> | serviceTopologies/服务 | 是 | 是 |
> | serviceTopologies/services/serviceUnits | 是 | 是 |
> | steps | 是 | 是 |

## <a name="microsoftdesktopvirtualization"></a>DesktopVirtualization

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | applicationgroups | 是 | 是 |
> | applicationgroups/应用程序 | 否 | 否 |
> | applicationgroups/台式机 | 否 | 否 |
> | applicationgroups / startmenuitems | 否 | 否 |
> | hostpools | 是 | 是 |
> | hostpools / sessionhosts | 否 | 否 |
> | hostpools / sessionhosts / usersessions | 否 | 否 |
> | hostpools / usersessions | 否 | 否 |
> | workspaces | 是 | 是 |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | ElasticPools | 是 | 是 |
> | ElasticPools / IotHubTenants | 是 | 是 |
> | IotHubs | 是 | 是 |
> | IotHubs / eventGridFilters | 否 | 否 |
> | ProvisioningServices | 是 | 是 |
> | usages | 否 | 否 |

## <a name="microsoftdevops"></a>DevOps

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | 段 | 是 | 是 |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | 控制器 | 是 | 是 |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | labcenters | 是 | 是 |
> | labs | 是 | 是 |
> | 实验室/环境 | 是 | 是 |
> | 实验室/serviceRunners | 是 | 是 |
> | 实验室/virtualMachines | 是 | 是 |
> | schedules | 是 | 是 |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | databaseAccountNames | 否 | 否 |
> | databaseAccounts | 是 | 是 |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | domains | 是 | 是 |
> | 域/domainOwnershipIdentifiers | 否 | 否 |
> | generateSsoRequest | 否 | 否 |
> | topLevelDomains | 否 | 否 |
> | validateDomainRegistrationInformation | 否 | 否 |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | lcsprojects | 否 | 否 |
> | lcsprojects / clouddeployments | 否 | 否 |
> | lcsprojects/连接器 | 否 | 否 |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | services | 是 | 是 |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | domains | 是 | 是 |
> | 域/主题 | 否 | 否 |
> | eventSubscriptions | 否 | 否 |
> | extensionTopics | 否 | 否 |
> | topics | 是 | 是 |
> | topicTypes | 否 | 否 |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | clusters | 是 | 是 |
> | namespaces | 是 | 是 |
> | 命名空间/authorizationrules | 否 | 否 |
> | 命名空间/disasterrecoveryconfigs | 否 | 否 |
> | 命名空间/eventhubs | 否 | 否 |
> | 命名空间/eventhubs/authorizationrules | 否 | 否 |
> | 命名空间/eventhubs/consumergroups | 否 | 否 |
> | 命名空间/networkrulesets | 否 | 否 |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | features | 否 | 否 |
> | providers | 否 | 否 |

## <a name="microsoftgallery"></a>Microsoft.Gallery

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | enroll | 否 | 否 |
> | galleryitems | 否 | 否 |
> | generateartifactaccessuri | 否 | 否 |
> | myareas | 否 | 否 |
> | myareas/区域 | 否 | 否 |
> | myareas/区域/区域 | 否 | 否 |
> | myareas/区域/区域/galleryitems | 否 | 否 |
> | myareas/areas/galleryitems | 否 | 否 |
> | myareas / galleryitems | 否 | 否 |
> | register | 否 | 否 |
> | 资源 | 否 | 否 |
> | retrieveresourcesbyid | 否 | 否 |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | accounts | 是 | 是 |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | configurationProfileAssignments | 否 | 否 |
> | guestConfigurationAssignments | 否 | 否 |
> | software | 否 | 否 |
> | softwareUpdateProfile | 否 | 否 |
> | softwareUpdates | 否 | 否 |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | hanainstances | 是 | 是 |
> | sapMonitors | 是 | 是 |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | dedicatedHSMs | 是 | 是 |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | clusters | 是 | 是 |
> | clusters/applications | 否 | 否 |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | services | 是 | 是 |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | 造 | 是 | 是 |
> | 计算机/扩展 | 是 | 是 |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | dataManagers | 是 | 是 |

## <a name="microsofthydra"></a>Hydra

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | components | 是 | 是 |
> | networkScopes | 是 | 是 |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | jobs | 是 | 是 |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | 否 | 否 |
> | diagnosticSettingsCategories | 否 | 否 |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | appTemplates | 否 | 否 |
> | IoTApps | 是 | 是 |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | 图形 | 是 | 是 |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | deletedVaults | 否 | 否 |
> | hsmPools | 是 | 是 |
> | vaults | 是 | 是 |
> | 保管库/accessPolicies | 否 | 否 |
> | 保管库/eventGridFilters | 否 | 否 |
> | 保管库/机密 | 否 | 否 |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | clusters | 是 | 是 |
> | 群集/attacheddatabaseconfigurations | 否 | 否 |
> | 群集/数据库 | 否 | 否 |
> | 群集/数据库/dataconnections | 否 | 否 |
> | 群集/数据库/eventhubconnections | 否 | 否 |
> | 群集/sharedidentities | 否 | 否 |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | labaccounts | 是 | 是 |
> | users | 否 | 否 |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | hostingEnvironments | 是 | 是 |
> | integrationAccounts | 是 | 是 |
> | integrationServiceEnvironments | 是 | 是 |
> | integrationServiceEnvironments / managedApis | 是 | 是 |
> | isolatedEnvironments | 是 | 是 |
> | workflows | 是 | 是 |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | commitmentPlans | 是 | 是 |
> | webServices | 是 | 是 |
> | 工作区 | 是 | 是 |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | workspaces | 是 | 是 |
> | 工作区/计算 | 否 | 否 |
> | 工作区/eventGridFilters | 否 | 否 |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | 标识 | 否 | 否 |
> | userAssignedIdentities | 是 | 是 |

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | marketplaceRegistrationDefinitions | 否 | 否 |
> | registrationAssignments | 否 | 否 |
> | registrationDefinitions | 否 | 否 |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | getEntities | 否 | 否 |
> | managementGroups | 否 | 否 |
> | 资源 | 否 | 否 |
> | startTenantBackfill | 否 | 否 |
> | tenantBackfillStatus | 否 | 否 |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | accounts | 是 | 是 |
> | 帐户/eventGridFilters | 否 | 否 |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | offers | 否 | 否 |
> | offerTypes | 否 | 否 |
> | offerTypes/发布服务器 | 否 | 否 |
> | offerTypes/发布者/产品/服务 | 否 | 否 |
> | offerTypes/发布者/产品/服务/计划 | 否 | 否 |
> | offerTypes/发布者/产品/服务/计划/协议 | 否 | 否 |
> | offerTypes/发布者/服务/计划/配置 | 否 | 否 |
> | offerTypes/发布者/服务/计划/配置/importImage | 否 | 否 |
> | privategalleryitems | 否 | 否 |
> | products | 否 | 否 |
> | 发行者 | 否 | 否 |
> | 发布者/产品/服务 | 否 | 否 |
> | 发布者/服务/修正 | 否 | 否 |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | classicDevServices | 是 | 是 |
> | updateCommunicationPreference | 否 | 否 |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | 协议 | 否 | 否 |
> | offertypes | 否 | 否 |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | mediaservices | 是 | 是 |
> | windowsazure.mediaservices/accountFilters | 否 | 否 |
> | windowsazure.mediaservices/资产 | 否 | 否 |
> | windowsazure.mediaservices/资产/Assetfilter | 否 | 否 |
> | windowsazure.mediaservices/contentKeyPolicies | 否 | 否 |
> | windowsazure.mediaservices/eventGridFilters | 否 | 否 |
> | windowsazure.mediaservices/liveEventOperations | 否 | 否 |
> | windowsazure.mediaservices/liveEvents | 是 | 是 |
> | windowsazure.mediaservices/liveEvents/liveOutputs | 否 | 否 |
> | windowsazure.mediaservices/liveOutputOperations | 否 | 否 |
> | windowsazure.mediaservices/mediaGraphs | 否 | 否 |
> | windowsazure.mediaservices/streamingEndpointOperations | 否 | 否 |
> | windowsazure.mediaservices/Streamingendpoint | 是 | 是 |
> | windowsazure.mediaservices/streamingLocators | 否 | 否 |
> | windowsazure.mediaservices/streamingPolicies | 否 | 否 |
> | windowsazure.mediaservices/转换 | 否 | 否 |
> | windowsazure.mediaservices/转换/作业 | 否 | 否 |

## <a name="microsoftmicroservices4spring"></a>Microservices4Spring

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | appClusters | 是 | 是 |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | assessmentProjects | 是 | 是 |
> | migrateprojects | 是 | 是 |
> | projects | 是 | 是 |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | holographicsBroadcastAccounts | 是 | 是 |
> | objectUnderstandingAccounts | 是 | 是 |
> | remoteRenderingAccounts | 是 | 是 |
> | spatialAnchorsAccounts | 是 | 是 |
> | surfaceReconstructionAccounts | 是 | 是 |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | netAppAccounts | 是 | 否 |
> | netAppAccounts / capacityPools | 是 | 否 |
> | netAppAccounts/capacityPools/卷 | 是 | 否 |
> | netAppAccounts/capacityPools/卷集/mountTargets | 是 | 否 |
> | netAppAccounts/capacityPools/卷/快照 | 是 | 否 |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | applicationGateways | 是 | 是 |
> | applicationGatewayWebApplicationFirewallPolicies | 是 | 是 |
> | applicationSecurityGroups | 是 | 是 |
> | azureFirewallFqdnTags | 否 | 否 |
> | azureFirewalls | 是 | 否 |
> | bastionHosts | 是 | 是 |
> | bgpServiceCommunities | 否 | 否 |
> | connections | 是 | 是 |
> | ddosCustomPolicies | 是 | 是 |
> | ddosProtectionPlans | 是 | 是 |
> | dnsOperationStatuses | 否 | 否 |
> | dnszones | 是 | 是 |
> | dnszones/A | 否 | 否 |
> | dnszones/AAAA | 否 | 否 |
> | dnszones/所有 | 否 | 否 |
> | dnszones/CAA | 否 | 否 |
> | dnszones/CNAME | 否 | 否 |
> | dnszones/MX | 否 | 否 |
> | dnszones/NS | 否 | 否 |
> | dnszones/PTR | 否 | 否 |
> | dnszones/记录集 | 否 | 否 |
> | dnszones/SOA | 否 | 否 |
> | dnszones/SRV | 否 | 否 |
> | dnszones/TXT | 否 | 否 |
> | expressRouteCircuits | 是 | 是 |
> | expressRouteCrossConnections | 是 | 是 |
> | expressRouteGateways | 是 | 是 |
> | expressRoutePorts | 是 | 是 |
> | expressRouteServiceProviders | 否 | 否 |
> | firewallPolicies | 是 | 是 |
> | frontdoors | 是，但受限制（请参见[下面的注释](#frontdoor)） | 是 |
> | frontdoorWebApplicationFirewallManagedRuleSets | 是，但受限制（请参见[下面的注释](#frontdoor)） | 否 |
> | frontdoorWebApplicationFirewallPolicies | 是，但受限制（请参见[下面的注释](#frontdoor)） | 是 |
> | getDnsResourceReference | 否 | 否 |
> | internalNotify | 否 | 否 |
> | loadBalancers | 是 | 否 |
> | localNetworkGateways | 是 | 是 |
> | natGateways | 是 | 是 |
> | networkIntentPolicies | 是 | 是 |
> | networkInterfaces | 是 | 是 |
> | networkProfiles | 是 | 是 |
> | networkSecurityGroups | 是 | 是 |
> | networkWatchers | 是 | 否 |
> | networkWatchers / connectionMonitors | 是 | 否 |
> | networkWatchers/重用功能区 | 是 | 否 |
> | networkWatchers / pingMeshes | 是 | 否 |
> | p2sVpnGateways | 是 | 是 |
> | privateDnsOperationStatuses | 否 | 否 |
> | privateDnsZones | 是 | 是 |
> | privateDnsZones/A | 否 | 否 |
> | privateDnsZones/AAAA | 否 | 否 |
> | privateDnsZones/所有 | 否 | 否 |
> | privateDnsZones/CNAME | 否 | 否 |
> | privateDnsZones/MX | 否 | 否 |
> | privateDnsZones/PTR | 否 | 否 |
> | privateDnsZones/SOA | 否 | 否 |
> | privateDnsZones/SRV | 否 | 否 |
> | privateDnsZones/TXT | 否 | 否 |
> | privateDnsZones / virtualNetworkLinks | 是 | 是 |
> | privateEndpoints | 是 | 是 |
> | privateLinkServices | 是 | 是 |
> | publicIPAddresses | 是 | 是 |
> | publicIPPrefixes | 是 | 是 |
> | routeFilters | 是 | 是 |
> | routeTables | 是 | 是 |
> | serviceEndpointPolicies | 是 | 是 |
> | trafficManagerGeographicHierarchies | 否 | 否 |
> | trafficmanagerprofiles | 是 | 是 |
> | trafficmanagerprofiles/heatMaps | 否 | 否 |
> | trafficManagerUserMetricsKeys | 否 | 否 |
> | virtualHubs | 是 | 是 |
> | virtualNetworkGateways | 是 | 是 |
> | virtualNetworks | 是 | 是 |
> | virtualNetworkTaps | 是 | 是 |
> | virtualWans | 是 | 是 |
> | vpnGateways | 是 | 否 |
> | vpnSites | 是 | 是 |
> | webApplicationFirewallPolicies | 是 | 是 |

<a id="frontdoor" />

> [!NOTE]
> 对于 Azure 前门服务，可以在创建资源时应用标记，但目前不支持更新或添加标记。


## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | namespaces | 是 | 否 |
> | 命名空间/notificationHubs | 是 | 否 |

## <a name="microsoftobjectstore"></a>ObjectStore

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | osNamespaces | 是 | 是 |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | HyperVSites | 是 | 是 |
> | ImportSites | 是 | 是 |
> | ServerSites | 是 | 是 |
> | VMwareSites | 是 | 是 |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | clusters | 是 | 是 |
> | devices | 否 | 否 |
> | linkTargets | 否 | 否 |
> | storageInsightConfigs | 否 | 否 |
> | workspaces | 是 | 是 |
> | 工作区/数据源 | 否 | 否 |
> | 工作区/Linkedservices.json | 否 | 否 |
> | 工作区/查询 | 否 | 否 |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | managementassociations | 否 | 否 |
> | managementconfigurations | 是 | 是 |
> | solutions | 是 | 是 |
> | 视图 | 是 | 是 |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | legacyPeerings | 否 | 否 |
> | peerAsns | 否 | 否 |
> | 对等互连 | 是 | 是 |
> | peeringServiceProviders | 否 | 否 |
> | peeringServices | 是 | 是 |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | policyEvents | 否 | 否 |
> | policyMetadata | 否 | 否 |
> | policyStates | 否 | 否 |
> | policyTrackedResources | 否 | 否 |
> | remediations | 否 | 否 |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | consoles | 否 | 否 |
> | dashboards | 是 | 是 |
> | userSettings | 否 | 否 |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | workspaceCollections | 是 | 是 |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | capacities | 是 | 是 |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | backupProtectedItems | 否 | 否 |
> | vaults | 是 | 是 |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | namespaces | 是 | 是 |
> | 命名空间/authorizationrules | 否 | 否 |
> | 命名空间/hybridconnections | 否 | 否 |
> | 命名空间/hybridconnections/authorizationrules | 否 | 否 |
> | 命名空间/wcfrelays | 否 | 否 |
> | 命名空间/wcfrelays/authorizationrules | 否 | 否 |

## <a name="microsoftremoteapp"></a>Microsoft RemoteApp

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | accounts | 否 | 否 |
> | 收藏 | 是 | 是 |
> | 集合/应用程序 | 否 | 否 |
> | 集合/securityprincipalsgetresponse | 否 | 否 |
> | templateImages | 否 | 否 |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | 查询 | 是 | 是 |
> | resourceChangeDetails | 否 | 否 |
> | resourceChanges | 否 | 否 |
> | 资源 | 否 | 否 |
> | resourcesHistory | 否 | 否 |
> | subscriptionsStatus | 否 | 否 |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | availabilityStatuses | 否 | 否 |
> | childAvailabilityStatuses | 否 | 否 |
> | childResources | 否 | 否 |
> | events | 否 | 否 |
> | impactedResources | 否 | 否 |
> | metadata | 否 | 否 |
> | 通知 | 否 | 否 |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | deployments | 是 | 否 |
> | 部署/操作 | 否 | 否 |
> | deploymentScripts | 是 | 是 |
> | deploymentScripts/logs | 否 | 否 |
> | links | 否 | 否 |
> | notifyResourceJobs | 否 | 否 |
> | providers | 否 | 否 |
> | resourceGroups | 是 | 否 |
> | subscriptions | 否 | 否 |
> | tenants | 否 | 否 |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | 服务 | 是 | 是 |
> | saasresources | 否 | 否 |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | jobcollections | 是 | 是 |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | resourceHealthMetadata | 否 | 否 |
> | searchServices | 是 | 是 |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | adaptiveNetworkHardenings | 否 | 否 |
> | advancedThreatProtectionSettings | 否 | 否 |
> | alerts | 否 | 否 |
> | allowedConnections | 否 | 否 |
> | applicationWhitelistings | 否 | 否 |
> | assessmentMetadata | 否 | 否 |
> | assessments | 否 | 否 |
> | autoDismissAlertsRules | 否 | 否 |
> | 自动化 | 是 | 是 |
> | AutoProvisioningSettings | 否 | 否 |
> | Compliances | 否 | 否 |
> | dataCollectionAgents | 否 | 否 |
> | deviceSecurityGroups | 否 | 否 |
> | discoveredSecuritySolutions | 否 | 否 |
> | externalSecuritySolutions | 否 | 否 |
> | InformationProtectionPolicies | 否 | 否 |
> | iotSecuritySolutions | 是 | 是 |
> | iotSecuritySolutions / analyticsModels | 否 | 否 |
> | iotSecuritySolutions / analyticsModels / aggregatedAlerts | 否 | 否 |
> | iotSecuritySolutions / analyticsModels / aggregatedRecommendations | 否 | 否 |
> | jitNetworkAccessPolicies | 否 | 否 |
> | networkData | 否 | 否 |
> | 策略 | 否 | 否 |
> | pricings | 否 | 否 |
> | regulatoryComplianceStandards | 否 | 否 |
> | regulatoryComplianceStandards / regulatoryComplianceControls | 否 | 否 |
> | regulatoryComplianceStandards / regulatoryComplianceControls / regulatoryComplianceAssessments | 否 | 否 |
> | securityContacts | 否 | 否 |
> | securitySolutions | 否 | 否 |
> | securitySolutionsReferenceData | 否 | 否 |
> | securityStatuses | 否 | 否 |
> | securityStatusesSummaries | 否 | 否 |
> | serverVulnerabilityAssessments | 否 | 否 |
> | 设置 | 否 | 否 |
> | subAssessments | 否 | 否 |
> | 任务 | 否 | 否 |
> | topologies | 否 | 否 |
> | workspaceSettings | 否 | 否 |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | 否 | 否 |
> | diagnosticSettingsCategories | 否 | 否 |

## <a name="microsoftsecurityinsights"></a>SecurityInsights

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | 聚合 | 否 | 否 |
> | alertRules | 否 | 否 |
> | alertRuleTemplates | 否 | 否 |
> | 书签 | 否 | 否 |
> | cases | 否 | 否 |
> | dataConnectors | 否 | 否 |
> | 实体 | 否 | 否 |
> | entityQueries | 否 | 否 |
> | officeConsents | 否 | 否 |
> | 设置 | 否 | 否 |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | namespaces | 是 | 否 |
> | 命名空间/authorizationrules | 否 | 否 |
> | 命名空间/disasterrecoveryconfigs | 否 | 否 |
> | 命名空间/eventgridfilters | 否 | 否 |
> | 命名空间/networkrulesets | 否 | 否 |
> | 命名空间/队列 | 否 | 否 |
> | 命名空间/队列/authorizationrules | 否 | 否 |
> | 命名空间/主题 | 否 | 否 |
> | 命名空间/主题/authorizationrules | 否 | 否 |
> | 命名空间/主题/订阅 | 否 | 否 |
> | 命名空间/主题/订阅/规则 | 否 | 否 |
> | premiumMessagingRegions | 否 | 否 |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | 服务 | 是 | 是 |
> | clusters | 是 | 是 |
> | clusters/applications | 否 | 否 |
> | containerGroups | 是 | 是 |
> | containerGroupSets | 是 | 是 |
> | edgeclusters | 是 | 是 |
> | edgeclusters/应用程序 | 否 | 否 |
> | networks | 是 | 是 |
> | secretstores | 是 | 是 |
> | secretstores/证书 | 否 | 否 |
> | secretstores/机密 | 否 | 否 |
> | volumes | 是 | 是 |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | 服务 | 是 | 是 |
> | containerGroups | 是 | 是 |
> | gateways | 是 | 是 |
> | networks | 是 | 是 |
> | secrets | 是 | 是 |
> | volumes | 是 | 是 |

## <a name="microsoftservices"></a>Microsoft. 服务

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | providerRegistrations | 否 | 否 |
> | providerRegistrations / resourceTypeRegistrations | 否 | 否 |
> | rollouts | 是 | 是 |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | SignalR | 是 | 是 |
> | SignalR/eventGridFilters | 否 | 否 |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | SiteRecoveryVault | 是 | 是 |

## <a name="microsoftsoftwareplan"></a>SoftwarePlan

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | hybridUseBenefits | 否 | 否 |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | applicationDefinitions | 是 | 是 |
> | 服务 | 是 | 是 |
> | jitRequests | 是 | 是 |


## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | managedInstances | 是 | 是 |
> | managedInstances/数据库 | 否 | 否 |
> | managedInstances/数据库/backupShortTermRetentionPolicies | 否 | 否 |
> | managedInstances/数据库/架构/表/列/sensitivityLabels | 否 | 否 |
> | managedInstances/数据库/vulnerabilityAssessments | 否 | 否 |
> | managedInstances/数据库/vulnerabilityAssessments/规则/基线 | 否 | 否 |
> | managedInstances / encryptionProtector | 否 | 否 |
> | managedInstances/keys | 否 | 否 |
> | managedInstances / restorableDroppedDatabases / backupShortTermRetentionPolicies | 否 | 否 |
> | managedInstances / vulnerabilityAssessments | 否 | 否 |
> | servers | 是 | 是 |
> | 服务器/管理员 | 否 | 否 |
> | 服务器/communicationLinks | 否 | 否 |
> | servers/databases | 是（请参见[下面的注释](#sqlnote)） | 是 |
> | 服务器/encryptionProtector | 否 | 否 |
> | 服务器/firewallRules | 否 | 否 |
> | 服务器/密钥 | 否 | 否 |
> | 服务器/restorableDroppedDatabases | 否 | 否 |
> | 服务器/serviceobjectives | 否 | 否 |
> | 服务器/tdeCertificates | 否 | 否 |
> | virtualClusters | 否 | 否 |

<a id="sqlnote" />

> [!NOTE]
> Master 数据库不支持标记，但其他数据库（包括 Azure SQL 数据仓库数据库）支持标记。 Azure SQL 数据仓库数据库必须处于活动（而非暂停）状态。

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | SqlVirtualMachineGroups | 是 | 是 |
> | SqlVirtualMachineGroups / AvailabilityGroupListeners | 否 | 否 |
> | SqlVirtualMachines | 是 | 是 |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | storageAccounts | 是 | 是 |
> | storageAccounts/blobServices | 否 | 否 |
> | storageAccounts/fileServices | 否 | 否 |
> | storageAccounts/queueServices | 否 | 否 |
> | storageAccounts/服务 | 否 | 否 |
> | storageAccounts/services/metricDefinitions | 否 | 否 |
> | storageAccounts/tableServices | 否 | 否 |
> | usages | 否 | 否 |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | 缓存 | 是 | 是 |
> | 缓存/storageTargets | 否 | 否 |
> | usageModels | 否 | 否 |

## <a name="microsoftstoragereplication"></a>StorageReplication

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | replicationGroups | 否 | 否 |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | 是 | 是 |
> | storageSyncServices / registeredServers | 否 | 否 |
> | storageSyncServices / syncGroups | 否 | 否 |
> | storageSyncServices / syncGroups / cloudEndpoints | 否 | 否 |
> | storageSyncServices / syncGroups / serverEndpoints | 否 | 否 |
> | storageSyncServices/工作流 | 否 | 否 |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | 是 | 是 |
> | storageSyncServices / registeredServers | 否 | 否 |
> | storageSyncServices / syncGroups | 否 | 否 |
> | storageSyncServices / syncGroups / cloudEndpoints | 否 | 否 |
> | storageSyncServices / syncGroups / serverEndpoints | 否 | 否 |
> | storageSyncServices/工作流 | 否 | 否 |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | 是 | 是 |
> | storageSyncServices / registeredServers | 否 | 否 |
> | storageSyncServices / syncGroups | 否 | 否 |
> | storageSyncServices / syncGroups / cloudEndpoints | 否 | 否 |
> | storageSyncServices / syncGroups / serverEndpoints | 否 | 否 |
> | storageSyncServices/工作流 | 否 | 否 |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | managers | 是 | 是 |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | streamingjobs | 是（见下方备注） | 是 |

> [!NOTE]
> Streamingjobs 运行时无法添加标记。 停止要添加标记的资源。

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | 取消 | 否 | 否 |
> | CreateSubscription | 否 | 否 |
> | enable | 否 | 否 |
> | 重命名 | 否 | 否 |
> | SubscriptionDefinitions | 否 | 否 |
> | SubscriptionOperations | 否 | 否 |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | environments | 是 | 否 |
> | 环境/accessPolicies | 否 | 否 |
> | environments/eventsources | 是 | 否 |
> | 环境/referenceDataSets | 是 | 否 |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | dedicatedCloudNodes | 是 | 是 |
> | dedicatedCloudServices | 是 | 是 |
> | virtualMachines | 是 | 是 |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | apiManagementAccounts | 否 | 否 |
> | apiManagementAccounts/apiAcls | 否 | 否 |
> | apiManagementAccounts/api | 否 | 否 |
> | apiManagementAccounts/api/apiAcls | 否 | 否 |
> | apiManagementAccounts/api/connectionAcls | 否 | 否 |
> | apiManagementAccounts/api/连接 | 否 | 否 |
> | apiManagementAccounts/api/connections/connectionAcls | 否 | 否 |
> | apiManagementAccounts/api/localizedDefinitions | 否 | 否 |
> | apiManagementAccounts/connectionAcls | 否 | 否 |
> | apiManagementAccounts/连接 | 否 | 否 |
> | billingMeters | 否 | 否 |
> | certificates | 是 | 是 |
> | connectionGateways | 是 | 是 |
> | connections | 是 | 是 |
> | customApis | 是 | 是 |
> | deletedSites | 否 | 否 |
> | functions | 否 | 否 |
> | hostingEnvironments | 是 | 是 |
> | hostingEnvironments/multiRolePools | 否 | 否 |
> | hostingEnvironments/辅助角色池 | 否 | 否 |
> | publishingUsers | 否 | 否 |
> | 建议 | 否 | 否 |
> | resourceHealthMetadata | 否 | 否 |
> | runtimes | 否 | 否 |
> | serverFarms | 是 | 是 |
> | serverFarms/eventGridFilters | 否 | 否 |
> | sites | 是 | 是 |
> | 站点/配置  | 否 | 否 |
> | sites/eventGridFilters | 否 | 否 |
> | sites/hostNameBindings | 否 | 否 |
> | sites/Networkconfig.netcfg | 否 | 否 |
> | sites/premieraddons | 是 | 是 |
> | sites/slots | 是 | 是 |
> | 站点/槽/eventGridFilters | 否 | 否 |
> | 站点/槽/hostNameBindings | 否 | 否 |
> | 站点/槽/Networkconfig.netcfg | 否 | 否 |
> | sourceControls | 否 | 否 |
> | validate | 否 | 否 |
> | verifyHostingEnvironmentVnet | 否 | 否 |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | 否 | 否 |
> | diagnosticSettingsCategories | 否 | 否 |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | DeviceServices | 是 | 是 |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | components | 否 | 否 |
> | componentsSummary | 否 | 否 |
> | monitorInstances | 否 | 否 |
> | monitorInstancesSummary | 否 | 否 |
> | monitors | 否 | 否 |
> | notificationSettings | 否 | 否 |

## <a name="next-steps"></a>后续步骤

若要了解如何将标记应用于资源，请参见[使用标记来组织 Azure 资源](resource-group-using-tags.md)。
