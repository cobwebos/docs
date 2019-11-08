---
title: 在 Azure 数字孪生 API 中导航 | Microsoft Docs
description: 了解 Azure 数字孪生管理 API 的常用查询模式。
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/27/2019
ms.openlocfilehash: b0c3d72085e008524811f479ccbdeec013e26c4c
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2019
ms.locfileid: "73822249"
---
# <a name="how-to-use-azure-digital-twins-management-apis"></a>如何使用 Azure 数字孪生管理 API

Azure 数字孪生管理 API 为 IoT 应用提供强大的功能。 本文介绍如何在 API 结构中导航。  

## <a name="api-summary"></a>API 摘要

以下列表显示了数字孪生 API 的组件。

* [/spaces](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Spaces)：这些 api 与安装中的物理位置交互。 这些 API 可帮助你创建、删除和管理物理位置的、[空间图形](concepts-objectmodel-spatialgraph.md#spatial-intelligence-graph)形式的数字映射。

* [/devices](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Devices)：这些 api 与安装中的设备交互。 这些设备可以管理一个或多个传感器。 例如，设备可能是手机、Raspberry Pi 传感器 Pod、Lora 网关，等等。

* [/sensors](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Sensors)：这些 api 可帮助你与设备和物理位置相关的传感器通信。 传感器记录并发送环境值，然后，这些值可用于操纵空间环境。  

* [/resources](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Resources)：这些 api 可帮助你设置数字孪生实例的资源（例如 IoT 中心）。

* [/types](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Types)：通过这些 api，可以将扩展类型与数字孪生对象关联，以将特定特征添加到这些对象。 使用这些类型可以轻松在 UI 中筛选和分组对象，以及用于处理遥测数据的自定义函数。 扩展类型的示例包括 *DeviceType*、*SensorType*、*SensorDataType*、*SpaceType*、*SpaceSubType*、*SpaceBlobType*、*SpaceResourceType*，等等。

* [/ontologies](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/Ontologies)：这些 api 可帮助你管理本体论，这是扩展类型的集合。 本体根据它们表示的物理空间提供对象类型的名称。 例如，*BACnet* 本体为 *sensortypes*、*datatypes*、*datasubtypes* 和 *dataunittypes* 提供特定的名称。 本体由服务托管和创建。 用户可以加载和卸载本体。 加载某个本体时，它的所有关联类型名称都将启用，并可在空间图形进行预配。 

* [/propertyKeys](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/PropertyKeys)：可以使用这些 api 为*空间*、*设备*、*用户*和*传感器*创建自定义属性。 这些属性以键/值对的形式创建。 可以通过设置这些属性的 *PrimitiveDataType* 来为其定义数据类型。 例如，可为传感器定义名为 *BasicTemperatureDeltaProcessingRefreshTime*、类型为 *uint* 的属性，然后为每个传感器分配此属性的值。 还可以在创建属性时为这些值添加约束（例如 *Min* 和 *Max* 范围）以及允许的值（例如 *ValidationData*）。

* [/matchers](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/Matchers)：这些 api 允许你指定要从传入设备数据评估的条件。 有关详细信息，请参阅[此文](concepts-user-defined-functions.md#matchers)。 

* [/userDefinedFunctions](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/UserDefinedFunctions)：通过这些 api，你可以创建、删除或更新自定义函数，该函数将在由*匹配程序*定义的条件处理来自安装程序的数据时执行。 有关这些自定义函数（也称为“用户定义的函数”）的详细信息，请参阅[此文](concepts-user-defined-functions.md#user-defined-functions)。 

* [/endpoints](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/Endpoints)：通过这些 api，你可以创建终结点，以便数字孪生解决方案可以与其他 Azure 服务通信，以便进行数据存储和分析。 有关详细信息，请阅读[此文](concepts-events-routing.md)。 

* [/keyStores](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/KeyStores)：通过这些 api，你可以管理共享空间的安全密钥存储。 这些存储可以保存安全密钥集合，并可让你轻松检索最新的有效密钥。

* [/users](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Users)：通过这些 api，可以将用户与共享空间相关联，以便在需要时找到这些个人。 

* [/system](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/System)：这些 api 允许管理系统范围的设置，例如，默认的空间和传感器类型。 

* [/roleAssignments](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/RoleAssignments)：这些 api 允许将角色关联到实体，如用户 ID、用户定义的函数 ID 等。每个角色分配都包含要关联的实体的 ID、实体类型、要关联的角色的 ID、租户 ID 以及定义实体可通过该关联访问的资源上限的路径。 有关详细信息，请阅读[此文](security-role-based-access-control.md)。


## <a name="api-navigation"></a>API 导航

数字孪生 API 支持使用以下参数在整个空间图形中进行筛选和导航：

- **spaceId**： API 将按给定的空间 ID 对结果进行筛选。 此外，布尔标志 **useParentSpace** 适用于 [/spaces](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Spaces) API。该标志指示给定的空间 ID 引用父空间而不是当前空间。 

- **minLevel**和**maxLevel**： Root 空格被视为在级别1。 包含第 *n* 级别父空间的空间位于第 *n+1* 级别。 设置这些值后，可在特定的级别筛选结果。 这些值在设置后是非独占性的。 设备、传感器和其他对象被认为与其最靠近的空间位于同一级别。 若要获取给定级别处的所有对象，请将 **minLevel** 和 **maxLevel** 设置为相同的值。

- **minRelative**和**maxRelative**：给定这些筛选器后，对应的级别相对于给定空间 ID 的级别：
   - 相对级别 *0* 与给定空间 ID 的级别相同。
   - 相对级别 *1* 表示空间级别与给定空间 ID 子级的级别相同。 相对级别 *n* 表示空间比指定空间要低 *n* 个级别。
   - 相对级别 *-1* 表示空间级别与指定的空间的父空间级别相同。

- **遍历**：允许从给定的空间 ID 按以下值指定的任一方向进行遍历。
   - **None**：此默认值筛选到给定的空间 ID。
   - **Down**：按给定的空间 ID 及其子代筛选。 
   - **向上**：按给定的空间 ID 及其上级进行筛选。 
   - **跨度**：这将筛选空间关系图的水平部分，其级别与给定空间 ID 相同。 这需要将 **minRelative** 或 **maxRelative** 设置为 true。 


### <a name="examples"></a>示例

以下列表显示了在 [/devices](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Devices) API 中导航的一些示例。 请注意，占位符 `YOUR_MANAGEMENT_API_URL` 引用数字孪生 API 的 `https://YOUR_INSTANCE_NAME.YOUR_LOCATION.azuresmartspaces.net/management/api/v1.0/` 格式 URI，其中，`YOUR_INSTANCE_NAME` 是 Azure 数字孪生实例的名称，`YOUR_LOCATION` 是实例的托管区域。

- `YOUR_MANAGEMENT_API_URL/devices?maxLevel=1` 返回附加到根空间的所有设备。
- `YOUR_MANAGEMENT_API_URL/devices?minLevel=2&maxLevel=4` 返回附加到第 2、3 或 4 级别空间的所有设备。
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId` 返回直接附加到 mySpaceId 的所有设备。
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down` 返回附加到 mySpaceId 或其下级之一的所有设备。
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down&minLevel=1&minRelative=true` 返回附加到 mySpaceId 下级（不包括 mySpaceId）的所有设备。
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down&minLevel=1&minRelative=true&maxLevel=1&maxRelative=true` 返回附加到 mySpaceId 的直属子级的所有设备。
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Up&maxLevel=-1&maxRelative=true` 返回附加到 mySpaceId 的上级之一的所有设备。
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down&maxLevel=5` 返回附加到级别小于或等于 5 的 mySpaceId 下级的所有设备。
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Span&minLevel=0&minRelative=true&maxLevel=0&maxRelative=true` 返回附加到级别与 mySpaceId 相同的空间的所有设备。


## <a name="odata-support"></a>OData 支持

返回集合的大多数 API（例如，针对 /spaces 的 GET 调用）都支持泛型 [OData](https://www.odata.org/getting-started/basic-tutorial/#queryData) 系统查询选项的以下子集：  

* **$filter**
* **$orderby** 
* **$top**
* **$skip** - 如果你想要显示整个集合，应在单个调用中以完整集的形式发出此请求，然后在应用程序中执行分页。 

> [!NOTE]
> 目前尚不支持某些 OData 选项（如查询选项 **$count**、 **$expand**和 **$search**）。

### <a name="examples"></a>示例

下面的列表描述了具有有效 OData 语法的几个查询：

- `YOUR_MANAGEMENT_API_URL/devices?$top=3&$orderby=Name desc`
- `YOUR_MANAGEMENT_API_URL/keystores?$filter=endswith(Description,'space')`
- `YOUR_MANAGEMENT_API_URL/devices?$filter=TypeId eq 2`
- `YOUR_MANAGEMENT_API_URL/resources?$filter=StatusId ne 1`
- `YOUR_MANAGEMENT_API_URL/users?$top=4&$filter=endswith(LastName,'k')&$orderby=LastName`
- `YOUR_MANAGEMENT_API_URL/spaces?$orderby=Name desc&$top=3&$filter=substringof('Floor',Name)`
 
## <a name="next-steps"></a>后续步骤

若要了解一些常用的 API 查询模式，请参阅[如何在常见任务中查询 Azure 数字孪生 API](./how-to-query-common-apis.md)。

若要了解有关 API 终结点的详细信息，请参阅[如何使用数字孪生 Swagger](./how-to-use-swagger.md)。

若要查看 OData 语法和可用的比较运算符，请阅读[Azure 认知搜索中的 odata 比较运算符](../search/search-query-odata-comparison-operators.md)。
