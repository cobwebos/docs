---
title: 如何在 Azure 数字孪生中使用用户定义的函数 | Microsoft Docs
description: 有关如何使用 Azure 数字孪生创建用户定义的函数、匹配程序和角色分配的指南。
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/08/2018
ms.author: alinast
ms.openlocfilehash: 7fbaff5ed1b60a4434ba2eb0c78c6aa1f3fd6645
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2018
ms.locfileid: "49323706"
---
# <a name="how-to-use-user-defined-functions-in-azure-digital-twins"></a>如何在 Azure 数字孪生中使用用户定义的函数

[用户定义的函数](./concepts-user-defined-functions.md)使用户能够针对传入的遥测消息和空间图形元数据运行自定义逻辑，以便用户将事件发送到预定义的终结点。 在本指南中，我们将演示如何对温度事件执行操作，以检测超过特定温度的任何读数并发出警报。

在以下示例中，`https://yourManagementApiUrl` 代表数字孪生 API 的 URI：

```plaintext
https://yourInstanceName.yourLocation.azuresmartspaces.net/management
```

| 自定义属性名称 | 替换为 |
| --- | --- |
| `yourInstanceName` | Azure 数字孪生实例的名称 |
| `yourLocation` | 托管实例的服务器区域 |

## <a name="client-library-reference"></a>客户端库参考

以下[客户端参考](#Client-Reference)列举了用户定义函数运行时中可用作帮助程序方法的函数。

## <a name="create-a-matcher"></a>创建匹配程序

匹配程序是图形对象，可决定要对给定遥测消息执行哪些用户定义的函数。

有效匹配程序条件的比较：

- `Equals`
- `NotEquals`
- `Contains`

有效匹配程序条件的目标：

- `Sensor`
- `SensorDevice`
- `SensorSpace`

对于数据类型值为 `Temperature` 的所有传感器遥测事件，以下示例匹配程序的计算结果都为 true。 可以在用户定义的函数上创建多个匹配程序。

```text
POST https://yourManagementApiUrl/api/v1.0/matchers
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
  "SpaceId": "yourSpaceIdentifier"
}
```

| 自定义属性名称 | 替换为 |
| --- | --- |
| `yourManagementApiUrl` | 管理 API 的完整 URL 路径  |
| `yourSpaceIdentifier` | 托管实例的服务器区域 |

## <a name="create-a-user-defined-function-udf"></a>创建用户定义函数 (UDF)

创建匹配程序之后，使用以下 POST 调用来上传函数代码片段：

> [!IMPORTANT]
> - 在标头中，设置以下内容：`Content-Type: multipart/form-data; boundary="userDefinedBoundary"`。
> - 正文由多个部分组成：
>   - 第一部分是 UDF 所需的相关元数据。
>   - 第二部分是 javascript 计算逻辑。
> - 替换 `userDefinedBoundary` 节的 `SpaceId` 和 `Machers` GUID。

```plaintext
POST https://yourManagementApiUrl/api/v1.0/userdefinedfunctions with Content-Type: multipart/form-data; boundary="userDefinedBoundary"
```

| 自定义属性名称 | 替换为 |
| --- | --- |
| `yourManagementApiUrl` | 管理 API 的完整 URL 路径  |

正文：

```plaintext
--userDefinedBoundary
Content-Type: application/json; charset=utf-8
Content-Disposition: form-data; name="metadata"

{
  "SpaceId": "yourSpaceIdentifier",
  "Name": "User Defined Function",
  "Description": "The contents of this udf will be executed when matched against incoming telemetry.",
  "Matchers": ["yourMatcherIdentifier"]
}
--userDefinedBoundary
Content-Disposition: form-data; name="contents"; filename="userDefinedFunction.js"
Content-Type: text/javascript

function process(telemetry, executionContext) {
  // Code goes here.
}

--userDefinedBoundary--
```

| 自定义属性名称 | 替换为 |
| --- | --- |
| `yourSpaceIdentifier` | 空间标识符  |
| `yourMatcherIdentifier` | 要使用的匹配程序的 ID |

### <a name="example-functions"></a>示例函数

直接针对数据类型为 `Temperature`（也就是 sensor.DataType）的传感器设置传感器遥测读数：

```javascript
function process(telemetry, executionContext) {

  // Get sensor metadata
  var sensor = getSensorMetadata(telemetry.SensorId);

  // Retrieve the sensor value
  var parseReading = JSON.parse(telemetry.Message);

  // Set the sensor reading as the current value for the sensor.
  setSensorValue(telemetry.SensorId, sensor.DataType, parseReading.SensorValue);
}
```

如果传感器遥测读数超过预定义的阈值，则记录消息。 如果数字孪生实例上启用了诊断设置，则会转发用户定义函数中的日志：

```javascript
function process(telemetry, executionContext) {

  // Retrieve the sensor value
  var parseReading = JSON.parse(telemetry.Message);

  // Example sensor telemetry reading range is greater than 0.5
  if(parseFloat(parseReading.SensorValue) > 0.5) {
    log(`Alert: Sensor with ID: ${telemetry.SensorId} detected an anomaly!`);
  }
}
```

如果温度高于预定义常量，以下代码会触发通知。

```javascript
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

有关更复杂的 UDF 代码示例，请参阅 [Check available spaces with fresh air UDF](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/availability.js)（通过新鲜空气 UDF 检查可用空间）

## <a name="create-a-role-assignment"></a>创建角色分配

我们需要创建角色分配，让用户定义的函数依据其执行。 如果不这样做，用户定义的函数就没有适当的权限与管理 API 交互，进而无法对图形对象执行操作。 用户定义的函数所执行的操作也需遵循数字孪生管理 API 内基于角色的访问控制。 可通过指定特定角色或特定访问控制路径来限制这些操作的范围。 有关详细信息，请参阅[基于角色的访问控制](./security-role-based-access-control.md)文档。

- 查询角色，并获取要分配给 UDF 的角色的 ID；将它传递给下面的 RoleId。

```plaintext
GET https://yourManagementApiUrl/api/v1.0/system/roles
```

| 自定义属性名称 | 替换为 |
| --- | --- |
| `yourManagementApiUrl` | 管理 API 的完整 URL 路径  |

- ObjectId 就是先前创建的 UDF ID
- 使用空间的完整路径来查询空间，以查找 `Path`，并复制 `spacePaths` 值。 创建 UDF 角色分配时，将该值粘贴到下面的 Path 中

```plaintext
GET https://yourManagementApiUrl/api/v1.0/spaces?name=yourSpaceName&includes=fullpath
```

| 自定义属性名称 | 替换为 |
| --- | --- |
| `yourManagementApiUrl` | 管理 API 的完整 URL 路径  |
| `yourSpaceName` | 要使用的空间名称 |

```plaintext
POST https://yourManagementApiUrl/api/v1.0/roleassignments
{
  "RoleId": "yourDesiredRoleIdentifier",
  "ObjectId": "yourUserDefinedFunctionId",
  "ObjectIdType": "UserDefinedFunctionId",
  "Path": "yourAccessControlPath"
}
```

| 自定义属性名称 | 替换为 |
| --- | --- |
| `yourManagementApiUrl` | 管理 API 的完整 URL 路径  |
| `yourDesiredRoleIdentifier` | 所需角色的标识符 |
| `yourUserDefinedFunctionId` | 要使用的 UDF 的 ID |
| `yourAccessControlPath` | 访问控制路径 |

## <a name="send-telemetry-to-be-processed"></a>发送要处理的遥测数据

由图形中所述传感器生成的遥测数据应触发已上传的用户定义函数的执行。 一旦数据处理器选取了遥测数据，系统就会针对用户定义函数的调用创建执行计划。

1. 为生成读数的传感器检索匹配程序。
1. 根据成功进行计算的匹配程序，检索相关联的用户定义函数。
1. 执行每个用户定义函数。

## <a name="client-reference"></a>客户端参考

### <a name="getspacemetadataid--space"></a>getSpaceMetadata(id) ⇒ `space`

给定空间标识符后，从图形中检索空间。

**类型**：全局函数

| Param  | 类型                | Description  |
| ------ | ------------------- | ------------ |
| id  | `guid` | 空间标识符 |

### <a name="getsensormetadataid--sensor"></a>getSensorMetadata(id) ⇒ `sensor`

给定传感器标识符后，从图形中检索传感器。

**类型**：全局函数

| Param  | 类型                | Description  |
| ------ | ------------------- | ------------ |
| id  | `guid` | 传感器标识符 |

### <a name="getdevicemetadataid--device"></a>getDeviceMetadata(id) ⇒ `device`

给定设备标识符后，从图形中检索设备。

**类型**：全局函数

| Param  | 类型                | Description  |
| ------ | ------------------- | ------------ |
| id  | `guid` | 设备标识符 |

### <a name="getsensorvaluesensorid-datatype--value"></a>getSensorValue(sensorId, dataType) ⇒ `value`

给定传感器标识符及其数据类型后，检索该传感器的当前值。

**类型**：全局函数

| Param  | 类型                | Description  |
| ------ | ------------------- | ------------ |
| sensorId  | `guid` | 传感器标识符 |
| dataType  | `string` | 传感器数据类型 |

### <a name="getspacevaluespaceid-valuename--value"></a>getSpaceValue(spaceId, valueName) ⇒ `value`

给定空间标识符和值名称后，检索该空间属性的当前值。

**类型**：全局函数

| Param  | 类型                | Description  |
| ------ | ------------------- | ------------ |
| spaceId  | `guid` | 空间标识符 |
| valueName  | `string` | 空间属性名称 |

### <a name="getsensorhistoryvaluessensorid-datatype--value"></a>getSensorHistoryValues(sensorId, dataType) ⇒ `value[]`

给定传感器标识符及其数据类型后，检索该传感器的历史值。

**类型**：全局函数

| Param  | 类型                | Description  |
| ------ | ------------------- | ------------ |
| sensorId  | `guid` | 传感器标识符 |
| dataType  | `string` | 传感器数据类型 |

### <a name="getspacehistoryvaluesspaceid-datatype--value"></a>getSpaceHistoryValues(spaceId, dataType) ⇒ `value[]`

给定空间标识符和值名称后，检索空间上该属性的历史值。

**类型**：全局函数

| Param  | 类型                | Description  |
| ------ | ------------------- | ------------ |
| spaceId  | `guid` | 空间标识符 |
| valueName  | `string` | 空间属性名称 |

### <a name="getspacechildspacesspaceid--space"></a>getSpaceChildSpaces(spaceId) ⇒ `space[]`

给定空间标识符后，检索该父空间的子空间。

**类型**：全局函数

| Param  | 类型                | Description  |
| ------ | ------------------- | ------------ |
| spaceId  | `guid` | 空间标识符 |

### <a name="getspacechildsensorsspaceid--sensor"></a>getSpaceChildSensors(spaceId) ⇒ `sensor[]`

给定空间标识符后，检索该父空间的子传感器。

**类型**：全局函数

| Param  | 类型                | Description  |
| ------ | ------------------- | ------------ |
| spaceId  | `guid` | 空间标识符 |

### <a name="getspacechilddevicesspaceid--device"></a>getSpaceChildDevices(spaceId) ⇒ `device[]`

给定空间标识符后，检索该父空间的子设备。

**类型**：全局函数

| Param  | 类型                | Description  |
| ------ | ------------------- | ------------ |
| spaceId  | `guid` | 空间标识符 |

### <a name="getdevicechildsensorsdeviceid--sensor"></a>getDeviceChildSensors(deviceId) ⇒ `sensor[]`

给定设备标识符后，检索该父设备的子传感器。

**类型**：全局函数

| Param  | 类型                | Description  |
| ------ | ------------------- | ------------ |
| deviceId  | `guid` | 设备标识符 |

### <a name="getspaceparentspacechildspaceid--space"></a>getSpaceParentSpace(childSpaceId) ⇒ `space`

给定空间标识符后，检索其父空间。

**类型**：全局函数

| Param  | 类型                | Description  |
| ------ | ------------------- | ------------ |
| childSpaceId  | `guid` | 空间标识符 |

### <a name="getsensorparentspacechildsensorid--space"></a>getSensorParentSpace(childSensorId) ⇒ `space`

给定传感器标识符后，检索其父空间。

**类型**：全局函数

| Param  | 类型                | Description  |
| ------ | ------------------- | ------------ |
| childSensorId  | `guid` | 传感器标识符 |

### <a name="getdeviceparentspacechilddeviceid--space"></a>getDeviceParentSpace(childDeviceId) ⇒ `space`

给定设备标识符后，检索其父空间。

**类型**：全局函数

| Param  | 类型                | Description  |
| ------ | ------------------- | ------------ |
| childDeviceId  | `guid` | 设备标识符 |

### <a name="getsensorparentdevicechildsensorid--space"></a>getSensorParentDevice(childSensorId) ⇒ `space`

给定传感器标识符后，检索其父设备。

**类型**：全局函数

| Param  | 类型                | Description  |
| ------ | ------------------- | ------------ |
| childSensorId  | `guid` | 传感器标识符 |

### <a name="getspaceextendedpropertyspaceid-propertyname--extendedproperty"></a>getSpaceExtendedProperty(spaceId, propertyName) ⇒ `extendedProperty`

给定空间标识符后，从空间中检索属性及其值。

**类型**：全局函数

| Param  | 类型                | Description  |
| ------ | ------------------- | ------------ |
| spaceId  | `guid` | 空间标识符 |
| propertyName  | `string` | 空间属性名称 |

### <a name="getsensorextendedpropertysensorid-propertyname--extendedproperty"></a>getSensorExtendedProperty(sensorId, propertyName) ⇒ `extendedProperty`

给定传感器标识符后，从传感器中检索属性及其值。

**类型**：全局函数

| Param  | 类型                | Description  |
| ------ | ------------------- | ------------ |
| sensorId  | `guid` | 传感器标识符 |
| propertyName  | `string` | 传感器属性名称 |

### <a name="getdeviceextendedpropertydeviceid-propertyname--extendedproperty"></a>getDeviceExtendedProperty(deviceId, propertyName) ⇒ `extendedProperty`

给定设备标识符后，从设备中检索属性及其值。

**类型**：全局函数

| Param  | 类型                | Description  |
| ------ | ------------------- | ------------ |
| deviceId  | `guid` | 设备标识符 |
| propertyName  | `string` | 设备属性名称 |

### <a name="setsensorvaluesensorid-datatype-value"></a>setSensorValue(sensorId, dataType, value)

在具有给定数据类型的传感器对象上设置值。

**类型**：全局函数

| Param  | 类型                | Description  |
| ------ | ------------------- | ------------ |
| sensorId  | `guid` | 传感器标识符 |
| dataType  | `string` | 传感器数据类型 |
| 值  | `string` | 值 |

### <a name="setspacevaluespaceid-datatype-value"></a>setSpaceValue(spaceId, dataType, value)

在具有给定数据类型的空间对象上设置值。

**类型**：全局函数

| Param  | 类型                | Description  |
| ------ | ------------------- | ------------ |
| spaceId  | `guid` | 空间标识符 |
| dataType  | `string` | 数据类型 |
| 值  | `string` | 值 |

### <a name="logmessage"></a>log(message)

记录用户定义函数中的以下消息。

**类型**：全局函数

| Param  | 类型                | Description  |
| ------ | ------------------- | ------------ |
| message  | `string` | 要记录的消息 |

### <a name="sendnotificationtopologyobjectid-topologyobjecttype-payload"></a>sendNotification(topologyObjectId, topologyObjectType, payload)

发出要分派的自定义通知。

**类型**：全局函数

| Param  | 类型                | Description  |
| ------ | ------------------- | ------------ |
| topologyObjectId  | `Guid` | 图形对象标识符（例如： 空间/传感器/设备 ID）|
| topologyObjectType  | `string` | （例如： 空间/传感器/设备）|
| payload  | `string` | 与通知一起发送的 json 有效负载 |

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

返回当前空间的父空间。

#### <a name="childsensors--sensor"></a>ChildSensors() ⇒ `sensor[]`

返回当前空间的子传感器。

#### <a name="childdevices--device"></a>ChildDevices() ⇒ `device[]`

返回当前空间的子设备。

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty(propertyName) ⇒ `extendedProperty`

返回当前空间的扩展属性及其值。

| Param  | 类型                | Description  |
| ------ | ------------------- | ------------ |
| propertyName | `string` | 扩展属性的名称 |

#### <a name="valuevaluename--value"></a>Value(valueName) ⇒ `value`

返回当前空间的值。

| Param  | 类型                | Description  |
| ------ | ------------------- | ------------ |
| valueName | `string` | 值的名称 |

#### <a name="historyvaluename--value"></a>History(valueName) ⇒ `value[]`

返回当前空间的历史值。

| Param  | 类型                | Description  |
| ------ | ------------------- | ------------ |
| valueName | `string` | 值的名称 |

#### <a name="notifypayload"></a>Notify(payload)

发送具有指定有效负载的通知。

| Param  | 类型                | Description  |
| ------ | ------------------- | ------------ |
| payload | `string` | 要包含在通知中的 json 有效负载 |

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

返回当前设备的父空间。

#### <a name="childsensors--sensor"></a>ChildSensors() ⇒ `sensor[]`

返回当前设备的子传感器。

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty(propertyName) ⇒ `extendedProperty`

返回当前设备的扩展属性及其值。

| Param  | 类型                | Description  |
| ------ | ------------------- | ------------ |
| propertyName | `string` | 扩展属性的名称 |

#### <a name="notifypayload"></a>Notify(payload)

发送具有指定有效负载的通知。

| Param  | 类型                | Description  |
| ------ | ------------------- | ------------ |
| payload | `string` | 要包含在通知中的 json 有效负载 |

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

返回当前传感器的父空间。

#### <a name="device--device"></a>Device() ⇒ `device`

返回当前传感器的父设备。

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty(propertyName) ⇒ `extendedProperty`

返回当前传感器的扩展属性及其值。

| Param  | 类型                | Description  |
| ------ | ------------------- | ------------ |
| propertyName | `string` | 扩展属性的名称 |

#### <a name="value--value"></a>Value() ⇒ `value`

返回当前传感器的值。

#### <a name="history--value"></a>History() ⇒ `value[]`

返回当前传感器的历史值。

#### <a name="notifypayload"></a>Notify(payload)

发送具有指定有效负载的通知。

| Param  | 类型                | Description  |
| ------ | ------------------- | ------------ |
| payload | `string` | 要包含在通知中的 json 有效负载 |

### <a name="value"></a>值

```JSON
{
  "DataType": "Temperature",
  "Value": "70",
  "CreatedTime": ""
}
```

### <a name="extended-property"></a>扩展的属性

```JSON
{
  "Name": "OccupancyStatus",
  "Value": "Occupied"
}
```

## <a name="next-steps"></a>后续步骤

若要了解如何创建数字孪生终结点以向其发送事件，请参阅[创建数字孪生终结点](how-to-egress-endpoints.md)。

有关数字孪生终结点的更多详细信息，请阅读[详细了解终结点](concepts-events-routing.md)。
