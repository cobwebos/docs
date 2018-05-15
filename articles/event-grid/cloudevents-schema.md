---
title: 在 CloudEvents 架构中将 Azure 事件网格与事件配合使用
description: 说明如何针对 Azure 事件网格中的事件设置 CloudEvents 架构。
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 04/30/2018
ms.author: babanisa
ms.openlocfilehash: a882073fce28be1b93a6c9118c40398062f61bc5
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
---
# <a name="use-cloudevents-schema-with-event-grid"></a>将 CloudEvents 架构与事件网格配合使用

除了采用[默认事件架构](event-schema.md)的事件，Azure 事件网格本身还支持采用 [CloudEvents JSON 架构](https://github.com/cloudevents/spec/blob/master/json-format.md)的事件。 [CloudEvents](http://cloudevents.io/) 是一种[开放式标准规范](https://github.com/cloudevents/spec/blob/master/spec.md)，用于以常用方式描述事件数据。

CloudEvents 提供的常用事件架构适合发布和使用基于云的事件，因此可简化互操作性。 可以通过此架构使用统一的工具、以标准方式路由和处理事件，以及以通用方式反序列化外部事件架构。 使用通用架构可以更轻松地跨平台集成工作。

CloudEvents 是由包括 Microsoft 在内的多个[协作者](https://github.com/cloudevents/spec/blob/master/community/contributors.md)通过 [Cloud Native Compute Foundation](https://www.cncf.io/) 构建的。 它目前的发布版本为 0.1。

本文介绍如何将 CloudEvents 架构与事件网格配合使用。

## <a name="cloudevent-schema"></a>CloudEvent 架构

下面以示例方式说明了 CloudEvents 格式的 Azure Blob 存储事件：

``` JSON
{
    "cloudEventsVersion" : "0.1",
    "eventType" : "Microsoft.Storage.BlobCreated",
    "eventTypeVersion" : "",
    "source" : "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-account}#blobServices/default/containers/{storage-container}/blobs/{new-file}",
    "eventID" : "173d9985-401e-0075-2497-de268c06ff25",
    "eventTime" : "2018-04-28T02:18:47.1281675Z",
    "data" : {
      "api": "PutBlockList",
      "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
      "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
      "eTag": "0x8D4BCC2E4835CD0",
      "contentType": "application/octet-stream",
      "contentLength": 524288,
      "blobType": "BlockBlob",
      "url": "https://oc2d2817345i60006.blob.core.windows.net/oc2d2817345i200097container/oc2d2817345i20002296blob",
      "sequencer": "00000000000004420000000000028963",
      "storageDiagnostics": {
        "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
      }
    }
}
```

CloudEvents v0.1 提供以下属性：

| CloudEvents        | Type     | 示例 JSON 值             | 说明                                                        | 事件网格映射
|--------------------|----------|--------------------------------|--------------------------------------------------------------------|-------------------------
| eventType          | String   | "com.example.someevent"          | 发生的事件的类型                                   | eventType
| eventTypeVersion   | String   | "1.0"                            | eventType 的版本（可选）                            | dataVersion
| cloudEventsVersion | String   | "0.1"                            | 事件使用的 CloudEvents 规范的版本        | *已传递*
| 源             | URI      | "/mycontext"                     | 描述事件生成者                                       | topic#subject
| eventID            | String   | "1234-1234-1234"                 | 事件的 ID                                                    | id
| EventTime          | Timestamp| "2018-04-05T17:31:00Z"           | 事件发生时的时间戳（可选）                    | EventTime
| schemaURL          | URI      | "https://myschema.com"           | 数据属性所遵循的架构的链接（可选） | 未使用
| contentType        | String   | "application/json"               | 描述数据编码格式（可选）                       | 未使用
| 扩展         | 映射      | { "extA": "vA", "extB", "vB" }  | 任何其他的元数据（可选）                                 | 未使用
| 数据               | 对象   | { "objA": "vA", "objB", "vB" }  | 事件有效负载（可选）                                       | 数据

有关详细信息，请参阅 [CloudEvents 规范](https://github.com/cloudevents/spec/blob/master/spec.md#context-attributes)。

## <a name="configure-event-grid-for-cloudevents"></a>为 CloudEvents 配置事件网格

目前，Azure 事件网格为**美国中西部**、**美国中部**和**北欧**的 CloudEvents JSON 格式输入和输出提供预览版支持。

若要使用 CloudEvent，必须为 Azure CLI 启用扩展：

```azurecli
az extension add –-name eventgrid
```

可以将事件网格用于 CloudEvents 架构的事件的输入和输出。 可以将 CloudEvents 用于系统事件（例如 Blob 存储事件和 IoT 中心事件）和自定义事件。 它还可以将网络上的这些事件来回转换。


| 输入架构       | 输出架构
|--------------------|---------------------
| CloudEvents 格式 | CloudEvents 格式
| 事件网格格式  | CloudEvents 格式
| CloudEvents 格式 | 事件网格格式
| 事件网格格式  | 事件网格格式

对于所有事件架构，事件网格都要求在发布到事件网格主题时以及在创建事件订阅时进行验证。 有关详细信息，请参阅[事件网格安全性和身份验证](security-authentication.md)。

### <a name="input-schema"></a>输入架构

若要将某个自定义主题的输入架构设置为 CloudEvents，请在创建主题 `--input-schema cloudeventv01schema` 时，在 Azure CLI 中使用以下参数。 此自定义主题现在会预期传入事件采用 CloudEvents v0.1 格式。

若要创建事件网格主题，请使用：

```azurecli
az eventgrid topic create \
  --name <topic_name> \
  -l westcentralus \
  -g gridResourceGroup \
  --input-schema cloudeventv01schema
```

CloudEvents 的当前版本不支持对事件进行批处理。 若要使用 CloudEvent 架构将事件发布到某个主题，请单独发布每个事件。

### <a name="output-schema"></a>输出架构

若要将某个事件订阅的输出架构设置为 CloudEvents，请在创建事件订阅 `--event-delivery-schema cloudeventv01schema` 时，在 Azure CLI 中使用以下参数。 此事件订阅的事件现在以 CloudEvents v0.1 格式分发。

若要创建事件订阅，请使用：

```azurecli
az eventgrid event-subscription create \
  --name <event_subscription_name> \  
  --topic-name <topic_name> \
  -g gridResourceGroup \
  --endpoint <endpoint_URL> \
  --event-delivery-schema cloudeventv01schema
```

CloudEvents 的当前版本不支持对事件进行批处理。 针对 CloudEvent 架构配置的事件订阅会单独接收每个事件。

## <a name="next-steps"></a>后续步骤

* 有关监视事件传送的信息，请参阅[监视事件网格消息传送](monitor-event-delivery.md)。
* 我们鼓励你对 CloudEvents 进行测试、评论并亲自[参与](https://github.com/cloudevents/spec/blob/master/CONTRIBUTING.md)进来。
* 有关创建 Azure 事件网格订阅的详细信息，请参阅[事件网格订阅架构](subscription-creation-schema.md)。
