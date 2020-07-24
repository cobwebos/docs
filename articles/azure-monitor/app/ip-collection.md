---
title: Azure Application Insights IP 地址收集 | Microsoft Docs
description: 了解如何使用 Azure Application Insights 处理 IP 地址和地理位置
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: 1576207eb267166a33b84009407ec119de471e6e
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87014433"
---
# <a name="geolocation-and-ip-address-handling"></a>地理位置和 IP 地址处理

本文介绍 Application Insights 中的地理位置查找和 IP 地址处理，以及如何修改默认行为。

## <a name="default-behavior"></a>默认行为

默认情况下，IP 地址是暂时收集的，不会存储在 Application Insights 中。 基本过程如下：

IP 地址作为遥测数据的一部分发送到 Application Insights。 IP 地址抵达 Azure 中的引入终结点后，将用于通过 [MaxMind 中的 GeoLite2](https://dev.maxmind.com/geoip/geoip2/geolite2/) 执行地理位置查找。 此查找的结果用于填充 `client_City`、`client_StateOrProvince` 和 `client_CountryOrRegion` 字段。 此时，将丢弃 IP 地址，并将 `0.0.0.0` 写入 `client_IP` 字段。

* 浏览器遥测：我们会暂时收集发送方的 IP 地址。 IP 地址由引入终结点计算。
* 服务器遥测：Application Insights 模块暂时收集客户端 IP 地址。 如果设置了 `X-Forwarded-For`，则不会进行收集。

此行为是有意设计的，目的是帮助避免不必要地收集个人数据。 我们建议尽量避免收集个人数据。 

## <a name="overriding-default-behavior"></a>重写默认行为

尽管默认行为是尽量避免收集个人数据，但我们仍然可以让用户灵活收集和存储 IP 地址数据。 在存储任何个人数据（例如 IP 地址）之前，我们强烈建议确认此行为不会违反任何需要遵守的合规要求或当地法规。 若要详细了解 Application Insights 中的个人数据处理，请参阅[个人数据指南](../platform/personal-data-mgmt.md)。

## <a name="storing-ip-address-data"></a>存储 IP 地址数据

若要启用 IP 收集和存储，必须将 Application Insights 组件的 `DisableIpMasking` 属性设置为 `true`。 可以通过 Azure 资源管理器模板或调用 REST API 来设置此属性。 

### <a name="azure-resource-manager-template"></a>Azure Resource Manager 模板

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

如果只需修改单个 Application Insights 资源的行为，最简单的方法是使用 Azure 门户。  

1. 转到你的 Application Insights 资源并选择“设置” > “导出模板”  

    ![导出模板](media/ip-collection/export-template.png)

2. 选择“部署”

    ![以红色突出显示的“部署”按钮](media/ip-collection/deploy.png)

3. 选择“编辑模板”。 （如果你的模板包含未显示在此示例模板中的其他属性或资源，请小心地继续操作，确保所有资源接受模板部署作为增量更改/更新。）

    ![编辑模板](media/ip-collection/edit-template.png)

4. 对资源的 json 进行以下更改，然后单击“保存”：

    ![在屏幕截图中，“IbizaAIExtension”的后面添加了一个逗号，并在下面添加了包含 "DisableIpMasking": true 的新行](media/ip-collection/save.png)

    > [!WARNING]
    > 如果遇到了包含以下消息的错误：“资源组所在的位置不受模板中的一个或多个资源支持。请选择其他资源组。” 请从下拉列表中暂时选择另一个资源组，然后重新选择原始资源组来解决此错误。

5. 选择“我同意” > “购买”。  

    ![编辑模板](media/ip-collection/purchase.png)

    在本例中，我们无需购买任何新的服务，只是更新现有 Application Insights 资源的配置。

6. 部署完成后，将会记录新的遥测数据。

    如果再次选择并编辑模板，只会看到默认模板，而看不到新添加的属性及其关联值。 如果未看到 IP 地址数据并希望确认是否已设置 `"DisableIpMasking": true`， 请运行以下 PowerShell：（请将 `Fabrikam-dev` 替换为相应的资源和资源组名称。）
    
    ```powershell
    # If you aren't using the cloud shell you will need to connect to your Azure account
    # Connect-AzAccount 
    $AppInsights = Get-AzResource -Name 'Fabrikam-dev' -ResourceType 'microsoft.insights/components' -ResourceGroupName 'Fabrikam-dev'
    $AppInsights.Properties
    ```
    
    将返回属性列表作为结果。 其中一个属性显示为 `DisableIpMasking: true`。 如果在使用 Azure 资源管理器部署新属性之前运行 PowerShell，该属性将不存在。

### <a name="rest-api"></a>REST API

用于做出相同修改的 [REST API](/rest/api/azure/) 有效负载如下：

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

如果需要通过比 `DisableIpMasking` 更灵活的方式来记录所有或一部分 IP 地址，可以使用[遥测初始化表达式](./api-filtering-sampling.md#addmodify-properties-itelemetryinitializer)将所有或一部分 IP 复制到自定义字段。 

### <a name="aspnet--aspnet-core"></a>ASP.NET / ASP.NET Core

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;

namespace MyWebApp
{
    public class CloneIPAddress : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            ISupportProperties propTelemetry = telemetry as ISupportProperties;

            if (propTelemetry !=null && !propTelemetry.Properties.ContainsKey("client-ip"))
            {
                string clientIPValue = telemetry.Context.Location.Ip;
                propTelemetry.Properties.Add("client-ip", clientIPValue);
            }
        }
    } 
}
```

> [!NOTE]
> 如果无法访问 `ISupportProperties`，请检查并确保运行最新稳定版本的 Application Insights SDK。 `ISupportProperties` 适合用于高基数值，而 `GlobalProperties` 更适合用于低基数值，如区域名称、环境名称等。 

### <a name="enable-telemetry-initializer-for-aspnet"></a>为 ASP.NET 启用遥测初始化表达式

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

对于 ASP.NET Core，可以采用与 ASP.NET 相同的方式创建遥测初始化表达式，但若要启用初始化表达式，请参考以下示例：

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

与服务器端 SDK 不同，客户端 Javascript SDK 不会计算 IP 地址。 默认情况下，客户端遥测的 IP 地址计算是在遥测数据抵达时，在 Azure 中的引入终结点上执行的。 这意味着，如果将客户端数据发送到代理，然后将其转发到引入终结点，则 IP 地址计算可能会显示代理而不是客户端的 IP 地址。 如果不使用代理，则没有关系。

如果你希望直接在客户端上计算 IP 地址，则需要添加自己的自定义逻辑来执行此计算，并使用结果来设置 `ai.location.ip` 标记。 设置 `ai.location.ip` 后，IP 地址计算不会由引入终结点执行，同时会遵循提供的 IP 地址并使用它来执行地理查找。 在这种情况下，IP 地址默认仍会归零。 

若要保留自定义逻辑计算的整个 IP 地址，可以使用遥测初始化表达式将 `ai.location.ip` 中提供的 IP 地址数据复制到单独的自定义字段。 但同样，与服务器端 SDK 不同，如果不依赖第三方库或你自己的自定义客户端 IP 收集逻辑，客户端 SDK 不会计算 IP。    


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

### <a name="view-the-results-of-your-telemetry-initializer"></a>查看遥测初始化表达式的结果

如果随后针对站点触发新流量并等待大约 2-5 分钟来确保有时间引入该流量，则可以运行 Kusto 查询来查看 IP 地址收集是否正常工作：

```kusto
requests
| where timestamp > ago(1h) 
| project appName, operation_Name, url, resultCode, client_IP, customDimensions.["client-ip"]
```

新收集的 IP 地址应显示在 `customDimensions_client-ip` 列中。 默认的 `client-ip` 列仍包含所有 4 个八进制数，这些数字已归零，或者仅显示前 3 个八进制数，具体取决于在组件级别配置 IP 地址收集的方式。 如果在实现遥测初始化表达式后在本地进行测试，并且 `customDimensions_client-ip` 的值为 `::1`，则这是预期的行为。 `::1` 表示 IPv6 中的环回地址。 它等效于 IPv4 中的 `127.0.01`，是从 localhost 测试时看到的结果。

## <a name="next-steps"></a>后续步骤

* 详细了解 Application Insights 中的[个人数据收集](../platform/personal-data-mgmt.md)。

* 详细了解 Application Insights 中 [IP 地址收集](https://apmtips.com/posts/2016-07-05-client-ip-address/)的工作原理。 （这是我们的某位工程师在较早前撰写的一篇外部博客文章。 其中所述的机制不同当前的默认行为，现在，IP 地址将记录为 `0.0.0.0`，不过此文更深入地描述了内置 `ClientIpHeaderTelemetryInitializer` 的机制。）
