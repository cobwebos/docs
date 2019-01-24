---
title: Azure 资源类型支持的移动操作 | Microsoft Docs
description: 列出了可移动到新资源组或订阅的 Azure 资源类型。
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: tomfitz
ms.openlocfilehash: c16a0eeb674e712ec1c3678a2e0002a8ddcfc329
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2019
ms.locfileid: "54464694"
---
# <a name="move-operation-support-for-resources"></a>支持移动操作的资源

本文列出了 Azure 资源类型是否支持移动操作。 尽管资源类型支持移动操作，但可能有阻止资源移动的情况。 有关影响移动操作的情况的详细信息，请参阅[将资源移动至新资源组或订阅](resource-group-move-resources.md)。

## <a name="find-resource-provider-and-resource-type"></a>查找资源提供程序和资源类型

若要确定是否可移动某个资源，你必须找到其资源提供程序和资源类型。

对于 PowerShell，请使用：

```azurepowershell-interactive
Get-AzureRmResource -ResourceGroupName demogroup | Select Name, ResourceType | Format-table
```

对于 Azure CLI，请使用：

```azurecli-interactive
az resource list -g demogroup --query '[].{name:name, resourceType:type}' --output table
```

返回的资源类型格式为 `<resource-provider>/<resource-type-name>`。 因此，该值 `Microsoft.OperationalInsights/workspaces` 中显示的资源提供程序为 Microsoft.OperationalInsights，资源类型名称为 workspaces。

找到资源提供程序和资源类型后，使用本文中的表来确定资源类型是否支持移动操作。

## <a name="microsoftaad"></a>Microsoft.AAD

| 资源类型 | 资源组 | 订阅 |
| ------------- | --------------- | ----------- |
| domainservices | 否 | 否 |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices
| 资源类型 | 资源组 | 订阅 |
| ------------- | -------------- | ------------ |
| servers | 是 | 是 |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement
| 资源类型 | 资源组 | 订阅 |
| ------------- | --------------- | ----------- |
| 服务 | 是 | 是 |

## <a name="microsoftauthorization"></a>Microsoft.Authorization
| 资源类型 | 资源组 | 订阅 |
| ------------- | --------------- | ----------- |
| policyassignments | 否 | 否 |

## <a name="microsoftautomation"></a>Microsoft.Automation
| 资源类型 | 资源组 | 订阅 |
| ------------- | -------------- | ------------ |
| automationaccounts | 是 | 是 |
| automationaccounts/configurations | 是 | 是 |
| automationaccounts/runbooks | 是 | 是 |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory
| 资源类型 | 资源组 | 订阅 |
| ------------- | -------------- | ------------ |
| b2cdirectories | 是 | 是 |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack
| 资源类型 | 资源组 | 订阅 |
| ------------- | -------------- | ------------ |
| registrations | 是 | 是 |

## <a name="microsoftbackup"></a>Microsoft.Backup
| 资源类型 | 资源组 | 订阅 |
| ------------- | -------------- | ------------ |
| backupvault | 否 | 否 |

## <a name="microsoftbatch"></a>Microsoft.Batch
| 资源类型 | 资源组 | 订阅 |
| ------------- | -------------- | ------------ |
| batchaccounts | 是 | 是 |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps
| 资源类型 | 资源组 | 订阅 |
| ------------- | -------------- | ------------ |
| mapapis | 否 | 否 |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices
| 资源类型 | 资源组 | 订阅 |
| ------------- | -------------- | ------------ |
| biztalk | 是 | 是 |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint
| 资源类型 | 资源组 | 订阅 |
| ------------- | -------------- | ------------ |
| blueprintassignments | 否 | 否 |

## <a name="microsoftbotservice"></a>Microsoft.BotService
| 资源类型 | 资源组 | 订阅 |
| ------------- | -------------- | ------------ |
| botservices | 是 | 是 |

## <a name="microsoftcache"></a>Microsoft.Cache
| 资源类型 | 资源组 | 订阅 |
| ------------- | -------------- | ------------ |
| redis | 是 | 是 |

## <a name="microsoftcdn"></a>Microsoft.Cdn
| 资源类型 | 资源组 | 订阅 |
| ------------- | -------------- | ------------ |
| 配置文件 | 是 | 是 |
| profiles/endpoints | 是 | 是 |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration
| 资源类型 | 资源组 | 订阅 |
| ------------- | -------------- | ------------ |
| certificateorders | 是 | 是 |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute
| 资源类型 | 资源组 | 订阅 |
| ------------- | -------------- | ------------ |
| domainnames | 是 | 否 |
| virtualmachines | 是 | 否 |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork
| 资源类型 | 资源组 | 订阅 |
| ------------- | -------------- | ------------ |
| networksecuritygroups | 否 | 否 |
| reservedips | 否 | 否 |
| virtualnetworks | 否 | 否 |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage
| 资源类型 | 资源组 | 订阅 |
| ------------- | -------------- | ------------ |
| storageaccounts | 是 | 否 |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices
| 资源类型 | 资源组 | 订阅 |
| ------------- | -------------- | ------------ |
| accounts | 是 | 是 |

## <a name="microsoftcompute"></a>Microsoft.Compute
| 资源类型 | 资源组 | 订阅 |
| ------------- | -------------- | ------------ |
| availabilitysets | 是 | 是 |
| disks | 是 | 是 |
| galleries | 否 | 否 |
| galleries/images | 否 | 否 |
| galleries/images/versions | 否 | 否 |
| images | 是 | 是 |
| restorepointcollections | 否 | 否 |
| sharedvmimages | 否 | 否 |
| sharedvmimages/versions | 否 | 否 |
| snapshots | 是 | 是 |
| virtualmachines | 是 | 是 |
| virtualmachines/extensions | 是 | 是 |
| virtualmachinescalesets | 是 | 是 |

## <a name="microsoftcontainer"></a>Microsoft.Container
| 资源类型 | 资源组 | 订阅 |
| ------------- | -------------- | ------------ |
| containergroups | 否 | 否 |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance
| 资源类型 | 资源组 | 订阅 |
| ------------- | -------------- | ------------ |
| containergroups | 否 | 否 |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry
| 资源类型 | 资源组 | 订阅 |
| ------------- | -------------- | ------------ |
| registries | 是 | 是 |
| registries/buildtasks | 是 | 是 |
| registries/replications | 否 | 否 |
| registries/tasks | 是 | 是 |
| registries/webhooks | 是 | 是 |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService
| 资源类型 | 资源组 | 订阅 |
| ------------- | -------------- | ------------ |
| containerservices | 否 | 否 |
| managedclusters | 否 | 否 |
| openshiftmanagedclusters | 否 | 否 |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator
| 资源类型 | 资源组 | 订阅 |
| ------------- | -------------- | ------------ |
| 服务 | 是 | 是 |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement
| 资源类型 | 资源组 | 订阅 |
| ------------- | -------------- | ------------ |
| 连接器 | 是 | 是 |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights
| 资源类型 | 资源组 | 订阅 |
| ------------- | -------------- | ------------ |
| hubs | 是 | 是 |

## <a name="microsoftdatabox"></a>Microsoft.DataBox
| 资源类型 | 资源组 | 订阅 |
| ------------- | -------------- | ------------ |
| jobs | 否 | 否 |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge
| 资源类型 | 资源组 | 订阅 |
| ------------- | -------------- | ------------ |
| databoxedgedevices | 否 | 否 |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks
| 资源类型 | 资源组 | 订阅 |
| ------------- | -------------- | ------------ |
| workspaces | 否 | 否 |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog
| 资源类型 | 资源组 | 订阅 |
| ------------- | -------------- | ------------ |
| catalogs | 是 | 是 |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory
| 资源类型 | 资源组 | 订阅 |
| ------------- | -------------- | ------------ |
| datafactories | 是 | 是 |
| factories | 是 | 是 |

## <a name="microsoftdatalake"></a>Microsoft.DataLake
| 资源类型 | 资源组 | 订阅 |
| ------------- | -------------- | ------------ |
| datalakeaccounts | 否 | 否 |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics
| 资源类型 | 资源组 | 订阅 |
| ------------- | -------------- | ------------ |
| accounts | 是 | 是 |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore
| 资源类型 | 资源组 | 订阅 |
| ------------- | -------------- | ------------ |
| accounts | 是 | 是 |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration
| 资源类型 | 资源组 | 订阅 |
| ------------- | -------------- | ------------ |
| services | 否 | 否 |
| services/projects | 否 | 否 |
| slots | 否 | 否 |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB
| 资源类型 | 资源组 | 订阅 |
| ------------- | -------------- | ------------ |
| servers | 否 | 否 |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL
| 资源类型 | 资源组 | 订阅 |
| ------------- | -------------- | ------------ |
| servers | 是 | 是 |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL
| 资源类型 | 资源组 | 订阅 |
| ------------- | -------------- | ------------ |
| servergroups | 否 | 否 |
| servers | 是 | 是 |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager
| 资源类型 | 资源组 | 订阅 |
| ------------- | -------------- | ------------ |
| artifactsources | 否 | 否 |
| rollouts | 否 | 否 |
| servicetopologies | 否 | 否 |
| servicetopologies/services | 否 | 否 |
| servicetopologies/services/serviceunits | 否 | 否 |

## <a name="microsoftdevices"></a>Microsoft.Devices
| 资源类型 | 资源组 | 订阅 |
| ------------- | -------------- | ------------ |
| iothubs | 是 | 是 |
| provisioningservices | 是 | 是 |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab
| 资源类型 | 资源组 | 订阅 |
| ------------- | -------------- | ------------ |
| labcenters | 否 | 否 |
| labs | 是 | 否 |
| labs/servicerunners | 是 | 是 |
| labs/virtualmachines | 是 | 否 |
| schedules | 否 | 否 |

## <a name="microsoftdns"></a>microsoft.dns
| 资源类型 | 资源组 | 订阅 |
| ------------- | -------------- | ------------ |
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
| ------------- | -------------- | ------------ |
| databaseaccounts | 是 | 是 |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration
| 资源类型 | 资源组 | 订阅 |
| ------------- | -------------- | ------------ |
| domains | 是 | 是 |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid
| 资源类型 | 资源组 | 订阅 |
| ------------- | -------------- | ------------ |
| topics | 是 | 是 |

## <a name="microsofteventhub"></a>Microsoft.EventHub
| 资源类型 | 资源组 | 订阅 |
| ------------- | -------------- | ------------ |
| clusters | 是 | 是 |
| namespaces | 是 | 是 |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure
| 资源类型 | 资源组 | 订阅 |
| ------------- | -------------- | ------------ |
| hanainstances | 是 | 是 |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules
| 资源类型 | 资源组 | 订阅 |
| ------------- | -------------- | ------------ |
| dedicatedhsms | 否 | 否 |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight
| 资源类型 | 资源组 | 订阅 |
| ------------- | -------------- | ------------ |
| clusters | 是 | 是 |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport
| 资源类型 | 资源组 | 订阅 |
| ------------- | -------------- | ------------ |
| jobs | 是 | 是 |

## <a name="microsoftinsights"></a>microsoft.insights
| 资源类型 | 资源组 | 订阅 |
| ------------- | -------------- | ------------ |
| actiongroups | 是 | 是 |
| activitylogalerts | 否 | 否 |
| alertrules | 是 | 是 |
| autoscalesettings | 是 | 是 |
| components | 是 | 是 |
| metricalerts | 否 | 否 |
| scheduledqueryrules | 是 | 是 |
| webtests | 是 | 是 |
| workbooks | 是 | 是 |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral
| 资源类型 | 资源组 | 订阅 |
| ------------- | -------------- | ------------ |
| iotapps | 是 | 是 |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault
| 资源类型 | 资源组 | 订阅 |
| ------------- | -------------- | ------------ |
| vaults | 是 | 是 |

## <a name="microsoftkusto"></a>Microsoft.Kusto
| 资源类型 | 资源组 | 订阅 |
| ------------- | -------------- | ------------ |
| clusters | 是 | 是 |

## <a name="microsoftlabservices"></a>Microsoft.LabServices
| 资源类型 | 资源组 | 订阅 |
| ------------- | -------------- | ------------ |
| labaccounts | 是 | 是 |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices
| 资源类型 | 资源组 | 订阅 |
| ------------- | -------------- | ------------ |
| accounts | 是 | 是 |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices
| 资源类型 | 资源组 | 订阅 |
| ------------- | -------------- | ------------ |
| accounts | 是 | 是 |

## <a name="microsoftlogic"></a>Microsoft.Logic
| 资源类型 | 资源组 | 订阅 |
| ------------- | -------------- | ------------ |
| integrationaccounts | 是 | 是 |
| workflows | 是 | 是 |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning
| 资源类型 | 资源组 | 订阅 |
| ------------- | -------------- | ------------ |
| commitmentplans | 是 | 是 |
| webservices | 是 | 否 |
| workspaces | 是 | 是 |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute
| 资源类型 | 资源组 | 订阅 |
| ------------- | -------------- | ------------ |
| operationalizationclusters | 是 | 是 |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation
| 资源类型 | 资源组 | 订阅 |
| ------------- | -------------- | ------------ |
| accounts | 是 | 是 |
| accounts/workspaces | 是 | 是 |
| accounts/workspaces/projects | 是 | 是 |
| teamaccounts | 是 | 是 |
| teamaccounts/workspaces | 是 | 是 |
| teamaccounts/workspaces/projects | 是 | 是 |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement
| 资源类型 | 资源组 | 订阅 |
| ------------- | -------------- | ------------ |
| accounts | 是 | 是 |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices
| 资源类型 | 资源组 | 订阅 |
| ------------- | -------------- | ------------ |
| workspaces | 是 | 是 |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity
| 资源类型 | 资源组 | 订阅 |
| ------------- | -------------- | ------------ |
| userassignedidentities | 是 | 是 |

## <a name="microsoftmaps"></a>Microsoft.Maps
| 资源类型 | 资源组 | 订阅 |
| ------------- | -------------- | ------------ |
| accounts | 是 | 是 |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps
| 资源类型 | 资源组 | 订阅 |
| ------------- | -------------- | ------------ |
| classicdevservices | 否 | 否 |

## <a name="microsoftmedia"></a>Microsoft.Media
| 资源类型 | 资源组 | 订阅 |
| ------------- | -------------- | ------------ |
| mediaservices | 是 | 是 |
| mediaservices/liveevents | 是 | 是 |
| mediaservices/streamingendpoints | 是 | 是 |

## <a name="microsoftmigrate"></a>Microsoft.Migrate
| 资源类型 | 资源组 | 订阅 |
| ------------- | -------------- | ------------ |
| projects | 否 | 否 |

## <a name="microsoftnetwork"></a>Microsoft.Network
| 资源类型 | 资源组 | 订阅 |
| ------------- | -------------- | ------------ |
| applicationgateways | 否 | 否 |
| applicationsecuritygroups | 是 | 是 |
| azurefirewalls | 否 | 否 |
| connections | 是 | 是 |
| ddosprotectionplans | 否 | 否 |
| dnszones | 是 | 是 |
| expressroutecircuits | 否 | 否 |
| expressroutecrossconnections | 否 | 否 |
| expressroutegateways | 否 | 否 |
| expressrouteports | 否 | 否 |
| frontdoors | 是 | 是 |
| frontdoorwebapplicationfirewallpolicies | 是 | 是 |
| interfaceendpoints | 否 | 否 |
| loadbalancers | 是 | 是 |
| localnetworkgateways | 是 | 是 |
| networkintentpolicies | 是 | 是 |
| networkinterfaces | 是 | 是 |
| networkprofiles | 否 | 否 |
| networksecuritygroups | 是 | 是 |
| networkwatchers | 是 | 是 |
| networkwatchers/connectionmonitors | 是 | 是 |
| networkwatchers/lenses | 是 | 是 |
| networkwatchers/pingmeshes | 是 | 是 |
| publicipaddresses | 是 | 是 |
| publicipprefixes | 是 | 是 |
| routefilters | 否 | 否 |
| routetables | 是 | 是 |
| serviceendpointpolicies | 是 | 是 |
| trafficmanagerprofiles | 是 | 是 |
| virtualhubs | 是 | 是 |
| virtualnetworkgateways | 是 | 是 |
| virtualnetworks | 是 | 是 |
| virtualnetworktaps | 否 | 否 |
| virtualwans | 是 | 是 |
| vpngateways | 是 | 是 |
| vpnsites | 是 | 是 |
| webapplicationfirewallpolicies | 是 | 是 |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs
| 资源类型 | 资源组 | 订阅 |
| ------------- | -------------- | ------------ |
| namespaces | 是 | 是 |
| namespaces/notificationhubs | 是 | 是 |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights
| 资源类型 | 资源组 | 订阅 |
| ------------- | -------------- | ------------ |
| workspaces | 是 | 是 |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement
| 资源类型 | 资源组 | 订阅 |
| ------------- | -------------- | ------------ |
| managementconfigurations | 是 | 是 |
| solutions | 是 | 是 |
| 视图 | 是 | 是 |

## <a name="microsoftportal"></a>Microsoft.Portal
| 资源类型 | 资源组 | 订阅 |
| ------------- | -------------- | ------------ |
| dashboards | 是 | 是 |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI
| 资源类型 | 资源组 | 订阅 |
| ------------- | -------------- | ------------ |
| workspacecollections | 是 | 是 |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated
| 资源类型 | 资源组 | 订阅 |
| ------------- | -------------- | ------------ |
| capacities | 是 | 是 |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices
| 资源类型 | 资源组 | 订阅 |
| ------------- | -------------- | ------------ |
| vaults | 是 | 是 |

## <a name="microsoftrelay"></a>Microsoft.Relay
| 资源类型 | 资源组 | 订阅 |
| ------------- | -------------- | ------------ |
| namespaces | 是 | 是 |

## <a name="microsoftsaas"></a>Microsoft.SaaS
| 资源类型 | 资源组 | 订阅 |
| ------------- | -------------- | ------------ |
| 服务 | 是 | 否 |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler
| 资源类型 | 资源组 | 订阅 |
| ------------- | -------------- | ------------ |
| flows | 是 | 是 |
| jobcollections | 是 | 是 |

## <a name="microsoftsearch"></a>Microsoft.Search
| 资源类型 | 资源组 | 订阅 |
| ------------- | -------------- | ------------ |
| searchservices | 是 | 是 |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus
| 资源类型 | 资源组 | 订阅 |
| ------------- | -------------- | ------------ |
| namespaces | 是 | 是 |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric
| 资源类型 | 资源组 | 订阅 |
| ------------- | -------------- | ------------ |
| clusters | 是 | 是 |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh
| 资源类型 | 资源组 | 订阅 |
| ------------- | -------------- | ------------ |
| 服务 | 是 | 是 |
| networks | 是 | 是 |
| volumes | 是 | 是 |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService
| 资源类型 | 资源组 | 订阅 |
| ------------- | -------------- | ------------ |
| signalr | 是 | 是 |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery
| 资源类型 | 资源组 | 订阅 |
| ------------- | -------------- | ------------ |
| siterecoveryvault | 否 | 否 |

## <a name="microsoftsolutions"></a>Microsoft.Solutions
| 资源类型 | 资源组 | 订阅 |
| ------------- | -------------- | ------------ |
| appliancedefinitions | 否 | 否 |
| appliances | 否 | 否 |
| applicationdefinitions | 否 | 否 |
| 服务 | 否 | 否 |

## <a name="microsoftsql"></a>Microsoft.Sql
| 资源类型 | 资源组 | 订阅 |
| ------------- | -------------- | ------------ |
| managedinstances | 是 | 是 |
| managedinstances/databases | 是 | 是 |
| servers | 是 | 是 |
| servers/databases | 是 | 是 |
| servers/elasticpools | 是 | 是 |
| virtualclusters | 是 | 是 |

## <a name="microsoftstorage"></a>Microsoft.Storage
| 资源类型 | 资源组 | 订阅 |
| ------------- | -------------- | ------------ |
| storageaccounts | 是 | 是 |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync
| 资源类型 | 资源组 | 订阅 |
| ------------- | -------------- | ------------ |
| storagesyncservices | 是 | 是 |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple
| 资源类型 | 资源组 | 订阅 |
| ------------- | -------------- | ------------ |
| managers | 否 | 否 |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics
| 资源类型 | 资源组 | 订阅 |
| ------------- | -------------- | ------------ |
| streamingjobs | 是 | 是 |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights
| 资源类型 | 资源组 | 订阅 |
| ------------- | -------------- | ------------ |
| environments | 是 | 是 |
| environments/eventsources | 是 | 是 |
| environments/referencedatasets | 是 | 是 |

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio
| 资源类型 | 资源组 | 订阅 |
| ------------- | -------------- | ------------ |
| 帐户 | 是 | 是 |
| account/extension | 是 | 是 |
| account/project | 是 | 是 |

## <a name="microsoftweb"></a>Microsoft.Web
| 资源类型 | 资源组 | 订阅 |
| ------------- | -------------- | ------------ |
| certificates | 否 | 是 |
| classicmobileservices | 否 | 否 |
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
| ------------- | -------------- | ------------ |
| deviceservices | 是 | 是 |


## <a name="third-party-services"></a>第三方服务

第三方服务当前不支持移动操作。 这些资源提供程序是：

* 84codes.CloudAMQP
* AppDynamics.APM
* Aspera.Transfers
* Auth0.Cloud
* Citrix.Cloud
* Citrix.Services
* CloudSimple.PrivateCloudIaaS
* Cloudyn.Analytics
* Conexlink.MyCloudIT
* Crypteron.DataSecurity
* Dynatrace.DynatraceSaaS
* Dynatrace.Ruxit
* LiveArena.Broadcast
* Lombiq.DotNest
* Mailjet.Email
* Myget.PackageManagement
* NewRelic.APM
* nuubit.nextgencdn
* Paraleap.CloudMonix
* Pokitdok.Platform
* RavenHq.Db
* Raygun.CrashReporting
* RevAPM.MobileCDN
* Sendgrid.Email
* Sparkpost.Basic
* stackify.retrace
* SuccessBricks.ClearDB
* TrendMicro.DeepSecurity
* U2uconsult.TheIdentityHub


## <a name="next-steps"></a>后续步骤

* 有关移动资源的命令，请参阅[将资源移动至新资源组或订阅](resource-group-move-resources.md)。
