---
author: baanders
description: Azure 数字孪生路由筛选器选项的包含文件
ms.service: digital-twins
ms.topic: include
ms.date: 8/3/2020
ms.author: baanders
ms.openlocfilehash: c5b78d80b1aa958af50b81dc152b4a746ce85f70
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2020
ms.locfileid: "87902178"
---
| 筛选器名称 | 描述 | 筛选器文本架构 | 支持的值 | 
| --- | --- | --- | --- |
| True/False | 允许创建不带筛选的路由，或禁用路由，以便不发送事件 | `<true/false>` | `true`= 启用无筛选的路由 <br> `false`= 已禁用路由 |
| 类型 | 通过数字克隆实例流动的[事件类型](../articles/digital-twins/concepts-route-events.md#types-of-event-messages) | `type = '<eventType>'` | 下面是可能的事件类型值： <br>`Microsoft.DigitalTwins.Twin.Create` <br> `Microsoft.DigitalTwins.Twin.Delete` <br> `Microsoft.DigitalTwins.Twin.Update`<br>`Microsoft.DigitalTwins.Relationship.Create`<br>`Microsoft.DigitalTwins.Relationship.Update`<br> `Microsoft.DigitalTwins.Relationship.Delete` <br> `microsoft.iot.telemetry`  |
| 源 | Azure 数字孪生实例的名称 | `source = '<hostname>'`| 下面是可能的主机名值： <br> **对于通知**：`<yourDigitalTwinInstance>.<yourRegion>.azuredigitaltwins.net` <br> **对于遥测**：`<yourDigitalTwinInstance>.<yourRegion>.azuredigitaltwins.net/digitaltwins/<twinId>`|
| 使用者 | 上述事件源上下文中事件的说明 | `subject = '<subject>'` | 下面是可能的主题值： <br>**对于通知**：使用者为`<twinid>` <br> 或主题的 URI 格式，由多个部件或 Id 唯一标识：<br>`<twinid>/relationships/<relationshipid>`<br> **对于遥测**：主体是组件路径 (如果遥测数据是从克隆的组件发出的) （如） `comp1.comp2` 。 如果未从组件发出遥测数据，则其 "使用者" 字段为空。 |
| 数据架构 | DTDL 模型 ID | `dataschema = '<model-dtmi-ID>'` | **对于遥测**：数据架构是克隆或发出遥测数据的组件的模型 ID。 例如 `dtmi:example:com:floor4;2` <br>**对于通知**：不支持数据架构|
| 内容类型 | 数据值的内容类型 | `datacontenttype = '<contentType>'` | 内容类型为`application/json` |
| 规范版本 | 你使用的事件架构的版本 | `specversion = '<version>'` | 版本必须为 `1.0` 。 这表示 CloudEvents 架构版本1。0 |

还可以使用以下操作合并筛选器：

| 筛选器名称 | 筛选器文本架构 | 示例 | 
| --- | --- | --- |
| AND/OR | `<filter1> AND <filter2>` | `type != 'microsoft.iot.telemetry' AND datacontenttype = 'application/json'` |
| AND/OR | `<filter1> OR <filter2>` | `type != 'microsoft.iot.telemetry' OR datacontenttype = 'application/json'` |
| 嵌套操作 | `(<Comparison1>) AND (<Comparison2>)` | `(type != 'microsoft.iot.telemetry' OR datacontenttype = 'application/json') OR (specversion != '1.0')` |

> [!NOTE]
> 预览期间，仅支持字符串相等性 (=、！ =) 。

实现或更新筛选器时，更改可能需要几分钟才会反映在数据管道中。
