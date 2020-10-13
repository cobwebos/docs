---
title: Azure Application Insights IP 地址收集 | Microsoft Docs
description: 了解如何使用 Azure Application Insights 处理 IP 地址和地理位置
ms.topic: conceptual
ms.date: 09/23/2020
ms.custom: devx-track-js
ms.openlocfilehash: 6131105ef78a8559b0fb95043a87e562e887ebfd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91333301"
---
# <a name="geolocation-and-ip-address-handling"></a>地理位置和 IP 地址处理

本文介绍如何在 Application Insights 中使用地理位置查找和 IP 地址处理，以及如何修改默认行为。

## <a name="default-behavior"></a>默认行为

默认情况下，IP 地址是暂时收集的，不会存储在 Application Insights 中。 基本过程如下：

将遥测发送到 Azure 时，使用 IP 地址通过 [MaxMind 中的 GeoLite2](https://dev.maxmind.com/geoip/geoip2/geolite2/)执行地理位置查找。 此查找的结果用于填充字段 `client_City` 、 `client_StateOrProvince` 和 `client_CountryOrRegion` 。 然后丢弃地址，并 `0.0.0.0` 将其写入 `client_IP` 字段。

* 浏览器遥测：我们会暂时收集发送方的 IP 地址。 IP 地址由引入终结点计算。
* 服务器遥测： Application Insights 遥测模块暂时收集客户端 IP 地址。 如果 `X-Forwarded-For` 设置了标头，则不会在本地收集 IP 地址。

此行为是有意设计的，目的是帮助避免不必要地收集个人数据。 我们建议尽量避免收集个人数据。 

## <a name="overriding-default-behavior"></a>重写默认行为

默认情况下，不收集 IP 地址。 我们仍提供重写此行为的灵活性。 但是，我们建议验证该集合是否不违反任何符合性要求或本地法规。 

若要详细了解 Application Insights 中的个人数据处理，请参阅[个人数据指南](../platform/personal-data-mgmt.md)。

## <a name="storing-ip-address-data"></a>存储 IP 地址数据

若要启用 IP 收集和存储，则 `DisableIpMasking` 必须将 Application Insights 组件的属性设置为 `true` 。 可以通过 Azure 资源管理器模板或通过调用 REST API 来设置此属性。 

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

如果只需要修改单个 Application Insights 资源的行为，请使用 Azure 门户。 

1. 将 Application Insights 资源 >**自动**  >  **导出模板** 

2. 选择“部署”

    ![以红色突出显示 word "Deploy" 的按钮](media/ip-collection/deploy.png)

3. 选择“编辑模板”。

    ![红色突出显示了 "编辑" 的按钮](media/ip-collection/edit-template.png)

4. 对资源进行以下更改，然后选择 " **保存**"：

    ![在屏幕截图中，“IbizaAIExtension”的后面添加了一个逗号，并在下面添加了包含 "DisableIpMasking": true 的新行](media/ip-collection/save.png)

    > [!WARNING]
    > 如果遇到了包含以下消息的错误：“资源组所在的位置不受模板中的一个或多个资源支持。请选择其他资源组。” 请从下拉列表中暂时选择另一个资源组，然后重新选择原始资源组来解决此错误。

5. 选择“我同意” > “购买”。  

    ![选中 "我同意上述条款和条件" 复选框，并在按钮上面以红色突出显示 "购买" 字样的按钮。](media/ip-collection/purchase.png)

    在这种情况下，实际不会购买任何新内容。 我们仅更新现有 Application Insights 资源的配置。

6. 部署完成后，将记录新的遥测数据。

    如果再次选择和编辑模板，则仅会看到默认模板，但不会显示新添加的属性。 如果看不到 IP 地址数据，并希望确认 `"DisableIpMasking": true` 已设置，请运行以下 PowerShell： 
    
    ```powershell
    # Replace `Fabrikam-dev` with the appropriate resource and resource group name.
    # If you aren't using the cloud shell you will need to connect to your Azure account
    # Connect-AzAccount 
    $AppInsights = Get-AzResource -Name 'Fabrikam-dev' -ResourceType 'microsoft.insights/components' -ResourceGroupName 'Fabrikam-dev'
    $AppInsights.Properties
    ```
    
    将返回属性列表作为结果。 其中一个属性显示为 `DisableIpMasking: true`。 如果在通过 Azure 资源管理器部署新属性之前运行 PowerShell，则该属性不存在。

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

如果需要比更灵活的替代方法 `DisableIpMasking` ，可以使用 [遥测初始值设定项](./api-filtering-sampling.md#addmodify-properties-itelemetryinitializer) 将所有或部分 IP 地址复制到自定义字段。 

# <a name="net"></a>[.NET](#tab/net)

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
# <a name="nodejs"></a>[Node.js](#tab/nodejs)

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
# <a name="client-side-javascript"></a>[客户端 JavaScript](#tab/javascript)

### <a name="client-side-javascript"></a>客户端 JavaScript

与服务器端 Sdk 不同，客户端 JavaScript SDK 不会计算 IP 地址。 默认情况下，客户端遥测的 IP 地址计算发生在 Azure 中的引入终结点上。 

如果要直接在客户端上计算 IP 地址，则需要添加自己的自定义逻辑，并使用结果来设置 `ai.location.ip` 标记。 如果 `ai.location.ip` 设置了，则不会通过摄取终结点执行 IP 地址计算，提供的 ip 地址将用于地理位置查找。 在这种情况下，IP 地址默认仍会归零。 

若要保留通过自定义逻辑计算的整个 IP 地址，可以使用遥测初始值设定项，将提供的 IP 地址数据复制 `ai.location.ip` 到单独的自定义字段。 但与服务器端 Sdk 不同，不依赖于第三方库或你自己的自定义集合逻辑，客户端 SDK 不会为你计算地址。    


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

如果客户端数据在转发到引入终结点之前遍历代理，则 IP 地址计算可能会显示代理的 IP 地址，而不是客户端的 IP 地址。 

---

### <a name="view-the-results-of-your-telemetry-initializer"></a>查看遥测初始化表达式的结果

如果将新流量发送到站点，请等待几分钟。 然后，你可以运行查询以确认收集工作：

```kusto
requests
| where timestamp > ago(1h) 
| project appName, operation_Name, url, resultCode, client_IP, customDimensions.["client-ip"]
```

新收集的 IP 地址将显示在 `customDimensions_client-ip` 列中。 默认 `client-ip` 列仍将所有四个八进制数归零。 

如果是从 localhost 进行测试，并且的值为 `customDimensions_client-ip` `::1` ，则此值是预期的行为。 `::1` 表示 IPv6 中的环回地址。 它等效于 `127.0.01` IPv4 中的。

## <a name="next-steps"></a>后续步骤

* 详细了解 Application Insights 中的[个人数据收集](../platform/personal-data-mgmt.md)。

* 详细了解 Application Insights 中 [IP 地址收集](https://apmtips.com/posts/2016-07-05-client-ip-address/)的工作原理。  (本文介绍了我们的一位工程师编写的更早的外部博客文章。 其中所述的机制不同当前的默认行为，现在，IP 地址将记录为 `0.0.0.0`，不过此文更深入地描述了内置 `ClientIpHeaderTelemetryInitializer` 的机制。）
