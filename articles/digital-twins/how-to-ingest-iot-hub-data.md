---
title: 从 IoT 中心引入遥测数据
titleSuffix: Azure Digital Twins
description: 请参阅如何从 IoT 中心摄取设备遥测消息。
author: cschormann
ms.author: cschorm
ms.date: 3/17/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 351f7ed131d545d2aa83df753cac3f26e76e4ccb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84725845"
---
# <a name="ingest-iot-hub-telemetry-into-azure-digital-twins"></a>将 IoT 中心遥测数据引入 Azure 数字孪生

Azure 数字孪生由 IoT 设备和其他来源的数据驱动。 要在 Azure 数字孪生中使用的设备数据的常见来源为[IoT 中心](../iot-hub/about-iot-hub.md)。

在预览期间，将数据引入 Azure 数字孪生的过程是设置外部计算资源（如[azure 函数](../azure-functions/functions-overview.md)），该资源接收数据并使用[DigitalTwins api](how-to-use-apis-sdks.md)相应设置属性或在[数字孪生](concepts-twins-graph.md)上触发遥测事件。 

本操作方法文档演练如何编写可从 IoT 中心引入遥测数据的 Azure 函数。

## <a name="example-telemetry-scenario"></a>遥测方案示例

本操作指南概述了如何使用 Azure 函数将消息从 IoT 中心发送到 Azure 数字孪生。 有许多可能的配置和匹配策略可用于此操作，但本文的示例包含以下部分：
* IoT 中心的温度计设备，具有已知的设备 ID。
* 用于表示设备的数字克隆，具有匹配 ID
* 表示房间的数字双子

> [!NOTE]
> 此示例在设备 ID 与相应的数字克隆的 ID 之间使用简单的 ID 匹配，但可以提供从设备到其克隆的更复杂的映射（如使用映射表）。

当温度计设备发送温度遥测事件时，*房间*克隆的*温度*属性应该会更新。 为实现此目的，您将从设备上的遥测事件映射到数字克隆上的属性 setter。 你将使用来自克隆[图形](concepts-twins-graph.md)的拓扑信息来查找*文件*组，然后可以设置克隆的属性。 在其他情况下，用户可能想要对匹配的个（在本示例中，ID 为*123*的双子）设置一个属性。 Azure 数字孪生为你提供了很大的灵活性，可决定如何将遥测数据映射到孪生。 

下图中概述了此方案：

:::image type="content" source="media/how-to-ingest-iot-hub-data/events.png" alt-text="IoT 中心设备通过 IoT 中心、事件网格或系统主题将温度遥测发送到 Azure 函数，该函数更新 Azure 数字孪生中孪生的温度属性。" border="false":::

## <a name="prerequisites"></a>先决条件

继续此示例之前，需要完成以下先决条件。
1. 创建 IoT 中心。 有关说明，请参阅[此 Iot 中心快速入门](../iot-hub/quickstart-send-telemetry-cli.md)的*创建 iot 中心*部分。
2. 创建至少一个 Azure 函数，用于处理 IoT 中心的事件。 请参阅[如何：设置用于处理数据的 azure 函数](how-to-create-azure-function.md)以构建可连接到 Azure 数字孪生并调用 Azure 数字孪生 API 函数的基本 azure 功能。 本操作方法的其余部分将基于此函数构建。
3. 设置中心数据的事件目标。 在[Azure 门户](https://portal.azure.com/)中，导航到 IoT 中心实例。 在 "*事件*" 下，为 Azure 函数创建订阅。 

    :::image type="content" source="media/how-to-ingest-iot-hub-data/add-event-subscription.png" alt-text="Azure 门户：添加事件订阅":::

4. 在 "*创建事件订阅*" 页中，按如下所示填写字段：
   * 在 "*事件订阅详细信息*" 下，将订阅命名为你想要的
   * 在 "*事件类型*" 下，选择 "*设备遥测*" 作为要筛选的事件类型
      - 如果需要，请将筛选器添加到其他事件类型。
   * 在 "*终结点详细信息*" 下，选择 Azure 函数作为终结点

## <a name="create-an-azure-function-in-visual-studio"></a>在 Visual Studio 中创建 Azure 函数

本部分使用相同的 Visual Studio 启动步骤和 Azure 函数主干中的[操作方法：设置用于处理数据的 azure 函数](how-to-create-azure-function.md)。 该主干处理身份验证并创建服务客户端，使你可以处理数据，并在响应中调用 Azure 数字孪生 Api。 

主干函数的核心是：

```csharp
namespace FunctionSample
{
    public static class FooFunction
    {
        const string adtAppId = "https://digitaltwins.azure.net";
        private static string adtInstanceUrl = Environment.GetEnvironmentVariable("ADT_SERVICE_URL");
        private static HttpClient httpClient = new HttpClient();

        [FunctionName("Foo")]
        public static async Task Run([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
        {
            DigitalTwinsClient client = null;
            try
            {
                ManagedIdentityCredential cred = new ManagedIdentityCredential(adtAppId);
                DigitalTwinsClientOptions opts = 
                    new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });
                client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, opts);
                                                
                log.LogInformation($"ADT service client connection created.");
            }
            catch (Exception e)
            {
                log.LogError($"ADT service client connection failed. " + e.ToString());
                return;
            }
            log.LogInformation(eventGridEvent.Data.ToString());
        }
    }
}
```

在接下来的步骤中，你将向其添加特定代码，用于处理 IoT 中心的 IoT 遥测事件。  

## <a name="add-telemetry-processing"></a>添加遥测处理

遥测事件以来自设备的消息形式提供。 添加遥测处理代码的第一步是从事件网格事件中提取此设备消息的相关部分。 

不同的设备可能以不同的方式构造其消息，因此此步骤的代码取决于所连接的设备。 

下面的代码显示了将遥测作为 JSON 发送的简单设备的示例。 此示例将提取发送消息的设备的设备 ID 以及温度值。

```csharp
JObject job = eventGridEvent.Data as JObject;
string devid = (string)job["systemProperties"].ToObject<JObject>().Property("IoT-hub-connection-device-ID").Value;
double temp = (double)job["body"].ToObject<JObject>().Property("temperature").Value;
```

请记住，此练习的目的是更新 "克隆" 图中的*空间*温度。 这意味着，我们的消息目标不是与此设备相关联的数字克隆，而是其父级的*空间*克隆。 您可以使用上述代码从遥测消息中提取的设备 ID 值查找父对象。

为此，请使用 Azure 数字孪生 Api 来访问代表克隆的设备（在此示例中，其 ID 与设备相同）的传入关系。 从传入关系中，可以通过以下代码片段查找父对象的 ID。

下面的代码片段演示了如何检索克隆的传入关系：

```csharp
AsyncPageable<IncomingRelationship> res = client.GetIncomingRelationshipsAsync(twin_id);
await foreach (IncomingRelationship irel in res)
{
    Log.Ok($"Relationship: {irel.RelationshipName} from {irel.SourceId} | {irel.RelationshipId}");
}
```

克隆的父级位于关系的*SourceId*属性中。

对于表示设备的克隆的模型而言，只有单个传入关系非常常见。 在这种情况下，您可以选择返回的第一个（且仅限）关系。 如果您的模型允许多种类型的关系与此不成对的关系，则您可能需要进一步指定以从多个传入关系中进行选择。 实现此目的的常见方法是选择关系 `RelationshipName` 。 

如果有代表*房间*的父克隆的 ID，则可以 "修补" （对其进行更新）。 为此，请使用以下代码：

```csharp
UpdateOperationsUtility uou = new UpdateOperationsUtility();
uou.AppendAddOp("/Temperature", temp);
try
{
    await client.UpdateDigitalTwinAsync(twin_id, uou.Serialize());
    Log.Ok($"Twin '{twin_id}' updated successfully!");
}
...
```

### <a name="full-azure-function-code"></a>完整的 Azure 函数代码

使用前面示例中的代码，以下是上下文中的整个 Azure 函数：

```csharp
[FunctionName("ProcessHubToDTEvents")]
public async void Run([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
{
    // After this is deployed, in order for this function to be authorized on Azure Digital Twins APIs,
    // you'll need to turn the Managed Identity Status to "On", 
    // grab the Object ID of the function, and assign the "Azure Digital Twins Owner (Preview)" role to this function identity.

    DigitalTwinsClient client = null;
    //log.LogInformation(eventGridEvent.Data.ToString());
    // Authenticate on Azure Digital Twins APIs
    try
    {
        
        ManagedIdentityCredential cred = new ManagedIdentityCredential(adtAppId);
        client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });
        log.LogInformation($"ADT service client connection created.");
    }
    catch (Exception e)
    {
        log.LogError($"ADT service client connection failed. " + e.ToString());
        return;
    }

    if (client != null)
    {
        try
        {
            if (eventGridEvent != null && eventGridEvent.Data != null)
            {
                #region Open this region for message format information
                // Telemetry message format
                //{
                //  "properties": { },
                //  "systemProperties": 
                // {
                //    "iothub-connection-device-id": "thermostat1",
                //    "iothub-connection-auth-method": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
                //    "iothub-connection-auth-generation-id": "637199981642612179",
                //    "iothub-enqueuedtime": "2020-03-18T18:35:08.269Z",
                //    "iothub-message-source": "Telemetry"
                //  },
                //  "body": "eyJUZW1wZXJhdHVyZSI6NzAuOTI3MjM0MDg3MTA1NDg5fQ=="
                //}
                #endregion

                // Reading deviceId from message headers
                log.LogInformation(eventGridEvent.Data.ToString());
                JObject job = (JObject)JsonConvert.DeserializeObject(eventGridEvent.Data.ToString());
                string deviceId = (string)job["systemProperties"]["iothub-connection-device-id"];
                log.LogInformation($"Found device: {deviceId}");

                // Extracting temperature from device telemetry
                byte[] body = System.Convert.FromBase64String(job["body"].ToString());
                var value = System.Text.ASCIIEncoding.ASCII.GetString(body);
                var bodyProperty = (JObject)JsonConvert.DeserializeObject(value);
                var temperature = bodyProperty["Temperature"];
                log.LogInformation($"Device Temperature is:{temperature}");

                // Update device Temperature property
                await AdtUtilities.UpdateTwinProperty(client, deviceId, "/Temperature", temperature, log);

                // Find parent using incoming relationships
                string parentId = await AdtUtilities.FindParent(client, deviceId, "contains", log);
                if (parentId != null)
                {
                    await AdtUtilities.UpdateTwinProperty(client, parentId, "/Temperature", temperature, log);
                }

            }
        }
        catch (Exception e)
        {
            log.LogError($"Error in ingest function: {e.Message}");
        }
    }
}
```

用于查找传入关系的实用工具函数：
```csharp
public static async Task<string> FindParent(DigitalTwinsClient client, string child, string relname, ILogger log)
{
    // Find parent using incoming relationships
    try
    {
        AsyncPageable<IncomingRelationship> rels = client.GetIncomingRelationshipsAsync(child);

        await foreach (IncomingRelationship ie in rels)
        {
            if (ie.RelationshipName == relname)
                return (ie.SourceId);
        }
    }
    catch (RequestFailedException exc)
    {
        log.LogInformation($"*** Error in retrieving parent:{exc.Status}:{exc.Message}");
    }
    return null;
}
```

以及用于修补克隆的实用工具函数：
```csharp
public static async Task UpdateTwinProperty(DigitalTwinsClient client, string twinId, string propertyPath, object value, ILogger log)
{
    // If the twin does not exist, this will log an error
    try
    {
        // Update twin property
        UpdateOperationsUtility uou = new UpdateOperationsUtility();
        uou.AppendAddOp(propertyPath, value);
        await client.UpdateDigitalTwinAsync(twinId, uou.Serialize());
    }
    catch (RequestFailedException exc)
    {
        log.LogInformation($"*** Error:{exc.Status}/{exc.Message}");
    }
}
```

现在，你有了一个 Azure 函数，该函数可用于读取和解释来自 IoT 中心的方案数据。

## <a name="debug-azure-function-apps-locally"></a>在本地调试 Azure function apps

可以在本地调试包含事件网格触发器的 Azure 函数。 有关此内容的详细信息，请参阅在[本地调试事件网格触发器](../azure-functions/functions-debug-event-grid-trigger-local.md)。

## <a name="next-steps"></a>后续步骤

阅读有关 Azure 数字孪生的数据入口和出口：
* [概念：与其他服务集成](concepts-integration.md)
