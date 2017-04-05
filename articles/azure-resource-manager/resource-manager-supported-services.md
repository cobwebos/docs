---
title: "Resource Manager 支持的服务 | Microsoft Docs"
description: "介绍支持资源管理器的资源提供程序及其架构和可用 API 版本，以及可托管资源的区域。"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 3c7a6fe4-371a-40da-9ebe-b574f583305b
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/20/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 0bec803e4b49f3ae53f2cc3be6b9cb2d256fe5ea
ms.openlocfilehash: 7dc5143086e3a73e0536408a41468b8cdd40bd12
ms.lasthandoff: 03/24/2017


---
# <a name="resource-manager-providers-regions-api-versions-and-schemas"></a>资源管理器提供程序、区域、 API 版本和架构
本主题提供支持 Azure Resource Manager 的资源提供程序列表。

部署资源时，你还需要知道哪些区域支持这些资源，以及哪些 API 版本可用于资源。 [支持的区域](#supported-regions)部分说明了如何找出哪些区域支持相应的订阅和资源。 [支持的 API 版本](#supported-api-versions)部分说明了如何判断可以使用哪些 API 版本。

若要查看 Azure 门户和经典门户支持哪些服务，请参阅 [Azure 门户可用性图表](https://azure.microsoft.com/features/azure-portal/availability/)。 若要查看哪些服务支持移动资源，请参阅[将资源移到新资源组或订阅](resource-group-move-resources.md)。

下表列出哪些 Microsoft 服务可通过 Resource Manager 支持部署和管理，哪些 Microsoft 服务不可以。 还有许多第三方资源提供程序支持 Resource Manager。 可在[资源提供程序和类型](#resource-providers-and-types)部分中了解如何查看所有资源提供程序。

## <a name="compute"></a>计算
| 服务 | 已启用资源管理器 | REST API | 模板格式 |
| --- | --- | --- | --- |
| 批处理 |是 |[批处理 REST](/rest/api/batchservice) |[Batch 资源](/azure/templates/microsoft.batch/batchaccounts) |
| 容器注册表 |是 |[容器注册表 REST](/rest/api/containerregistry) |[容器注册表资源](/azure/templates/microsoft.containerregistry/registries) |
| 容器服务 |是 |[容器服务 REST](/rest/api/compute/containerservices) |[容器服务资源](/azure/templates/microsoft.containerservice/containerservices) |
| 动态生命周期服务 |是 | | |
| 规模集 |是 |[规模集 REST](/rest/api/compute/virtualmachinescalesets) |[规模集资源](/azure/templates/microsoft.compute/virtualmachinescalesets) |
| Service Fabric |是 |[Service Fabric Rest](/rest/api/servicefabric) | [Service Fabric 资源](/azure/templates/microsoft.servicefabric/clusters) |
| 虚拟机 |是 |[VM REST](/rest/api/compute/virtualmachines) |[VM 资源](/azure/templates/microsoft.compute/virtualmachines) |
| 虚拟机（经典） |受限制 |- |- |
| 远程应用 |否 |- |- |
| 云服务（经典） |有限（参阅下文） |- |- |

虚拟机（经典）是指已通过经典部署模型部署的资源，而不是通过资源管理器部署模型部署的资源。 一般而言，这些资源不支持资源管理器操作，但已启用某些操作。 有关这些部署模型的详细信息，请参阅[了解 Resource Manager 部署和经典部署](resource-manager-deployment-model.md)。 

云服务（经典）可与其他经典资源配合使用。 但是，经典资源不能充分利用所有 Resource Manager 功能，并且不太适合未来的解决方案。 应该考虑将你的应用程序基础结构更改为使用 Microsoft.Compute、Microsoft.Storage 和 Microsoft.Network 命名空间中的资源。

## <a name="networking"></a>联网
| 服务 | 已启用资源管理器 | REST API | 模板格式 |
| --- | --- | --- | --- |
| 应用程序网关 |是 |[应用程序网关 REST](https://msdn.microsoft.com/library/azure/mt684939.aspx) | [应用程序网关资源](/azure/templates/microsoft.network/applicationgateways) |
| DNS |是 |[DNS REST](/rest/api/dns) |[DNS 资源](/azure/templates/microsoft.network/dnszones) |
| ExpressRoute |是 |[ExpressRoute REST](https://msdn.microsoft.com/library/azure/mt586720.aspx) | [ExpressRoute 资源](/azure/templates/microsoft.network/expressroutecircuits) |
| 负载均衡器 |是 |[负载均衡器 REST](https://msdn.microsoft.com/library/azure/mt163651.aspx) |[负载均衡器资源](/azure/templates/microsoft.network/loadbalancers) |
| 流量管理器 |是 |[流量管理器 REST](https://msdn.microsoft.com/library/azure/mt163667.aspx) |[流量管理器资源](/azure/templates/microsoft.network/trafficmanagerprofiles) |
| 虚拟网络 |是 |[虚拟网络 REST](https://msdn.microsoft.com/library/azure/mt163650.aspx) |[虚拟网络资源](/azure/templates/microsoft.network/virtualnetworks) |
| 网关 |是 |[网络网关 REST](https://msdn.microsoft.com/library/azure/mt163859.aspx) | [连接资源](/azure/templates/microsoft.network/connections) <br /> [本地网关资源](/azure/templates/microsoft.network/localnetworkgateways) <br /> [虚拟网关资源](/azure/templates/microsoft.network/virtualnetworkgateways) |

## <a name="storage"></a>存储
| 服务 | 已启用资源管理器 | REST API | 模板格式 |
| --- | --- | --- | --- | --- |
| 导入/导出 | 是 | [导入/导出 REST](/rest/api/storageimportexport/) | [导入/导出资源](/azure/templates/microsoft.importexport/jobs) |
| 存储 |是 |[存储 REST](/rest/api/storagerp) |[存储资源](/azure/templates/microsoft.storage/storageaccounts) |
| StorSimple |是 | | |

## <a name="databases"></a>数据库
| 服务 | 已启用资源管理器 | REST API | 模板格式 |
| --- | --- | --- | --- | --- |
| DocumentDB |是 |[DocumentDB REST](/rest/api/documentdbresourceprovider) |[DocumentDB 资源](/azure/templates/microsoft.documentdb/databaseaccounts) |
| Redis 缓存 |是 | [Redis 缓存 REST](/rest/api/redis) |[Redis 资源](/azure/templates/microsoft.cache/redis) |
| SQL 数据库 |是 |[SQL 数据库 REST](/rest/api/sql) |[SQL 数据库资源](/azure/templates/microsoft.sql/servers) |
| SQL 数据仓库 |是 | | |

## <a name="web--mobile"></a>Web 和移动
| 服务 | 已启用资源管理器 | REST API | 模板格式 |
| --- | --- | --- | --- |
| API 应用 |是 | [应用服务 REST](/rest/api/appservice) |[Web 资源](/azure/templates/microsoft.web/sites) |
| API 管理 |是 |[API 管理 REST](/rest/api/apimanagement) |[API 管理资源](/azure/templates/microsoft.apimanagement/service) |
| 证书注册 | 是 | [证书注册 REST](/rest/api/appservice/appservicecertificateorders) | [证书注册资源](/azure/templates/microsoft.certificateregistration/certificateorders)  |
| 内容审查器 |是 | | | |
| 域注册 | 是 | [域注册](/rest/api/appservice/domains) | [域注册资源](/azure/templates/microsoft.domainregistration/domains)  |
| Function App |是 | [Function App REST](/rest/api/appservice) | [Web 资源](/azure/templates/microsoft.web/sites) |
| Logic Apps |是 |[逻辑应用 REST](/rest/api/logic) |[逻辑应用资源](/azure/templates/microsoft.logic/workflows) |
| 移动应用 |是 | [应用服务 REST](/rest/api/appservice) | [Web 资源](/azure/templates/microsoft.web/sites) |
| Mobile Engagement |是 |[Mobile Engagement REST](https://msdn.microsoft.com/library/azure/mt683754.aspx) | |
| 搜索 |是 |[搜索 REST](/rest/api/searchservice) | [搜索资源](/azure/templates/microsoft.search/searchservices) |
| Web 应用 |是 | [Web 应用 REST](/rest/api/appservice/webapps) | [Web 资源](/azure/templates/microsoft.web/sites) |

## <a name="intelligence--analytics"></a>智能 + 分析
| 服务 | 已启用资源管理器 | REST API | 模板格式 | 
| --- | --- | --- | --- |
| Analysis Services | 是 | [Analysis Service REST](/rest/api/analysisservices) | [Analysis Services 资源](/azure/templates/microsoft.analysisservices/servers) |
| 认知服务 |是 | [认知服务 REST](/rest/api/cognitiveservices) |[认知服务资源](/azure/templates/microsoft.cognitiveservices/accounts) |
| 数据目录 |是 |[数据目录 REST](/rest/api/datacatalog) |[数据目录架构](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-03-30/Microsoft.DataCatalog.json) |
| 数据工厂 |是 |[数据工厂 REST](/rest/api/datafactory) | |
| 数据湖分析 |是 | [Data Lake REST](/rest/api/datalakeanalytics) |[Data Lake Analytics 资源](/azure/templates/microsoft.datalakeanalytics/accounts) |
| Data Lake Store |是 |[Data Lake Store REST](/rest/api/datalakestore) |[Data Lake Store 资源](/azure/templates/microsoft.datalakestore/accounts) |
| HDInsights |是 |[HDInsights REST](/rest/api/hdinsight) | |
| 机器学习 |是 |[机器学习 REST](/rest/api/machinelearning) |[机器学习资源](/azure/templates/microsoft.machinelearning/commitmentplans) |
| 流分析 |是 |[流分析 REST](/rest/api/streamanalytics) | |
| Power BI |是 |[Power BI Embedded REST](/rest/api/powerbiembedded) |[Power BI 资源](/azure/templates/microsoft.powerbi/workspacecollections) |


## <a name="internet-of-things"></a>物联网
| 服务 | 已启用资源管理器 | REST API | 模板格式 |
| --- | --- | --- | --- |
| 事件中心 |是 |[事件中心 REST](/rest/api/eventhub) |[事件中心资源](/azure/templates/microsoft.eventhub/namespaces) |
| IoTHubs |是 |[IoT 中心 REST](/rest/api/iothub) |[IoT 中心资源](/azure/templates/microsoft.devices/iothubs) |
| 通知中心 |是 |[通知中心 REST](/rest/api/notificationhubs) |[通知中心资源](/azure/templates/microsoft.notificationhubs/namespaces) |

## <a name="media--cdn"></a>媒体和 CDN
| 服务 | 已启用资源管理器 | REST API | 模板格式 |
| --- | --- | --- | --- |
| CDN |是 |[CDN REST](/rest/api/cdn) |[CDN 资源](/azure/templates/microsoft.cdn/profiles) |
| 媒体服务 |是 |[媒体服务 REST](/rest/api/media) |[媒体资源](/azure/templates/microsoft.media/mediaservices) |

## <a name="enterprise-integration"></a>企业集成
| 服务 | 已启用资源管理器 | REST API | 模板格式 |
| --- | --- | --- | --- |
| BizTalk 服务 |是 | |[BizTalk 架构](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01/Microsoft.BizTalkServices.json) |
| 中继 | 是 |  | [中继资源](/azure/templates/microsoft.relay/namespaces) |
| 服务总线 |是 |[服务总线 REST](/rest/api/servicebus) |[服务总线资源](/azure/templates/microsoft.servicebus/namespaces) |

## <a name="identity--access-management"></a>标识和访问管理
Azure Active Directory 可以使用 Resource Manager 为订阅启用基于角色的访问控制。 若要了解如何使用基于角色的访问控制和 Active Directory，请参阅 [Azure 基于角色的访问控制](../active-directory/role-based-access-control-configure.md)。

## <a name="developer-services"></a>开发人员服务
| 服务 | 已启用资源管理器 | REST API | 模板格式 |
| --- | --- | --- | --- |
| 监视 |是 |[监视器 REST](/rest/api/monitor) |[Insights 资源](/azure/templates/microsoft.insights/alertrules) |
| 必应地图 |是 | | |
| DevTest Labs |是 | [开发测试 REST](/rest/api/dtl) |[开发测试实验室资源](/azure/templates/microsoft.devtestlab/labs) |
| Visual Studio 帐户 |是 | |[Visual Studio 架构](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-02-26/microsoft.visualstudio.json) |

## <a name="management-and-security"></a>管理和安全性
| 服务 | 已启用资源管理器 | REST API | 模板格式 |
| --- | --- | --- | --- |
| 顾问 | 是 | [顾问 REST](/rest/api/advisor/) | - |
| 自动化 |是 |[自动化 REST](https://msdn.microsoft.com/library/azure/mt662285.aspx) |[自动化资源](/azure/templates/microsoft.automation/automationaccounts) |
| 计费 | 是 | [计费 REST](/rest/api/billing/) | - |
| 密钥保管库 |是 |[密钥保管库 REST](/rest/api/keyvault) |[Key Vault 资源](/azure/templates/microsoft.keyvault/vaults) |
| 操作见解 |是 | | |
| 恢复服务 |是 |[恢复服务 REST](/rest/api/recoveryservices) |[恢复服务资源](/azure/templates/microsoft.recoveryservices/vaults) |
| 计划程序 |是 |[计划程序 REST](/rest/api/scheduler) |[计划程序资源](/azure/templates/microsoft.scheduler/jobcollections) |
| “安全” |是 |[安全 REST](https://msdn.microsoft.com/library/azure/mt704034.aspx) | |
| 服务器管理 | 是 | [服务器管理 REST](/rest/api/servermanagement/) | [服务器管理资源](/azure/templates/microsoft.servermanagement/gateways) |

## <a name="resource-manager"></a>资源管理器
| 功能 | 已启用资源管理器 | REST API | 模板格式 |
| --- | --- | --- | --- | --- |
| 授权 |是 |[授权 REST](/rest/api/authorization) |[授权资源](/azure/templates/microsoft.authorization/locks) |
| 资源 |是 |[资源 REST](/rest/api/resources) |[部署资源](/azure/templates/microsoft.resources/deployments) |

## <a name="resource-providers-and-types"></a>资源提供程序和类型
部署资源时，经常需要检索有关资源提供程序和类型的信息。 可以通过 REST API、Azure PowerShell 或 Azure CLI 检索此信息。

若要使用资源提供程序，该资源提供程序必须已注册到你的帐户。 默认情况下，会自动注册多个资源提供程序；但是，你可能需要手动注册某些资源提供程序。 下面的示例演示如何获取资源提供程序的注册状态，并注册资源提供程序（如果需要）。

### <a name="portal"></a>门户
从订阅边栏选项卡选择“资源提供程序”即可轻松查看支持的资源提供程序列表。 若要向资源提供程序注册订阅，请选择“注册”链接。
   
![列出资源提供程序](./media/resource-manager-supported-services/view-resource-providers.png)

### <a name="rest-api"></a>REST API
若要获取所有可用的资源提供程序，包括其类型、位置、API 版本和注册状态，请使用[列出所有资源提供程序](https://docs.microsoft.com/rest/api/resources/providers#Providers_List)操作。 如果需要注册资源提供程序，请参阅 [Register a subscription with a resource provider](https://docs.microsoft.com/rest/api/resources/providers#Providers_Register)（将订阅注册到资源提供程序）。

### <a name="powershell"></a>PowerShell
以下示例说明如何获取所有可用的资源提供程序。

```powershell
Get-AzureRmResourceProvider -ListAvailable
```


以下示例演示如何获取特定资源提供程序的资源类型。

```powershell
(Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes
```

若要注册资源提供程序，请提供命名空间：

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ApiManagement
```

### <a name="azure-cli"></a>Azure CLI
以下示例说明如何获取所有可用的资源提供程序。

```azurecli
az provider list
```

可使用以下命令查看特定资源提供程序的信息：

```azurecli
az provider show --namespace Microsoft.Web
```

若要注册资源提供程序，请提供命名空间：

```azurecli
az provider register --namespace Microsoft.ServiceBus
```

## <a name="supported-regions"></a>支持的区域
部署资源时，通常需要指定资源的区域。 所有区域都支持资源管理器，但部署的资源可能无法在所有区域中受到支持。 此外，订阅可能存在一些限制，以防止用户使用某些支持该资源的区域。 这些限制可能与所在国家/地区的税务问题有关，或者与由订阅管理员所放置，只能使用特定区域的策略结果有关。 

有关所有 Azure 服务的所有支持的区域的完整列表，请参阅[可用产品（按区域）](https://azure.microsoft.com/regions/#services)。 但是，此列表可能包含你的订阅不支持的区域。 可以通过门户、REST API、PowerShell 或 Azure CLI 来确定订阅支持的特定资源类型所在的区域。

### <a name="portal"></a>门户
可以通过以下步骤查看资源类型的支持区域：

1. 选择“更多服务” > “Resource Explorer”。
   
    ![资源浏览器](./media/resource-manager-supported-services/select-resource-explorer.png)
2. 打开“提供程序”节点。
   
    ![选择提供程序](./media/resource-manager-supported-services/select-providers.png)
3. 选择资源提供程序，并查看支持的区域和 API 版本。
   
    ![查看提供程序](./media/resource-manager-supported-services/view-provider.png)

### <a name="rest-api"></a>REST API
若要发现哪些区域可供订阅中的特定资源类型使用，请使用[列出所有资源提供程序](https://docs.microsoft.com/rest/api/resources/providers#Providers_List)操作。 

### <a name="powershell"></a>PowerShell
以下示例演示如何获取支持网站的区域。

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations
```

### <a name="azure-cli"></a>Azure CLI
以下示例演示如何获取网站支持的位置。

```azurecli
az provider show --namespace Microsoft.Web --query "resourceTypes[?resourceType=='sites'].locations"
```


## <a name="supported-api-versions"></a>支持的 API 版本
部署模板时，必须指定要用于创建每个资源的 API 版本。 API 版本对应于资源提供程序发布的 REST API 操作版本。 资源提供程序启用新功能时，会发布 REST API 的新版本。 因此，在模板中指定的 API 版本会影响你可以在模板中指定的属性。 通常，在创建模板时，需要选择最新的 API 版本。 对于现有模板，你可以决定是要继续使用以前的 API 版本，还是要选择最新版本来更新模板以利用新功能。

### <a name="portal"></a>门户
可以采用与确定支持的区域（如前所述）相同的方式确定支持的 API 版本。

### <a name="rest-api"></a>REST API
若要发现哪些 API 版本可供资源类型使用，请使用[列出所有资源提供程序](https://docs.microsoft.com/rest/api/resources/providers#Providers_List)操作。 

### <a name="powershell"></a>PowerShell
以下示例演示如何获取特定资源类型的可用 API 版本。

```powershell
    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions
```

输出类似于：

```powershell
2015-08-01
2015-07-01
2015-06-01
2015-05-01
2015-04-01
2015-02-01
2014-11-01
2014-06-01
2014-04-01-preview
2014-04-01
```

### <a name="azure-cli"></a>Azure CLI
可以使用以下命令获取资源提供程序的可用 API 版本：

```azurecli
az provider show --namespace Microsoft.Web --query "resourceTypes[?resourceType=='sites'].apiVersions"
```

## <a name="next-steps"></a>后续步骤
* 若要了解如何创建 Resource Manager 模板，请参阅[创作 Azure Resource Manager 模板](resource-group-authoring-templates.md)。
* 若要了解如何部署资源，请参阅[使用 Azure Resource Manager 模板部署应用程序](resource-group-template-deploy.md)。


