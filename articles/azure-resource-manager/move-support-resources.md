---
title: Azure 资源类型支持的移动操作
description: 列出了可移动到新资源组或订阅的 Azure 资源类型。
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 10/24/2019
ms.author: tomfitz
ms.openlocfilehash: 05f7d022588eee0e5e97f10d6300da04c4be6270
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2019
ms.locfileid: "73161956"
---
# <a name="move-operation-support-for-resources"></a>支持移动操作的资源
本文列出了 Azure 资源类型是否支持移动操作。 它还提供有关移动资源时要考虑的特殊情况的信息。

跳转到资源提供程序命名空间：
> [!div class="op_single_selector"]
> - [Microsoft AAD](#microsoftaad)
> - [aadiam](#microsoftaadiam)
> - [AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.analysisservices.sharepoint.integration.dll](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [AppConfiguration](#microsoftappconfiguration)
> - [AppService](#microsoftappservice)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [AzureActiveDirectory](#microsoftazureactivedirectory)
> - [AzureData](#microsoftazuredata)
> - [Test-azurestack](#microsoftazurestack)
> - [Microsoft. 批处理](#microsoftbatch)
> - [BatchAI](#microsoftbatchai)
> - [BingMaps](#microsoftbingmaps)
> - [BizTalkServices](#microsoftbiztalkservices)
> - [区块链](#microsoftblockchain)
> - [Microsoft 蓝图](#microsoftblueprint)
> - [BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Microsoft.Cdn](#microsoftcdn)
> - [CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.classiccompute](#microsoftclassiccompute)
> - [Microsoft.classicnetwork](#microsoftclassicnetwork)
> - [ClassicStorage](#microsoftclassicstorage)
> - [Cognitiveservices account](#microsoftcognitiveservices)
> - [Microsoft. 计算](#microsoftcompute)
> - [Microsoft. 容器](#microsoftcontainer)
> - [ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [ContentModerator](#microsoftcontentmoderator)
> - [CortanaAnalytics](#microsoftcortanaanalytics)
> - [CostManagement](#microsoftcostmanagement)
> - [CustomerInsights](#microsoftcustomerinsights)
> - [CustomProviders](#microsoftcustomproviders)
> - [DataBox](#microsoftdatabox)
> - [DataBoxEdge](#microsoftdataboxedge)
> - [Databricks](#microsoftdatabricks)
> - [Microsoft.datacatalog](#microsoftdatacatalog)
> - [DataConnect](#microsoftdataconnect)
> - [DataExchange](#microsoftdataexchange)
> - [Microsoft.DataFactory](#microsoftdatafactory)
> - [DataLake](#microsoftdatalake)
> - [Microsoft.DataLakeAnalytics](#microsoftdatalakeanalytics)
> - [Microsoft.DataLakeStore](#microsoftdatalakestore)
> - [Microsoft.datamigration](#microsoftdatamigration)
> - [DataShare](#microsoftdatashare)
> - [DBforMariaDB](#microsoftdbformariadb)
> - [DBforMySQL](#microsoftdbformysql)
> - [DBforPostgreSQL](#microsoftdbforpostgresql)
> - [DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft.Devices](#microsoftdevices)
> - [DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [DomainRegistration](#microsoftdomainregistration)
> - [EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [基因组学](#microsoftgenomics)
> - [HanaOnAzure](#microsofthanaonazure)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [HealthcareApis](#microsofthealthcareapis)
> - [HybridCompute](#microsofthybridcompute)
> - [HybridData](#microsofthybriddata)
> - [ImportExport](#microsoftimportexport)
> - [microsoft insights](#microsoftinsights)
> - [IoTCentral](#microsoftiotcentral)
> - [IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Kusto](#microsoftkusto)
> - [LabServices](#microsoftlabservices)
> - [LocationBasedServices](#microsoftlocationbasedservices)
> - [Locationservices.log](#microsoftlocationservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [Default-machinelearning-southcentralus](#microsoftmachinelearning)
> - [MachineLearningCompute](#microsoftmachinelearningcompute)
> - [Microsoft.machinelearningexperimentation](#microsoftmachinelearningexperimentation)
> - [MachineLearningModelManagement](#microsoftmachinelearningmodelmanagement)
> - [MachineLearningOperationalization](#microsoftmachinelearningoperationalization)
> - [MachineLearningServices](#microsoftmachinelearningservices)
> - [对 microsoft.managedidentity](#microsoftmanagedidentity)
> - [Microsoft. Maps](#microsoftmaps)
> - [MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft.Media](#microsoftmedia)
> - [Microservices4Spring](#microsoftmicroservices4spring)
> - [Microsoft. 迁移](#microsoftmigrate)
> - [Microsoft NetApp](#microsoftnetapp)
> - [Microsoft 网络](#microsoftnetwork)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft.operationalinsights](#microsoftoperationalinsights)
> - [为 microsoft.operationsmanagement](#microsoftoperationsmanagement)
> - [Microsoft。对等互连](#microsoftpeering)
> - [Microsoft 门户](#microsoftportal)
> - [PortalSdk](#microsoftportalsdk)
> - [Microsoft PowerBI](#microsoftpowerbi)
> - [PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft.projectoxford.face](#microsoftprojectoxford)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft 中继](#microsoftrelay)
> - [ResourceGraph](#microsoftresourcegraph)
> - [Microsoft SaaS](#microsoftsaas)
> - [Microsoft.Scheduler](#microsoftscheduler)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [ServerManagement](#microsoftservermanagement)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [ServiceFabricMesh](#microsoftservicefabricmesh)
> - [SignalRService](#microsoftsignalrservice)
> - [Microsoft. 解决方案](#microsoftsolutions)
> - [Microsoft.Sql](#microsoftsql)
> - [SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [SqlVM](#microsoftsqlvm)
> - [Microsoft.Storage](#microsoftstorage)
> - [StorageCache](#microsoftstoragecache)
> - [Storagesync.sys](#microsoftstoragesync)
> - [StorageSyncDev](#microsoftstoragesyncdev)
> - [StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft StorSimple](#microsoftstorsimple)
> - [Streamanalytics-default-central-us](#microsoftstreamanalytics)
> - [StreamAnalyticsExplorer](#microsoftstreamanalyticsexplorer)
> - [TerraformOSS](#microsoftterraformoss)
> - [Timeseriesinsights-environment-with-eventhub](#microsofttimeseriesinsights)
> - [Microsoft Token](#microsofttoken)
> - [VirtualMachineImages](#microsoftvirtualmachineimages)
> - [visualstudio](#microsoftvisualstudio)
> - [VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft.Web](#microsoftweb)
> - [WindowsIoT](#microsoftwindowsiot)
> - [WindowsVirtualDesktop](#microsoftwindowsvirtualdesktop)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | domainservices | No | No |
> | domainservices/replicasets | No | No |

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | tenants | No | No |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | actionrules | 是 | 是 |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 服务器 | 是 | 是 |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 服务 | 是 | 是 |

## <a name="microsoftappconfiguration"></a>AppConfiguration

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | configurationstores | 是 | 是 |

## <a name="microsoftappservice"></a>Microsoft.AppService

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | apiapps | No | No |
> | appidentities | No | No |
> | gateways | No | No |

> [!IMPORTANT]
> 请参阅[应用服务移动指南](./move-limitations/app-service-move-limitations.md)。

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | policyassignments | No | No |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | automationaccounts | 是 | 是 |
> | automationaccounts/配置 | 是 | 是 |
> | automationaccounts/runbook | 是 | 是 |

> [!IMPORTANT]
> Runbook 必须与自动化帐户位于同一资源组中。

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | b2cdirectories | 是 | 是 |

## <a name="microsoftazuredata"></a>AzureData

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | sqlserverregistrations | No | No |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | registrations | 是 | 是 |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | batchaccounts | 是 | 是 |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | clusters | No | No |
> | fileservers | No | No |
> | jobs | No | No |
> | workspaces | No | No |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | mapapis | No | No |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | BizTalk | 是 | 是 |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | blockchainmembers | 是 | 是 |
> | 观看者 | No | No |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | blueprintassignments | No | No |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | botservices | 是 | 是 |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | redis | 是 | 是 |

> [!IMPORTANT]
> 如果为 Redis 实例的 Azure Cache 配置了虚拟网络，则无法将该实例移到不同的订阅。 请参阅[网络移动限制](./move-limitations/networking-move-limitations.md)。

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | cdnwebapplicationfirewallpolicies | No | No |
> | 配置文件 | 是 | 是 |
> | 配置文件/终结点 | 是 | 是 |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | certificateorders | 是 | 是 |

> [!IMPORTANT]
> 请参阅[应用服务移动指南](./move-limitations/app-service-move-limitations.md)。

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | domainnames | 是 | No |
> | virtualmachines | 是 | No |

> [!IMPORTANT]
> 请参阅[经典部署移动指南](./move-limitations/classic-model-move-limitations.md)。 可以使用特定于该方案的操作跨订阅移动经典部署资源。

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | networksecuritygroups | No | No |
> | reservedips | No | No |
> | virtualnetworks | No | No |

> [!IMPORTANT]
> 请参阅[经典部署移动指南](./move-limitations/classic-model-move-limitations.md)。 可以使用特定于该方案的操作跨订阅移动经典部署资源。

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | storageaccounts | 是 | No |

> [!IMPORTANT]
> 请参阅[经典部署移动指南](./move-limitations/classic-model-move-limitations.md)。 可以使用特定于该方案的操作跨订阅移动经典部署资源。

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | 是 | 是 |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | availabilitysets | 是 | 是 |
> | diskencryptionsets | No | No |
> | 磁盘 | 是 | 是 |
> | galleries | No | No |
> | 库/图像 | No | No |
> | 库/图像/版本 | No | No |
> | hostgroups | No | No |
> | hostgroups/主机 | No | No |
> | images | 是 | 是 |
> | proximityplacementgroups | No | No |
> | restorepointcollections | No | No |
> | sharedvmimages | No | No |
> | sharedvmimages/版本 | No | No |
> | snapshots | 是 | 是 |
> | virtualmachines | 是 | 是 |
> | virtualmachines/extensions | 是 | 是 |
> | virtualmachinescalesets | 是 | 是 |

> [!IMPORTANT]
> 请参阅[虚拟机移动指南](./move-limitations/virtual-machines-move-limitations.md)。

## <a name="microsoftcontainer"></a>Microsoft.Container

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | containergroups | No | No |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | containergroups | No | No |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | registries | 是 | 是 |
> | 注册表/buildtasks | 是 | 是 |
> | 注册表/复制 | 是 | 是 |
> | 注册表/任务 | 是 | 是 |
> | 注册表/webhook | 是 | 是 |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | containerservices | No | No |
> | managedclusters | No | No |
> | openshiftmanagedclusters | No | No |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 应用程序 | 是 | 是 |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | No | No |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 连接器 | 是 | 是 |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | hubs | 是 | 是 |

## <a name="microsoftcustomproviders"></a>CustomProviders

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | resourceproviders | 是 | 是 |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | jobs | No | No |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | databoxedgedevices | No | No |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | workspaces | No | No |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | catalogs | 是 | 是 |
> | datacatalogs | No | No |

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | connectionmanagers | No | No |

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | packages | No | No |
> | plans | No | No |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | datafactories | 是 | 是 |
> | factories | 是 | 是 |

## <a name="microsoftdatalake"></a>Microsoft.DataLake

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | datalakeaccounts | No | No |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | 是 | 是 |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | 是 | 是 |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | services | No | No |
> | 服务/项目 | No | No |
> | slots | No | No |

## <a name="microsoftdatashare"></a>DataShare

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | No | No |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 服务器 | 是 | 是 |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 服务器 | 是 | 是 |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | servergroups | No | No |
> | 服务器 | 是 | 是 |
> | serversv2 | 是 | 是 |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | artifactsources | 是 | 是 |
> | rollouts | 是 | 是 |
> | servicetopologies | 是 | 是 |
> | servicetopologies/服务 | 是 | 是 |
> | servicetopologies/services/serviceunits | 是 | 是 |
> | steps | 是 | 是 |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | elasticpools | No | No |
> | elasticpools / iothubtenants | No | No |
> | iothubs | 是 | 是 |
> | provisioningservices | 是 | 是 |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 控制器 | 是 | 是 |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | labcenters | No | No |
> | labs | 是 | No |
> | 实验室/环境 | 是 | 是 |
> | 实验室/servicerunners | 是 | 是 |
> | 实验室/virtualmachines | 是 | No |
> | schedules | 是 | 是 |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | databaseaccounts | 是 | 是 |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | domains | 是 | 是 |

## <a name="microsoftenterpriseknowledgegraph"></a>EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | services | 是 | 是 |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | domains | 是 | 是 |
> | topics | 是 | 是 |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | clusters | 是 | 是 |
> | namespaces | 是 | 是 |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | No | No |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | hanainstances | No | No |
> | sapmonitors | 是 | 是 |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | clusters | 是 | 是 |

> [!IMPORTANT]
> 可以将 HDInsight 群集移到新的订阅或资源组。 但是，无法在订阅之间移动链接到 HDInsight 群集的网络资源（例如虚拟网络、NIC 或负载均衡器）。 此外，无法将连接到群集的虚拟机的 NIC 移到新的资源组。
>
> 将 HDInsight 群集移到新的订阅时，首先移动其他资源（如存储帐户）。 然后移动 HDInsight 群集本身。

## <a name="microsofthealthcareapis"></a>HealthcareApis

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | services | 是 | 是 |

## <a name="microsofthybridcompute"></a>HybridCompute

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 造 | No | No |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | datamanagers | 是 | 是 |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | jobs | 是 | 是 |

## <a name="microsoftinsights"></a>microsoft.insights

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | No | No |
> | actiongroups | 是 | 是 |
> | activitylogalerts | No | No |
> | alertrules | 是 | 是 |
> | autoscalesettings | 是 | 是 |
> | components | 是 | 是 |
> | guestdiagnosticsettings | No | No |
> | metricalerts | No | No |
> | notificationgroups | No | No |
> | notificationrules | No | No |
> | scheduledqueryrules | 是 | 是 |
> | webtests | 是 | 是 |
> | workbooks | 是 | 是 |

> [!IMPORTANT]
> 请确保移动到新订阅不超过[订阅配额](../azure-subscription-service-limits.md#azure-monitor-limits)。

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | iotapps | 是 | 是 |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checknameavailability | 是 | 是 |
> | graph | 是 | 是 |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | hsmpools | No | No |
> | vaults | 是 | 是 |

> [!IMPORTANT]
> 用于磁盘加密的密钥保管库不能移到同一订阅中的资源组或跨订阅。

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | clusters | 是 | 是 |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | labaccounts | No | No |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | No | No |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | No | No |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | hostingenvironments | No | No |
> | integrationaccounts | 是 | 是 |
> | integrationserviceenvironments | No | No |
> | isolatedenvironments | No | No |
> | workflows | 是 | 是 |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | commitmentplans | 是 | 是 |
> | webservices | 是 | No |
> | workspaces | 是 | 是 |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | operationalizationclusters | 是 | 是 |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | No | No |
> | 帐户/工作区 | No | No |
> | 帐户/工作区/项目 | No | No |
> | teamaccounts | No | No |
> | teamaccounts/工作区 | No | No |
> | teamaccounts/工作区/项目 | No | No |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | No | No |

## <a name="microsoftmachinelearningoperationalization"></a>Microsoft.MachineLearningOperationalization

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | hostingaccounts | No | No |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | workspaces | No | No |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | userassignedidentities | No | No |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | 是 | 是 |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | classicdevservices | No | No |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | mediaservices | 是 | 是 |
> | windowsazure.mediaservices/liveevents | 是 | 是 |
> | windowsazure.mediaservices/streamingendpoint | 是 | 是 |

## <a name="microsoftmicroservices4spring"></a>Microservices4Spring

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | appclusters | No | No |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | assessmentprojects | No | No |
> | migrateprojects | No | No |
> | projects | No | No |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | netappaccounts | No | No |
> | netappaccounts / capacitypools | No | No |
> | netappaccounts/capacitypools/卷 | No | No |
> | netappaccounts/capacitypools/卷集/mounttargets | No | No |
> | netappaccounts/capacitypools/卷/快照 | No | No |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | applicationgateways | No | No |
> | applicationgatewaywebapplicationfirewallpolicies | No | No |
> | applicationsecuritygroups | 是 | 是 |
> | azurefirewalls | 是 | 是 |
> | bastionhosts | No | No |
> | connections | 是 | 是 |
> | ddoscustompolicies | 是 | 是 |
> | ddosprotectionplans | No | No |
> | dnszones | 是 | 是 |
> | expressroutecircuits | No | No |
> | expressroutecrossconnections | No | No |
> | expressroutegateways | No | No |
> | expressrouteports | No | No |
> | frontdoors | No | No |
> | frontdoorwebapplicationfirewallpolicies | No | No |
> | loadbalancers | 是-基本 SKU<br>无标准 SKU | 是-基本 SKU<br>无标准 SKU |
> | localnetworkgateways | 是 | 是 |
> | natgateways | 是 | 是 |
> | networkintentpolicies | 是 | 是 |
> | networkinterfaces | 是 | 是 |
> | networkprofiles | No | No |
> | networksecuritygroups | 是 | 是 |
> | networkwatchers | 是 | 是 |
> | networkwatchers / connectionmonitors | 是 | 是 |
> | networkwatchers/重用功能区 | 是 | 是 |
> | networkwatchers / pingmeshes | 是 | 是 |
> | p2svpngateways | No | No |
> | privatednszones | 是 | 是 |
> | privatednszones / virtualnetworklinks | 是 | 是 |
> | privateendpoints | No | No |
> | privatelinkservices | No | No |
> | publicipaddresses | 是-基本 SKU<br>无标准 SKU | 是-基本 SKU<br>无标准 SKU |
> | publicipprefixes | 是 | 是 |
> | routefilters | No | No |
> | routetables | 是 | 是 |
> | serviceendpointpolicies | 是 | 是 |
> | trafficmanagerprofiles | 是 | 是 |
> | virtualhubs | No | No |
> | virtualnetworkgateways | 是 | 是 |
> | virtualnetworks | 是 | 是 |
> | virtualnetworktaps | No | No |
> | virtualwans | No | No |
> | vpngateways （虚拟 WAN） | No | No |
> | vpnsites （虚拟 WAN） | No | No |
> | webapplicationfirewallpolicies | 是 | 是 |

> [!IMPORTANT]
> 请参阅[网络移动指南](./move-limitations/networking-move-limitations.md)。

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | namespaces | 是 | 是 |
> | 命名空间/notificationhubs | 是 | 是 |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | workspaces | 是 | 是 |

> [!IMPORTANT]
> 请确保移动到新订阅不超过[订阅配额](../azure-subscription-service-limits.md#azure-monitor-limits)。

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | managementconfigurations | 是 | 是 |
> | solutions | 是 | 是 |
> | 视图 | 是 | 是 |

## <a name="microsoftpeering"></a>Microsoft。对等互连

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 对等互连 | No | No |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | dashboards | 是 | 是 |

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | rootresources | No | No |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | workspacecollections | 是 | 是 |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | capacities | 是 | 是 |

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | No | No |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | vaults | 是 | 是 |

> [!IMPORTANT]
> 请参阅[恢复服务移动指南](../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)。

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | namespaces | 是 | 是 |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 查询 | 是 | 是 |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 应用程序 | 是 | No |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | flows | 是 | 是 |
> | jobcollections | 是 | 是 |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | searchservices | 是 | 是 |

> [!IMPORTANT]
> 不能在一个操作中将多个搜索资源移动到不同区域。 只能通过多个单独的操作移动它们。

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | iotsecuritysolutions | 是 | 是 |
> | playbookconfigurations | No | No |

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | gateways | No | No |
> | 节点 | No | No |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | namespaces | 是 | 是 |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 应用程序 | No | No |
> | clusters | 是 | 是 |
> | 群集/应用程序 | No | No |
> | containergroups | No | No |
> | containergroupsets | No | No |
> | edgeclusters | No | No |
> | networks | No | No |
> | secretstores | No | No |
> | volumes | No | No |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 应用程序 | 是 | 是 |
> | containergroups | No | No |
> | gateways | 是 | 是 |
> | networks | 是 | 是 |
> | 机密 | 是 | 是 |
> | volumes | 是 | 是 |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | signalr | 是 | 是 |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | appliancedefinitions | No | No |
> | appliances | No | No |
> | applicationdefinitions | No | No |
> | 应用程序 | No | No |
> | jitrequests | No | No |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | instancepools | No | No |
> | managedinstances | No | No |
> | managedinstances/数据库 | No | No |
> | 服务器 | 是 | 是 |
> | 服务器/数据库 | 是 | 是 |
> | 服务器/elasticpools | 是 | 是 |
> | virtualclusters | 是 | 是 |

> [!IMPORTANT]
> 数据库和服务器必须位于同一个资源组中。 当移动 SQL 服务器时，其所有数据库也会一起移动。 此行为适用于 Azure SQL 数据库和 Azure SQL 数据仓库数据库。

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | sqlvirtualmachinegroups | 是 | 是 |
> | sqlvirtualmachines | 是 | 是 |

## <a name="microsoftsqlvm"></a>Microsoft.SqlVM

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | dwvm | No | No |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | storageaccounts | 是 | 是 |

## <a name="microsoftstoragecache"></a>StorageCache

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 缓存 | No | No |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | 是 | 是 |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | No | No |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | No | No |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | managers | No | No |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | streamingjobs | 是 | 是 |

> [!IMPORTANT]
> 当流分析作业处于运行状态时，无法将其移动。

## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | environments | No | No |
> | 环境/eventsources | No | No |
> | 实例 | No | No |
> | 实例/环境 | No | No |
> | 实例/环境/eventsources | No | No |

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | providerregistrations | No | No |
> | 资源 | No | No |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | environments | 是 | 是 |
> | 环境/eventsources | 是 | 是 |
> | 环境/referencedatasets | 是 | 是 |

## <a name="microsofttoken"></a>Microsoft Token

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 区内 | No | No |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | imagetemplates | No | No |

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 帐户 | 是 | 是 |
> | 帐户/扩展 | 是 | 是 |
> | 帐户/项目 | 是 | 是 |

> [!IMPORTANT]
> 若要更改 Azure DevOps 的订阅，请参阅[更改用于计费的 Azure 订阅](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)。

## <a name="microsoftvmwarecloudsimple"></a>VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | dedicatedcloudnodes | No | No |
> | dedicatedcloudservices | No | No |
> | virtualmachines | No | No |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | certificates | No | 是 |
> | connectiongateways | 是 | 是 |
> | connections | 是 | 是 |
> | customapis | 是 | 是 |
> | hostingenvironments | No | No |
> | serverfarms | 是 | 是 |
> | sites | 是 | 是 |
> | sites/premieraddons | 是 | 是 |
> | 站点/槽 | 是 | 是 |

> [!IMPORTANT]
> 请参阅[应用服务移动指南](./move-limitations/app-service-move-limitations.md)。

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | deviceservices | No | No |

## <a name="microsoftwindowsvirtualdesktop"></a>WindowsVirtualDesktop

> [!div class="mx-tableFixed"]
> | 资源类型 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | applicationgroups | No | No |
> | hostpools | No | No |
> | workspaces | No | No |

## <a name="third-party-services"></a>第三方服务

第三方服务当前不支持移动操作。

## <a name="next-steps"></a>后续步骤
有关移动资源的命令，请参阅[将资源移动至新资源组或订阅](resource-group-move-resources.md)。

若要以逗号分隔值文件的形式获取同一数据，请下载 [move-support-resources.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv)。
