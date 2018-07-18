---
title: 配置连接的工厂拓扑 | Microsoft Docs
description: 如何配置连接的工厂解决方案加速器的拓扑。
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 12/12/2017
ms.author: dobett
ms.openlocfilehash: c6c5e27dad7f80a329edbd8fbcb95647dc4cd15a
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34626713"
---
# <a name="configure-the-connected-factory-solution-accelerator"></a>配置连接的工厂解决方案加速器

连接的工厂解决方案加速器显示了虚拟公司 Contoso 的模拟仪表板。 此公司在全球许多地方都设有工厂。

本文以 Contoso 为例，介绍如何配置连接的工厂解决方案的拓扑。

## <a name="simulated-factories-configuration"></a>模拟工厂配置

每个 Contoso 工厂具有由三个工作站组成的生产线。 每个工作站是具有特定角色的真正 OPC UA 服务器：

* 组装工作站
* 测试工作站
* 打包工作站

这些 OPC UA 服务器具有 OPC UA 节点，并且 [OPC 发布服务器](https://github.com/Azure/iot-edge-opc-publisher)会将这些节点的值发送到连接的工厂。 这包括：

* 当前的操作状态，例如当前的功耗。
* 生产信息，例如生产的产品数量。

可以使用仪表板将 Contoso 工厂拓扑从全局视图向下钻取至工作站级别视图。 连接的工厂仪表板实现了以下功能：

* 拓扑中每层的 OEE 和 KPI 图表数据的可视化效果。
* 工作站中 OPC UA 节点当前值的可视化效果。
* 从工作站级别到全局级别的 OEE 和 KPI 图表数据的聚合。
* 当值达到特定阈值时要执行的警报和操作的可视化效果。

## <a name="connected-factory-topology"></a>连接的工厂拓扑

工厂、生产线和工作站的拓扑是分层的：

* 全局级别具有作为子级的工厂节点。
* 工厂具有作为子级的生产线节点。
* 生产线具有作为子级的工作站节点。
* 工作站（OPC UA 服务器）具有作为子级的 OPC UA 节点。

拓扑中的每个节点都具有一组定义以下内容的常见属性：

* 拓扑节点的唯一标识符。
* 名称。
* 说明。
* 图像。
* 拓扑节点的子级。
* OEE 和 KPI 图表数据的最小值、目标值和最大值以及要执行的警报操作。

## <a name="topology-configuration-file"></a>拓扑配置文件

若要配置上一部分中列出的属性，连接的工厂解决方案将使用名为 [ContosoTopologyDescription.json](https://github.com/Azure/azure-iot-connected-factory/blob/master/WebApp/Contoso/Topology/ContosoTopologyDescription.json) 的配置文件。

可以在 `WebApp/Contoso/Topology` 文件夹的解决方案源代码中找到此文件。

以下代码片段显示 `ContosoTopologyDescription.json` 配置文件的大纲：

```json
{
  <global_configuration>,
  "Factories": [
    <factory_configuration>,
    "ProductionLines": [
      <production_line_configuration>,
      "Stations": [
        <station_configuration>,
        <more station_configurations>
      ],
      <more production_line_configurations>
    ]
    <more factory_configurations>
  ]
}
```

`<global_configuration>`、`<factory_configuration>`、`<production_line_configuration>` 和 `<station_configuration>` 的常见属性为：

* **Name**（字符串类型）

  定义一个描述性名称，此名称应该只为要在仪表板中显示的拓扑节点的一个词。

* **Description**（字符串类型）

  更详细地说明拓扑节点。

* **Image**（字符串类型）

  当拓扑节点的相关信息显示在仪表板中时，要显示的 WebApp 解决方案中的图像路径。

* **OeeOverall**、**OeePerformance**、**OeeAvailability**、**OeeQuality**、**Kpi1** 和 **Kpi2**（`<performance_definition>` 类型）

  这些属性定义用于生成警报的操作图表数据的最小值、目标值和最大值。 如果检测到警报，这些属性还定义要执行的操作。

`<factory_configuration>` 和 `<production_line_configuration>` 项具有一个属性：

* **Guid**（字符串类型）

  唯一标识拓扑节点。

`<factory_configuration>` 具有一个属性：

* **Location**（`<location_definition>` 类型）

  指定工厂所在的位置。

`<station_configuration>` 具有以下属性：

* **OpcUri**（字符串类型）

  此属性必须设置为 OPC UA 服务器的 OPC UA 应用程序 URI。
  由于 OPC UA 规范必须是全局唯一，因此，此属性用于标识工作站拓扑节点。

* **OpcNodes**，这是 OPC UA 节点的数组（`<opc_node_description>` 类型）。

`<location_definition>` 具有以下属性：

* **City**（字符串类型）

  与工厂所在位置最接近的城市名称

* **Country**（字符串类型）

  工厂所在位置的国家/地区

* **Latitude**（双精度类型）

  工厂所在位置的纬度

* **Longitude**（双精度类型）

  工厂所在位置的经度

`<performance_definition>` 具有以下属性：

* **Minimum**（双精度类型）

  值可以达到的阈值下限。 如果当前值低于此阈值，将生成警报。

* **Target**（双精度类型）

  理想的目标值。

* **Maximum**（双精度类型）

  值可以达到的阈值上限。 如果当前值高于此阈值，将生成警报。

* **MinimumAlertActions**（`<alert_action>` 类型）

  定义操作的集合，可执行这些操作以响应最小值警报。

* **MaximumAlertActions**（`<alert_action>` 类型）

  定义操作的集合，可执行这些操作以响应最大值警报。

`<alert_action`> 具有以下属性：

* **Type**（字符串类型）

  警报操作的类型。 以下是已知的类型：

  * **AcknowledgeAlert**：警报的状态应更改为“已确认”。
  * **CloseAlert**：同一类型的所有旧警报不应再显示在仪表板中。
  * **CallOpcMethod**：应该调用 OPC UA 方法。
  * **OpenWebPage**：应打开一个浏览器窗口以显示额外的上下文信息。

* **Description**（字符串类型）

  仪表板中显示的操作说明。

* **Parameter**（字符串类型）

  执行操作所需的参数。 此值因操作类型而定。

  * **AcknowledgeAlert**：不需要参数。
  * **CloseAlert**：不需要参数。
  * **CallOpcMethod**：以“父节点的 NodeId, 要调用的方法的 NodeId, OPC UA 服务器的 URI”格式调用的 OPC UA 方法的节点信息和参数。
  * **OpenWebPage**：要在浏览器窗口中显示的 URL。

`<opc_node_description>` 包含一个工作站（OPC UA 服务器）中的 OPC UA 节点的信息。 具有以下特征的节点也有效：表示没有任何现有的 OPC UA 节点，但在连接的工厂的计算逻辑中作为存储使用。 它具有以下属性：

* **NodeId**（字符串类型）

  工作站的（OPC UA 服务器的）地址空间中 OPC UA 节点的地址。 必须在 OPC UA 规范中为 NodeId 指定语法。

* **SymbolicName**（字符串类型）

  显示此 OPC UA 节点的值时，要显示在仪表板中的名称。

* **Relevance**（类型字符串的数组）

  指示与 OPC UA 节点值相关的 OEE 或 KPI 计算。 每个数组元素可以为以下值之一：

  * **OeeAvailability_Running**：此值与 OEE 可用性的计算相关。
  * **OeeAvailability_Fault**：此值与 OEE 可用性的计算相关。
  * **OeePerformance_Ideal**：此值与 OEE 性能的计算相关，并且通常是常量值。
  * **OeePerformance_Actual**：此值与 OEE 性能的计算相关。
  * **OeeQuality_Good**：此值与 OEE 质量的计算相关。
  * **OeeQuality_Bad**：此值与 OEE 质量的计算相关。
  * **Kpi1**：此值与 KPI1 的计算相关。
  * **Kpi2**：此值与 KPI2 的计算相关。

* **OpCode**（字符串类型）

  指示 OPC UA 节点的值在时序见解查询和 OEE/KPI 计算中的处理方式。 每个时序见解查询以特定的时间跨度为目标，此时间跨度是查询的参数并且提供一个结果。 OpCode 控制结果的计算方式，并且可为以下值之一：

  * **Diff**：时间跨度中最后一个值和第一个值之间的差异。
  * **Avg**：时间跨度中所有值的平均值。
  * **Sum**：时间跨度中所有值的总和。
  * **Last**：当前未使用。
  * **Count**：时间跨度中值的数量。
  * **Max**：时间跨度中的最大值。
  * **Min**：时间跨度中的最小值。
  * **Const**：此结果为属性 ConstValue 指定的值。
  * **SubMaxMin**：最大值和最小值之间的差异。
  * **Timespan**：时间跨度。

* **Units**（字符串类型）

  定义要在仪表板中显示的值的单位。

* **Visible**（布尔类型）

  控制值是否应显示在仪表板中。

* **ConstValue**（双精度类型）

  如果“OpCode”为“Const”，此属性则为节点的值。

* **Minimum**（双精度类型）

  如果当前值低于此值，则生成最小值警报。

* **Maximum**（双精度类型）

  如果当前值高于此值，则生成最大值警报。

* **MinimumAlertActions**（`<alert_action>` 类型）

  定义操作的集合，可执行这些操作以响应最小值警报。

* **MaximumAlertActions**（`<alert_action>` 类型）

  定义操作的集合，可执行这些操作以响应最大值警报。

在工作站级别，还可以看到模拟对象。 这些对象仅用于配置连接的工厂模拟，不应用于配置真正的拓扑。

## <a name="how-the-configuration-data-is-used-at-runtime"></a>配置数据在运行时的使用方式

可以根据属性的使用方式将配置文件中使用的所有属性分组成不同的类别。 这些类别为：

### <a name="visual-appearance"></a>可视外观

此类别中的属性定义连接的工厂仪表板的可视外观。 示例包括：

* 名称
* 说明
* 映像
* 位置
* 单位
* Visible

### <a name="internal-topology-tree-addressing"></a>内部拓扑树寻址

WebApp 维护包含所有拓扑节点信息的内部数据字典。 Guid 和 OpcUri 属性用作访问此字典的密钥，并且需要是唯一的。

### <a name="oeekpi-computation"></a>OEE/KPI 计算

连接的工厂模拟的 OEE/KPI 图表数据按以下内容参数化：

* 要包括在计算中的 OPC UA 节点值。
* 从遥测值计算图表数据的方式。

连接的工厂使用 http://oeeindustrystandard.oeefoundation.org 上发布的 OEE 公式。

工作站中的 OPC UA 节点对象支持在 OEE/KPI 计算中使用标记。 Relevance 属性指示 OPC UA 节点值应用于的 OEE/KPI 图表数据。 OpCode 属性定义如何在计算中包含值。

### <a name="alert-handling"></a>警报处理

连接的工厂支持基于最小/最大阈值的简单警报生成机制。 可以配置大量的预定义操作以响应这些警报。 此机制由以下属性控制：

* 最大值
* 最小值
* MaximumAlertActions
* MinimumAlertActions

## <a name="correlating-to-telemetry-data"></a>与遥测数据相关联

对于某些操作（例如可视化最后一个值或创建时序见解查询），WebApp 需要一个用于引入的遥测数据的寻址方案。 发送到连接的工厂的遥测数据还需要存储在内部数据结构中。 支持这些操作的两个属性位于工作站（OPC UA 服务器）和 OPC UA 节点级别：

* **OpcUri**

  标识遥测来自的（全局唯一）OPC UA 服务器。 在引入的消息中，此属性作为 ApplicationUri 发送。

* **NodeId**

  标识 OPC UA 服务器中的节点值。 此属性的格式必须在 OPC UA 规范中指定。 在引入的消息中，此属性作为 NodeId 发送。

查看[此](https://github.com/Azure/iot-edge-opc-publisher) GitHub 页面，详细了解如何使用 OPC 发布服务器将遥测数据引入连接的工厂。

## <a name="example-how-kpi1-is-calculated"></a>示例：如何计算 KPI1

`ContosoTopologyDescription.json` 文件中的配置控制 OEE/KPI 数据的计算方式。 以下示例显示此文件中的属性如何控制 KPI1 的计算。

在连接的工厂中，KPI1 用于测量最后一小时成功生产的产品数量。 连接的工厂模拟中的每个工作站（OPC UA 服务器）都提供一个 OPC UA 节点 (`NodeId: "ns=2;i=385"`)，它提供遥测数据来计算此 KPI。

此 OPC UA 节点的配置与以下代码片段类似：

```json
{
  "NodeId": "ns=2;i=385",
  "SymbolicName": "NumberOfManufacturedProducts",
  "Relevance": [ "Kpi1", "OeeQuality_Good" ],
  "OpCode": "SubMaxMin"
},
```

此配置支持使用时序见解查询此节点的遥测值。 时序见解查询检索：

* 值的数量。
* 最小值。
* 最大值。
* 所有值的平均值。
* 给定时间跨度中所有唯一的 OpcUri (ApplicationUri)、NodeId 对的所有值的总和。

NumberOfManufactureredProducts 节点值的一个特征是它只会增加。 若要计算在时间跨度中生产的产品数量，连接的工厂将使用 **OpCode** **SubMaxMin**。 此计算在时间跨度的开头检索最小值，在时间跨度的结尾检索最大值。

配置中的 OpCode 配置计算逻辑以计算最大值和最小值差异的结果。 然后这些结果将由下而上累计到根（全局）级别并显示在仪表板中。

## <a name="next-steps"></a>后续步骤

建议执行的下一步骤是了解如何[在 Windows 或 Linux 上为连接的工厂解决方案加速器部署网关](iot-accelerators-connected-factory-gateway-deployment.md)。