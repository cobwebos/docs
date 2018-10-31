---
title: Azure 数字孪生中的数据处理和用户定义的函数 | Microsoft Docs
description: 概述 Azure 数字孪生中的数据处理、匹配程序和用户定义的函数
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/08/2018
ms.author: alinast
ms.openlocfilehash: b7561848ffd0158e22e97530774112dcee2a9864
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2018
ms.locfileid: "49323686"
---
# <a name="data-processing-and-user-defined-functions"></a>数据处理和用户定义的函数

Azure 数字孪生提供高级计算功能。 开发人员可以针对传入的遥测消息定义和运行自定义函数，以将事件发送到预定义的终结点。

## <a name="data-processing-flow"></a>数据处理流

一旦设备将遥测数据发送到数字孪生，开发人员就可以分四个阶段处理数据：_验证_、_匹配_、_计算_和_分派_：

![数字孪生数据处理流][1]

1. _验证_阶段将传入的遥测消息转换为通俗易懂的 [`data transfer object`](https://en.wikipedia.org/wiki/Data_transfer_object) 格式。 此阶段还执行设备和传感器验证。
1. _匹配_阶段查找要运行的相应用户定义函数。 预定义的匹配程序将根据传入的遥测消息中的设备、传感器和空间信息查找用户定义的函数。
1. _计算_阶段运行在前一阶段匹配的用户定义函数。 这些函数可以读取和更新空间图形节点上的计算值，并发出自定义通知。
1. _分派_阶段将计算阶段的所有自定义通知路由到图形中定义的终结点。

## <a name="data-processing-objects"></a>数据处理对象

Azure 数字孪生中的数据处理包括定义三个对象：_匹配程序_、_用户定义的函数_和_角色分配_：

![数字孪生数据处理流][2]

### <a name="matchers"></a>匹配程序

_匹配程序_定义一组条件，用于评估将根据传入的传感器遥测数据执行的操作。 这些用于确定匹配项的条件可以包括来自传感器、传感器父设备和传感器父空间的属性。 这些条件表示为根据 [JSON 路径](http://jsonpath.com/)进行的比较，如以下示例所述：

- 所有数据类型为 `Temperature` 的传感器。
- 在其端口中有 `01`。
- 属于扩展属性键 `Manufacturer` 设置为值 `GoodCorp` 的设备。
- 属于 `Venue` 类型的空间。
- 是父 `SpaceId``DE8F06CA-1138-4AD7-89F4-F782CC6F69FD` 的后代。

```JSON
{
  "SpaceId": "DE8F06CA-1138-4AD7-89F4-F782CC6F69FD",
  "Name": "My custom matcher",
  "Description": "All sensors of datatype Temperature with 01 in their port that belong to devices with the extended property key Manufacturer set to the value GoodCorp and that belong to spaces of type Venue that are somewhere below space Id DE8F06CA-1138-4AD7-89F4-F782CC6F69FD",
  "Conditions": [
    {
      "target": "Sensor",
      "path": "$.dataType",
      "value": "\"Temperature\"",
      "comparison": "Equals"
    },
    {
      "target": "Sensor",
      "path": "$.port",
      "value": "01",
      "comparison": "Contains"
    },
    {
      "target": "SensorDevice",
      "path": "$.properties[?(@.name == 'Manufacturer')].value",
      "value": "\"GoodCorp\"",
      "comparison": "Equals"
    },
    {
      "target": "SensorSpace",
      "path": "$.type",
      "value": "\"Venue\"",
      "comparison": "Equals"
    }
  ]
}
```

> [!IMPORTANT]
> - JSON 路径区分大小写。
> - JSON 有效负载与以下项返回的有效负载相同：
>   - 用于传感器的 `/sensors/{id}?includes=properties,types`。
>   - 用于传感器父设备的 `/devices/{id}?includes=properties,types,sensors,sensorsproperties,sensorstypes`。
>   - 用于传感器父空间的 `/spaces/{id}?includes=properties,types,location,timezone`。
> - 比较不区分大小写。

### <a name="user-defined-functions"></a>用户定义的函数

_用户定义的函数_（即 _UDF_）是在 Azure 数字孪生的独立环境中运行的自定义函数。 UDF 可以访问接收到的原始传感器遥测消息，以及空间图形和调度程序服务。 在图形中注册 UDF 后，必须创建匹配程序（详见上文）来指定何时运行 UDF。 比方说，当数字孪生从给定传感器收到新的遥测数据时，匹配的 UDF 可以计算最后几个传感器读数的移动平均值。

UDF 可以用 JavaScript 编写，并允许开发人员针对传感器遥测消息执行自定义代码片段。 也可以通过一些帮助程序方法在用户定义的执行环境中与图形进行交互。 使用 UDF，开发人员可以：

- 将传感器读数直接设置到图形中的传感器对象上。
- 根据图形中空间内的各种传感器读数执行操作。
- 在传入的传感器读数满足特定条件时创建通知。
- 在发出通知之前将图形元数据附加到传感器读数。

有关更多详细信息，请参阅[如何使用用户定义的函数](how-to-user-defined-functions.md)。

### <a name="role-assignment"></a>角色分配

UDF 的操作受数字孪生基于角色的访问控制的约束，以保护服务中的数据。 角色分配可确保给定的 UDF 具有与空间图形交互的适当权限。 例如，UDF 可能会尝试在给定空间下创建、读取、更新或删除图形数据。 当 UDF 向图形请求数据或尝试执行操作时，系统会检查 UDF 的访问级别。 有关详细信息，请参阅[基于角色的访问控制](security-create-manage-role-assignments.md)。

匹配程序有可能触发没有角色分配的 UDF。 在这种情况下，该 UDF 无法从图形中读取任何数据。

## <a name="next-steps"></a>后续步骤

若要详细了解如何将事件和遥测消息路由到其他 Azure 服务，请阅读[路由事件和消息](concepts-events-routing.md)。

若要详细了解如何创建匹配程序、用户定义的函数和角色分配，请阅读[用户定义函数使用指南](how-to-user-defined-functions.md)。

<!-- Images -->
[1]: media/concepts/digital-twins-data-processing-flow.png
[2]: media/concepts/digital-twins-user-defined-functions.png
