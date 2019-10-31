---
title: 针对资源的 Azure 资源管理器标记支持
description: 显示支持标记的 Azure资源类型。 提供所有 Azure 服务的详细信息。
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 10/27/2019
ms.author: tomfitz
ms.openlocfilehash: 6b64eb955476a8ab5307b7d508484c290fd7660a
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2019
ms.locfileid: "73162171"
---
# <a name="tag-support-for-azure-resources"></a>Azure 资源的标记支持
本文介绍某一资源类型是否支持[标记](resource-group-using-tags.md)。 标记为**支持标记**的列指示资源类型是否具有标记的属性。 标记为 **"成本报表中的标记**" 的列指示该资源类型是否将标记传递给成本报告。

若要以逗号分隔值文件的形式获取同一数据，请下载 [tag-support.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/tag-support.csv)。

跳转到资源提供程序命名空间：
> [!div class="op_single_selector"]
> - [Microsoft AAD](#microsoftaad)
> - [加载项](#microsoftaddons)
> - [ADHybridHealthService](#microsoftadhybridhealthservice)
> - [Microsoft Advisor](#microsoftadvisor)
> - [AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.analysisservices.sharepoint.integration.dll](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [AppConfiguration](#microsoftappconfiguration)
> - [AppPlatform](#microsoftappplatform)
> - [Microsoft. 证明](#microsoftattestation)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [Azconfig](#microsoftazconfig)
> - [Geneva](#microsoftazuregeneva)
> - [AzureActiveDirectory](#microsoftazureactivedirectory)
> - [AzureData](#microsoftazuredata)
> - [Test-azurestack](#microsoftazurestack)
> - [Microsoft. 批处理](#microsoftbatch)
> - [微软帐单](#microsoftbilling)
> - [BingMaps](#microsoftbingmaps)
> - [区块链](#microsoftblockchain)
> - [Microsoft 蓝图](#microsoftblueprint)
> - [BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Microsoft. 容量](#microsoftcapacity)
> - [Microsoft.Cdn](#microsoftcdn)
> - [CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.classiccompute](#microsoftclassiccompute)
> - [ClassicInfrastructureMigrate](#microsoftclassicinfrastructuremigrate)
> - [Microsoft.classicnetwork](#microsoftclassicnetwork)
> - [ClassicStorage](#microsoftclassicstorage)
> - [Cognitiveservices account](#microsoftcognitiveservices)
> - [Microsoft 商业](#microsoftcommerce)
> - [Microsoft. 计算](#microsoftcompute)
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
> - [Microsoft.datamigration](#microsoftdatamigration)
> - [DataShare](#microsoftdatashare)
> - [DBforMariaDB](#microsoftdbformariadb)
> - [DBforMySQL](#microsoftdbformysql)
> - [DBforPostgreSQL](#microsoftdbforpostgresql)
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
> - [EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft. 功能](#microsoftfeatures)
> - [Microsoft. 库](#microsoftgallery)
> - [基因组学](#microsoftgenomics)
> - [GuestConfiguration](#microsoftguestconfiguration)
> - [HanaOnAzure](#microsofthanaonazure)
> - [Microsoft.hardwaresecuritymodules](#microsofthardwaresecuritymodules)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [HealthcareApis](#microsofthealthcareapis)
> - [HybridCompute](#microsofthybridcompute)
> - [HybridData](#microsofthybriddata)
> - [Hydra](#microsofthydra)
> - [ImportExport](#microsoftimportexport)
> - [Microsoft Intune](#microsoftintune)
> - [IoTCentral](#microsoftiotcentral)
> - [IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Kusto](#microsoftkusto)
> - [LabServices](#microsoftlabservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [Default-machinelearning-southcentralus](#microsoftmachinelearning)
> - [MachineLearningServices](#microsoftmachinelearningservices)
> - [对 microsoft.managedidentity](#microsoftmanagedidentity)
> - [ManagedServices](#microsoftmanagedservices)
> - [Microsoft. 管理](#microsoftmanagement)
> - [Microsoft. Maps](#microsoftmaps)
> - [Microsoft Marketplace](#microsoftmarketplace)
> - [MarketplaceApps](#microsoftmarketplaceapps)
> - [MarketplaceOrdering](#microsoftmarketplaceordering)
> - [Microsoft.Media](#microsoftmedia)
> - [Microservices4Spring](#microsoftmicroservices4spring)
> - [Microsoft. 迁移](#microsoftmigrate)
> - [MixedReality](#microsoftmixedreality)
> - [Microsoft NetApp](#microsoftnetapp)
> - [Microsoft 网络](#microsoftnetwork)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [ObjectStore](#microsoftobjectstore)
> - [OffAzure](#microsoftoffazure)
> - [Microsoft.operationalinsights](#microsoftoperationalinsights)
> - [为 microsoft.operationsmanagement](#microsoftoperationsmanagement)
> - [Microsoft。对等互连](#microsoftpeering)
> - [Microsoft.PolicyInsights](#microsoftpolicyinsights)
> - [Microsoft 门户](#microsoftportal)
> - [Microsoft PowerBI](#microsoftpowerbi)
> - [PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft 中继](#microsoftrelay)
> - [Microsoft RemoteApp](#microsoftremoteapp)
> - [ResourceGraph](#microsoftresourcegraph)
> - [ResourceHealth](#microsoftresourcehealth)
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
> - [Azurerm.recoveryservices.siterecovery](#microsoftsiterecovery)
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
> - [Streamanalytics-default-central-us](#microsoftstreamanalytics)
> - [Microsoft. 订阅](#microsoftsubscription)
> - [Timeseriesinsights-environment-with-eventhub](#microsofttimeseriesinsights)
> - [VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft.Web](#microsoftweb)
> - [WindowsDefenderATP](#microsoftwindowsdefenderatp)
> - [WindowsIoT](#microsoftwindowsiot)
> - [WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | DomainServices | 是 | 是 |
> | DomainServices/oucontainer | No | No |
> | DomainServices/ReplicaSets | 是 | 是 |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | supportProviders | No | No |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | aadsupportcases | No | No |
> | addsservices | No | No |
> | 代理 | No | No |
> | anonymousapiusers | No | No |
> | 配置 | No | No |
> | 日志 | No | No |
> | 报表 | No | No |
> | servicehealthmetrics | No | No |
> | services | No | No |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | 配置 | No | No |
> | generateRecommendations | No | No |
> | metadata | No | No |
> | 建议 | No | No |
> | 禁止显示 | No | No |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | actionRules | 是 | 是 |
> | alerts | No | No |
> | alertsList | No | No |
> | alertsMetaData | No | No |
> | alertsSummary | No | No |
> | alertsSummaryList | No | No |
> | 意见 | No | No |
> | smartDetectorAlertRules | 是 | 是 |
> | smartDetectorRuntimeEnvironments | No | No |
> | smartGroups | No | No |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | 服务器 | 是 | 是 |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | reportFeedback | No | No |
> | 服务 | 是 | 是 |
> | validateServiceName | No | No |

## <a name="microsoftappconfiguration"></a>AppConfiguration

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | configurationStores | 是 | 是 |
> | configurationStores/eventGridFilters | No | No |

## <a name="microsoftappplatform"></a>AppPlatform

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | Spring | 是 | 是 |

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | attestationProviders | No | No |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | classicAdministrators | No | No |
> | dataAliases | No | No |
> | denyAssignments | No | No |
> | elevateAccess | No | No |
> | findOrphanRoleAssignments | No | No |
> | 锁定 | No | No |
> | 权限 | No | No |
> | policyAssignments | No | No |
> | policyDefinitions | No | No |
> | policySetDefinitions | No | No |
> | providerOperations | No | No |
> | roleAssignments | No | No |
> | roleDefinitions | No | No |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | automationAccounts | 是 | 是 |
> | automationAccounts/configurations | 是 | 是 |
> | automationAccounts/jobs | No | No |
> | automationAccounts/runbooks | 是 | 是 |
> | automationAccounts/softwareUpdateConfigurations | No | No |
> | automationAccounts/webhooks | No | No |

## <a name="microsoftazconfig"></a>Azconfig

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | configurationStores | 是 | 是 |
> | configurationStores/eventGridFilters | No | No |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | environments | No | No |
> | environments/accounts | No | No |
> | environments/accounts/namespaces | No | No |
> | environments/accounts/namespaces/configurations | No | No |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | b2cDirectories | 是 | No |
> | b2ctenants | No | No |

## <a name="microsoftazuredata"></a>AzureData

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | hybridDataManagers | 是 | 是 |
> | postgresInstances | 是 | 是 |
> | sqlBigDataClusters | 是 | 是 |
> | sqlInstances | 是 | 是 |
> | sqlServerRegistrations | 是 | 是 |
> | sqlServerRegistrations/sqlServers | No | No |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | registrations | 是 | 是 |
> | registrations/customerSubscriptions | No | No |
> | registrations/products | No | No |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | batchAccounts | 是 | 是 |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | billingAccounts | No | No |
> | billingAccounts/协议 | No | No |
> | billingAccounts/billingPermissions | No | No |
> | billingAccounts/billingProfiles | No | No |
> | billingAccounts/billingProfiles/billingPermissions | No | No |
> | billingAccounts/billingProfiles/billingRoleAssignments | No | No |
> | billingAccounts/billingProfiles/billingRoleDefinitions | No | No |
> | billingAccounts/billingProfiles/billingSubscriptions | No | No |
> | billingAccounts/billingProfiles/createBillingRoleAssignment | No | No |
> | billingAccounts/billingProfiles/customers | No | No |
> | billingAccounts/billingProfiles/invoices | No | No |
> | billingAccounts/billingProfiles/invoices/pricesheet | No | No |
> | billingAccounts/billingProfiles/invoiceSections | No | No |
> | billingAccounts/billingProfiles/invoiceSections/billingPermissions | No | No |
> | billingAccounts/billingProfiles/invoiceSections/billingRoleAssignments | No | No |
> | billingAccounts/billingProfiles/invoiceSections/billingRoleDefinitions | No | No |
> | billingAccounts/billingProfiles/invoiceSections/billingSubscriptions | No | No |
> | billingAccounts/billingProfiles/invoiceSections/createBillingRoleAssignment | No | No |
> | billingAccounts/billingProfiles/invoiceSections/initiateTransfer | No | No |
> | billingAccounts/billingProfiles/invoiceSections/products | No | No |
> | billingAccounts/billingProfiles/invoiceSections/products/transfer | No | No |
> | billingAccounts/billingProfiles/invoiceSections/products/updateAutoRenew | No | No |
> | billingAccounts/billingProfiles/invoiceSections/事务 | No | No |
> | billingAccounts/billingProfiles/invoiceSections/传输 | No | No |
> | billingAccounts/BillingProfiles/patchOperations | No | No |
> | billingAccounts/billingProfiles/paymentMethods | No | No |
> | billingAccounts/billingProfiles/policies | No | No |
> | billingAccounts/billingProfiles/pricesheet | No | No |
> | billingAccounts/billingProfiles/pricesheetDownloadOperations | No | No |
> | billingAccounts/billingProfiles/products | No | No |
> | billingAccounts/billingProfiles/transactions | No | No |
> | billingAccounts/billingRoleAssignments | No | No |
> | billingAccounts/billingRoleDefinitions | No | No |
> | billingAccounts/billingSubscriptions | No | No |
> | billingAccounts/createBillingRoleAssignment | No | No |
> | billingAccounts/createInvoiceSectionOperations | No | No |
> | billingAccounts/客户 | No | No |
> | billingAccounts/customers/billingPermissions | No | No |
> | billingAccounts/customers/billingSubscriptions | No | No |
> | billingAccounts/customers/initiateTransfer | No | No |
> | billingAccounts/客户/策略 | No | No |
> | billingAccounts/客户/产品 | No | No |
> | billingAccounts/客户/事务 | No | No |
> | billingAccounts/客户/转移 | No | No |
> | billingAccounts/departments | No | No |
> | billingAccounts/enrollmentAccounts | No | No |
> | billingAccounts/invoices | No | No |
> | billingAccounts/invoiceSections | No | No |
> | billingAccounts/invoiceSections/billingSubscriptionMoveOperations | No | No |
> | billingAccounts/invoiceSections/billingSubscriptions | No | No |
> | billingAccounts/invoiceSections/billingSubscriptions/transfer | No | No |
> | billingAccounts/invoiceSections/提升 | No | No |
> | billingAccounts/invoiceSections/initiateTransfer | No | No |
> | billingAccounts/invoiceSections/patchOperations | No | No |
> | billingAccounts/invoiceSections/productMoveOperations | No | No |
> | billingAccounts/invoiceSections/products | No | No |
> | billingAccounts/invoiceSections/products/transfer | No | No |
> | billingAccounts/invoiceSections/products/updateAutoRenew | No | No |
> | billingAccounts/invoiceSections/事务 | No | No |
> | billingAccounts/invoiceSections/transfers | No | No |
> | billingAccounts/lineOfCredit | No | No |
> | billingAccounts/patchOperations | No | No |
> | billingAccounts/paymentMethods | No | No |
> | billingAccounts/products | No | No |
> | billingAccounts/transactions | No | No |
> | billingPeriods | No | No |
> | billingPermissions | No | No |
> | billingProperty | No | No |
> | billingRoleAssignments | No | No |
> | billingRoleDefinitions | No | No |
> | createBillingRoleAssignment | No | No |
> | departments | No | No |
> | enrollmentAccounts | No | No |
> | invoices | No | No |
> | transfers | No | No |
> | transfers/acceptTransfer | No | No |
> | transfers/declineTransfer | No | No |
> | transfers/operationStatus | No | No |
> | 传输/validateTransfer | No | No |
> | validateAddress | No | No |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | mapApis | 是 | 是 |
> | updateCommunicationPreference | No | No |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | blockchainMembers | 是 | 是 |
> | cordaMembers | 是 | 是 |
> | 观看者 | 是 | 是 |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | blueprintAssignments | No | No |
> | blueprintAssignments/assignmentOperations | No | No |
> | blueprintAssignments/operations | No | No |
> | blueprints | No | No |
> | blueprints/artifacts | No | No |
> | blueprints/versions | No | No |
> | blueprints/versions/artifacts | No | No |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | botServices | 是 | 是 |
> | botServices/channels | No | No |
> | botServices/connections | No | No |
> | 语言 | No | No |
> | 模板 | No | No |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | Redis | 是 | 是 |
> | RedisConfigDefinition | No | No |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | appliedReservations | No | No |
> | calculateExchange | No | No |
> | calculatePrice | No | No |
> | calculatePurchasePrice | No | No |
> | catalogs | No | No |
> | commercialReservationOrders | No | No |
> | exchange | No | No |
> | placePurchaseOrder | No | No |
> | reservationOrders | No | No |
> | reservationOrders/calculateRefund | No | No |
> | reservationOrders/merge | No | No |
> | reservationOrders/reservations | No | No |
> | reservationOrders/reservations/revisions | No | No |
> | reservationOrders/return | No | No |
> | reservationOrders/split | No | No |
> | reservationOrders/swap | No | No |
> | reservations | No | No |
> | 资源 | No | No |
> | validateReservationOrder | No | No |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | CdnWebApplicationFirewallManagedRuleSets | No | No |
> | CdnWebApplicationFirewallPolicies | 是 | 是 |
> | edgenodes | No | No |
> | 配置文件 | 是 | 是 |
> | profiles/endpoints | 是 | 是 |
> | profiles/endpoints/customdomains | No | No |
> | profiles/endpoints/origins | No | No |
> | validateProbe | No | No |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | certificateOrders | 是 | 是 |
> | certificateOrders/certificates | No | No |
> | validateCertificateRegistrationInformation | No | No |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | capabilities | No | No |
> | domainNames | No | No |
> | domainNames/capabilities | No | No |
> | domainNames/internalLoadBalancers | No | No |
> | domainNames/serviceCertificates | No | No |
> | domainNames/slots | No | No |
> | domainNames/slots/roles | No | No |
> | domainNames/槽位/role/metricDefinitions | No | No |
> | domainNames/插槽/角色/指标 | No | No |
> | moveSubscriptionResources | No | No |
> | operatingSystemFamilies | No | No |
> | operatingSystems | No | No |
> | quotas | No | No |
> | resourceTypes | No | No |
> | validateSubscriptionMoveAvailability | No | No |
> | virtualMachines | No | No |
> | virtualMachines/diagnosticSettings | No | No |
> | virtualMachines/metricDefinitions | No | No |
> | virtualMachines/metrics | No | No |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | classicInfrastructureResources | No | No |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | capabilities | No | No |
> | expressRouteCrossConnections | No | No |
> | expressRouteCrossConnections/peerings | No | No |
> | gatewaySupportedDevices | No | No |
> | networkSecurityGroups | No | No |
> | quotas | No | No |
> | reservedIps | No | No |
> | virtualNetworks | No | No |
> | virtualNetworks/remoteVirtualNetworkPeeringProxies | No | No |
> | virtualNetworks/virtualNetworkPeerings | No | No |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | capabilities | No | No |
> | 磁盘 | No | No |
> | images | No | No |
> | osImages | No | No |
> | osPlatformImages | No | No |
> | publicImages | No | No |
> | quotas | No | No |
> | storageAccounts | No | No |
> | storageAccounts/blobServices | No | No |
> | storageAccounts/fileServices | No | No |
> | storageAccounts/metricDefinitions | No | No |
> | storageAccounts/指标 | No | No |
> | storageAccounts/queueServices | No | No |
> | storageAccounts/services | No | No |
> | storageAccounts/services/diagnosticSettings | No | No |
> | storageAccounts/services/metricDefinitions | No | No |
> | storageAccounts/服务/指标 | No | No |
> | storageAccounts/tableServices | No | No |
> | storageAccounts/vmImages | No | No |
> | vmImages | No | No |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | accounts | 是 | 是 |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | RateCard | No | No |
> | UsageAggregates | No | No |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | availabilitySets | 是 | 是 |
> | diskEncryptionSets | 是 | 是 |
> | 磁盘 | 是 | 是 |
> | galleries | 是 | 是 |
> | 库/应用程序 | No | No |
> | 库/应用程序/版本 | No | No |
> | galleries/images | No | No |
> | galleries/images/versions | No | No |
> | hostGroups | 是 | 是 |
> | hostGroups/主机 | 是 | 是 |
> | images | 是 | 是 |
> | proximityPlacementGroups | 是 | 是 |
> | restorePointCollections | 是 | 是 |
> | restorePointCollections/restorePoints | No | No |
> | sharedVMExtensions | 是 | 是 |
> | sharedVMExtensions/版本 | No | No |
> | sharedVMImages | 是 | 是 |
> | sharedVMImages/versions | No | No |
> | snapshots | 是 | 是 |
> | virtualMachines | 是 | 是 |
> | virtualMachines/extensions | 是 | 是 |
> | virtualMachines/metricDefinitions | No | No |
> | virtualMachineScaleSets | 是 | 是 |
> | virtualMachineScaleSets/extensions | No | No |
> | virtualMachineScaleSets/networkInterfaces | No | No |
> | virtualMachineScaleSets/publicIPAddresses | No | No |
> | virtualMachineScaleSets/virtualMachines | No | No |
> | virtualMachineScaleSets/virtualMachines/networkInterfaces | No | No |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | AggregatedCost | No | No |
> | 余额 | No | No |
> | 预算 | No | No |
> | Charges | No | No |
> | CostTags | No | No |
> | credits | No | No |
> | 活动 | No | No |
> | 预测 | No | No |
> | lots | No | No |
> | 市场 | No | No |
> | Pricesheets | No | No |
> | products | No | No |
> | ReservationDetails | No | No |
> | ReservationRecommendations | No | No |
> | ReservationSummaries | No | No |
> | ReservationTransactions | No | No |
> | Tags | No | No |
> | tenants | No | No |
> | 术语 | No | No |
> | UsageDetails | No | No |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | containerGroups | 是 | 是 |
> | serviceAssociationLinks | No | No |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | registries | 是 | 是 |
> | registries/builds | No | No |
> | registries/builds/cancel | No | No |
> | registries/builds/getLogLink | No | No |
> | registries/buildTasks | 是 | 是 |
> | registries/buildTasks/steps | No | No |
> | registries/eventGridFilters | No | No |
> | 注册表/generateCredentials | No | No |
> | registries/getBuildSourceUploadUrl | No | No |
> | registries/GetCredentials | No | No |
> | registries/importImage | No | No |
> | registries/queueBuild | No | No |
> | registries/regenerateCredential | No | No |
> | registries/regenerateCredentials | No | No |
> | registries/replications | 是 | 是 |
> | registries/runs | No | No |
> | registries/runs/cancel | No | No |
> | registries/scheduleRun | No | No |
> | 注册表/scopeMaps | No | No |
> | registries/tasks | 是 | 是 |
> | 注册表/标记 | No | No |
> | registries/updatePolicies | No | No |
> | registries/webhooks | 是 | 是 |
> | registries/webhooks/getCallbackConfig | No | No |
> | registries/webhooks/ping | No | No |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | containerServices | 是 | 是 |
> | managedClusters | 是 | 是 |
> | openShiftManagedClusters | 是 | 是 |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | accounts | 是 | 是 |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | 警报 | No | No |
> | BillingAccounts | No | No |
> | 预算 | No | No |
> | CloudConnectors | No | No |
> | 连接器 | 是 | 是 |
> | Departments | No | No |
> | 维度 | No | No |
> | EnrollmentAccounts | No | No |
> | 导出 | No | No |
> | ExternalBillingAccounts | No | No |
> | ExternalBillingAccounts/警报 | No | No |
> | ExternalBillingAccounts/维度 | No | No |
> | ExternalBillingAccounts/预测 | No | No |
> | ExternalBillingAccounts/查询 | No | No |
> | ExternalSubscriptions | No | No |
> | ExternalSubscriptions/警报 | No | No |
> | ExternalSubscriptions/维度 | No | No |
> | ExternalSubscriptions/预测 | No | No |
> | ExternalSubscriptions/查询 | No | No |
> | 预测 | No | No |
> | Query | No | No |
> | register | No | No |
> | Reportconfigs | No | No |
> | 报表 | No | No |
> | 设置 | No | No |
> | showbackRules | No | No |
> | 视图 | No | No |

## <a name="microsoftcustomerlockbox"></a>CustomerLockbox

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | 请求 | No | No |

## <a name="microsoftcustomproviders"></a>CustomProviders

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | 相关性 | No | No |
> | resourceProviders | 是 | 是 |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | jobs | 是 | 是 |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | DataBoxEdgeDevices | 是 | 是 |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | workspaces | 是 | No |
> | workspaces/virtualNetworkPeerings | No | No |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | catalogs | 是 | 是 |
> | datacatalogs | 是 | 是 |
> | datacatalogs/数据源 | No | No |
> | datacatalogs/数据源/扫描 | No | No |
> | datacatalogs/数据源/扫描/数据集 | No | No |
> | datacatalogs/数据源/扫描/触发器 | No | No |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | dataFactories | 是 | No |
> | dataFactories/diagnosticSettings | No | No |
> | dataFactories/metricDefinitions | No | No |
> | dataFactorySchema | No | No |
> | factories | 是 | No |
> | factories/integrationRuntimes | No | No |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | accounts | 是 | 是 |
> | accounts/dataLakeStoreAccounts | No | No |
> | accounts/storageAccounts | No | No |
> | accounts/storageAccounts/containers | No | No |
> | 帐户/transferAnalyticsUnits | No | No |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | accounts | 是 | 是 |
> | accounts/eventGridFilters | No | No |
> | accounts/firewallRules | No | No |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | services | No | No |
> | services/projects | No | No |

## <a name="microsoftdatashare"></a>DataShare

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | accounts | 是 | 是 |
> | 帐户/共享 | No | No |
> | 帐户/共享/数据集 | No | No |
> | 帐户/共享/邀请 | No | No |
> | 帐户/共享/providersharesubscriptions | No | No |
> | 帐户/共享/synchronizationSettings | No | No |
> | 帐户/sharesubscriptions | No | No |
> | accounts/sharesubscriptions/consumerSourceDataSets | No | No |
> | accounts/sharesubscriptions/datasetmappings | No | No |
> | 帐户/sharesubscriptions/触发器 | No | No |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | 服务器 | 是 | 是 |
> | servers/advisors | No | No |
> | 服务器/privateEndpointConnectionProxies | No | No |
> | 服务器/privateEndpointConnections | No | No |
> | 服务器/privateLinkResources | No | No |
> | servers/queryTexts | No | No |
> | servers/recoverableServers | No | No |
> | servers/topQueryStatistics | No | No |
> | servers/virtualNetworkRules | No | No |
> | servers/waitStatistics | No | No |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | 服务器 | 是 | 是 |
> | servers/advisors | No | No |
> | 服务器/privateEndpointConnectionProxies | No | No |
> | 服务器/privateEndpointConnections | No | No |
> | 服务器/privateLinkResources | No | No |
> | servers/queryTexts | No | No |
> | servers/recoverableServers | No | No |
> | servers/topQueryStatistics | No | No |
> | servers/virtualNetworkRules | No | No |
> | servers/waitStatistics | No | No |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | serverGroups | 是 | 是 |
> | 服务器 | 是 | 是 |
> | servers/advisors | No | No |
> | servers/keys | No | No |
> | 服务器/privateEndpointConnectionProxies | No | No |
> | 服务器/privateEndpointConnections | No | No |
> | 服务器/privateLinkResources | No | No |
> | servers/queryTexts | No | No |
> | servers/recoverableServers | No | No |
> | servers/topQueryStatistics | No | No |
> | servers/virtualNetworkRules | No | No |
> | servers/waitStatistics | No | No |
> | serversv2 | 是 | 是 |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | artifactSources | 是 | 是 |
> | rollouts | 是 | 是 |
> | serviceTopologies | 是 | 是 |
> | serviceTopologies/服务 | 是 | 是 |
> | serviceTopologies/services/serviceUnits | 是 | 是 |
> | steps | 是 | 是 |

## <a name="microsoftdesktopvirtualization"></a>DesktopVirtualization

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | applicationgroups | 是 | 是 |
> | applicationgroups/应用程序 | No | No |
> | applicationgroups/台式机 | No | No |
> | applicationgroups/startmenuitems | No | No |
> | hostpools | 是 | 是 |
> | hostpools/sessionhosts | No | No |
> | hostpools/sessionhosts/usersessions | No | No |
> | hostpools/usersessions | No | No |
> | workspaces | 是 | 是 |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | ElasticPools | 是 | 是 |
> | ElasticPools/IotHubTenants | 是 | 是 |
> | IotHubs | 是 | 是 |
> | IotHubs/eventGridFilters | No | No |
> | ProvisioningServices | 是 | 是 |
> | usages | No | No |

## <a name="microsoftdevops"></a>DevOps

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | 段 | 是 | 是 |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | 控制器 | 是 | 是 |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | labcenters | 是 | 是 |
> | labs | 是 | 是 |
> | 实验室/环境 | 是 | 是 |
> | labs/serviceRunners | 是 | 是 |
> | labs/virtualMachines | 是 | 是 |
> | schedules | 是 | 是 |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | databaseAccountNames | No | No |
> | databaseAccounts | 是 | 是 |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | domains | 是 | 是 |
> | domains/domainOwnershipIdentifiers | No | No |
> | generateSsoRequest | No | No |
> | topLevelDomains | No | No |
> | validateDomainRegistrationInformation | No | No |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | lcsprojects | No | No |
> | lcsprojects/clouddeployments | No | No |
> | lcsprojects/connectors | No | No |

## <a name="microsoftenterpriseknowledgegraph"></a>EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | services | 是 | 是 |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | domains | 是 | 是 |
> | 域/主题 | No | No |
> | eventSubscriptions | No | No |
> | extensionTopics | No | No |
> | topics | 是 | 是 |
> | topicTypes | No | No |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | clusters | 是 | 是 |
> | namespaces | 是 | 是 |
> | namespaces/authorizationrules | No | No |
> | namespaces/disasterrecoveryconfigs | No | No |
> | namespaces/eventhubs | No | No |
> | namespaces/eventhubs/authorizationrules | No | No |
> | namespaces/eventhubs/consumergroups | No | No |
> | 命名空间/networkrulesets | No | No |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | 功能 | No | No |
> | providers | No | No |

## <a name="microsoftgallery"></a>Microsoft.Gallery

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | enroll | No | No |
> | galleryitems | No | No |
> | generateartifactaccessuri | No | No |
> | myareas | No | No |
> | myareas/areas | No | No |
> | myareas/areas/areas | No | No |
> | myareas/areas/areas/galleryitems | No | No |
> | myareas/areas/galleryitems | No | No |
> | myareas/galleryitems | No | No |
> | register | No | No |
> | 资源 | No | No |
> | retrieveresourcesbyid | No | No |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | accounts | 是 | 是 |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | autoManagedVmConfigurationProfiles | 是 | 是 |
> | configurationProfileAssignments | No | No |
> | guestConfigurationAssignments | No | No |
> | software | No | No |
> | softwareUpdateProfile | No | No |
> | softwareUpdates | No | No |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | hanainstances | 是 | 是 |
> | sapMonitors | 是 | 是 |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | dedicatedHSMs | 是 | 是 |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | clusters | 是 | 是 |
> | clusters/applications | No | No |

## <a name="microsofthealthcareapis"></a>HealthcareApis

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | services | 是 | 是 |

## <a name="microsofthybridcompute"></a>HybridCompute

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | 造 | 是 | 是 |
> | 计算机/扩展 | 是 | 是 |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | dataManagers | 是 | 是 |

## <a name="microsofthydra"></a>Hydra

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | components | 是 | 是 |
> | networkScopes | 是 | 是 |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | jobs | 是 | 是 |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | No | No |
> | diagnosticSettingsCategories | No | No |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | appTemplates | No | No |
> | IoTApps | 是 | 是 |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | 图形 | 是 | 是 |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | deletedVaults | No | No |
> | hsmPools | 是 | 是 |
> | vaults | 是 | 是 |
> | vaults/accessPolicies | No | No |
> | 保管库/eventGridFilters | No | No |
> | vaults/secrets | No | No |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | clusters | 是 | 是 |
> | 群集/attacheddatabaseconfigurations | No | No |
> | clusters/databases | No | No |
> | clusters/databases/dataconnections | No | No |
> | clusters/databases/eventhubconnections | No | No |
> | 群集/sharedidentities | No | No |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | labaccounts | 是 | 是 |
> | users | No | No |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | hostingEnvironments | 是 | 是 |
> | integrationAccounts | 是 | 是 |
> | integrationServiceEnvironments | 是 | 是 |
> | integrationServiceEnvironments/managedApis | 是 | 是 |
> | isolatedEnvironments | 是 | 是 |
> | workflows | 是 | 是 |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | commitmentPlans | 是 | 是 |
> | webServices | 是 | 是 |
> | 工作区 | 是 | 是 |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | workspaces | 是 | 是 |
> | workspaces/computes | No | No |
> | 工作区/eventGridFilters | No | No |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | 标识 | No | No |
> | userAssignedIdentities | 是 | 是 |

## <a name="microsoftmanagedservices"></a>ManagedServices

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | marketplaceRegistrationDefinitions | No | No |
> | registrationAssignments | No | No |
> | registrationDefinitions | No | No |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | getEntities | No | No |
> | managementGroups | No | No |
> | 资源 | No | No |
> | startTenantBackfill | No | No |
> | tenantBackfillStatus | No | No |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | accounts | 是 | 是 |
> | accounts/eventGridFilters | No | No |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | offers | No | No |
> | offerTypes | No | No |
> | offerTypes/publishers | No | No |
> | offerTypes/publishers/offers | No | No |
> | offerTypes/publishers/offers/plans | No | No |
> | offerTypes/publishers/offers/plans/agreements | No | No |
> | offerTypes/publishers/offers/plans/configs | No | No |
> | offerTypes/publishers/offers/plans/configs/importImage | No | No |
> | privategalleryitems | No | No |
> | products | No | No |
> | 发行者 | No | No |
> | 发布者/产品/服务 | No | No |
> | 发布者/服务/修正 | No | No |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | classicDevServices | 是 | 是 |
> | updateCommunicationPreference | No | No |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | 协议 | No | No |
> | offertypes | No | No |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | mediaservices | 是 | 是 |
> | mediaservices/accountFilters | No | No |
> | mediaservices/assets | No | No |
> | mediaservices/assets/assetFilters | No | No |
> | mediaservices/contentKeyPolicies | No | No |
> | mediaservices/eventGridFilters | No | No |
> | mediaservices/liveEventOperations | No | No |
> | mediaservices/liveEvents | 是 | 是 |
> | mediaservices/liveEvents/liveOutputs | No | No |
> | mediaservices/liveOutputOperations | No | No |
> | windowsazure.mediaservices/mediaGraphs | No | No |
> | mediaservices/streamingEndpointOperations | No | No |
> | mediaservices/streamingEndpoints | 是 | 是 |
> | mediaservices/streamingLocators | No | No |
> | mediaservices/streamingPolicies | No | No |
> | mediaservices/transforms | No | No |
> | mediaservices/transforms/jobs | No | No |

## <a name="microsoftmicroservices4spring"></a>Microservices4Spring

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | appClusters | 是 | 是 |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | assessmentProjects | 是 | 是 |
> | migrateprojects | 是 | 是 |
> | projects | 是 | 是 |

## <a name="microsoftmixedreality"></a>MixedReality

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | holographicsBroadcastAccounts | 是 | 是 |
> | objectUnderstandingAccounts | 是 | 是 |
> | remoteRenderingAccounts | 是 | 是 |
> | spatialAnchorsAccounts | 是 | 是 |
> | surfaceReconstructionAccounts | 是 | 是 |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | netAppAccounts | 是 | 是 |
> | netAppAccounts/backupPolicies | 是 | 是 |
> | netAppAccounts/capacityPools | 是 | 是 |
> | netAppAccounts/capacityPools/卷 | 是 | 是 |
> | netAppAccounts/capacityPools/卷/备份 | No | No |
> | netAppAccounts/capacityPools/卷集/mountTargets | 是 | 是 |
> | netAppAccounts/capacityPools/卷/快照 | 是 | 是 |
> | netAppAccounts/保管库 | No | No |
## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | applicationGateways | 是 | 是 |
> | applicationGatewayWebApplicationFirewallPolicies | 是 | 是 |
> | applicationSecurityGroups | 是 | 是 |
> | azureFirewallFqdnTags | No | No |
> | azureFirewalls | 是 | No |
> | bastionHosts | 是 | 是 |
> | bgpServiceCommunities | No | No |
> | connections | 是 | 是 |
> | ddosCustomPolicies | 是 | 是 |
> | ddosProtectionPlans | 是 | 是 |
> | dnsOperationStatuses | No | No |
> | dnszones | 是 | 是 |
> | dnszones/A | No | No |
> | dnszones/AAAA | No | No |
> | dnszones/all | No | No |
> | dnszones/CAA | No | No |
> | dnszones/CNAME | No | No |
> | dnszones/MX | No | No |
> | dnszones/NS | No | No |
> | dnszones/PTR | No | No |
> | dnszones/recordsets | No | No |
> | dnszones/SOA | No | No |
> | dnszones/SRV | No | No |
> | dnszones/TXT | No | No |
> | expressRouteCircuits | 是 | 是 |
> | expressRouteCrossConnections | 是 | 是 |
> | expressRouteGateways | 是 | 是 |
> | expressRoutePorts | 是 | 是 |
> | expressRouteServiceProviders | No | No |
> | firewallPolicies | 是 | 是 |
> | frontdoors | 是，但受限制（请参阅[下面的注释](#frontdoor)） | 是 |
> | frontdoorWebApplicationFirewallManagedRuleSets | 是，但受限制（请参阅[下面的注释](#frontdoor)） | No |
> | frontdoorWebApplicationFirewallPolicies | 是，但受限制（请参阅[下面的注释](#frontdoor)） | 是 |
> | getDnsResourceReference | No | No |
> | internalNotify | No | No |
> | loadBalancers | 是 | No |
> | localNetworkGateways | 是 | 是 |
> | natGateways | 是 | 是 |
> | networkIntentPolicies | 是 | 是 |
> | networkInterfaces | 是 | 是 |
> | networkProfiles | 是 | 是 |
> | networkSecurityGroups | 是 | 是 |
> | networkWatchers | 是 | No |
> | networkWatchers/connectionMonitors | 是 | No |
> | networkWatchers/lenses | 是 | No |
> | networkWatchers/pingMeshes | 是 | No |
> | p2sVpnGateways | 是 | 是 |
> | privateDnsOperationStatuses | No | No |
> | privateDnsZones | 是 | 是 |
> | privateDnsZones/A | No | No |
> | privateDnsZones/AAAA | No | No |
> | privateDnsZones/所有 | No | No |
> | privateDnsZones/CNAME | No | No |
> | privateDnsZones/MX | No | No |
> | privateDnsZones/PTR | No | No |
> | privateDnsZones/SOA | No | No |
> | privateDnsZones/SRV | No | No |
> | privateDnsZones/TXT | No | No |
> | privateDnsZones/virtualNetworkLinks | 是 | 是 |
> | privateEndpoints | 是 | 是 |
> | privateLinkServices | 是 | 是 |
> | publicIPAddresses | 是 | 是 |
> | publicIPPrefixes | 是 | 是 |
> | routeFilters | 是 | 是 |
> | routeTables | 是 | 是 |
> | serviceEndpointPolicies | 是 | 是 |
> | trafficManagerGeographicHierarchies | No | No |
> | trafficmanagerprofiles | 是 | 是 |
> | trafficmanagerprofiles/heatMaps | No | No |
> | trafficManagerUserMetricsKeys | No | No |
> | virtualHubs | 是 | 是 |
> | virtualNetworkGateways | 是 | 是 |
> | virtualNetworks | 是 | 是 |
> | virtualNetworkTaps | 是 | 是 |
> | virtualWans | 是 | 是 |
> | vpnGateways | 是 | No |
> | vpnSites | 是 | 是 |
> | webApplicationFirewallPolicies | 是 | 是 |

<a id="frontdoor" />

> [!NOTE]
> 对于 Azure 前门服务，可以在创建资源时应用标记，但目前不支持更新或添加标记。


## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | namespaces | 是 | No |
> | namespaces/notificationHubs | 是 | No |

## <a name="microsoftobjectstore"></a>ObjectStore

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | osNamespaces | 是 | 是 |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | HyperVSites | 是 | 是 |
> | ImportSites | 是 | 是 |
> | ServerSites | 是 | 是 |
> | VMwareSites | 是 | 是 |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | clusters | 是 | 是 |
> | devices | No | No |
> | linkTargets | No | No |
> | storageInsightConfigs | No | No |
> | workspaces | 是 | 是 |
> | workspaces/dataSources | No | No |
> | workspaces/linkedServices | No | No |
> | workspaces/query | No | No |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | managementassociations | No | No |
> | managementconfigurations | 是 | 是 |
> | solutions | 是 | 是 |
> | 视图 | 是 | 是 |

## <a name="microsoftpeering"></a>Microsoft。对等互连

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | legacyPeerings | No | No |
> | peerAsns | No | No |
> | 对等互连 | 是 | 是 |
> | peeringServiceProviders | No | No |
> | peeringServices | 是 | 是 |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | policyEvents | No | No |
> | policyMetadata | No | No |
> | policyStates | No | No |
> | policyTrackedResources | No | No |
> | remediations | No | No |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | consoles | No | No |
> | dashboards | 是 | 是 |
> | userSettings | No | No |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | workspaceCollections | 是 | 是 |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | capacities | 是 | 是 |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | backupProtectedItems | No | No |
> | vaults | 是 | 是 |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | namespaces | 是 | 是 |
> | namespaces/authorizationrules | No | No |
> | namespaces/hybridconnections | No | No |
> | namespaces/hybridconnections/authorizationrules | No | No |
> | namespaces/wcfrelays | No | No |
> | namespaces/wcfrelays/authorizationrules | No | No |

## <a name="microsoftremoteapp"></a>Microsoft RemoteApp

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | accounts | No | No |
> | 收藏 | 是 | 是 |
> | 集合/应用程序 | No | No |
> | 集合/securityprincipalsgetresponse | No | No |
> | templateImages | No | No |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | 查询 | 是 | 是 |
> | resourceChangeDetails | No | No |
> | resourceChanges | No | No |
> | 资源 | No | No |
> | resourcesHistory | No | No |
> | subscriptionsStatus | No | No |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | availabilityStatuses | No | No |
> | childAvailabilityStatuses | No | No |
> | childResources | No | No |
> | 活动 | No | No |
> | impactedResources | No | No |
> | metadata | No | No |
> | 通知 | No | No |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | deployments | 是 | No |
> | deployments/operations | No | No |
> | deploymentScripts | 是 | 是 |
> | deploymentScripts/logs | No | No |
> | links | No | No |
> | notifyResourceJobs | No | No |
> | providers | No | No |
> | resourceGroups | 是 | No |
> | 资源 | No | No |
> | subscriptions | No | No |
> | subscriptions/providers | No | No |
> | subscriptions/resourceGroups | No | No |
> | subscriptions/resourcegroups/resources | No | No |
> | subscriptions/resources | No | No |
> | subscriptions/tagnames | No | No |
> | subscriptions/tagNames/tagValues | No | No |
> | tenants | No | No |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | 应用程序 | 是 | 是 |
> | saasresources | No | No |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | jobcollections | 是 | 是 |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | resourceHealthMetadata | No | No |
> | searchServices | 是 | 是 |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | adaptiveNetworkHardenings | No | No |
> | advancedThreatProtectionSettings | No | No |
> | alerts | No | No |
> | allowedConnections | No | No |
> | applicationWhitelistings | No | No |
> | assessmentMetadata | No | No |
> | 风险 | No | No |
> | 自动化 | 是 | 是 |
> | AutoProvisioningSettings | No | No |
> | Compliances | No | No |
> | dataCollectionAgents | No | No |
> | deviceSecurityGroups | No | No |
> | discoveredSecuritySolutions | No | No |
> | externalSecuritySolutions | No | No |
> | InformationProtectionPolicies | No | No |
> | iotSecuritySolutions | 是 | 是 |
> | iotSecuritySolutions/analyticsModels | No | No |
> | iotSecuritySolutions/analyticsModels/aggregatedAlerts | No | No |
> | iotSecuritySolutions/analyticsModels/aggregatedRecommendations | No | No |
> | jitNetworkAccessPolicies | No | No |
> | Networkdata.xml | No | No |
> | playbookConfigurations | 是 | 是 |
> | 策略 | No | No |
> | pricings | No | No |
> | regulatoryComplianceStandards | No | No |
> | regulatoryComplianceStandards/regulatoryComplianceControls | No | No |
> | regulatoryComplianceStandards/regulatoryComplianceControls/regulatoryComplianceAssessments | No | No |
> | securityContacts | No | No |
> | securitySolutions | No | No |
> | securitySolutionsReferenceData | No | No |
> | securityStatuses | No | No |
> | securityStatusesSummaries | No | No |
> | serverVulnerabilityAssessments | No | No |
> | 设置 | No | No |
> | subAssessments | No | No |
> | 任务 | No | No |
> | topologies | No | No |
> | workspaceSettings | No | No |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | No | No |
> | diagnosticSettingsCategories | No | No |

## <a name="microsoftsecurityinsights"></a>SecurityInsights

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | 聚合 | No | No |
> | alertRules | No | No |
> | alertRuleTemplates | No | No |
> | 书签 | No | No |
> | cases | No | No |
> | dataConnectors | No | No |
> | 实体 | No | No |
> | entityQueries | No | No |
> | officeConsents | No | No |
> | 设置 | No | No |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | namespaces | 是 | No |
> | namespaces/authorizationrules | No | No |
> | namespaces/disasterrecoveryconfigs | No | No |
> | namespaces/eventgridfilters | No | No |
> | 命名空间/networkrulesets | No | No |
> | namespaces/queues | No | No |
> | namespaces/queues/authorizationrules | No | No |
> | namespaces/topics | No | No |
> | namespaces/topics/authorizationrules | No | No |
> | namespaces/topics/subscriptions | No | No |
> | namespaces/topics/subscriptions/rules | No | No |
> | premiumMessagingRegions | No | No |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | 应用程序 | 是 | 是 |
> | clusters | 是 | 是 |
> | clusters/applications | No | No |
> | containerGroups | 是 | 是 |
> | containerGroupSets | 是 | 是 |
> | edgeclusters | 是 | 是 |
> | edgeclusters/应用程序 | No | No |
> | networks | 是 | 是 |
> | secretstores | 是 | 是 |
> | secretstores/证书 | No | No |
> | secretstores/机密 | No | No |
> | volumes | 是 | 是 |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | 应用程序 | 是 | 是 |
> | containerGroups | 是 | 是 |
> | gateways | 是 | 是 |
> | networks | 是 | 是 |
> | 机密 | 是 | 是 |
> | volumes | 是 | 是 |

## <a name="microsoftservices"></a>Microsoft. 服务

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | providerRegistrations | No | No |
> | providerRegistrations/resourceTypeRegistrations | No | No |
> | rollouts | 是 | 是 |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | SignalR | 是 | 是 |
> | SignalR/eventGridFilters | No | No |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | SiteRecoveryVault | 是 | 是 |

## <a name="microsoftsoftwareplan"></a>SoftwarePlan

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | hybridUseBenefits | No | No |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | applicationDefinitions | 是 | 是 |
> | 应用程序 | 是 | 是 |
> | jitRequests | 是 | 是 |

## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | managedInstances | 是 | 是 |
> | managedInstances/databases | 是（请参阅[下面的注释](#sqlnote)） | 是 |
> | managedInstances/databases/backupShortTermRetentionPolicies | No | No |
> | managedInstances/databases/schemas/tables/columns/sensitivityLabels | No | No |
> | managedInstances/databases/vulnerabilityAssessments | No | No |
> | managedInstances/databases/vulnerabilityAssessments/rules/baselines | No | No |
> | managedInstances/encryptionProtector | No | No |
> | managedInstances/keys | No | No |
> | managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies | No | No |
> | managedInstances/vulnerabilityAssessments | No | No |
> | 服务器 | 是 | 是 |
> | servers/administrators | No | No |
> | servers/communicationLinks | No | No |
> | servers/databases | 是（请参阅[下面的注释](#sqlnote)） | 是 |
> | servers/encryptionProtector | No | No |
> | servers/firewallRules | No | No |
> | servers/keys | No | No |
> | servers/restorableDroppedDatabases | No | No |
> | servers/serviceobjectives | No | No |
> | servers/tdeCertificates | No | No |
> | virtualClusters | No | No |

<a id="sqlnote" />

> [!NOTE]
> Master 数据库不支持标记，但其他数据库（包括 Azure SQL 数据仓库数据库）支持标记。 Azure SQL 数据仓库数据库必须处于活动（而非暂停）状态。

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | SqlVirtualMachineGroups | 是 | 是 |
> | SqlVirtualMachineGroups/AvailabilityGroupListeners | No | No |
> | SqlVirtualMachines | 是 | 是 |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | storageAccounts | 是 | 是 |
> | storageAccounts/blobServices | No | No |
> | storageAccounts/fileServices | No | No |
> | storageAccounts/queueServices | No | No |
> | storageAccounts/services | No | No |
> | storageAccounts/services/metricDefinitions | No | No |
> | storageAccounts/tableServices | No | No |
> | usages | No | No |

## <a name="microsoftstoragecache"></a>StorageCache

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | 缓存 | 是 | 是 |
> | 缓存/storageTargets | No | No |
> | usageModels | No | No |

## <a name="microsoftstoragereplication"></a>StorageReplication

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | replicationGroups | No | No |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | 是 | 是 |
> | storageSyncServices/registeredServers | No | No |
> | storageSyncServices/syncGroups | No | No |
> | storageSyncServices/syncGroups/cloudEndpoints | No | No |
> | storageSyncServices/syncGroups/serverEndpoints | No | No |
> | storageSyncServices/workflows | No | No |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | 是 | 是 |
> | storageSyncServices/registeredServers | No | No |
> | storageSyncServices/syncGroups | No | No |
> | storageSyncServices/syncGroups/cloudEndpoints | No | No |
> | storageSyncServices/syncGroups/serverEndpoints | No | No |
> | storageSyncServices/workflows | No | No |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | 是 | 是 |
> | storageSyncServices/registeredServers | No | No |
> | storageSyncServices/syncGroups | No | No |
> | storageSyncServices/syncGroups/cloudEndpoints | No | No |
> | storageSyncServices/syncGroups/serverEndpoints | No | No |
> | storageSyncServices/workflows | No | No |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | managers | 是 | 是 |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | streamingjobs | 是（见下方备注） | 是 |

> [!NOTE]
> Streamingjobs 运行时无法添加标记。 停止要添加标记的资源。

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | 取消 | No | No |
> | CreateSubscription | No | No |
> | enable | No | No |
> | 重命名 | No | No |
> | SubscriptionDefinitions | No | No |
> | SubscriptionOperations | No | No |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | environments | 是 | No |
> | environments/accessPolicies | No | No |
> | environments/eventsources | 是 | No |
> | environments/referenceDataSets | 是 | No |

## <a name="microsoftvmwarecloudsimple"></a>VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | dedicatedCloudNodes | 是 | 是 |
> | dedicatedCloudServices | 是 | 是 |
> | virtualMachines | 是 | 是 |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | apiManagementAccounts | No | No |
> | apiManagementAccounts/apiAcls | No | No |
> | apiManagementAccounts/apis | No | No |
> | apiManagementAccounts/apis/apiAcls | No | No |
> | apiManagementAccounts/apis/connectionAcls | No | No |
> | apiManagementAccounts/apis/connections | No | No |
> | apiManagementAccounts/apis/connections/connectionAcls | No | No |
> | apiManagementAccounts/apis/localizedDefinitions | No | No |
> | apiManagementAccounts/connectionAcls | No | No |
> | apiManagementAccounts/connections | No | No |
> | billingMeters | No | No |
> | certificates | 是 | 是 |
> | connectionGateways | 是 | 是 |
> | connections | 是 | 是 |
> | customApis | 是 | 是 |
> | deletedSites | No | No |
> | functions | No | No |
> | hostingEnvironments | 是 | 是 |
> | hostingEnvironments/multiRolePools | No | No |
> | hostingEnvironments/workerPools | No | No |
> | publishingUsers | No | No |
> | 建议 | No | No |
> | resourceHealthMetadata | No | No |
> | runtimes | No | No |
> | serverFarms | 是 | 是 |
> | serverFarms/eventGridFilters | No | No |
> | sites | 是 | 是 |
> | 站点/配置  | No | No |
> | sites/eventGridFilters | No | No |
> | sites/hostNameBindings | No | No |
> | sites/Networkconfig.netcfg | No | No |
> | sites/premieraddons | 是 | 是 |
> | sites/slots | 是 | 是 |
> | 站点/槽/eventGridFilters | No | No |
> | sites/slots/hostNameBindings | No | No |
> | 站点/槽/Networkconfig.netcfg | No | No |
> | sourceControls | No | No |
> | validate | No | No |
> | verifyHostingEnvironmentVnet | No | No |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | No | No |
> | diagnosticSettingsCategories | No | No |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | DeviceServices | 是 | 是 |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 成本报表中的标记 |
> | ------------- | ----------- | ----------- |
> | components | No | No |
> | componentsSummary | No | No |
> | monitorInstances | No | No |
> | monitorInstancesSummary | No | No |
> | monitors | No | No |
> | notificationSettings | No | No |

## <a name="next-steps"></a>后续步骤

若要了解如何将标记应用于资源，请参见[使用标记来组织 Azure 资源](resource-group-using-tags.md)。
