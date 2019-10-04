---
title: 由资源类型进行的 Azure 资源管理器完全模式删除
description: 显示资源类型如何在 Azure 资源管理器模板中进行完全模式删除。
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 10/03/2019
ms.author: tomfitz
ms.openlocfilehash: cdc00ccc61e20865e993a18c72b930d9570896f2
ms.sourcegitcommit: f2d9d5133ec616857fb5adfb223df01ff0c96d0a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/03/2019
ms.locfileid: "71937051"
---
# <a name="deletion-of-azure-resources-for-complete-mode-deployments"></a>针对完全模式部署的 Azure 资源删除

本文描述了资源类型如何在不是以完全模式部署的模板中进行删除。

标记为 **Yes** 的资源类型不在以完全模式部署的模板中时，将会删除该类型。

标记为 **No** 的资源类型不在模板中时不会自动删除；但是，如果删除了父资源，则会删除它们。 有关此行为的完整描述，请参阅 [Azure 资源管理器部署模式](deployment-modes.md)。

跳转到资源提供程序命名空间：
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [加载项](#microsoftaddons)
> - [ADHybridHealthService](#microsoftadhybridhealthservice)
> - [Microsoft.Advisor](#microsoftadvisor)
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
> - [Microsoft.ClassicInfrastructureMigrate](#microsoftclassicinfrastructuremigrate)
> - [Microsoft.ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft 商业](#microsoftcommerce)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft. 消耗](#microsoftconsumption)
> - [Microsoft.ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft.ContentModerator](#microsoftcontentmoderator)
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
> - [Microsoft.Features](#microsoftfeatures)
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
> - [Microsoft.Management](#microsoftmanagement)
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
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft RemoteApp](#microsoftremoteapp)
> - [Microsoft.ResourceGraph](#microsoftresourcegraph)
> - [Microsoft.ResourceHealth](#microsoftresourcehealth)
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
> - [Microsoft.Subscription](#microsoftsubscription)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft.VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft.Web](#microsoftweb)
> - [WindowsDefenderATP](#microsoftwindowsdefenderatp)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | DomainServices | 是 |
> | DomainServices/oucontainer | 否 |
> | DomainServices/ReplicaSets | 是 |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | supportProviders | 否 |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | aadsupportcases | 否 |
> | addsservices | 否 |
> | 代理 | 否 |
> | anonymousapiusers | 否 |
> | 配置 | 否 |
> | logs | 否 |
> | 报告 | 否 |
> | servicehealthmetrics | 否 |
> | 服务 | 否 |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | 配置 | 否 |
> | generateRecommendations | 否 |
> | metadata | 否 |
> | 推荐 | 否 |
> | 禁止显示 | 否 |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | actionRules | 是 |
> | 警报 | 否 |
> | alertsList | 否 |
> | alertsMetaData | 否 |
> | alertsSummary | 否 |
> | alertsSummaryList | 否 |
> | 反馈 | 否 |
> | smartDetectorAlertRules | 是 |
> | smartDetectorRuntimeEnvironments | 否 |
> | smartGroups | 否 |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | 服务器 | 是 |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | reportFeedback | 否 |
> | 服务 | 是 |
> | validateServiceName | 否 |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | configurationStores | 是 |
> | configurationStores/eventGridFilters | 否 |

## <a name="microsoftappplatform"></a>AppPlatform

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | 春天颜色 | 是 |

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | attestationProviders | 否 |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | classicAdministrators | 否 |
> | dataAliases | 否 |
> | denyAssignments | 否 |
> | elevateAccess | 否 |
> | 锁定 | 否 |
> | 权限 | 否 |
> | policyAssignments | 否 |
> | policyDefinitions | 否 |
> | policySetDefinitions | 否 |
> | providerOperations | 否 |
> | roleAssignments | 否 |
> | roleDefinitions | 否 |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | automationAccounts | 是 |
> | automationAccounts/configurations | 是 |
> | automationAccounts/jobs | 否 |
> | automationAccounts/runbooks | 是 |
> | automationAccounts/softwareUpdateConfigurations | 否 |
> | automationAccounts/webhooks | 否 |

## <a name="microsoftazconfig"></a>Azconfig

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | configurationStores | 是 |
> | configurationStores/eventGridFilters | 否 |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | environments | 否 |
> | environments/accounts | 否 |
> | environments/accounts/namespaces | 否 |
> | environments/accounts/namespaces/configurations | 否 |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | b2cDirectories | 是 |
> | b2ctenants | 否 |

## <a name="microsoftazuredata"></a>AzureData

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | hybridDataManagers | 是 |
> | postgresInstances | 是 |
> | sqlBigDataClusters | 是 |
> | sqlInstances | 是 |
> | sqlServerRegistrations | 是 |
> | sqlServerRegistrations/sqlServers | 否 |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | registrations | 是 |
> | registrations/customerSubscriptions | 否 |
> | registrations/products | 否 |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | batchAccounts | 是 |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | billingAccounts | 否 |
> | billingAccounts/协议 | 否 |
> | billingAccounts/billingPermissions | 否 |
> | billingAccounts/billingProfiles | 否 |
> | billingAccounts/billingProfiles/billingPermissions | 否 |
> | billingAccounts/billingProfiles/billingRoleAssignments | 否 |
> | billingAccounts/billingProfiles/billingRoleDefinitions | 否 |
> | billingAccounts/billingProfiles/billingSubscriptions | 否 |
> | billingAccounts/billingProfiles/createBillingRoleAssignment | 否 |
> | billingAccounts/billingProfiles/customers | 否 |
> | billingAccounts/billingProfiles/invoices | 否 |
> | billingAccounts/billingProfiles/invoices/pricesheet | 否 |
> | billingAccounts/billingProfiles/invoiceSections | 否 |
> | billingAccounts/billingProfiles/invoiceSections/billingPermissions | 否 |
> | billingAccounts/billingProfiles/invoiceSections/billingRoleAssignments | 否 |
> | billingAccounts/billingProfiles/invoiceSections/billingRoleDefinitions | 否 |
> | billingAccounts/billingProfiles/invoiceSections/billingSubscriptions | 否 |
> | billingAccounts/billingProfiles/invoiceSections/createBillingRoleAssignment | 否 |
> | billingAccounts/billingProfiles/invoiceSections/initiateTransfer | 否 |
> | billingAccounts/billingProfiles/invoiceSections/products | 否 |
> | billingAccounts/billingProfiles/invoiceSections/products/transfer | 否 |
> | billingAccounts/billingProfiles/invoiceSections/products/updateAutoRenew | 否 |
> | billingAccounts/billingProfiles/invoiceSections/事务 | 否 |
> | billingAccounts/billingProfiles/invoiceSections/传输 | 否 |
> | billingAccounts/BillingProfiles/patchOperations | 否 |
> | billingAccounts/billingProfiles/paymentMethods | 否 |
> | billingAccounts/billingProfiles/policies | 否 |
> | billingAccounts/billingProfiles/pricesheet | 否 |
> | billingAccounts/billingProfiles/pricesheetDownloadOperations | 否 |
> | billingAccounts/billingProfiles/products | 否 |
> | billingAccounts/billingProfiles/transactions | 否 |
> | billingAccounts/billingRoleAssignments | 否 |
> | billingAccounts/billingRoleDefinitions | 否 |
> | billingAccounts/billingSubscriptions | 否 |
> | billingAccounts/createBillingRoleAssignment | 否 |
> | billingAccounts/createInvoiceSectionOperations | 否 |
> | billingAccounts/客户 | 否 |
> | billingAccounts/customers/billingSubscriptions | 否 |
> | billingAccounts/customers/initiateTransfer | 否 |
> | billingAccounts/客户/策略 | 否 |
> | billingAccounts/客户/产品 | 否 |
> | billingAccounts/客户/事务 | 否 |
> | billingAccounts/客户/转移 | 否 |
> | billingAccounts/departments | 否 |
> | billingAccounts/enrollmentAccounts | 否 |
> | billingAccounts/invoices | 否 |
> | billingAccounts/invoiceSections | 否 |
> | billingAccounts/invoiceSections/billingSubscriptionMoveOperations | 否 |
> | billingAccounts/invoiceSections/billingSubscriptions | 否 |
> | billingAccounts/invoiceSections/billingSubscriptions/transfer | 否 |
> | billingAccounts/invoiceSections/提升 | 否 |
> | billingAccounts/invoiceSections/initiateTransfer | 否 |
> | billingAccounts/invoiceSections/patchOperations | 否 |
> | billingAccounts/invoiceSections/productMoveOperations | 否 |
> | billingAccounts/invoiceSections/products | 否 |
> | billingAccounts/invoiceSections/products/transfer | 否 |
> | billingAccounts/invoiceSections/products/updateAutoRenew | 否 |
> | billingAccounts/invoiceSections/事务 | 否 |
> | billingAccounts/invoiceSections/transfers | 否 |
> | billingAccounts/lineOfCredit | 否 |
> | billingAccounts/patchOperations | 否 |
> | billingAccounts/paymentMethods | 否 |
> | billingAccounts/products | 否 |
> | billingAccounts/transactions | 否 |
> | billingPeriods | 否 |
> | BillingPermissions | 否 |
> | billingProperty | 否 |
> | BillingRoleAssignments | 否 |
> | BillingRoleDefinitions | 否 |
> | CreateBillingRoleAssignment | 否 |
> | departments | 否 |
> | enrollmentAccounts | 否 |
> | invoices | 否 |
> | transfers | 否 |
> | transfers/acceptTransfer | 否 |
> | transfers/declineTransfer | 否 |
> | transfers/operationStatus | 否 |
> | 传输/validateTransfer | 否 |
> | validateAddress | 否 |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | mapApis | 是 |
> | updateCommunicationPreference | 否 |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | blockchainMembers | 是 |
> | 观看者 | 是 |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | blueprintAssignments | 否 |
> | blueprintAssignments/assignmentOperations | 否 |
> | blueprintAssignments/operations | 否 |
> | blueprints | 否 |
> | blueprints/artifacts | 否 |
> | blueprints/versions | 否 |
> | blueprints/versions/artifacts | 否 |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | botServices | 是 |
> | botServices/channels | 否 |
> | botServices/connections | 否 |
> | 语言 | 否 |
> | 模板 | 否 |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | Redis | 是 |
> | RedisConfigDefinition | 否 |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | appliedReservations | 否 |
> | calculateExchange | 否 |
> | calculatePrice | 否 |
> | calculatePurchasePrice | 否 |
> | catalogs | 否 |
> | commercialReservationOrders | 否 |
> | 交易所 | 否 |
> | placePurchaseOrder | 否 |
> | reservationOrders | 否 |
> | reservationOrders/calculateRefund | 否 |
> | reservationOrders/merge | 否 |
> | reservationOrders/reservations | 否 |
> | reservationOrders/reservations/revisions | 否 |
> | reservationOrders/return | 否 |
> | reservationOrders/split | 否 |
> | reservationOrders/swap | 否 |
> | reservations | 否 |
> | 资源 | 否 |
> | validateReservationOrder | 否 |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | CdnWebApplicationFirewallManagedRuleSets | 否 |
> | CdnWebApplicationFirewallPolicies | 是 |
> | edgenodes | 否 |
> | profiles | 是 |
> | profiles/endpoints | 是 |
> | profiles/endpoints/customdomains | 否 |
> | profiles/endpoints/origins | 否 |
> | validateProbe | 否 |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | certificateOrders | 是 |
> | certificateOrders/certificates | 否 |
> | validateCertificateRegistrationInformation | 否 |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | capabilities | 否 |
> | domainNames | 是 |
> | domainNames/capabilities | 否 |
> | domainNames/internalLoadBalancers | 否 |
> | domainNames/serviceCertificates | 否 |
> | domainNames/slots | 否 |
> | domainNames/slots/roles | 否 |
> | domainNames/slots/roles/metricDefinitions | 否 |
> | domainNames/slots/roles/metrics | 否 |
> | moveSubscriptionResources | 否 |
> | operatingSystemFamilies | 否 |
> | operatingSystems | 否 |
> | quotas | 否 |
> | resourceTypes | 否 |
> | validateSubscriptionMoveAvailability | 否 |
> | virtualMachines | 是 |
> | virtualMachines/diagnosticSettings | 否 |
> | virtualMachines/metricDefinitions | 否 |
> | virtualMachines/metrics | 否 |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | classicInfrastructureResources | 否 |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | capabilities | 否 |
> | expressRouteCrossConnections | 否 |
> | expressRouteCrossConnections/peerings | 否 |
> | gatewaySupportedDevices | 否 |
> | networkSecurityGroups | 是 |
> | quotas | 否 |
> | reservedIps | 是 |
> | virtualNetworks | 是 |
> | virtualNetworks/remoteVirtualNetworkPeeringProxies | 否 |
> | virtualNetworks/virtualNetworkPeerings | 否 |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | capabilities | 否 |
> | 磁盘 | 否 |
> | 映像 | 否 |
> | osImages | 否 |
> | osPlatformImages | 否 |
> | publicImages | 否 |
> | quotas | 否 |
> | storageAccounts | 是 |
> | storageAccounts/blobServices | 否 |
> | storageAccounts/fileServices | 否 |
> | storageAccounts/metricDefinitions | 否 |
> | storageAccounts/metrics | 否 |
> | storageAccounts/queueServices | 否 |
> | storageAccounts/services | 否 |
> | storageAccounts/services/diagnosticSettings | 否 |
> | storageAccounts/services/metricDefinitions | 否 |
> | storageAccounts/services/metrics | 否 |
> | storageAccounts/tableServices | 否 |
> | storageAccounts/vmImages | 否 |
> | vmImages | 否 |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | 帐户 | 是 |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | RateCard | 否 |
> | UsageAggregates | 否 |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | availabilitySets | 是 |
> | diskEncryptionSets | 是 |
> | 磁盘 | 是 |
> | galleries | 是 |
> | galleries/applications | 否 |
> | galleries/applications/versions | 否 |
> | galleries/images | 否 |
> | galleries/images/versions | 否 |
> | hostGroups | 是 |
> | hostGroups/hosts | 是 |
> | 映像 | 是 |
> | proximityPlacementGroups | 是 |
> | restorePointCollections | 是 |
> | restorePointCollections/restorePoints | 否 |
> | sharedVMImages | 是 |
> | sharedVMImages/versions | 否 |
> | 快照 | 是 |
> | virtualMachines | 是 |
> | virtualMachines/extensions | 是 |
> | virtualMachines/metricDefinitions | 否 |
> | virtualMachineScaleSets | 是 |
> | virtualMachineScaleSets/extensions | 否 |
> | virtualMachineScaleSets/networkInterfaces | 否 |
> | virtualMachineScaleSets/publicIPAddresses | 否 |
> | virtualMachineScaleSets/virtualMachines | 否 |
> | virtualMachineScaleSets/virtualMachines/networkInterfaces | 否 |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | AggregatedCost | 否 |
> | 余额 | 否 |
> | 预算 | 否 |
> | Charges | 否 |
> | CostTags | 否 |
> | credits | 否 |
> | 事件 | 否 |
> | 预测 | 否 |
> | lots | 否 |
> | 市场 | 否 |
> | Pricesheets | 否 |
> | products | 否 |
> | ReservationDetails | 否 |
> | ReservationRecommendations | 否 |
> | ReservationSummaries | 否 |
> | ReservationTransactions | 否 |
> | Tags | 否 |
> | tenants | 否 |
> | 条款 | 否 |
> | UsageDetails | 否 |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | containerGroups | 是 |
> | serviceAssociationLinks | 否 |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | registries | 是 |
> | registries/builds | 否 |
> | registries/builds/cancel | 否 |
> | registries/builds/getLogLink | 否 |
> | registries/buildTasks | 是 |
> | registries/buildTasks/steps | 否 |
> | registries/eventGridFilters | 否 |
> | registries/getBuildSourceUploadUrl | 否 |
> | registries/GetCredentials | 否 |
> | registries/importImage | 否 |
> | registries/queueBuild | 否 |
> | registries/regenerateCredential | 否 |
> | registries/regenerateCredentials | 否 |
> | registries/replications | 是 |
> | registries/runs | 否 |
> | registries/runs/cancel | 否 |
> | registries/scheduleRun | 否 |
> | registries/tasks | 是 |
> | registries/updatePolicies | 否 |
> | registries/webhooks | 是 |
> | registries/webhooks/getCallbackConfig | 否 |
> | registries/webhooks/ping | 否 |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | containerServices | 是 |
> | managedClusters | 是 |
> | openShiftManagedClusters | 是 |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | 应用程序 | 是 |
> | updateCommunicationPreference | 否 |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | 帐户 | 是 |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | 警报 | 否 |
> | BillingAccounts | 否 |
> | 预算 | 否 |
> | CloudConnectors | 否 |
> | 连接器 | 是 |
> | Departments | 否 |
> | 维度 | 否 |
> | EnrollmentAccounts | 否 |
> | 导出作业 | 否 |
> | ExternalBillingAccounts | 否 |
> | ExternalBillingAccounts/警报 | 否 |
> | ExternalBillingAccounts/维度 | 否 |
> | ExternalBillingAccounts/预测 | 否 |
> | ExternalBillingAccounts/查询 | 否 |
> | ExternalSubscriptions | 否 |
> | ExternalSubscriptions/警报 | 否 |
> | ExternalSubscriptions/维度 | 否 |
> | ExternalSubscriptions/预测 | 否 |
> | ExternalSubscriptions/查询 | 否 |
> | 预测 | 否 |
> | 查询 | 否 |
> | register | 否 |
> | Reportconfigs | 否 |
> | 报告 | 否 |
> | 设置 | 否 |
> | showbackRules | 否 |
> | Views | 否 |

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | 请求 | 否 |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | 相关性 | 否 |
> | resourceProviders | 是 |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | 作业 | 是 |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | DataBoxEdgeDevices | 是 |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | 工作区 | 是 |
> | workspaces/virtualNetworkPeerings | 否 |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | catalogs | 是 |
> | datacatalogs | 是 |
> | datacatalogs/数据源 | 否 |
> | datacatalogs/数据源/扫描 | 否 |
> | datacatalogs/数据源/扫描/数据集 | 否 |
> | datacatalogs/数据源/扫描/触发器 | 否 |
> | datacatalogs/scantargets | 否 |
> | datacatalogs/scantargets/dataset | 否 |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | dataFactories | 是 |
> | dataFactories/diagnosticSettings | 否 |
> | dataFactories/metricDefinitions | 否 |
> | dataFactorySchema | 否 |
> | factories | 是 |
> | factories/integrationRuntimes | 否 |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | 帐户 | 是 |
> | accounts/dataLakeStoreAccounts | 否 |
> | accounts/storageAccounts | 否 |
> | accounts/storageAccounts/containers | 否 |
> | 帐户/transferAnalyticsUnits | 否 |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | 帐户 | 是 |
> | accounts/eventGridFilters | 否 |
> | accounts/firewallRules | 否 |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | 服务 | 是 |
> | services/projects | 是 |

## <a name="microsoftdatashare"></a>DataShare

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | 帐户 | 是 |
> | 帐户/共享 | 否 |
> | 帐户/共享/数据集 | 否 |
> | 帐户/共享/邀请 | 否 |
> | 帐户/共享/providersharesubscriptions | 否 |
> | 帐户/共享/synchronizationSettings | 否 |
> | 帐户/sharesubscriptions | 否 |
> | accounts/sharesubscriptions/consumerSourceDataSets | 否 |
> | accounts/sharesubscriptions/datasetmappings | 否 |
> | 帐户/sharesubscriptions/触发器 | 否 |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | 服务器 | 是 |
> | servers/advisors | 否 |
> | servers/queryTexts | 否 |
> | servers/recoverableServers | 否 |
> | servers/topQueryStatistics | 否 |
> | servers/virtualNetworkRules | 否 |
> | servers/waitStatistics | 否 |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | 服务器 | 是 |
> | servers/advisors | 否 |
> | servers/queryTexts | 否 |
> | servers/recoverableServers | 否 |
> | servers/topQueryStatistics | 否 |
> | servers/virtualNetworkRules | 否 |
> | servers/waitStatistics | 否 |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | serverGroups | 是 |
> | 服务器 | 是 |
> | servers/advisors | 否 |
> | 服务器/privateEndpointConnectionProxies | 否 |
> | 服务器/privateEndpointConnections | 否 |
> | 服务器/privateLinkResources | 否 |
> | servers/queryTexts | 否 |
> | servers/recoverableServers | 否 |
> | servers/topQueryStatistics | 否 |
> | servers/virtualNetworkRules | 否 |
> | servers/waitStatistics | 否 |
> | serversv2 | 是 |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | artifactSources | 是 |
> | rollouts | 是 |
> | serviceTopologies | 是 |
> | serviceTopologies/服务 | 是 |
> | serviceTopologies/services/serviceUnits | 是 |
> | steps | 是 |

## <a name="microsoftdesktopvirtualization"></a>DesktopVirtualization

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | applicationgroups | 是 |
> | applicationgroups/应用程序 | 否 |
> | applicationgroups/startmenuitems | 否 |
> | hostpools | 是 |
> | hostpools/sessionhosts | 否 |
> | hostpools/sessionhosts/usersessions | 否 |
> | hostpools/usersessions | 否 |
> | 工作区 | 是 |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | ElasticPools | 是 |
> | ElasticPools/IotHubTenants | 是 |
> | IotHubs | 是 |
> | IotHubs/eventGridFilters | 否 |
> | ProvisioningServices | 是 |
> | usages | 否 |

## <a name="microsoftdevops"></a>DevOps

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | 管道 | 是 |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | 控制器 | 是 |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | labcenters | 是 |
> | labs | 是 |
> | 实验室/环境 | 是 |
> | labs/serviceRunners | 是 |
> | labs/virtualMachines | 是 |
> | schedules | 是 |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | databaseAccountNames | 否 |
> | databaseAccounts | 是 |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | domains | 是 |
> | domains/domainOwnershipIdentifiers | 否 |
> | generateSsoRequest | 否 |
> | topLevelDomains | 否 |
> | validateDomainRegistrationInformation | 否 |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | lcsprojects | 否 |
> | lcsprojects/clouddeployments | 否 |
> | lcsprojects/connectors | 否 |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | 服务 | 是 |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | domains | 是 |
> | 域/主题 | 否 |
> | eventSubscriptions | 否 |
> | extensionTopics | 否 |
> | topics | 是 |
> | topicTypes | 否 |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | clusters | 是 |
> | 命名空间 | 是 |
> | namespaces/authorizationrules | 否 |
> | namespaces/disasterrecoveryconfigs | 否 |
> | namespaces/eventhubs | 否 |
> | namespaces/eventhubs/authorizationrules | 否 |
> | namespaces/eventhubs/consumergroups | 否 |
> | namespaces/networkrulesets | 否 |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | features | 否 |
> | providers | 否 |

## <a name="microsoftgallery"></a>Microsoft.Gallery

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | enroll | 否 |
> | galleryitems | 否 |
> | generateartifactaccessuri | 否 |
> | myareas | 否 |
> | myareas/areas | 否 |
> | myareas/areas/areas | 否 |
> | myareas/areas/areas/galleryitems | 否 |
> | myareas/areas/galleryitems | 否 |
> | myareas/galleryitems | 否 |
> | register | 否 |
> | 资源 | 否 |
> | retrieveresourcesbyid | 否 |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | 帐户 | 是 |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | autoManagedVmConfigurationProfiles | 是 |
> | guestConfigurationAssignments | 否 |
> | software | 否 |
> | softwareUpdateProfile | 否 |
> | softwareUpdates | 否 |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | hanainstances | 是 |
> | sapMonitors | 是 |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | dedicatedHSMs | 是 |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | clusters | 是 |
> | clusters/applications | 否 |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | 服务 | 是 |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | 计算机 | 是 |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | dataManagers | 是 |

## <a name="microsofthydra"></a>Hydra

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | components | 是 |
> | networkScopes | 是 |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | 作业 | 是 |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | diagnosticSettings | 否 |
> | diagnosticSettingsCategories | 否 |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | appTemplates | 否 |
> | IoTApps | 是 |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | 图形 | 是 |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | deletedVaults | 否 |
> | hsmPools | 是 |
> | vaults | 是 |
> | vaults/accessPolicies | 否 |
> | vaults/eventGridFilters | 否 |
> | vaults/secrets | 否 |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | clusters | 是 |
> | clusters/attacheddatabaseconfigurations | 否 |
> | clusters/databases | 否 |
> | clusters/databases/dataconnections | 否 |
> | clusters/databases/eventhubconnections | 否 |
> | 群集/sharedidentities | 否 |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | labaccounts | 是 |
> | 用户 | 否 |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | hostingEnvironments | 是 |
> | integrationAccounts | 是 |
> | integrationServiceEnvironments | 是 |
> | integrationServiceEnvironments/managedApis | 是 |
> | isolatedEnvironments | 是 |
> | workflows | 是 |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | commitmentPlans | 是 |
> | webServices | 是 |
> | 工作区 | 是 |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | 工作区 | 是 |
> | workspaces/computes | 否 |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | 标识 | 否 |
> | userAssignedIdentities | 是 |

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | marketplaceRegistrationDefinitions | 否 |
> | registrationAssignments | 否 |
> | registrationDefinitions | 否 |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | getEntities | 否 |
> | managementGroups | 否 |
> | 资源 | 否 |
> | startTenantBackfill | 否 |
> | tenantBackfillStatus | 否 |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | 帐户 | 是 |
> | accounts/eventGridFilters | 否 |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | 套餐 | 否 |
> | offerTypes | 否 |
> | offerTypes/publishers | 否 |
> | offerTypes/publishers/offers | 否 |
> | offerTypes/publishers/offers/plans | 否 |
> | offerTypes/publishers/offers/plans/agreements | 否 |
> | offerTypes/publishers/offers/plans/configs | 否 |
> | offerTypes/publishers/offers/plans/configs/importImage | 否 |
> | privategalleryitems | 否 |
> | products | 否 |
> | 位发布者 | 否 |
> | 发布者/产品/服务 | 否 |
> | 发布者/服务/修正 | 否 |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | classicDevServices | 是 |
> | updateCommunicationPreference | 否 |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | 协议 | 否 |
> | offertypes | 否 |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | mediaservices | 是 |
> | mediaservices/accountFilters | 否 |
> | mediaservices/assets | 否 |
> | mediaservices/assets/assetFilters | 否 |
> | mediaservices/contentKeyPolicies | 否 |
> | mediaservices/eventGridFilters | 否 |
> | mediaservices/liveEventOperations | 否 |
> | mediaservices/liveEvents | 是 |
> | mediaservices/liveEvents/liveOutputs | 否 |
> | mediaservices/liveOutputOperations | 否 |
> | mediaservices/streamingEndpointOperations | 否 |
> | mediaservices/streamingEndpoints | 是 |
> | mediaservices/streamingLocators | 否 |
> | mediaservices/streamingPolicies | 否 |
> | mediaservices/transforms | 否 |
> | mediaservices/transforms/jobs | 否 |

## <a name="microsoftmicroservices4spring"></a>Microservices4Spring

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | appClusters | 是 |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | assessmentProjects | 是 |
> | migrateprojects | 是 |
> | projects | 是 |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | holographicsBroadcastAccounts | 是 |
> | objectUnderstandingAccounts | 是 |
> | remoteRenderingAccounts | 是 |
> | spatialAnchorsAccounts | 是 |
> | surfaceReconstructionAccounts | 是 |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | netAppAccounts | 是 |
> | netAppAccounts/backupPolicies | 是 |
> | netAppAccounts/capacityPools | 是 |
> | netAppAccounts/capacityPools/卷 | 是 |
> | netAppAccounts/capacityPools/卷/备份 | 否 |
> | netAppAccounts/capacityPools/卷集/mountTargets | 是 |
> | netAppAccounts/capacityPools/卷/快照 | 是 |
> | netAppAccounts/保管库 | 否 |
## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | applicationGateways | 是 |
> | applicationGatewayWebApplicationFirewallPolicies | 是 |
> | applicationSecurityGroups | 是 |
> | azureFirewallFqdnTags | 否 |
> | azureFirewalls | 是 |
> | bastionHosts | 是 |
> | bgpServiceCommunities | 否 |
> | 连接 | 是 |
> | ddosCustomPolicies | 是 |
> | ddosProtectionPlans | 是 |
> | dnsOperationStatuses | 否 |
> | dnszones | 是 |
> | dnszones/A | 否 |
> | dnszones/AAAA | 否 |
> | dnszones/all | 否 |
> | dnszones/CAA | 否 |
> | dnszones/CNAME | 否 |
> | dnszones/MX | 否 |
> | dnszones/NS | 否 |
> | dnszones/PTR | 否 |
> | dnszones/recordsets | 否 |
> | dnszones/SOA | 否 |
> | dnszones/SRV | 否 |
> | dnszones/TXT | 否 |
> | expressRouteCircuits | 是 |
> | expressRouteCrossConnections | 是 |
> | expressRouteGateways | 是 |
> | expressRoutePorts | 是 |
> | expressRouteServiceProviders | 否 |
> | firewallPolicies | 是 |
> | frontdoors | 是 |
> | frontdoorWebApplicationFirewallManagedRuleSets | 否 |
> | frontdoorWebApplicationFirewallPolicies | 是 |
> | getDnsResourceReference | 否 |
> | internalNotify | 否 |
> | loadBalancers | 是 |
> | localNetworkGateways | 是 |
> | natGateways | 是 |
> | networkIntentPolicies | 是 |
> | networkInterfaces | 是 |
> | networkProfiles | 是 |
> | networkSecurityGroups | 是 |
> | networkWatchers | 是 |
> | networkWatchers/connectionMonitors | 是 |
> | networkWatchers/lenses | 是 |
> | networkWatchers/pingMeshes | 是 |
> | p2sVpnGateways | 是 |
> | privateDnsOperationStatuses | 否 |
> | privateDnsZones | 是 |
> | privateDnsZones/A | 否 |
> | privateDnsZones/AAAA | 否 |
> | privateDnsZones/all | 否 |
> | privateDnsZones/CNAME | 否 |
> | privateDnsZones/MX | 否 |
> | privateDnsZones/PTR | 否 |
> | privateDnsZones/SOA | 否 |
> | privateDnsZones/SRV | 否 |
> | privateDnsZones/TXT | 否 |
> | privateDnsZones/virtualNetworkLinks | 是 |
> | privateEndpoints | 是 |
> | privateLinkServices | 是 |
> | publicIPAddresses | 是 |
> | publicIPPrefixes | 是 |
> | routeFilters | 是 |
> | routeTables | 是 |
> | secureGateways | 是 |
> | serviceEndpointPolicies | 是 |
> | trafficManagerGeographicHierarchies | 否 |
> | trafficmanagerprofiles | 是 |
> | trafficmanagerprofiles/heatMaps | 否 |
> | trafficManagerUserMetricsKeys | 否 |
> | virtualHubs | 是 |
> | virtualNetworkGateways | 是 |
> | virtualNetworks | 是 |
> | virtualNetworkTaps | 是 |
> | virtualWans | 是 |
> | vpnGateways | 是 |
> | vpnSites | 是 |
> | webApplicationFirewallPolicies | 是 |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | 命名空间 | 是 |
> | namespaces/notificationHubs | 是 |

## <a name="microsoftobjectstore"></a>ObjectStore

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | osNamespaces | 是 |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | HyperVSites | 是 |
> | ImportSites | 是 |
> | ServerSites | 是 |
> | VMwareSites | 是 |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | clusters | 是 |
> | 设备 | 否 |
> | linkTargets | 否 |
> | storageInsightConfigs | 否 |
> | 工作区 | 是 |
> | workspaces/dataSources | 否 |
> | workspaces/linkedServices | 否 |
> | workspaces/query | 否 |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | managementassociations | 否 |
> | managementconfigurations | 是 |
> | 解决方案 | 是 |
> | 视图 | 是 |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | legacyPeerings | 否 |
> | peerAsns | 否 |
> | 对等 | 是 |
> | peeringServiceProviders | 否 |
> | peeringServices | 是 |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | policyEvents | 否 |
> | policyStates | 否 |
> | policyTrackedResources | 否 |
> | remediations | 否 |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | consoles | 否 |
> | dashboards | 是 |
> | userSettings | 否 |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | workspaceCollections | 是 |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | capacities | 是 |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | backupProtectedItems | 否 |
> | vaults | 是 |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | 命名空间 | 是 |
> | namespaces/authorizationrules | 否 |
> | namespaces/hybridconnections | 否 |
> | namespaces/hybridconnections/authorizationrules | 否 |
> | namespaces/wcfrelays | 否 |
> | namespaces/wcfrelays/authorizationrules | 否 |

## <a name="microsoftremoteapp"></a>Microsoft RemoteApp

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | 帐户 | 否 |
> | 收藏 | 是 |
> | 集合/应用程序 | 否 |
> | 集合/securityprincipalsgetresponse | 否 |
> | templateImages | 否 |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | 查询 | 是 |
> | resourceChangeDetails | 否 |
> | resourceChanges | 否 |
> | 资源 | 否 |
> | resourcesHistory | 否 |
> | subscriptionsStatus | 否 |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | availabilityStatuses | 否 |
> | childAvailabilityStatuses | 否 |
> | childResources | 否 |
> | 事件 | 否 |
> | impactedResources | 否 |
> | metadata | 否 |
> | 通知 | 否 |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | 部署 | 否 |
> | deployments/operations | 否 |
> | 链接 | 否 |
> | notifyResourceJobs | 否 |
> | providers | 否 |
> | resourceGroups | 否 |
> | 资源 | 否 |
> | 订阅 | 否 |
> | subscriptions/providers | 否 |
> | subscriptions/resourceGroups | 否 |
> | subscriptions/resourcegroups/resources | 否 |
> | subscriptions/resources | 否 |
> | subscriptions/tagnames | 否 |
> | subscriptions/tagNames/tagValues | 否 |
> | tenants | 否 |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | 应用程序 | 是 |
> | saasresources | 否 |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | jobcollections | 是 |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | resourceHealthMetadata | 否 |
> | searchServices | 是 |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | adaptiveNetworkHardenings | 否 |
> | advancedThreatProtectionSettings | 否 |
> | 警报 | 否 |
> | allowedConnections | 否 |
> | applicationWhitelistings | 否 |
> | assessmentMetadata | 否 |
> | assessments | 否 |
> | AutoProvisioningSettings | 否 |
> | Compliances | 否 |
> | dataCollectionAgents | 否 |
> | deviceSecurityGroups | 否 |
> | discoveredSecuritySolutions | 否 |
> | externalSecuritySolutions | 否 |
> | InformationProtectionPolicies | 否 |
> | iotSecuritySolutions | 是 |
> | iotSecuritySolutions/analyticsModels | 否 |
> | iotSecuritySolutions/analyticsModels/aggregatedAlerts | 否 |
> | iotSecuritySolutions/analyticsModels/aggregatedRecommendations | 否 |
> | jitNetworkAccessPolicies | 否 |
> | networkData | 否 |
> | playbookConfigurations | 是 |
> | 策略 | 否 |
> | pricings | 否 |
> | regulatoryComplianceStandards | 否 |
> | regulatoryComplianceStandards/regulatoryComplianceControls | 否 |
> | regulatoryComplianceStandards/regulatoryComplianceControls/regulatoryComplianceAssessments | 否 |
> | securityContacts | 否 |
> | securitySolutions | 否 |
> | securitySolutionsReferenceData | 否 |
> | securityStatuses | 否 |
> | securityStatusesSummaries | 否 |
> | serverVulnerabilityAssessments | 否 |
> | 设置 | 否 |
> | subAssessments | 否 |
> | 任务 | 否 |
> | topologies | 否 |
> | workspaceSettings | 否 |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | diagnosticSettings | 否 |
> | diagnosticSettingsCategories | 否 |

## <a name="microsoftsecurityinsights"></a>SecurityInsights

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | 聚合 | 否 |
> | alertRules | 否 |
> | alertRuleTemplates | 否 |
> | 书签 | 否 |
> | cases | 否 |
> | dataConnectors | 否 |
> | 实体 | 否 |
> | entityQueries | 否 |
> | officeConsents | 否 |
> | 设置 | 否 |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | 命名空间 | 是 |
> | namespaces/authorizationrules | 否 |
> | namespaces/disasterrecoveryconfigs | 否 |
> | namespaces/eventgridfilters | 否 |
> | namespaces/networkrulesets | 否 |
> | namespaces/queues | 否 |
> | namespaces/queues/authorizationrules | 否 |
> | namespaces/topics | 否 |
> | namespaces/topics/authorizationrules | 否 |
> | namespaces/topics/subscriptions | 否 |
> | namespaces/topics/subscriptions/rules | 否 |
> | premiumMessagingRegions | 否 |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | 应用程序 | 是 |
> | clusters | 是 |
> | clusters/applications | 否 |
> | containerGroups | 是 |
> | containerGroupSets | 是 |
> | edgeclusters | 是 |
> | edgeclusters/applications | 否 |
> | 网络 | 是 |
> | secretstores | 是 |
> | secretstores/certificates | 否 |
> | secretstores/secrets | 否 |
> | 卷 | 是 |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | 应用程序 | 是 |
> | containerGroups | 是 |
> | gateways | 是 |
> | 网络 | 是 |
> | 机密 | 是 |
> | 卷 | 是 |

## <a name="microsoftservices"></a>Microsoft. 服务

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | providerRegistrations | 否 |
> | providerRegistrations/resourceTypeRegistrations | 否 |
> | rollouts | 是 |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | SignalR | 是 |
> | SignalR/eventGridFilters | 否 |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | SiteRecoveryVault | 是 |

## <a name="microsoftsoftwareplan"></a>SoftwarePlan

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | hybridUseBenefits | 否 |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | applicationDefinitions | 是 |
> | 应用程序 | 是 |
> | jitRequests | 是 |

## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | managedInstances | 是 |
> | managedInstances/databases | 是 |
> | managedInstances/databases/backupShortTermRetentionPolicies | 否 |
> | managedInstances/databases/schemas/tables/columns/sensitivityLabels | 否 |
> | managedInstances/databases/vulnerabilityAssessments | 否 |
> | managedInstances/databases/vulnerabilityAssessments/rules/baselines | 否 |
> | managedInstances/encryptionProtector | 否 |
> | managedInstances/keys | 否 |
> | managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies | 否 |
> | managedInstances/vulnerabilityAssessments | 否 |
> | 服务器 | 是 |
> | servers/administrators | 否 |
> | servers/communicationLinks | 否 |
> | servers/databases | 是 |
> | servers/encryptionProtector | 否 |
> | servers/firewallRules | 否 |
> | servers/keys | 否 |
> | servers/restorableDroppedDatabases | 否 |
> | servers/serviceobjectives | 否 |
> | servers/tdeCertificates | 否 |
> | virtualClusters | 否 |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | SqlVirtualMachineGroups | 是 |
> | SqlVirtualMachineGroups/AvailabilityGroupListeners | 否 |
> | SqlVirtualMachines | 是 |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | storageAccounts | 是 |
> | storageAccounts/blobServices | 否 |
> | storageAccounts/fileServices | 否 |
> | storageAccounts/queueServices | 否 |
> | storageAccounts/services | 否 |
> | storageAccounts/services/metricDefinitions | 否 |
> | storageAccounts/tableServices | 否 |
> | usages | 否 |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | 缓存 | 是 |
> | 缓存/storageTargets | 否 |
> | usageModels | 否 |

## <a name="microsoftstoragereplication"></a>StorageReplication

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | replicationGroups | 否 |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | storageSyncServices | 是 |
> | storageSyncServices/registeredServers | 否 |
> | storageSyncServices/syncGroups | 否 |
> | storageSyncServices/syncGroups/cloudEndpoints | 否 |
> | storageSyncServices/syncGroups/serverEndpoints | 否 |
> | storageSyncServices/workflows | 否 |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | storageSyncServices | 是 |
> | storageSyncServices/registeredServers | 否 |
> | storageSyncServices/syncGroups | 否 |
> | storageSyncServices/syncGroups/cloudEndpoints | 否 |
> | storageSyncServices/syncGroups/serverEndpoints | 否 |
> | storageSyncServices/workflows | 否 |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | storageSyncServices | 是 |
> | storageSyncServices/registeredServers | 否 |
> | storageSyncServices/syncGroups | 否 |
> | storageSyncServices/syncGroups/cloudEndpoints | 否 |
> | storageSyncServices/syncGroups/serverEndpoints | 否 |
> | storageSyncServices/workflows | 否 |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | managers | 是 |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | streamingjobs | 是 |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | 取消 | 否 |
> | CreateSubscription | 否 |
> | 启用 | 否 |
> | 重命名 | 否 |
> | SubscriptionDefinitions | 否 |
> | SubscriptionOperations | 否 |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | environments | 是 |
> | environments/accessPolicies | 否 |
> | environments/eventsources | 是 |
> | environments/referenceDataSets | 是 |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | dedicatedCloudNodes | 是 |
> | dedicatedCloudServices | 是 |
> | virtualMachines | 是 |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | apiManagementAccounts | 否 |
> | apiManagementAccounts/apiAcls | 否 |
> | apiManagementAccounts/apis | 否 |
> | apiManagementAccounts/apis/apiAcls | 否 |
> | apiManagementAccounts/apis/connectionAcls | 否 |
> | apiManagementAccounts/apis/connections | 否 |
> | apiManagementAccounts/apis/connections/connectionAcls | 否 |
> | apiManagementAccounts/apis/localizedDefinitions | 否 |
> | apiManagementAccounts/connectionAcls | 否 |
> | apiManagementAccounts/connections | 否 |
> | billingMeters | 否 |
> | 证书 | 是 |
> | connectionGateways | 是 |
> | 连接 | 是 |
> | customApis | 是 |
> | deletedSites | 否 |
> | functions | 否 |
> | hostingEnvironments | 是 |
> | hostingEnvironments/multiRolePools | 否 |
> | hostingEnvironments/workerPools | 否 |
> | publishingUsers | 否 |
> | 推荐 | 否 |
> | resourceHealthMetadata | 否 |
> | runtimes | 否 |
> | serverFarms | 是 |
> | serverFarms/eventGridFilters | 否 |
> | 站点 | 是 |
> | sites/config  | 否 |
> | sites/eventGridFilters | 否 |
> | sites/hostNameBindings | 否 |
> | sites/networkConfig | 否 |
> | sites/premieraddons | 是 |
> | sites/slots | 是 |
> | sites/slots/eventGridFilters | 否 |
> | sites/slots/hostNameBindings | 否 |
> | sites/slots/networkConfig | 否 |
> | sourceControls | 否 |
> | validate | 否 |
> | verifyHostingEnvironmentVnet | 否 |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | diagnosticSettings | 否 |
> | diagnosticSettingsCategories | 否 |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | DeviceServices | 是 |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | components | 否 |
> | componentsSummary | 否 |
> | monitorInstances | 否 |
> | monitorInstancesSummary | 否 |
> | monitors | 否 |
> | notificationSettings | 否 |

## <a name="next-steps"></a>后续步骤

若要以逗号分隔值文件的形式获取同一数据，请下载 [complete-mode-deletion.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/complete-mode-deletion.csv)。
