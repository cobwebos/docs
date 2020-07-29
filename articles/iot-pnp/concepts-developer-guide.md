---
title: 开发人员指南-IoT 即插即用预览版 |Microsoft Docs
description: 面向开发人员的 IoT 即插即用说明
author: rido-min
ms.author: rmpablos
ms.date: 07/16/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: ef221ea068f2786a4a84f20a29e80dd7176f06c6
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/28/2020
ms.locfileid: "87337409"
---
# <a name="iot-plug-and-play-preview-developer-guide"></a>IoT 即插即用预览版开发人员指南

IoT 即插即用预览版允许构建智能设备，将其功能公布到 Azure IoT 应用程序。 当客户将其连接到 IoT 即插即用启用的应用程序时，IoT 即插即用设备不需要手动配置。

本指南介绍了创建遵循[IoT 即插即用约定](concepts-convention.md)的设备所需的基本步骤，以及可用于与设备进行交互的可用 REST api。

若要构建 IoT 即插即用设备，请执行以下步骤：

1. 确保你的设备使用 MQTT 或 MQTT over Websocket 协议连接到 Azure IoT 中心。
1. 创建[数字孪生定义语言（DTDL）](https://github.com/Azure/opendigitaltwins-dtdl)模型来描述你的设备。 若要了解详细信息，请参阅[了解 IoT 即插即用模型中的组件](concepts-components.md)。
1. 更新设备以在 `model-id` 设备连接过程中公告。
1. 使用[IoT 即插即用约定](concepts-convention.md)实现遥测、属性和命令

设备实现准备就绪后，请使用[Azure iot 浏览器](howto-use-iot-explorer.md)验证设备是否遵循 IoT 即插即用约定。

> [!Tip]
> 本文中的所有代码片段都使用 c #，但这些概念适用于适用于 C、Python、Node 和 Java 的任何可用的 Sdk。

## <a name="model-id-announcement"></a>模型 ID 公告

若要公布模型 ID，设备必须将其包含在连接信息中：

```csharp
DeviceClient.CreateFromConnectionString(
  connectionString,
  TransportType.Mqtt,
  new ClientOptions() { ModelId = modelId })
```

新 `ClientOptions` 重载在 `DeviceClient` 用于初始化连接的所有方法中可用。

已将模型 ID 公告添加到 Sdk 的下一版本

|SDK 中 IsInRole 中的声明|版本|
|---|-------|
|C-SDK|1.3.9|
|.NET|1.27.0|
|Java|1.14.0|
|节点|1.17.0|
|Python|2.1.4|

## <a name="implement-telemetry-properties-and-commands"></a>实现遥测、属性和命令

如[了解 IoT 即插即用模型中的组件](concepts-components.md)中所述，设备构建者必须决定是否要使用组件来描述其设备。 使用组件时，设备必须遵循本部分中所述的规则。

### <a name="telemetry"></a>遥测

没有组件的模型不需要任何特殊属性。

使用组件时，设备必须使用组件名称设置消息属性：

```c#
public async Task SendComponentTelemetryValueAsync(string componentName, string serializedTelemetry)
{
  var message = new Message(Encoding.UTF8.GetBytes(serializedTelemetry));
  message.Properties.Add("$.sub", componentName);
  message.ContentType = "application/json";
  message.ContentEncoding = "utf-8";
  await deviceClient.SendEventAsync(message);
}
```

### <a name="read-only-properties"></a>只读属性

没有组件的模型不需要任何特殊构造：

```csharp
TwinCollection reportedProperties = new TwinCollection();
reportedProperties["maxTemperature"] = 38.7;
await client.UpdateReportedPropertiesAsync(reportedProperties);
```

用下一个报告的属性更新设备克隆：

```json
{
  "reported": {
      "maxTemperature" : 38.7
  }
}
```

使用组件时，必须在组件名称中创建属性：

```csharp
TwinCollection reportedProperties = new TwinCollection();
TwinCollection component = new TwinCollection();
component["maxTemperature"] = 38.7;
component["__t"] = "c"; // marker to identify a component
reportedProperties["thermostat1"] = component;
await client.UpdateReportedPropertiesAsync(reportedProperties);
```

用下一个报告的属性更新设备克隆：

```json
{
  "reported": {
    "thermostat1" : {  
      "__t" : "c",  
      "maxTemperature" : 38.7
     } 
  }
}
```

### <a name="writable-properties"></a>可写属性

这些属性可以由设备设置或通过解决方案更新。 如果解决方案更新属性，则客户端会在中收到作为回调的通知 `DeviceClient` 。 若要遵循 IoT 即插即用约定，设备必须通知服务属性已成功接收。

#### <a name="report-a-writable-property"></a>报告可写属性

当设备报告可写属性时，它必须包含 `ack` 约定中定义的值。

报告不含组件的可写属性：

```csharp
TwinCollection reportedProperties = new TwinCollection();
TwinCollection ackProps = new TwinCollection();
ackProps["value"] = 23.2;
ackProps["ac"] = 200; // using HTTP status codes
ackProps["av"] = 0; // not readed from a desired property
ackProps["ad"] = "reported default value";
reportedProperties["targetTemperature"] = ackProps;
await client.UpdateReportedPropertiesAsync(reportedProperties);
```

用下一个报告的属性更新设备克隆：

```json
{
  "reported": {
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "complete"
      }
  }
}
```

若要从组件报告可写属性，则克隆必须包括标记：

```csharp
TwinCollection reportedProperties = new TwinCollection();
TwinCollection component = new TwinCollection();
TwinCollection ackProps = new TwinCollection();
component["__t"] = "c"; // marker to identify a component
ackProps["value"] = 23.2;
ackProps["ac"] = 200; // using HTTP status codes
ackProps["av"] = 0; // not read from a desired property
ackProps["ad"] = "reported default value";
component["targetTemperature"] = ackProps;
reportedProperties["thermostat1"] = component;
await client.UpdateReportedPropertiesAsync(reportedProperties);
```

用下一个报告的属性更新设备克隆：

```json
{
  "reported": {
    "thermostat1": {
      "__t" : "c",
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "complete"
      }
    }
  }
}
```

#### <a name="subscribe-to-desired-property-updates"></a>订阅所需属性更新

服务可以更新在连接的设备上触发通知的所需属性。 此通知包括更新的所需属性，其中包括用于标识更新的版本号。 设备必须用与报告属性相同的消息进行响应 `ack` 。

如果模型没有组件，请参阅单一属性，并创建报告 `ack` 时所收到的版本：

```csharp
await client.SetDesiredPropertyUpdateCallbackAsync(async (desired, ctx) => 
{
  JValue targetTempJson = desired["targetTemperature"];
  double targetTemperature = targetTempJson.Value<double>();

  TwinCollection reportedProperties = new TwinCollection();
  TwinCollection ackProps = new TwinCollection();
  ackProps["value"] = targetTemperature;
  ackProps["ac"] = 200;
  ackProps["av"] = desired.Version; 
  ackProps["ad"] = "desired property received";
  reportedProperties["targetTemperature"] = ackProps;

  await client.UpdateReportedPropertiesAsync(reportedProperties);
}, null);
```

设备克隆在所需的和报告的部分显示属性：

```json
{
  "desired" : {
    "targetTemperature": 23.2,
    "$version" : 3
  },
  "reported": {
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "complete"
      }
  }
}
```

包含组件的模型接收用组件名称包装的所需属性，并报告返回 `ack` 报告的属性：

```csharp
await client.SetDesiredPropertyUpdateCallbackAsync(async (desired, ctx) =>
{
  JObject thermostatComponent = desired["thermostat1"];
  JToken targetTempProp = thermostatComponent["targetTemperature"];
  double targetTemperature = targetTempProp.Value<double>();

  TwinCollection reportedProperties = new TwinCollection();
  TwinCollection component = new TwinCollection();
  TwinCollection ackProps = new TwinCollection();
  component["__t"] = "c"; // marker to identify a component
  ackProps["value"] = targetTemperature;
  ackProps["ac"] = 200; // using HTTP status codes
  ackProps["av"] = desired.Version; // not readed from a desired property
  ackProps["ad"] = "desired property received";
  component["targetTemperature"] = ackProps;
  reportedProperties["thermostat1"] = component;

  await client.UpdateReportedPropertiesAsync(reportedProperties);
}, null);
```

组件的设备克隆显示了所需的和报告的部分，如下所示：

```json
{
  "desired" : {
    "thermostat1" : {
        "__t" : "c",
        "targetTemperature": 23.2,
    }
    "$version" : 3
  },
  "reported": {
    "thermostat1" : {
        "__t" : "c",
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "complete"
      }
    }
  }
}
```

### <a name="commands"></a>命令

不含组件的模型会接收服务调用的命令名称。

包含组件的模型将接收以组件和分隔符为前缀的命令名称 `*` 。

```csharp
await client.SetMethodHandlerAsync("themostat*reboot", (MethodRequest req, object ctx) =>
{
  Console.WriteLine("REBOOT");
  return Task.FromResult(new MethodResponse(200));
},
null);
```

#### <a name="request-and-response-payloads"></a>请求和响应负载

命令使用类型来定义其请求和响应负载。 设备必须反序列化传入的输入参数并序列化响应。 下面的示例演示如何实现具有负载中定义的复杂类型的命令：

```json
{
  "@type": "Command",
  "name": "start",
  "request": {
    "name": "startRequest",
    "schema": {
      "@type": "Object",
      "fields": [
        {
          "name": "startPriority",
          "schema": "integer"
        },
        {
          "name": "startMessage",
          "schema" : "string"
        }
      ]
    }
  },
  "response": {
    "name": "startReponse",
    "schema": {
      "@type": "Object",
      "fields": [
        {
            "name": "startupTime",
            "schema": "integer" 
        },
        {
          "name": "startupMessage",
          "schema": "string"
        }
      ]
    }
  }
}
```

下面的代码段演示了设备如何实现此命令定义，其中包括用于启用序列化和反序列化的类型：

```csharp
class startRequest
{
  public int startPriority { get; set; }
  public string startMessage { get; set; }
}

class startResponse
{
  public int startupTime { get; set; }
  public string startupMessage { get; set; }
}

// ... 

await client.SetMethodHandlerAsync("start", (MethodRequest req, object ctx) =>
{
  var startRequest = JsonConvert.DeserializeObject<startRequest>(req.DataAsJson);
  Console.WriteLine($"Received start command with priority ${startRequest.startPriority} and ${startRequest.startMessage}");

  var startResponse = new startResponse
  {
    startupTime = 123,
    startupMessage = "device started with message " + startRequest.startMessage
  };

  string responsePayload = JsonConvert.SerializeObject(startResponse);
  MethodResponse response = new MethodResponse(Encoding.UTF8.GetBytes(responsePayload), 200);
  return Task.FromResult(response);
},null);
```

> [!Tip]
> 请求和响应名称不存在于通过网络传输的序列化有效负载中。

## <a name="interact-with-the-device"></a>与设备交互 

IoT 即插即用允许你使用已通过 IoT 中心宣布其模型 ID 的设备。 例如，你可以直接访问设备的属性和命令。

若要使用已连接到 IoT 中心的 IoT 即插即用设备，请使用 IoT 中心 REST API 或 IoT 语言 Sdk 之一。 以下示例使用 IoT 中心 REST API。 该 API 的当前版本为 `2020-05-31-preview` 。 追加 `?api-version=2020-05-31` 到 REST PI 调用。

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

## <a name="next-steps"></a>后续步骤

现在，你已了解设备建模，以下是一些其他资源：

- [数字孪生定义语言（DTDL）](https://github.com/Azure/opendigitaltwins-dtdl)
- [C 设备 SDK](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
- [IoT REST API](https://docs.microsoft.com/rest/api/iothub/device)
- [模型组件](./concepts-components.md)
