---
title: Azure 数字孪生的常用查询模式 | Microsoft Docs
description: 了解 Azure 数字孪生管理 API 的常用查询模式。
author: dsk-2015
manager: philmea
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 1/7/2019
ms.author: dkshir
ms.openlocfilehash: ff8638042fa10c939ff9c5fa7af99a660fcdc753
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60924730"
---
# <a name="how-to-query-azure-digital-twins-apis-for-common-tasks"></a>如何在常见任务中查询 Azure 数字孪生 API

本文介绍可帮助你针对 Azure 数字孪生实例执行常见方案的查询模式。 本文假设数字孪生实例已运行。 可以使用任何 REST 客户端，例如 Postman。 

[!INCLUDE [digital-twins-management-api](../../includes/digital-twins-management-api.md)]


## <a name="queries-for-spaces-and-types"></a>针对空间和类型的查询

本部分演示用于获取有关预配空间的详细信息的示例查询。 使用示例查询发出经过身份验证的 GET HTTP 请求（请将占位符替换为你的设置中的值）。 

- 获取作为根节点的空间。

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?$filter=ParentSpaceId eq null
    ```

- 按名称获取空间，并包含设备、传感器、计算值和传感器值。 

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?name=Focus Room A1&includes=fullpath,devices,sensors,values,sensorsvalues
    ```

- 获取空间及其设备/传感器信息，其父级是给定的空间 ID，且[相对于给定空间](how-to-navigate-apis.md#api-navigation)，则位于级别 2 到 5。 

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?spaceId=YOUR_SPACE_ID&includes=fullpath,devices,sensors,values,sensorsvalues&traverse=Down&minLevel=1&minRelative=true&maxLevel=5&maxRelative=true
    ```

- 获取具有给定 ID 的空间，并包含计算值和传感器值。

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?ids=YOUR_SPACE_ID&includes=Values,sensors,SensorsValues
    ```

- 获取特定空间的属性键。

    ```plaintext
    YOUR_MANAGEMENT_API_URL/propertykeys?spaceId=YOUR_SPACE_ID
    ```

- 获取属性键名为 *AreaInSqMeters*、属性值为 30 的空间。 还可以执行字符串操作，例如，获取包含属性键 `name = X contains Y` 的空间。

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?propertyKey=AreaInSqMeters&propertyValue=30
    ```

- 获取包含 *Temperature* 的所有名称，以及关联的依赖项和本体。

    ```plaintext
    YOUR_MANAGEMENT_API_URL/types?names=Temperature&includes=space,ontologies,description,fullpath
    ```


## <a name="queries-for-roles-and-role-assignments"></a>针对角色和角色分配的查询

本部分演示一些用于获取有关角色及其分配的详细信息的查询。 

- 获取 Azure 数字孪生支持的所有角色。

    ```plaintext
    YOUR_MANAGEMENT_API_URL/system/roles
    ```

- 获取数字孪生实例中的所有角色分配。 

    ```plaintext
    YOUR_MANAGEMENT_API_URL/roleassignments?path=/&traverse=down
    ```

- 获取特定路径中的角色分配。

    ```plaintext
    YOUR_MANAGEMENT_API_URL/roleassignments?path=/A_SPATIAL_PATH
    ```

## <a name="queries-for-devices"></a>查询设备

本部分通过一些示例来演示如何使用管理 API 获取有关设备的具体信息。 所有 API 调用需是经过身份验证的 GET HTTP 请求。

- 获取所有设备。

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices
    ```

- 查找所有设备状态。

    ```plaintext
    YOUR_MANAGEMENT_API_URL/system/devices/statuses
    ```

- 获取特定的设备。

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices/YOUR_DEVICE_ID
    ```

- 获取已附加到根空间的所有设备。

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?maxLevel=1
    ```

- 获取已附加到第 2 到第 4 级别空间的所有设备。

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?minLevel=2&maxLevel=4
    ```

- 获取已直接附加到特定空间 ID 的所有设备。

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID
    ```

- 获取已附加到特定空间及其下级的所有设备。

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Down
    ```

- 获取已附加某个空间的下级（不包括该空间）的所有设备。

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Down&minLevel=1&minRelative=true
    ```

- 获取已附加到某个空间的直属子级的所有设备。

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Down&minLevel=1&minRelative=true&maxLevel=1&maxRelative=true
    ```

- 获取已附加到某个空间的上级之一的所有设备。

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Up&maxLevel=-1&maxRelative=true
    ```

- 获取已附加到级别小于或等于 5 的空间下级的所有设备。

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Down&maxLevel=5
    ```

- 获取已附加到级别与 ID 为 *YOUR_SPACE_ID* 的空间相同的空间的所有设备。

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Span&minLevel=0&minRelative=true&maxLevel=0&maxRelative=true
    ```

- 获取设备的 IoT 中心设备连接字符串。

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices/YOUR_DEVICE_ID?includes=ConnectionString
    ```

- 获取具有给定硬件 ID 的设备，包括附加的传感器。

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?hardwareIds=YOUR_DEVICE_HARDWARE_ID&includes=sensors
    ```

- 获取特定数据类型（在本例中为 *Motion* 和 *Temperature*）的传感器。

    ```plaintext
    YOUR_MANAGEMENT_API_URL/sensors?dataTypes=Motion,Temperature
    ```

## <a name="queries-for-matchers-and-user-defined-functions"></a>针对匹配程序和用户定义的函数的查询 

- 获取所有预配的匹配程序及其 ID。

   ```plaintext
    YOUR_MANAGEMENT_API_URL/matchers
    ```

- 获取有关特定匹配程序的详细信息，包括其关联的空间和用户定义的函数。

    ```plaintext
    YOUR_MANAGEMENT_API_URL/matchers/YOUR_MATCHER_ID?includes=description, conditions, fullpath, userdefinedfunctions, space
    ```

- 根据传感器评估匹配程序，并启用日志记录用于调试。 此 HTTP GET 消息的返回值告知匹配程序和传感器是否属于该数据类型。 

   ```plaintext
    YOUR_MANAGEMENT_API_URL/matchers/YOUR_MATCHER_ID/evaluate/YOUR_SENSOR_ID?enableLogging=true
    ```

- 获取用户定义的函数的 ID 

   ```plaintext
    YOUR_MANAGEMENT_API_URL/userdefinedfunctions
    ```

- 获取特定用户定义的函数的内容 

   ```plaintext
    YOUR_MANAGEMENT_API_URL/userdefinedfunctions/YOUR_USER_DEFINED_FUNCTION_ID/contents
    ```


## <a name="queries-for-users"></a>针对用户的查询

本部分演示一些用于管理 Azure 数字孪生中的用户的示例 API 查询。 发出 HTTP GET 请求（请将占位符替换为你的设置中的值）。 

- 获取所有用户。 

    ```plaintext
    YOUR_MANAGEMENT_API_URL/users
    ```

- 获取特定的用户。

    ```plaintext
    YOUR_MANAGEMENT_API_URL/users/ANY_USER_ID
    ```

## <a name="next-steps"></a>后续步骤

要了解如何使用管理 API 进行身份验证，请阅读[使用 API 进行身份验证](./security-authenticating-apis.md)。

若要查看所有 API 终结点，请参阅[如何使用数字孪生 Swagger](./how-to-use-swagger.md)。
