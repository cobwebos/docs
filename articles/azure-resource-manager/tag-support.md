---
title: 针对资源的 Azure 资源管理器标记支持
description: 显示支持标记的 Azure资源类型。 提供所有 Azure 服务的详细信息。
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 06/07/2019
ms.author: tomfitz
ms.openlocfilehash: 72bb11cd064c90c2bbe1e9e6452dcbf07fe37817
ms.sourcegitcommit: 770b060438122f090ab90d81e3ff2f023455213b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/17/2019
ms.locfileid: "68304868"
---
# <a name="tag-support-for-azure-resources"></a>Azure 资源的标记支持
本文介绍某一资源类型是否支持[标记](resource-group-using-tags.md)。 标记为**支持标记**的列指示资源类型是否具有标记的属性。 标记为 **"成本报表中的标记**" 的列指示该资源类型是否将标记传递给成本报告。

若要以逗号分隔值文件的形式获取同一数据，请下载 [tag-support.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/tag-support.csv)。

## <a name="microsoftaad"></a>Microsoft.AAD
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| DomainServices | 是 | 是 |
| DomainServices/oucontainer | 否 | 否 |

## <a name="microsoftaadiam"></a>microsoft.aadiam
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| diagnosticSettings | 否 |  否 |
| diagnosticSettingsCategories | 否 |  否 |

## <a name="microsoftaddons"></a>Microsoft.Addons
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| supportProviders | 否 |  否 |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| aadsupportcases | 否 |  否 |
| addsservices | 否 |  否 |
| 代理 | 否 |  否 |
| anonymousapiusers | 否 |  否 |
| 配置 | 否 |  否 |
| logs | 否 |  否 |
| 报表 | 否 |  否 |
| services | 否 |  否 |

## <a name="microsoftadvisor"></a>Microsoft.Advisor
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| 配置 | 否 |  否 |
| generateRecommendations | 否 |  否 |
| 建议 | 否 |  否 |
| 禁止显示 | 否 |  否 |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| actionRules | 否 |  否 |
| alerts | 否 |  否 |
| alertsList | 否 |  否 |
| alertsSummary | 否 |  否 |
| alertsSummaryList | 否 |  否 |
| smartDetectorAlertRules | 否 |  否 |
| smartDetectorRuntimeEnvironments | 否 |  否 |
| smartGroups | 否 |  否 |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| servers | 是 | 是 |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| reportFeedback | 否 |  否 |
| 服务 | 是 | 是 |
| validateServiceName | 否 |  否 |

## <a name="microsoftattestation"></a>Microsoft.Attestation
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| attestationProviders | 否 |  否 |

## <a name="microsoftauthorization"></a>Microsoft.Authorization
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| classicAdministrators | 否 |  否 |
| denyAssignments | 否 |  否 |
| elevateAccess | 否 |  否 |
| 锁定 | 否 |  否 |
| 权限 | 否 |  否 |
| policyAssignments | 否 |  否 |
| policyDefinitions | 否 |  否 |
| policySetDefinitions | 否 |  否 |
| providerOperations | 否 |  否 |
| roleAssignments | 否 |  否 |
| roleDefinitions | 否 |  否 |

## <a name="microsoftautomation"></a>Microsoft.Automation
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| automationAccounts | 是 | 是 |
| automationAccounts/configurations | 是 | 是 |
| automationAccounts/jobs | 否 |  否 |
| automationAccounts/runbooks | 是 | 是 |
| automationAccounts/softwareUpdateConfigurations | 否 | 否 |
| automationAccounts/webhooks | 否 |  否 |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| environments | 否 |  否 |
| environments/accounts | 否 |  否 |
| environments/accounts/namespaces | 否 |  否 |
| environments/accounts/namespaces/configurations | 否 |  否 |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| b2cDirectories | 是 | 否 |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| registrations | 是 | 是 |
| registrations/customerSubscriptions | 否 |  否 |
| registrations/products | 否 |  否 |

## <a name="microsoftbatch"></a>Microsoft.Batch
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| batchAccounts | 是 | 是 |

## <a name="microsoftbilling"></a>Microsoft.Billing
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| billingAccounts | 否 |  否 |
| billingAccounts/billingProfiles | 否 |  否 |
| billingAccounts/billingProfiles/billingSubscriptions | 否 |  否 |
| billingAccounts/billingProfiles/invoices | 否 |  否 |
| billingAccounts/billingProfiles/invoices/pricesheet | 否 |  否 |
| billingAccounts/billingProfiles/operationStatus | 否 |  否 |
| billingAccounts/billingProfiles/paymentMethods | 否 |  否 |
| billingAccounts/billingProfiles/policies | 否 |  否 |
| billingAccounts/billingProfiles/pricesheet | 否 |  否 |
| billingAccounts/billingProfiles/products | 否 |  否 |
| billingAccounts/billingProfiles/transactions | 否 |  否 |
| billingAccounts/billingSubscriptions | 否 |  否 |
| billingAccounts/departments | 否 |  否 |
| billingAccounts/eligibleOffers | 否 |  否 |
| billingAccounts/enrollmentAccounts | 否 |  否 |
| billingAccounts/invoices | 否 |  否 |
| billingAccounts/invoiceSections | 否 |  否 |
| billingAccounts/invoiceSections/billingSubscriptions | 否 |  否 |
| billingAccounts/invoiceSections/billingSubscriptions/transfer | 否 |  否 |
| billingAccounts/invoiceSections/importRequests | 否 |  否 |
| billingAccounts/invoiceSections/initiateImportRequest | 否 |  否 |
| billingAccounts/invoiceSections/initiateTransfer | 否 |  否 |
| billingAccounts/invoiceSections/operationStatus | 否 |  否 |
| billingAccounts/invoiceSections/products | 否 |  否 |
| billingAccounts/invoiceSections/transfers | 否 |  否 |
| billingAccounts/products | 否 |  否 |
| billingAccounts/projects | 否 |  否 |
| billingAccounts/projects/billingSubscriptions | 否 |  否 |
| billingAccounts/projects/importRequests | 否 |  否 |
| billingAccounts/projects/initiateImportRequest | 否 |  否 |
| billingAccounts/projects/operationStatus | 否 |  否 |
| billingAccounts/projects/products | 否 |  否 |
| billingAccounts/transactions | 否 |  否 |
| billingPeriods | 否 |  否 |
| BillingPermissions | 否 |  否 |
| billingProperty | 否 |  否 |
| BillingRoleAssignments | 否 |  否 |
| BillingRoleDefinitions | 否 |  否 |
| CreateBillingRoleAssignment | 否 |  否 |
| departments | 否 |  否 |
| enrollmentAccounts | 否 |  否 |
| importRequests | 否 |  否 |
| importRequests/acceptImportRequest | 否 |  否 |
| importRequests/declineImportRequest | 否 |  否 |
| invoices | 否 |  否 |
| transfers | 否 |  否 |
| transfers/acceptTransfer | 否 |  否 |
| transfers/declineTransfer | 否 |  否 |
| transfers/operationStatus | 否 |  否 |
| usagePlans | 否 |  否 |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| mapApis | 是 | 是 |
| updateCommunicationPreference | 否 |  否 |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| BizTalk | 是 | 是 |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| blueprintAssignments | 否 |  否 |
| blueprintAssignments/assignmentOperations | 否 |  否 |
| blueprintAssignments/operations | 否 |  否 |
| blueprints | 否 |  否 |
| blueprints/artifacts | 否 |  否 |
| blueprints/versions | 否 |  否 |
| blueprints/versions/artifacts | 否 |  否 |

## <a name="microsoftbotservice"></a>Microsoft.BotService
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| botServices | 是 | 是 |
| botServices/channels | 否 |  否 |
| botServices/connections | 否 |  否 |

## <a name="microsoftcache"></a>Microsoft.Cache
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| Redis | 是 | 是 |
| RedisConfigDefinition | 否 |  否 |

## <a name="microsoftcapacity"></a>Microsoft.Capacity
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| appliedReservations | 否 |  否 |
| calculatePrice | 否 |  否 |
| catalogs | 否 |  否 |
| commercialReservationOrders | 否 |  否 |
| reservationOrders | 否 |  否 |
| reservationOrders/calculateRefund | 否 |  否 |
| reservationOrders/merge | 否 |  否 |
| reservationOrders/reservations | 否 |  否 |
| reservationOrders/reservations/revisions | 否 |  否 |
| reservationOrders/return | 否 |  否 |
| reservationOrders/split | 否 |  否 |
| reservationOrders/swap | 否 |  否 |
| reservations | 否 |  否 |
| 资源 | 否 |  否 |
| validateReservationOrder | 否 |  否 |

## <a name="microsoftcdn"></a>Microsoft.Cdn
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| edgenodes | 否 |  否 |
| profiles | 是 | 是 |
| profiles/endpoints | 是 | 是 |
| profiles/endpoints/customdomains | 否 |  否 |
| profiles/endpoints/origins | 否 |  否 |
| validateProbe | 否 |  否 |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| certificateOrders | 是 | 是 |
| certificateOrders/certificates | 否 |  否 |
| validateCertificateRegistrationInformation | 否 |  否 |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| capabilities | 否 |  否 |
| domainNames | 否 |  否 |
| domainNames/capabilities | 否 |  否 |
| domainNames/internalLoadBalancers | 否 |  否 |
| domainNames/serviceCertificates | 否 |  否 |
| domainNames/slots | 否 |  否 |
| domainNames/slots/roles | 否 |  否 |
| moveSubscriptionResources | 否 |  否 |
| operatingSystemFamilies | 否 |  否 |
| operatingSystems | 否 |  否 |
| quotas | 否 |  否 |
| resourceTypes | 否 |  否 |
| validateSubscriptionMoveAvailability | 否 |  否 |
| virtualMachines | 否 |  否 |
| virtualMachines/diagnosticSettings | 否 |  否 |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| classicInfrastructureResources | 否 |  否 |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| capabilities | 否 |  否 |
| expressRouteCrossConnections | 否 |  否 |
| expressRouteCrossConnections/peerings | 否 |  否 |
| gatewaySupportedDevices | 否 |  否 |
| networkSecurityGroups | 否 |  否 |
| quotas | 否 |  否 |
| reservedIps | 否 |  否 |
| virtualNetworks | 否 |  否 |
| virtualNetworks/remoteVirtualNetworkPeeringProxies | 否 |  否 |
| virtualNetworks/virtualNetworkPeerings | 否 |  否 |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| capabilities | 否 |  否 |
| disks | 否 |  否 |
| images | 否 |  否 |
| osImages | 否 |  否 |
| osPlatformImages | 否 |  否 |
| publicImages | 否 |  否 |
| quotas | 否 |  否 |
| storageAccounts | 否 |  否 |
| storageAccounts/services | 否 |  否 |
| storageAccounts/services/diagnosticSettings | 否 |  否 |
| storageAccounts/vmImages | 否 |  否 |
| vmImages | 否 |  否 |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| accounts | 是 | 是 |

## <a name="microsoftcommerce"></a>Microsoft.Commerce
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| RateCard | 否 |  否 |
| UsageAggregates | 否 |  否 |

## <a name="microsoftcompute"></a>Microsoft.Compute
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| availabilitySets | 是 | 是 |
| disks | 是 | 是 |
| images | 是 | 是 |
| restorePointCollections | 是 | 是 |
| restorePointCollections/restorePoints | 否 |  否 |
| sharedVMImages | 是 | 是 |
| sharedVMImages/versions | 是 | 是 |
| snapshots | 是 | 是 |
| virtualMachines | 是 | 是 |
| virtualMachines/diagnosticSettings | 否 |  否 |
| virtualMachines/extensions | 是 | 是 |
| virtualMachineScaleSets | 是 | 是 |
| virtualMachineScaleSets/extensions | 否 |  否 |
| virtualMachineScaleSets/networkInterfaces | 否 |  否 |
| virtualMachineScaleSets/publicIPAddresses | 否 |  否 |
| virtualMachineScaleSets/virtualMachines | 否 |  否 |
| virtualMachineScaleSets/virtualMachines/networkInterfaces | 否 |  否 |

## <a name="microsoftconsumption"></a>Microsoft.Consumption
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| AggregatedCost | 否 |  否 |
| 余额 | 否 |  否 |
| 预算 | 否 |  否 |
| Charges | 否 |  否 |
| CostTags | 否 |  否 |
| credits | 否 |  否 |
| 活动 | 否 |  否 |
| 预测 | 否 |  否 |
| lots | 否 |  否 |
| 市场 | 否 |  否 |
| Pricesheets | 否 |  否 |
| products | 否 |  否 |
| ReservationDetails | 否 |  否 |
| ReservationRecommendations | 否 |  否 |
| ReservationSummaries | 否 |  否 |
| ReservationTransactions | 否 |  否 |
| Tags | 否 |  否 |
| 术语 | 否 |  否 |
| UsageDetails | 否 |  否 |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| containerGroups | 是 | 是 |
| serviceAssociationLinks | 否 |  否 |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| registries | 是 | 是 |
| registries/builds | 否 |  否 |
| registries/builds/cancel | 否 |  否 |
| registries/builds/getLogLink | 否 |  否 |
| registries/buildTasks | 是 | 是 |
| registries/buildTasks/steps | 否 |  否 |
| registries/eventGridFilters | 否 |  否 |
| registries/getBuildSourceUploadUrl | 否 |  否 |
| registries/GetCredentials | 否 |  否 |
| registries/importImage | 否 |  否 |
| registries/queueBuild | 否 |  否 |
| registries/regenerateCredential | 否 |  否 |
| registries/regenerateCredentials | 否 |  否 |
| registries/replications | 是 | 是 |
| registries/runs | 否 |  否 |
| registries/runs/cancel | 否 |  否 |
| registries/scheduleRun | 否 |  否 |
| registries/tasks | 是 | 是 |
| registries/updatePolicies | 否 |  否 |
| registries/webhooks | 是 | 是 |
| registries/webhooks/getCallbackConfig | 否 |  否 |
| registries/webhooks/ping | 否 |  否 |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| containerServices | 是 | 是 |
| managedClusters | 是 | 是 |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| 服务 | 是 | 是 |
| updateCommunicationPreference | 否 |  否 |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| accounts | 是 | 是 |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| 警报 | 否 |  否 |
| BillingAccounts | 否 |  否 |
| 连接器 | 是 | 是 |
| Departments | 否 |  否 |
| 维度 | 否 |  否 |
| EnrollmentAccounts | 否 |  否 |
| Query | 否 |  否 |
| register | 否 |  否 |
| Reportconfigs | 否 |  否 |
| 报告 | 否 |  否 |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| hubs | 是 | 是 |
| hubs/authorizationPolicies | 否 |  否 |
| hubs/connectors | 否 |  否 |
| hubs/connectors/mappings | 否 |  否 |
| hubs/interactions | 否 |  否 |
| hubs/kpi | 否 |  否 |
| hubs/links | 否 |  否 |
| hubs/profiles | 否 |  否 |
| hubs/roleAssignments | 否 |  否 |
| hubs/roles | 否 |  否 |
| hubs/suggestTypeSchema | 否 |  否 |
| hubs/views | 否 |  否 |
| hubs/widgetTypes | 否 |  否 |

## <a name="microsoftdatabox"></a>Microsoft.DataBox
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| jobs | 是 | 是 |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| DataBoxEdgeDevices | 是 | 是 |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| workspaces | 是 | 否 |
| workspaces/virtualNetworkPeerings | 否 |  否 |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| catalogs | 是 | 是 |

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| connectionManagers | 是 | 是 |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| dataFactories | 是 | 否 |
| dataFactories/diagnosticSettings | 否 |  否 |
| dataFactorySchema | 否 |  否 |
| factories | 是 | 否 |
| factories/integrationRuntimes | 否 |  否 |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| accounts | 是 | 是 |
| accounts/dataLakeStoreAccounts | 否 |  否 |
| accounts/storageAccounts | 否 |  否 |
| accounts/storageAccounts/containers | 否 |  否 |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| accounts | 是 | 是 |
| accounts/eventGridFilters | 否 |  否 |
| accounts/firewallRules | 否 |  否 |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| services | 是 | 是 |
| services/projects | 是 | 是 |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| servers | 是 | 是 |
| servers/recoverableServers | 否 |  否 |
| servers/virtualNetworkRules | 否 |  否 |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| servers | 是 | 是 |
| servers/recoverableServers | 否 |  否 |
| servers/virtualNetworkRules | 否 |  否 |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| servers | 是 | 是 |
| servers/advisors | 否 |  否 |
| servers/queryTexts | 否 |  否 |
| servers/recoverableServers | 否 |  否 |
| servers/topQueryStatistics | 否 |  否 |
| servers/virtualNetworkRules | 否 |  否 |
| servers/waitStatistics | 否 |  否 |

## <a name="microsoftdevices"></a>Microsoft.Devices
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| IotHubs | 是 | 是 |
| IotHubs/eventGridFilters | 否 |  否 |
| ProvisioningServices | 是 | 是 |
| usages | 否 |  否 |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| 控制器 | 是 | 是 |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| labs | 是 | 是 |
| labs/serviceRunners | 是 | 是 |
| labs/virtualMachines | 是 | 是 |
| schedules | 是 | 是 |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| databaseAccountNames | 否 |  否 |
| databaseAccounts | 是 | 是 |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| domains | 是 | 是 |
| domains/domainOwnershipIdentifiers | 否 |  否 |
| generateSsoRequest | 否 |  否 |
| topLevelDomains | 否 |  否 |
| validateDomainRegistrationInformation | 否 |  否 |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| lcsprojects | 否 |  否 |
| lcsprojects/clouddeployments | 否 |  否 |
| lcsprojects/connectors | 否 |  否 |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| domains | 是 | 否 |
| 域/主题 | 否 |  否 |
| eventSubscriptions | 否 |  否 |
| extensionTopics | 否 |  否 |
| topics | 是 | 否 |
| topicTypes | 否 |  否 |

## <a name="microsofteventhub"></a>Microsoft.EventHub
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| clusters | 是 | 否 |
| namespaces | 是 | 否 |
| namespaces/authorizationrules | 否 |  否 |
| namespaces/disasterrecoveryconfigs | 否 |  否 |
| namespaces/eventhubs | 否 |  否 |
| namespaces/eventhubs/authorizationrules | 否 |  否 |
| namespaces/eventhubs/consumergroups | 否 |  否 |

## <a name="microsoftfeatures"></a>Microsoft.Features
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| features | 否 |  否 |
| providers | 否 |  否 |

## <a name="microsoftgallery"></a>Microsoft.Gallery
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| enroll | 否 |  否 |
| galleryitems | 否 |  否 |
| generateartifactaccessuri | 否 |  否 |
| myareas | 否 |  否 |
| myareas/areas | 否 |  否 |
| myareas/areas/areas | 否 |  否 |
| myareas/areas/areas/galleryitems | 否 |  否 |
| myareas/areas/galleryitems | 否 |  否 |
| myareas/galleryitems | 否 |  否 |
| register | 否 |  否 |
| 资源 | 否 |  否 |
| retrieveresourcesbyid | 否 |  否 |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| guestConfigurationAssignments | 否 |  否 |
| software | 否 |  否 |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| hanainstances | 是 |  是 |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| clusters | 是 | 是 |
| clusters/applications | 否 |  否 |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| jobs | 是 | 是 |

## <a name="microsoftinformationprotection"></a>Microsoft.InformationProtection
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| labelGroups | 否 |  否 |
| labelGroups/labels | 否 |  否 |
| labelGroups/labels/conditions | 否 |  否 |
| labelGroups/labels/subLabels | 否 |  否 |
| labelGroups/labels/subLabels/conditions | 否 |  否 |

## <a name="microsoftinsights"></a>microsoft.insights
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| actiongroups | 是 | 是 |
| activityLogAlerts | 是 | 是 |
| alertrules | 是 | 是 |
| automatedExportSettings | 否 |  否 |
| autoscalesettings | 是 | 是 |
| baseline | 否 |  否 |
| calculatebaseline | 否 |  否 |
| components | 是 | 是 |
| components/events | 否 |  否 |
| components/pricingPlans | 否 |  否 |
| components/query | 否 |  否 |
| diagnosticSettings | 否 |  否 |
| diagnosticSettingsCategories | 否 |  否 |
| eventCategories | 否 |  否 |
| eventtypes | 否 |  否 |
| extendedDiagnosticSettings | 否 |  否 |
| logDefinitions | 否 |  否 |
| logprofiles | 否 |  否 |
| logs | 否 |  否 |
| metricAlerts | 是 | 是 |
| migrateToNewPricingModel | 否 |  否 |
| myWorkbooks | 否 |  否 |
| 查询 | 否 |  否 |
| rollbackToLegacyPricingModel | 否 |  否 |
| scheduledqueryrules | 是 | 是 |
| vmInsightsOnboardingStatuses | 否 |  否 |
| webtests | 是 | 是 |
| workbooks | 是 | 是 |

## <a name="microsoftintune"></a>Microsoft.Intune
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| diagnosticSettings | 否 |  否 |
| diagnosticSettingsCategories | 否 |  否 |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| IoTApps | 是 | 是 |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| 图形 | 是 | 是 |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| deletedVaults | 否 |  否 |
| vaults | 是 | 是 |
| vaults/accessPolicies | 否 |  否 |
| vaults/secrets | 否 |  否 |

## <a name="microsoftkusto"></a>Microsoft.Kusto
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| clusters | 是 | 是 |
| clusters/databases | 否 |  否 |
| clusters/databases/dataconnections | 否 |  否 |
| clusters/databases/eventhubconnections | 否 |  否 |

## <a name="microsoftlabservices"></a>Microsoft.LabServices
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| labaccounts | 是 | 是 |
| users | 否 |  否 |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| accounts | 是 | 是 |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| accounts | 是 | 是 |

## <a name="microsoftloganalytics"></a>Microsoft.LogAnalytics
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| logs | 否 |  否 |

## <a name="microsoftlogic"></a>Microsoft.Logic
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| integrationAccounts | 是 | 是 |
| workflows | 是 | 是 |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| commitmentPlans | 是 | 是 |
| webServices | 是 | 是 |
| 工作区 | 是 | 是 |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| accounts | 是 | 是 |
| accounts/workspaces | 是 | 是 |
| accounts/workspaces/projects | 是 | 是 |
| teamAccounts | 是 | 是 |
| teamAccounts/workspaces | 是 | 是 |
| teamAccounts/workspaces/projects | 是 | 是 |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| accounts | 是 | 是 |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| workspaces | 是 | 是 |
| workspaces/computes | 否 |  否 |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| 标识 | 否 |  否 |
| userAssignedIdentities | 是 | 

## <a name="microsoftmanagement"></a>Microsoft.Management
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| getEntities | 否 |  否 |
| managementGroups | 否 |  否 |
| 资源 | 否 |  否 |
| startTenantBackfill | 否 |  否 |
| tenantBackfillStatus | 否 |  否 |

## <a name="microsoftmaps"></a>Microsoft.Maps
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| accounts | 是 | 是 |
| accounts/eventGridFilters | 否 |  否 |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| offers | 否 |  否 |
| offerTypes | 否 |  否 |
| offerTypes/publishers | 否 |  否 |
| offerTypes/publishers/offers | 否 |  否 |
| offerTypes/publishers/offers/plans | 否 |  否 |
| offerTypes/publishers/offers/plans/agreements | 否 |  否 |
| offerTypes/publishers/offers/plans/configs | 否 |  否 |
| offerTypes/publishers/offers/plans/configs/importImage | 否 |  否 |
| privategalleryitems | 否 |  否 |
| products | 否 |  否 |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| classicDevServices | 是 | 是 |
| updateCommunicationPreference | 否 |  否 |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| 协议 | 否 |  否 |
| offertypes | 否 |  否 |

## <a name="microsoftmedia"></a>Microsoft.Media
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| mediaservices | 是 | 是 |
| mediaservices/accountFilters | 否 |  否 |
| mediaservices/assets | 否 |  否 |
| mediaservices/assets/assetFilters | 否 |  否 |
| mediaservices/contentKeyPolicies | 否 |  否 |
| mediaservices/eventGridFilters | 否 |  否 |
| mediaservices/liveEventOperations | 否 |  否 |
| mediaservices/liveEvents | 是 | 是 |
| mediaservices/liveEvents/liveOutputs | 否 |  否 |
| mediaservices/liveOutputOperations | 否 |  否 |
| mediaservices/streamingEndpointOperations | 否 |  否 |
| mediaservices/streamingEndpoints | 是 | 是 |
| mediaservices/streamingLocators | 否 |  否 |
| mediaservices/streamingPolicies | 否 |  否 |
| mediaservices/transforms | 否 |  否 |
| mediaservices/transforms/jobs | 否 |  否 |

## <a name="microsoftmigrate"></a>Microsoft.Migrate
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| projects | 是 | 是 |

## <a name="microsoftnetwork"></a>Microsoft.Network
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| applicationGateways | 是 | 否 |
| applicationSecurityGroups | 是 | 是 |
| azureFirewallFqdnTags | 否 |  否 |
| azureFirewalls | 是 | 否 |
| bgpServiceCommunities | 否 |  否 |
| connections | 是 | 是 |
| ddosCustomPolicies | 是 | 是 |
| ddosProtectionPlans | 是 | 是 |
| dnsOperationStatuses | 否 |  否 |
| dnszones | 是 | 是 |
| dnszones/A | 否 |  否 |
| dnszones/AAAA | 否 |  否 |
| dnszones/all | 否 |  否 |
| dnszones/CAA | 否 |  否 |
| dnszones/CNAME | 否 |  否 |
| dnszones/MX | 否 |  否 |
| dnszones/NS | 否 |  否 |
| dnszones/PTR | 否 |  否 |
| dnszones/recordsets | 否 |  否 |
| dnszones/SOA | 否 |  否 |
| dnszones/SRV | 否 |  否 |
| dnszones/TXT | 否 |  否 |
| expressRouteCircuits | 是  | 否 |
| expressRouteServiceProviders | 否 |  否 |
| frontdoors | 是, 但受限制 (请参阅[下面的注释](#frontdoor)) | 是 |
| frontdoorWebApplicationFirewallPolicies | 是, 但受限制 (请参阅[下面的注释](#frontdoor)) | 是 |
| getDnsResourceReference | 否 |  否 |
| interfaceEndpoints | 是 | 是 |
| internalNotify | 否 |  否 |
| loadBalancers | 是 | 否 |
| localNetworkGateways | 是 | 是 |
| natGateways | 是 | 是 |
| networkIntentPolicies | 是 | 是 |
| networkInterfaces | 是 | 是 |
| networkProfiles | 是 | 是 |
| networkSecurityGroups | 是 | 是 |
| networkWatchers | 是 | 否 |
| networkWatchers/connectionMonitors | 是 | 否 |
| networkWatchers/lenses | 是 | 否 |
| networkWatchers/pingMeshes | 是 | 否 |
| privateLinkServices | 是 | 是 |
| publicIPAddresses | 是 | 是 |
| publicIPPrefixes | 是 | 是 |
| routeFilters | 是 | 是 |
| routeTables | 是 | 是 |
| serviceEndpointPolicies | 是 | 是 |
| trafficManagerGeographicHierarchies | 否 |  否 |
| trafficmanagerprofiles | 是 | 是 |
| trafficmanagerprofiles/heatMaps | 否 |  否 |
| virtualHubs | 是 | 是 |
| virtualNetworkGateways | 是 | 是 |
| virtualNetworks | 是 | 是 |
| virtualNetworks/subnets | 否 |  否 |
| virtualNetworkTaps | 是 | 是 |
| virtualWans | 是 | 是 |
| vpnGateways | 是 | 否 |
| vpnSites | 是 | 是 |
| webApplicationFirewallPolicies | 是 | 是 |

<a id="frontdoor" />

对于 Azure 前门服务, 可以在创建资源时应用标记, 但目前不支持更新或添加标记。

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| namespaces | 是 | 否 |
| namespaces/notificationHubs | 是 | 否 |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| devices | 否 |  否 |
| linkTargets | 否 |  否 |
| storageInsightConfigs | 否 |  否 |
| workspaces | 是 | 是 |
| workspaces/dataSources | 否 |  否 |
| workspaces/linkedServices | 否 |  否 |
| workspaces/query | 否 |  否 |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| managementassociations | 否 |  否 |
| managementconfigurations | 是 | 是 |
| solutions | 是 | 是 |
| 视图 | 是 | 是 |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| policyEvents | 否 |  否 |
| policyStates | 否 |  否 |
| policyTrackedResources | 否 |  否 |
| remediations | 否 |  否 |

## <a name="microsoftportal"></a>Microsoft.Portal
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| consoles | 否 |  否 |
| dashboards | 是 | 是 |
| userSettings | 否 |  否 |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| workspaceCollections | 是 | 是 |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| capacities | 是 | 是 |

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| accounts | 是 | 是 |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| backupProtectedItems | 否 |  否 |
| vaults | 是 | 是 |

## <a name="microsoftrelay"></a>Microsoft.Relay
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| namespaces | 是 | 是 |
| namespaces/authorizationrules | 否 |  否 |
| namespaces/hybridconnections | 否 |  否 |
| namespaces/hybridconnections/authorizationrules | 否 |  否 |
| namespaces/wcfrelays | 否 |  否 |
| namespaces/wcfrelays/authorizationrules | 否 |  否 |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| 资源 | 否 |  否 |
| subscriptionsStatus | 否 |  否 |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| availabilityStatuses | 否 |  否 |
| childAvailabilityStatuses | 否 |  否 |
| childResources | 否 |  否 |
| 活动 | 否 |  否 |
| impactedResources | 否 |  否 |
| 通知 | 否 |  否 |

## <a name="microsoftresources"></a>Microsoft.Resources
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| deployments | 否 |  否 |
| deployments/operations | 否 |  否 |
| links | 否 |  否 |
| notifyResourceJobs | 否 |  否 |
| providers | 否 |  否 |
| resourceGroups | 否 |  否 |
| 资源 | 否 |  否 |
| subscriptions | 否 |  否 |
| subscriptions/providers | 否 |  否 |
| subscriptions/resourceGroups | 否 |  否 |
| subscriptions/resourcegroups/resources | 否 |  否 |
| subscriptions/resources | 否 |  否 |
| subscriptions/tagnames | 否 |  否 |
| subscriptions/tagNames/tagValues | 否 |  否 |
| tenants | 否 |  否 |

## <a name="microsoftsaas"></a>Microsoft.SaaS
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| 服务 | 是 | 是 |
| saasresources | 否 |  否 |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| flows | 是 | 是 |
| jobcollections | 是 | 是 |

## <a name="microsoftsearch"></a>Microsoft.Search
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| resourceHealthMetadata | 否 |  否 |
| searchServices | 是 | 是 |

## <a name="microsoftsecurity"></a>Microsoft.Security
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| advancedThreatProtectionSettings | 否 |  否 |
| alerts | 否 |  否 |
| allowedConnections | 否 |  否 |
| appliances | 否 |  否 |
| applicationWhitelistings | 否 |  否 |
| AutoProvisioningSettings | 否 |  否 |
| Compliances | 否 |  否 |
| dataCollectionAgents | 否 |  否 |
| discoveredSecuritySolutions | 否 |  否 |
| externalSecuritySolutions | 否 |  否 |
| InformationProtectionPolicies | 否 |  否 |
| jitNetworkAccessPolicies | 否 |  否 |
| monitoring | 否 |  否 |
| monitoring/antimalware | 否 |  否 |
| monitoring/baseline | 否 |  否 |
| monitoring/patch | 否 |  否 |
| 策略 | 否 |  否 |
| pricings | 否 |  否 |
| securityContacts | 否 |  否 |
| securitySolutions | 否 |  否 |
| securitySolutionsReferenceData | 否 |  否 |
| securityStatus | 否 |  否 |
| securityStatus/endpoints | 否 |  否 |
| securityStatus/subnets | 否 |  否 |
| securityStatus/virtualMachines | 否 |  否 |
| securityStatuses | 否 |  否 |
| securityStatusesSummaries | 否 |  否 |
| 设置 | 否 |  否 |
| 任务 | 否 |  否 |
| topologies | 否 |  否 |
| workspaceSettings | 否 |  否 |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| diagnosticSettings | 否 |  否 |
| diagnosticSettingsCategories | 否 |  否 |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| namespaces | 是 | 否 |
| namespaces/authorizationrules | 否 |  否 |
| namespaces/disasterrecoveryconfigs | 否 |  否 |
| namespaces/eventgridfilters | 否 |  否 |
| namespaces/queues | 否 |  否 |
| namespaces/queues/authorizationrules | 否 |  否 |
| namespaces/topics | 否 |  否 |
| namespaces/topics/authorizationrules | 否 |  否 |
| namespaces/topics/subscriptions | 否 |  否 |
| namespaces/topics/subscriptions/rules | 否 |  否 |
| premiumMessagingRegions | 否 |  否 |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| clusters | 是 | 是 |
| clusters/applications | 否 |  否 |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| 服务 | 是 | 是 |
| gateways | 是 | 是 |
| networks | 是 | 是 |
| 机密 | 是 | 是 |
| volumes | 是 | 是 |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| SignalR | 是 | 是 |

## <a name="microsoftsolutions"></a>Microsoft.Solutions
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| applianceDefinitions | 是 | 是 |
| appliances | 是 | 是 |
| applicationDefinitions | 是 | 是 |
| 服务 | 是 | 是 |
| jitRequests | 是 | 是 |

## <a name="microsoftsql"></a>Microsoft.SQL
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| managedInstances | 是 | 是 |
| managedInstances/databases | 是（见下方备注） | 是 |
| managedInstances/databases/backupShortTermRetentionPolicies | 否 | 否 |
| managedInstances/databases/schemas/tables/columns/sensitivityLabels | 否 | 否 |
| managedInstances/databases/vulnerabilityAssessments | 否 | 否 |
| managedInstances/databases/vulnerabilityAssessments/rules/baselines | 否 | 否 |
| managedInstances/encryptionProtector | 否 | 否 |
| managedInstances/keys | 否 | 否 |
| managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies | 否 | 否 |
| managedInstances/vulnerabilityAssessments | 否 | 否 |
| servers | 是 | 是 |
| servers/administrators | 否 |  否 |
| servers/communicationLinks | 否 |  否 |
| servers/databases | 是（见下方备注） | 是 |
| servers/encryptionProtector | 否 |  否 |
| servers/firewallRules | 否 |  否 |
| servers/keys | 否 |  否 |
| servers/restorableDroppedDatabases | 否 |  否 |
| servers/serviceobjectives | 否 |  否 |
| servers/tdeCertificates | 否 |  否 |

> [!NOTE]
> Master 数据库不支持标记，但其他数据库（包括 Azure SQL 数据仓库数据库）支持标记。 Azure SQL 数据仓库数据库必须处于活动（而非暂停）状态。


## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| SqlVirtualMachineGroups | 是 | 是 |
| SqlVirtualMachineGroups/AvailabilityGroupListeners | 否 |  否 |
| SqlVirtualMachines | 是 | 是 |

## <a name="microsoftstorage"></a>Microsoft.Storage
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| storageAccounts | 是 | 是 |
| storageAccounts/blobServices | 否 |  否 |
| storageAccounts/fileServices | 否 |  否 |
| storageAccounts/queueServices | 否 |  否 |
| storageAccounts/services | 否 |  否 |
| storageAccounts/tableServices | 否 |  否 |
| usages | 否 |  否 |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| storageSyncServices | 是 | 是 |
| storageSyncServices/registeredServers | 否 |  否 |
| storageSyncServices/syncGroups | 否 |  否 |
| storageSyncServices/syncGroups/cloudEndpoints | 否 |  否 |
| storageSyncServices/syncGroups/serverEndpoints | 否 |  否 |
| storageSyncServices/workflows | 否 |  否 |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| managers | 是 | 是 |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| streamingjobs | 是（见下方备注） | 是 |
| streamingjobs/diagnosticSettings | 否 |  否 |

> [!NOTE]
> Streamingjobs 运行时无法添加标记。 停止要添加标记的资源。

## <a name="microsoftsubscription"></a>Microsoft.Subscription
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| CreateSubscription | 否 |  否 |
| SubscriptionDefinitions | 否 |  否 |
| SubscriptionOperations | 否 |  否 |

## <a name="microsoftsupport"></a>microsoft.support
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| supporttickets | 否 |  否 |

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| providerRegistrations | 是 | 是 |
| 资源 | 是 | 是 |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| environments | 是 | 否 |
| environments/accessPolicies | 否 |  否 |
| environments/eventsources | 是 | 否 |
| environments/referenceDataSets | 是 | 否 |

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| 帐户 | 是 | 是 |
| account/extension | 是 | 是 |
| account/project | 是 | 是 |

## <a name="microsoftweb"></a>Microsoft.Web
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| apiManagementAccounts | 否 |  否 |
| apiManagementAccounts/apiAcls | 否 |  否 |
| apiManagementAccounts/apis | 否 |  否 |
| apiManagementAccounts/apis/apiAcls | 否 |  否 |
| apiManagementAccounts/apis/connectionAcls | 否 |  否 |
| apiManagementAccounts/apis/connections | 否 |  否 |
| apiManagementAccounts/apis/connections/connectionAcls | 否 |  否 |
| apiManagementAccounts/apis/localizedDefinitions | 否 |  否 |
| apiManagementAccounts/connectionAcls | 否 |  否 |
| apiManagementAccounts/connections | 否 |  否 |
| billingMeters | 否 |  否 |
| certificates | 是 | 是 |
| connectionGateways | 是 | 是 |
| connections | 是 | 是 |
| customApis | 是 | 是 |
| deletedSites | 否 |  否 |
| functions | 否 |  否 |
| hostingEnvironments | 是 | 是 |
| hostingEnvironments/multiRolePools | 否 |  否 |
| hostingEnvironments/multiRolePools/instances | 否 |  否 |
| hostingEnvironments/workerPools | 否 |  否 |
| hostingEnvironments/workerPools/instances | 否 |  否 |
| publishingUsers | 否 |  否 |
| 建议 | 否 |  否 |
| resourceHealthMetadata | 否 |  否 |
| runtimes | 否 |  否 |
| serverFarms | 是 | 是 |
| serverFarms/workers | 否 |  否 |
| sites | 是 | 是 |
| sites/domainOwnershipIdentifiers | 否 |  否 |
| sites/hostNameBindings | 否 |  否 |
| sites/instances | 否 |  否 |
| sites/instances/extensions | 否 |  否 |
| sites/premieraddons | 是 | 是 |
| sites/recommendations | 否 |  否 |
| sites/resourceHealthMetadata | 否 |  否 |
| sites/slots | 是 | 是 |
| sites/slots/hostNameBindings | 否 |  否 |
| sites/slots/instances | 否 |  否 |
| sites/slots/instances/extensions | 否 |  否 |
| sourceControls | 否 |  否 |
| validate | 否 |  否 |
| verifyHostingEnvironmentVnet | 否 |  否 |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| diagnosticSettings | 否 |  否 |
| diagnosticSettingsCategories | 否 |  否 |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| DeviceServices | 是 | 是 |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor
| 资源类型 | 支持标记 | 成本报表中的标记 |
| ------------- | ----------- | ----------- |
| components | 否 |  否 |
| componentsSummary | 否 |  否 |
| monitorInstances | 否 |  否 |
| monitorInstancesSummary | 否 |  否 |
| monitors | 否 |  否 |
| notificationSettings | 否 |  否 |

## <a name="next-steps"></a>后续步骤
若要了解如何将标记应用于资源，请参见[使用标记来组织 Azure 资源](resource-group-using-tags.md)。
