---
title: 服务开发人员指南-IoT 即插即用 |Microsoft Docs
description: 面向服务开发人员的 IoT 即插即用说明
author: dominicbetts
ms.author: dobett
ms.date: 10/01/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 431ea692ac734a875e27cb8ed4b015155e0e9b91
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92042416"
---
# <a name="iot-plug-and-play-service-developer-guide"></a>IoT 即插即用服务开发人员指南

IoT 即插即用允许构建智能设备，将其功能公布到 Azure IoT 应用程序。 当客户将其连接到 IoT 即插即用启用的应用程序时，IoT 即插即用设备不需要手动配置。

IoT 即插即用允许你使用已通过 IoT 中心宣布其模型 ID 的设备。 例如，你可以直接访问设备的属性和命令。

若要使用已连接到 IoT 中心的 IoT 即插即用设备，请使用其中一个 IoT 服务 Sdk 或 IoT 中心 REST API：

## <a name="service-sdks"></a>服务 SDK

使用解决方案中的 Azure IoT 服务 Sdk 与设备和模块交互。 例如，你可以使用服务 Sdk 来读取和更新克隆属性和调用命令。 支持的语言包括 c #、Java、Node.js 和 Python。

服务 Sdk 允许您从解决方案（如桌面或 web 应用程序）访问设备信息。 服务 Sdk 包括两个可用于检索模型 ID 的命名空间和对象模型：

- Iot 中心服务客户端。 此服务将模型 ID 公开为设备克隆的属性。

- 数字孪生服务客户端。 新的数字孪生 API 操作 [数字孪生定义语言 (DTDL) ](concepts-digital-twin.md) 模型构造，如组件、属性和命令。 数字克隆 Api 使解决方案构建者可以更轻松地创建 IoT 即插即用解决方案。

| 平台 | IoT 中心服务客户端 | 数字孪生服务客户端 |
| -------- | ---------------------- | ---------------------------- |
| .NET     | [文档](/dotnet/api/microsoft.azure.devices) <br/> [示例](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Samples/service/PnpServiceSamples)| [示例](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Samples/service/DigitalTwinClientSamples) |
| Java     | [文档](/java/api/com.microsoft.azure.sdk.iot.service.devicetwin.devicetwindevice?preserve-view=true&view=azure-java-stable) <br/> [示例](https://github.com/Azure/azure-iot-sdk-java/blob/master/service/iot-service-samples/pnp-service-sample)| [示例](https://github.com/Azure/azure-iot-sdk-java/tree/master/service/iot-service-samples/digitaltwin-service-samples) |
| Node.js  | [文档](/javascript/api/azure-iothub/twin?preserve-view=true&view=azure-node-latest) <br/> [示例](https://github.com/Azure/azure-iot-sdk-node/blob/master/service/samples/javascript/twin.js)| [文档](/javascript/api/azure-iot-digitaltwins-service/?preserve-view=true&view=azure-node-latest) <br/> [示例](https://github.com/Azure/azure-iot-sdk-node/blob/master/service/samples/javascript/get_digital_twin.js) |
| Python   | [文档](/python/api/azure-iot-hub/azure.iot.hub.iothubregistrymanager?preserve-view=true&view=azure-python) <br/> [示例](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-hub/samples/iothub_registry_manager_method_sample.py)| [文档](/python/api/azure-iot-hub/azure.iot.hub.iothubdigitaltwinmanager?preserve-view=true&view=azure-python) <br/> [示例](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-hub/samples/get_digital_twin_sample.py) |

## <a name="iot-hub-service-client-examples"></a>IoT 中心服务客户端示例

本部分介绍使用 IoT 中心服务客户端和 **RegistryManager** 和 **ServiceClient** 类的 c # 示例。 使用 **RegistryManager** 类通过设备孪生与设备状态交互。 还可以使用 **RegistryManager** 类在 IoT 中心 [查询设备注册](..\iot-hub\iot-hub-devguide-query-language.md) 。 使用 **ServiceClient** 类调用设备上的命令。 设备的 [DTDL](concepts-digital-twin.md) 模型定义设备实现的属性和命令。 在代码片段中， `deviceTwinId` 变量保存注册到 iot 中心的 iot 即插即用设备的设备 ID。

### <a name="get-the-device-twin-and-model-id"></a>获取设备克隆和模型 ID

若要获取连接到 IoT 中心的 IoT 即插即用设备的设备克隆和模型 ID，请执行以下操作：

```csharp
RegistryManager registryManager = RegistryManager.CreateFromConnectionString(parameters.HubConnectionString);

Twin twin = await registryManager.GetTwinAsync(deviceTwinId);
Console.WriteLine($"Device twin: \n{JsonConvert.SerializeObject(twin, Formatting.Indented)}");
Console.WriteLine($"Model ID: {twin.ModelId}.");
```

### <a name="update-device-twin"></a>更新设备孪生

下面的代码段演示如何更新 `targetTemperature` 设备上的属性。 此示例演示如何在更新之前获取克隆的 `ETag` 。 在设备的默认组件中定义属性：

```csharp
Twin twin = await registryManager.GetTwinAsync(deviceTwinId);

int desiredTargetTemperature = 60;

// Update the twin
var twinPatch = new Twin();
twinPatch.Properties.Desired["targetTemperature"] = desiredTargetTemperature;

Console.WriteLine($"Update the targetTemperature property to {desiredTargetTemperature}.");

await registryManager.UpdateTwinAsync(deviceTwinId, twinPatch, twin.ETag);
```

以下代码片段演示如何更新组件的 `targetTemperature` 属性。 此示例演示如何在更新之前获取克隆的 `ETag` 。 在 **Thermostat1** 接口中定义属性：

```csharp
Twin twin = await registryManager.GetTwinAsync(deviceTwinId);

int desiredTargetTemperature = 60;

var twinPatch = CreatePropertyPatch("targetTemperature", desiredTargetTemperature, "thermostat1");

await registryManager.UpdateTwinAsync(deviceTwinId, twinPatch, twin.ETag);

// ...

private static Twin CreatePropertyPatch(string propertyName, object propertyValue, string componentName)
{
    var twinPatch = new Twin();
    twinPatch.Properties.Desired[componentName] = new
    {
        __t = "c"
    };
    twinPatch.Properties.Desired[componentName][propertyName] = JsonConvert.SerializeObject(propertyValue);
    return twinPatch;
}
```

对于组件中的属性，属性修补程序类似于以下示例：

```json
{
"sampleComponentName":
  {
    "__t": "c",
    "samplePropertyName": 20
  }
}
```

### <a name="call-command"></a>Call 命令

以下代码片段演示如何调用 `getMaxMinReport` 默认组件中定义的命令：

```csharp
ServiceClient serviceClient = ServiceClient.CreateFromConnectionString(parameters.HubConnectionString);

var commandInvocation = new CloudToDeviceMethod("getMaxMinReport") { ResponseTimeout = TimeSpan.FromSeconds(30) };

// Set command payload
DateTimeOffset since = DateTimeOffset.Now.Subtract(TimeSpan.FromMinutes(2));
string componentCommandPayload = JsonConvert.SerializeObject(since);
commandInvocation.SetPayloadJson(componentCommandPayload);

try
{
  CloudToDeviceMethodResult result = await serviceClient.InvokeDeviceMethodAsync(deviceTwinId, commandInvocation);

  Console.WriteLine($"Command getMaxMinReport was invoked." +
      $"\nDevice returned status: {result.Status}. \nReport: {result.GetPayloadAsJson()}");
}
catch (DeviceNotFoundException)
{
    Console.WriteLine($"Unable to execute command getMaxMinReport on {deviceTwinId}.";
}
```

以下代码片段演示如何对组件调用 `getMaxMinReport` 命令。 命令在 **Thermostat1** 接口中定义：

```csharp
// Create command name to invoke for component. The command is formatted as <component name>*<command name>
string commandToInvoke = "thermostat1*getMaxMinReport";
var commandInvocation = new CloudToDeviceMethod(commandToInvoke) { ResponseTimeout = TimeSpan.FromSeconds(30) };

// Set command payload
DateTimeOffset since = DateTimeOffset.Now.Subtract(TimeSpan.FromMinutes(2));
string componentCommandPayload = JsonConvert.SerializeObject(since);
commandInvocation.SetPayloadJson(componentCommandPayload);

try
{
    CloudToDeviceMethodResult result = await serviceClient.InvokeDeviceMethodAsync(deviceTwinId, commandInvocation);
    Console.WriteLine($"Command getMaxMinReport was invoked on component thermostat1." +
        $"\nDevice returned status: {result.Status}. \nReport: {result.GetPayloadAsJson()}");
}
catch (DeviceNotFoundException)
{
    Console.WriteLine("Unable to execute command getMaxMinReport on component thermostat1.");
}
```

## <a name="digital-twin-examples"></a>数字输出示例

使用 **DigitalTwinClient** 类通过数字孪生与设备状态交互。 设备的 [DTDL](concepts-digital-twin.md) 模型定义设备实现的属性和命令。

本部分介绍使用数字孪生 API 的 c # 示例。 以下代码片段使用以下类来表示恒温器和温度控制器设备的数字输出：

```csharp
using Microsoft.Azure.Devices.Serialization;
using Newtonsoft.Json;
using System;

namespace Microsoft.Azure.Devices.Samples
{
  internal class ThermostatTwin : BasicDigitalTwin
  {
    [JsonProperty("$metadata")]
    public new ThermostatMetadata Metadata { get; set; }

    [JsonProperty("maxTempSinceLastReboot")]
    public double? MaxTempSinceLastReboot { get; set; }

    [JsonProperty("targetTemperature")]
    public double? TargetTemperature { get; set; }
  }

  internal class ThermostatMetadata : DigitalTwinMetadata
  {
    [JsonProperty("maxTempSinceLastReboot")]
    public ReportedPropertyMetadata MaxTempSinceLastReboot { get; set; }

    [JsonProperty("targetTemperature")]
    public WritableProperty TargetTemperature { get; set; }
  }

  internal class ReportedPropertyMetadata
  {
    [JsonProperty("lastUpdateTime")]
    public DateTimeOffset LastUpdateTime { get; set; }
  }

  internal class TemperatureControllerTwin : BasicDigitalTwin
  {
    [JsonProperty("$metadata")]
    public new TemperatureControllerMetadata Metadata { get; set; }

    [JsonProperty("serialNumber")]
    public string SerialNumber { get; set; }

    [JsonProperty("thermostat1")]
    public ThermostatTwin Thermostat1 { get; set; }

    [JsonProperty("thermostat2")]
    public ThermostatTwin Thermostat2 { get; set; }
  }

  internal class TemperatureControllerMetadata : DigitalTwinMetadata
  {
    [JsonProperty("serialNumber")]
    public ReportedPropertyMetadata SerialNumber { get; set; }

    [JsonProperty("thermostat1")]
    public WritableProperty Thermostat1 { get; set; }

    [JsonProperty("thermostat2")]
    public WritableProperty Thermostat2 { get; set; }
  }
}
```

`digitalTwinId`变量包含注册到 iot 中心的 iot 即插即用设备的设备 ID。

### <a name="get-the-digital-twin-and-model-id"></a>获取数字克隆和型号 ID

若要获取连接到 IoT 中心的 IoT 即插即用设备的数字输出和型号 ID，请执行以下操作：

```csharp
DigitalTwinClient digitalTwinClient = DigitalTwinClient.CreateFromConnectionString(parameters.HubConnectionString);
HttpOperationResponse<ThermostatTwin, DigitalTwinGetHeaders> getDigitalTwinResponse = await digitalTwinClient
    .GetDigitalTwinAsync<ThermostatTwin>(digitalTwinId);
ThermostatTwin thermostatTwin = getDigitalTwinResponse.Body;
Console.WriteLine($"Model ID: {thermostatTwin.Metadata.ModelId}.");
Console.WriteLine($"Digital Twin: \n{JsonConvert.SerializeObject(thermostatTwin, Formatting.Indented)}");
```

### <a name="update-digital-twin"></a>更新数字克隆

下面的代码段演示如何更新 `targetTemperature` 设备上的属性。 在设备的默认组件中定义属性：

```csharp
var updateOperation = new UpdateOperationsUtility();

int desiredTargetTemperature = 60;

// Get the current value of the targetTemperature property
HttpOperationResponse<ThermostatTwin, DigitalTwinGetHeaders> getDigitalTwinResponse = await digitalTwinClient
    .GetDigitalTwinAsync<ThermostatTwin>(digitalTwinId);
double? currentTargetTemperature = getDigitalTwinResponse.Body.TargetTemperature;

// Has the targetTemperature property previously been set?
if (currentTargetTemperature != null)
{
  // Update the existing property
  // Prepend the property path with a '/'
  updateOperation.AppendReplacePropertyOp($"/targetTemperature", desiredTargetTemperature);
}
else
{
  // Add a new property
  // Prepend the property path with a '/'
  updateOperation.AppendAddPropertyOp($"/targetTemperature", desiredTargetTemperature);
}

// Update the targetTemperature property on the digital twin
HttpOperationHeaderResponse<DigitalTwinUpdateHeaders> updateDigitalTwinResponse = await digitalTwinClient
    .UpdateDigitalTwinAsync(digitalTwinId, updateOperation.Serialize());

Console.WriteLine($"Update {digitalTwinId} digital twin response: {updateDigitalTwinResponse.Response.StatusCode}.");
```

以下代码片段演示如何更新组件的 `targetTemperature` 属性。 在 **Thermostat1** 组件中定义属性：

```csharp
int desiredTargetTemperature = 60;

var updateOperation = new UpdateOperationsUtility();

// Look at when the property was updated and what was it set to.
HttpOperationResponse<TemperatureControllerTwin, DigitalTwinGetHeaders> getDigitalTwinResponse = await digitalTwinClient
  .GetDigitalTwinAsync<TemperatureControllerTwin>(digitalTwinId);

ThermostatTwin thermostat1 = getDigitalTwinResponse.Body.Thermostat1;

if (thermostat1 != null)
{
  // Thermostat1 is present in the TemperatureController twin. You can add/replace the component-level property "targetTemperature"
  double? currentComponentTargetTemperature = getDigitalTwinResponse.Body.Thermostat1.TargetTemperature;
  if (currentComponentTargetTemperature != null)
  {
      DateTimeOffset targetTemperatureDesiredLastUpdateTime = getDigitalTwinResponse.Body.Thermostat1.Metadata.TargetTemperature.LastUpdateTime;

      // The property path to be replaced should be prepended with a '/'
      updateOperation.AppendReplacePropertyOp("/thermostat1/targetTemperature", desiredTargetTemperature);
  }
  else
  {
      // The property path to be added should be prepended with a '/'
      updateOperation.AppendAddPropertyOp("/thermostat1/targetTemperature", desiredTargetTemperature);
  }
}
else
{
    // Thermostat1 is not present in the TemperatureController twin. Add the component.
    var componentProperty = new Dictionary<string, object> { { "targetTemperature", desiredTargetTemperature }, { "$metadata", new object() } };

    // The property path to be replaced should be prepended with a '/'
    updateOperation.AppendAddComponentOp("/thermostat1", componentProperty);
}

HttpOperationHeaderResponse<DigitalTwinUpdateHeaders> updateDigitalTwinResponse = await digitalTwinClient
    .UpdateDigitalTwinAsync(digitalTwinId, updateOperation.Serialize());

Console.WriteLine($"Update {digitalTwinId} digital twin response: {updateDigitalTwinResponse.Response.StatusCode}.");
```

### <a name="call-command"></a>Call 命令

以下代码片段演示如何调用 `getMaxMinReport` 默认组件中定义的命令：

```csharp
DateTimeOffset since = DateTimeOffset.Now.Subtract(TimeSpan.FromMinutes(2));

try
{
  HttpOperationResponse<DigitalTwinCommandResponse, DigitalTwinInvokeCommandHeaders> invokeCommandResponse = await digitalTwinClient
    .InvokeCommandAsync(digitalTwinId, "getMaxMinReport", JsonConvert.SerializeObject(since));

  Console.WriteLine($"Command getMaxMinReport was invoked. \nDevice returned status: {invokeCommandResponse.Body.Status}." +
    $"\nReport: {invokeCommandResponse.Body.Payload}");
}
catch (HttpOperationException e)
{
  if (e.Response.StatusCode == HttpStatusCode.NotFound)
  {
    Console.WriteLine($"Unable to execute command getMaxMinReport on {digitalTwinId}.");
  }
}
```

以下代码片段演示如何对组件调用 `getMaxMinReport` 命令。 命令在 **Thermostat1** 接口中定义：

```csharp
DateTimeOffset since = DateTimeOffset.Now.Subtract(TimeSpan.FromMinutes(2));

try
{
    HttpOperationResponse<DigitalTwinCommandResponse, DigitalTwinInvokeCommandHeaders> invokeCommandResponse = await digitalTwinClient
        .InvokeComponentCommandAsync(digitalTwinId, "thermostat1", "getMaxMinReport", JsonConvert.SerializeObject(since));

    Console.WriteLine("Command getMaxMinReport was invoked on component thermostat1." +
        $"\nDevice returned status: {invokeCommandResponse.Body.Status}. \nReport: {invokeCommandResponse.Body.Payload}");
}
catch (HttpOperationException e)
{
    if (e.Response.StatusCode == HttpStatusCode.NotFound)
    {
        Console.WriteLine("Unable to execute command getMaxMinReport on component thermostat1.");
    }
}
```

## <a name="rest-api"></a>REST API

以下示例使用 IoT 中心 REST API 与连接 IoT 即插即用设备交互。 该 API 的当前版本为 `2020-09-30` 。 追加 `?api-version=2020-09-30` 到 REST PI 调用。

> [!NOTE]
> API 目前不支持 Module 孪生 `digitalTwins` 。

如果调用恒温器设备 `t-123` ，你将获得设备使用 REST API get 调用实现的所有接口的所有属性：

```REST
GET /digitalTwins/t-123
```

此调用将包含 `$metadata.$model` 包含设备所公布的模型 ID 的 Json 属性。

所有接口上的所有属性都是通过 `GET /DigitalTwin/{device-id}` REST API 模板访问的，其中 `{device-id}` ，是设备的标识符：

```REST
GET /digitalTwins/{device-id}
```

可以直接调用 IoT 即插即用设备命令。 如果 `Thermostat` 设备中的组件 `t-123` 有 `restart` 命令，则可以使用 REST API POST 调用来调用它：

```REST
POST /digitalTwins/t-123/components/Thermostat/commands/restart
```

通常，可以通过此 REST API 模板调用命令：

- `device-id`：设备的标识符。
- `component-name`：设备功能模型的实现部分中的接口的名称。
- `command-name`：命令的名称。

```REST
/digitalTwins/{device-id}/components/{component-name}/commands/{command-name}
```

## <a name="read-device-telemetry"></a>读取设备遥测

IoT 即插即用设备将在 DTDL 模型中定义的遥测发送到 IoT 中心。 默认情况下，IoT 中心会将遥测路由到事件中心终结点，你可以在其中使用该终结点。 若要了解详细信息，请参阅 [使用 IoT 中心消息路由将设备到云的消息发送到不同的终结点](../iot-hub/iot-hub-devguide-messages-d2c.md)。

下面的代码段演示如何从默认事件中心终结点读取遥测数据。 此代码段中的代码来自 IoT 中心快速入门，将 [遥测数据从设备发送到 IoT 中心，并使用后端应用程序进行读取](../iot-hub/quickstart-send-telemetry-dotnet.md)：

```csharp
await using EventHubConsumerClient consumer = new EventHubConsumerClient(EventHubConsumerClient.DefaultConsumerGroupName, connectionString, EventHubName);

Console.WriteLine("Listening for messages on all partitions");

try
{
    await foreach (PartitionEvent partitionEvent in consumer.ReadEventsAsync(cancellationToken))
    {
        Console.WriteLine("Message received on partition {0}:", partitionEvent.Partition.PartitionId);

        string data = Encoding.UTF8.GetString(partitionEvent.Data.Body.ToArray());
        Console.WriteLine("\t{0}:", data);

        Console.WriteLine("Application properties (set by device):");
        foreach (var prop in partitionEvent.Data.Properties)
        {
            Console.WriteLine("\t{0}: {1}", prop.Key, prop.Value);
        }

        Console.WriteLine("System properties (set by IoT Hub):");
        foreach (var prop in partitionEvent.Data.SystemProperties)
        {
            Console.WriteLine("\t{0}: {1}", prop.Key, prop.Value);
        }
    }
}
catch (TaskCanceledException)
{
    // This is expected when the token is signaled; it should not be considered an
    // error in this scenario.
}
```

以下来自前面代码的输出显示了 **恒温器** IoT 即插即用设备上只有默认组件发送的温度遥测。 " `dt-dataschema` 系统" 属性显示模型 ID：

```cmd/sh
Message received on partition 1:
        { "temperature": 25.5 }:
Application properties (set by device):
System properties (set by IoT Hub):
        iothub-connection-device-id: my-pnp-device
        iothub-connection-auth-method: {"scope":"device","type":"sas","issuer":"iothub","acceptingIpFilterRule":null}
        iothub-connection-auth-generation-id: 637375045610235418
        iothub-enqueuedtime: 05/10/2020 14:30:58
        iothub-message-source: Telemetry
        dt-dataschema: dtmi:com:example:Thermostat;1
        content-type: application/json
        content-encoding: utf-8
```

前面代码的以下输出显示了由多组件 **TemperatureController** IoT 即插即用设备发送的温度遥测。 " `dt-subject` 系统" 属性显示发送了遥测数据的组件的名称。 在此示例中，两个组件都是 `thermostat1` 和 `thermostat2` 在 DTDL 模型中定义的。 " `dt-dataschema` 系统" 属性显示模型 ID：

```cmd/sh
Message received on partition 1:
        {"temperature":11.1}:
Application properties (set by device):
System properties (set by IoT Hub):
        dt-subject: thermostat1
        iothub-connection-device-id: my-pnp-device
        iothub-connection-auth-method: {"scope":"device","type":"sas","issuer":"iothub","acceptingIpFilterRule":null}
        iothub-connection-auth-generation-id: 637375045610235418
        iothub-enqueuedtime: 05/10/2020 14:23:36
        iothub-message-source: Telemetry
        dt-dataschema: dtmi:com:example:TemperatureController;1
        content-type: application/json
        content-encoding: utf-8
Message received on partition 1:
        {"temperature":41.2}:
Application properties (set by device):
System properties (set by IoT Hub):
        dt-subject: thermostat2
        iothub-connection-device-id: my-pnp-device
        iothub-connection-auth-method: {"scope":"device","type":"sas","issuer":"iothub","acceptingIpFilterRule":null}
        iothub-connection-auth-generation-id: 637375045610235418
        iothub-enqueuedtime: 05/10/2020 14:23:36
        iothub-message-source: Telemetry
        dt-dataschema: dtmi:com:example:TemperatureController;1
        content-type: application/json
        content-encoding: utf-8
```

## <a name="read-device-twin-change-notifications"></a>读取设备克隆更改通知

可以将 IoT 中心配置为生成设备克隆更改通知，以路由到受支持的终结点。 若要了解详细信息，请参阅 [使用 IoT 中心消息路由将设备到云的消息发送到不同的终结点，> 非遥测事件](../iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events)。

当 IoT 中心为非组件恒温器设备生成设备克隆更改通知时，前面的 c # 代码段中显示的代码将生成以下输出。 应用程序属性 `iothub-message-schema` ，并 `opType` 向你显示有关更改通知类型的信息：

```cmd/sh
Message received on partition 1:
        {"version":3,"properties":{"reported":{"maxTempSinceLastReboot":9.6,"$metadata":{"$lastUpdated":"2020-10-06T10:17:41.7408552Z","maxTempSinceLastReboot":{"$lastUpdated":"2020-10-06T10:17:41.7408552Z"}},"$version":2}}}:
Application properties (set by device):
        hubName: my-pnp-hub
        deviceId: my-pnp-device
        operationTimestamp: 2020-10-06T10:17:41.7408552+00:00
        iothub-message-schema: twinChangeNotification
        opType: updateTwin
System properties (set by IoT Hub):
        iothub-connection-device-id: my-pnp-device
        iothub-enqueuedtime: 06/10/2020 10:17:41
        iothub-message-source: twinChangeEvents
        user-id: System.ArraySegment`1[System.Byte]
        correlation-id: 61394e8ba7d
        content-type: application/json
        content-encoding: utf-8
```

当 IoT 中心为包含组件的设备生成设备克隆更改通知时，前面的 c # 代码段中显示的代码将生成以下输出。 此示例显示了具有恒温器组件的温度传感器设备生成通知时的输出。 应用程序属性 `iothub-message-schema` ，并 `opType` 向你显示有关更改通知类型的信息：

```cmd/sh
Message received on partition 1:
        {"version":5,"properties":{"reported":{"thermostat1":{"__t":"c","maxTempSinceLastReboot":9.6},"$metadata":{"$lastUpdated":"2020-10-06T10:27:59.515972Z","thermostat1":{"$lastUpdated":"2020-10-06T10:27:59.515972Z","__t":{"$lastUpdated":"2020-10-06T10:27:59.515972Z"},"maxTempSinceLastReboot":{"$lastUpdated":"2020-10-06T10:27:59.515972Z"}}},"$version":4}}}:
Application properties (set by device):
        hubName: my-pnp-hub
        deviceId: my-pnp-device
        operationTimestamp: 2020-10-06T10:27:59.5159720+00:00
        iothub-message-schema: twinChangeNotification
        opType: updateTwin
System properties (set by IoT Hub):
        iothub-connection-device-id: my-pnp-device
        iothub-enqueuedtime: 06/10/2020 10:27:59
        iothub-message-source: twinChangeEvents
        user-id: System.ArraySegment`1[System.Byte]
        correlation-id: 615051f364e
        content-type: application/json
        content-encoding: utf-8
```

## <a name="read-digital-twin-change-notifications"></a>读取数字克隆更改通知

可以配置 IoT 中心来生成数字克隆更改通知，以路由到受支持的终结点。 若要了解详细信息，请参阅 [使用 IoT 中心消息路由将设备到云的消息发送到不同的终结点，> 非遥测事件](../iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events)。

当 IoT 中心生成非组件恒温器设备的数字克隆更改通知时，前面的 c # 代码段中显示的代码将生成以下输出。 应用程序属性 `iothub-message-schema` ，并 `opType` 向你显示有关更改通知类型的信息：

```cmd/sh
Message received on partition 1:
        [{"op":"add","path":"/$metadata/maxTempSinceLastReboot","value":{"lastUpdateTime":"2020-10-06T10:39:16.0209836Z"}},{"op":"add","path":"/maxTempSinceLastReboot","value":34.9}]:
Application properties (set by device):
        hubName: my-pnp-hub
        deviceId: my-pnp-device
        operationTimestamp: 2020-10-06T10:39:16.0209836+00:00
        iothub-message-schema: digitalTwinChangeNotification
        opType: updateTwin
System properties (set by IoT Hub):
        iothub-connection-device-id: my-pnp-device
        iothub-enqueuedtime: 06/10/2020 10:39:16
        iothub-message-source: digitalTwinChangeEvents
        user-id: System.ArraySegment`1[System.Byte]
        correlation-id: 6169857bf8c
        content-type: application/json-patch+json
        content-encoding: utf-8
```

当 IoT 中心为包含组件的设备生成数字克隆更改通知时，前面的 c # 代码段中显示的代码将生成以下输出。 此示例显示了具有恒温器组件的温度传感器设备生成通知时的输出。 应用程序属性 `iothub-message-schema` ，并 `opType` 向你显示有关更改通知类型的信息：

```cmd/sh
Message received on partition 1:
        [{"op":"add","path":"/thermostat1","value":{"$metadata":{"maxTempSinceLastReboot":{"lastUpdateTime":"2020-10-06T10:41:44.8312666Z"}},"maxTempSinceLastReboot":29.1}}]:
Application properties (set by device):
        hubName: my-pnp-hub
        deviceId: my-pnp-device
        operationTimestamp: 2020-10-06T10:41:44.8312666+00:00
        iothub-message-schema: digitalTwinChangeNotification
        opType: updateTwin
System properties (set by IoT Hub):
        iothub-connection-device-id: my-pnp-device
        iothub-enqueuedtime: 06/10/2020 10:41:44
        iothub-message-source: digitalTwinChangeEvents
        user-id: System.ArraySegment`1[System.Byte]
        correlation-id: 616f108f0e3
        content-type: application/json-patch+json
        content-encoding: utf-8
```

## <a name="next-steps"></a>后续步骤

现在，你已了解设备建模，以下是一些其他资源：

- [数字孪生定义语言 (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl)
- [C 设备 SDK](/azure/iot-hub/iot-c-sdk-ref/)
- [IoT REST API](/rest/api/iothub/device)
- [模型组件](./concepts-components.md)
- [安装并使用 DTDL 创作工具](howto-use-dtdl-authoring-tools.md)