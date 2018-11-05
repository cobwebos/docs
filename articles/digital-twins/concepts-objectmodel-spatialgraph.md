---
title: 了解数字孪生对象模型和空间智能图 | Microsoft Docs
description: 使用 Azure 数字孪生对人员、位置和设备之间的关系进行建模
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: alinast
ms.openlocfilehash: c1d66e0b58567244f8c1406ee258c9311994ff20
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2018
ms.locfileid: "50215100"
---
# <a name="understanding-digital-twins-object-models-and-spatial-intelligence-graph"></a>了解数字孪生对象模型和空间智能图

Azure 数字孪生是一种 Azure IoT 服务，可提供物理环境及相关设备、传感器和人员的全面虚拟呈现形式。 它可通过将特定于域的概念组织成有用的模型来改进开发（这些模型本身就位于空间智能图中）。 此类概念可如实地为人员、空间和设备之间的关系和交互进行建模。

数字孪生对象模型介绍了特定于域的概念、类别和属性。 模型是由希望根据其特定需求定制解决方案的用户预定义的。 这些预定义的数字孪生对象模型共同构成“本体”。 智能建筑物的本体可描述地区、场所、楼层、办公室、区域、会议室和电话间。 能源网格本体可描述各种发电站、变电站、能源资源和客户。 这些数字孪生对象模型和本体支持自定义 Azure 数字孪生以适应不同的方案和需求。

转备好“数字孪生对象模型”和“本体”后，用户可以填充“空间图”。 空间图是与 IoT 解决方案相关的空间、设备和人员之间的多种关系的虚拟呈现形式。 下图显示了使用智能建筑物本体的空间图示例。

![数字孪生空间图建筑物][1]

<a id="model"></a>

空间图将空间、设备、传感器和用户汇集在一起。 每个对象都以模拟现实世界的形式连接在一起：43号场馆有四层楼，每层都有多个不同的区域。 用户与其工作站相关联，并且可以访问图形的某些部分。  例如，管理员有权更改空间图，而访问者可能仅有权查看某些建筑物数据。

## <a name="digital-twins-object-models"></a>数字孪生对象模型

数字孪生对象模型支持以下主要对象类别：

- “空间”是虚拟或实体位置，例如 `Tenant`、`Customer`、`Region`、`Venue`。
- 设备：虚拟或实体设备，例如 `AwesomeCompany Device`、`Raspberry Pi 3`。
- 传感器：检测事件的对象，例如 `AwesomeCompany Temperature Sensor`、`AwesomeCompany Presence Sensor`。
- 用户：标识在位者及其特征。

其他对象类别包括：

- 资源：附加到空间，通常表示空间图中的对象要使用的 Azure 资源，例如 `IoTHub`。
- Blob：附加到对象（如空间、设备、传感器和用户），用作具有 mime 类型和元数据的文件，例如 `maps`、`pictures`、`manuals`。
- 扩展类型：可扩展的枚举，用于扩充具有特定特征的实体，例如 `SpaceType`、`SpaceSubtype`。
- 本体：表示一组扩展的类型，例如 `Default`、`Building`、`BACnet`、`EnergyGrid`。
- 属性密钥和值：空间、设备、传感器和用户的自定义特征。 除了用作内置特征，它们还可以用于其他用途，例如将 `DeltaProcessingRefreshTime` 作为密钥和将 `10` 作为值。
- 角色：分配给空间图中的用户和设备的权限集，例如 `Space Administrator`、`User Administrator`、`Device Administrator`。
- 角色分配：角色与空间图中对象之间的关联，例如授予用户或服务主体管理空间图中空间的权限。
- 安全密钥存储：为给定空间对象下的层次结构中的所有设备提供安全密钥，以允许设备与数字孪生服务进行安全通信。
- 用户定义函数或 UDF：允许在空间图中进行可自定义的传感器遥测处理。 例如，UDF 可以设置传感器值、根据传感器读数执行自定义逻辑，还可以将输出设置为空间、将元数据附加到空间以及在满足预定义条件时发送通知。 目前，可以用 JavaScript 编写 UDF。
- 匹配器：确定将为给定的遥测消息执行哪些 UDF 的对象。
- 终结点：可以路由遥测消息和数字孪生事件的位置，例如 `Event Hub`、`Service Bus`、`Event Grid`。

<a id="graph"></a>

## <a name="spatial-intelligence-graph"></a>空间智能图

空间图是数字孪生对象模型中定义的空间、设备和人员的层次结构关系图。 空间图支持继承、筛选、遍历、可伸缩性和可扩展性。 用户可以使用 REST API 集合管理空间图并与其交互（参见下文）。

在订阅中部署数字孪生服务的用户将成为根节点的全局管理员，同时会自动授予对整个结构的完全访问权限。 然后，此用户可以使用空间 API 预配关系图中的空间。 可以使用设备 API 预配设备，可以使用传感器 API 预配传感器等等。我们还提供用于批量预配关系图的[开放源工具](https://github.com/Azure-Samples/digital-twins-samples-csharp)。

关系图继承适用于来源于父节点及其以下所有节点的权限和属性。 例如，当为给定节点上的用户分配角色时，该用户将拥有该角色对给定节点及其以下每个节点所拥有的权限。 此外，该节点下的所有节点将继承为给定节点定义的每个属性密钥和扩展类型。

关系图_筛选_使用户能够按 ID、名称、类型、子类型、父空间、关联空间、传感器数据类型、属性密钥和值、遍历、minLevel、maxLevel 和其他 OData 筛选器参数缩小请求结果的范围。

关系图遍历允许用户从深度和广度层面导航空间图。 对于深度，可以使用导航参数 traverse、minLevel、maxLevel 自上而下或自下而上遍历关系图。 对于广度，可以导航关系图以使同级节点直接连接到父空间或其后代之一。 查询对象时，可以使用 GET API 的 *includes* 参数获取与该对象有关系的所有相关对象。

Azure 数字孪生确保关系图具有可伸缩性，以便它能处理实际工作负荷。 数字孪生可以用于表示房地产、基础设施、设备、传感器、遥测等大型项目组合。

关系图可扩展性允许用户使用新类型和本体自定义基础数字孪生对象模型。 还可以使用可扩展的属性和值扩充数字孪生的数据。

### <a name="spatial-intelligence-graph-management-apis"></a>空间智能图管理 API

从 [Azure门户](https://portal.azure.com)部署 Azure 数字孪生后，会自动生成管理 API 的 [Swagger](https://swagger.io/tools/swagger-ui/) URL，该 URL 将以以下格式显示在 Azure 门户的概述部分中：

```plaintext
https://yourInstanceName.yourLocation.azuresmartspaces.net/management/swagger
```

| 自定义属性名称 | 替换为 |
| --- | --- |
| *yourInstanceName* | Azure 数字孪生实例的名称 |
| *yourLocation* | 托管实例的服务器区域 |

 可在下图中看到使用的完整 URL 格式：

![数字孪生门户管理 API][2]

有关使用空间智能图的详细信息，请访问 Azure 数字孪生管理 API 非公开预览。

> [!TIP]
> 我们提供了 Swagger 非公开预览版来演示 API 功能集。
> 它托管在 [ docs.westcentralus.azuresmartspaces.net/management/swagger](https://docs.westcentralus.azuresmartspaces.net/management/swagger) 中。

详细了解[如何使用 Swagger](how-to-use-swagger.md)

所有 API 调用必须使用 [OAuth](https://docs.microsoft.com/azure/active-directory/develop/v1-protocols-oauth-code) 进行身份验证。 API 遵循 [Microsoft REST API 准则约定](https://github.com/Microsoft/api-guidelines/blob/master/Guidelines.md)。 大多数返回集合的 API 支持 [OData](http://www.odata.org/getting-started/basic-tutorial/#queryData) 系统查询选项。

## <a name="next-steps"></a>后续步骤

要了解有关设备连接以及如何将遥测消息发送到 Azure 数字孪生服务的信息，请参阅 [Azure 数字孪生设备连接与遥测数据入口](concepts-device-ingress.md)。

要了解管理 API 限制和节流的信息，请参阅 [Azure 数字孪生 API 管理和限制](concepts-service-limits.md)。

<!-- Images -->
[1]: media/concepts/digital-twins-spatial-graph-building.png
[2]: media/concepts/digital-twins-spatial-graph-management-api-url.png
