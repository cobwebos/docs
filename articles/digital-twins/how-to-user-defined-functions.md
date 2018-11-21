---
title: 如何在 Azure 数字孪生中使用用户定义的函数 | Microsoft Docs
description: 有关如何使用 Azure 数字孪生创建用户定义的函数、匹配程序和角色分配的指南。
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: alinast
ms.openlocfilehash: 6a757dca48dc3ff41adfe6f8802fad40e7a4ca81
ms.sourcegitcommit: 542964c196a08b83dd18efe2e0cbfb21a34558aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/14/2018
ms.locfileid: "51636826"
---
# <a name="how-to-use-user-defined-functions-in-azure-digital-twins"></a>如何在 Azure 数字孪生中使用用户定义的函数

[用户定义的函数](./concepts-user-defined-functions.md) (UDF) 使用户能够针对传入的遥测消息和空间图形元数据运行自定义逻辑。 然后，用户可将事件发送到预定义的终结点。 本指南将演示如何对温度事件执行操作，以检测超过特定温度的任何读数并发出警报。

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

## <a name="client-library-reference"></a>客户端库参考

[客户端参考](#Client-Reference)部分中列出了用户定义函数运行时中可用作帮助程序方法的函数。

## <a name="create-a-matcher"></a>创建匹配程序

匹配程序是图形对象，可决定要对给定遥测消息运行哪些用户定义的函数。

- 有效匹配程序条件的比较：

  - `Equals`
  - `NotEquals`
  - `Contains`

- 有效匹配程序条件的目标：

  - `Sensor`
  - `SensorDevice`
  - `SensorSpace`

对于数据类型值为 `"Temperature"` 的所有传感器遥测事件，以下示例匹配程序的计算结果都为 true。 可以在用户定义的函数上创建多个匹配程序：

```plaintext
POST YOUR_MANAGEMENT_API_URL/matchers
{
  "Name": "Temperature Matcher",
  "Conditions": [
    {
      "target": "Sensor",
      "path": "$.dataType",
      "value": "\"Temperature\"",
      "comparison": "Equals"
    }
  ],
  "SpaceId": "YOUR_SPACE_IDENTIFIER"
}
```

| 值 | 替换为 |
| --- | --- |
| YOUR_SPACE_IDENTIFIER | 托管实例的服务器区域 |

## <a name="create-a-user-defined-function-udf"></a>创建用户定义函数 (UDF)

创建匹配程序后，使用以下 POST 调用来上传函数代码片段：

> [!IMPORTANT]
> - 在标头中，设置以下内容：`Content-Type: multipart/form-data; boundary="userDefinedBoundary"`。
> - 正文由多个部分组成：
>   - 第一部分是 UDF 所需的相关元数据。
>   - 第二部分是 JavaScript 计算逻辑。
> - 在 USER_DEFINED_BOUNDARY 部分中，替换 SpaceId 和 Machers 值。

```plaintext
POST YOUR_MANAGEMENT_API_URL/userdefinedfunctions with Content-Type: multipart/form-data; boundary="USER_DEFINED_BOUNDARY"
```

| 参数值 | 替换为 |
| --- | --- |
| USER_DEFINED_BOUNDARY | 多部分内容边界名称 |

### <a name="body"></a>Body

```plaintext
--USER_DEFINED_BOUNDARY
Content-Type: application/json; charset=utf-8
Content-Disposition: form-data; name="metadata"

{
  "SpaceId": "YOUR_SPACE_IDENTIFIER",
  "Name": "User Defined Function",
  "Description": "The contents of this udf will be executed when matched against incoming telemetry.",
  "Matchers": ["YOUR_MATCHER_IDENTIFIER"]
}
--USER_DEFINED_BOUNDARY
Content-Disposition: form-data; name="contents"; filename="userDefinedFunction.js"
Content-Type: text/javascript

function process(telemetry, executionContext) {
  // Code goes here.
}

--USER_DEFINED_BOUNDARY--
```

| 值 | 替换为 |
| --- | --- |
| YOUR_SPACE_IDENTIFIER | 空间标识符  |
| YOUR_MATCHER_IDENTIFIER | 要使用的匹配程序的 ID |

### <a name="example-functions"></a>示例函数

直接针对数据类型为 **Temperature**（也就是 `sensor.DataType`）的传感器设置传感器遥测读数：

```JavaScript
function process(telemetry, executionContext) {

  // Get sensor metadata
  var sensor = getSensorMetadata(telemetry.SensorId);

  // Retrieve the sensor value
  var parseReading = JSON.parse(telemetry.Message);

  // Set the sensor reading as the current value for the sensor.
  setSensorValue(telemetry.SensorId, sensor.DataType, parseReading.SensorValue);
}
```

telemetry 参数公开了 SensorId 和 Message 属性（对应于传感器发送的消息）。 **ExecutionContext** 参数公开了以下属性：

```csharp
var executionContext = new UdfExecutionContext
{
    EnqueuedTime = request.HubEnqueuedTime,
    ProcessorReceivedTime = request.ProcessorReceivedTime,
    UserDefinedFunctionId = request.UserDefinedFunctionId,
    CorrelationId = correlationId.ToString(),
};
```

在下一示例中，如果传感器遥测读数超过预定义的阈值，则记录消息。 如果 Azure 数字孪生实例上启用了诊断设置，则还会转发用户定义函数中的日志：

```JavaScript
function process(telemetry, executionContext) {

  // Retrieve the sensor value
  var parseReading = JSON.parse(telemetry.Message);

  // Example sensor telemetry reading range is greater than 0.5
  if(parseFloat(parseReading.SensorValue) > 0.5) {
    log(`Alert: Sensor with ID: ${telemetry.SensorId} detected an anomaly!`);
  }
}
```

如果温度高于预定义常量，以下代码会触发通知：

```JavaScript
function process(telemetry, executionContext) {

  // Retrieve the sensor value
  var parseReading = JSON.parse(telemetry.Message);

  // Define threshold
  var threshold = 70;

  // Trigger notification 
  if(parseInt(parseReading) > threshold) {
    var alert = {
      message: 'Temperature reading has surpassed threshold',
      sensorId: telemetry.SensorId,
      reading: parseReading
    };

    sendNotification(telemetry.SensorId, "Sensor", JSON.stringify(alert));
  }
}
```

有关更复杂的 UDF 代码示例，请参阅[通过新鲜空气 UDF 检查可用空间](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/availability.js)。

## <a name="create-a-role-assignment"></a>创建角色分配

我们需要创建角色分配，让用户定义的函数依据其运行。 如果不这样做，用户定义的函数就没有适当的权限与管理 API 交互，进而无法对图形对象执行操作。 用户定义的函数所执行的操作也需遵循 Azure 数字孪生管理 API 内基于角色的访问控制。 可通过指定特定角色或特定访问控制路径来限制这些操作的范围。 有关详细信息，请参阅[基于角色的访问控制](./security-role-based-access-control.md)文档。

1. 查询角色，并获取要分配给 UDF 的角色的 ID。 将其传递给 RoleId：

    ```plaintext
    GET YOUR_MANAGEMENT_API_URL/system/roles
    ```

1. **ObjectId** 就是先前创建的 UDF ID。
1. 通过使用 `fullpath` 查询你的空间来查找 **Path** 的值。
1. 复制返回的 `spacePaths` 值。 在以下代码中，将要用到该值：

    ```plaintext
    GET YOUR_MANAGEMENT_API_URL/spaces?name=YOUR_SPACE_NAME&includes=fullpath
    ```

    | 参数值 | 替换为 |
    | --- | --- |
    | YOUR_SPACE_NAME | 要使用的空间名称 |

1. 将返回的 `spacePaths` 值粘贴到 Path 中以创建 UDF 角色分配：

    ```plaintext
    POST YOUR_MANAGEMENT_API_URL/roleassignments
    {
      "RoleId": "YOUR_DESIRED_ROLE_IDENTIFIER",
      "ObjectId": "YOUR_USER_DEFINED_FUNCTION_ID",
      "ObjectIdType": "YOUR_USER_DEFINED_FUNCTION_TYPE_ID",
      "Path": "YOUR_ACCESS_CONTROL_PATH"
    }
    ```

    | 值 | 替换为 |
    | --- | --- |
    | YOUR_DESIRED_ROLE_IDENTIFIER | 所需角色的标识符 |
    | YOUR_USER_DEFINED_FUNCTION_ID | 要使用的 UDF 的 ID |
    | YOUR_USER_DEFINED_FUNCTION_TYPE_ID | 指定 UDF 类型的 ID |
    | YOUR_ACCESS_CONTROL_PATH | 访问控制路径 |

## <a name="send-telemetry-to-be-processed"></a>发送要处理的遥测数据

由图形中所述传感器生成的遥测数据触发已上传的用户定义函数的运行。 数据处理器将选取遥测数据。 然后为调用用户定义函数创建运行计划。

1. 为生成读数的传感器检索匹配程序。
1. 根据成功进行计算的匹配程序，检索相关联的用户定义函数。
1. 运行每个用户定义函数。

## <a name="client-reference"></a>客户端参考

### <a name="getspacemetadataid--space"></a>getSpaceMetadata(id) ⇒ `space`

给定空间标识符后，此函数将从图形中检索空间。

**类型**：全局函数

| 参数  | 类型                | Description  |
| ---------- | ------------------- | ------------ |
| *id*  | `guid` | 空间标识符 |

### <a name="getsensormetadataid--sensor"></a>getSensorMetadata(id) ⇒ `sensor`

给定传感器标识符后，此函数将从图形中检索传感器。

**类型**：全局函数

| 参数  | 类型                | Description  |
| ---------- | ------------------- | ------------ |
| *id*  | `guid` | 传感器标识符 |

### <a name="getdevicemetadataid--device"></a>getDeviceMetadata(id) ⇒ `device`

给定设备标识符后，此函数将从图形中检索设备。

**类型**：全局函数

| 参数  | 类型                | Description  |
| ------ | ------------------- | ------------ |
| *id* | `guid` | 设备标识符 |

### <a name="getsensorvaluesensorid-datatype--value"></a>getSensorValue(sensorId, dataType) ⇒ `value`

给定传感器标识符及其数据类型后，此函数将检索该传感器的当前值。

**类型**：全局函数

| 参数  | 类型                | Description  |
| ------ | ------------------- | ------------ |
| *sensorId*  | `guid` | 传感器标识符 |
| *dataType*  | `string` | 传感器数据类型 |

### <a name="getspacevaluespaceid-valuename--value"></a>getSpaceValue(spaceId, valueName) ⇒ `value`

给定空间标识符及其值名称后，此函数将检索该空间属性的当前值。

**类型**：全局函数

| 参数  | 类型                | Description  |
| ------ | ------------------- | ------------ |
| *spaceId*  | `guid` | 空间标识符 |
| *valueName* | `string` | 空间属性名称 |

### <a name="getsensorhistoryvaluessensorid-datatype--value"></a>getSensorHistoryValues(sensorId, dataType) ⇒ `value[]`

给定传感器标识符及其数据类型后，此函数将检索该传感器的历史值。

**类型**：全局函数

| 参数  | 类型                | Description  |
| ------ | ------------------- | ------------ |
| *sensorId* | `guid` | 传感器标识符 |
| *dataType* | `string` | 传感器数据类型 |

### <a name="getspacehistoryvaluesspaceid-datatype--value"></a>getSpaceHistoryValues(spaceId, dataType) ⇒ `value[]`

给定空间标识符和值名称后，此函数将检索空间上该属性的历史值。

**类型**：全局函数

| 参数  | 类型                | Description  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | 空间标识符 |
| *valueName* | `string` | 空间属性名称 |

### <a name="getspacechildspacesspaceid--space"></a>getSpaceChildSpaces(spaceId) ⇒ `space[]`

给定空间标识符后，此函数将检索该父空间的子空间。

**类型**：全局函数

| 参数  | 类型                | Description  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | 空间标识符 |

### <a name="getspacechildsensorsspaceid--sensor"></a>getSpaceChildSensors(spaceId) ⇒ `sensor[]`

给定空间标识符后，此函数将检索该父空间的子传感器。

**类型**：全局函数

| 参数  | 类型                | Description  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | 空间标识符 |

### <a name="getspacechilddevicesspaceid--device"></a>getSpaceChildDevices(spaceId) ⇒ `device[]`

给定空间标识符后，此函数将检索该父空间的子设备。

**类型**：全局函数

| 参数  | 类型                | Description  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | 空间标识符 |

### <a name="getdevicechildsensorsdeviceid--sensor"></a>getDeviceChildSensors(deviceId) ⇒ `sensor[]`

给定设备标识符后，此函数将检索该父设备的子传感器。

**类型**：全局函数

| 参数  | 类型                | Description  |
| ------ | ------------------- | ------------ |
| *deviceId* | `guid` | 设备标识符 |

### <a name="getspaceparentspacechildspaceid--space"></a>getSpaceParentSpace(childSpaceId) ⇒ `space`

给定空间标识符后，此函数将检索其父空间。

**类型**：全局函数

| 参数  | 类型                | Description  |
| ------ | ------------------- | ------------ |
| *childSpaceId* | `guid` | 空间标识符 |

### <a name="getsensorparentspacechildsensorid--space"></a>getSensorParentSpace(childSensorId) ⇒ `space`

给定传感器标识符后，此函数将检索其父空间。

**类型**：全局函数

| 参数  | 类型                | Description  |
| ------ | ------------------- | ------------ |
| *childSensorId* | `guid` | 传感器标识符 |

### <a name="getdeviceparentspacechilddeviceid--space"></a>getDeviceParentSpace(childDeviceId) ⇒ `space`

给定设备标识符后，此函数将检索其父空间。

**类型**：全局函数

| 参数  | 类型                | Description  |
| ------ | ------------------- | ------------ |
| *childDeviceId* | `guid` | 设备标识符 |

### <a name="getsensorparentdevicechildsensorid--space"></a>getSensorParentDevice(childSensorId) ⇒ `space`

给定传感器标识符后，此函数将检索其父设备。

**类型**：全局函数

| 参数  | 类型                | Description  |
| ------ | ------------------- | ------------ |
| *childSensorId* | `guid` | 传感器标识符 |

### <a name="getspaceextendedpropertyspaceid-propertyname--extendedproperty"></a>getSpaceExtendedProperty(spaceId, propertyName) ⇒ `extendedProperty`

给定空间标识符后，此函数将从空间中检索属性及其值。

**类型**：全局函数

| 参数  | 类型                | Description  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | 空间标识符 |
| *propertyName* | `string` | 空间属性名称 |

### <a name="getsensorextendedpropertysensorid-propertyname--extendedproperty"></a>getSensorExtendedProperty(sensorId, propertyName) ⇒ `extendedProperty`

给定传感器标识符后，此函数将从传感器中检索属性及其值。

**类型**：全局函数

| 参数  | 类型                | Description  |
| ------ | ------------------- | ------------ |
| *sensorId* | `guid` | 传感器标识符 |
| *propertyName* | `string` | 传感器属性名称 |

### <a name="getdeviceextendedpropertydeviceid-propertyname--extendedproperty"></a>getDeviceExtendedProperty(deviceId, propertyName) ⇒ `extendedProperty`

给定设备标识符后，此函数将从设备中检索属性及其值。

**类型**：全局函数

| 参数  | 类型                | Description  |
| ------ | ------------------- | ------------ |
| *deviceId* | `guid` | 设备标识符 |
| *propertyName* | `string` | 设备属性名称 |

### <a name="setsensorvaluesensorid-datatype-value"></a>setSensorValue(sensorId, dataType, value)

此函数将在具有给定数据类型的传感器对象上设置值。

**类型**：全局函数

| 参数  | 类型                | Description  |
| ------ | ------------------- | ------------ |
| *sensorId* | `guid` | 传感器标识符 |
| *dataType*  | `string` | 传感器数据类型 |
| *值*  | `string` | 值 |

### <a name="setspacevaluespaceid-datatype-value"></a>setSpaceValue(spaceId, dataType, value)

此函数将在具有给定数据类型的空间对象上设置值。

**类型**：全局函数

| 参数  | 类型                | Description  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | 空间标识符 |
| *dataType* | `string` | 数据类型 |
| *值* | `string` | 值 |

### <a name="logmessage"></a>log(message)

此函数记录用户定义函数中的以下消息。

**类型**：全局函数

| 参数  | 类型                | Description  |
| ------ | ------------------- | ------------ |
| *message* | `string` | 要记录的消息 |

### <a name="sendnotificationtopologyobjectid-topologyobjecttype-payload"></a>sendNotification(topologyObjectId, topologyObjectType, payload)

此函数发出要分派的自定义通知。

**类型**：全局函数

| 参数  | 类型                | Description  |
| ------ | ------------------- | ------------ |
| *topologyObjectId*  | `guid` | 图形对象标识符。 例如，空格、传感器和设备 ID。|
| *topologyObjectType*  | `string` | 例如，空格、传感器和设备。|
| *payload*  | `string` | 与通知一起发送的 JSON 有效负载。 |

## <a name="return-types"></a>返回类型

以下模型描述了上述客户端参考中的返回对象。

### <a name="space"></a>空格

```JSON
{
  "Id": "00000000-0000-0000-0000-000000000000",
  "Name": "Space",
  "FriendlyName": "Conference Room",
  "TypeId": 0,
  "ParentSpaceId": "00000000-0000-0000-0000-000000000001",
  "SubtypeId": 0
}
```

### <a name="space-methods"></a>空间方法

#### <a name="parent--space"></a>Parent() ⇒ `space`

此函数将返回当前空间的父空间。

#### <a name="childsensors--sensor"></a>ChildSensors() ⇒ `sensor[]`

此函数将返回当前空间的子传感器。

#### <a name="childdevices--device"></a>ChildDevices() ⇒ `device[]`

此函数将返回当前空间的子设备。

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty(propertyName) ⇒ `extendedProperty`

此函数将返回当前空间的扩展属性及其值。

| 参数  | 类型                | Description  |
| ------ | ------------------- | ------------ |
| *propertyName* | `string` | 扩展属性的名称 |

#### <a name="valuevaluename--value"></a>Value(valueName) ⇒ `value`

此函数将返回当前空间的值。

| 参数  | 类型                | Description  |
| ------ | ------------------- | ------------ |
| *valueName* | `string` | 值的名称 |

#### <a name="historyvaluename--value"></a>History(valueName) ⇒ `value[]`

此函数将返回当前空间的历史值。

| 参数  | 类型                | Description  |
| ------ | ------------------- | ------------ |
| *valueName* | `string` | 值的名称 |

#### <a name="notifypayload"></a>Notify(payload)

此函数发送具有指定有效负载的通知。

| 参数  | 类型                | Description  |
| ------ | ------------------- | ------------ |
| *payload* | `string` | 要包含在通知中的 JSON 有效负载 |

### <a name="device"></a>设备

```JSON
{
  "Id": "00000000-0000-0000-0000-000000000002",
  "Name": "Device",
  "FriendlyName": "Temperature Sensing Device",
  "Description": "This device contains a sensor that captures temperature readings.",
  "Type": "None",
  "Subtype": "None",
  "TypeId": 0,
  "SubtypeId": 0,
  "HardwareId": "ABC123",
  "GatewayId": "ABC",
  "SpaceId": "00000000-0000-0000-0000-000000000000"
}
```

### <a name="device-methods"></a>设备方法

#### <a name="parent--space"></a>Parent() ⇒ `space`

此函数返回当前设备的父空间。

#### <a name="childsensors--sensor"></a>ChildSensors() ⇒ `sensor[]`

此函数返回当前设备的子传感器。

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty(propertyName) ⇒ `extendedProperty`

此函数返回当前设备的扩展属性及其值。

| 参数  | 类型                | Description  |
| ------ | ------------------- | ------------ |
| *propertyName* | `string` | 扩展属性的名称 |

#### <a name="notifypayload"></a>Notify(payload)

此函数发送具有指定有效负载的通知。

| 参数  | 类型                | Description  |
| ------ | ------------------- | ------------ |
| *payload* | `string` | 要包含在通知中的 JSON 有效负载 |

### <a name="sensor"></a>传感器

```JSON
{
  "Id": "00000000-0000-0000-0000-000000000003",
  "Port": "30",
  "PollRate": 3600,
  "DataType": "Temperature",
  "DataSubtype": "None",
  "Type": "Classic",
  "PortType": "None",
  "DataUnitType": "FahrenheitTemperature",
  "SpaceId": "00000000-0000-0000-0000-000000000000",
  "DeviceId": "00000000-0000-0000-0000-000000000001",
  "PortTypeId": 0,
  "DataUnitTypeId": 0,
  "DataTypeId": 0,
  "DataSubtypeId": 0,
  "TypeId": 0  
}
```

### <a name="sensor-methods"></a>传感器方法

#### <a name="space--space"></a>Space() ⇒ `space`

此函数返回当前传感器的父空间。

#### <a name="device--device"></a>Device() ⇒ `device`

此函数返回当前传感器的父设备。

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty(propertyName) ⇒ `extendedProperty`

此函数返回当前传感器的扩展属性及其值。

| 参数  | 类型                | Description  |
| ------ | ------------------- | ------------ |
| *propertyName* | `string` | 扩展属性的名称 |

#### <a name="value--value"></a>Value() ⇒ `value`

此函数返回当前传感器的值。

#### <a name="history--value"></a>History() ⇒ `value[]`

此函数返回当前传感器的历史值。

#### <a name="notifypayload"></a>Notify(payload)

此函数发送具有指定有效负载的通知。

| 参数  | 类型                | Description  |
| ------ | ------------------- | ------------ |
| *payload* | `string` | 要包含在通知中的 JSON 有效负载 |

### <a name="value"></a>值

```JSON
{
  "DataType": "Temperature",
  "Value": "70",
  "CreatedTime": ""
}
```

### <a name="extended-property"></a>扩展属性

```JSON
{
  "Name": "OccupancyStatus",
  "Value": "Occupied"
}
```

## <a name="next-steps"></a>后续步骤

- 了解如何[创建 Azure 数字孪生终结点](how-to-egress-endpoints.md)以向其发送事件。

- 有关 Azure 数字孪生终结点的详细信息，请参阅[详细了解终结点](concepts-events-routing.md)。
