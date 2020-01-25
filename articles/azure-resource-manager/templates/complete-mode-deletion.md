---
title: 전체 모드 삭제
description: 리소스 종류가 Azure Resource Manager 템플릿에서 전체 모드 삭제를 처리하는 방법을 보여줍니다.
ms.topic: conceptual
ms.date: 01/23/2020
ms.openlocfilehash: b24bf2d8076dbaa1975f5444d597647156a31f4f
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76715591"
---
# <a name="deletion-of-azure-resources-for-complete-mode-deployments"></a>완료 모드 배포를 위한 Azure 리소스의 삭제

이 문서에서는 완료 모드로 배포된 템플릿에 없는 경우 리소스 종류가 삭제를 처리하는 방법을 설명합니다.

当类型不在以完整模式部署的模板中时，将删除标记为 **"是"** 的资源类型。

如果不在模板中，则不会自动删除标记为 "**否**" 的资源类型。但是，如果删除父资源，它们将被删除。 동작에 대한 전체 설명은 [Azure Resource Manager 배포 모드](deployment-modes.md)를 참조하세요.

如果在[模板中部署到多个资源组](cross-resource-group-deployment.md)，则部署操作中指定的资源组中的资源可以被删除。 辅助资源组中的资源不会被删除。

跳转到资源提供程序命名空间：
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [加载项](#microsoftaddons)
> - [ADHybridHealthService](#microsoftadhybridhealthservice)
> - [Microsoft Advisor](#microsoftadvisor)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft.AppConfiguration](#microsoftappconfiguration)
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
> - [Microsoft.BingMaps](#microsoftbingmaps)
> - [Microsoft.Blockchain](#microsoftblockchain)
> - [Microsoft.Blueprint](#microsoftblueprint)
> - [Microsoft.BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Microsoft. 容量](#microsoftcapacity)
> - [Microsoft.Cdn](#microsoftcdn)
> - [Microsoft.CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [ClassicInfrastructureMigrate](#microsoftclassicinfrastructuremigrate)
> - [Microsoft.ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft 商业](#microsoftcommerce)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft. 消耗](#microsoftconsumption)
> - [Microsoft.ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft.CortanaAnalytics](#microsoftcortanaanalytics)
> - [Microsoft.CostManagement](#microsoftcostmanagement)
> - [CustomerLockbox](#microsoftcustomerlockbox)
> - [CustomProviders](#microsoftcustomproviders)
> - [Microsoft.DataBox](#microsoftdatabox)
> - [Microsoft.DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft.Databricks](#microsoftdatabricks)
> - [Microsoft.DataCatalog](#microsoftdatacatalog)
> - [Microsoft.DataFactory](#microsoftdatafactory)
> - [Microsoft.DataLakeAnalytics](#microsoftdatalakeanalytics)
> - [Microsoft.DataLakeStore](#microsoftdatalakestore)
> - [Microsoft.DataMigration](#microsoftdatamigration)
> - [DataShare](#microsoftdatashare)
> - [Microsoft.DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft.DBforMySQL](#microsoftdbformysql)
> - [Microsoft.DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft.DeploymentManager](#microsoftdeploymentmanager)
> - [DesktopVirtualization](#microsoftdesktopvirtualization)
> - [Microsoft.Devices](#microsoftdevices)
> - [DevOps](#microsoftdevops)
> - [Microsoft.DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [DynamicsLcs](#microsoftdynamicslcs)
> - [Microsoft.EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft. 功能](#microsoftfeatures)
> - [Microsoft. 库](#microsoftgallery)
> - [Microsoft.Genomics](#microsoftgenomics)
> - [Microsoft.GuestConfiguration](#microsoftguestconfiguration)
> - [Microsoft.HanaOnAzure](#microsofthanaonazure)
> - [Microsoft.hardwaresecuritymodules](#microsofthardwaresecuritymodules)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [Microsoft.HealthcareApis](#microsofthealthcareapis)
> - [Microsoft.HybridCompute](#microsofthybridcompute)
> - [Microsoft.HybridData](#microsofthybriddata)
> - [Hydra](#microsofthydra)
> - [Microsoft.ImportExport](#microsoftimportexport)
> - [Microsoft Intune](#microsoftintune)
> - [Microsoft.IoTCentral](#microsoftiotcentral)
> - [Microsoft.IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft.LabServices](#microsoftlabservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [Microsoft.MachineLearning](#microsoftmachinelearning)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft.ManagedIdentity](#microsoftmanagedidentity)
> - [ManagedServices](#microsoftmanagedservices)
> - [Microsoft. 管理](#microsoftmanagement)
> - [Microsoft.Maps](#microsoftmaps)
> - [Microsoft Marketplace](#microsoftmarketplace)
> - [Microsoft.MarketplaceApps](#microsoftmarketplaceapps)
> - [MarketplaceOrdering](#microsoftmarketplaceordering)
> - [Microsoft.Media](#microsoftmedia)
> - [Microservices4Spring](#microsoftmicroservices4spring)
> - [Microsoft.Migrate](#microsoftmigrate)
> - [MixedReality](#microsoftmixedreality)
> - [Microsoft.NetApp](#microsoftnetapp)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [ObjectStore](#microsoftobjectstore)
> - [OffAzure](#microsoftoffazure)
> - [Microsoft.OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft.OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft.Peering](#microsoftpeering)
> - [Microsoft.PolicyInsights](#microsoftpolicyinsights)
> - [Microsoft.Portal](#microsoftportal)
> - [Microsoft.PowerBI](#microsoftpowerbi)
> - [Microsoft.PowerBIDedicated](#microsoftpowerbidedicated)
> - [ProjectBabylon](#microsoftprojectbabylon)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft RemoteApp](#microsoftremoteapp)
> - [ResourceGraph](#microsoftresourcegraph)
> - [ResourceHealth](#microsoftresourcehealth)
> - [Microsoft.Resources](#microsoftresources)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft.Scheduler](#microsoftscheduler)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [SecurityGraph](#microsoftsecuritygraph)
> - [SecurityInsights](#microsoftsecurityinsights)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft. 服务](#microsoftservices)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [Microsoft.SiteRecovery](#microsoftsiterecovery)
> - [SoftwarePlan](#microsoftsoftwareplan)
> - [Microsoft.Solutions](#microsoftsolutions)
> - [Microsoft .SQL](#microsoftsql)
> - [Microsoft.SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft.StorageCache](#microsoftstoragecache)
> - [StorageReplication](#microsoftstoragereplication)
> - [Microsoft.StorageSync](#microsoftstoragesync)
> - [Microsoft.StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft.StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft. 订阅](#microsoftsubscription)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft.VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [VnfManager](#microsoftvnfmanager)
> - [Microsoft.Web](#microsoftweb)
> - [WindowsDefenderATP](#microsoftwindowsdefenderatp)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | DomainServices | 예 |
> | DomainServices/oucontainer | 아닙니다. |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | supportProviders | 아닙니다. |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | aadsupportcases | 아닙니다. |
> | addsservices | 아닙니다. |
> | agents | 아닙니다. |
> | anonymousapiusers | 아닙니다. |
> | 구성 | 아닙니다. |
> | 로그 | 아닙니다. |
> | reports | 아닙니다. |
> | servicehealthmetrics | 아닙니다. |
> | services | 아닙니다. |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 구성 | 아닙니다. |
> | generateRecommendations | 아닙니다. |
> | metadata | 아닙니다. |
> | 동영상 추천 기능 | 아닙니다. |
> | suppressions | 아닙니다. |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | actionRules | 예 |
> | 경고 | 아닙니다. |
> | alertsList | 아닙니다. |
> | alertsMetaData | 아닙니다. |
> | alertsSummary | 아닙니다. |
> | alertsSummaryList | 아닙니다. |
> | 意见 | 아닙니다. |
> | smartDetectorAlertRules | 예 |
> | smartDetectorRuntimeEnvironments | 아닙니다. |
> | smartGroups | 아닙니다. |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 서버 | 예 |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | reportFeedback | 아닙니다. |
> | 서비스 | 예 |
> | validateServiceName | 아닙니다. |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | configurationStores | 예 |
> | configurationStores / eventGridFilters | 아닙니다. |

## <a name="microsoftappplatform"></a>AppPlatform

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | Spring | 예 |

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | attestationProviders | 아닙니다. |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | classicAdministrators | 아닙니다. |
> | dataAliases | 아닙니다. |
> | denyAssignments | 아닙니다. |
> | elevateAccess | 아닙니다. |
> | findOrphanRoleAssignments | 아닙니다. |
> | locks | 아닙니다. |
> | 권한 | 아닙니다. |
> | policyAssignments | 아닙니다. |
> | policyDefinitions | 아닙니다. |
> | policySetDefinitions | 아닙니다. |
> | providerOperations | 아닙니다. |
> | roleAssignments | 아닙니다. |
> | roleAssignmentsUsageMetrics | 아닙니다. |
> | roleDefinitions | 아닙니다. |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | automationAccounts | 예 |
> | automationAccounts/配置 | 예 |
> | automationAccounts/作业 | 아닙니다. |
> | automationAccounts/runbook | 예 |
> | automationAccounts/softwareUpdateConfigurations | 아닙니다. |
> | automationAccounts/webhook | 아닙니다. |

## <a name="microsoftazconfig"></a>Azconfig

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | configurationStores | 예 |
> | configurationStores / eventGridFilters | 아닙니다. |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | environments | 아닙니다. |
> | 环境/帐户 | 아닙니다. |
> | 环境/帐户/命名空间 | 아닙니다. |
> | 环境/帐户/命名空间/配置 | 아닙니다. |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | b2cDirectories | 예 |
> | b2ctenants | 아닙니다. |

## <a name="microsoftazuredata"></a>AzureData

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | hybridDataManagers | 예 |
> | postgresInstances | 예 |
> | sqlBigDataClusters | 예 |
> | sqlInstances | 예 |
> | sqlServerRegistrations | 예 |
> | sqlServerRegistrations/sqlServers | 아닙니다. |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | registrations | 예 |
> | 注册/customerSubscriptions | 아닙니다. |
> | 注册/产品 | 아닙니다. |
> | verificationKeys | 아닙니다. |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | batchAccounts | 예 |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | billingAccounts | 아닙니다. |
> | billingAccounts/协议 | 아닙니다. |
> | billingAccounts / billingPermissions | 아닙니다. |
> | billingAccounts / billingProfiles | 아닙니다. |
> | billingAccounts / billingProfiles / billingPermissions | 아닙니다. |
> | billingAccounts / billingProfiles / billingRoleAssignments | 아닙니다. |
> | billingAccounts / billingProfiles / billingRoleDefinitions | 아닙니다. |
> | billingAccounts / billingProfiles / billingSubscriptions | 아닙니다. |
> | billingAccounts / billingProfiles / createBillingRoleAssignment | 아닙니다. |
> | billingAccounts/billingProfiles/customers | 아닙니다. |
> | billingAccounts/billingProfiles/说明 | 아닙니다. |
> | billingAccounts/billingProfiles/发票 | 아닙니다. |
> | billingAccounts/billingProfiles/发票/价目表 | 아닙니다. |
> | billingAccounts / billingProfiles / invoiceSections | 아닙니다. |
> | billingAccounts / billingProfiles / invoiceSections / billingPermissions | 아닙니다. |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleAssignments | 아닙니다. |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleDefinitions | 아닙니다. |
> | billingAccounts / billingProfiles / invoiceSections / billingSubscriptions | 아닙니다. |
> | billingAccounts / billingProfiles / invoiceSections / createBillingRoleAssignment | 아닙니다. |
> | billingAccounts / billingProfiles / invoiceSections / initiateTransfer | 아닙니다. |
> | billingAccounts/billingProfiles/invoiceSections/products | 아닙니다. |
> | billingAccounts/billingProfiles/invoiceSections/products/transfer | 아닙니다. |
> | billingAccounts/billingProfiles/invoiceSections/products/updateAutoRenew | 아닙니다. |
> | billingAccounts/billingProfiles/invoiceSections/事务 | 아닙니다. |
> | billingAccounts/billingProfiles/invoiceSections/传输 | 아닙니다. |
> | billingAccounts / BillingProfiles / patchOperations | 아닙니다. |
> | billingAccounts / billingProfiles / paymentMethods | 아닙니다. |
> | billingAccounts/billingProfiles/策略 | 아닙니다. |
> | billingAccounts/billingProfiles/价目表 | 아닙니다. |
> | billingAccounts / billingProfiles / pricesheetDownloadOperations | 아닙니다. |
> | billingAccounts/billingProfiles/products | 아닙니다. |
> | billingAccounts/billingProfiles/事务 | 아닙니다. |
> | billingAccounts / billingRoleAssignments | 아닙니다. |
> | billingAccounts / billingRoleDefinitions | 아닙니다. |
> | billingAccounts / billingSubscriptions | 아닙니다. |
> | billingAccounts/billingSubscriptions/发票 | 아닙니다. |
> | billingAccounts / createBillingRoleAssignment | 아닙니다. |
> | billingAccounts / createInvoiceSectionOperations | 아닙니다. |
> | billingAccounts/客户 | 아닙니다. |
> | billingAccounts/customers/billingPermissions | 아닙니다. |
> | billingAccounts/customers/billingSubscriptions | 아닙니다. |
> | billingAccounts/customers/initiateTransfer | 아닙니다. |
> | billingAccounts/客户/策略 | 아닙니다. |
> | billingAccounts/客户/产品 | 아닙니다. |
> | billingAccounts/客户/事务 | 아닙니다. |
> | billingAccounts/客户/转移 | 아닙니다. |
> | billingAccounts/部门 | 아닙니다. |
> | billingAccounts / enrollmentAccounts | 아닙니다. |
> | billingAccounts/发票 | 아닙니다. |
> | billingAccounts / invoiceSections | 아닙니다. |
> | billingAccounts / invoiceSections / billingSubscriptionMoveOperations | 아닙니다. |
> | billingAccounts / invoiceSections / billingSubscriptions | 아닙니다. |
> | billingAccounts/invoiceSections/billingSubscriptions/transfer | 아닙니다. |
> | billingAccounts/invoiceSections/提升 | 아닙니다. |
> | billingAccounts / invoiceSections / initiateTransfer | 아닙니다. |
> | billingAccounts / invoiceSections / patchOperations | 아닙니다. |
> | billingAccounts / invoiceSections / productMoveOperations | 아닙니다. |
> | billingAccounts/invoiceSections/products | 아닙니다. |
> | billingAccounts/invoiceSections/products/transfer | 아닙니다. |
> | billingAccounts/invoiceSections/products/updateAutoRenew | 아닙니다. |
> | billingAccounts/invoiceSections/事务 | 아닙니다. |
> | billingAccounts/invoiceSections/传输 | 아닙니다. |
> | billingAccounts / lineOfCredit | 아닙니다. |
> | billingAccounts / patchOperations | 아닙니다. |
> | billingAccounts / paymentMethods | 아닙니다. |
> | billingAccounts/products | 아닙니다. |
> | billingAccounts/事务 | 아닙니다. |
> | billingPeriods | 아닙니다. |
> | billingPermissions | 아닙니다. |
> | billingProperty | 아닙니다. |
> | billingRoleAssignments | 아닙니다. |
> | billingRoleDefinitions | 아닙니다. |
> | createBillingRoleAssignment | 아닙니다. |
> | departments | 아닙니다. |
> | enrollmentAccounts | 아닙니다. |
> | invoices | 아닙니다. |
> | transfers | 아닙니다. |
> | 传输/acceptTransfer | 아닙니다. |
> | 传输/declineTransfer | 아닙니다. |
> | 传输/operationStatus | 아닙니다. |
> | 传输/validateTransfer | 아닙니다. |
> | validateAddress | 아닙니다. |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | mapApis | 예 |
> | updateCommunicationPreference | 아닙니다. |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | blockchainMembers | 예 |
> | cordaMembers | 예 |
> | 감시자 | 예 |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | blueprintAssignments | 아닙니다. |
> | blueprintAssignments / assignmentOperations | 아닙니다. |
> | blueprintAssignments/操作 | 아닙니다. |
> | blueprints | 아닙니다. |
> | 蓝图/项目 | 아닙니다. |
> | 蓝图/版本 | 아닙니다. |
> | 蓝图/版本/项目 | 아닙니다. |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | botServices | 예 |
> | botServices/通道 | 아닙니다. |
> | botServices/连接 | 아닙니다. |
> | 언어 | 아닙니다. |
> | 템플릿 | 아닙니다. |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | Redis | 예 |
> | RedisConfigDefinition | 아닙니다. |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | appliedReservations | 아닙니다. |
> | autoQuotaIncrease | 아닙니다. |
> | calculateExchange | 아닙니다. |
> | calculatePrice | 아닙니다. |
> | calculatePurchasePrice | 아닙니다. |
> | catalogs | 아닙니다. |
> | commercialReservationOrders | 아닙니다. |
> | exchange | 아닙니다. |
> | placePurchaseOrder | 아닙니다. |
> | reservationOrders | 아닙니다. |
> | reservationOrders / calculateRefund | 아닙니다. |
> | reservationOrders/merge | 아닙니다. |
> | reservationOrders/预订 | 아닙니다. |
> | reservationOrders/保留/修订 | 아닙니다. |
> | reservationOrders/return | 아닙니다. |
> | reservationOrders/split | 아닙니다. |
> | reservationOrders/swap | 아닙니다. |
> | reservations | 아닙니다. |
> | resourceProviders | 아닙니다. |
> | 리소스 | 아닙니다. |
> | validateReservationOrder | 아닙니다. |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | CdnWebApplicationFirewallManagedRuleSets | 아닙니다. |
> | CdnWebApplicationFirewallPolicies | 예 |
> | edgenodes | 아닙니다. |
> | 프로필 | 예 |
> | 配置文件/终结点 | 예 |
> | 配置文件/终结点/customdomains | 아닙니다. |
> | 配置文件/终结点/源 | 아닙니다. |
> | validateProbe | 아닙니다. |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | certificateOrders | 예 |
> | Certificateorder/证书 | 아닙니다. |
> | validateCertificateRegistrationInformation | 아닙니다. |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | capabilities | 아닙니다. |
> | domainNames | 예 |
> | domainNames/功能 | 아닙니다. |
> | domainNames / internalLoadBalancers | 아닙니다. |
> | domainNames / serviceCertificates | 아닙니다. |
> | domainNames/槽 | 아닙니다. |
> | domainNames/插槽/角色 | 아닙니다. |
> | domainNames/槽位/role/metricDefinitions | 아닙니다. |
> | domainNames/插槽/角色/指标 | 아닙니다. |
> | moveSubscriptionResources | 아닙니다. |
> | operatingSystemFamilies | 아닙니다. |
> | operatingSystems | 아닙니다. |
> | quotas | 아닙니다. |
> | resourceTypes | 아닙니다. |
> | validateSubscriptionMoveAvailability | 아닙니다. |
> | virtualMachines | 예 |
> | virtualMachines/diagnosticSettings | 아닙니다. |
> | virtualMachines/metricDefinitions | 아닙니다. |
> | virtualMachines/指标 | 아닙니다. |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | classicInfrastructureResources | 아닙니다. |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | capabilities | 아닙니다. |
> | expressRouteCrossConnections | 아닙니다. |
> | expressRouteCrossConnections/对等互连 | 아닙니다. |
> | gatewaySupportedDevices | 아닙니다. |
> | networkSecurityGroups | 예 |
> | quotas | 아닙니다. |
> | reservedIps | 예 |
> | virtualNetworks | 예 |
> | virtualNetworks/remoteVirtualNetworkPeeringProxies | 아닙니다. |
> | virtualNetworks/virtualNetworkPeerings | 아닙니다. |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | capabilities | 아닙니다. |
> | disks | 아닙니다. |
> | images | 아닙니다. |
> | osImages | 아닙니다. |
> | osPlatformImages | 아닙니다. |
> | publicImages | 아닙니다. |
> | quotas | 아닙니다. |
> | storageAccounts | 예 |
> | storageAccounts/blobServices | 아닙니다. |
> | storageAccounts/fileServices | 아닙니다. |
> | storageAccounts/metricDefinitions | 아닙니다. |
> | storageAccounts/指标 | 아닙니다. |
> | storageAccounts/queueServices | 아닙니다. |
> | storageAccounts/服务 | 아닙니다. |
> | storageAccounts/services/diagnosticSettings | 아닙니다. |
> | storageAccounts/services/metricDefinitions | 아닙니다. |
> | storageAccounts/服务/指标 | 아닙니다. |
> | storageAccounts/tableServices | 아닙니다. |
> | storageAccounts/vmImages | 아닙니다. |
> | vmImages | 아닙니다. |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 계정 | 예 |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | RateCard | 아닙니다. |
> | UsageAggregates | 아닙니다. |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | availabilitySets | 예 |
> | diskEncryptionSets | 예 |
> | disks | 예 |
> | galleries | 예 |
> | 库/应用程序 | 아닙니다. |
> | 库/应用程序/版本 | 아닙니다. |
> | 库/图像 | 아닙니다. |
> | 库/图像/版本 | 아닙니다. |
> | hostGroups | 예 |
> | hostGroups/主机 | 예 |
> | images | 예 |
> | proximityPlacementGroups | 예 |
> | restorePointCollections | 예 |
> | restorePointCollections / restorePoints | 아닙니다. |
> | sharedVMImages | 예 |
> | sharedVMImages/版本 | 아닙니다. |
> | 스냅샷 | 예 |
> | virtualMachines | 예 |
> | virtualMachines/extensions | 예 |
> | virtualMachines/metricDefinitions | 아닙니다. |
> | virtualMachineScaleSets | 예 |
> | virtualMachineScaleSets/extensions | 아닙니다. |
> | virtualMachineScaleSets/networkInterfaces | 아닙니다. |
> | virtualMachineScaleSets/publicIPAddresses | 아닙니다. |
> | virtualMachineScaleSets/virtualMachines | 아닙니다. |
> | virtualMachineScaleSets/virtualMachines/networkInterfaces | 아닙니다. |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | AggregatedCost | 아닙니다. |
> | 잔액 | 아닙니다. |
> | 예산 | 아닙니다. |
> | Charges | 아닙니다. |
> | CostTags | 아닙니다. |
> | credits | 아닙니다. |
> | events | 아닙니다. |
> | 예측 | 아닙니다. |
> | lots | 아닙니다. |
> | Marketplace | 아닙니다. |
> | Pricesheets | 아닙니다. |
> | products | 아닙니다. |
> | ReservationDetails | 아닙니다. |
> | ReservationRecommendations | 아닙니다. |
> | ReservationSummaries | 아닙니다. |
> | ReservationTransactions | 아닙니다. |
> | 태그 | 아닙니다. |
> | tenants | 아닙니다. |
> | 용어 | 아닙니다. |
> | UsageDetails | 아닙니다. |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | containerGroups | 예 |
> | serviceAssociationLinks | 아닙니다. |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | registries | 예 |
> | 注册表/生成 | 아닙니다. |
> | 注册表/生成/取消 | 아닙니다. |
> | 注册表/生成/getLogLink | 아닙니다. |
> | 注册表/buildTasks | 예 |
> | 注册表/buildTasks/步骤 | 아닙니다. |
> | 注册表/eventGridFilters | 아닙니다. |
> | 注册表/generateCredentials | 아닙니다. |
> | 注册表/getBuildSourceUploadUrl | 아닙니다. |
> | 注册表/System.web.clientservices.providers.iclientformsauthenticationcredentialsprovider.getcredentials | 아닙니다. |
> | 注册表/importImage | 아닙니다. |
> | 注册表/queueBuild | 아닙니다. |
> | 注册表/regenerateCredential | 아닙니다. |
> | 注册表/regenerateCredentials | 아닙니다. |
> | 注册表/复制 | 예 |
> | 注册表/运行 | 아닙니다. |
> | 注册表/运行/取消 | 아닙니다. |
> | 注册表/scheduleRun | 아닙니다. |
> | 注册表/scopeMaps | 아닙니다. |
> | 注册表/taskRuns | 예 |
> | 注册表/任务 | 예 |
> | 注册表/标记 | 아닙니다. |
> | 注册表/updatePolicies | 아닙니다. |
> | 注册表/webhook | 예 |
> | 注册表/webhook/getCallbackConfig | 아닙니다. |
> | 注册表/webhook/ping | 아닙니다. |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | containerServices | 예 |
> | managedClusters | 예 |
> | openShiftManagedClusters | 예 |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 계정 | 예 |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 경고 | 아닙니다. |
> | BillingAccounts | 아닙니다. |
> | 예산 | 아닙니다. |
> | CloudConnectors | 아닙니다. |
> | 커넥터 | 예 |
> | Departments | 아닙니다. |
> | 차원 | 아닙니다. |
> | EnrollmentAccounts | 아닙니다. |
> | 내보내기 | 아닙니다. |
> | ExternalBillingAccounts | 아닙니다. |
> | ExternalBillingAccounts/警报 | 아닙니다. |
> | ExternalBillingAccounts/维度 | 아닙니다. |
> | ExternalBillingAccounts/预测 | 아닙니다. |
> | ExternalBillingAccounts/查询 | 아닙니다. |
> | ExternalSubscriptions | 아닙니다. |
> | ExternalSubscriptions/警报 | 아닙니다. |
> | ExternalSubscriptions/维度 | 아닙니다. |
> | ExternalSubscriptions/预测 | 아닙니다. |
> | ExternalSubscriptions/查询 | 아닙니다. |
> | 预测 | 아닙니다. |
> | 쿼리 | 아닙니다. |
> | register | 아닙니다. |
> | Reportconfigs | 아닙니다. |
> | 보고서 | 아닙니다. |
> | 설정 | 아닙니다. |
> | showbackRules | 아닙니다. |
> | 뷰 | 아닙니다. |

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | requests | 아닙니다. |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 相关性 | 아닙니다. |
> | resourceProviders | 예 |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | jobs | 예 |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | DataBoxEdgeDevices | 예 |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | workspaces | 예 |
> | 工作区/dbWorkspaces | 아닙니다. |
> | 工作区/storageEncryption | 아닙니다. |
> | 工作区/virtualNetworkPeerings | 아닙니다. |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | catalogs | 예 |
> | datacatalogs | 예 |
> | datacatalogs/数据源 | 아닙니다. |
> | datacatalogs/数据源/扫描 | 아닙니다. |
> | datacatalogs/数据源/扫描/数据集 | 아닙니다. |
> | datacatalogs/数据源/扫描/触发器 | 아닙니다. |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | dataFactories | 예 |
> | Microsoft.azure.management.datafactories/diagnosticSettings | 아닙니다. |
> | Microsoft.azure.management.datafactories/metricDefinitions | 아닙니다. |
> | dataFactorySchema | 아닙니다. |
> | factories | 예 |
> | 工厂/integrationRuntimes | 아닙니다. |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 계정 | 예 |
> | 帐户/dataLakeStoreAccounts | 아닙니다. |
> | 帐户/storageAccounts | 아닙니다. |
> | 帐户/storageAccounts/容器 | 아닙니다. |
> | 帐户/transferAnalyticsUnits | 아닙니다. |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 계정 | 예 |
> | 帐户/eventGridFilters | 아닙니다. |
> | 帐户/firewallRules | 아닙니다. |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | services | 예 |
> | 服务/项目 | 예 |

## <a name="microsoftdatashare"></a>DataShare

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 계정 | 예 |
> | 帐户/共享 | 아닙니다. |
> | 帐户/共享/数据集 | 아닙니다. |
> | 帐户/共享/邀请 | 아닙니다. |
> | 帐户/共享/providersharesubscriptions | 아닙니다. |
> | 帐户/共享/synchronizationSettings | 아닙니다. |
> | 帐户/sharesubscriptions | 아닙니다. |
> | accounts/sharesubscriptions/consumerSourceDataSets | 아닙니다. |
> | accounts/sharesubscriptions/datasetmappings | 아닙니다. |
> | 帐户/sharesubscriptions/触发器 | 아닙니다. |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 서버 | 예 |
> | 服务器/顾问 | 아닙니다. |
> | 服务器/密钥 | 아닙니다. |
> | 服务器/privateEndpointConnectionProxies | 아닙니다. |
> | 服务器/privateEndpointConnections | 아닙니다. |
> | 服务器/privateLinkResources | 아닙니다. |
> | 服务器/queryTexts | 아닙니다. |
> | 服务器/recoverableServers | 아닙니다. |
> | 服务器/topQueryStatistics | 아닙니다. |
> | 服务器/virtualNetworkRules | 아닙니다. |
> | 服务器/waitStatistics | 아닙니다. |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 서버 | 예 |
> | 服务器/顾问 | 아닙니다. |
> | 服务器/密钥 | 아닙니다. |
> | 服务器/privateEndpointConnectionProxies | 아닙니다. |
> | 服务器/privateEndpointConnections | 아닙니다. |
> | 服务器/privateLinkResources | 아닙니다. |
> | 服务器/queryTexts | 아닙니다. |
> | 服务器/recoverableServers | 아닙니다. |
> | 服务器/topQueryStatistics | 아닙니다. |
> | 服务器/virtualNetworkRules | 아닙니다. |
> | 服务器/waitStatistics | 아닙니다. |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | serverGroups | 예 |
> | 서버 | 예 |
> | 服务器/顾问 | 아닙니다. |
> | 服务器/密钥 | 아닙니다. |
> | 服务器/privateEndpointConnectionProxies | 아닙니다. |
> | 服务器/privateEndpointConnections | 아닙니다. |
> | 服务器/privateLinkResources | 아닙니다. |
> | 服务器/queryTexts | 아닙니다. |
> | 服务器/recoverableServers | 아닙니다. |
> | 服务器/topQueryStatistics | 아닙니다. |
> | 服务器/virtualNetworkRules | 아닙니다. |
> | 服务器/waitStatistics | 아닙니다. |
> | serversv2 | 예 |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | artifactSources | 예 |
> | rollouts | 예 |
> | serviceTopologies | 예 |
> | serviceTopologies/服务 | 예 |
> | serviceTopologies/services/serviceUnits | 예 |
> | 단계 | 예 |

## <a name="microsoftdesktopvirtualization"></a>DesktopVirtualization

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | applicationgroups | 예 |
> | applicationgroups/应用程序 | 아닙니다. |
> | applicationgroups/台式机 | 아닙니다. |
> | applicationgroups / startmenuitems | 아닙니다. |
> | hostpools | 예 |
> | hostpools / sessionhosts | 아닙니다. |
> | hostpools / sessionhosts / usersessions | 아닙니다. |
> | hostpools / usersessions | 아닙니다. |
> | workspaces | 예 |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | ElasticPools | 예 |
> | ElasticPools / IotHubTenants | 예 |
> | ElasticPools/IotHubTenants/securitySettings | 아닙니다. |
> | IotHubs | 예 |
> | IotHubs / eventGridFilters | 아닙니다. |
> | IotHubs/securitySettings | 아닙니다. |
> | ProvisioningServices | 예 |
> | usages | 아닙니다. |

## <a name="microsoftdevops"></a>DevOps

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 段 | 예 |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | controllers | 예 |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | labcenters | 예 |
> | labs | 예 |
> | 实验室/环境 | 예 |
> | 实验室/serviceRunners | 예 |
> | 实验室/virtualMachines | 예 |
> | schedules | 예 |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | databaseAccountNames | 아닙니다. |
> | databaseAccounts | 예 |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | domains | 예 |
> | 域/domainOwnershipIdentifiers | 아닙니다. |
> | generateSsoRequest | 아닙니다. |
> | topLevelDomains | 아닙니다. |
> | validateDomainRegistrationInformation | 아닙니다. |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | lcsprojects | 아닙니다. |
> | lcsprojects / clouddeployments | 아닙니다. |
> | lcsprojects/连接器 | 아닙니다. |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | services | 예 |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | domains | 예 |
> | 域/主题 | 아닙니다. |
> | eventSubscriptions | 아닙니다. |
> | extensionTopics | 아닙니다. |
> | partnerNamespaces | 예 |
> | partnerNamespaces/eventChannels | 아닙니다. |
> | partnerRegistrations | 예 |
> | partnerTopics | 예 |
> | systemTopics | 예 |
> | systemTopics/eventSubscriptions | 아닙니다. |
> | topics | 예 |
> | topicTypes | 아닙니다. |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | clusters | 예 |
> | 네임스페이스 | 예 |
> | 命名空间/authorizationrules | 아닙니다. |
> | 命名空间/disasterrecoveryconfigs | 아닙니다. |
> | 命名空间/eventhubs | 아닙니다. |
> | 命名空间/eventhubs/authorizationrules | 아닙니다. |
> | 命名空间/eventhubs/consumergroups | 아닙니다. |
> | 命名空间/networkrulesets | 아닙니다. |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | SaaS 앱 개발 | 아닙니다. |
> | providers | 아닙니다. |

## <a name="microsoftgallery"></a>Microsoft.Gallery

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | enroll | 아닙니다. |
> | galleryitems | 아닙니다. |
> | generateartifactaccessuri | 아닙니다. |
> | myareas | 아닙니다. |
> | myareas/区域 | 아닙니다. |
> | myareas/区域/区域 | 아닙니다. |
> | myareas/区域/区域/galleryitems | 아닙니다. |
> | myareas/areas/galleryitems | 아닙니다. |
> | myareas / galleryitems | 아닙니다. |
> | register | 아닙니다. |
> | 리소스 | 아닙니다. |
> | retrieveresourcesbyid | 아닙니다. |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 계정 | 예 |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | autoManagedAccounts | 예 |
> | autoManagedVmConfigurationProfiles | 예 |
> | configurationProfileAssignments | 아닙니다. |
> | guestConfigurationAssignments | 아닙니다. |
> | software | 아닙니다. |
> | softwareUpdateProfile | 아닙니다. |
> | softwareUpdates | 아닙니다. |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | hanaInstances | 예 |
> | sapMonitors | 예 |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | dedicatedHSMs | 예 |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | clusters | 예 |
> | 群集/应用程序 | 아닙니다. |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | services | 예 |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 造 | 예 |
> | 计算机/扩展 | 예 |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | dataManagers | 예 |

## <a name="microsofthydra"></a>Hydra

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | components | 예 |
> | networkScopes | 예 |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | jobs | 예 |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | diagnosticSettings | 아닙니다. |
> | diagnosticSettingsCategories | 아닙니다. |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | appTemplates | 아닙니다. |
> | IoTApps | 예 |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 그래프 | 예 |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | deletedVaults | 아닙니다. |
> | hsmPools | 예 |
> | vaults | 예 |
> | 保管库/accessPolicies | 아닙니다. |
> | 保管库/eventGridFilters | 아닙니다. |
> | 保管库/机密 | 아닙니다. |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | clusters | 예 |
> | 群集/attacheddatabaseconfigurations | 아닙니다. |
> | 群集/数据库 | 아닙니다. |
> | 群集/数据库/dataconnections | 아닙니다. |
> | 群集/数据库/eventhubconnections | 아닙니다. |
> | 群集/数据库/principalassignments | 아닙니다. |
> | 群集/principalassignments | 아닙니다. |
> | 群集/sharedidentities | 아닙니다. |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | labaccounts | 예 |
> | 사용자 | 아닙니다. |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | hostingEnvironments | 예 |
> | integrationAccounts | 예 |
> | integrationServiceEnvironments | 예 |
> | integrationServiceEnvironments / managedApis | 예 |
> | isolatedEnvironments | 예 |
> | workflows | 예 |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | commitmentPlans | 예 |
> | webServices | 예 |
> | 작업 영역 | 예 |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | workspaces | 예 |
> | 工作区/计算 | 아닙니다. |
> | 工作区/eventGridFilters | 아닙니다. |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | Identities | 아닙니다. |
> | userAssignedIdentities | 예 |

## <a name="microsoftmanagedservices"></a>ManagedServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | marketplaceRegistrationDefinitions | 아닙니다. |
> | registrationAssignments | 아닙니다. |
> | registrationDefinitions | 아닙니다. |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | getEntities | 아닙니다. |
> | managementGroups | 아닙니다. |
> | 리소스 | 아닙니다. |
> | startTenantBackfill | 아닙니다. |
> | tenantBackfillStatus | 아닙니다. |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 계정 | 예 |
> | 帐户/eventGridFilters | 아닙니다. |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | offers | 아닙니다. |
> | offerTypes | 아닙니다. |
> | offerTypes/发布服务器 | 아닙니다. |
> | offerTypes/发布者/产品/服务 | 아닙니다. |
> | offerTypes/发布者/产品/服务/计划 | 아닙니다. |
> | offerTypes/发布者/产品/服务/计划/协议 | 아닙니다. |
> | offerTypes/发布者/服务/计划/配置 | 아닙니다. |
> | offerTypes/发布者/服务/计划/配置/importImage | 아닙니다. |
> | privategalleryitems | 아닙니다. |
> | privateStoreClient | 아닙니다. |
> | products | 아닙니다. |
> | 发行者 | 아닙니다. |
> | 发布者/产品/服务 | 아닙니다. |
> | 发布者/服务/修正 | 아닙니다. |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | classicDevServices | 예 |
> | updateCommunicationPreference | 아닙니다. |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | agreements | 아닙니다. |
> | offertypes | 아닙니다. |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | mediaservices | 예 |
> | windowsazure.mediaservices/accountFilters | 아닙니다. |
> | windowsazure.mediaservices/资产 | 아닙니다. |
> | windowsazure.mediaservices/资产/Assetfilter | 아닙니다. |
> | windowsazure.mediaservices/contentKeyPolicies | 아닙니다. |
> | windowsazure.mediaservices/eventGridFilters | 아닙니다. |
> | windowsazure.mediaservices/liveEventOperations | 아닙니다. |
> | windowsazure.mediaservices/liveEvents | 예 |
> | windowsazure.mediaservices/liveEvents/liveOutputs | 아닙니다. |
> | windowsazure.mediaservices/liveOutputOperations | 아닙니다. |
> | windowsazure.mediaservices/mediaGraphs | 아닙니다. |
> | windowsazure.mediaservices/streamingEndpointOperations | 아닙니다. |
> | windowsazure.mediaservices/Streamingendpoint | 예 |
> | windowsazure.mediaservices/streamingLocators | 아닙니다. |
> | windowsazure.mediaservices/streamingPolicies | 아닙니다. |
> | windowsazure.mediaservices/转换 | 아닙니다. |
> | windowsazure.mediaservices/转换/作业 | 아닙니다. |

## <a name="microsoftmicroservices4spring"></a>Microservices4Spring

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | appClusters | 예 |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | assessmentProjects | 예 |
> | migrateprojects | 예 |
> | moveCollections | 예 |
> | projects | 예 |

## <a name="microsoftmixedreality"></a>MixedReality

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | holographicsBroadcastAccounts | 예 |
> | objectUnderstandingAccounts | 예 |
> | remoteRenderingAccounts | 예 |
> | spatialAnchorsAccounts | 예 |
> | surfaceReconstructionAccounts | 예 |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | netAppAccounts | 예 |
> | netAppAccounts / capacityPools | 예 |
> | netAppAccounts/capacityPools/卷 | 예 |
> | netAppAccounts/capacityPools/卷集/mountTargets | 예 |
> | netAppAccounts/capacityPools/卷/快照 | 예 |
## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | applicationGateways | 예 |
> | applicationGatewayWebApplicationFirewallPolicies | 예 |
> | applicationSecurityGroups | 예 |
> | azureFirewallFqdnTags | 아닙니다. |
> | azureFirewalls | 예 |
> | bastionHosts | 예 |
> | bgpServiceCommunities | 아닙니다. |
> | connections | 예 |
> | ddosCustomPolicies | 예 |
> | ddosProtectionPlans | 예 |
> | dnsOperationStatuses | 아닙니다. |
> | dnszones | 예 |
> | dnszones/A | 아닙니다. |
> | dnszones/AAAA | 아닙니다. |
> | dnszones/所有 | 아닙니다. |
> | dnszones/CAA | 아닙니다. |
> | dnszones/CNAME | 아닙니다. |
> | dnszones/MX | 아닙니다. |
> | dnszones/NS | 아닙니다. |
> | dnszones/PTR | 아닙니다. |
> | dnszones/记录集 | 아닙니다. |
> | dnszones/SOA | 아닙니다. |
> | dnszones/SRV | 아닙니다. |
> | dnszones/TXT | 아닙니다. |
> | expressRouteCircuits | 예 |
> | expressRouteCrossConnections | 예 |
> | expressRouteGateways | 예 |
> | expressRoutePorts | 예 |
> | expressRouteServiceProviders | 아닙니다. |
> | firewallPolicies | 예 |
> | frontdoors | 예 |
> | frontdoorWebApplicationFirewallManagedRuleSets | 아닙니다. |
> | frontdoorWebApplicationFirewallPolicies | 예 |
> | getDnsResourceReference | 아닙니다. |
> | internalNotify | 아닙니다. |
> | loadBalancers | 예 |
> | localNetworkGateways | 예 |
> | natGateways | 예 |
> | networkIntentPolicies | 예 |
> | networkInterfaces | 예 |
> | networkProfiles | 예 |
> | networkSecurityGroups | 예 |
> | networkWatchers | 예 |
> | networkWatchers / connectionMonitors | 예 |
> | networkWatchers/重用功能区 | 예 |
> | networkWatchers / pingMeshes | 예 |
> | p2sVpnGateways | 예 |
> | privateDnsOperationStatuses | 아닙니다. |
> | privateDnsZones | 예 |
> | privateDnsZones/A | 아닙니다. |
> | privateDnsZones/AAAA | 아닙니다. |
> | privateDnsZones/所有 | 아닙니다. |
> | privateDnsZones/CNAME | 아닙니다. |
> | privateDnsZones/MX | 아닙니다. |
> | privateDnsZones/PTR | 아닙니다. |
> | privateDnsZones/SOA | 아닙니다. |
> | privateDnsZones/SRV | 아닙니다. |
> | privateDnsZones/TXT | 아닙니다. |
> | privateDnsZones / virtualNetworkLinks | 예 |
> | privateEndpoints | 예 |
> | privateLinkServices | 예 |
> | publicIPAddresses | 예 |
> | publicIPPrefixes | 예 |
> | routeFilters | 예 |
> | routeTables | 예 |
> | serviceEndpointPolicies | 예 |
> | trafficManagerGeographicHierarchies | 아닙니다. |
> | trafficmanagerprofiles | 예 |
> | trafficmanagerprofiles/热图 | 아닙니다. |
> | trafficManagerUserMetricsKeys | 아닙니다. |
> | virtualHubs | 예 |
> | virtualNetworkGateways | 예 |
> | virtualNetworks | 예 |
> | virtualNetworkTaps | 예 |
> | virtualWans | 예 |
> | vpnGateways | 예 |
> | vpnSites | 예 |
> | webApplicationFirewallPolicies | 예 |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 네임스페이스 | 예 |
> | 命名空间/notificationHubs | 예 |

## <a name="microsoftobjectstore"></a>ObjectStore

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | osNamespaces | 예 |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | HyperVSites | 예 |
> | ImportSites | 예 |
> | ServerSites | 예 |
> | VMwareSites | 예 |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | clusters | 예 |
> | devices | 아닙니다. |
> | linkTargets | 아닙니다. |
> | storageInsightConfigs | 아닙니다. |
> | workspaces | 예 |
> | 工作区/dataExports | 아닙니다. |
> | 工作区/数据源 | 아닙니다. |
> | 工作区/Linkedservices.json | 아닙니다. |
> | 工作区/privateEndpointConnectionProxies | 아닙니다. |
> | 工作区/privateEndpointConnections | 아닙니다. |
> | 工作区/privateLinkResources | 아닙니다. |
> | 工作区/查询 | 아닙니다. |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | managementassociations | 아닙니다. |
> | managementconfigurations | 예 |
> | solutions | 예 |
> | 뷰 | 예 |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | legacyPeerings | 아닙니다. |
> | peerAsns | 아닙니다. |
> | 对等互连 | 예 |
> | peeringServiceProviders | 아닙니다. |
> | peeringServices | 예 |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | policyEvents | 아닙니다. |
> | policyMetadata | 아닙니다. |
> | policyStates | 아닙니다. |
> | policyTrackedResources | 아닙니다. |
> | remediations | 아닙니다. |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | consoles | 아닙니다. |
> | dashboards | 예 |
> | userSettings | 아닙니다. |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | workspaceCollections | 예 |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | capacities | 예 |

## <a name="microsoftprojectbabylon"></a>ProjectBabylon

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 계정 | 예 |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | backupProtectedItems | 아닙니다. |
> | vaults | 예 |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 네임스페이스 | 예 |
> | 命名空间/authorizationrules | 아닙니다. |
> | 命名空间/hybridconnections | 아닙니다. |
> | 命名空间/hybridconnections/authorizationrules | 아닙니다. |
> | 命名空间/wcfrelays | 아닙니다. |
> | 命名空间/wcfrelays/authorizationrules | 아닙니다. |

## <a name="microsoftremoteapp"></a>Microsoft RemoteApp

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 계정 | 아닙니다. |
> | 收藏 | 예 |
> | 集合/应用程序 | 아닙니다. |
> | 集合/securityprincipalsgetresponse | 아닙니다. |
> | templateImages | 아닙니다. |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 쿼리 | 예 |
> | resourceChangeDetails | 아닙니다. |
> | resourceChanges | 아닙니다. |
> | 리소스 | 아닙니다. |
> | resourcesHistory | 아닙니다. |
> | subscriptionsStatus | 아닙니다. |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | availabilityStatuses | 아닙니다. |
> | childAvailabilityStatuses | 아닙니다. |
> | childResources | 아닙니다. |
> | emergingissues | 아닙니다. |
> | events | 아닙니다. |
> | impactedResources | 아닙니다. |
> | metadata | 아닙니다. |
> | 알림 | 아닙니다. |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 배포 | 아닙니다. |
> | 部署/操作 | 아닙니다. |
> | deploymentScripts | 예 |
> | deploymentScripts/logs | 아닙니다. |
> | 링크 | 아닙니다. |
> | notifyResourceJobs | 아닙니다. |
> | providers | 아닙니다. |
> | resourceGroups | 아닙니다. |
> | 구독 | 아닙니다. |
> | tenants | 아닙니다. |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 애플리케이션 | 예 |
> | saasresources | 아닙니다. |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | jobcollections | 예 |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | resourceHealthMetadata | 아닙니다. |
> | searchServices | 예 |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | adaptiveNetworkHardenings | 아닙니다. |
> | advancedThreatProtectionSettings | 아닙니다. |
> | 경고 | 아닙니다. |
> | allowedConnections | 아닙니다. |
> | applicationWhitelistings | 아닙니다. |
> | assessmentMetadata | 아닙니다. |
> | 风险 | 아닙니다. |
> | autoDismissAlertsRules | 아닙니다. |
> | 自动化 | 예 |
> | AutoProvisioningSettings | 아닙니다. |
> | Compliances | 아닙니다. |
> | dataCollectionAgents | 아닙니다. |
> | deviceSecurityGroups | 아닙니다. |
> | discoveredSecuritySolutions | 아닙니다. |
> | externalSecuritySolutions | 아닙니다. |
> | InformationProtectionPolicies | 아닙니다. |
> | iotSecuritySolutions | 예 |
> | iotSecuritySolutions / analyticsModels | 아닙니다. |
> | iotSecuritySolutions / analyticsModels / aggregatedAlerts | 아닙니다. |
> | iotSecuritySolutions / analyticsModels / aggregatedRecommendations | 아닙니다. |
> | jitNetworkAccessPolicies | 아닙니다. |
> | Networkdata.xml | 아닙니다. |
> | 정책 | 아닙니다. |
> | pricings | 아닙니다. |
> | regulatoryComplianceStandards | 아닙니다. |
> | regulatoryComplianceStandards / regulatoryComplianceControls | 아닙니다. |
> | regulatoryComplianceStandards / regulatoryComplianceControls / regulatoryComplianceAssessments | 아닙니다. |
> | securityContacts | 아닙니다. |
> | securitySolutions | 아닙니다. |
> | securitySolutionsReferenceData | 아닙니다. |
> | securityStatuses | 아닙니다. |
> | securityStatusesSummaries | 아닙니다. |
> | serverVulnerabilityAssessments | 아닙니다. |
> | 설정 | 아닙니다. |
> | subAssessments | 아닙니다. |
> | tasks | 아닙니다. |
> | topologies | 아닙니다. |
> | workspaceSettings | 아닙니다. |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | diagnosticSettings | 아닙니다. |
> | diagnosticSettingsCategories | 아닙니다. |

## <a name="microsoftsecurityinsights"></a>SecurityInsights

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 聚合 | 아닙니다. |
> | alertRules | 아닙니다. |
> | alertRuleTemplates | 아닙니다. |
> | 书签 | 아닙니다. |
> | cases | 아닙니다. |
> | dataConnectors | 아닙니다. |
> | 엔터티 | 아닙니다. |
> | entityQueries | 아닙니다. |
> | officeConsents | 아닙니다. |
> | 설정 | 아닙니다. |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 네임스페이스 | 예 |
> | 命名空间/authorizationrules | 아닙니다. |
> | 命名空间/disasterrecoveryconfigs | 아닙니다. |
> | 命名空间/eventgridfilters | 아닙니다. |
> | 命名空间/networkrulesets | 아닙니다. |
> | 命名空间/队列 | 아닙니다. |
> | 命名空间/队列/authorizationrules | 아닙니다. |
> | 命名空间/主题 | 아닙니다. |
> | 命名空间/主题/authorizationrules | 아닙니다. |
> | 命名空间/主题/订阅 | 아닙니다. |
> | 命名空间/主题/订阅/规则 | 아닙니다. |
> | premiumMessagingRegions | 아닙니다. |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 애플리케이션 | 예 |
> | clusters | 예 |
> | 群集/应用程序 | 아닙니다. |
> | containerGroups | 예 |
> | containerGroupSets | 예 |
> | edgeclusters | 예 |
> | edgeclusters/应用程序 | 아닙니다. |
> | networks | 예 |
> | secretstores | 예 |
> | secretstores/证书 | 아닙니다. |
> | secretstores/机密 | 아닙니다. |
> | volumes | 예 |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 애플리케이션 | 예 |
> | containerGroups | 예 |
> | gateways | 예 |
> | networks | 예 |
> | secrets | 예 |
> | volumes | 예 |

## <a name="microsoftservices"></a>Microsoft. 服务

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | providerRegistrations | 아닙니다. |
> | providerRegistrations / resourceTypeRegistrations | 아닙니다. |
> | rollouts | 예 |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | SignalR | 예 |
> | SignalR/eventGridFilters | 아닙니다. |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | SiteRecoveryVault | 예 |

## <a name="microsoftsoftwareplan"></a>SoftwarePlan

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | hybridUseBenefits | 아닙니다. |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | applicationDefinitions | 예 |
> | 애플리케이션 | 예 |
> | jitRequests | 예 |

## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | managedInstances | 예 |
> | managedInstances/数据库 | 예 |
> | managedInstances/数据库/backupShortTermRetentionPolicies | 아닙니다. |
> | managedInstances/数据库/架构/表/列/sensitivityLabels | 아닙니다. |
> | managedInstances/数据库/vulnerabilityAssessments | 아닙니다. |
> | managedInstances/数据库/vulnerabilityAssessments/规则/基线 | 아닙니다. |
> | managedInstances / encryptionProtector | 아닙니다. |
> | managedInstances/keys | 아닙니다. |
> | managedInstances / restorableDroppedDatabases / backupShortTermRetentionPolicies | 아닙니다. |
> | managedInstances / vulnerabilityAssessments | 아닙니다. |
> | 서버 | 예 |
> | 服务器/管理员 | 아닙니다. |
> | 服务器/communicationLinks | 아닙니다. |
> | 服务器/数据库 | 예 |
> | 服务器/encryptionProtector | 아닙니다. |
> | 服务器/firewallRules | 아닙니다. |
> | 服务器/密钥 | 아닙니다. |
> | 服务器/restorableDroppedDatabases | 아닙니다. |
> | 服务器/serviceobjectives | 아닙니다. |
> | 服务器/tdeCertificates | 아닙니다. |
> | virtualClusters | 아닙니다. |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | SqlVirtualMachineGroups | 예 |
> | SqlVirtualMachineGroups / AvailabilityGroupListeners | 아닙니다. |
> | SqlVirtualMachines | 예 |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | storageAccounts | 예 |
> | storageAccounts/blobServices | 아닙니다. |
> | storageAccounts/fileServices | 아닙니다. |
> | storageAccounts/queueServices | 아닙니다. |
> | storageAccounts/服务 | 아닙니다. |
> | storageAccounts/services/metricDefinitions | 아닙니다. |
> | storageAccounts/tableServices | 아닙니다. |
> | usages | 아닙니다. |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 缓存 | 예 |
> | 缓存/storageTargets | 아닙니다. |
> | usageModels | 아닙니다. |

## <a name="microsoftstoragereplication"></a>StorageReplication

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | replicationGroups | 아닙니다. |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | storageSyncServices | 예 |
> | storageSyncServices / registeredServers | 아닙니다. |
> | storageSyncServices / syncGroups | 아닙니다. |
> | storageSyncServices / syncGroups / cloudEndpoints | 아닙니다. |
> | storageSyncServices / syncGroups / serverEndpoints | 아닙니다. |
> | storageSyncServices/工作流 | 아닙니다. |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | storageSyncServices | 예 |
> | storageSyncServices / registeredServers | 아닙니다. |
> | storageSyncServices / syncGroups | 아닙니다. |
> | storageSyncServices / syncGroups / cloudEndpoints | 아닙니다. |
> | storageSyncServices / syncGroups / serverEndpoints | 아닙니다. |
> | storageSyncServices/工作流 | 아닙니다. |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | storageSyncServices | 예 |
> | storageSyncServices / registeredServers | 아닙니다. |
> | storageSyncServices / syncGroups | 아닙니다. |
> | storageSyncServices / syncGroups / cloudEndpoints | 아닙니다. |
> | storageSyncServices / syncGroups / serverEndpoints | 아닙니다. |
> | storageSyncServices/工作流 | 아닙니다. |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | managers | 예 |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | streamingjobs | 예 |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | cancel | 아닙니다. |
> | CreateSubscription | 아닙니다. |
> | enable | 아닙니다. |
> | 이름 바꾸기 | 아닙니다. |
> | SubscriptionDefinitions | 아닙니다. |
> | SubscriptionOperations | 아닙니다. |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | environments | 예 |
> | 环境/accessPolicies | 아닙니다. |
> | 环境/eventsources | 예 |
> | 环境/referenceDataSets | 예 |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | dedicatedCloudNodes | 예 |
> | dedicatedCloudServices | 예 |
> | virtualMachines | 예 |

## <a name="microsoftvnfmanager"></a>VnfManager

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | devices | 예 |
> | 供应商 | 아닙니다. |
> | 供应商/sku | 아닙니다. |
> | vnfs | 예 |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | apiManagementAccounts | 아닙니다. |
> | apiManagementAccounts/apiAcls | 아닙니다. |
> | apiManagementAccounts/api | 아닙니다. |
> | apiManagementAccounts/api/apiAcls | 아닙니다. |
> | apiManagementAccounts/api/connectionAcls | 아닙니다. |
> | apiManagementAccounts/api/连接 | 아닙니다. |
> | apiManagementAccounts/api/connections/connectionAcls | 아닙니다. |
> | apiManagementAccounts/api/localizedDefinitions | 아닙니다. |
> | apiManagementAccounts/connectionAcls | 아닙니다. |
> | apiManagementAccounts/连接 | 아닙니다. |
> | billingMeters | 아닙니다. |
> | certificates | 예 |
> | connectionGateways | 예 |
> | connections | 예 |
> | customApis | 예 |
> | deletedSites | 아닙니다. |
> | hostingEnvironments | 예 |
> | hostingEnvironments/eventGridFilters | 아닙니다. |
> | hostingEnvironments/multiRolePools | 아닙니다. |
> | hostingEnvironments/辅助角色池 | 아닙니다. |
> | publishingUsers | 아닙니다. |
> | 동영상 추천 기능 | 아닙니다. |
> | resourceHealthMetadata | 아닙니다. |
> | runtimes | 아닙니다. |
> | serverFarms | 예 |
> | serverFarms/eventGridFilters | 아닙니다. |
> | sites | 예 |
> | 站点/配置  | 아닙니다. |
> | sites/eventGridFilters | 아닙니다. |
> | sites/hostNameBindings | 아닙니다. |
> | sites/Networkconfig.netcfg | 아닙니다. |
> | sites/premieraddons | 예 |
> | 站点/槽 | 예 |
> | 站点/槽/eventGridFilters | 아닙니다. |
> | 站点/槽/hostNameBindings | 아닙니다. |
> | 站点/槽/Networkconfig.netcfg | 아닙니다. |
> | sourceControls | 아닙니다. |
> | staticSites | 예 |
> | validate | 아닙니다. |
> | verifyHostingEnvironmentVnet | 아닙니다. |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | diagnosticSettings | 아닙니다. |
> | diagnosticSettingsCategories | 아닙니다. |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | DeviceServices | 예 |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | components | 아닙니다. |
> | componentsSummary | 아닙니다. |
> | monitorInstances | 아닙니다. |
> | monitorInstancesSummary | 아닙니다. |
> | monitors | 아닙니다. |
> | notificationSettings | 아닙니다. |

## <a name="next-steps"></a>다음 단계

쉼표로 구분된 값의 파일과 동일한 데이터를 가져오려면 [complete-mode-deletion.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/complete-mode-deletion.csv)를 다운로드합니다.
