---
title: "Azure 事件网格订阅架构"
description: "介绍用于通过 Azure 事件网格订阅到事件的属性。"
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 01/30/2018
ms.author: babanisa
ms.openlocfilehash: a915473c67a7577582837b56d1a9ccec4d21c461
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2018
---
# <a name="event-grid-subscription-schema"></a>事件网格订阅架构

若要创建一个事件网格订阅，将请求发送到创建事件订阅操作。 使用以下格式：

```
PUT /subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/{resource-provider}/{resource-type}/{resource-name}/Microsoft.EventGrid/eventSubscriptions/{event-type-definitions}?api-version=2018-01-01
``` 

例如，若要在名为 `examplegroup` 的资源组中创建名为 `examplestorage` 的存储帐户的事件订阅，请使用以下格式：

```
PUT /subscriptions/{subscription-id}/resourceGroups/examplegroup/providers/Microsoft.Storage/storageaccounts/examplestorage/Microsoft.EventGrid/eventSubscriptions/{event-type-definitions}?api-version=2018-01-01
``` 

本文介绍请求正文的属性和架构。
 
## <a name="event-subscription-properties"></a>事件订阅属性

| 属性 | Type | 说明 |
| -------- | ---- | ----------- |
| 目标 | 对象 | 定义终结点的对象。 |
| filter | 对象 | 用于筛选事件类型的可选字段。 |

### <a name="destination-object"></a>目标对象

| 属性 | Type | 说明 |
| -------- | ---- | ----------- |
| endpointType | 字符串 | 订阅（webhook/HTTP、事件中心或队列）的终结点类型。 | 
| endpointUrl | 字符串 | 此事件订阅中的事件的目标 URL。 | 

### <a name="filter-object"></a>筛选器对象

| 属性 | Type | 说明 |
| -------- | ---- | ----------- |
| includedEventTypes | 数组 | 当事件消息中的事件类型与这些事件类型名称之一完全匹配时匹配。 当事件名称与事件源的已注册事件类型名称不匹配时，将引发错误。 默认匹配所有事件类型。 |
| subjectBeginsWith | 字符串 | 事件消息中使用者字段的前缀匹配筛选器。 默认或空字符串匹配所有类型。 | 
| subjectEndsWith | 字符串 | 事件消息中使用者字段的后缀匹配筛选器。 默认或空字符串匹配所有类型。 |
| subjectIsCaseSensitive | 字符串 | 用于筛选器的区分大小写匹配的控件。 |


## <a name="example-subscription-schema"></a>订阅架构示例

```json
{
  "properties": {
    "destination": {
      "endpointType": "webhook",
      "properties": {
          "endpointUrl": "https://example.azurewebsites.net/api/HttpTriggerCSharp1?code=VXbGWce53l48Mt8wuotr0GPmyJ/nDT4hgdFj9DpBiRt38qqnnm5OFg=="
      }
    },
    "filter": {
      "includedEventTypes": [ "blobCreated", "blobDeleted" ],
      "subjectBeginsWith": "blobServices/default/containers/mycontainer/log",
      "subjectEndsWith": ".jpg",
      "subjectIsCaseSensitive": "true"
    }
  }
}
```

## <a name="next-steps"></a>后续步骤

* 有关事件网格的介绍，请参阅[什么是事件网格？](overview.md)