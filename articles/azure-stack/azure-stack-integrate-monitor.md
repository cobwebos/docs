---
title: "外部的监视解决方案与 Azure 堆栈集成 |Microsoft 文档"
description: "了解如何将 Azure 堆栈与数据中心外部监视解决方案集成。"
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 856738a7-1510-442a-88a8-d316c67c757c
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/20/2017
ms.author: mabrigg
ms.openlocfilehash: 76499ac959b77e83494bc4f9593c20a99da5c147
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2017
---
# <a name="integrate-external-monitoring-solution-with-azure-stack"></a>外部的监视解决方案与 Azure 堆栈集成

*适用范围： Azure 堆栈集成系统*

对于外部监视 Azure 堆栈基础结构，你需要监视 Azure 堆栈软件、 物理计算机、 和的物理网络交换机。 上述每个区域提供一种方法来检索运行状况和警报的信息。

- Azure 堆栈软件提供基于 REST 的 API 来检索运行状况和警报。 （使用软件定义的技术，如存储空间直通，存储运行状况和警报是软件监视的一部分。）
- 物理计算机可以使运行状况和警报的信息可通过基板管理控制器 (Bmc)。
- 物理网络设备可以使运行状况和警报的信息可通过 SNMP 协议。

每个 Azure 堆栈解决方案附带的硬件生命周期主机。 此主机运行原始设备制造商 (OEM) 硬件供应商的物理服务器和网络设备监视软件。 如果需要，可以绕过这些监视解决方案，并直接将与现有监视解决方案集成你的数据中心。

> [!IMPORTANT]
> 你使用的外部监视解决方案必须是无代理。 你无法安装在 Azure 堆栈组件内的第三方代理。

下图显示一个集成的 Azure 堆栈系统、 硬件生命周期主机、 外部监视解决方案和外部/填写记录的数据收集系统之间的通信流。

![关系图显示 Azure 堆栈，监视和票证解决方案之间的流量。](media/azure-stack-integrate-monitor/MonitoringIntegration.png)  

此文章介绍了如何将 Azure 堆栈与外部的监视解决方案，例如 System Center Operations Manager 和 Nagios 集成。 它还包括如何以编程方式使用除外的警报，通过使用 PowerShell 或通过 REST API 调用。

## <a name="integrate-with-operations-manager"></a>与 Operations Manager 集成

你可用于 Azure 堆栈的外部监视 Operations Manager。 用于 Microsoft Azure 堆栈的 System Center 管理包可监视多个 Azure 堆栈部署与单个的 Operations Manager 实例。 该管理包使用的运行状况资源提供程序和更新资源提供程序 REST Api 与 Azure 堆栈进行通信。 如果你打算绕过 OEM 监视硬件生命周期主机正在运行的软件，你可以安装供应商管理包监视物理服务器。 Operations Manager 网络设备发现还可用于监视网络交换机。

用于 Azure 堆栈的管理包提供以下功能：

- 你可以管理多个 Azure 堆栈部署。
- 这是 Azure Active Directory (Azure AD) 和 Active Directory 联合身份验证服务 (AD FS) 的支持。
- 你可以检索并关闭警报。
- 没有在运行状况和容量仪表板。
- 包括自动维护模式修补程序和更新 (P & U) 是当正在进行中的检测。
- 包括用于部署和区域的强制更新任务。
- 可以将自定义信息添加到区域。
- 支持通知和报告。

你可以下载 System Center 管理包为 Microsoft Azure 堆栈和关联的用户指南[此处](https://www.microsoft.com/en-us/download/details.aspx?id=55184)，或直接从 Operations Manager。

对于票证的解决方案，可以将集成 Operations Manager 与 System Center Service Manager 中。 集成的产品连接器允许，可关闭 Azure 堆栈和 Operations Manager 中的警报解决 Service Manager 中的服务请求后的双向通信。

下图显示了与现有的 System Center 部署 Azure 堆栈的集成。 您可以自动化服务管理器进一步与 System Center Orchestrator 或 Service Management Automation (SMA)，来运行 Azure 堆栈中的操作。

![显示与 OM、 Service Manager 和 SMA 集成的图示。](media/azure-stack-integrate-monitor/SystemCenterIntegration.png)

## <a name="integrate-with-nagios"></a>将与 Nagios 集成

监视插件 Nagios 开发以及合作伙伴 Cloudbase 解决方案，这是宽松免费软件许可证 – 麻省理工学院 (Massachusetts Institute of Technology) 下可用。

插件使用 Python 编写的并可利用运行状况资源提供程序 REST API。 它提供基本功能，以检索并关闭 Azure 堆栈中的警报。 如 System Center 管理包，它使您可以添加多个 Azure 堆栈部署，并以发送通知。

该插件可用于 Nagios 企业和 Nagios 核心。 可以在[此处](https://exchange.nagios.org/directory/Plugins/Cloud/Monitoring-AzureStack-Alerts/details)下载。 下载站点还包括安装和配置详细信息。

### <a name="plugin-parameters"></a>插件参数

使用以下参数配置插件文件"Azurestack_plugin.py":

| 参数 | 说明 | 示例 |
|---------|---------|---------|
| *arm_endpoint* | Azure 资源管理器 （管理员） 终结点 |https://adminmanagement.local.azurestack.external |
| *api_endpoint* | Azure 资源管理器 （管理员） 终结点  | https://adminmanagement.local.azurestack.external |
| *Tenant_id* | 管理员的订阅 ID | 检索通过管理员门户或 PowerShell |
| *User_name* | 运算符订阅用户名 | operator@myazuredirectory.onmicrosoft.com |
| *User_password* | 运算符订阅密码 | mypassword |
| *Client_id* | 客户端 | 0a7bdc5c-7b57-40be-9939-d4c5fc7cd417 * |
| *区域* |  Azure 堆栈区域名称 | local |
|  |  |

* 提供 PowerShell GUID 是通用的。 你可以使用它为每个部署。

## <a name="use-powershell-to-monitor-health-and-alerts"></a>使用 PowerShell 监视运行状况和警报

如果你不使用 Operations Manager、 Nagios 或基于 Nagios 的解决方案，你可以使用 PowerShell 启用广泛的监视解决方案，以将与 Azure 堆栈集成。
 
1. 若要使用 PowerShell，请确保你有[PowerShell 安装和配置](azure-stack-powershell-configure-quickstart.md)Azure 堆栈运算符环境。 在可以访问资源管理器 （管理员） 终结点的本地计算机上安装 PowerShell (https://adminmanagement。 [区域]。[External_FQDN])。

2. 运行以下命令以连接到 Azure 堆栈环境与 Azure 堆栈运算符：

   ```PowerShell
   Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint https://adminmanagement.[Region].[External_FQDN]

   Login-AzureRmAccount -EnvironmentName "AzureStackAdmin"
   ```
3. 将更改为安装目录[Azure 堆栈工具](https://github.com/Azure/AzureStack-Tools)作为 PowerShell 安装，例如，c:\azurestack-tools-master 的一部分。 然后，将更改为基础结构目录并运行以下命令以导入基础结构模块：

   ```PowerShell
   Import-Module .\AzureStack.Infra.psm1
    ```
4. 使用命令如下面的示例以使用的警报：
   ```PowerShell
   #Retrieve all alerts
   Get-AzsAlert -location [Region]

   #Filter for active alerts
   $Active=Get-AzsAlert -location [Region] | Where {$_.State -eq "active"}
   $Active

   #Close alert
   Close-AzsAlert -location [Region] -AlertID "ID"

   #Retrieve resource provider health
   Get-AzsResourceProviderHealths -location [Region]

   #Retrieve infrastructure role instance health
   Get-AzsInfrastructureRoleHealths -location [Region]
   ```

## <a name="use-the-rest-api-to-monitor-health-and-alerts"></a>使用 REST API 来监视运行状况和警报

可以使用 REST API 调用来获取警报，请关闭警报，并获取资源提供程序的运行状况。

### <a name="get-alert"></a>获取警报

**请求**

请求获取默认提供程序订阅的所有活动和已关闭警报。 没有任何请求正文。


|方法  |请求 URI  |
|---------|---------|
|GET     |   https://{armendpoint}/subscriptions/{subId}/resourceGroups/system。{RegionName}/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/{RegionName}/Alerts?api-version=2016-05-01"      |
|     |         |

**参数**

|参数  |说明  |
|---------|---------|
|armendpoint     |  你的 Azure 堆栈环境，格式 https://adminmanagement Azure 资源管理器终结点。{RegionName}。{外部 FQDN}。 例如，如果外部 FQDN 为*azurestack.external*和地区名称都是*本地*，则资源管理器终结点是 https://adminmanagement.local.azurestack.external。       |
|subid     |   进行调用的用户的订阅 ID。 你可以使用查询此 API 仅具有对默认提供程序订阅的权限的用户。      |
|RegionName     |    Azure 堆栈部署的区域名称。     |
|api-version     |  用于发出此请求的协议的版本。 你必须使用 2016年-05-01。      |
|     |         |

**响应**

```http
GET https://adminmanagement.local.azurestack.external/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/Alerts?api-version=2016-05-01 HTTP/1.1
```

```json
{
"value":[
{"id":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/alerts/71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"name":"71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"type":"Microsoft.InfrastructureInsights.Admin/regionHealths/alerts",
"location":"local",
"tags":{},
"properties":
{
"closedTimestamp":"",
"createdTimestamp":"2017-08-10T20:13:57.4398842Z",
"description":[{"text":"The infrastructure role (Updates) is experiencing issues.",
"type":"Text"}],
"faultId":"ServiceFabric:/UpdateResourceProvider/fabric:/AzurestackUpdateResourceProvider",
"alertId":"71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"faultTypeId":"ServiceFabricApplicationUnhealthy",
"lastUpdatedTimestamp":"2017-08-10T20:18:58.1584868Z",
"alertProperties":
{
"healthState":"Warning",
"name":"Updates",
"fabricName":"fabric:/AzurestackUpdateResourceProvider",
"description":null,
"serviceType":"UpdateResourceProvider"},
"remediation":[{"text":"1. Navigate to the (Updates) and restart the role. 2. If after closing the alert the issue persists, please contact support.",
"type":"Text"}],
"resourceRegistrationId":null,
"resourceProviderRegistrationId":"472aaaa6-3f63-43fa-a489-4fd9094e235f",
"serviceRegistrationId":"472aaaa6-3f63-43fa-a489-4fd9094e235f",
"severity":"Warning",
"state":"Active",
"title":"Infrastructure role is unhealthy",
"impactedResourceId":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.Fabric.Admin/fabricLocations/local/infraRoles/UpdateResourceProvider",
"impactedResourceDisplayName":"UpdateResourceProvider",
"closedByUserAlias":null
}
},

…
```

**响应详细信息**


|  参数  |说明  |
|---------|---------|
|*id*     |      警报的唯一 ID。   |
|*name*     |     警报的内部名称。   |
|*类型*     |     资源定义。    |
|*位置*     |       区域名称。     |
|*标记*     |   资源标记。     |
|*closedtimestamp*    |  警报已关闭时的 UTC 时间。    |
|*createdtimestamp*     |     创建警报时的 UTC 时间。   |
|*说明*     |    警报的说明。     |
|*faultid*     | 受影响的组件。        |
|*alertid 匹配的警报*     |  警报的唯一 ID。       |
|*faulttypeid*     |  发生故障的组件的唯一类型。       |
|*lastupdatedtimestamp*     |   上次更新警报的信息的 UTC 时间。    |
|*healthstate*     | 总体运行状况状态。        |
|*name*     |   特定警报的名称。      |
|*fabricname*     |    已注册的 fabric 发生故障的组件名称。   |
|*说明*     |  已注册的结构组件的说明。   |
|*servicetype*     |   已注册的 fabric 服务的类型。   |
|*修正*     |   建议的修正步骤。    |
|*类型*     |   警报类型。    |
|*resourceRegistrationid*    |     受影响的已注册资源 ID。    |
|*resourceProviderRegistrationID*   |    受影响的组件的已注册的资源提供程序 ID。  |
|*serviceregistrationid*     |    已注册的服务 ID。   |
|*严重性*     |     警报严重性。  |
|*state*     |    警报状态。   |
|*标题*     |    警报标题。   |
|*impactedresourceid*     |     受影响资源的 ID。    |
|*ImpactedresourceDisplayName*     |     受影响资源的名称。  |
|*closedByUserAlias*     |   关闭警报的用户。      |

### <a name="close-alert"></a>关闭警报

**请求**

请求关闭警报由其唯一的 id。

|方法    |请求 URI  |
|---------|---------|
|PUT     |   https://{armendpoint}/subscriptions/{subId}/resourceGroups/system。{RegionName}/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/{RegionName}/Alerts/alertid?api-version=2016-05-01"    |

**参数**


|参数  |说明  |
|---------|---------|
|*armendpoint*     |   你的 Azure 堆栈环境，格式 https://adminmanagement 资源管理器终结点。{RegionName}。{外部 FQDN}。 例如，如果外部 FQDN 为*azurestack.external*和地区名称都是*本地*，则资源管理器终结点是 https://adminmanagement.local.azurestack.external。      |
|*subid*     |    进行调用的用户的订阅 ID。 你可以使用查询此 API 仅具有对默认提供程序订阅的权限的用户。     |
|*RegionName*     |   Azure 堆栈部署的区域名称。      |
|*api 版本*     |    用于发出此请求的协议的版本。 你必须使用 2016年-05-01。     |
|*alertid 匹配的警报*     |    警报的唯一 ID。     |

**正文**

```json

{
"value":[
{"id":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/alerts/71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"name":"71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"type":"Microsoft.InfrastructureInsights.Admin/regionHealths/alerts",
"location":"local",
"tags":{},
"properties":
{
"closedTimestamp":"2017-08-10T20:18:58.1584868Z",
"createdTimestamp":"2017-08-10T20:13:57.4398842Z",
"description":[{"text":"The infrastructure role (Updates) is experiencing issues.",
"type":"Text"}],
"faultId":"ServiceFabric:/UpdateResourceProvider/fabric:/AzurestackUpdateResourceProvider",
"alertId":"71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"faultTypeId":"ServiceFabricApplicationUnhealthy",
"lastUpdatedTimestamp":"2017-08-10T20:18:58.1584868Z",
"alertProperties":
{
"healthState":"Warning",
"name":"Updates",
"fabricName":"fabric:/AzurestackUpdateResourceProvider",
"description":null,
"serviceType":"UpdateResourceProvider"},
"remediation":[{"text":"1. Navigate to the (Updates) and restart the role. 2. If after closing the alert the issue persists, please contact support.",
"type":"Text"}],
"resourceRegistrationId":null,
"resourceProviderRegistrationId":"472aaaa6-3f63-43fa-a489-4fd9094e235f",
"serviceRegistrationId":"472aaaa6-3f63-43fa-a489-4fd9094e235f",
"severity":"Warning",
"state":"Closed",
"title":"Infrastructure role is unhealthy",
"impactedResourceId":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.Fabric.Admin/fabricLocations/local/infraRoles/UpdateResourceProvider",
"impactedResourceDisplayName":"UpdateResourceProvider",
"closedByUserAlias":null
}
},
```
**响应**

```http
PUT https://adminmanagement.local.azurestack.external//subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/alerts/71dbd379-1d1d-42e2-8439-6190cc7aa80b?api-version=2016-05-01 HTTP/1.1
```

```json
{
"value":[
{"id":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/alerts/71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"name":"71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"type":"Microsoft.InfrastructureInsights.Admin/regionHealths/alerts",
"location":"local",
"tags":{},
"properties":
{
"closedTimestamp":"",
"createdTimestamp":"2017-08-10T20:13:57.4398842Z",
"description":[{"text":"The infrastructure role (Updates) is experiencing issues.",
"type":"Text"}],
"faultId":"ServiceFabric:/UpdateResourceProvider/fabric:/AzurestackUpdateResourceProvider",
"alertId":"71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"faultTypeId":"ServiceFabricApplicationUnhealthy",
"lastUpdatedTimestamp":"2017-08-10T20:18:58.1584868Z",
"alertProperties":
{
"healthState":"Warning",
"name":"Updates",
"fabricName":"fabric:/AzurestackUpdateResourceProvider",
"description":null,
"serviceType":"UpdateResourceProvider"},
"remediation":[{"text":"1. Navigate to the (Updates) and restart the role. 2. If after closing the alert the issue persists, please contact support.",
"type":"Text"}],
"resourceRegistrationId":null,
"resourceProviderRegistrationId":"472aaaa6-3f63-43fa-a489-4fd9094e235f",
"serviceRegistrationId":"472aaaa6-3f63-43fa-a489-4fd9094e235f",
"severity":"Warning",
"state":"Closed",
"title":"Infrastructure role is unhealthy",
"impactedResourceId":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.Fabric.Admin/fabricLocations/local/infraRoles/UpdateResourceProvider",
"impactedResourceDisplayName":"UpdateResourceProvider",
"closedByUserAlias":null
}
},
```

**响应详细信息**


|  参数  |说明  |
|---------|---------|
|*id*     |      警报的唯一 ID。   |
|*name*     |     警报的内部名称。   |
|*类型*     |     资源定义。    |
|*位置*     |       区域名称。     |
|*标记*     |   资源标记。     |
|*closedtimestamp*    |  警报已关闭时的 UTC 时间。    |
|*createdtimestamp*     |     创建警报时的 UTC 时间。   |
|*说明*     |    警报的说明。     |
|*faultid*     | 受影响的组件。        |
|*alertid 匹配的警报*     |  警报的唯一 ID。       |
|*faulttypeid*     |  发生故障的组件的唯一类型。       |
|*lastupdatedtimestamp*     |   上次更新警报的信息的 UTC 时间。    |
|*healthstate*     | 总体运行状况状态。        |
|*name*     |   特定警报的名称。      |
|*fabricname*     |    已注册的 fabric 发生故障的组件名称。   |
|*说明*     |  已注册的结构组件的说明。   |
|*servicetype*     |   已注册的 fabric 服务的类型。   |
|*修正*     |   建议的修正步骤。    |
|*类型*     |   警报类型。    |
|*resourceRegistrationid*    |     受影响的已注册资源 ID。    |
|*resourceProviderRegistrationID*   |    受影响的组件的已注册的资源提供程序 ID。  |
|*serviceregistrationid*     |    已注册的服务 ID。   |
|*严重性*     |     警报严重性。  |
|*state*     |    警报状态。   |
|*标题*     |    警报标题。   |
|*impactedresourceid*     |     受影响资源的 ID。    |
|*ImpactedresourceDisplayName*     |     受影响资源的名称。  |
|*closedByUserAlias*     |   关闭警报的用户。      |

### <a name="get-resource-provider-health"></a>获取资源提供程序运行状况

**请求**

请求获取的所有已注册的资源提供程序的运行状况状态。


|方法  |请求 URI  |
|---------|---------|
|GET    |   https://{armendpoint}/subscriptions/{subId}/resourceGroups/system。{RegionName}/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/{RegionName}/serviceHealths?api-version=2016-05-01"   |


**参数**


|参数  |说明  |
|---------|---------|
|*armendpoint*     |    你的 Azure 堆栈环境，格式 https://adminmanagement 资源管理器终结点。{RegionName}。{外部 FQDN}。 例如，如果外部 FQDN 是 azurestack.external 且本地区域名称，则资源管理器终结点是 https://adminmanagement.local.azurestack.external。     |
|*subid*     |     进行调用的用户的订阅 ID。 你可以使用查询此 API 仅具有对默认提供程序订阅的权限的用户。    |
|*RegionName*     |     Azure 堆栈部署的区域名称。    |
|*api 版本*     |   用于发出此请求的协议的版本。 你必须使用 2016年-05-01。      |


**响应**

```http
GET https://adminmanagement.local.azurestack.external/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/serviceHealths?api-version=2016-05-01
```

```json
{
"value":[
{
"id":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/serviceHealths/03ccf38f-f6b1-4540-9dc8-ec7b6389ecca",
"name":"03ccf38f-f6b1-4540-9dc8ec7b6389ecca",
"type":"Microsoft.InfrastructureInsights.Admin/regionHealths/serviceHealths",
"location":"local",
"tags":{},
"properties":{
"registrationId":"03ccf38f-f6b1-4540-9dc8-ec7b6389ecca",
"displayName":"Key Vault",
"namespace":"Microsoft.KeyVault.Admin",
"routePrefix":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.KeyVault.Admin/locations/local",
"serviceLocation":"local",
"infraURI":"/subscriptions/4aa97de3-6b83-4582-86e1-65a5e4d1295b/resourceGroups/system.local/providers/Microsoft.KeyVault.Admin/locations/local/infraRoles/Key Vault",
"alertSummary":{"criticalAlertCount":0,"warningAlertCount":0},
"healthState":"Healthy"
}
}

…
```
**响应详细信息**


|参数  |说明  |
|---------|---------|
|*Id*     |   警报的唯一 ID。      |
|*name*     |  警报的内部名称。       |
|*类型*     |  资源定义。       |
|*位置*     |  区域名称。       |
|*标记*     |     资源标记。    |
|*registrationId*     |   资源提供程序的唯一注册。      |
|*displayName*     |资源提供程序显示名称。        |
|*命名空间*     |   API 命名空间资源提供程序实现。       |
|*routePrefix*     |    与资源提供程序交互的 URI。     |
|*serviceLocation*     |   此资源提供程序注册到的区域。      |
|*infraURI*     |   作为基础结构角色中列出的资源提供程序的 URI。      |
|*alertSummary*     |   与资源提供程序关联的关键和警告警报的摘要。      |
|*healthState*     |    资源提供程序的运行状况状态。     |


### <a name="get-resource-health"></a>获取资源运行状况

请求获取特定的已注册的资源提供程序的运行状况状态。

**请求**

|方法  |请求 URI  |
|---------|---------|
|GET     |     https://{armendpoint}/subscriptions/{subId}/resourceGroups/system。{RegionName}/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/{RegionName}/serviceHealths/{RegistrationID}/resourceHealths?api-version=2016-05-01"    |

**参数**

|参数  |说明  |
|---------|---------|
|*armendpoint*     |    你的 Azure 堆栈环境，格式 https://adminmanagement 资源管理器终结点。{RegionName}。{外部 FQDN}。 例如，如果外部 FQDN 是 azurestack.external 且本地区域名称，则资源管理器终结点是 https://adminmanagement.local.azurestack.external。     |
|*subid*     |进行调用的用户的订阅 ID。 你可以使用查询此 API 仅具有对默认提供程序订阅的权限的用户。         |
|*RegionName*     |  Azure 堆栈部署的区域名称。       |
|*api 版本*     |  用于发出此请求的协议的版本。 你必须使用 2016年-05-01。       |
|*RegistrationID* |特定的资源提供程序的注册 ID。 |

**响应**

```http
GET https://adminmanagement.local.azurestack.external/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/serviceHealths/03ccf38f-f6b1-4540-9dc8-ec7b6389ecca /resourceHealths?api-version=2016-05-01 HTTP/1.1
```

```json
{
"value":
[
{"id":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/serviceHealths/472aaaa6-3f63-43fa-a489-4fd9094e235f/resourceHealths/028c3916-ab86-4e7f-b5c2-0468e607915c",
"name":"028c3916-ab86-4e7f-b5c2-0468e607915c",
"type":"Microsoft.InfrastructureInsights.Admin/regionHealths/serviceHealths/resourceHealths",
"location":"local",
"tags":{},
"properties":
{"registrationId":"028c3916-ab86-4e7f-b5c2 0468e607915c","namespace":"Microsoft.Fabric.Admin","routePrefix":"/subscriptions/4aa97de3-6b83-4582-86e1 65a5e4d1295b/resourceGroups/system.local/providers/Microsoft.Fabric.Admin/fabricLocations/local",
"resourceType":"infraRoles",
"resourceName":"Privileged endpoint",
"usageMetrics":[],
"resourceLocation":"local",
"resourceURI":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.Fabric.Admin/fabricLocations/local/infraRoles/Privileged endpoint",
"rpRegistrationId":"472aaaa6-3f63-43fa-a489-4fd9094e235f",
"alertSummary":{"criticalAlertCount":0,"warningAlertCount":0},"healthState":"Unknown"
}
}
…
```

**响应详细信息**

|参数  |说明  |
|---------|---------|
|*Id*     |   警报的唯一 ID。      |
|*name*     |  警报的内部名称。       |
|*类型*     |  资源定义。       |
|*位置*     |  区域名称。       |
|*标记*     |     资源标记。    |
|*registrationId*     |   资源提供程序的唯一注册。      |
|*resourceType*     |资源的类型。        |
|*资源名称*     |   资源名称。   |
|*usageMetrics*     |    资源的使用情况度量值。     |
|*resourceLocation*     |   区域名称部署的位置。      |
|*resourceURI*     |   资源的 URI。   |
|*alertSummary*     |   摘要的关键和警告性警报，运行状况状态。     |

## <a name="next-steps"></a>后续步骤

- 有关内置的运行状况监视的信息，请参阅[监视运行状况和 Azure 堆栈中的警报](azure-stack-monitor-health.md)。


