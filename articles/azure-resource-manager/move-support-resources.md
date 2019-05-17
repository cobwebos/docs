---
title: Azure 资源类型支持的移动操作
description: 列出了可移动到新资源组或订阅的 Azure 资源类型。
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 5/16/2019
ms.author: tomfitz
ms.openlocfilehash: d662607eaaabb8ccfad89f625165c542af81b0fa
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2019
ms.locfileid: "65794523"
---
# <a name="move-operation-support-for-resources"></a>支持移动操作的资源
本文列出了 Azure 资源类型是否支持移动操作。 尽管资源类型支持移动操作，但可能有阻止资源移动的情况。 有关影响移动操作的情况的详细信息，请参阅[将资源移动至新资源组或订阅](resource-group-move-resources.md)。

若要以逗号分隔值文件的形式获取同一数据，请下载 [move-support-resources.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv)。

## <a name="microsoftaad"></a>Microsoft.AAD
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| domainservices | 否 | 否 |

## <a name="microsoftaadiam"></a>microsoft.aadiam
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| tenants | 否 | 否 |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| actionrules | 是 | 是 |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| servers | 是 | 是 |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| 服务 | 是 | 是 |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| configurationstores | 是 | 是 |

## <a name="microsoftappservice"></a>Microsoft.AppService
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| apiapps | 否 | 否 |
| appidentities | 否 | 否 |
| gateways | 否 | 否 |

## <a name="microsoftauthorization"></a>Microsoft.Authorization
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| policyassignments | 否 | 否 |

## <a name="microsoftautomation"></a>Microsoft.Automation
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| automationaccounts | 是 | 是 |
| automationaccounts/configurations | 是 | 是 |
| automationaccounts/runbooks | 是 | 是 |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| b2cdirectories | 是 | 是 |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| registrations | 是 | 是 |

## <a name="microsoftbackup"></a>Microsoft.Backup
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| backupvault | 否 | 否 |

## <a name="microsoftbatch"></a>Microsoft.Batch
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| batchaccounts | 是 | 是 |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| clusters | 否 | 否 |
| fileservers | 否 | 否 |
| jobs | 否 | 否 |
| 工作区 | 否 | 否 |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| mapapis | 否 | 否 |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| biztalk | 是 | 是 |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| blockchainmembers | 是 | 是 |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| blueprintassignments | 否 | 否 |

## <a name="microsoftbotservice"></a>Microsoft.BotService
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| botservices | 是 | 是 |

## <a name="microsoftcache"></a>Microsoft.Cache
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| redis | 是 | 是 |

## <a name="microsoftcdn"></a>Microsoft.Cdn
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| 配置文件 | 是 | 是 |
| profiles/endpoints | 是 | 是 |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| certificateorders | 是 | 是 |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| domainnames | 是 | 否 |
| virtualmachines | 是 | 否 |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| networksecuritygroups | 否 | 否 |
| reservedips | 否 | 否 |
| virtualnetworks | 否 | 否 |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| storageaccounts | 是 | 否 |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| accounts | 是 | 是 |

## <a name="microsoftcompute"></a>Microsoft.Compute
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| availabilitysets | 是 | 是 |
| disks | 是 | 是 |
| galleries | 否 | 否 |
| galleries/images | 否 | 否 |
| galleries/images/versions | 否 | 否 |
| hostgroups | 否 | 否 |
| hostgroups/主机 | 否 | 否 |
| images | 是 | 是 |
| proximityplacementgroups | 否 | 否 |
| restorepointcollections | 否 | 否 |
| sharedvmimages | 否 | 否 |
| sharedvmimages/versions | 否 | 否 |
| snapshots | 是 | 是 |
| virtualmachines | 是 | 是 |
| virtualmachines/extensions | 是 | 是 |
| virtualmachinescalesets | 是 | 是 |

## <a name="microsoftcontainer"></a>Microsoft.Container
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| containergroups | 否 | 否 |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| containergroups | 否 | 否 |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| registries | 是 | 是 |
| registries/buildtasks | 是 | 是 |
| registries/replications | 是 | 是 |
| registries/tasks | 是 | 是 |
| registries/webhooks | 是 | 是 |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| containerservices | 否 | 否 |
| managedclusters | 否 | 否 |
| openshiftmanagedclusters | 否 | 否 |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| 服务 | 是 | 是 |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| accounts | 否 | 否 |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| 连接器 | 是 | 是 |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| hubs | 是 | 是 |

## <a name="microsoftdatabox"></a>Microsoft.DataBox
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| jobs | 否 | 否 |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| databoxedgedevices | 否 | 否 |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| 工作区 | 否 | 否 |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| catalogs | 是 | 是 |
| datacatalogs | 否 | 否 |

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| connectionmanagers | 否 | 否 |

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| packages | 否 | 否 |
| plans | 否 | 否 |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| datafactories | 是 | 是 |
| factories | 是 | 是 |

## <a name="microsoftdatalake"></a>Microsoft.DataLake
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| datalakeaccounts | 否 | 否 |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| accounts | 是 | 是 |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| accounts | 是 | 是 |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| services | 否 | 否 |
| services/projects | 否 | 否 |
| slots | 否 | 否 |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| servers | 是 | 是 |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| servers | 是 | 是 |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| servergroups | 否 | 否 |
| servers | 是 | 是 |
| serversv2 | 是 | 是 |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| artifactsources | 是 | 是 |
| rollouts | 是 | 是 |
| servicetopologies | 是 | 是 |
| servicetopologies/services | 是 | 是 |
| servicetopologies/services/serviceunits | 是 | 是 |
| steps | 是 | 是 |

## <a name="microsoftdevices"></a>Microsoft.Devices
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| elasticpools | 否 | 否 |
| elasticpools/iothubtenants | 否 | 否 |
| iothubs | 是 | 是 |
| provisioningservices | 是 | 是 |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| 控制器 | 否 | 否 |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| labcenters | 否 | 否 |
| labs | 是 | 否 |
| 实验室/环境 | 是 | 是 |
| labs/servicerunners | 是 | 是 |
| labs/virtualmachines | 是 | 否 |
| schedules | 是 | 是 |

## <a name="microsoftdns"></a>microsoft.dns
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| dnszones | 否 | 否 |
| dnszones/a | 否 | 否 |
| dnszones/aaaa | 否 | 否 |
| dnszones/cname | 否 | 否 |
| dnszones/mx | 否 | 否 |
| dnszones/ptr | 否 | 否 |
| dnszones/srv | 否 | 否 |
| dnszones/txt | 否 | 否 |
| trafficmanagerprofiles | 否 | 否 |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| databaseaccounts | 是 | 是 |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| domains | 是 | 是 |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| services | 是 | 是 |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| domains | 是 | 是 |
| topics | 是 | 是 |

## <a name="microsofteventhub"></a>Microsoft.EventHub
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| clusters | 是 | 是 |
| namespaces | 是 | 是 |

## <a name="microsoftgenomics"></a>Microsoft.Genomics
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| accounts | 否 | 否 |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| hanainstances | 是 | 是 |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| clusters | 是 | 是 |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| services | 是 | 是 |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| 计算机 | 否 | 否 |

## <a name="microsofthybriddata"></a>Microsoft.HybridData
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| datamanagers | 是 | 是 |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| jobs | 是 | 是 |

## <a name="microsoftinsights"></a>microsoft.insights
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| accounts | 否 | 否 |
| actiongroups | 是 | 是 |
| activitylogalerts | 否 | 否 |
| alertrules | 是 | 是 |
| autoscalesettings | 是 | 是 |
| components | 是 | 是 |
| guestdiagnosticsettings | 否 | 否 |
| metricalerts | 否 | 否 |
| notificationgroups | 否 | 否 |
| notificationrules | 否 | 否 |
| scheduledqueryrules | 否 | 否 |
| webtests | 是 | 是 |
| workbooks | 是 | 是 |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| iotapps | 是 | 是 |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| checknameavailability | 是 | 是 |
| graph | 是 | 是 |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| hsmpools | 否 | 否 |
| vaults | 是 | 是 |

## <a name="microsoftkusto"></a>Microsoft.Kusto
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| clusters | 是 | 是 |

## <a name="microsoftlabservices"></a>Microsoft.LabServices
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| labaccounts | 是 | 是 |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| accounts | 是 | 是 |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| accounts | 否 | 否 |

## <a name="microsoftlogic"></a>Microsoft.Logic
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| hostingenvironments | 否 | 否 |
| integrationaccounts | 是 | 是 |
| integrationserviceenvironments | 否 | 否 |
| isolatedenvironments | 否 | 否 |
| workflows | 是 | 是 |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| commitmentplans | 是 | 是 |
| webservices | 是 | 否 |
| 工作区 | 是 | 是 |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| operationalizationclusters | 是 | 是 |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| accounts | 否 | 否 |
| accounts/workspaces | 否 | 否 |
| accounts/workspaces/projects | 否 | 否 |
| teamaccounts | 否 | 否 |
| teamaccounts/workspaces | 否 | 否 |
| teamaccounts/workspaces/projects | 否 | 否 |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| accounts | 是 | 是 |

## <a name="microsoftmachinelearningoperationalization"></a>Microsoft.MachineLearningOperationalization
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| hostingaccounts | 否 | 否 |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| 工作区 | 否 | 否 |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| userassignedidentities | 否 | 否 |

## <a name="microsoftmaps"></a>Microsoft.Maps
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| accounts | 是 | 是 |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| classicdevservices | 否 | 否 |

## <a name="microsoftmedia"></a>Microsoft.Media
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| mediaservices | 是 | 是 |
| mediaservices/liveevents | 是 | 是 |
| mediaservices/streamingendpoints | 是 | 是 |

## <a name="microsoftmigrate"></a>Microsoft.Migrate
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| assessmentprojects | 否 | 否 |
| migrateprojects | 否 | 否 |
| projects | 否 | 否 |

## <a name="microsoftnetapp"></a>Microsoft.NetApp
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| netappaccounts | 否 | 否 |
| netappaccounts/capacitypools | 否 | 否 |
| netappaccounts/capacitypools/volumes | 否 | 否 |
| netappaccounts/capacitypools/volumes/mounttargets | 否 | 否 |
| netappaccounts/capacitypools/volumes/snapshots | 否 | 否 |

## <a name="microsoftnetwork"></a>Microsoft.Network
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| applicationgateways | 否 | 否 |
| applicationgatewaywebapplicationfirewallpolicies | 否 | 否 |
| applicationsecuritygroups | 是 | 是 |
| azurefirewalls | 是 | 是 |
| bastionhosts | 否 | 否 |
| connections | 是 | 是 |
| ddoscustompolicies | 是 | 是 |
| ddosprotectionplans | 否 | 否 |
| dnszones | 是 | 是 |
| expressroutecircuits | 否 | 否 |
| expressroutecrossconnections | 否 | 否 |
| expressroutegateways | 否 | 否 |
| expressrouteports | 否 | 否 |
| frontdoors | 是 | 是 |
| frontdoorwebapplicationfirewallpolicies | 是 | 是 |
| loadbalancers | 是 | 是 |
| localnetworkgateways | 是 | 是 |
| natgateways | 是 | 是 |
| networkintentpolicies | 是 | 是 |
| networkinterfaces | 是 | 是 |
| networkprofiles | 否 | 否 |
| networksecuritygroups | 是 | 是 |
| networkwatchers | 是 | 是 |
| networkwatchers/connectionmonitors | 是 | 是 |
| networkwatchers/lenses | 是 | 是 |
| networkwatchers/pingmeshes | 是 | 是 |
| p2svpngateways | 否 | 否 |
| privatednszones | 是 | 是 |
| privatednszones/virtualnetworklinks | 是 | 是 |
| privateendpoints | 否 | 否 |
| privatelinkservices | 否 | 否 |
| publicipaddresses | 是 | 是 |
| publicipprefixes | 是 | 是 |
| routefilters | 否 | 否 |
| routetables | 是 | 是 |
| securegateways | 是 | 是 |
| serviceendpointpolicies | 是 | 是 |
| trafficmanagerprofiles | 是 | 是 |
| virtualhubs | 否 | 否 |
| virtualnetworkgateways | 是 | 是 |
| virtualnetworks | 是 | 是 |
| virtualnetworktaps | 否 | 否 |
| virtualwans | 否 | 否 |
| vpngateways | 否 | 否 |
| vpnsites | 否 | 否 |
| webapplicationfirewallpolicies | 是 | 是 |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| namespaces | 是 | 是 |
| namespaces/notificationhubs | 是 | 是 |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| 工作区 | 是 | 是 |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| managementconfigurations | 是 | 是 |
| 解决方案 | 是 | 是 |
| 视图 | 是 | 是 |

## <a name="microsoftpeering"></a>Microsoft.Peering
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| 对等 | 否 | 否 |

## <a name="microsoftportal"></a>Microsoft.Portal
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| dashboards | 是 | 是 |

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| rootresources | 否 | 否 |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| workspacecollections | 是 | 是 |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| capacities | 是 | 是 |

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| accounts | 否 | 否 |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| vaults | 是 | 是 |

## <a name="microsoftrelay"></a>Microsoft.Relay
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| namespaces | 是 | 是 |

## <a name="microsoftsaas"></a>Microsoft.SaaS
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| 服务 | 是 | 否 |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| flows | 是 | 是 |
| jobcollections | 是 | 是 |

## <a name="microsoftsearch"></a>Microsoft.Search
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| searchservices | 是 | 是 |

## <a name="microsoftsecurity"></a>Microsoft.Security
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| iotsecuritysolutions | 是 | 是 |

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| gateways | 否 | 否 |
| nodes | 否 | 否 |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| namespaces | 是 | 是 |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| 服务 | 否 | 否 |
| clusters | 是 | 是 |
| containergroups | 否 | 否 |
| containergroupsets | 否 | 否 |
| edgeclusters | 否 | 否 |
| networks | 否 | 否 |
| secretstores | 否 | 否 |
| volumes | 否 | 否 |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| 服务 | 是 | 是 |
| containergroups | 否 | 否 |
| gateways | 是 | 是 |
| networks | 是 | 是 |
| 机密 | 是 | 是 |
| volumes | 是 | 是 |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| signalr | 是 | 是 |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| siterecoveryvault | 否 | 否 |

## <a name="microsoftsolutions"></a>Microsoft.Solutions
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| appliancedefinitions | 否 | 否 |
| appliances | 否 | 否 |
| applicationdefinitions | 否 | 否 |
| 服务 | 否 | 否 |
| jitrequests | 否 | 否 |

## <a name="microsoftsql"></a>Microsoft.Sql
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| instancepools | 是 | 是 |
| managedinstances | 是 | 是 |
| managedinstances/databases | 是 | 是 |
| servers | 是 | 是 |
| servers/databases | 是 | 是 |
| servers/elasticpools | 是 | 是 |
| virtualclusters | 是 | 是 |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| sqlvirtualmachinegroups | 是 | 是 |
| sqlvirtualmachines | 是 | 是 |

## <a name="microsoftsqlvm"></a>Microsoft.SqlVM
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| dwvm | 否 | 否 |

## <a name="microsoftstorage"></a>Microsoft.Storage
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| storageaccounts | 是 | 是 |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| 缓存 | 否 | 否 |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| storagesyncservices | 是 | 是 |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| storagesyncservices | 否 | 否 |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| storagesyncservices | 否 | 否 |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| managers | 否 | 否 |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| streamingjobs | 是 | 是 |

## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| environments | 否 | 否 |
| environments/eventsources | 否 | 否 |
| instances | 否 | 否 |
| instances/environments | 否 | 否 |
| instances/environments/eventsources | 否 | 否 |

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| providerregistrations | 否 | 否 |
| 资源 | 否 | 否 |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| environments | 是 | 是 |
| environments/eventsources | 是 | 是 |
| environments/referencedatasets | 是 | 是 |

## <a name="microsofttoken"></a>Microsoft.Token
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| 将存储 | 否 | 否 |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| imagetemplates | 否 | 否 |

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| 帐户 | 是 | 是 |
| account/extension | 是 | 是 |
| account/project | 是 | 是 |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| dedicatedcloudnodes | 是 | 是 |
| dedicatedcloudservices | 是 | 是 |
| virtualmachines | 是 | 是 |

## <a name="microsoftweb"></a>Microsoft.Web
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| certificates | 否 | 是 |
| connectiongateways | 是 | 是 |
| connections | 是 | 是 |
| customapis | 是 | 是 |
| hostingenvironments | 否 | 否 |
| serverfarms | 是 | 是 |
| sites | 是 | 是 |
| sites/premieraddons | 是 | 是 |
| sites/slots | 是 | 是 |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| deviceservices | 是 | 是 |

## <a name="microsoftwindowsvirtualdesktop"></a>Microsoft.WindowsVirtualDesktop
| 资源类型 | 资源组 | 订阅 |
| ------------- | ----------- | ---------- |
| applicationgroups | 否 | 否 |
| hostpools | 否 | 否 |
| 工作区 | 否 | 否 |

## <a name="third-party-services"></a>第三方服务

第三方服务当前不支持移动操作。

## <a name="next-steps"></a>后续步骤
有关移动资源的命令，请参阅[将资源移动至新资源组或订阅](resource-group-move-resources.md)。
