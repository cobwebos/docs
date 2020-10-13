---
title: 跨区域移动 Azure 资源的支持
description: 列出可跨 Azure 区域移动的 Azure 资源类型
author: rayne-wiselman
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 08/25/2020
ms.author: raynew
ms.openlocfilehash: 83cd36683a0487f13ab5707e4b1534cc7f20a88a
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2020
ms.locfileid: "91948521"
---
# <a name="support-for-moving-azure-resources-across-regions"></a>跨区域移动 Azure 资源的支持

本文确认了是否支持将某种 Azure 资源类型移到另一个 Azure 区域。 

跳转到资源提供程序命名空间：
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [microsoft.aadiam](#microsoftaadiam)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft.AppConfiguration](#microsoftappconfiguration)
> - [Microsoft.AppService](#microsoftappservice)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft.AzureData](#microsoftazuredata)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.BatchAI](#microsoftbatchai)
> - [Microsoft.BingMaps](#microsoftbingmaps)
> - [Microsoft.BizTalkServices](#microsoftbiztalkservices)
> - [Microsoft.Blockchain](#microsoftblockchain)
> - [Microsoft.Blueprint](#microsoftblueprint)
> - [Microsoft.BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Microsoft.Cdn](#microsoftcdn)
> - [Microsoft.CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft.ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft.Container](#microsoftcontainer)
> - [Microsoft.ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft.ContentModerator](#microsoftcontentmoderator)
> - [Microsoft.CortanaAnalytics](#microsoftcortanaanalytics)
> - [Microsoft.CostManagement](#microsoftcostmanagement)
> - [Microsoft.CustomerInsights](#microsoftcustomerinsights)
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
> - [Microsoft.DataShare](#microsoftdatashare)
> - [Microsoft.DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft.DBforMySQL](#microsoftdbformysql)
> - [Microsoft.DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft.DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft.Devices](#microsoftdevices)
> - [Microsoft.DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [Microsoft.EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft.Genomics](#microsoftgenomics)
> - [Microsoft.HanaOnAzure](#microsofthanaonazure)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [Microsoft.HealthcareApis](#microsofthealthcareapis)
> - [Microsoft.HybridCompute](#microsofthybridcompute)
> - [Microsoft.HybridData](#microsofthybriddata)
> - [Microsoft.ImportExport](#microsoftimportexport)
> - [microsoft.insights](#microsoftinsights)
> - [Microsoft.IoTCentral](#microsoftiotcentral)
> - [Microsoft.IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
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
> - [Microsoft.ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft.Maps](#microsoftmaps)
> - [Microsoft.MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft.Media](#microsoftmedia)
> - [Microsoft.Microservices4Spring](#microsoftmicroservices4spring)
> - [Microsoft.Migrate](#microsoftmigrate)
> - [Microsoft.NetApp](#microsoftnetapp)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft.OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft.OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft.Peering](#microsoftpeering)
> - [Microsoft.Portal](#microsoftportal)
> - [Microsoft.PortalSdk](#microsoftportalsdk)
> - [Microsoft.PowerBI](#microsoftpowerbi)
> - [Microsoft.PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft.ProjectOxford](#microsoftprojectoxford)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft.ResourceGraph](#microsoftresourcegraph)
> - [Microsoft.Resources](#microsoftresources)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft.Scheduler](#microsoftscheduler)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft.ServerManagement](#microsoftservermanagement)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
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
> - [Microsoft.TerraformOSS](#microsoftterraformoss)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft.Token](#microsofttoken)
> - [Microsoft.VirtualMachineImages](#microsoftvirtualmachineimages)
> - [microsoft.visualstudio](#microsoftvisualstudio)
> - [Microsoft.VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [WindowsVirtualDesktop](#microsoftwindowsvirtualdesktop)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- | 
> | domainservices | 否 | 
> | domainservices/replicasets | 否 | 

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | tenants | 否 |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | actionrules | 否 | 

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | servers | 否 |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | 服务 |  是（使用模板） <br/><br/> [跨区域移动 API 管理](../../api-management/api-management-howto-migrate.md)。 | 

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | configurationstores | 否 | 

## <a name="microsoftappservice"></a>Microsoft.AppService

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | apiapps | 是（使用模板）<br/><br/> [将应用服务应用移动到其他区域](../../app-service/manage-move-across-regions.md) | 
> | appidentities | 否 | 
> | gateways | 否 | 


## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | policyassignments | 否 |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | automationaccounts | 是（使用模板） <br/><br/> [使用异地复制](../../automation/automation-managing-data.md#geo-replication-in-azure-automation) |  
> | automationaccounts/configurations | 否 | 
> | automationaccounts/runbooks | 否 | 



## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | b2cdirectories | 否 | 

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | sqlserverregistrations | 否 |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | registrations | 否 | 

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | batchaccounts |  Batch 帐户不能直接从一个区域移到另一个区域，但你可以使用模板来导出模板，对其进行修改，然后将模板部署到新区域。 <br/><br/> 了解如何[跨区域移动 Batch 帐户](../../batch/best-practices.md#moving-batch-accounts-across-regions) |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | clusters | 否 <br/><br/> Azure Batch AI 服务已 [停](/previous-versions/azure/batch-ai/overview-what-happened-batch-ai)用。
> | fileservers | 否 | 
> | jobs | 否 | 
> | workspaces | 否 | 

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | mapapis | 否 | 

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | biztalk | 否 | 

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | blockchainmembers | 否 <br/><br/> 区块链网络不能具有不同区域中的节点。 
> | watchers | 否 | 

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | blueprintassignments | 否 | 

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | botservices | 否 | 

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | redis | 否 | 


## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | cdnwebapplicationfirewallpolicies | 否 |
> | 配置文件 | 否 | 
> | profiles/endpoints | 否 | 

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | certificateorders | 否 | 


## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | domainnames | 不会为经典服务计划任何工作。
> | virtualmachines | 否 | 



## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | networksecuritygroups | 不会为经典服务计划任何工作。
> | reservedips | 否 | 
> | virtualnetworks | 否 | 

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | storageaccounts | 是 |  


## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | accounts | 否 | 
> | 认知搜索 | 支持手动步骤。<br/><br/> 了解如何 [将 Azure 认知搜索服务转移到另一个区域](../../search/search-howto-move-across-regions.md)

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | availabilitysets | 是 <br/><br/> 使用 [Azure 资源移动器](../../resource-mover/tutorial-move-region-virtual-machines.md) 移动可用性集。 | 
> | diskencryptionsets | 否 | 
> | disks | 是 <br/><br/> 使用 [Azure 资源移动器](../../resource-mover/tutorial-move-region-virtual-machines.md) 移动 azure vm 和相关磁盘。 | 
> | galleries | 否 | 
> | galleries/images | 否 | 
> | galleries/images/versions | 否 | 
> | hostgroups | 否 | 
> | hostgroups/hosts | 否 | 
> | images | 否 | 
> | proximityplacementgroups | 否 | 
> | restorepointcollections | 否 | 
> | sharedvmimages | 否 | 
> | sharedvmimages/versions | 否 | 
> | snapshots | 否 | 
> | virtualmachines | 是 <br/><br/> 使用 [Azure 资源移动器](../../resource-mover/tutorial-move-region-virtual-machines.md) 移动 azure vm。 | 
> | virtualmachines/extensions | 否 | 
> | virtualmachinescalesets | 否 | 

## <a name="microsoftcontainer"></a>Microsoft.Container

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | containergroups | 否 | 

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | containergroups | 否 | 

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | registries | 否 |  
> | registries/buildtasks | 否 |  
> | registries/replications | 否 | 
> | registries/tasks | 否 |  
> | registries/webhooks | 否 | 

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | containerservices | 否。<br/><br/> 服务[已停用](https://azure.microsoft.com/updates/azure-container-service-will-retire-on-january-31-2020/)。
> | managedclusters | 否 | 
> | openshiftmanagedclusters | 否 | 

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | applications | 否 | 

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | accounts | 否 | 

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | 连接器 | 否 |  

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | hubs | 否 |  

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | resourceproviders | 否 | 

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | jobs | 否 | 

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | databoxedgedevices | 否 | 

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | workspaces | 否 | 

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | catalogs | 否 | 
> | datacatalogs | 否 | 

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | connectionmanagers | 否 | 

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | packages | 否 | 
> | plans | 否 | 

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | datafactories | 否 | 
> | factories | 否 |  

## <a name="microsoftdatalake"></a>Microsoft.DataLake

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | datalakeaccounts | 否 | 

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | accounts | 否 | 

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | accounts | 否 | 

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | services | 否 | 
> | services/projects | 否 | 
> | slots | 否 | 

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | accounts | 否 | 

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | servers | 可以使用跨区域只读副本移动现有服务器。 [了解详细信息](../../postgresql/howto-move-regions-portal.md)。<br/><br/> 如果为服务预配了异地冗余备份存储，则可以使用异地还原在其他区域中进行还原。 [了解详细信息](../../mariadb/concepts-business-continuity.md#recover-from-an-azure-regional-data-center-outage)。

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | servers | 可以使用跨区域只读副本移动现有服务器。 [了解详细信息](../../mysql/howto-move-regions-portal.md)。

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | servergroups | 否 | 
> | servers | 可以使用跨区域只读副本移动现有服务器。 [了解详细信息](../../postgresql/howto-move-regions-portal.md)。
> | serversv2 | 否 | 

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | artifactsources | 否 | 
> | rollouts | 否 |  
> | servicetopologies | 否 | 
> | servicetopologies / services | 否 |  
> | servicetopologies / services / serviceunits | 否 | 
> | steps | 否 | 

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | elasticpools | 否。 资源未公开。
> | elasticpools/iothubtenants | 否。 资源未公开。
> | iothubs | 是的。 [了解详细信息](../../iot-hub/iot-hub-how-to-clone.md)
> | provisioningservices | 否 | 

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | 控制器 | 否 | 
> | AKS 群集 | 否<br/><br/> [了解](../../dev-spaces/faq.md#can-i-migrate-my-aks-cluster-with-azure-dev-spaces-to-another-region) 有关移动到另一个区域的详细信息。

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | labcenters | 否 | 
> | labs | 否 | 
> | labs / environments | 否 |  
> | labs / servicerunners | 否 | 
> | labs / virtualmachines | 否 |  
> | schedules | 否 |  

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | databaseaccounts | 否 | 

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | domains | 否 | 

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | services | 否 |  

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | domains | 否 |  
> | topics | 否 | 

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | clusters | 否 |  
> | namespaces | 是（带模板）<br/><br/> [将事件中心命名空间移到另一个区域](../../event-hubs/move-across-regions.md) | 

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | accounts | 否 | 

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | hanainstances | 否 | 
> | sapmonitors | 否 |  

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | clusters | 否 | 

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | services | 否 |  

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | machines | 否 | 

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | datamanagers |  否 | 

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | jobs |  否 | 

## <a name="microsoftinsights"></a>microsoft.insights

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | accounts | 错误。 [了解详细信息](../../azure-monitor/faq.md#how-do-i-move-an-application-insights-resource-to-a-new-region)。
> | actiongroups |  否 | 
> | activitylogalerts | 否 | 
> | alertrules |  否 | 
> | autoscalesettings |  否 | 
> | components |  否 |  
> | guestdiagnosticsettings | 否 | 
> | metricalerts | 否 | 
> | notificationgroups | 否 | 
> | notificationrules | 否 | 
> | scheduledqueryrules |  否 | 
> | webtests |  否 | 
> | workbooks |  否 |  


## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | checknameavailability |  否。<br/><br/> IoT Central 使用地理位置而不是区域。
> | graph | 否

## <a name="microsoftiothub"></a>Microsoft.IoTHub

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> |  iothub |  是 (克隆中心)  <br/><br/> [将 IoT 中心克隆到另一个区域](../../iot-hub/iot-hub-how-to-clone.md)

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | checknameavailability |  否 |  
> | graph |  否 | 

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | hsmpools | 否 | 
> | vaults |  否 | 


## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | clusters |  否 |  

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | labaccounts | 否 | 

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | accounts | 否 | 

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | accounts | 不是，它是一种全球服务。

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | hostingenvironments | 否 | 
> | integrationaccounts |  否 |  
> | integrationserviceenvironments | 否 | 
> | isolatedenvironments | 否 | 
> | workflows |  否 |  

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | commitmentplans |  否 | 
> | webservices |  否 | 
> | workspaces |  否 | 

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | operationalizationclusters |  否 | 

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | accounts | 否 | 
> | accounts / workspaces | 否 | 
> | accounts / workspaces / projects | 否 | 
> | teamaccounts | 否 | 
> | teamaccounts / workspaces | 否 | 
> | teamaccounts / workspaces / projects | 否 | 

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | accounts | 否 | 

## <a name="microsoftmachinelearningoperationalization"></a>Microsoft.MachineLearningOperationalization

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | hostingaccounts | 否 | 

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | workspaces | 否 | 

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | userassignedidentities | 否 | 

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | accounts |  不，Azure Maps 是地理空间服务。 

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | classicdevservices | 无计划使用经典服务 

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | mediaservices |  否 | 
> | mediaservices/liveevents |  否 | 
> | mediaservices/streamingendpoints |  否 | 

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | appclusters | 否 | 

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | assessmentprojects | 否 | 
> | migrateprojects | 否 | 
> | projects | 否 | 

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | netappaccounts | 否 | 
> | netappaccounts / capacitypools | 否 | 
> | netappaccounts / capacitypools / volumes | 否 | 
> | netappaccounts / capacitypools / volumes / mounttargets | 否 | 
> | netappaccounts / capacitypools / volumes / snapshots | 否 | 

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | applicationgateways | 否 | 
> | applicationgatewaywebapplicationfirewallpolicies | 否 | 
> | applicationsecuritygroups |  否 |  
> | azurefirewalls |  否 |  
> | bastionhosts | 否 | 
> | connections |  否 | 
> | ddoscustompolicies |  否 | 
> | ddosprotectionplans | 否 | 
> | dnszones |  否 | 
> | expressroutecircuits | 否 | 
> | expressroutecrossconnections | 否 | 
> | expressroutegateways | 否 | 
> | expressrouteports | 否 | 
> | frontdoors | 否 | 
> | frontdoorwebapplicationfirewallpolicies | 否 | 
> | loadbalancers | 是 <br/><br/> 使用 [Azure 资源移动器](../../resource-mover/tutorial-move-region-virtual-machines.md) 来移动内部和外部负载均衡器。 |
> | localnetworkgateways |  否 | 
> | natgateways |  否 | 
> | networkintentpolicies |  否 | 
> | networkinterfaces | 是 <br/><br/> 使用 [Azure 资源移动器](../../resource-mover/tutorial-move-region-virtual-machines.md) 移动 nic。 | 
> | networkprofiles | 否 | 
> | networksecuritygroups | 是 <br/><br/> 使用 [Azure 资源移动器](../../resource-mover/tutorial-move-region-virtual-machines.md) (NGSs) 移动网络安全组。 | 
> | networkwatchers |  否 |  
> | networkwatchers/connectionmonitors |  否 | 
> | networkwatchers/lenses |  否 | 
> | networkwatchers/pingmeshes |  否 | 
> | p2svpngateways | 否 | 
> | privatednszones |  否 |  
> | privatednszones/virtualnetworklinks |  否 |  
> | privateendpoints | 否 | 
> | privatelinkservices | 否 | 
> | publicipaddresses | 是<br/><br/> 使用 [Azure 资源移动器](../../resource-mover/tutorial-move-region-virtual-machines.md) 移动公共 IP 地址。 |
> | publicipprefixes | 否 | 
> | routefilters | 否 | 
> | routetables |  否 | 
> | serviceendpointpolicies |  否 | 
> | trafficmanagerprofiles |  否 | 
> | virtualhubs | 否 | 
> | virtualnetworkgateways |  否 |  
> | virtualnetworks |  否 | 
> | virtualnetworktaps | 否 | 
> | virtualwans | 否 | 
> | vpngateways（虚拟 WAN） | 否 | 
> | vpnsites（虚拟 WAN） | 否 | 
> | webapplicationfirewallpolicies |  否 | 


## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | namespaces |  否 | 
> | namespaces/notificationhubs |  否 |  

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | workspaces |  否 | 



## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | managementconfigurations |  否 | 
> | 视图 |  否 | 

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | peerings | 否 | 

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | dashboards | 否 | 

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | rootresources | 否 | 

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | workspacecollections |  否 | 

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | capacities |  否 | 

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | accounts | 否 | 

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | vaults | 否。<br/><br/> 不支持跨 Azure 区域移动 Azure 备份的恢复服务保管库。<br/><br/> 在 Azure Site Recovery 的恢复服务保管库中，可以在目标区域中[禁用并重新创建保管库](../../site-recovery/move-vaults-across-regions.md)。 | 


## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | namespaces |  否 | 

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | 查询 |  否 |  

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 |
> | ------------- | ----------- |
> | deploymentScripts |  是<br/><br/>[将 Microsoft .Resources 资源移动到新区域](microsoft-resources-move-regions.md) |
> | templateSpecs |  是<br/><br/>[将 Microsoft .Resources 资源移动到新区域](microsoft-resources-move-regions.md) |  

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | applications |  否 | 

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | flows |  否 |  
> | jobcollections |  否 | 

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | searchservices |  否 | 


## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | iotsecuritysolutions |  否 | 
> | playbookconfigurations | 否 | 

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | gateways | 否 | 
> | nodes | 否 | 

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | namespaces |  否 | 

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | applications | 否 | 
> | clusters |  否 | 
> | clusters/applications | 否 | 
> | containergroups | 否 | 
> | containergroupsets | 否 | 
> | edgeclusters | 否 | 
> | networks | 否 | 
> | secretstores | 否 | 
> | volumes | 否 | 

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | applications |  否 | 
> | containergroups | 否 | 
> | gateways |  否 | 
> | networks |  否 | 
> | 机密 |  否 | 
> | volumes |  否 |  

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | signalr |  否 |  

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | appliancedefinitions | 否 | 
> | appliances | 否 | 
> | applicationdefinitions | 否 | 
> | applications | 否 | 
> | jitrequests | 否 | 

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | instancepools | 否 | 
> | managedinstances | 是 <br/><br/> [详细了解](../../azure-sql/database/move-resources-across-regions.md)如何在区域之间移动托管实例。 | 
> | managedinstances/databases | 是 | 
> | servers | 是 | 
> | servers/databases | 是 <br/><br/> [详细了解](../../azure-sql/database/move-resources-across-regions.md)如何在区域之间移动数据库。<br/><br/> [详细了解](../../resource-mover/tutorial-move-region-sql.md) 如何使用 Azure 资源移动器移动 azure SQL 数据库。  | 
> | servers/elasticpools | 是 <br/><br/> [详细了解](../../azure-sql/database/move-resources-across-regions.md)如何在区域之间移动弹性池。<br/><br/> [详细了解](../../resource-mover/tutorial-move-region-sql.md) 如何使用 Azure 资源移动器移动 azure SQL 弹性池。  | 
> | virtualclusters | 是 | 

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | sqlvirtualmachinegroups |  否 |  
> | sqlvirtualmachines |  否 |  

## <a name="microsoftsqlvm"></a>Microsoft.SqlVM

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | dwvm | 否 | 

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | storageaccounts | 是<br/><br/> [将 Azure 存储帐户移到另一个区域](../../storage/common/storage-account-move.md) | 

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | caches | 否 | 

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | storagesyncservices |  否 | 

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | storagesyncservices | 否 | 

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | storagesyncservices | 否 | 

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | managers | 否 | 

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | streamingjobs |  否 |  


## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | environments | 否 | 
> | environments / eventsources | 否 | 
> | instances | 否 | 
> | instances / environments | 否 | 
> | instances / environments / eventsources | 否 | 

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | providerregistrations | 否 | 
> | resources | 否 | 

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | environments |  否 | 
> | environments / eventsources |  否 |  
> | environments/referencedatasets |  否 | 

## <a name="microsofttoken"></a>Microsoft.Token

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | stores | 否 | 

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | imagetemplates | 否 | 

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | account |  否 | 
> | account / extension |  否 | 
> | account / project |  否 | 



## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | dedicatedcloudnodes | 否 | 
> | dedicatedcloudservices | 否 | 
> | virtualmachines | 否 | 

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | certificates | 否 | 
> | connectiongateways |  否 |  
> | connections |  否 |  
> | customapis |  否 | 
> | hostingenvironments | 否 | 
> | serverfarms |  否 |  
> | sites |  否 | 
> | sites/premieraddons |  否 |  
> | sites/slots |  否 |  


## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | deviceservices | 否 | 

## <a name="microsoftwindowsvirtualdesktop"></a>WindowsVirtualDesktop

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | applicationgroups | 否 | 
> | hostpools | 否 | 
> | workspaces | 否 | 

## <a name="third-party-services"></a>第三方服务

第三方服务目前不支持移动操作。