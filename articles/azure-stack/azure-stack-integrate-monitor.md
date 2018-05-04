---
title: 将外部监视解决方案与 Azure Stack 集成 | Microsoft Docs
description: 了解如何将 Azure Stack 与数据中心内的外部监视解决方案集成。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: 856738a7-1510-442a-88a8-d316c67c757c
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/01/2018
ms.author: jeffgilb
ms.reviewer: wfayed
ms.openlocfilehash: 4188d114aa86086821b2c640d7f2d98a78bcbf4e
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2018
---
# <a name="integrate-external-monitoring-solution-with-azure-stack"></a>将外部监视解决方案与 Azure Stack 集成

要在外部监视 Azure Stack 基础结构，需要监视 Azure Stack 软件、物理计算机和物理网络交换机。 上述每个监视区域都提供相应的方法来检索运行状况和警报信息：

- Azure Stack 软件提供基于 REST 的 API 来检索运行状况和警报。 （存储运行状况和警报是软件监视的一部分，使用“存储空间直通”等软件定义的技术）。
- 物理计算机可以通过基板管理控制器 (BMC) 提供运行状况和警报信息。
- 物理网络设备可以通过 SNMP 协议提供运行状况和警报信息。

每个 Azure Stack 解决方案随附硬件生命周期主机。 此主机针对物理服务器和网络设备运行原始设备制造商 (OEM) 硬件供应商的监视软件。 如果需要，可以绕过这些监视解决方案，直接与数据中心内现有的监视解决方案集成。

> [!IMPORTANT]
> 使用的外部监视解决方案必须无代理。 不能在 Azure Stack 组件内部安装第三方代理。

下图演示 Azure Stack 集成系统、硬件生命周期主机、外部监视解决方案与外部票证/数据收集系统之间的流量流。

![显示 Azure Stack、监视与票证解决方案之间的流量的示意图。](media/azure-stack-integrate-monitor/MonitoringIntegration.png)  

本文介绍如何将 Azure Stack 与外部监视解决方案（例如 System Center Operations Manager 和 Nagios）集成。 此外，还介绍如何使用 PowerShell 或 REST API 调用以编程方式处理警报。

## <a name="integrate-with-operations-manager"></a>与 Operations Manager 集成

可将 Operations Manager 用于 Azure Stack 的外部监视。 用于 Microsoft Azure 堆栈的 System Center 管理包可监视多个 Azure 堆栈部署与单个的 Operations Manager 实例。 该管理包使用运行状况资源提供程序和更新资源提供程序 REST API 来与 Azure Stack 通信。 如果打算绕过硬件生命周期主机上运行的 OEM 监视软件，则可以安装供应商管理包来监视物理服务器。 还可以使用 Operations Manager 网络设备发现来监视网络交换机。

适用于 Azure Stack 的管理包提供以下功能：

- 可以管理多个 Azure Stack 部署
- 支持 Azure Active Directory (Azure AD) 和 Active Directory 联合身份验证服务 (AD FS)
- 可以检索和关闭警报
- 提供运行状况和容量仪表板
- 正在修补和更新 (P&U) 时可以执行自动维护模式检测
- 包含针对部署和区域的强制更新任务
- 可将自定义信息添加到区域
- 支持通知和报告

你可以下载 System Center 管理包为 Microsoft Azure 堆栈和关联[用户指南](https://www.microsoft.com/en-us/download/details.aspx?id=55184)，或直接从 Operations Manager。

对于票证解决方案，可将 Operations Manager 与 System Center Service Manager 集成。 集成的产品连接器支持双向通信，可让你在解决 Service Manager 中的服务请求之后关闭 Azure Stack 和 Operations Manager 中的警报。

下图演示了 Azure Stack 与现有 System Center 部署的集成。 可以进一步使用 System Center Orchestrator 或 Service Management Automation (SMA) 将 Service Manager 自动化，以便在 Azure Stack 中运行操作。

![演示与 OM、Service Manager 和 SMA 集成的示意图。](media/azure-stack-integrate-monitor/SystemCenterIntegration.png)

## <a name="integrate-with-nagios"></a>与 Nagios 集成

监视插件 Nagios 开发以及合作伙伴 Cloudbase 解决方案，这是宽松免费软件许可证 – 麻省理工学院 (Massachusetts Institute of Technology) 下可用。

该插件以 Python 编写，利用运行状况资源提供程序 REST API。 它提供在 Azure Stack 中检索和关闭警报的基本功能。 与 System Center 管理包一样，它可以让你添加多个 Azure Stack 部署以及发送通知。

该插件可与 Nagios Enterprise 和 Nagios Core 配合使用。 可以在[此处](https://exchange.nagios.org/directory/Plugins/Cloud/Monitoring-AzureStack-Alerts/details)下载。 下载站点还包含安装和配置详细信息。

### <a name="plugin-parameters"></a>插件参数

使用以下参数来配置插件文件“Azurestack_plugin.py”：

| 参数 | 说明 | 示例 |
|---------|---------|---------|
| *arm_endpoint* | Azure 资源管理器（管理员）终结点 |https://adminmanagement.local.azurestack.external |
| *api_endpoint* | Azure 资源管理器（管理员）终结点  | https://adminmanagement.local.azurestack.external |
| *Tenant_id* | 管理员订阅 ID | 通过管理员门户或 PowerShell 检索 |
| *User_name* | 操作员订阅用户名 | operator@myazuredirectory.onmicrosoft.com |
| *User_password* | 操作员订阅密码 | mypassword |
| *Client_id* | Client | 0a7bdc5c-7b57-40be-9939-d4c5fc7cd417* |
| *region* |  Azure Stack 区域名称 | local |
|  |  |

*提供的 PowerShell GUID 是通用的。 可对每个部署使用它。

## <a name="use-powershell-to-monitor-health-and-alerts"></a>使用 PowerShell 监视运行状况和警报

如果不使用 Operations Manager、Nagios 或基于 Nagios 的解决方案，可以使用 PowerShell 来启用广泛的监视解决方案，以便与 Azure Stack 集成。
 
1. 若要使用 PowerShell，请确保已针对 Azure Stack 操作员环境[安装并配置 PowerShell](azure-stack-powershell-configure-quickstart.md)。 在可以访问资源管理器 （管理员） 终结点的本地计算机上安装 PowerShell (https://adminmanagement。 [区域]。[External_FQDN])。

2. 以 Azure Stack 操作员身份运行以下命令，以连接到 Azure Stack 环境：

   ```PowerShell
   Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint https://adminmanagement.[Region].[External_FQDN]

   Add-AzureRmAccount -EnvironmentName "AzureStackAdmin"
   ```
3. 切换到安装 PowerShell 期间安装的 [Azure Stack 工具](https://github.com/Azure/AzureStack-Tools)所在的目录，例如 c:\azurestack-tools-master。 然后切换到 Infrastructure 目录，并运行以下命令导入基础结构模块：

   ```PowerShell
   Import-Module .\AzureStack.Infra.psm1
    ```
4. 使用如下所示的命令来处理警报：
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

## <a name="use-the-rest-api-to-monitor-health-and-alerts"></a>使用 REST API 监视运行状况和警报

可以使用 REST API 调用获取警报、关闭警报，以及获取资源提供程序的运行状况。

### <a name="get-alert"></a>获取警报

**请求**

该请求获取默认提供商订阅的所有活动警报和已关闭的警报。 没有请求正文。


|方法  |请求 URI  |
|---------|---------|
|GET     |   https://{armendpoint}/subscriptions/{subId}/resourceGroups/system.{RegionName}/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/{RegionName}/Alerts?api-version=2016-05-01"      |
|     |         |

**参数**

|参数  |说明  |
|---------|---------|
|armendpoint     |  你的 Azure 堆栈环境，格式为 Azure 资源管理器终结点https://adminmanagement.{RegionName}.{ExternalFQDN}。 例如，如果外部 FQDN 为*azurestack.external*和地区名称都是*本地*，则资源管理器终结点是https://adminmanagement.local.azurestack.external。       |
|subid     |   正在发出调用的用户的订阅 ID。 只能通过拥有默认提供商订阅权限的用户身份使用此 API 进行查询。      |
|RegionName     |    Azure Stack 部署的区域名称。     |
|api-version     |  用于发出此请求的协议版本。 必须使用 2016-05-01。      |
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
|*closedtimestamp*    |  关闭警报时的 UTC 时间。    |
|*createdtimestamp*     |     创建警报时的 UTC 时间。   |
|*说明*     |    警报的说明。     |
|*faultid*     | 受影响的组件。        |
|*alertid*     |  警报的唯一 ID。       |
|*faulttypeid*     |  有故障组件的唯一类型。       |
|*lastupdatedtimestamp*     |   上次更新警报信息时的 UTC 时间。    |
|*healthstate*     | 总体运行状况。        |
|*name*     |   特定警报的名称。      |
|*fabricname*     |    有故障组件的已注册结构名称。   |
|*说明*     |  已注册结构组件的说明。   |
|*servicetype*     |   已注册结构服务的类型。   |
|*remediation*     |   建议的补救步骤。    |
|*类型*     |   警报类型。    |
|*resourceRegistrationid*    |     受影响的已注册资源的 ID。    |
|*resourceProviderRegistrationID*   |    受影响组件的已注册资源提供程序的 ID。  |
|*serviceregistrationid*     |    已注册服务的 ID。   |
|*severity*     |     警报严重性。  |
|*state*     |    警报状态。   |
|*title*     |    警报标题。   |
|*impactedresourceid*     |     受影响资源的 ID。    |
|*ImpactedresourceDisplayName*     |     受影响资源的名称。  |
|*closedByUserAlias*     |   关闭警报的用户。      |

### <a name="close-alert"></a>关闭警报

**请求**

该请求按警报的唯一 ID 关闭警报。

|方法    |请求 URI  |
|---------|---------|
|PUT     |   https://{armendpoint}/subscriptions/{subId}/resourceGroups/system.{RegionName}/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/{RegionName}/Alerts/alertid?api-version=2016-05-01"    |

**参数**


|参数  |说明  |
|---------|---------|
|*armendpoint*     |   资源管理器终结点的 Azure 堆栈环境，采用格式https://adminmanagement.{RegionName}.{ExternalFQDN}。 例如，如果外部 FQDN 为*azurestack.external*和地区名称都是*本地*，则资源管理器终结点是https://adminmanagement.local.azurestack.external。      |
|*subid*     |    正在发出调用的用户的订阅 ID。 只能通过拥有默认提供商订阅权限的用户身份使用此 API 进行查询。     |
|*RegionName*     |   Azure Stack 部署的区域名称。      |
|*api-version*     |    用于发出此请求的协议版本。 必须使用 2016-05-01。     |
|*alertid*     |    警报的唯一 ID。     |

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
|*closedtimestamp*    |  关闭警报时的 UTC 时间。    |
|*createdtimestamp*     |     创建警报时的 UTC 时间。   |
|*说明*     |    警报的说明。     |
|*faultid*     | 受影响的组件。        |
|*alertid*     |  警报的唯一 ID。       |
|*faulttypeid*     |  有故障组件的唯一类型。       |
|*lastupdatedtimestamp*     |   上次更新警报信息时的 UTC 时间。    |
|*healthstate*     | 总体运行状况。        |
|*name*     |   特定警报的名称。      |
|*fabricname*     |    有故障组件的已注册结构名称。   |
|*说明*     |  已注册结构组件的说明。   |
|*servicetype*     |   已注册结构服务的类型。   |
|*remediation*     |   建议的补救步骤。    |
|*类型*     |   警报类型。    |
|*resourceRegistrationid*    |     受影响的已注册资源的 ID。    |
|*resourceProviderRegistrationID*   |    受影响组件的已注册资源提供程序的 ID。  |
|*serviceregistrationid*     |    已注册服务的 ID。   |
|*severity*     |     警报严重性。  |
|*state*     |    警报状态。   |
|*title*     |    警报标题。   |
|*impactedresourceid*     |     受影响资源的 ID。    |
|*ImpactedresourceDisplayName*     |     受影响资源的名称。  |
|*closedByUserAlias*     |   关闭警报的用户。      |

### <a name="get-resource-provider-health"></a>获取资源提供程序运行状况

**请求**

该请求获取所有已注册资源提供程序的运行状况。


|方法  |请求 URI  |
|---------|---------|
|GET    |   https://{armendpoint}/subscriptions/{subId}/resourceGroups/system.{RegionName}/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/{RegionName}/serviceHealths?api-version=2016-05-01"   |


**参数**


|参数  |说明  |
|---------|---------|
|*armendpoint*     |    你的 Azure 堆栈环境，采用格式的资源管理器终结点https://adminmanagement.{RegionName}.{ExternalFQDN}。 例如，如果外部 FQDN 是 azurestack.external 且本地区域名称，则资源管理器终结点是https://adminmanagement.local.azurestack.external。     |
|*subid*     |     正在发出调用的用户的订阅 ID。 只能通过拥有默认提供商订阅权限的用户身份使用此 API 进行查询。    |
|*RegionName*     |     Azure Stack 部署的区域名称。    |
|*api-version*     |   用于发出此请求的协议版本。 必须使用 2016-05-01。      |


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
|*displayName*     |资源提供程序的显示名称。        |
|*namespace*     |   资源提供程序实现的 API 命名空间。       |
|*routePrefix*     |    用来与资源提供程序交互的 URI。     |
|*serviceLocation*     |   此资源提供程序注册到的区域。      |
|*infraURI*     |   列为基础结构角色的资源提供程序的 URI。      |
|*alertSummary*     |   与资源提供程序关联的严重警报和警告警报的摘要。      |
|*healthState*     |    资源提供程序的运行状况。     |


### <a name="get-resource-health"></a>获取资源运行状况

该请求获取特定已注册资源提供程序的运行状况。

**请求**

|方法  |请求 URI  |
|---------|---------|
|GET     |     https://{armendpoint}/subscriptions/{subId}/resourceGroups/system.{RegionName}/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/{RegionName}/serviceHealths/{RegistrationID}/resourceHealths?api-version=2016-05-01"    |

**参数**

|参数  |说明  |
|---------|---------|
|*armendpoint*     |    你的 Azure 堆栈环境，采用格式的资源管理器终结点https://adminmanagement.{RegionName}.{ExternalFQDN}。 例如，如果外部 FQDN 是 azurestack.external 且本地区域名称，则资源管理器终结点是https://adminmanagement.local.azurestack.external。     |
|*subid*     |正在发出调用的用户的订阅 ID。 只能通过拥有默认提供商订阅权限的用户身份使用此 API 进行查询。         |
|*RegionName*     |  Azure Stack 部署的区域名称。       |
|*api-version*     |  用于发出此请求的协议版本。 必须使用 2016-05-01。       |
|*RegistrationID* |特定资源提供程序的注册 ID。 |

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
|*resourceName*     |   资源名称。   |
|*usageMetrics*     |    资源的用量指标。     |
|*resourceLocation*     |   部署区域的名称。      |
|*resourceURI*     |   资源的 URI。   |
|*alertSummary*     |   严重警报、警告警报和运行状况的摘要。     |

## <a name="learn-more"></a>了解详细信息

有关内置运行状况监视的信息，请参阅[在 Azure Stack 中监视运行状况和警报](azure-stack-monitor-health.md)。


## <a name="next-steps"></a>后续步骤

[安全集成](azure-stack-integrate-security.md)
