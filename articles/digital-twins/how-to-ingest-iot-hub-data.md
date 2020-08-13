---
title: 从 IoT 中心引入遥测数据
titleSuffix: Azure Digital Twins
description: 请参阅如何从 IoT 中心摄取设备遥测消息。
author: alexkarcher-msft
ms.author: alkarche
ms.date: 8/11/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 5209ffb0328e90fb2ca9b91773cbf18dd4ed2916
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2020
ms.locfileid: "88163591"
---
# <a name="ingest-iot-hub-telemetry-into-azure-digital-twins"></a>将 IoT 中心遥测数据引入 Azure 数字孪生

Azure 数字孪生由 IoT 设备和其他来源的数据驱动。 要在 Azure 数字孪生中使用的设备数据的常见来源为[IoT 中心](../iot-hub/about-iot-hub.md)。

将数据引入 Azure 数字孪生的过程是设置外部计算资源（如[Azure 函数](../azure-functions/functions-overview.md)），该资源接收数据并使用[DigitalTwins api](how-to-use-apis-sdks.md)相应地设置属性或在[数字孪生](concepts-twins-graph.md)上触发遥测事件。 

本操作方法文档演练如何编写可从 IoT 中心引入遥测数据的 Azure 函数。

## <a name="prerequisites"></a>先决条件

继续此示例之前，需要完成以下先决条件。
* **IoT 中心**。 有关说明，请参阅[此 Iot 中心快速入门](../iot-hub/quickstart-send-telemetry-cli.md)的*创建 iot 中心*部分。
* 使用正确的权限调用数字克隆实例的**Azure 函数**。 有关说明，请参阅[*操作方法：设置用于处理数据的 Azure 函数*](how-to-create-azure-function.md)。 
* 将接收设备遥测数据的**数字孪生实例**。 请参阅[*如何：设置 Azure 数字孪生实例和身份验证*](./how-to-set-up-instance-portal.md) 

### <a name="example-telemetry-scenario"></a>遥测方案示例

本操作指南概述了如何使用 Azure 函数将消息从 IoT 中心发送到 Azure 数字孪生。 有许多可能的配置和匹配策略可用于此操作，但本文的示例包含以下部分：
* IoT 中心的温度计设备，具有已知的设备 ID。
* 用于表示设备的数字克隆，具有匹配 ID

> [!NOTE]
> 此示例在设备 ID 与相应的数字克隆的 ID 之间使用简单的 ID 匹配，但可以提供更复杂的映射，从设备到其克隆 (如) 的映射表。

当温度计设备发送温度遥测事件时，数字克隆的*温度*属性应该会更新。 下图中概述了此方案：

:::image type="content" source="media/how-to-ingest-iot-hub-data/events.png" alt-text="显示流程图的关系图。在此图表中，IoT 中心设备通过 IoT 中心将温度遥测发送到 Azure 功能，该功能可更新 Azure 数字孪生中的克隆温度属性。" border="false":::

## <a name="add-a-model-and-twin"></a>添加模型和克隆

需要使用一个克隆来更新 IoT 中心信息。

模型如下所示：
```JSON
{
  "@id": "dtmi:contosocom:DigitalTwins:Thermostat;1",
  "@type": "Interface",
  "@context": "dtmi:dtdl:context;2",
  "contents": [
    {
      "@type": "Property",
      "name": "Temperature",
      "schema": "double"
    }
  ]
}
```

若要将**此模型上传到孪生实例**，请打开 Azure CLI，并运行以下命令：
```azurecli-interactive
az dt model create --models '{  "@id": "dtmi:contosocom:DigitalTwins:Thermostat;1",  "@type": "Interface",  "@context": "dtmi:dtdl:context;2",  "contents": [    {      "@type": "Property",      "name": "Temperature",      "schema": "double"    }  ]}' -n {digital_twins_instance_name}
```

然后，需要**使用此模型创建一个**克隆。 使用以下命令创建一个克隆并将0.0 设置为初始温度值。
```azurecli-interactive
az dt twin create --dtmi "dtmi:contosocom:DigitalTwins:Thermostat;1" --twin-id thermostat67 --properties '{"Temperature": 0.0,}' --dt-name {digital_twins_instance_name}
```

成功的 "创建" 命令的输出应如下所示：
```json
{
  "$dtId": "thermostat67",
  "$etag": "W/\"0000000-9735-4f41-98d5-90d68e673e15\"",
  "$metadata": {
    "$model": "dtmi:contosocom:DigitalTwins:Thermostat;1",
    "Temperature": {
      "ackCode": 200,
      "ackDescription": "Auto-Sync",
      "ackVersion": 1,
      "desiredValue": 0.0,
      "desiredVersion": 1
    }
  },
  "Temperature": 0.0
}
```

## <a name="create-an-azure-function"></a>创建 Azure 函数

本部分使用相同的 Visual Studio 启动步骤和 Azure 函数主干中的[*操作方法：设置用于处理数据的 azure 函数*](how-to-create-azure-function.md)。 该主干处理身份验证并创建服务客户端，使你可以处理数据，并在响应中调用 Azure 数字孪生 Api。 

在接下来的步骤中，你将向其添加特定代码，用于处理 IoT 中心的 IoT 遥测事件。  

### <a name="add-telemetry-processing"></a>添加遥测处理
    
遥测事件以来自设备的消息形式提供。 添加遥测处理代码的第一步是从事件网格事件中提取此设备消息的相关部分。 

不同的设备可能以不同的方式构造其消息，因此**此步骤的代码取决于所连接的设备。** 

下面的代码显示了将遥测作为 JSON 发送的简单设备的示例。 本示例在[*教程：连接端到端解决方案*](./tutorial-end-to-end.md)中进行了全面的探讨。 以下代码将查找发送消息的设备的设备 ID 以及温度值。

```csharp
JObject deviceMessage = (JObject)JsonConvert.DeserializeObject(eventGridEvent.Data.ToString());
string deviceId = (string)deviceMessage["systemProperties"]["iothub-connection-device-id"];
var temperature = deviceMessage["body"]["Temperature"];
```

接下来的代码示例采用 ID 和温度值，并使用它们 "patch" (对克隆) 进行更新。

```csharp
//Update twin using device temperature
var uou = new UpdateOperationsUtility();
uou.AppendReplaceOp("/Temperature", temperature.Value<double>());
await client.UpdateDigitalTwinAsync(deviceId, uou.Serialize());
...
```

### <a name="update-your-azure-function-code"></a>更新 Azure function 代码

现在，你已了解之前示例中的代码，请打开 Visual Studio，并将 Azure 函数的代码替换为此示例代码。

```csharp
using System;
using System.Net.Http;
using Azure.Core.Pipeline;
using Azure.DigitalTwins.Core;
using Azure.DigitalTwins.Core.Serialization;
using Azure.Identity;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.EventGrid;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;

namespace IotHubtoTwins
{
    public class IoTHubtoTwins
    {
        private static readonly string adtInstanceUrl = Environment.GetEnvironmentVariable("ADT_SERVICE_URL");
        private static readonly HttpClient httpClient = new HttpClient();

        [FunctionName("IoTHubtoTwins")]
        public async void Run([EventGridTrigger] EventGridEvent eventGridEvent, ILogger log)
        {
            if (adtInstanceUrl == null) log.LogError("Application setting \"ADT_SERVICE_URL\" not set");

            try
            {
                //Authenticate with Digital Twins
                ManagedIdentityCredential cred = new ManagedIdentityCredential("https://digitaltwins.azure.net");
                DigitalTwinsClient client = new DigitalTwinsClient(
                    new Uri(adtInstanceUrl), cred, new DigitalTwinsClientOptions 
                    { Transport = new HttpClientTransport(httpClient) });
                log.LogInformation($"ADT service client connection created.");
            
                if (eventGridEvent != null && eventGridEvent.Data != null)
                {
                    log.LogInformation(eventGridEvent.Data.ToString());

                    // Reading deviceId and temperature for IoT Hub JSON
                    JObject deviceMessage = (JObject)JsonConvert.DeserializeObject(eventGridEvent.Data.ToString());
                    string deviceId = (string)deviceMessage["systemProperties"]["iothub-connection-device-id"];
                    var temperature = deviceMessage["body"]["Temperature"];
                    
                    log.LogInformation($"Device:{deviceId} Temperature is:{temperature}");

                    //Update twin using device temperature
                    var uou = new UpdateOperationsUtility();
                    uou.AppendReplaceOp("/Temperature", temperature.Value<double>());
                    await client.UpdateDigitalTwinAsync(deviceId, uou.Serialize());
                }
            }
            catch (Exception e)
            {
                log.LogError($"Error in ingest function: {e.Message}");
            }
        }
    }
}
```

## <a name="connect-your-function-to-iot-hub"></a>将函数连接到 IoT 中心

1. 设置中心数据的事件目标。 在[Azure 门户](https://portal.azure.com/)中，导航到 IoT 中心实例。 在 "**事件**" 下，为 Azure 函数创建订阅。 

    :::image type="content" source="media/how-to-ingest-iot-hub-data/add-event-subscription.png" alt-text="显示添加事件订阅的 Azure 门户屏幕截图。":::

2. 在 "**创建事件订阅**" 页中，按如下所示填写字段：
    1. 在 "**名称**" 下，将订阅命名为你所需的名称。
    2. 在 "**事件架构**" 下，选择 "**事件网格架构**"。
    3. 在 "**系统主题名称**" 下，选择唯一名称。
    4. 在 "**事件类型**" 下，选择 "**设备遥测**" 作为要筛选的事件类型。
    5. 在 "**终结点详细信息**" 下，选择 Azure 函数作为终结点。

    :::image type="content" source="media/how-to-ingest-iot-hub-data/event-subscription-2.png" alt-text="显示事件订阅详细信息 Azure 门户的屏幕截图":::

## <a name="send-simulated-iot-data"></a>发送模拟 IoT 数据

若要测试新的入口函数，请使用[*教程：连接端到端解决方案*](./tutorial-end-to-end.md)中的设备模拟器。 该教程由用 c # 编写的示例项目驱动。 示例代码位于此处： [Azure 数字孪生示例](https://docs.microsoft.com/samples/azure-samples/digital-twins-samples/digital-twins-samples)。 将使用该存储库中的**devicesimulator.exe**项目。

在端到端教程中，完成以下步骤：
1. [*在 IoT 中心注册模拟设备*](./tutorial-end-to-end.md#register-the-simulated-device-with-iot-hub)
2. [*配置和运行模拟*](./tutorial-end-to-end.md#configure-and-run-the-simulation)

## <a name="validate-your-results"></a>验证结果

在运行上面的设备模拟器时，将更改数字克隆的温度值。 在 Azure CLI 中运行以下命令以查看温度值。

```azurecli-interactive
az dt twin query -q "select * from digitaltwins" -n {digital_twins_instance_name}
```

输出应包含温度值，如下所示：

```json
{
  "result": [
    {
      "$dtId": "thermostat67",
      "$etag": "W/\"0000000-1e83-4f7f-b448-524371f64691\"",
      "$metadata": {
        "$model": "dtmi:contosocom:DigitalTwins:Thermostat;1",
        "Temperature": {
          "ackCode": 200,
          "ackDescription": "Auto-Sync",
          "ackVersion": 1,
          "desiredValue": 69.75806974934324,
          "desiredVersion": 1
        }
      },
      "Temperature": 69.75806974934324
    }
  ]
}
```

若要查看值更改，请重复运行上面的查询命令。

## <a name="next-steps"></a>后续步骤

阅读有关 Azure 数字孪生的数据入口和出口：
* [*概念：与其他服务集成*](concepts-integration.md)
