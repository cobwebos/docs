---
title: Azure 应用程序 Insights IP 地址集合 |Microsoft Docs
description: 了解如何通过 Azure 应用程序 Insights 处理 IP 地址和地理位置
services: application-insights
author: mrbullwinkle
manager: carmonm
ms.assetid: 0e3b103c-6e2a-4634-9e8c-8b85cf5e9c84
ms.service: application-insights
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/24/2019
ms.author: mbullwin
ms.openlocfilehash: 4c60cb78c01d7e18801cbe43c8b767f622ef4b39
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2019
ms.locfileid: "68473093"
---
# <a name="geolocation-and-ip-address-handling"></a>地理位置和 IP 地址处理

本文介绍了如何在 Application Insights 中进行地理位置查找和 IP 地址处理, 以及如何修改默认行为。

## <a name="default-behavior"></a>默认行为

默认情况下, 将暂时收集 IP 地址, 但不会将其存储在 Application Insights 中。 基本过程如下所示:

IP 地址将作为遥测数据的一部分发送到 Application Insights。 到达 Azure 中的引入终结点后, IP 地址将用于通过[MaxMind 中的 GeoLite2](https://dev.maxmind.com/geoip/geoip2/geolite2/)执行地理位置查找。 此查找的结果用于填充以下字段`client_City` `client_StateOrProvince`:、 `client_CountryOrRegion`。 此时, 将丢弃 IP 地址并`0.0.0.0`将其写入`client_IP`字段。

* 浏览器遥测：我们暂时收集发件人的 IP 地址。 IP 地址由摄取终结点计算。
* 服务器遥测：Application Insights 模块暂时收集客户端 IP 地址。 如果设置了 `X-Forwarded-For`，则不会进行收集。

此行为是设计使然, 以帮助避免不必要的个人数据收集。 建议尽可能避免收集个人数据。 

## <a name="overriding-default-behavior"></a>重写默认行为

尽管默认行为是最大程度地减少个人数据收集, 但我们仍可以灵活地收集和存储 IP 地址数据。 在选择存储任何个人数据 (例如 IP 地址) 之前, 我们强烈建议验证是否不会违反任何符合性要求或可能受约束的本地法规。 若要详细了解 Application Insights 中的个人数据处理, 请参阅[有关个人数据的指南](https://docs.microsoft.com/azure/azure-monitor/platform/personal-data-mgmt)。

## <a name="storing-partial-ip-address-data"></a>存储部分 IP 地址数据

若要启用部分 IP 收集和存储, `DisableIpMasking`则必须将 Application Insights 组件的属性设置为。 `true` 可以通过 Azure 资源管理器模板或通过调用 REST API 来设置此属性。 将用最后一个八进制数来记录 IP 地址。


### <a name="azure-resource-manager-template"></a>Azure 资源管理器模板

```json
{
       "id": "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/microsoft.insights/components/<resource-name>",
       "name": "<resource-name>",
       "type": "microsoft.insights/components",
       "location": "westcentralus",
       "tags": {
              
       },
       "kind": "web",
       "properties": {
              "Application_Type": "web",
              "Flow_Type": "Redfield",
              "Request_Source": "IbizaAIExtension",
              // ...
              "DisableIpMasking": true
       }
}
```

### <a name="portal"></a>门户 

如果只需要修改单个 Application Insights 资源的行为, 最简单的方法是通过 Azure 门户完成此操作。  

1. 开始 Application Insights 资源 >**设置** > **导出模板** 

    ![导出模板](media/ip-collection/export-template.png)

2. 选择**部署**

    ![以红色突出显示的 "部署" 按钮](media/ip-collection/deploy.png)

3. 选择 "**编辑模板**"。 (如果模板具有此示例模板中未显示的其他属性或资源, 请务必小心, 以确保所有资源都接受模板部署作为增量更改/更新。)

    ![编辑模板](media/ip-collection/edit-template.png)

4. 对资源进行以下更改, 然后单击 "**保存**":

    ![屏幕截图将在 "IbizaAIExtension" 后添加一个逗号, 并在下面添加新行, 其中包含 "DisableIpMasking": true](media/ip-collection/save.png)

    > [!NOTE]
    > 如果遇到错误, 请注意:_资源组所在的位置不受模板中的一个或多个资源支持。请选择其他资源组。_ 临时从下拉列表中选择不同的资源组, 然后重新选择原始资源组以解决此错误。

5. 选择 "**我同意** > **购买**"。 

    ![编辑模板](media/ip-collection/purchase.png)

    在这种情况下, 不会进行任何新的购买, 只是更新现有 Application Insights 资源的配置。

6. 部署完成后, 将会记录前三个八位字节, 其中填充了 IP, 最后一个八进制数为零。

    如果再次选择和编辑模板, 则只会看到默认模板, 而不会显示新添加的属性及其关联值。 如果看不到 IP 地址数据, 并希望确认`"DisableIpMasking": true`已设置了。 运行以下 PowerShell:(替换`Fabrikam-dev`为适当的资源和资源组名称。)
    
    ```powershell
    # If you aren't using the cloud shell you will need to connect to your Azure account
    # Connect-AzAccount 
    $AppInsights = Get-AzResource -Name 'Fabrikam-dev' -ResourceType 'microsoft.insights/components' -ResourceGroupName 'Fabrikam-dev'
    $AppInsights.Properties
    ```
    
    结果将返回属性列表。 其中一个属性应该会被`DisableIpMasking: true`读取。 如果在部署具有 Azure 资源管理器的新属性之前运行 PowerShell, 则该属性将不存在。

### <a name="rest-api"></a>REST API

执行相同修改的[REST API](https://docs.microsoft.com/rest/api/azure/)有效负载如下所示:

```
PATCH https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/microsoft.insights/components/<resource-name>?api-version=2018-05-01-preview HTTP/1.1
Host: management.azure.com
Authorization: AUTH_TOKEN
Content-Type: application/json
Content-Length: 54

{
       "location": "<resource location>",
       "kind": "web",
       "properties": {
              "Application_Type": "web",
              "DisableIpMasking": true
       }
}
```

## <a name="telemetry-initializer"></a>遥测初始化程序

如果需要记录整个 IP 地址而不只是前三个八位字节, 可以使用[遥测初始值设定项](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#add-properties-itelemetryinitializer)将 IP 地址复制到不会被屏蔽的自定义字段。

### <a name="aspnetaspnet-core"></a>ASP.NET/ASP.NET 核心

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;

namespace MyWebApp
{
    public class CloneIPAddress : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            if(!string.IsNullOrEmpty(telemetry.Context.Location.Ip))
            {
                telemetry.Context.Properties["client-ip"] = telemetry.Context.Location.Ip;
            }
        }
    }

}
```

### <a name="enable-telemetry-initializer-for-aspnet"></a>为启用遥测初始值设定项。ASP.NET

```csharp
using Microsoft.ApplicationInsights.Extensibility;


namespace MyWebApp
{
    public class MvcApplication : System.Web.HttpApplication
    {
        protected void Application_Start()
        {
              //Enable your telemetry initializer:
              TelemetryConfiguration.Active.TelemetryInitializers.Add(new CloneIPAddress());
        }
    }
}

```

### <a name="enable-telemetry-initializer-for-aspnet-core"></a>为 ASP.NET Core 启用遥测初始化表达式

可以采用与 ASP.NET 相同的 ASP.NET Core 方式创建遥测初始值设定项, 但要启用初始值设定项, 请使用以下示例进行引用:

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, CloneIPAddress>();
}
```

### <a name="nodejs"></a>Node.js

```javascript
appInsights.defaultClient.addTelemetryProcessor((envelope) => {
    const baseData = envelope.data.baseData;
    if (appInsights.Contracts.domainSupportsProperties(baseData)) {
        const ipAddress = envelope.tags[appInsights.defaultClient.context.keys.locationIp];
        if (ipAddress) {
            baseData.properties["client-ip"] = ipAddress;
        }
    }
});
```

### <a name="client-side-javascript"></a>客户端 JavaScript

与服务器端 Sdk 不同, 客户端 Javascript SDK 不会计算 IP 地址。 默认情况下, 在遥测到达时, 会在 Azure 中的引入终结点上执行客户端遥测的 IP 地址计算。 这意味着, 如果要将客户端数据发送到代理, 然后将其转发到摄取终结点, 则 IP 地址计算可能会显示代理的 IP 地址, 而不是客户端的 IP 地址。 如果未使用代理, 则不会出现问题。

如果希望直接在客户端上计算 IP 地址, 则需要添加自己的自定义逻辑来执行此计算, 并使用结果来设置`ai.location.ip`标记。 如果`ai.location.ip`设置了, 则不会通过摄取终结点执行 IP 地址计算, 并使用提供的 ip 地址进行地域查找。 在这种情况下, 默认情况下, IP 地址仍将归零。 

若要保留通过自定义逻辑计算的整个 IP 地址, 可以使用遥测初始值设定项, 将您在中提供的 ip 地址`ai.location.ip`数据复制到单独的自定义字段中。 但再次不同于服务器端 Sdk, 无需依赖第三方库或你自己的自定义客户端 IP 集合逻辑, 客户端 SDK 将不会为你计算 IP。    


```javascript
appInsights.addTelemetryInitializer((item) => {
    const ipAddress = item.tags && item.tags["ai.location.ip"];
    if (ipAddress) {
        item.baseData.properties = {
            ...item.baseData.properties,
            "client-ip": ipAddress
        };
    }
});

```  

### <a name="view-the-results-of-your-telemetry-initializer"></a>查看遥测初始值设定项的结果

如果随后针对站点触发了新的流量并等待大约2-5 分钟, 以确保有时间引入, 可以运行 Kusto 查询来查看 IP 地址收集是否正常工作:

```kusto
requests
| where timestamp > ago(1h) 
| project appName, operation_Name, url, resultCode, client_IP, customDimensions.["client-ip"]
```

新收集的`customDimensions_client-ip` IP 地址应显示在列中。 默认`client-ip`列仍将包含所有4个八进制数, 或者只显示前三个八进制数, 具体取决于在组件级别配置 IP 地址集合的方式。 如果在实现了遥测初始值设定项后在本地进行测试, 则会`customDimensions_client-ip`出现`::1`这种情况。 `::1`表示 IPv6 中的环回地址。 它等效`127.0.01`于 IPv4 中的, 这是从 localhost 进行测试时看到的结果。

## <a name="next-steps"></a>后续步骤

* 详细了解 Application Insights 中的[个人数据收集](https://docs.microsoft.com/azure/azure-monitor/platform/personal-data-mgmt)。

* 详细了解 Application Insights 中的[IP 地址收集](https://apmtips.com/blog/2016/07/05/client-ip-address/)的工作方式。 (这是一个由我们的工程师撰写的旧外部博客文章。 它早于将 IP 地址记录为`0.0.0.0`的当前默认行为, 但它更深入地介绍了内置`ClientIpHeaderTelemetryInitializer`的机制。)