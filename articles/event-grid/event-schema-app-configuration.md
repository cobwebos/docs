---
title: Azure 事件网格 Azure 应用程序配置事件架构
description: 介绍为 Azure 事件网格的 Azure 应用程序配置事件提供的属性
services: event-grid
author: jimmyca
ms.service: event-grid
ms.topic: reference
ms.date: 05/30/2019
ms.author: jimmyca
ms.openlocfilehash: fe0274f723692eea3cfd25cc0e9e146b35dce2ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "66735776"
---
# <a name="azure-event-grid-event-schema-for-azure-app-configuration"></a>Azure 应用程序配置的 Azure 事件网格事件架构

本文提供 Azure 应用程序配置事件的属性和架构。 有关事件架构的简介，请参阅 [Azure 事件网格事件架构](event-schema.md)。

有关示例脚本和教程的列表，请参阅 [Azure 应用程序配置事件源](event-sources.md#app-configuration)。

## <a name="available-event-types"></a>可用事件类型

Azure 应用程序配置会发出以下事件类型：

| 事件类型 | 描述 |
| ---------- | ----------- |
| Microsoft.AppConfiguration.KeyValueModified | 创建或替换键/值时引发。 |
| Microsoft.AppConfiguration.KeyValueDeleted | 删除键/值时引发。 |

## <a name="example-event"></a>示例事件

以下示例显示键/值修改事件的架构： 

```json
[{
  "id": "84e17ea4-66db-4b54-8050-df8f7763f87b",
  "topic": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/microsoft.appconfiguration/configurationstores/contoso",
  "subject": "https://contoso.azconfig.io/kv/Foo?label=FizzBuzz",
  "data": {
    "key": "Foo",
    "label": "FizzBuzz",
    "etag": "FnUExLaj2moIi4tJX9AXn9sakm0"
  },
  "eventType": "Microsoft.AppConfiguration.KeyValueModified",
  "eventTime": "2019-05-31T20:05:03Z",
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

键/值删除事件的架构与此类似： 

```json
[{
  "id": "84e17ea4-66db-4b54-8050-df8f7763f87b",
  "topic": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/microsoft.appconfiguration/configurationstores/contoso",
  "subject": "https://contoso.azconfig.io/kv/Foo?label=FizzBuzz",
  "data": {
    "key": "Foo",
    "label": "FizzBuzz",
    "etag": "FnUExLaj2moIi4tJX9AXn9sakm0"
  },
  "eventType": "Microsoft.AppConfiguration.KeyValueDeleted",
  "eventTime": "2019-05-31T20:05:03Z",
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```
 
## <a name="event-properties"></a>事件属性

事件具有以下顶级数据：

| properties | 类型 | 说明 |
| -------- | ---- | ----------- |
| 主题 | 字符串 | 事件源的完整资源路径。 此字段不可写入。 事件网格提供此值。 |
| subject | 字符串 | 事件主题的发布者定义路径。 |
| eventType | 字符串 | 此事件源的一个注册事件类型。 |
| EventTime | 字符串 | 基于提供程序 UTC 时间的事件生成时间。 |
| id | 字符串 | 事件的唯一标识符。 |
| data | 对象 (object) | 应用配置事件数据。 |
| dataVersion | 字符串 | 数据对象的架构版本。 发布者定义架构版本。 |
| metadataVersion | 字符串 | 事件元数据的架构版本。 事件网格定义顶级属性的架构。 事件网格提供此值。 |

数据对象具有以下属性：

| properties | 类型 | 说明 |
| -------- | ---- | ----------- |
| key | 字符串 | 已修改或已删除的键/值的键。 |
| label | 字符串 | 已修改或已删除的键/值的标签（如果有）。 |
| etag | 字符串 | 对于 `KeyValueModified`，为新键/值的 etag。 对于 `KeyValueDeleted`，为已删除的键/值的 etag。 |
 
## <a name="next-steps"></a>后续步骤

* 有关 Azure 事件网格的简介，请参阅[什么是事件网格？](overview.md)
* 有关创建 Azure 事件网格订阅的详细信息，请参阅[事件网格订阅架构](subscription-creation-schema.md)。
* 有关使用 Azure 应用配置事件的简介，请参阅[路由 Azure 应用配置事件 - Azure CLI](../azure-app-configuration/howto-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json)。 