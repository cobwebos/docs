---
title: User-defined functions client library reference - Azure Digital Twins | Microsoft Docs
description: Azure Digital Twins user-defined functions client library reference documentation.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: article
ms.date: 11/21/2019
ms.custom: seodec18
ms.openlocfilehash: 41baacd4485e6702ec29057f5d539724b74e353b
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383246"
---
# <a name="user-defined-functions-client-library-reference"></a>用户定义函数客户端库参考

本文档提供 Azure 数字孪生用户定义函数客户端库的参考信息。

## <a name="helper-methods"></a>帮助器方法

客户端库为常用操作定义帮助器方法。

### <a name="getspacemetadataid--space"></a>getSpaceMetadata(id) ⇒ `space`

给定空间标识符后，此函数将从图形中检索空间。

**类型**：全局函数

| 参数  | Type                | 描述  |
| ---------- | ------------------- | ------------ |
| *id*  | `guid` | 空间标识符 |

### <a name="getsensormetadataid--sensor"></a>getSensorMetadata(id) ⇒ `sensor`

给定传感器标识符后，此函数将从图形中检索传感器。

**类型**：全局函数

| 参数  | Type                | 描述  |
| ---------- | ------------------- | ------------ |
| *id*  | `guid` | 传感器标识符 |

### <a name="getdevicemetadataid--device"></a>getDeviceMetadata(id) ⇒ `device`

给定设备标识符后，此函数将从图形中检索设备。

**类型**：全局函数

| 参数  | Type                | 描述  |
| ------ | ------------------- | ------------ |
| *id* | `guid` | 设备标识符 |

### <a name="getsensorvaluesensorid-datatype--value"></a>getSensorValue(sensorId, dataType) ⇒ `value`

给定传感器标识符及其数据类型后，此函数将检索该传感器的当前值。

**类型**：全局函数

| 参数  | Type                | 描述  |
| ------ | ------------------- | ------------ |
| *sensorId*  | `guid` | 传感器标识符 |
| *dataType*  | `string` | 传感器数据类型 |

### <a name="getspacevaluespaceid-valuename--value"></a>getSpaceValue(spaceId, valueName) ⇒ `value`

给定空间标识符及其值名称后，此函数将检索该空间属性的当前值。

**类型**：全局函数

| 参数  | Type                | 描述  |
| ------ | ------------------- | ------------ |
| *spaceId*  | `guid` | 空间标识符 |
| *valueName* | `string` | 空间属性名称 |

### <a name="getsensorhistoryvaluessensorid-datatype--value"></a>getSensorHistoryValues(sensorId, dataType) ⇒ `value[]`

给定传感器标识符及其数据类型后，此函数将检索该传感器的历史值。

**类型**：全局函数

| 参数  | Type                | 描述  |
| ------ | ------------------- | ------------ |
| *sensorId* | `guid` | 传感器标识符 |
| *dataType* | `string` | 传感器数据类型 |

### <a name="getspacehistoryvaluesspaceid-datatype--value"></a>getSpaceHistoryValues(spaceId, dataType) ⇒ `value[]`

给定空间标识符和值名称后，此函数将检索空间上该属性的历史值。

**类型**：全局函数

| 参数  | Type                | 描述  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | 空间标识符 |
| *valueName* | `string` | 空间属性名称 |

### <a name="getspacechildspacesspaceid--space"></a>getSpaceChildSpaces(spaceId) ⇒ `space[]`

给定空间标识符后，此函数将检索该父空间的子空间。

**类型**：全局函数

| 参数  | Type                | 描述  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | 空间标识符 |

### <a name="getspacechildsensorsspaceid--sensor"></a>getSpaceChildSensors(spaceId) ⇒ `sensor[]`

给定空间标识符后，此函数将检索该父空间的子传感器。

**类型**：全局函数

| 参数  | Type                | 描述  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | 空间标识符 |

### <a name="getspacechilddevicesspaceid--device"></a>getSpaceChildDevices(spaceId) ⇒ `device[]`

给定空间标识符后，此函数将检索该父空间的子设备。

**类型**：全局函数

| 参数  | Type                | 描述  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | 空间标识符 |

### <a name="getdevicechildsensorsdeviceid--sensor"></a>getDeviceChildSensors(deviceId) ⇒ `sensor[]`

给定设备标识符后，此函数将检索该父设备的子传感器。

**类型**：全局函数

| 参数  | Type                | 描述  |
| ------ | ------------------- | ------------ |
| *deviceId* | `guid` | 设备标识符 |

### <a name="getspaceparentspacechildspaceid--space"></a>getSpaceParentSpace(childSpaceId) ⇒ `space`

给定空间标识符后，此函数将检索其父空间。

**类型**：全局函数

| 参数  | Type                | 描述  |
| ------ | ------------------- | ------------ |
| *childSpaceId* | `guid` | 空间标识符 |

### <a name="getsensorparentspacechildsensorid--space"></a>getSensorParentSpace(childSensorId) ⇒ `space`

给定传感器标识符后，此函数将检索其父空间。

**类型**：全局函数

| 参数  | Type                | 描述  |
| ------ | ------------------- | ------------ |
| *childSensorId* | `guid` | 传感器标识符 |

### <a name="getdeviceparentspacechilddeviceid--space"></a>getDeviceParentSpace(childDeviceId) ⇒ `space`

给定设备标识符后，此函数将检索其父空间。

**类型**：全局函数

| 参数  | Type                | 描述  |
| ------ | ------------------- | ------------ |
| *childDeviceId* | `guid` | 设备标识符 |

### <a name="getsensorparentdevicechildsensorid--space"></a>getSensorParentDevice(childSensorId) ⇒ `space`

给定传感器标识符后，此函数将检索其父设备。

**类型**：全局函数

| 参数  | Type                | 描述  |
| ------ | ------------------- | ------------ |
| *childSensorId* | `guid` | 传感器标识符 |

### <a name="getspaceextendedpropertyspaceid-propertyname--extendedproperty"></a>getSpaceExtendedProperty(spaceId, propertyName) ⇒ `extendedProperty`

给定空间标识符后，此函数将从空间中检索属性及其值。

**类型**：全局函数

| 参数  | Type                | 描述  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | 空间标识符 |
| *propertyName* | `string` | 空间属性名称 |

### <a name="getsensorextendedpropertysensorid-propertyname--extendedproperty"></a>getSensorExtendedProperty(sensorId, propertyName) ⇒ `extendedProperty`

给定传感器标识符后，此函数将从传感器中检索属性及其值。

**类型**：全局函数

| 参数  | Type                | 描述  |
| ------ | ------------------- | ------------ |
| *sensorId* | `guid` | 传感器标识符 |
| *propertyName* | `string` | 传感器属性名称 |

### <a name="getdeviceextendedpropertydeviceid-propertyname--extendedproperty"></a>getDeviceExtendedProperty(deviceId, propertyName) ⇒ `extendedProperty`

给定设备标识符后，此函数将从设备中检索属性及其值。

**类型**：全局函数

| 参数  | Type                | 描述  |
| ------ | ------------------- | ------------ |
| *deviceId* | `guid` | 设备标识符 |
| *propertyName* | `string` | 设备属性名称 |

### <a name="setsensorvaluesensorid-datatype-value"></a>setSensorValue(sensorId, dataType, value)

此函数将在具有给定数据类型的传感器对象上设置值。

**类型**：全局函数

| 参数  | Type                | 描述  |
| ------ | ------------------- | ------------ |
| *sensorId* | `guid` | 传感器标识符 |
| *dataType*  | `string` | 传感器数据类型 |
| *value*  | `string` | Value |

### <a name="setspacevaluespaceid-datatype-value"></a>setSpaceValue(spaceId, dataType, value)

此函数将在具有给定数据类型的空间对象上设置值。

**类型**：全局函数

| 参数  | Type                | 描述  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | 空间标识符 |
| *dataType* | `string` | 数据类型 |
| *value* | `string` | Value |

### <a name="logmessage"></a>log(message)

此函数记录用户定义函数中的以下消息。

**类型**：全局函数

| 参数  | Type                | 描述  |
| ------ | ------------------- | ------------ |
| *message* | `string` | 要记录的消息 |

### <a name="sendnotificationtopologyobjectid-topologyobjecttype-payload"></a>sendNotification(topologyObjectId, topologyObjectType, payload)

此函数发出要分派的自定义通知。

**类型**：全局函数

| 参数  | Type                | 描述  |
| ------ | ------------------- | ------------ |
| *topologyObjectId*  | `guid` | 图形对象标识符。 例如，空格、传感器和设备 ID。|
| *topologyObjectType*  | `string` | 例如，空格、传感器和设备。|
| *payload*  | `string` | 与通知一起发送的 JSON 有效负载。 |

## <a name="return-types"></a>返回类型

下面介绍从客户端参考帮助器方法返回的响应模型。

### <a name="space"></a>航天

```JSON
{
  "id": "00000000-0000-0000-0000-000000000000",
  "name": "Space",
  "friendlyName": "Conference Room",
  "typeId": 0,
  "parentSpaceId": "00000000-0000-0000-0000-000000000001",
  "subtypeId": 0
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

| 参数  | Type                | 描述  |
| ------ | ------------------- | ------------ |
| *propertyName* | `string` | 扩展属性的名称 |

#### <a name="valuevaluename--value"></a>Value(valueName) ⇒ `value`

此函数将返回当前空间的值。

| 参数  | Type                | 描述  |
| ------ | ------------------- | ------------ |
| *valueName* | `string` | 值的名称 |

#### <a name="historyvaluename--value"></a>History(valueName) ⇒ `value[]`

此函数将返回当前空间的历史值。

| 参数  | Type                | 描述  |
| ------ | ------------------- | ------------ |
| *valueName* | `string` | 值的名称 |

#### <a name="notifypayload"></a>Notify(payload)

此函数发送具有指定有效负载的通知。

| 参数  | Type                | 描述  |
| ------ | ------------------- | ------------ |
| *payload* | `string` | 要包含在通知中的 JSON 有效负载 |

### <a name="device"></a>设备

```JSON
{
  "id": "00000000-0000-0000-0000-000000000002",
  "name": "Device",
  "friendlyName": "Temperature Sensing Device",
  "description": "This device contains a sensor that captures temperature readings.",
  "type": "None",
  "subtype": "None",
  "typeId": 0,
  "subtypeId": 0,
  "hardwareId": "ABC123",
  "gatewayId": "ABC",
  "spaceId": "00000000-0000-0000-0000-000000000000"
}
```

### <a name="device-methods"></a>设备方法

#### <a name="parent--space"></a>Parent() ⇒ `space`

此函数返回当前设备的父空间。

#### <a name="childsensors--sensor"></a>ChildSensors() ⇒ `sensor[]`

此函数返回当前设备的子传感器。

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty(propertyName) ⇒ `extendedProperty`

此函数返回当前设备的扩展属性及其值。

| 参数  | Type                | 描述  |
| ------ | ------------------- | ------------ |
| *propertyName* | `string` | 扩展属性的名称 |

#### <a name="notifypayload"></a>Notify(payload)

此函数发送具有指定有效负载的通知。

| 参数  | Type                | 描述  |
| ------ | ------------------- | ------------ |
| *payload* | `string` | 要包含在通知中的 JSON 有效负载 |

### <a name="sensor"></a>传感器

```JSON
{
  "id": "00000000-0000-0000-0000-000000000003",
  "port": "30",
  "pollRate": 3600,
  "dataType": "Temperature",
  "dataSubtype": "None",
  "type": "Classic",
  "portType": "None",
  "dataUnitType": "FahrenheitTemperature",
  "spaceId": "00000000-0000-0000-0000-000000000000",
  "deviceId": "00000000-0000-0000-0000-000000000001",
  "portTypeId": 0,
  "dataUnitTypeId": 0,
  "dataTypeId": 0,
  "dataSubtypeId": 0,
  "typeId": 0  
}
```

### <a name="sensor-methods"></a>传感器方法

#### <a name="space--space"></a>Space() ⇒ `space`

此函数返回当前传感器的父空间。

#### <a name="device--device"></a>Device() ⇒ `device`

此函数返回当前传感器的父设备。

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty(propertyName) ⇒ `extendedProperty`

此函数返回当前传感器的扩展属性及其值。

| 参数  | Type                | 描述  |
| ------ | ------------------- | ------------ |
| *propertyName* | `string` | 扩展属性的名称 |

#### <a name="value--value"></a>Value() ⇒ `value`

此函数返回当前传感器的值。

#### <a name="history--value"></a>History() ⇒ `value[]`

此函数返回当前传感器的历史值。

#### <a name="notifypayload"></a>Notify(payload)

此函数发送具有指定有效负载的通知。

| 参数  | Type                | 描述  |
| ------ | ------------------- | ------------ |
| *payload* | `string` | 要包含在通知中的 JSON 有效负载 |

### <a name="value"></a>Value

```JSON
{
  "dataType": "Temperature",
  "value": "70",
  "createdTime": ""
}
```

### <a name="extended-property"></a>扩展属性

```JSON
{
  "name": "OccupancyStatus",
  "value": "Occupied"
}
```

## <a name="next-steps"></a>后续步骤

- 了解 [Azure 数字孪生用户定义函数](./concepts-user-defined-functions.md)。

- 了解[如何创建用户定义函数](./how-to-user-defined-functions.md)。

- 了解[如何调试用户定义函数](./how-to-diagnose-user-defined-functions.md)。
