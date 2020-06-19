---
title: 资源类型的移动操作支持
description: 列出了可移动到新资源组或订阅的 Azure 资源类型。
ms.topic: conceptual
ms.date: 05/21/2020
ms.openlocfilehash: 4f2ed7f1cb24b9896b533fb5d18ac4e57db48e2c
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/22/2020
ms.locfileid: "83780333"
---
# <a name="move-operation-support-for-resources"></a>支持移动操作的资源

本文列出了 Azure 资源类型是否支持移动操作。 它还提供有关移动资源时要考虑的特殊情况的信息。

跳转到资源提供程序命名空间：
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [microsoft.aadiam](#microsoftaadiam)
> - [Microsoft.Addons](#microsoftaddons)
> - [Microsoft.ADHybridHealthService](#microsoftadhybridhealthservice)
> - [Microsoft.Advisor](#microsoftadvisor)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft.AppConfiguration](#microsoftappconfiguration)
> - [Microsoft.AppPlatform](#microsoftappplatform)
> - [Microsoft.AppService](#microsoftappservice)
> - [Microsoft.Attestation](#microsoftattestation)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft.AVS](#microsoftavs)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft.AzureData](#microsoftazuredata)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.AzureStackHCI](#microsoftazurestackhci)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.BatchAI](#microsoftbatchai)
> - [Microsoft.Billing](#microsoftbilling)
> - [Microsoft.BingMaps](#microsoftbingmaps)
> - [Microsoft.BizTalkServices](#microsoftbiztalkservices)
> - [Microsoft.Blockchain](#microsoftblockchain)
> - [Microsoft.BlockchainTokens](#microsoftblockchaintokens)
> - [Microsoft.Blueprint](#microsoftblueprint)
> - [Microsoft.BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Microsoft.Capacity](#microsoftcapacity)
> - [Microsoft.Cdn](#microsoftcdn)
> - [Microsoft.CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.ChangeAnalysis](#microsoftchangeanalysis)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft.ClassicInfrastructureMigrate](#microsoftclassicinfrastructuremigrate)
> - [Microsoft.ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [Microsoft.ClassicSubscription](#microsoftclassicsubscription)
> - [Microsoft.Cognition](#microsoftcognition)
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft.Commerce](#microsoftcommerce)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft.Consumption](#microsoftconsumption)
> - [Microsoft.Container](#microsoftcontainer)
> - [Microsoft.ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft.ContentModerator](#microsoftcontentmoderator)
> - [Microsoft.CortanaAnalytics](#microsoftcortanaanalytics)
> - [Microsoft.CostManagement](#microsoftcostmanagement)
> - [Microsoft.CostManagementExports](#microsoftcostmanagementexports)
> - [Microsoft.CustomerInsights](#microsoftcustomerinsights)
> - [Microsoft.CustomerLockbox](#microsoftcustomerlockbox)
> - [Microsoft.CustomProviders](#microsoftcustomproviders)
> - [Microsoft.DataBox](#microsoftdatabox)
> - [Microsoft.DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft.Databricks](#microsoftdatabricks)
> - [Microsoft.DataCatalog](#microsoftdatacatalog)
> - [Microsoft.DataConnect](#microsoftdataconnect)
> - [Microsoft.DataExchange](#microsoftdataexchange)
> - [Microsoft.DataFactory](#microsoftdatafactory)
> - [Microsoft.DataLake](#microsoftdatalake)
> - [Microsoft.DataLakeAnalytics](#microsoftdatalakeanalytics)
> - [Microsoft.DataLakeStore](#microsoftdatalakestore)
> - [Microsoft.DataMigration](#microsoftdatamigration)
> - [Microsoft.DataProtection](#microsoftdataprotection)
> - [Microsoft.DataShare](#microsoftdatashare)
> - [Microsoft.DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft.DBforMySQL](#microsoftdbformysql)
> - [Microsoft.DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft.DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft.DesktopVirtualization](#microsoftdesktopvirtualization)
> - [Microsoft.Devices](#microsoftdevices)
> - [Microsoft.DevOps](#microsoftdevops)
> - [Microsoft.DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [Microsoft.DigitalTwins](#microsoftdigitaltwins)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [Microsoft.EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft.Experimentation](#microsoftexperimentation)
> - [Microsoft.Falcon](#microsoftfalcon)
> - [Microsoft.Features](#microsoftfeatures)
> - [Microsoft.Genomics](#microsoftgenomics)
> - [Microsoft.GuestConfiguration](#microsoftguestconfiguration)
> - [Microsoft.HanaOnAzure](#microsofthanaonazure)
> - [Microsoft.HardwareSecurityModules](#microsofthardwaresecuritymodules)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [Microsoft.HealthcareApis](#microsofthealthcareapis)
> - [Microsoft.HybridCompute](#microsofthybridcompute)
> - [Microsoft.HybridData](#microsofthybriddata)
> - [Microsoft.Hydra](#microsofthydra)
> - [Microsoft.ImportExport](#microsoftimportexport)
> - [microsoft.insights](#microsoftinsights)
> - [Microsoft.IoTCentral](#microsoftiotcentral)
> - [Microsoft.IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Microsoft.Kubernetes](#microsoftkubernetes)
> - [Microsoft.KubernetesConfiguration](#microsoftkubernetesconfiguration)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft.LabServices](#microsoftlabservices)
> - [Microsoft.LocationBasedServices](#microsoftlocationbasedservices)
> - [Microsoft.LocationServices](#microsoftlocationservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [Microsoft.MachineLearning](#microsoftmachinelearning)
> - [Microsoft.MachineLearningCompute](#microsoftmachinelearningcompute)
> - [Microsoft.MachineLearningExperimentation](#microsoftmachinelearningexperimentation)
> - [Microsoft.MachineLearningModelManagement](#microsoftmachinelearningmodelmanagement)
> - [Microsoft.MachineLearningOperationalization](#microsoftmachinelearningoperationalization)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft.Maintenance](#microsoftmaintenance)
> - [Microsoft.ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft.ManagedNetwork](#microsoftmanagednetwork)
> - [Microsoft.ManagedServices](#microsoftmanagedservices)
> - [Microsoft.Management](#microsoftmanagement)
> - [Microsoft.Maps](#microsoftmaps)
> - [Microsoft.Marketplace](#microsoftmarketplace)
> - [Microsoft.MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft.MarketplaceOrdering](#microsoftmarketplaceordering)
> - [Microsoft.Media](#microsoftmedia)
> - [Microsoft.Microservices4Spring](#microsoftmicroservices4spring)
> - [Microsoft.Migrate](#microsoftmigrate)
> - [Microsoft.MixedReality](#microsoftmixedreality)
> - [Microsoft.NetApp](#microsoftnetapp)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft.ObjectStore](#microsoftobjectstore)
> - [Microsoft.OffAzure](#microsoftoffazure)
> - [Microsoft.OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft.OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft.Peering](#microsoftpeering)
> - [Microsoft.PolicyInsights](#microsoftpolicyinsights)
> - [Microsoft.Portal](#microsoftportal)
> - [Microsoft.PortalSdk](#microsoftportalsdk)
> - [Microsoft.PowerBI](#microsoftpowerbi)
> - [Microsoft.PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft.ProjectBabylon](#microsoftprojectbabylon)
> - [Microsoft.ProjectOxford](#microsoftprojectoxford)
> - [Microsoft.ProviderHub](#microsoftproviderhub)
> - [Microsoft.Quantum](#microsoftquantum)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.RedHatOpenShift](#microsoftredhatopenshift)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft.ResourceGraph](#microsoftresourcegraph)
> - [Microsoft.ResourceHealth](#microsoftresourcehealth)
> - [Microsoft.Resources](#microsoftresources)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft.SecurityInsights](#microsoftsecurityinsights)
> - [Microsoft.SerialConsole](#microsoftserialconsole)
> - [Microsoft.ServerManagement](#microsoftservermanagement)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft.Services](#microsoftservices)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [Microsoft.SoftwarePlan](#microsoftsoftwareplan)
> - [Microsoft.Solutions](#microsoftsolutions)
> - [Microsoft.Sql](#microsoftsql)
> - [Microsoft.SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.SqlVM](#microsoftsqlvm)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft.StorageCache](#microsoftstoragecache)
> - [Microsoft.StorageSync](#microsoftstoragesync)
> - [Microsoft.StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft.StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft.StreamAnalyticsExplorer](#microsoftstreamanalyticsexplorer)
> - [Microsoft.Subscription](#microsoftsubscription)
> - [microsoft.support](#microsoftsupport)
> - [Microsoft.Synapse](#microsoftsynapse)
> - [Microsoft.TerraformOSS](#microsoftterraformoss)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft.Token](#microsofttoken)
> - [Microsoft.VirtualMachineImages](#microsoftvirtualmachineimages)
> - [microsoft.visualstudio](#microsoftvisualstudio)
> - [Microsoft.VMware](#microsoftvmware)
> - [Microsoft.VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft.VnfManager](#microsoftvnfmanager)
> - [Microsoft.VSOnline](#microsoftvsonline)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft.WindowsESU](#microsoftwindowsesu)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Microsoft.WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | domainservices | 否 | 否 |
> | domainservices / oucontainer | 否 | 否 |
> | 位置 | 否 | 否 |
> | locations / operationresults | 否 | 否 |
> | 操作 | 否 | 否 |

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | privatelinkforazuread | 否 | 否 |
> | tenants | 否 | 否 |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | operationresults | 否 | 否 |
> | 操作 | 否 | 否 |
> | supportproviders | 否 | 否 |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | aadsupportcases | 否 | 否 |
> | addsservices | 否 | 否 |
> | 代理 | 否 | 否 |
> | anonymousapiusers | 否 | 否 |
> | 配置 | 否 | 否 |
> | 日志 | 否 | 否 |
> | 操作 | 否 | 否 |
> | 报表 | 否 | 否 |
> | servicehealthmetrics | 否 | 否 |
> | services | 否 | 否 |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | 配置 | 否 | 否 |
> | generaterecommendations | 否 | 否 |
> | metadata | 否 | 否 |
> | 操作 | 否 | 否 |
> | 建议 | 否 | 否 |
> | 禁止显示 | 否 | 否 |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | actionrules | 是 | 是 |
> | alerts | 否 | 否 |
> | alertslist | 否 | 否 |
> | alertsmetadata | 否 | 否 |
> | alertssummary | 否 | 否 |
> | alertssummarylist | 否 | 否 |
> | 操作 | 否 | 否 |
> | smartdetectoralertrules | 是 | 是 |
> | smartgroups | 否 | 否 |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | 位置 | 否 | 否 |
> | locations / checknameavailability | 否 | 否 |
> | locations / operationresults | 否 | 否 |
> | locations / operationstatuses | 否 | 否 |
> | 操作 | 否 | 否 |
> | servers | 是 | 是 |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | checkfeedbackrequired | 否 | 否 |
> | checknameavailability | 否 | 否 |
> | checkservicenameavailability | 否 | 否 |
> | 操作 | 否 | 否 |
> | reportfeedback | 否 | 否 |
> | 服务 | 是 | 是 |
> | validateservicename | 否 | 否 |

> [!IMPORTANT]
> 无法移动设置为消耗 SKU 的 API 管理服务。

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | checknameavailability | 否 | 否 |
> | configurationstores | 是 | 是 |
> | configurationstores / eventgridfilters | 否 | 否 |
> | 位置 | 否 | 否 |
> | locations / operationsstatus | 否 | 否 |
> | 操作 | 否 | 否 |

## <a name="microsoftappplatform"></a>Microsoft.AppPlatform

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | 位置 | 否 | 否 |
> | locations / checknameavailability | 否 | 否 |
> | locations / operationresults | 否 | 否 |
> | locations / operationstatus | 否 | 否 |
> | 操作 | 否 | 否 |
> | spring | 是 | 是 |

## <a name="microsoftappservice"></a>Microsoft.AppService

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | apiapps | 否 | 否 |
> | appidentities | 否 | 否 |
> | gateways | 否 | 否 |

> [!IMPORTANT]
> 请参阅[应用服务移动指南](./move-limitations/app-service-move-limitations.md)。

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | attestationproviders | 是 | 是 |
> | 操作 | 否 | 否 |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | checkaccess | 否 | 否 |
> | classicadministrators | 否 | 否 |
> | dataaliases | 否 | 否 |
> | denyassignments | 否 | 否 |
> | elevateaccess | 否 | 否 |
> | findorphanroleassignments | 否 | 否 |
> | 锁定 | 否 | 否 |
> | 操作 | 否 | 否 |
> | 权限 | 否 | 否 |
> | policyassignments | 否 | 否 |
> | policydefinitions | 否 | 否 |
> | policysetdefinitions | 否 | 否 |
> | provideroperations | 否 | 否 |
> | roleassignments | 否 | 否 |
> | roleassignmentsusagemetrics | 否 | 否 |
> | roledefinitions | 否 | 否 |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | automationaccounts | 是 | 是 |
> | automationaccounts / configurations | 是 | 是 |
> | automationaccounts / jobs | 否 | 否 |
> | automationaccounts / privateendpointconnectionproxies | 否 | 否 |
> | automationaccounts / privateendpointconnections | 否 | 否 |
> | automationaccounts / privatelinkresources | 否 | 否 |
> | automationaccounts / runbooks | 是 | 是 |
> | automationaccounts / softwareupdateconfigurations | 否 | 否 |
> | automationaccounts / webhooks | 否 | 否 |
> | 操作 | 否 | 否 |

> [!IMPORTANT]
> Runbook 必须与自动化帐户存在于同一资源组中。

## <a name="microsoftavs"></a>Microsoft.AVS

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | 位置 | 否 | 否 |
> | locations / checkquotaavailability | 否 | 否 |
> | 操作 | 否 | 否 |
> | privateclouds | 是 | 是 |
> | privateclouds / clusters | 否 | 否 |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | b2cdirectories | 是 | 是 |
> | b2ctenants | 否 | 否 |
> | checknameavailability | 否 | 否 |
> | 操作 | 否 | 否 |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | datacontrollers | 否 | 否 |
> | hybriddatamanagers | 否 | 否 |
> | 操作 | 否 | 否 |
> | postgresinstances | 否 | 否 |
> | sqlinstances | 否 | 否 |
> | sqlmanagedinstances | 否 | 否 |
> | sqlserverinstances | 否 | 否 |
> | sqlserverregistrations | 是 | 是 |
> | sqlserverregistrations / sqlservers | 否 | 否 |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | cloudmanifestfiles | 否 | 否 |
> | 操作 | 否 | 否 |
> | registrations | 是 | 是 |
> | registrations / customersubscriptions | 否 | 否 |
> | registrations / products | 否 | 否 |

## <a name="microsoftazurestackhci"></a>Microsoft.AzureStackHCI

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | clusters | 否 | 否 |
> | 操作 | 否 | 否 |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | batchaccounts | 是 | 是 |
> | 位置 | 否 | 否 |
> | locations / accountoperationresults | 否 | 否 |
> | locations / checknameavailability | 否 | 否 |
> | locations / quotas | 否 | 否 |
> | 操作 | 否 | 否 |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | clusters | 否 | 否 |
> | fileservers | 否 | 否 |
> | jobs | 否 | 否 |
> | 操作 | 否 | 否 |
> | workspaces | 否 | 否 |
> | workspaces / clusters | 否 | 否 |
> | workspaces / experiments | 否 | 否 |
> | workspaces / experiments / jobs | 否 | 否 |
> | workspaces / fileservers | 否 | 否 |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | billingaccounts | 否 | 否 |
> | billingaccounts / agreements | 否 | 否 |
> | billingaccounts / billingpermissions | 否 | 否 |
> | billingaccounts / billingprofiles | 否 | 否 |
> | billingaccounts / billingprofiles / availablebalance | 否 | 否 |
> | billingaccounts / billingprofiles / billingpermissions | 否 | 否 |
> | billingaccounts / billingprofiles / billingroleassignments | 否 | 否 |
> | billingaccounts / billingprofiles / billingroledefinitions | 否 | 否 |
> | billingaccounts / billingprofiles / billingsubscriptions | 否 | 否 |
> | billingaccounts / billingprofiles / createbillingroleassignment | 否 | 否 |
> | billingaccounts / billingprofiles / customers | 否 | 否 |
> | billingaccounts / billingprofiles / instructions | 否 | 否 |
> | billingaccounts / billingprofiles / invoices | 否 | 否 |
> | billingaccounts / billingprofiles / invoices / pricesheet | 否 | 否 |
> | billingaccounts / billingprofiles / invoices / transactions | 否 | 否 |
> | billingaccounts / billingprofiles / invoicesections | 否 | 否 |
> | billingaccounts / billingprofiles / invoicesections / billingpermissions | 否 | 否 |
> | billingaccounts / billingprofiles / invoicesections / billingroleassignments | 否 | 否 |
> | billingaccounts / billingprofiles / invoicesections / billingroledefinitions | 否 | 否 |
> | billingaccounts / billingprofiles / invoicesections / billingsubscriptions | 否 | 否 |
> | billingaccounts / billingprofiles / invoicesections / createbillingroleassignment | 否 | 否 |
> | billingaccounts / billingprofiles / invoicesections / initiatetransfer | 否 | 否 |
> | billingaccounts / billingprofiles / invoicesections / products | 否 | 否 |
> | billingaccounts / billingprofiles / invoicesections / products / transfer | 否 | 否 |
> | billingaccounts / billingprofiles / invoicesections / products / updateautorenew | 否 | 否 |
> | billingaccounts / billingprofiles / invoicesections / transactions | 否 | 否 |
> | billingaccounts / billingprofiles / invoicesections / transfers | 否 | 否 |
> | billingaccounts / billingprofiles / patchoperations | 否 | 否 |
> | billingaccounts / billingprofiles / paymentmethods | 否 | 否 |
> | billingaccounts / billingprofiles / policies | 否 | 否 |
> | billingaccounts / billingprofiles / pricesheet | 否 | 否 |
> | billingaccounts / billingprofiles / pricesheetdownloadoperations | 否 | 否 |
> | billingaccounts / billingprofiles / products | 否 | 否 |
> | billingaccounts / billingprofiles / transactions | 否 | 否 |
> | billingaccounts / billingroleassignments | 否 | 否 |
> | billingaccounts / billingroledefinitions | 否 | 否 |
> | billingaccounts / billingsubscriptions | 否 | 否 |
> | billingaccounts / billingsubscriptions / invoices | 否 | 否 |
> | billingaccounts / createbillingroleassignment | 否 | 否 |
> | billingaccounts / createinvoicesectionoperations | 否 | 否 |
> | billingaccounts / customers | 否 | 否 |
> | billingaccounts / customers / billingpermissions | 否 | 否 |
> | billingaccounts / customers / billingsubscriptions | 否 | 否 |
> | billingaccounts / customers / initiatetransfer | 否 | 否 |
> | billingaccounts / customers / policies | 否 | 否 |
> | billingaccounts / customers / products | 否 | 否 |
> | billingaccounts / customers / transactions | 否 | 否 |
> | billingaccounts / customers / transfers | 否 | 否 |
> | billingaccounts / departments | 否 | 否 |
> | billingaccounts / enrollmentaccounts | 否 | 否 |
> | billingaccounts / invoices | 否 | 否 |
> | billingaccounts / invoicesections | 否 | 否 |
> | billingaccounts / invoicesections / billingsubscriptionmoveoperations | 否 | 否 |
> | billingaccounts / invoicesections / billingsubscriptions | 否 | 否 |
> | billingaccounts / invoicesections / billingsubscriptions / transfer | 否 | 否 |
> | billingaccounts / invoicesections / elevate | 否 | 否 |
> | billingaccounts / invoicesections / initiatetransfer | 否 | 否 |
> | billingaccounts / invoicesections / patchoperations | 否 | 否 |
> | billingaccounts / invoicesections / productmoveoperations | 否 | 否 |
> | billingaccounts / invoicesections / products | 否 | 否 |
> | billingaccounts / invoicesections / products / transfer | 否 | 否 |
> | billingaccounts / invoicesections / products / updateautorenew | 否 | 否 |
> | billingaccounts / invoicesections / producttransfersresults | 否 | 否 |
> | billingaccounts / invoicesections / transactions | 否 | 否 |
> | billingaccounts / invoicesections / transfers | 否 | 否 |
> | billingaccounts / lineofcredit | 否 | 否 |
> | billingaccounts / listinvoicesectionswithcreatesubscriptionpermission | 否 | 否 |
> | billingaccounts / operationresults | 否 | 否 |
> | billingaccounts / patchoperations | 否 | 否 |
> | billingaccounts / paymentmethods | 否 | 否 |
> | billingaccounts / products | 否 | 否 |
> | billingaccounts / transactions | 否 | 否 |
> | billingperiods | 否 | 否 |
> | billingpermissions | 否 | 否 |
> | billingproperty | 否 | 否 |
> | billingroleassignments | 否 | 否 |
> | billingroledefinitions | 否 | 否 |
> | createbillingroleassignment | 否 | 否 |
> | departments | 否 | 否 |
> | enrollmentaccounts | 否 | 否 |
> | invoices | 否 | 否 |
> | operationresults | 否 | 否 |
> | 操作 | 否 | 否 |
> | operationstatus | 否 | 否 |
> | transfers | 否 | 否 |
> | transfers / accepttransfer | 否 | 否 |
> | transfers / declinetransfer | 否 | 否 |
> | transfers / operationstatus | 否 | 否 |
> | transfers / validatetransfer | 否 | 否 |
> | validateaddress | 否 | 否 |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | listcommunicationpreference | 否 | 否 |
> | mapapis | 否 | 否 |
> | 操作 | 否 | 否 |
> | updatecommunicationpreference | 否 | 否 |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | biztalk | 否 | 否 |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | blockchainmembers | 否 | 否 |
> | cordamembers | 否 | 否 |
> | 位置 | 否 | 否 |
> | locations / blockchainmemberoperationresults | 否 | 否 |
> | locations / checknameavailability | 否 | 否 |
> | locations / listconsortiums | 否 | 否 |
> | locations / watcheroperationresults | 否 | 否 |
> | 操作 | 否 | 否 |
> | watchers | 否 | 否 |

## <a name="microsoftblockchaintokens"></a>Microsoft.BlockchainTokens

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | 操作 | 否 | 否 |
> | tokenservices | 否 | 否 |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | blueprintassignments | 否 | 否 |
> | blueprintassignments / assignmentoperations | 否 | 否 |
> | blueprintassignments / operations | 否 | 否 |
> | blueprints | 否 | 否 |
> | blueprints / artifacts | 否 | 否 |
> | blueprints / versions | 否 | 否 |
> | blueprints / versions / artifacts | 否 | 否 |
> | 操作 | 否 | 否 |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | botservices | 是 | 是 |
> | botservices / channels | 否 | 否 |
> | botservices / connections | 否 | 否 |
> | checknameavailability | 否 | 否 |
> | listauthserviceproviders | 否 | 否 |
> | 操作 | 否 | 否 |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | checknameavailability | 否 | 否 |
> | 位置 | 否 | 否 |
> | locations / operationresults | 否 | 否 |
> | locations / operationsstatus | 否 | 否 |
> | 操作 | 否 | 否 |
> | redis | 是 | 是 |
> | redis / privateendpointconnections | 否 | 否 |
> | redis / privatelinkresources | 否 | 否 |
> | redisenterprise | 否 | 否 |

> [!IMPORTANT]
> 如果 Azure Redis 缓存实例配置了虚拟网络，则实例无法被移动到其他订阅。 请参阅[网络移动限制](./move-limitations/networking-move-limitations.md)。

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | appliedreservations | 否 | 否 |
> | calculateexchange | 否 | 否 |
> | calculateprice | 否 | 否 |
> | calculatepurchaseprice | 否 | 否 |
> | catalogs | 否 | 否 |
> | checkoffers | 否 | 否 |
> | checkpurchasestatus | 否 | 否 |
> | checkscopes | 否 | 否 |
> | commercialreservationorders | 否 | 否 |
> | 交易所 | 否 | 否 |
> | listbenefits | 否 | 否 |
> | 操作 | 否 | 否 |
> | placepurchaseorder | 否 | 否 |
> | reservationorders | 否 | 否 |
> | reservationorders / availablescopes | 否 | 否 |
> | reservationorders / calculaterefund | 否 | 否 |
> | reservationorders / merge | 否 | 否 |
> | reservationorders / reservations | 否 | 否 |
> | reservationorders / reservations / availablescopes | 否 | 否 |
> | reservationorders / reservations / revisions | 否 | 否 |
> | reservationorders / return | 否 | 否 |
> | reservationorders / split | 否 | 否 |
> | reservationorders / swap | 否 | 否 |
> | reservations | 否 | 否 |
> | 资源 | 否 | 否 |
> | validatereservationorder | 否 | 否 |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | cdnwebapplicationfirewallmanagedrulesets | 否 | 否 |
> | cdnwebapplicationfirewallpolicies | 是 | 是 |
> | checknameavailability | 否 | 否 |
> | checkresourceusage | 否 | 否 |
> | edgenodes | 否 | 否 |
> | operationresults | 否 | 否 |
> | operationresults / profileresults | 否 | 否 |
> | operationresults / profileresults / endpointresults | 否 | 否 |
> | operationresults / profileresults / endpointresults / customdomainresults | 否 | 否 |
> | operationresults / profileresults / endpointresults / origingroupresults | 否 | 否 |
> | operationresults / profileresults / endpointresults / originresults | 否 | 否 |
> | 操作 | 否 | 否 |
> | 配置文件 | 是 | 是 |
> | profiles / endpoints | 是 | 是 |
> | profiles / endpoints / customdomains | 否 | 否 |
> | profiles / endpoints / origingroups | 否 | 否 |
> | profiles / endpoints / origins | 否 | 否 |
> | validateprobe | 否 | 否 |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | certificateorders | 是 | 是 |
> | certificateorders / certificates | 否 | 否 |
> | 操作 | 否 | 否 |
> | validatecertificateregistrationinformation | 否 | 否 |

> [!IMPORTANT]
> 请参阅[应用服务移动指南](./move-limitations/app-service-move-limitations.md)。

## <a name="microsoftchangeanalysis"></a>Microsoft.ChangeAnalysis

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | 操作 | 否 | 否 |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | capabilities | 否 | 否 |
> | checkdomainnameavailability | 否 | 否 |
> | domainnames | 是 | 否 |
> | domainnames / capabilities | 否 | 否 |
> | domainnames / internalloadbalancers | 否 | 否 |
> | domainnames / servicecertificates | 否 | 否 |
> | domainnames / slots | 否 | 否 |
> | domainnames / slots / roles | 否 | 否 |
> | domainnames / slots / roles / metricdefinitions | 否 | 否 |
> | domainnames / slots / roles / metrics | 否 | 否 |
> | movesubscriptionresources | 否 | 否 |
> | operatingsystemfamilies | 否 | 否 |
> | operatingsystems | 否 | 否 |
> | 操作 | 否 | 否 |
> | operationstatuses | 否 | 否 |
> | quotas | 否 | 否 |
> | resourcetypes | 否 | 否 |
> | validatesubscriptionmoveavailability | 否 | 否 |
> | virtualmachines | 是 | 否 |
> | virtualmachines / diagnosticsettings | 否 | 否 |
> | virtualmachines / metricdefinitions | 否 | 否 |
> | virtualmachines / metrics | 否 | 否 |

> [!IMPORTANT]
> 请参阅[经典部署模型移动指南](./move-limitations/classic-model-move-limitations.md)。 经典部署资源可以使用特定于该方案的操作跨订阅移动。

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | classicinfrastructureresources | 否 | 否 |

> [!IMPORTANT]
> 请参阅[经典部署模型移动指南](./move-limitations/classic-model-move-limitations.md)。 经典部署资源可以使用特定于该方案的操作跨订阅移动。

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | capabilities | 否 | 否 |
> | expressroutecrossconnections | 否 | 否 |
> | expressroutecrossconnections / peerings | 否 | 否 |
> | gatewaysupporteddevices | 否 | 否 |
> | networksecuritygroups | 否 | 否 |
> | 操作 | 否 | 否 |
> | quotas | 否 | 否 |
> | reservedips | 否 | 否 |
> | virtualnetworks | 否 | 否 |
> | virtualnetworks / remotevirtualnetworkpeeringproxies | 否 | 否 |
> | virtualnetworks / virtualnetworkpeerings | 否 | 否 |

> [!IMPORTANT]
> 请参阅[经典部署模型移动指南](./move-limitations/classic-model-move-limitations.md)。 经典部署资源可以使用特定于该方案的操作跨订阅移动。

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | capabilities | 否 | 否 |
> | checkstorageaccountavailability | 否 | 否 |
> | disks | 否 | 否 |
> | images | 否 | 否 |
> | 操作 | 否 | 否 |
> | osimages | 否 | 否 |
> | osplatformimages | 否 | 否 |
> | publicimages | 否 | 否 |
> | quotas | 否 | 否 |
> | storageaccounts | 是 | 否 |
> | storageaccounts / blobservices | 否 | 否 |
> | storageaccounts / fileservices | 否 | 否 |
> | storageaccounts / metricdefinitions | 否 | 否 |
> | storageaccounts / metrics | 否 | 否 |
> | storageaccounts / queueservices | 否 | 否 |
> | storageaccounts / services | 否 | 否 |
> | storageaccounts / services / diagnosticsettings | 否 | 否 |
> | storageaccounts / services / metricdefinitions | 否 | 否 |
> | storageaccounts / services / metrics | 否 | 否 |
> | storageaccounts / tableservices | 否 | 否 |
> | storageaccounts / vmimages | 否 | 否 |
> | vmimages | 否 | 否 |

> [!IMPORTANT]
> 请参阅[经典部署模型移动指南](./move-limitations/classic-model-move-limitations.md)。 经典部署资源可以使用特定于该方案的操作跨订阅移动。

## <a name="microsoftclassicsubscription"></a>Microsoft.ClassicSubscription

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | 操作 | 否 | 否 |

> [!IMPORTANT]
> 请参阅[经典部署模型移动指南](./move-limitations/classic-model-move-limitations.md)。 经典部署资源可以使用特定于该方案的操作跨订阅移动。

## <a name="microsoftcognition"></a>Microsoft.Cognition

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | syntheticsaccounts | 否 | 否 |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | accounts | 是 | 是 |
> | checkdomainavailability | 否 | 否 |
> | 位置 | 否 | 否 |
> | locations / checkskuavailability | 否 | 否 |
> | locations / deletevirtualnetworkorsubnets | 否 | 否 |
> | locations / operationresults | 否 | 否 |
> | 操作 | 否 | 否 |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | 操作 | 否 | 否 |
> | ratecard | 否 | 否 |
> | usageaggregates | 否 | 否 |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | availabilitysets | 是 | 是 |
> | diskaccesses | 否 | 否 |
> | diskencryptionsets | 否 | 否 |
> | disks | 是 | 是 |
> | galleries | 否 | 否 |
> | galleries / images | 否 | 否 |
> | galleries / images / versions | 否 | 否 |
> | hostgroups | 否 | 否 |
> | hostgroups / hosts | 否 | 否 |
> | images | 是 | 是 |
> | 位置 | 否 | 否 |
> | locations / artifactpublishers | 否 | 否 |
> | locations / capsoperations | 否 | 否 |
> | locations / diskoperations | 否 | 否 |
> | locations / loganalytics | 否 | 否 |
> | locations / operations | 否 | 否 |
> | locations / publishers | 否 | 否 |
> | locations / runcommands | 否 | 否 |
> | locations / usages | 否 | 否 |
> | locations / virtualmachines | 否 | 否 |
> | locations / vmsizes | 否 | 否 |
> | locations / vsmoperations | 否 | 否 |
> | 操作 | 否 | 否 |
> | proximityplacementgroups | 是 | 是 |
> | restorepointcollections | 否 | 否 |
> | restorepointcollections / restorepoints | 否 | 否 |
> | sharedvmextensions | 否 | 否 |
> | sharedvmimages | 否 | 否 |
> | sharedvmimages / versions | 否 | 否 |
> | snapshots | 是 | 是 |
> | sshpublickeys | 否 | 否 |
> | virtualmachines | 是 | 是 |
> | virtualmachines / extensions | 是 | 是 |
> | virtualmachines / metricdefinitions | 否 | 否 |
> | virtualmachines / runcommands | 否 | 否 |
> | virtualmachinescalesets | 是 | 是 |
> | virtualmachinescalesets / extensions | 否 | 否 |
> | virtualmachinescalesets / networkinterfaces | 否 | 否 |
> | virtualmachinescalesets / publicipaddresses | 否 | 否 |
> | virtualmachinescalesets / virtualmachines | 否 | 否 |
> | virtualmachinescalesets / virtualmachines / networkinterfaces | 否 | 否 |

> [!IMPORTANT]
> 请参阅[虚拟机移动指南](./move-limitations/virtual-machines-move-limitations.md)。

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | aggregatedcost | 否 | 否 |
> | balances | 否 | 否 |
> | budgets | 否 | 否 |
> | charges | 否 | 否 |
> | costtags | 否 | 否 |
> | credits | 否 | 否 |
> | 活动 | 否 | 否 |
> | forecasts | 否 | 否 |
> | lots | 否 | 否 |
> | marketplaces | 否 | 否 |
> | operationresults | 否 | 否 |
> | 操作 | 否 | 否 |
> | operationstatus | 否 | 否 |
> | pricesheets | 否 | 否 |
> | products | 否 | 否 |
> | reservationdetails | 否 | 否 |
> | reservationrecommendationdetails | 否 | 否 |
> | reservationrecommendations | 否 | 否 |
> | reservationsummaries | 否 | 否 |
> | reservationtransactions | 否 | 否 |
> | 标记 | 否 | 否 |
> | tenants | 否 | 否 |
> | terms | 否 | 否 |
> | usagedetails | 否 | 否 |

## <a name="microsoftcontainer"></a>Microsoft.Container

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | containergroups | 否 | 否 |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | containergroups | 否 | 否 |
> | 位置 | 否 | 否 |
> | locations / cachedimages | 否 | 否 |
> | locations / capabilities | 否 | 否 |
> | locations / deletevirtualnetworkorsubnets | 否 | 否 |
> | locations / operations | 否 | 否 |
> | locations / usages | 否 | 否 |
> | 操作 | 否 | 否 |
> | serviceassociationlinks | 否 | 否 |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | checknameavailability | 否 | 否 |
> | 位置 | 否 | 否 |
> | locations / authorize | 否 | 否 |
> | locations / deletevirtualnetworkorsubnets | 否 | 否 |
> | locations / operationresults | 否 | 否 |
> | locations / setupauth | 否 | 否 |
> | 操作 | 否 | 否 |
> | registries | 是 | 是 |
> | registries / agentpools | 是 | 是 |
> | registries / agentpools / listqueuestatus | 否 | 否 |
> | registries / builds | 否 | 否 |
> | registries / builds / cancel | 否 | 否 |
> | registries / builds / getloglink | 否 | 否 |
> | registries / buildtasks | 是 | 是 |
> | registries / buildtasks / listsourcerepositoryproperties | 否 | 否 |
> | registries / buildtasks / steps | 否 | 否 |
> | registries / buildtasks / steps / listbuildarguments | 否 | 否 |
> | registries / eventgridfilters | 否 | 否 |
> | registries / exportpipelines | 否 | 否 |
> | registries / generatecredentials | 否 | 否 |
> | registries / getbuildsourceuploadurl | 否 | 否 |
> | registries / getcredentials | 否 | 否 |
> | registries / importimage | 否 | 否 |
> | registries / importpipelines | 否 | 否 |
> | registries / listbuildsourceuploadurl | 否 | 否 |
> | registries / listcredentials | 否 | 否 |
> | registries / listpolicies | 否 | 否 |
> | registries / listusages | 否 | 否 |
> | registries / pipelineruns | 否 | 否 |
> | registries / privateendpointconnectionproxies | 否 | 否 |
> | registries / privateendpointconnectionproxies / validate | 否 | 否 |
> | registries / privateendpointconnections | 否 | 否 |
> | registries / privatelinkresources | 否 | 否 |
> | registries / queuebuild | 否 | 否 |
> | registries / regeneratecredential | 否 | 否 |
> | registries / regeneratecredentials | 否 | 否 |
> | registries / replications | 是 | 是 |
> | registries / runs | 否 | 否 |
> | registries / runs / cancel | 否 | 否 |
> | registries / runs / listlogsasurl | 否 | 否 |
> | registries / schedulerun | 否 | 否 |
> | registries / scopemaps | 否 | 否 |
> | registries / taskruns | 否 | 否 |
> | registries / taskruns / listdetails | 否 | 否 |
> | registries / tasks | 是 | 是 |
> | registries / tasks / listdetails | 否 | 否 |
> | registries / tokens | 否 | 否 |
> | registries / updatepolicies | 否 | 否 |
> | registries / webhooks | 是 | 是 |
> | registries / webhooks / getcallbackconfig | 否 | 否 |
> | registries / webhooks / listevents | 否 | 否 |
> | registries / webhooks / ping | 否 | 否 |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | containerservices | 否 | 否 |
> | 位置 | 否 | 否 |
> | locations / openshiftclusters | 否 | 否 |
> | locations / operationresults | 否 | 否 |
> | locations / operations | 否 | 否 |
> | locations / orchestrators | 否 | 否 |
> | managedclusters | 否 | 否 |
> | openshiftmanagedclusters | 否 | 否 |
> | 操作 | 否 | 否 |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | 服务 | 否 | 否 |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | accounts | 否 | 否 |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | alerts | 否 | 否 |
> | billingaccounts | 否 | 否 |
> | budgets | 否 | 否 |
> | cloudconnectors | 否 | 否 |
> | 连接器 | 是 | 是 |
> | departments | 否 | 否 |
> | dimensions | 否 | 否 |
> | enrollmentaccounts | 否 | 否 |
> | exports | 否 | 否 |
> | externalbillingaccounts | 否 | 否 |
> | externalbillingaccounts / alerts | 否 | 否 |
> | externalbillingaccounts / dimensions | 否 | 否 |
> | externalbillingaccounts / forecast | 否 | 否 |
> | externalbillingaccounts / query | 否 | 否 |
> | externalsubscriptions | 否 | 否 |
> | externalsubscriptions / alerts | 否 | 否 |
> | externalsubscriptions / dimensions | 否 | 否 |
> | externalsubscriptions / forecast | 否 | 否 |
> | externalsubscriptions / query | 否 | 否 |
> | 预测 | 否 | 否 |
> | 操作 | 否 | 否 |
> | query | 否 | 否 |
> | register | 否 | 否 |
> | reportconfigs | 否 | 否 |
> | 报表 | 否 | 否 |
> | 设置 | 否 | 否 |
> | showbackrules | 否 | 否 |
> | 视图 | 否 | 否 |

## <a name="microsoftcostmanagementexports"></a>Microsoft.CostManagementExports

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | 操作 | 否 | 否 |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | hubs | 否 | 否 |

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | 操作 | 否 | 否 |
> | 请求 | 否 | 否 |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | associations | 否 | 否 |
> | 位置 | 否 | 否 |
> | locations / operationstatuses | 否 | 否 |
> | 操作 | 否 | 否 |
> | resourceproviders | 是 | 是 |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | jobs | 否 | 否 |
> | 位置 | 否 | 否 |
> | locations / availableskus | 否 | 否 |
> | locations / checknameavailability | 否 | 否 |
> | locations / operationresults | 否 | 否 |
> | locations / regionconfiguration | 否 | 否 |
> | locations / validateaddress | 否 | 否 |
> | locations / validateinputs | 否 | 否 |
> | 操作 | 否 | 否 |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | databoxedgedevices | 否 | 否 |
> | databoxedgedevices / checknameavailability | 否 | 否 |
> | 操作 | 否 | 否 |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | 位置 | 否 | 否 |
> | locations / getnetworkpolicies | 否 | 否 |
> | locations / operationstatuses | 否 | 否 |
> | 操作 | 否 | 否 |
> | workspaces | 否 | 否 |
> | workspaces / dbworkspaces | 否 | 否 |
> | workspaces / virtualnetworkpeerings | 否 | 否 |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | catalogs | 是 | 是 |
> | checknameavailability | 否 | 否 |
> | datacatalogs | 否 | 否 |
> | 位置 | 否 | 否 |
> | locations / jobs | 否 | 否 |
> | locations / operationresults | 否 | 否 |
> | 操作 | 否 | 否 |

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | connectionmanagers | 否 | 否 |

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | packages | 否 | 否 |
> | plans | 否 | 否 |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | checkazuredatafactorynameavailability | 否 | 否 |
> | checkdatafactorynameavailability | 否 | 否 |
> | datafactories | 是 | 是 |
> | datafactories / diagnosticsettings | 否 | 否 |
> | datafactories / metricdefinitions | 否 | 否 |
> | datafactoryschema | 否 | 否 |
> | factories | 是 | 是 |
> | factories / integrationruntimes | 否 | 否 |
> | 位置 | 否 | 否 |
> | locations / configurefactoryrepo | 否 | 否 |
> | locations / getfeaturevalue | 否 | 否 |
> | 操作 | 否 | 否 |

## <a name="microsoftdatalake"></a>Microsoft.DataLake

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | datalakeaccounts | 否 | 否 |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | accounts | 是 | 是 |
> | accounts / datalakestoreaccounts | 否 | 否 |
> | accounts / storageaccounts | 否 | 否 |
> | accounts / storageaccounts / containers | 否 | 否 |
> | accounts / storageaccounts / containers / listsastokens | 否 | 否 |
> | 位置 | 否 | 否 |
> | locations / capability | 否 | 否 |
> | locations / checknameavailability | 否 | 否 |
> | locations / operationresults | 否 | 否 |
> | locations / usages | 否 | 否 |
> | 操作 | 否 | 否 |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | accounts | 是 | 是 |
> | accounts / eventgridfilters | 否 | 否 |
> | accounts / firewallrules | 否 | 否 |
> | 位置 | 否 | 否 |
> | locations / capability | 否 | 否 |
> | locations / checknameavailability | 否 | 否 |
> | locations / deletevirtualnetworkorsubnets | 否 | 否 |
> | locations / operationresults | 否 | 否 |
> | locations / usages | 否 | 否 |
> | 操作 | 否 | 否 |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | 位置 | 否 | 否 |
> | locations / checknameavailability | 否 | 否 |
> | locations / operationresults | 否 | 否 |
> | locations / operationstatuses | 否 | 否 |
> | 操作 | 否 | 否 |
> | services | 否 | 否 |
> | services / projects | 否 | 否 |
> | slots | 否 | 否 |

## <a name="microsoftdataprotection"></a>Microsoft.DataProtection

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | backupvaults | 否 | 否 |
> | 位置 | 否 | 否 |
> | 操作 | 否 | 否 |

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | accounts | 是 | 是 |
> | accounts / shares | 否 | 否 |
> | accounts / shares / datasets | 否 | 否 |
> | accounts / shares / invitations | 否 | 否 |
> | accounts / shares / providersharesubscriptions | 否 | 否 |
> | accounts / shares / synchronizationsettings | 否 | 否 |
> | accounts / sharesubscriptions | 否 | 否 |
> | accounts / sharesubscriptions / consumersourcedatasets | 否 | 否 |
> | accounts / sharesubscriptions / datasetmappings | 否 | 否 |
> | accounts / sharesubscriptions / triggers | 否 | 否 |
> | listinvitations | 否 | 否 |
> | 位置 | 否 | 否 |
> | locations / consumerinvitations | 否 | 否 |
> | locations / operationresults | 否 | 否 |
> | locations / rejectinvitation | 否 | 否 |
> | 操作 | 否 | 否 |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | checknameavailability | 否 | 否 |
> | 位置 | 否 | 否 |
> | locations / azureasyncoperation | 否 | 否 |
> | locations / operationresults | 否 | 否 |
> | locations / performancetiers | 否 | 否 |
> | locations / privateendpointconnectionazureasyncoperation | 否 | 否 |
> | locations / privateendpointconnectionoperationresults | 否 | 否 |
> | locations / privateendpointconnectionproxyazureasyncoperation | 否 | 否 |
> | locations / privateendpointconnectionproxyoperationresults | 否 | 否 |
> | locations / recommendedactionsessionsazureasyncoperation | 否 | 否 |
> | locations / recommendedactionsessionsoperationresults | 否 | 否 |
> | locations / securityalertpoliciesazureasyncoperation | 否 | 否 |
> | locations / securityalertpoliciesoperationresults | 否 | 否 |
> | locations / serverkeyazureasyncoperation | 否 | 否 |
> | locations / serverkeyoperationresults | 否 | 否 |
> | 操作 | 否 | 否 |
> | servers | 是 | 是 |
> | servers / advisors | 否 | 否 |
> | servers / privateendpointconnectionproxies | 否 | 否 |
> | servers / privateendpointconnections | 否 | 否 |
> | servers / privatelinkresources | 否 | 否 |
> | servers / querytexts | 否 | 否 |
> | servers / recoverableservers | 否 | 否 |
> | servers / topquerystatistics | 否 | 否 |
> | servers / virtualnetworkrules | 否 | 否 |
> | servers / waitstatistics | 否 | 否 |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | checknameavailability | 否 | 否 |
> | 位置 | 否 | 否 |
> | locations / administratorazureasyncoperation | 否 | 否 |
> | locations / administratoroperationresults | 否 | 否 |
> | locations / azureasyncoperation | 否 | 否 |
> | locations / operationresults | 否 | 否 |
> | locations / performancetiers | 否 | 否 |
> | locations / privateendpointconnectionazureasyncoperation | 否 | 否 |
> | locations / privateendpointconnectionoperationresults | 否 | 否 |
> | locations / privateendpointconnectionproxyazureasyncoperation | 否 | 否 |
> | locations / privateendpointconnectionproxyoperationresults | 否 | 否 |
> | locations / recommendedactionsessionsazureasyncoperation | 否 | 否 |
> | locations / recommendedactionsessionsoperationresults | 否 | 否 |
> | locations / securityalertpoliciesazureasyncoperation | 否 | 否 |
> | locations / securityalertpoliciesoperationresults | 否 | 否 |
> | locations / serverkeyazureasyncoperation | 否 | 否 |
> | locations / serverkeyoperationresults | 否 | 否 |
> | 操作 | 否 | 否 |
> | servers | 是 | 是 |
> | servers / advisors | 否 | 否 |
> | servers / keys | 否 | 否 |
> | servers / privateendpointconnectionproxies | 否 | 否 |
> | servers / privateendpointconnections | 否 | 否 |
> | servers / privatelinkresources | 否 | 否 |
> | servers / querytexts | 否 | 否 |
> | servers / recoverableservers | 否 | 否 |
> | servers / topquerystatistics | 否 | 否 |
> | servers / virtualnetworkrules | 否 | 否 |
> | servers / waitstatistics | 否 | 否 |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | checknameavailability | 否 | 否 |
> | 位置 | 否 | 否 |
> | locations / administratorazureasyncoperation | 否 | 否 |
> | locations / administratoroperationresults | 否 | 否 |
> | locations / azureasyncoperation | 否 | 否 |
> | locations / operationresults | 否 | 否 |
> | locations / performancetiers | 否 | 否 |
> | locations / privateendpointconnectionazureasyncoperation | 否 | 否 |
> | locations / privateendpointconnectionoperationresults | 否 | 否 |
> | locations / privateendpointconnectionproxyazureasyncoperation | 否 | 否 |
> | locations / privateendpointconnectionproxyoperationresults | 否 | 否 |
> | locations / recommendedactionsessionsazureasyncoperation | 否 | 否 |
> | locations / recommendedactionsessionsoperationresults | 否 | 否 |
> | locations / securityalertpoliciesazureasyncoperation | 否 | 否 |
> | locations / securityalertpoliciesoperationresults | 否 | 否 |
> | locations / serverkeyazureasyncoperation | 否 | 否 |
> | locations / serverkeyoperationresults | 否 | 否 |
> | 操作 | 否 | 否 |
> | servergroups | 否 | 否 |
> | servers | 是 | 是 |
> | servers / advisors | 否 | 否 |
> | servers / keys | 否 | 否 |
> | servers / privateendpointconnectionproxies | 否 | 否 |
> | servers / privateendpointconnections | 否 | 否 |
> | servers / privatelinkresources | 否 | 否 |
> | servers / querytexts | 否 | 否 |
> | servers / recoverableservers | 否 | 否 |
> | servers / topquerystatistics | 否 | 否 |
> | servers / virtualnetworkrules | 否 | 否 |
> | servers / waitstatistics | 否 | 否 |
> | serversv2 | 是 | 是 |
> | singleservers | 是 | 是 |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | artifactsources | 是 | 是 |
> | operationresults | 否 | 否 |
> | 操作 | 否 | 否 |
> | rollouts | 是 | 是 |
> | servicetopologies | 是 | 是 |
> | servicetopologies / services | 是 | 是 |
> | servicetopologies / services / serviceunits | 是 | 是 |
> | steps | 是 | 是 |

## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualization

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | applicationgroups | 是 | 是 |
> | applicationgroups / applications | 否 | 否 |
> | applicationgroups / desktops | 否 | 否 |
> | applicationgroups / startmenuitems | 否 | 否 |
> | hostpools | 是 | 是 |
> | hostpools / sessionhosts | 否 | 否 |
> | hostpools / sessionhosts / usersessions | 否 | 否 |
> | hostpools / usersessions | 否 | 否 |
> | 操作 | 否 | 否 |
> | workspaces | 是 | 是 |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | checknameavailability | 否 | 否 |
> | checkprovisioningservicenameavailability | 否 | 否 |
> | elasticpools | 否 | 否 |
> | elasticpools / iothubtenants | 否 | 否 |
> | iothubs | 是 | 是 |
> | iothubs / eventgridfilters | 否 | 否 |
> | operationresults | 否 | 否 |
> | 操作 | 否 | 否 |
> | provisioningservices | 是 | 是 |
> | usages | 否 | 否 |

## <a name="microsoftdevops"></a>Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | pipelines | 是 | 是 |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | 控制器 | 是 | 是 |
> | controllers / listconnectiondetails | 否 | 否 |
> | 位置 | 否 | 否 |
> | locations / checkcontainerhostmapping | 否 | 否 |
> | locations / operationresults | 否 | 否 |
> | 操作 | 否 | 否 |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | labcenters | 否 | 否 |
> | labs | 是 | 否 |
> | labs / environments | 是 | 是 |
> | labs / servicerunners | 是 | 是 |
> | labs / virtualmachines | 是 | 否 |
> | 位置 | 否 | 否 |
> | locations / operations | 否 | 否 |
> | 操作 | 否 | 否 |
> | schedules | 是 | 是 |

## <a name="microsoftdigitaltwins"></a>Microsoft.DigitalTwins

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | digitaltwinsinstances | 否 | 否 |
> | 位置 | 否 | 否 |
> | 操作 | 否 | 否 |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | databaseaccountnames | 否 | 否 |
> | databaseaccounts | 是 | 是 |
> | 位置 | 否 | 否 |
> | locations / deletevirtualnetworkorsubnets | 否 | 否 |
> | locations / operationresults | 否 | 否 |
> | locations / operationsstatus | 否 | 否 |
> | operationresults | 否 | 否 |
> | 操作 | 否 | 否 |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | checkdomainavailability | 否 | 否 |
> | domains | 是 | 是 |
> | domains / domainownershipidentifiers | 否 | 否 |
> | generatessorequest | 否 | 否 |
> | listdomainrecommendations | 否 | 否 |
> | 操作 | 否 | 否 |
> | topleveldomains | 否 | 否 |
> | validatedomainregistrationinformation | 否 | 否 |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | 位置 | 否 | 否 |
> | locations / operationresults | 否 | 否 |
> | 操作 | 否 | 否 |
> | services | 是 | 是 |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | domains | 是 | 是 |
> | eventSubscriptions | 否 - 无法独立移动，但会自动随订阅的资源一起移动。 | 否 - 无法独立移动，但会自动随订阅的资源一起移动。 |
> | domains / topics | 否 | 否 |
> | eventsubscriptions | 否 - 无法独立移动，但会自动随订阅的资源一起移动。 | 否 - 无法独立移动，但会自动随订阅的资源一起移动。 |
> | extensiontopics | 否 | 否 |
> | 位置 | 否 | 否 |
> | locations / eventsubscriptions | 否 | 否 |
> | locations / operationresults | 否 | 否 |
> | locations / operationsstatus | 否 | 否 |
> | locations / topictypes | 否 | 否 |
> | operationresults | 否 | 否 |
> | 操作 | 否 | 否 |
> | operationsstatus | 否 | 否 |
> | partnernamespaces | 是 | 是 |
> | partnernamespaces / eventchannels | 否 | 否 |
> | partnerregistrations | 否 | 否 |
> | partnertopics | 是 | 是 |
> | partnertopics / eventsubscriptions | 否 | 否 |
> | systemtopics | 是 | 是 |
> | systemtopics / eventsubscriptions | 否 | 否 |
> | topics | 是 | 是 |
> | topictypes | 否 | 否 |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | availableclusterregions | 否 | 否 |
> | checknameavailability | 否 | 否 |
> | checknamespaceavailability | 否 | 否 |
> | clusters | 是 | 是 |
> | 位置 | 否 | 否 |
> | locations / deletevirtualnetworkorsubnets | 否 | 否 |
> | namespaces | 是 | 是 |
> | namespaces / authorizationrules | 否 | 否 |
> | namespaces / disasterrecoveryconfigs | 否 | 否 |
> | namespaces / disasterrecoveryconfigs / checknameavailability | 否 | 否 |
> | namespaces / eventhubs | 否 | 否 |
> | namespaces / eventhubs / authorizationrules | 否 | 否 |
> | namespaces / eventhubs / consumergroups | 否 | 否 |
> | namespaces / networkrulesets | 否 | 否 |
> | 操作 | 否 | 否 |
> | sku | 否 | 否 |

## <a name="microsoftexperimentation"></a>Microsoft.Experimentation

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | experimentworkspaces | 否 | 否 |
> | 位置 | 否 | 否 |
> | locations / operations | 否 | 否 |

## <a name="microsoftfalcon"></a>Microsoft.Falcon

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | namespaces | 是 | 是 |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | featureproviders | 否 | 否 |
> | features | 否 | 否 |
> | 操作 | 否 | 否 |
> | providers | 否 | 否 |
> | subscriptionfeatureregistrations | 否 | 否 |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | accounts | 否 | 否 |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | automanagedaccounts | 否 | 否 |
> | automanagedvmconfigurationprofiles | 否 | 否 |
> | guestconfigurationassignments | 否 | 否 |
> | 操作 | 否 | 否 |
> | software | 否 | 否 |
> | softwareupdateprofile | 否 | 否 |
> | softwareupdates | 否 | 否 |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | hanainstances | 否 | 否 |
> | 位置 | 否 | 否 |
> | locations / operations | 否 | 否 |
> | locations / operationsstatus | 否 | 否 |
> | 操作 | 否 | 否 |
> | sapmonitors | 是 | 是 |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | dedicatedhsms | 否 | 否 |
> | 位置 | 否 | 否 |
> | 操作 | 否 | 否 |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | clusters | 是 | 是 |
> | clusters / applications | 否 | 否 |
> | clusters / operationresults | 否 | 否 |
> | 位置 | 否 | 否 |
> | locations / azureasyncoperations | 否 | 否 |
> | locations / billingspecs | 否 | 否 |
> | locations / capabilities | 否 | 否 |
> | locations / operationresults | 否 | 否 |
> | locations / usages | 否 | 否 |
> | locations / validatecreaterequest | 否 | 否 |
> | 操作 | 否 | 否 |

> [!IMPORTANT]
> 可以将 HDInsight 群集移到新的订阅或资源组。 但是，无法在订阅之间移动链接到 HDInsight 群集的网络资源（例如虚拟网络、NIC 或负载均衡器）。 此外，无法将连接到群集的虚拟机的 NIC 移到新的资源组。
>
> 将 HDInsight 群集移到新的订阅时，首先移动其他资源（如存储帐户）。 然后移动 HDInsight 群集本身。

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | checknameavailability | 否 | 否 |
> | 位置 | 否 | 否 |
> | locations / operationresults | 否 | 否 |
> | 操作 | 否 | 否 |
> | services | 是 | 是 |
> | services / privateendpointconnections | 否 | 否 |
> | services / privatelinkresources | 否 | 否 |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | machines | 是 | 是 |
> | machines / extensions | 否 | 否 |
> | 操作 | 否 | 否 |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | datamanagers | 是 | 是 |
> | 操作 | 否 | 否 |

## <a name="microsofthydra"></a>Microsoft.Hydra

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | components | 否 | 否 |
> | 位置 | 否 | 否 |
> | networkscopes | 否 | 否 |
> | 操作 | 否 | 否 |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | jobs | 是 | 是 |
> | 位置 | 否 | 否 |
> | locations / operationresults | 否 | 否 |
> | 操作 | 否 | 否 |

## <a name="microsoftinsights"></a>microsoft.insights

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | actiongroups | 是 | 是 |
> | activitylogalerts | 否 | 否 |
> | alertrules | 是 | 是 |
> | autoscalesettings | 是 | 是 |
> | baseline | 否 | 否 |
> | calculatebaseline | 否 | 否 |
> | components | 是 | 是 |
> | components / events | 否 | 否 |
> | components / linkedstorageaccounts | 否 | 否 |
> | components / metrics | 否 | 否 |
> | components / pricingplans | 否 | 否 |
> | components / query | 否 | 否 |
> | datacollectionrules | 否 | 否 |
> | diagnosticsettings | 否 | 否 |
> | diagnosticsettingscategories | 否 | 否 |
> | eventcategories | 否 | 否 |
> | eventtypes | 否 | 否 |
> | extendeddiagnosticsettings | 否 | 否 |
> | guestdiagnosticsettings | 否 | 否 |
> | listmigrationdate | 否 | 否 |
> | 位置 | 否 | 否 |
> | locations / operationresults | 否 | 否 |
> | logdefinitions | 否 | 否 |
> | logprofiles | 否 | 否 |
> | 日志 | 否 | 否 |
> | metricalerts | 否 | 否 |
> | metricbaselines | 否 | 否 |
> | metricbatch | 否 | 否 |
> | metricdefinitions | 否 | 否 |
> | metricnamespaces | 否 | 否 |
> | 指标 | 否 | 否 |
> | migratealertrules | 否 | 否 |
> | migratetonewpricingmodel | 否 | 否 |
> | myworkbooks | 否 | 否 |
> | notificationgroups | 否 | 否 |
> | 操作 | 否 | 否 |
> | privatelinkscopeoperationstatuses | 否 | 否 |
> | privatelinkscopes | 否 | 否 |
> | privatelinkscopes / privateendpointconnectionproxies | 否 | 否 |
> | privatelinkscopes / privateendpointconnections | 否 | 否 |
> | privatelinkscopes / scopedresources | 否 | 否 |
> | rollbacktolegacypricingmodel | 否 | 否 |
> | scheduledqueryrules | 是 | 是 |
> | 拓扑 | 否 | 否 |
> | 事务 | 否 | 否 |
> | vminsightsonboardingstatuses | 否 | 否 |
> | webtests | 是 | 是 |
> | workbooks | 是 | 是 |
> | workbooktemplates | 是 | 是 |

> [!IMPORTANT]
> 确保移动到新订阅时，不会超出[订阅配额](azure-subscription-service-limits.md#azure-monitor-limits)。

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | apptemplates | 否 | 否 |
> | checknameavailability | 否 | 否 |
> | checksubdomainavailability | 否 | 否 |
> | iotapps | 是 | 是 |
> | 操作 | 否 | 否 |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | checknameavailability | 是 | 是 |
> | graph | 是 | 是 |
> | 操作 | 否 | 否 |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | checknameavailability | 否 | 否 |
> | deletedvaults | 否 | 否 |
> | hsmpools | 否 | 否 |
> | 位置 | 否 | 否 |
> | locations / deletedvaults | 否 | 否 |
> | locations / deletevirtualnetworkorsubnets | 否 | 否 |
> | locations / operationresults | 否 | 否 |
> | 操作 | 否 | 否 |
> | vaults | 是 | 是 |
> | vaults / accesspolicies | 否 | 否 |
> | vaults / eventgridfilters | 否 | 否 |
> | vaults / secrets | 否 | 否 |

> [!IMPORTANT]
> 用于磁盘加密的 Key Vault 不能移动到同一订阅中的资源组，也不能跨订阅移动。

## <a name="microsoftkubernetes"></a>Microsoft.Kubernetes

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | connectedclusters | 是 | 是 |
> | 位置 | 否 | 否 |
> | locations / operationstatuses | 否 | 否 |
> | 操作 | 否 | 否 |
> | registeredsubscriptions | 否 | 否 |

## <a name="microsoftkubernetesconfiguration"></a>Microsoft.KubernetesConfiguration

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | sourcecontrolconfigurations | 否 | 否 |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | clusters | 是 | 是 |
> | clusters / attacheddatabaseconfigurations | 否 | 否 |
> | clusters / databases | 否 | 否 |
> | clusters / databases / dataconnections | 否 | 否 |
> | clusters / databases / eventhubconnections | 否 | 否 |
> | clusters / databases / principalassignments | 否 | 否 |
> | clusters / principalassignments | 否 | 否 |
> | 位置 | 否 | 否 |
> | locations / checknameavailability | 否 | 否 |
> | locations / operationresults | 否 | 否 |
> | 操作 | 否 | 否 |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | labaccounts | 否 | 否 |
> | 位置 | 否 | 否 |
> | locations / operations | 否 | 否 |
> | 操作 | 否 | 否 |
> | users | 否 | 否 |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | accounts | 否 | 否 |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | accounts | 否 | 否 |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | hostingenvironments | 否 | 否 |
> | integrationaccounts | 是 | 是 |
> | integrationserviceenvironments | 是 | 否 |
> | integrationserviceenvironments / managedapis | 是 | 否 |
> | isolatedenvironments | 否 | 否 |
> | 位置 | 否 | 否 |
> | locations / workflows | 否 | 否 |
> | 操作 | 否 | 否 |
> | workflows | 是 | 是 |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | commitmentplans | 否 | 否 |
> | 位置 | 否 | 否 |
> | locations / operations | 否 | 否 |
> | locations / operationsstatus | 否 | 否 |
> | 操作 | 否 | 否 |
> | webservices | 是 | 否 |
> | workspaces | 是 | 是 |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | operationalizationclusters | 否 | 否 |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | accounts | 否 | 否 |
> | accounts / workspaces | 否 | 否 |
> | accounts / workspaces / projects | 否 | 否 |
> | teamaccounts | 否 | 否 |
> | teamaccounts / workspaces | 否 | 否 |
> | teamaccounts / workspaces / projects | 否 | 否 |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | accounts | 否 | 否 |

## <a name="microsoftmachinelearningoperationalization"></a>Microsoft.MachineLearningOperationalization

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | hostingaccounts | 否 | 否 |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | 位置 | 否 | 否 |
> | locations / computeoperationsstatus | 否 | 否 |
> | locations / quotas | 否 | 否 |
> | locations / updatequotas | 否 | 否 |
> | locations / usages | 否 | 否 |
> | locations / vmsizes | 否 | 否 |
> | locations / workspaceoperationsstatus | 否 | 否 |
> | 操作 | 否 | 否 |
> | workspaces | 否 | 否 |
> | workspaces / computes | 否 | 否 |
> | workspaces / eventgridfilters | 否 | 否 |

## <a name="microsoftmaintenance"></a>Microsoft.Maintenance

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | applyupdates | 否 | 否 |
> | configurationassignments | 否 | 否 |
> | maintenanceconfigurations | 是 | 是 |
> | updates | 否 | 否 |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | identities | 否 | 否 |
> | 操作 | 否 | 否 |
> | userassignedidentities | 否 | 否 |

## <a name="microsoftmanagednetwork"></a>Microsoft.ManagedNetwork

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | managednetworks | 否 | 否 |
> | managednetworks / managednetworkgroups | 否 | 否 |
> | managednetworks / managednetworkpeeringpolicies | 否 | 否 |
> | 通知 | 否 | 否 |

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | marketplaceregistrationdefinitions | 否 | 否 |
> | 操作 | 否 | 否 |
> | operationstatuses | 否 | 否 |
> | registrationassignments | 否 | 否 |
> | registrationdefinitions | 否 | 否 |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | checknameavailability | 否 | 否 |
> | getentities | 否 | 否 |
> | managementgroups | 否 | 否 |
> | managementgroups / settings | 否 | 否 |
> | operationresults | 否 | 否 |
> | operationresults / asyncoperation | 否 | 否 |
> | 操作 | 否 | 否 |
> | 资源 | 否 | 否 |
> | starttenantbackfill | 否 | 否 |
> | tenantbackfillstatus | 否 | 否 |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | accounts | 是 | 是 |
> | accounts / eventgridfilters | 否 | 否 |
> | accounts / privateatlases | 是 | 是 |
> | 操作 | 否 | 否 |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | listavailableoffers | 否 | 否 |
> | offers | 否 | 否 |
> | offertypes | 否 | 否 |
> | offertypes / publishers | 否 | 否 |
> | offertypes / publishers / offers | 否 | 否 |
> | offertypes / publishers / offers / plans | 否 | 否 |
> | offertypes / publishers / offers / plans / agreements | 否 | 否 |
> | offertypes / publishers / offers / plans / configs | 否 | 否 |
> | offertypes / publishers / offers / plans / configs / importimage | 否 | 否 |
> | 操作 | 否 | 否 |
> | privategalleryitems | 否 | 否 |
> | privatestoreclient | 否 | 否 |
> | privatestores | 否 | 否 |
> | privatestores / offers | 否 | 否 |
> | products | 否 | 否 |
> | publishers | 否 | 否 |
> | publishers / offers | 否 | 否 |
> | publishers / offers / amendments | 否 | 否 |
> | register | 否 | 否 |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | classicdevservices | 否 | 否 |
> | listcommunicationpreference | 否 | 否 |
> | 操作 | 否 | 否 |
> | updatecommunicationpreference | 否 | 否 |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | 协议 | 否 | 否 |
> | offertypes | 否 | 否 |
> | 操作 | 否 | 否 |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | checknameavailability | 否 | 否 |
> | 位置 | 否 | 否 |
> | locations / checknameavailability | 否 | 否 |
> | mediaservices | 是 | 是 |
> | mediaservices / accountfilters | 否 | 否 |
> | mediaservices / assets | 否 | 否 |
> | mediaservices / assets / assetfilters | 否 | 否 |
> | mediaservices / contentkeypolicies | 否 | 否 |
> | mediaservices / eventgridfilters | 否 | 否 |
> | mediaservices / liveeventoperations | 否 | 否 |
> | mediaservices / liveevents | 是 | 是 |
> | mediaservices / liveevents / liveoutputs | 否 | 否 |
> | mediaservices / liveoutputoperations | 否 | 否 |
> | mediaservices / streamingendpointoperations | 否 | 否 |
> | mediaservices / streamingendpoints | 是 | 是 |
> | mediaservices / streaminglocators | 否 | 否 |
> | mediaservices / streamingpolicies | 否 | 否 |
> | mediaservices / transforms | 否 | 否 |
> | mediaservices / transforms / jobs | 否 | 否 |
> | 操作 | 否 | 否 |

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | appclusters | 否 | 否 |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | assessmentprojects | 是 | 是 |
> | 位置 | 否 | 否 |
> | locations / assessmentoptions | 否 | 否 |
> | locations / checknameavailability | 否 | 否 |
> | migrateprojects | 是 | 是 |
> | movecollections | 否 | 否 |
> | 操作 | 否 | 否 |
> | projects | 否 | 否 |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | holographicsbroadcastaccounts | 否 | 否 |
> | 位置 | 否 | 否 |
> | locations / checknameavailability | 否 | 否 |
> | objectunderstandingaccounts | 否 | 否 |
> | 操作 | 否 | 否 |
> | remoterenderingaccounts | 是 | 是 |
> | spatialanchorsaccounts | 是 | 是 |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | netappaccounts | 否 | 否 |
> | netappaccounts / backuppolicies | 否 | 否 |
> | netappaccounts / capacitypools | 否 | 否 |
> | netappaccounts / capacitypools / volumes | 否 | 否 |
> | netappaccounts / capacitypools / volumes / mounttargets | 否 | 否 |
> | netappaccounts / capacitypools / volumes / snapshots | 否 | 否 |
> | 操作 | 否 | 否 |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | applicationgatewayavailablerequestheaders | 否 | 否 |
> | applicationgatewayavailableresponseheaders | 否 | 否 |
> | applicationgatewayavailableservervariables | 否 | 否 |
> | applicationgatewayavailablessloptions | 否 | 否 |
> | applicationgatewayavailablewafrulesets | 否 | 否 |
> | applicationgateways | 否 | 否 |
> | applicationgatewaywebapplicationfirewallpolicies | 否 | 否 |
> | applicationsecuritygroups | 是 | 是 |
> | azurefirewallfqdntags | 否 | 否 |
> | azurefirewalls | 否 | 否 |
> | bastionhosts | 否 | 否 |
> | bgpservicecommunities | 否 | 否 |
> | checkfrontdoornameavailability | 否 | 否 |
> | checktrafficmanagernameavailability | 否 | 否 |
> | connections | 是 | 是 |
> | ddoscustompolicies | 是 | 是 |
> | ddosprotectionplans | 否 | 否 |
> | dnsoperationresults | 否 | 否 |
> | dnsoperationstatuses | 否 | 否 |
> | dnszones | 是 | 是 |
> | dnszones / a | 否 | 否 |
> | dnszones / aaaa | 否 | 否 |
> | dnszones / all | 否 | 否 |
> | dnszones / caa | 否 | 否 |
> | dnszones / cname | 否 | 否 |
> | dnszones / mx | 否 | 否 |
> | dnszones / ns | 否 | 否 |
> | dnszones / ptr | 否 | 否 |
> | dnszones / recordsets | 否 | 否 |
> | dnszones / soa | 否 | 否 |
> | dnszones / srv | 否 | 否 |
> | dnszones / txt | 否 | 否 |
> | expressroutecircuits | 否 | 否 |
> | expressroutegateways | 否 | 否 |
> | expressrouteserviceproviders | 否 | 否 |
> | firewallpolicies | 是 | 是 |
> | frontdooroperationresults | 否 | 否 |
> | frontdoors | 否 | 否 |
> | frontdoorwebapplicationfirewallmanagedrulesets | 否 | 否 |
> | frontdoorwebapplicationfirewallpolicies | 否 | 否 |
> | getdnsresourcereference | 否 | 否 |
> | internalnotify | 否 | 否 |
> | ipgroups | 是 | 是 |
> | loadbalancers | 是 - 基本 SKU<br>否 - 标准 SKU | 是 - 基本 SKU<br>否 - 标准 SKU |
> | localnetworkgateways | 是 | 是 |
> | 位置 | 否 | 否 |
> | locations / autoapprovedprivatelinkservices | 否 | 否 |
> | locations / availabledelegations | 否 | 否 |
> | locations / availableprivateendpointtypes | 否 | 否 |
> | locations / availableservicealiases | 否 | 否 |
> | locations / baremetaltenants | 否 | 否 |
> | locations / batchnotifyprivateendpointsforresourcemove | 否 | 否 |
> | locations / batchvalidateprivateendpointsforresourcemove | 否 | 否 |
> | locations / checkacceleratednetworkingsupport | 否 | 否 |
> | locations / checkdnsnameavailability | 否 | 否 |
> | locations / checkprivatelinkservicevisibility | 否 | 否 |
> | locations / commitinternalazurenetworkmanagerconfiguration | 否 | 否 |
> | locations / effectiveresourceownership | 否 | 否 |
> | locations / nfvoperationresults | 否 | 否 |
> | locations / nfvoperations | 否 | 否 |
> | locations / operationresults | 否 | 否 |
> | locations / operations | 否 | 否 |
> | locations / servicetags | 否 | 否 |
> | locations / setresourceownership | 否 | 否 |
> | locations / supportedvirtualmachinesizes | 否 | 否 |
> | locations / usages | 否 | 否 |
> | locations / validateresourceownership | 否 | 否 |
> | locations / virtualnetworkavailableendpointservices | 否 | 否 |
> | natgateways | 是 | 是 |
> | networkexperimentprofiles | 否 | 否 |
> | networkintentpolicies | 是 | 是 |
> | networkinterfaces | 是 | 是 |
> | networkprofiles | 否 | 否 |
> | networksecuritygroups | 是 | 是 |
> | networkwatchers | 是 | 否 |
> | networkwatchers / connectionmonitors | 是 | 否 |
> | networkwatchers / flowlogs | 是 | 否 |
> | networkwatchers / pingmeshes | 是 | 否 |
> | 操作 | 否 | 否 |
> | p2svpngateways | 否 | 否 |
> | privatednsoperationresults | 否 | 否 |
> | privatednsoperationstatuses | 否 | 否 |
> | privatednszones | 是 | 是 |
> | privatednszones / a | 否 | 否 |
> | privatednszones / aaaa | 否 | 否 |
> | privatednszones / all | 否 | 否 |
> | privatednszones / cname | 否 | 否 |
> | privatednszones / mx | 否 | 否 |
> | privatednszones / ptr | 否 | 否 |
> | privatednszones / soa | 否 | 否 |
> | privatednszones / srv | 否 | 否 |
> | privatednszones / txt | 否 | 否 |
> | privatednszones / virtualnetworklinks | 是 | 是 |
> | privatednszonesinternal | 否 | 否 |
> | privateendpointredirectmaps | 否 | 否 |
> | privateendpoints | 是 | 是 |
> | privatelinkservices | 否 | 否 |
> | publicipaddresses | 是 - 基本 SKU<br>否 - 标准 SKU | 是 - 基本 SKU<br>否 - 标准 SKU |
> | publicipprefixes | 是 | 是 |
> | routefilters | 否 | 否 |
> | routetables | 是 | 是 |
> | securitypartnerproviders | 是 | 是 |
> | serviceendpointpolicies | 是 | 是 |
> | trafficmanagergeographichierarchies | 否 | 否 |
> | trafficmanagerprofiles | 是 | 是 |
> | trafficmanagerprofiles / heatmaps | 否 | 否 |
> | trafficmanagerusermetricskeys | 否 | 否 |
> | virtualhubs | 否 | 否 |
> | virtualnetworkgateways | 是 | 是 |
> | virtualnetworks | 是 | 是 |
> | virtualnetworktaps | 否 | 否 |
> | virtualrouters | 是 | 是 |
> | virtualwans | 否 | 否 |
> | vpngateways（虚拟 WAN） | 否 | 否 |
> | vpnserverconfigurations | 否 | 否 |
> | vpnsites（虚拟 WAN） | 否 | 否 |

> [!IMPORTANT]
> 请参阅[网络移动指南](./move-limitations/networking-move-limitations.md)。

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | checknameavailability | 否 | 否 |
> | checknamespaceavailability | 否 | 否 |
> | namespaces | 是 | 是 |
> | namespaces / notificationhubs | 是 | 是 |
> | operationresults | 否 | 否 |
> | 操作 | 否 | 否 |

## <a name="microsoftobjectstore"></a>Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | osnamespaces | 是 | 是 |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | hypervsites | 是 | 是 |
> | importsites | 是 | 是 |
> | 操作 | 否 | 否 |
> | serversites | 是 | 是 |
> | vmwaresites | 是 | 是 |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | clusters | 否 | 否 |
> | deletedworkspaces | 否 | 否 |
> | linktargets | 否 | 否 |
> | 位置 | 否 | 否 |
> | locations / operationstatuses | 否 | 否 |
> | 操作 | 否 | 否 |
> | storageinsightconfigs | 否 | 否 |
> | workspaces | 是 | 是 |
> | workspaces / datasources | 否 | 否 |
> | workspaces / linkedservices | 否 | 否 |
> | workspaces / linkedstorageaccounts | 否 | 否 |
> | workspaces / metadata | 否 | 否 |
> | workspaces / query | 否 | 否 |
> | workspaces / scopedprivatelinkproxies | 否 | 否 |

> [!IMPORTANT]
> 确保移动到新订阅时，不会超出[订阅配额](azure-subscription-service-limits.md#azure-monitor-limits)。
>
> 无法移动具有链接的自动化帐户的工作区。 在开始移动操作之前，请确保取消链接所有自动化帐户。

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | managementassociations | 否 | 否 |
> | managementconfigurations | 是 | 是 |
> | 操作 | 否 | 否 |
> | solutions | 是 | 是 |
> | 视图 | 是 | 是 |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | checkserviceprovideravailability | 否 | 否 |
> | legacypeerings | 否 | 否 |
> | 操作 | 否 | 否 |
> | peerasns | 否 | 否 |
> | peeringlocations | 否 | 否 |
> | peerings | 是 | 是 |
> | peeringservicecountries | 否 | 否 |
> | peeringservicelocations | 否 | 否 |
> | peeringserviceproviders | 否 | 否 |
> | peeringservices | 否 | 否 |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | asyncoperationresults | 否 | 否 |
> | 操作 | 否 | 否 |
> | policyevents | 否 | 否 |
> | policystates | 否 | 否 |
> | policytrackedresources | 否 | 否 |
> | remediations | 否 | 否 |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | consoles | 否 | 否 |
> | dashboards | 是 | 是 |
> | 位置 | 否 | 否 |
> | locations / consoles | 否 | 否 |
> | locations / usersettings | 否 | 否 |
> | 操作 | 否 | 否 |
> | usersettings | 否 | 否 |

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | rootresources | 否 | 否 |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | 位置 | 否 | 否 |
> | locations / checknameavailability | 否 | 否 |
> | workspacecollections | 是 | 是 |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | capacities | 是 | 是 |
> | 位置 | 否 | 否 |
> | locations / checknameavailability | 否 | 否 |
> | locations / operationresults | 否 | 否 |
> | locations / operationstatuses | 否 | 否 |
> | 操作 | 否 | 否 |

## <a name="microsoftprojectbabylon"></a>Microsoft.ProjectBabylon

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | accounts | 否 | 否 |
> | checknameavailability | 否 | 否 |
> | 操作 | 否 | 否 |

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | accounts | 否 | 否 |

## <a name="microsoftproviderhub"></a>Microsoft.ProviderHub

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | availableaccounts | 否 | 否 |
> | providerregistrations | 否 | 否 |
> | providerregistrations / resourcetyperegistrations | 否 | 否 |
> | rollouts | 否 | 否 |

## <a name="microsoftquantum"></a>Microsoft.Quantum

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | 位置 | 否 | 否 |
> | locations / operationstatuses | 否 | 否 |
> | 操作 | 否 | 否 |
> | workspaces | 否 | 否 |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | backupprotecteditems | 否 | 否 |
> | 位置 | 否 | 否 |
> | locations / allocatedstamp | 否 | 否 |
> | locations / allocatestamp | 否 | 否 |
> | locations / backupaadproperties | 否 | 否 |
> | locations / backupcrossregionrestore | 否 | 否 |
> | locations / backupcrrjob | 否 | 否 |
> | locations / backupcrrjobs | 否 | 否 |
> | locations / backupcrroperationresults | 否 | 否 |
> | locations / backupcrroperationsstatus | 否 | 否 |
> | locations / backupprevalidateprotection | 否 | 否 |
> | locations / backupstatus | 否 | 否 |
> | locations / backupvalidatefeatures | 否 | 否 |
> | locations / checknameavailability | 否 | 否 |
> | 操作 | 否 | 否 |
> | replicationeligibilityresults | 否 | 否 |
> | vaults | 是 | 是 |

> [!IMPORTANT]
> 请参阅[恢复服务移动指南](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)。

## <a name="microsoftredhatopenshift"></a>Microsoft.RedHatOpenShift

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | 位置 | 否 | 否 |
> | locations / operationresults | 否 | 否 |
> | locations / operationsstatus | 否 | 否 |
> | openshiftclusters | 否 | 否 |
> | 操作 | 否 | 否 |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | checknameavailability | 否 | 否 |
> | namespaces | 是 | 是 |
> | namespaces / authorizationrules | 否 | 否 |
> | namespaces / hybridconnections | 否 | 否 |
> | namespaces / hybridconnections / authorizationrules | 否 | 否 |
> | namespaces / privateendpointconnections | 否 | 否 |
> | namespaces / wcfrelays | 否 | 否 |
> | namespaces / wcfrelays / authorizationrules | 否 | 否 |
> | 操作 | 否 | 否 |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | 操作 | 否 | 否 |
> | 查询 | 是 | 是 |
> | resourcechangedetails | 否 | 否 |
> | resourcechanges | 否 | 否 |
> | 资源 | 否 | 否 |
> | resourceshistory | 否 | 否 |
> | subscriptionsstatus | 否 | 否 |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | availabilitystatuses | 否 | 否 |
> | childavailabilitystatuses | 否 | 否 |
> | childresources | 否 | 否 |
> | emergingissues | 否 | 否 |
> | 活动 | 否 | 否 |
> | metadata | 否 | 否 |
> | 通知 | 否 | 否 |
> | 操作 | 否 | 否 |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | checkpolicycompliance | 否 | 否 |
> | checkresourcename | 否 | 否 |
> | deployments | 否 | 否 |
> | deployments / operations | 否 | 否 |
> | deploymentscripts | 否 | 否 |
> | deploymentscripts / logs | 否 | 否 |
> | 链接 | 否 | 否 |
> | 位置 | 否 | 否 |
> | locations / deploymentscriptoperationresults | 否 | 否 |
> | notifyresourcejobs | 否 | 否 |
> | operationresults | 否 | 否 |
> | 操作 | 否 | 否 |
> | providers | 否 | 否 |
> | resourcegroups | 否 | 否 |
> | 资源 | 否 | 否 |
> | subscriptions | 否 | 否 |
> | subscriptions / locations | 否 | 否 |
> | subscriptions / operationresults | 否 | 否 |
> | subscriptions / providers | 否 | 否 |
> | subscriptions / resourcegroups | 否 | 否 |
> | subscriptions / resourcegroups / resources | 否 | 否 |
> | subscriptions / resources | 否 | 否 |
> | subscriptions / tagnames | 否 | 否 |
> | subscriptions / tagnames / tagvalues | 否 | 否 |
> | 标记 | 否 | 否 |
> | templatespecs | 否 | 否 |
> | templatespecs / versions | 否 | 否 |
> | tenants | 否 | 否 |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | 服务 | 是 | 否 |
> | checkmoderneligibility | 否 | 否 |
> | checknameavailability | 否 | 否 |
> | operationresults | 否 | 否 |
> | 操作 | 否 | 否 |
> | saasresources | 否 | 否 |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | checknameavailability | 否 | 否 |
> | checkservicenameavailability | 否 | 否 |
> | 操作 | 否 | 否 |
> | resourcehealthmetadata | 否 | 否 |
> | searchservices | 是 | 是 |

> [!IMPORTANT]
> 不能一次性移动不同区域中的多个搜索资源。 只能通过多个单独的操作移动它们。

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | adaptivenetworkhardenings | 否 | 否 |
> | advancedthreatprotectionsettings | 否 | 否 |
> | alerts | 否 | 否 |
> | allowedconnections | 否 | 否 |
> | applicationwhitelistings | 否 | 否 |
> | assessmentmetadata | 否 | 否 |
> | assessments | 否 | 否 |
> | autodismissalertsrules | 否 | 否 |
> | automations | 是 | 是 |
> | autoprovisioningsettings | 否 | 否 |
> | complianceresults | 否 | 否 |
> | compliances | 否 | 否 |
> | datacollectionagents | 否 | 否 |
> | devicesecuritygroups | 否 | 否 |
> | discoveredsecuritysolutions | 否 | 否 |
> | externalsecuritysolutions | 否 | 否 |
> | informationprotectionpolicies | 否 | 否 |
> | iotsecuritysolutions | 是 | 是 |
> | iotsecuritysolutions / analyticsmodels | 否 | 否 |
> | iotsecuritysolutions / analyticsmodels / aggregatedalerts | 否 | 否 |
> | iotsecuritysolutions / analyticsmodels / aggregatedrecommendations | 否 | 否 |
> | jitnetworkaccesspolicies | 否 | 否 |
> | 位置 | 否 | 否 |
> | locations / alerts | 否 | 否 |
> | locations / allowedconnections | 否 | 否 |
> | locations / applicationwhitelistings | 否 | 否 |
> | locations / discoveredsecuritysolutions | 否 | 否 |
> | locations / externalsecuritysolutions | 否 | 否 |
> | locations / jitnetworkaccesspolicies | 否 | 否 |
> | locations / securitysolutions | 否 | 否 |
> | locations / securitysolutionsreferencedata | 否 | 否 |
> | locations / tasks | 否 | 否 |
> | locations / topologies | 否 | 否 |
> | 操作 | 否 | 否 |
> | 策略 | 否 | 否 |
> | pricings | 否 | 否 |
> | regulatorycompliancestandards | 否 | 否 |
> | regulatorycompliancestandards / regulatorycompliancecontrols | 否 | 否 |
> | regulatorycompliancestandards / regulatorycompliancecontrols / regulatorycomplianceassessments | 否 | 否 |
> | securitycontacts | 否 | 否 |
> | securitysolutions | 否 | 否 |
> | securitysolutionsreferencedata | 否 | 否 |
> | securitystatuses | 否 | 否 |
> | securitystatusessummaries | 否 | 否 |
> | servervulnerabilityassessments | 否 | 否 |
> | 设置 | 否 | 否 |
> | subassessments | 否 | 否 |
> | 任务 | 否 | 否 |
> | topologies | 否 | 否 |
> | workspacesettings | 否 | 否 |

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | aggregations | 否 | 否 |
> | alertrules | 否 | 否 |
> | alertruletemplates | 否 | 否 |
> | bookmarks | 否 | 否 |
> | cases | 否 | 否 |
> | dataconnectors | 否 | 否 |
> | dataconnectorscheckrequirements | 否 | 否 |
> | 实体 | 否 | 否 |
> | entityqueries | 否 | 否 |
> | incidents | 否 | 否 |
> | officeconsents | 否 | 否 |
> | 操作 | 否 | 否 |
> | 设置 | 否 | 否 |
> | threatintelligence | 否 | 否 |

## <a name="microsoftserialconsole"></a>Microsoft.SerialConsole

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | consoleservices | 否 | 否 |
> | 位置 | 否 | 否 |
> | locations / consoleservices | 否 | 否 |
> | 操作 | 否 | 否 |

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | gateways | 否 | 否 |
> | nodes | 否 | 否 |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | checknameavailability | 否 | 否 |
> | checknamespaceavailability | 否 | 否 |
> | 位置 | 否 | 否 |
> | locations / deletevirtualnetworkorsubnets | 否 | 否 |
> | namespaces | 是 | 是 |
> | namespaces / authorizationrules | 否 | 否 |
> | namespaces / disasterrecoveryconfigs | 否 | 否 |
> | namespaces / disasterrecoveryconfigs / checknameavailability | 否 | 否 |
> | namespaces / eventgridfilters | 否 | 否 |
> | namespaces / networkrulesets | 否 | 否 |
> | namespaces / queues | 否 | 否 |
> | namespaces / queues / authorizationrules | 否 | 否 |
> | namespaces / topics | 否 | 否 |
> | namespaces / topics / authorizationrules | 否 | 否 |
> | namespaces / topics / subscriptions | 否 | 否 |
> | namespaces / topics / subscriptions / rules | 否 | 否 |
> | 操作 | 否 | 否 |
> | premiummessagingregions | 否 | 否 |
> | sku | 否 | 否 |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | 服务 | 否 | 否 |
> | clusters | 是 | 是 |
> | clusters / applications | 否 | 否 |
> | containergroups | 否 | 否 |
> | containergroupsets | 否 | 否 |
> | edgeclusters | 否 | 否 |
> | 位置 | 否 | 否 |
> | locations / clusterversions | 否 | 否 |
> | locations / environments | 否 | 否 |
> | locations / operationresults | 否 | 否 |
> | locations / operations | 否 | 否 |
> | managedclusters | 否 | 否 |
> | networks | 否 | 否 |
> | 操作 | 否 | 否 |
> | secretstores | 否 | 否 |
> | volumes | 否 | 否 |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | 服务 | 是 | 是 |
> | containergroups | 否 | 否 |
> | gateways | 是 | 是 |
> | 位置 | 否 | 否 |
> | locations / applicationoperations | 否 | 否 |
> | locations / gatewayoperations | 否 | 否 |
> | locations / networkoperations | 否 | 否 |
> | locations / secretoperations | 否 | 否 |
> | locations / volumeoperations | 否 | 否 |
> | networks | 是 | 是 |
> | 操作 | 否 | 否 |
> | 机密 | 是 | 是 |
> | volumes | 是 | 是 |

## <a name="microsoftservices"></a>Microsoft.Services

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | providerregistrations | 否 | 否 |
> | providerregistrations / resourcetyperegistrations | 否 | 否 |
> | rollouts | 否 | 否 |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | 位置 | 否 | 否 |
> | locations / checknameavailability | 否 | 否 |
> | locations / operationresults | 否 | 否 |
> | locations / operationstatuses | 否 | 否 |
> | locations / usages | 否 | 否 |
> | 操作 | 否 | 否 |
> | signalr | 是 | 是 |
> | signalr / eventgridfilters | 否 | 否 |

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | hybridusebenefits | 否 | 否 |
> | 操作 | 否 | 否 |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | applicationdefinitions | 否 | 否 |
> | 服务 | 否 | 否 |
> | jitrequests | 否 | 否 |
> | 位置 | 否 | 否 |
> | locations / operationstatuses | 否 | 否 |
> | 操作 | 否 | 否 |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | checknameavailability | 否 | 否 |
> | instancepools | 否 | 否 |
> | 位置 | 是 | 是 |
> | 位置 | 否 | 否 |
> | locations / administratorazureasyncoperation | 否 | 否 |
> | locations / administratoroperationresults | 否 | 否 |
> | locations / auditingsettingsazureasyncoperation | 否 | 否 |
> | locations / auditingsettingsoperationresults | 否 | 否 |
> | locations / capabilities | 否 | 否 |
> | locations / databaseazureasyncoperation | 否 | 否 |
> | locations / databaseoperationresults | 否 | 否 |
> | locations / databaserestoreazureasyncoperation | 否 | 否 |
> | locations / deletevirtualnetworkorsubnets | 否 | 否 |
> | locations / deletevirtualnetworkorsubnetsazureasyncoperation | 否 | 否 |
> | locations / deletevirtualnetworkorsubnetsoperationresults | 否 | 否 |
> | locations / dnsaliasasyncoperation | 否 | 否 |
> | locations / dnsaliasoperationresults | 否 | 否 |
> | locations / elasticpoolazureasyncoperation | 否 | 否 |
> | locations / elasticpooloperationresults | 否 | 否 |
> | locations / encryptionprotectorazureasyncoperation | 否 | 否 |
> | locations / encryptionprotectoroperationresults | 否 | 否 |
> | locations / extendedauditingsettingsazureasyncoperation | 否 | 否 |
> | locations / extendedauditingsettingsoperationresults | 否 | 否 |
> | locations / failovergroupazureasyncoperation | 否 | 否 |
> | locations / failovergroupoperationresults | 否 | 否 |
> | locations / firewallrulesazureasyncoperation | 否 | 否 |
> | locations / firewallrulesoperationresults | 否 | 否 |
> | locations / instancefailovergroupazureasyncoperation | 否 | 否 |
> | locations / instancefailovergroupoperationresults | 否 | 否 |
> | locations / instancefailovergroups | 否 | 否 |
> | locations / instancepoolazureasyncoperation | 否 | 否 |
> | locations / instancepooloperationresults | 否 | 否 |
> | locations / jobagentazureasyncoperation | 否 | 否 |
> | locations / jobagentoperationresults | 否 | 否 |
> | locations / longtermretentionbackupazureasyncoperation | 否 | 否 |
> | locations / longtermretentionbackupoperationresults | 否 | 否 |
> | locations / longtermretentionbackups | 否 | 否 |
> | locations / longtermretentionmanagedinstancebackupazureasyncoperation | 否 | 否 |
> | locations / longtermretentionmanagedinstancebackupoperationresults | 否 | 否 |
> | locations / longtermretentionmanagedinstancebackups | 否 | 否 |
> | locations / longtermretentionmanagedinstances | 否 | 否 |
> | locations / longtermretentionpolicyazureasyncoperation | 否 | 否 |
> | locations / longtermretentionpolicyoperationresults | 否 | 否 |
> | locations / longtermretentionservers | 否 | 否 |
> | locations / manageddatabaseazureasyncoperation | 否 | 否 |
> | locations / manageddatabasecompleterestoreazureasyncoperation | 否 | 否 |
> | locations / manageddatabasecompleterestoreoperationresults | 否 | 否 |
> | locations / manageddatabaseoperationresults | 否 | 否 |
> | locations / manageddatabaserestoreazureasyncoperation | 否 | 否 |
> | locations / manageddatabaserestoreoperationresults | 否 | 否 |
> | locations / managedinstanceazureasyncoperation | 否 | 否 |
> | locations / managedinstanceencryptionprotectorazureasyncoperation | 否 | 否 |
> | locations / managedinstanceencryptionprotectoroperationresults | 否 | 否 |
> | locations / managedinstancekeyazureasyncoperation | 否 | 否 |
> | locations / managedinstancekeyoperationresults | 否 | 否 |
> | locations / managedinstancelongtermretentionpolicyazureasyncoperation | 否 | 否 |
> | locations / managedinstancelongtermretentionpolicyoperationresults | 否 | 否 |
> | locations / managedinstanceoperationresults | 否 | 否 |
> | locations / managedinstancetdecertazureasyncoperation | 否 | 否 |
> | locations / managedinstancetdecertoperationresults | 否 | 否 |
> | locations / managedserversecurityalertpoliciesazureasyncoperation | 否 | 否 |
> | locations / managedserversecurityalertpoliciesoperationresults | 否 | 否 |
> | locations / managedshorttermretentionpolicyazureasyncoperation | 否 | 否 |
> | locations / managedshorttermretentionpolicyoperationresults | 否 | 否 |
> | locations / notifyazureasyncoperation | 否 | 否 |
> | locations / privateendpointconnectionazureasyncoperation | 否 | 否 |
> | locations / privateendpointconnectionoperationresults | 否 | 否 |
> | locations / privateendpointconnectionproxyazureasyncoperation | 否 | 否 |
> | locations / privateendpointconnectionproxyoperationresults | 否 | 否 |
> | locations / securityalertpoliciesazureasyncoperation | 否 | 否 |
> | locations / securityalertpoliciesoperationresults | 否 | 否 |
> | locations / serveradministratorazureasyncoperation | 否 | 否 |
> | locations / serveradministratoroperationresults | 否 | 否 |
> | locations / serverazureasyncoperation | 否 | 否 |
> | locations / serverkeyazureasyncoperation | 否 | 否 |
> | locations / serverkeyoperationresults | 否 | 否 |
> | locations / serveroperationresults | 否 | 否 |
> | locations / shorttermretentionpolicyazureasyncoperation | 否 | 否 |
> | locations / shorttermretentionpolicyoperationresults | 否 | 否 |
> | locations / syncagentoperationresults | 否 | 否 |
> | locations / syncdatabaseids | 否 | 否 |
> | locations / syncgroupoperationresults | 否 | 否 |
> | locations / syncmemberoperationresults | 否 | 否 |
> | locations / tdecertazureasyncoperation | 否 | 否 |
> | locations / tdecertoperationresults | 否 | 否 |
> | locations / usages | 否 | 否 |
> | locations / virtualclusterazureasyncoperation | 否 | 否 |
> | locations / virtualclusteroperationresults | 否 | 否 |
> | locations / virtualnetworkrulesazureasyncoperation | 否 | 否 |
> | locations / virtualnetworkrulesoperationresults | 否 | 否 |
> | locations / vulnerabilityassessmentscanazureasyncoperation | 否 | 否 |
> | locations / vulnerabilityassessmentscanoperationresults | 否 | 否 |
> | managedinstances | 否 | 否 |
> | managedinstances / administrators | 否 | 否 |
> | managedinstances / databases | 否 | 否 |
> | managedinstances / databases / backuplongtermretentionpolicies | 否 | 否 |
> | managedinstances / databases / vulnerabilityassessments | 否 | 否 |
> | managedinstances / metricdefinitions | 否 | 否 |
> | managedinstances / metrics | 否 | 否 |
> | managedinstances / recoverabledatabases | 否 | 否 |
> | managedinstances / tdecertificates | 否 | 否 |
> | managedinstances / vulnerabilityassessments | 否 | 否 |
> | 操作 | 否 | 否 |
> | servers | 是 | 是 |
> | servers / administratoroperationresults | 否 | 否 |
> | servers / administrators | 否 | 否 |
> | servers / advisors | 否 | 否 |
> | servers / aggregateddatabasemetrics | 否 | 否 |
> | servers / auditingpolicies | 否 | 否 |
> | servers / auditingsettings | 否 | 否 |
> | servers / automatictuning | 否 | 否 |
> | servers / communicationlinks | 否 | 否 |
> | servers / connectionpolicies | 否 | 否 |
> | servers / databases | 是 | 是 |
> | servers / databases / advisors | 否 | 否 |
> | servers / databases / auditingpolicies | 否 | 否 |
> | servers / databases / auditingsettings | 否 | 否 |
> | servers / databases / auditrecords | 否 | 否 |
> | servers / databases / automatictuning | 否 | 否 |
> | servers / databases / backuplongtermretentionpolicies | 否 | 否 |
> | servers / databases / backupshorttermretentionpolicies | 否 | 否 |
> | servers / databases / connectionpolicies | 否 | 否 |
> | servers / databases / datamaskingpolicies | 否 | 否 |
> | servers / databases / datamaskingpolicies / rules | 否 | 否 |
> | servers / databases / extensions | 否 | 否 |
> | servers / databases / geobackuppolicies | 否 | 否 |
> | servers / databases / metricdefinitions | 否 | 否 |
> | servers / databases / metrics | 否 | 否 |
> | servers / databases / recommendedsensitivitylabels | 否 | 否 |
> | servers / databases / securityalertpolicies | 否 | 否 |
> | servers / databases / syncgroups | 否 | 否 |
> | servers / databases / syncgroups / syncmembers | 否 | 否 |
> | servers / databases / topqueries | 否 | 否 |
> | servers / databases / topqueries / querytext | 否 | 否 |
> | servers / databases / transparentdataencryption | 否 | 否 |
> | servers / databases / vulnerabilityassessment | 否 | 否 |
> | servers / databases / vulnerabilityassessments | 否 | 否 |
> | servers / databases / vulnerabilityassessmentscans | 否 | 否 |
> | servers / databases / vulnerabilityassessmentsettings | 否 | 否 |
> | servers / databases / workloadgroups | 否 | 否 |
> | servers / databasesecuritypolicies | 否 | 否 |
> | servers / disasterrecoveryconfiguration | 否 | 否 |
> | servers / dnsaliases | 否 | 否 |
> | servers / elasticpoolestimates | 否 | 否 |
> | servers / elasticpools | 是 | 是 |
> | servers / elasticpools / advisors | 否 | 否 |
> | servers / elasticpools / metricdefinitions | 否 | 否 |
> | servers / elasticpools / metrics | 否 | 否 |
> | servers / encryptionprotector | 否 | 否 |
> | servers / extendedauditingsettings | 否 | 否 |
> | servers / failovergroups | 否 | 否 |
> | servers / import | 否 | 否 |
> | servers / importexportoperationresults | 否 | 否 |
> | servers / jobaccounts | 是 | 是 |
> | servers / jobagents | 是 | 是 |
> | servers / jobagents / jobs | 否 | 否 |
> | servers / jobagents / jobs / executions | 否 | 否 |
> | servers / jobagents / jobs / steps | 否 | 否 |
> | servers / keys | 否 | 否 |
> | servers / operationresults | 否 | 否 |
> | servers / recommendedelasticpools | 否 | 否 |
> | servers / recoverabledatabases | 否 | 否 |
> | servers / restorabledroppeddatabases | 否 | 否 |
> | servers / securityalertpolicies | 否 | 否 |
> | servers / serviceobjectives | 否 | 否 |
> | servers / syncagents | 否 | 否 |
> | servers / tdecertificates | 否 | 否 |
> | servers / usages | 否 | 否 |
> | servers / virtualnetworkrules | 否 | 否 |
> | servers / vulnerabilityassessments | 否 | 否 |
> | virtualclusters | 是 | 是 |

> [!IMPORTANT]
> 数据库和服务器必须位于同一个资源组中。 当移动 SQL 服务器时，其所有数据库也会一起移动。 此行为适用于 Azure SQL 数据库和 Azure SQL 数据仓库数据库。

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | 位置 | 否 | 否 |
> | locations / availabilitygrouplisteneroperationresults | 否 | 否 |
> | locations / operationtypes | 否 | 否 |
> | locations / sqlvirtualmachinegroupoperationresults | 否 | 否 |
> | locations / sqlvirtualmachineoperationresults | 否 | 否 |
> | 操作 | 否 | 否 |
> | sqlvirtualmachinegroups | 是 | 是 |
> | sqlvirtualmachinegroups / availabilitygrouplisteners | 否 | 否 |
> | sqlvirtualmachines | 是 | 是 |

## <a name="microsoftsqlvm"></a>Microsoft.SqlVM

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | dwvm | 否 | 否 |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | checknameavailability | 否 | 否 |
> | 位置 | 否 | 否 |
> | locations / asyncoperations | 否 | 否 |
> | locations / checknameavailability | 否 | 否 |
> | locations / deletevirtualnetworkorsubnets | 否 | 否 |
> | locations / usages | 否 | 否 |
> | 操作 | 否 | 否 |
> | storageaccounts | 是 | 是 |
> | storageaccounts / blobservices | 否 | 否 |
> | storageaccounts / fileservices | 否 | 否 |
> | storageaccounts / listaccountsas | 否 | 否 |
> | storageaccounts / listservicesas | 否 | 否 |
> | storageaccounts / queueservices | 否 | 否 |
> | storageaccounts / services | 否 | 否 |
> | storageaccounts / services / metricdefinitions | 否 | 否 |
> | storageaccounts / tableservices | 否 | 否 |
> | usages | 否 | 否 |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | caches | 否 | 否 |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | 位置 | 否 | 否 |
> | locations / checknameavailability | 否 | 否 |
> | locations / workflows | 否 | 否 |
> | 操作 | 否 | 否 |
> | storagesyncservices | 是 | 是 |
> | storagesyncservices / registeredservers | 否 | 否 |
> | storagesyncservices / syncgroups | 否 | 否 |
> | storagesyncservices / syncgroups / cloudendpoints | 否 | 否 |
> | storagesyncservices / syncgroups / serverendpoints | 否 | 否 |
> | storagesyncservices / workflows | 否 | 否 |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | 否 | 否 |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | 否 | 否 |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | managers | 否 | 否 |
> | 操作 | 否 | 否 |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | clusters | 否 | 否 |
> | 位置 | 否 | 否 |
> | locations / quotas | 否 | 否 |
> | 操作 | 否 | 否 |
> | streamingjobs | 是 | 是 |

> [!IMPORTANT]
> 当流分析作业处于运行状态时，无法进行移动。

## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | environments | 否 | 否 |
> | environments / eventsources | 否 | 否 |
> | instances | 否 | 否 |
> | instances / environments | 否 | 否 |
> | instances / environments / eventsources | 否 | 否 |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | cancel | 否 | 否 |
> | createsubscription | 否 | 否 |
> | enable | 否 | 否 |
> | operationresults | 否 | 否 |
> | 操作 | 否 | 否 |
> | 重命名 | 否 | 否 |
> | subscriptiondefinitions | 否 | 否 |
> | subscriptionoperations | 否 | 否 |
> | subscriptions | 否 | 否 |

## <a name="microsoftsupport"></a>microsoft.support

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | checknameavailability | 否 | 否 |
> | operationresults | 否 | 否 |
> | 操作 | 否 | 否 |
> | operationsstatus | 否 | 否 |
> | services | 否 | 否 |
> | services / problemclassifications | 否 | 否 |
> | supporttickets | 否 | 否 |

## <a name="microsoftsynapse"></a>Microsoft.Synapse

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | checknameavailability | 否 | 否 |
> | 操作 | 否 | 否 |
> | workspaces | 是 | 是 |
> | workspaces / bigdatapools | 是 | 是 |
> | workspaces / operationresults | 否 | 否 |
> | workspaces / operationstatuses | 否 | 否 |
> | workspaces / sqlpools | 是 | 是 |

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | providerregistrations | 否 | 否 |
> | 资源 | 否 | 否 |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | environments | 是 | 是 |
> | environments / accesspolicies | 否 | 否 |
> | environments / eventsources | 是 | 是 |
> | environments / referencedatasets | 是 | 是 |
> | 操作 | 否 | 否 |

## <a name="microsofttoken"></a>Microsoft.Token

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | stores | 是 | 是 |
> | stores / accesspolicies | 否 | 否 |
> | stores / services | 否 | 否 |
> | stores / services / tokens | 否 | 否 |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | imagetemplates | 否 | 否 |
> | 操作 | 否 | 否 |

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | account | 否 | 否 |
> | account / extension | 否 | 否 |
> | account / project | 否 | 否 |
> | checknameavailability | 否 | 否 |
> | 操作 | 否 | 否 |

> [!IMPORTANT]
> 若要更改 Azure DevOps 的订阅，请参阅[更改用于计费的 Azure 订阅](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)。

## <a name="microsoftvmware"></a>Microsoft.VMware

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | arczones | 否 | 否 |
> | 位置 | 否 | 否 |
> | locations / operationstatuses | 否 | 否 |
> | 操作 | 否 | 否 |
> | resourcepools | 否 | 否 |
> | vcenters | 否 | 否 |
> | virtualmachines | 否 | 否 |
> | virtualmachinetemplates | 否 | 否 |
> | virtualnetworks | 否 | 否 |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | dedicatedcloudnodes | 否 | 否 |
> | dedicatedcloudservices | 否 | 否 |
> | 位置 | 否 | 否 |
> | locations / availabilities | 否 | 否 |
> | locations / operationresults | 否 | 否 |
> | locations / privateclouds | 否 | 否 |
> | locations / privateclouds / resourcepools | 否 | 否 |
> | locations / privateclouds / virtualmachinetemplates | 否 | 否 |
> | locations / privateclouds / virtualnetworks | 否 | 否 |
> | locations / usages | 否 | 否 |
> | 操作 | 否 | 否 |
> | virtualmachines | 否 | 否 |

## <a name="microsoftvnfmanager"></a>Microsoft.VnfManager

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | devices | 否 | 否 |
> | 位置 | 否 | 否 |
> | locations / operationstatuses | 否 | 否 |
> | 操作 | 否 | 否 |
> | vnfs | 否 | 否 |

## <a name="microsoftvsonline"></a>Microsoft.VSOnline

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | accounts | 否 | 否 |
> | 操作 | 否 | 否 |
> | plans | 否 | 否 |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | availablestacks | 否 | 否 |
> | billingmeters | 否 | 否 |
> | certificates | 否 | 是 |
> | checknameavailability | 否 | 否 |
> | connectiongateways | 是 | 是 |
> | connections | 是 | 是 |
> | customapis | 是 | 是 |
> | deletedsites | 否 | 否 |
> | deploymentlocations | 否 | 否 |
> | georegions | 否 | 否 |
> | hostingenvironments | 否 | 否 |
> | hostingenvironments / eventgridfilters | 否 | 否 |
> | hostingenvironments / multirolepools | 否 | 否 |
> | hostingenvironments / workerpools | 否 | 否 |
> | ishostingenvironmentnameavailable | 否 | 否 |
> | ishostnameavailable | 否 | 否 |
> | isusernameavailable | 否 | 否 |
> | kubeenvironments | 是 | 是 |
> | listsitesassignedtohostname | 否 | 否 |
> | 位置 | 否 | 否 |
> | locations / apioperations | 否 | 否 |
> | locations / connectiongatewayinstallations | 否 | 否 |
> | locations / deletedsites | 否 | 否 |
> | locations / deletevirtualnetworkorsubnets | 否 | 否 |
> | locations / extractapidefinitionfromwsdl | 否 | 否 |
> | locations / getnetworkpolicies | 否 | 否 |
> | locations / listwsdlinterfaces | 否 | 否 |
> | locations / managedapis | 否 | 否 |
> | locations / operationresults | 否 | 否 |
> | locations / operations | 否 | 否 |
> | locations / runtimes | 否 | 否 |
> | 操作 | 否 | 否 |
> | publishingusers | 否 | 否 |
> | 建议 | 否 | 否 |
> | resourcehealthmetadata | 否 | 否 |
> | runtimes | 否 | 否 |
> | serverfarms | 是 | 是 |
> | serverfarms / eventgridfilters | 否 | 否 |
> | sites | 是 | 是 |
> | sites / eventgridfilters | 否 | 否 |
> | sites / hostnamebindings | 否 | 否 |
> | sites / networkconfig | 否 | 否 |
> | sites / premieraddons | 是 | 是 |
> | sites / slots | 是 | 是 |
> | sites / slots / eventgridfilters | 否 | 否 |
> | sites / slots / hostnamebindings | 否 | 否 |
> | sites / slots / networkconfig | 否 | 否 |
> | sourcecontrols | 否 | 否 |
> | staticsites | 否 | 否 |
> | validate | 否 | 否 |
> | verifyhostingenvironmentvnet | 否 | 否 |

> [!IMPORTANT]
> 请参阅[应用服务移动指南](./move-limitations/app-service-move-limitations.md)。

## <a name="microsoftwindowsesu"></a>Microsoft.WindowsESU

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | 位置 | 否 | 否 |
> | locations / operationstatuses | 否 | 否 |
> | multipleactivationkeys | 否 | 否 |
> | 操作 | 否 | 否 |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | deviceservices | 否 | 否 |
> | 操作 | 否 | 否 |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | 资源类型 | 资源组 | 订阅 |
> | ------------- | ----------- | ---------- |
> | components | 否 | 否 |
> | componentssummary | 否 | 否 |
> | monitorinstances | 否 | 否 |
> | monitorinstancessummary | 否 | 否 |
> | monitors | 否 | 否 |
> | notificationsettings | 否 | 否 |
> | 操作 | 否 | 否 |

## <a name="third-party-services"></a>第三方服务

第三方服务当前不支持移动操作。

## <a name="next-steps"></a>后续步骤
有关移动资源的命令，请参阅[将资源移动至新资源组或订阅](move-resource-group-and-subscription.md)。

若要以逗号分隔值文件的形式获取同一数据，请下载 [move-support-resources.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv)。
