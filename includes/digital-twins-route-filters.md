---
author: baanders
description: Azure 数字孪生路由筛选器选项的包含文件
ms.service: digital-twins
ms.topic: include
ms.date: 8/3/2020
ms.author: baanders
ms.openlocfilehash: a1098088a38b23ec1074434e5424e261e60bcd55
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2020
ms.locfileid: "91779494"
---
| 筛选器名称 | 说明 | 筛选器文本架构 | 支持的值 | 
| --- | --- | --- | --- |
| True/False | 允许创建不带筛选的路由，或禁用路由，以便不发送事件 | `<true/false>` | `true` = 启用无筛选的路由 <br> `false` = 已禁用路由 |
| 类型 | 通过数字克隆实例流动的[事件类型](../articles/digital-twins/concepts-route-events.md#types-of-event-messages) | `type = '<eventType>'` | 下面是可能的事件类型值： <br>`Microsoft.DigitalTwins.Twin.Create` <br> `Microsoft.DigitalTwins.Twin.Delete` <br> `Microsoft.DigitalTwins.Twin.Update`<br>`Microsoft.DigitalTwins.Relationship.Create`<br>`Microsoft.DigitalTwins.Relationship.Update`<br> `Microsoft.DigitalTwins.Relationship.Delete` <br> `microsoft.iot.telemetry`  |
| 源 | Azure 数字孪生实例的名称 | `source = '<hostname>'`| 下面是可能的主机名值： <br> **对于通知**： `<yourDigitalTwinInstance>.<yourRegion>.azuredigitaltwins.net` <br> **对于遥测**： `<yourDigitalTwinInstance>.<yourRegion>.azuredigitaltwins.net/digitaltwins/<twinId>`|
| 使用者 | 上述事件源上下文中事件的说明 | `subject = '<subject>'` | 下面是可能的主题值： <br>**对于通知**：使用者为 `<twinid>` <br> 或主题的 URI 格式，由多个部件或 Id 唯一标识：<br>`<twinid>/relationships/<relationshipid>`<br> **对于遥测**：主体是组件路径 (如果遥测数据是从克隆的组件发出的) （如） `comp1.comp2` 。 如果未从组件发出遥测数据，则其 "使用者" 字段为空。 |
| 数据架构 | DTDL 模型 ID | `dataschema = '<model-dtmi-ID>'` | **对于遥测**：数据架构是克隆或发出遥测数据的组件的模型 ID。 例如： `dtmi:example:com:floor4;2` <br>**对于通知**：可在通知正文中访问数据架构，网址为 `$body.$metadata.$model`|
| 内容类型 | 数据值的内容类型 | `datacontenttype = '<contentType>'` | 内容类型为 `application/json` |
| 规范版本 | 你使用的事件架构的版本 | `specversion = '<version>'` | 版本必须为 `1.0` 。 这表示 CloudEvents 架构版本1。0 |
| 通知正文 | 引用通知字段中的任何属性 `data` | `$body.<property>` | 有关通知的示例，请参阅 [*操作方法：了解事件数据*](https://docs.microsoft.com/azure/digital-twins/how-to-interpret-event-data) 。 可以使用引用此字段中的任何属性 `data``$body`

支持将以下数据类型作为对上述数据的引用返回的值：

| 数据类型 | 示例 |
|-|-|-|
|**字符串**| `STARTS_WITH($body.$metadate.$model, 'dtmi:example:com:floor')` <br> `CONTAINS(subject, '<twinID>')`|
|**整数**|`$body.errorCode > 200`|
|**双精度**|`$body.temperature <= 5.5`|
|**型**|`$body.poweredOn = true`|
|**Null**|`$body.prop != null`|

定义路由筛选器时支持以下运算符：

|系列|运算符|示例|
|-|-|-|
|逻辑|AND、OR ( ) |`(type != 'microsoft.iot.telemetry' OR datacontenttype = 'application/json') OR (specversion != '1.0')`|
|比较|<、<=、>、>=、=、！ =|`$body.temperature <= 5.5`

定义路由筛选器时支持以下函数：

|函数|说明|示例|
|--|--|--|
|STARTS_WITH (x、y) |如果值以字符串开头，则返回 true `x` `y` 。|`STARTS_WITH($body.$metadata.$model, 'dtmi:example:com:floor')`|
|ENDS_WITH (x、y)  | 如果值以字符串结尾，则返回 true `x` `y` 。|`ENDS_WITH($body.$metadata.$model, 'floor;1')`|
|CONTAINS(x,y)| 如果值包含字符串，则返回 true `x` `y` 。|`CONTAINS(subject, '<twinID>')`|

实现或更新筛选器时，更改可能需要几分钟才会反映在数据管道中。
