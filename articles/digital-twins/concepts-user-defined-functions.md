---
title: Azure 数字孪生中的数据处理和用户定义的函数 | Microsoft Docs
description: 概述 Azure 数字孪生中的数据处理、匹配程序和用户定义的函数。
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: alinast
ms.openlocfilehash: f4aa7e6660e3febdca6e0e5b1ad9f11bebaa48ea
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638456"
---
# <a name="data-processing-and-user-defined-functions"></a>数据处理和用户定义的函数

Azure 数字孪生提供高级计算功能。 开发人员可以针对传入的遥测消息定义和运行自定义函数，以将事件发送到预定义的终结点。

## <a name="data-processing-flow"></a>数据处理流

在设备将遥测数据发送到 Azure 数字孪生后，开发人员可以分四个阶段处理数据：*验证*、*匹配*、*计算*和*调度*。

![Azure 数字孪生数据处理流][1]

1. 验证阶段将传入的遥测消息转换为容易理解的[数据传输对象](https://docs.microsoft.com/aspnet/web-api/overview/data/using-web-api-with-entity-framework/part-5)格式。 此阶段还执行设备和传感器验证。
1. 匹配阶段查找要运行的相应用户定义函数。 预定义的匹配程序根据传入的遥测消息中的设备、传感器和空间信息查找用户定义的函数。
1. 计算阶段运行在前一阶段匹配的用户定义函数。 这些函数可以读取和更新空间图形节点上的计算值，并发出自定义通知。
1. 调度阶段将计算阶段的所有自定义通知路由到图形中定义的终结点。

## <a name="data-processing-objects"></a>数据处理对象

Azure 数字孪生中的数据处理包括定义三个对象：*匹配程序*、*用户定义的函数*和*角色分配*。

![Azure 数字孪生数据处理对象][2]

<div id="matcher"></div>

### <a name="matchers"></a>匹配程序

匹配程序定义一组条件，用于评估将根据传入的传感器遥测数据执行的操作。 用于确定匹配项的条件可能包括来自传感器、传感器父设备和传感器父空间的属性。 这些条件表示为根据 [JSON 路径](https://jsonpath.com/)进行的比较，如以下示例所述：

- 所有数据类型为 **Temperature** 的传感器，由转义的字符串值 `\"Temperature\"` 表示
- 其端口中包含 `01`
- 属于扩展属性键 **Manufacturer** 设置为转义字符串值 `\"GoodCorp\"` 的设备
- 属于转义字符串 `\"Venue\"` 指定的类型的空间
- 是父 **SpaceId** `DE8F06CA-1138-4AD7-89F4-F782CC6F69FD` 的后代

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

用户定义的函数是在隔离的 Azure 数字孪生环境中执行的自定义函数。 用户定义的函数有权访问它收到的原始传感器遥测消息。 用户定义的函数还有权访问空间图和调度程序服务。 在图形中注册用户定义的函数后，必须创建匹配程序（详见[上文](#matcher)）来指定何时执行该函数。 例如，当 Azure 数字孪生从给定传感器收到新的遥测数据时，匹配的用户定义的函数可以计算最后几个传感器读数的移动平均值。

用户定义的函数可以在 JavaScript 中编写。 帮助器方法可与用户定义的执行环境中的图形交互。 开发人员可以针对传感器遥测消息执行自定义的代码片段。 示例包括：

- 将传感器读数直接设置到图形中的传感器对象上。
- 根据图形中空间内的各种传感器读数执行操作。
- 在传入的传感器读数满足特定条件时创建通知。
- 在发出通知之前将图形元数据附加到传感器读数。

有关详细信息，请参阅[如何使用用户定义的函数](./how-to-user-defined-functions.md)。


#### <a name="examples"></a>示例

[数字孪生 C# 示例的 GitHub 存储库](https://github.com/Azure-Samples/digital-twins-samples-csharp/)包含了用户定义的函数的一些示例：
- [此函数](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/availabilityForTutorial.js)查找二氧化碳、运动和温度值，以确定是否存在这些值在范围内的房间。 [数字孪生教程](tutorial-facilities-udf.md)更详细地探究了此函数。 
- [此函数](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/multiplemotionsensors.js)从多个运动传感器查找数据，并且如果任何传感器都未检测到任何运动，则确定该空间可用。 通过执行文件的注释部分中提到的更改，可以轻松替换[快速入门](quickstart-view-occupancy-dotnet.md)或[教程](tutorial-facilities-setup.md)中使用的用户定义的函数。 



### <a name="role-assignment"></a>角色分配

用户定义的函数的操作受 Azure 数字孪生[基于角色的访问控制](./security-role-based-access-control.md)的约束，以保护服务中的数据。 角色分配定义哪些用户定义的函数具有与空间图形及其实体交互的适当权限。 例如，用户定义的函数可能具有在给定空间创建、读取、更新或删除图形数据的功能和权限。 当用户定义的函数向图形请求数据或尝试执行操作时，系统会检查用户定义的函数的访问级别。 有关详细信息，请参阅[基于角色的访问控制](./security-create-manage-role-assignments.md)。

匹配程序有可能触发没有角色分配的用户定义的函数。 在这种情况下，用户定义的函数无法从图形中读取任何数据。

## <a name="next-steps"></a>后续步骤

- 若要详细了解如何将事件和遥测消息路由到其他 Azure 服务，请阅读[路由事件和消息](./concepts-events-routing.md)。

- 若要详细了解如何创建匹配程序、用户定义的函数和角色分配，请阅读[用户定义函数使用指南](./how-to-user-defined-functions.md)。

- 请查看[用户定义函数客户端库参考文档](./reference-user-defined-functions-client-library.md)。

<!-- Images -->
[1]: media/concepts/digital-twins-data-processing-flow.png
[2]: media/concepts/digital-twins-user-defined-functions.png
