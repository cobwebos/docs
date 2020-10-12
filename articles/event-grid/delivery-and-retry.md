---
title: Azure 事件网格传送和重试
description: 介绍 Azure 事件网格如何传送事件以及如何处理未送达的消息。
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 924abaa1e5c12c4477bddf888541e7414b7bdbec
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91324087"
---
# <a name="event-grid-message-delivery-and-retry"></a>事件网格消息传送和重试

本文介绍了未确认送达时 Azure 事件网格如何处理事件。

事件网格提供持久传送。 它会将每个订阅的每条消息至少发送一次。 事件会立即发送到每个订阅的已注册终结点。 如果终结点未确认收到事件，事件网格会重试传送事件。

## <a name="batched-event-delivery"></a>批量事件传送

默认情况下，事件网格单独将每个事件发送给订阅者。 订阅者接收包含单个事件的数组。 你可以将事件网格配置为批量处理要传送的事件，以在高吞吐量方案中提高 HTTP 性能。

批量传送有两个设置：

* **每批最大事件数** - 事件网格每批将传送的最大事件数。 永远不会超过此数目，但是，如果在发布时没有更多事件，则可能会传送较少的事件。 如果只有较少的事件，事件网格不会为了创建某个批而延迟事件传送。 必须介于 1 到 5,000 之间。
* **首选批大小(KB)** - 批大小的目标上限 (KB)。 与最大事件数类似，如果发布时没有更多的事件，则批大小可能会较小。 *如果*单个事件大于首选大小，则批可能会大于首选批大小。 例如，如果首选大小为 4 KB，并且一个 10 KB 的事件推送到了事件网格，则 10 KB 事件将会在其自己的批中传送，而不会被删除。

可以通过门户、CLI、PowerShell 或 SDK 以每事件订阅为基础配置批量传送。

### <a name="azure-portal"></a>Azure 门户： 
![文件传送设置](./media/delivery-and-retry/batch-settings.png)

### <a name="azure-cli"></a>Azure CLI
创建事件订阅时，请使用以下参数： 

- **max-events-per-batch** - 每批的最大事件数。 必须是介于 1 和 5000 之间的数字。
- **preferred-batch-size-in-kilobytes** - 首选批大小 (KB)。 必须是介于 1 和 1024 之间的数字。

```azurecli
storageid=$(az storage account show --name <storage_account_name> --resource-group <resource_group_name> --query id --output tsv)
endpoint=https://$sitename.azurewebsites.net/api/updates

az eventgrid event-subscription create \
  --resource-id $storageid \
  --name <event_subscription_name> \
  --endpoint $endpoint \
  --max-events-per-batch 1000 \
  --preferred-batch-size-in-kilobytes 512
```

有关将 Azure CLI 与事件网格配合使用的详细信息，请参阅[使用 Azure CLI 将存储事件路由到 Web 终结点](../storage/blobs/storage-blob-event-quickstart.md)。

## <a name="retry-schedule-and-duration"></a>重试计划和持续时间

传送消息后，事件网格将等待 30 秒以接收响应。 30秒后，如果终结点未响应，则该消息将排队等待重试。 对于事件传送，事件网格使用指数性的回退重试策略。 事件网格会尽量按以下计划重试传送：

- 10 秒
- 30 秒
- 1 分钟
- 5 分钟
- 10 分钟
- 30 分钟
- 1 小时
- 按小时论的话，最多 24 小时

如果终结点在 3 分钟内做出了响应，则事件网格会尽量尝试从重试队列中删除事件，但仍可能会收到重复项。

事件网格为所有重试步骤添加小的随机性，在某个终结点持续运行不正常、停机很长时间，或者看起来已过载的情况下，会适时跳过某些重试。

对于确定性行为，请在[订阅重试策略](manage-event-delivery.md)中设置事件生存时间和最大传递尝试次数。

默认情况下，事件网格会使所有在 24 小时内未送达的事件过期。 创建事件订阅时，可[自定义重试策略](manage-event-delivery.md)。 提供最大传递尝试次数（默认值为 30）和事件生存时间（默认为 1440 分钟）。

## <a name="delayed-delivery"></a>延迟传送

当终结点遇到传送失败时，事件网格将开始延迟向该终结点传送和重试事件。 例如，如果发布到某个终结点的前 10 个事件失败，事件网格将假设该终结点遇到问题，并将所有后续重试和新的传送操作延迟一段时间 - 在某些情况下，会延迟几个小时。 

从功能上讲，延迟传送的目的是保护不正常的终结点以及事件网格系统。 如果不采用退让机制并延迟向不正常的终结点传送事件，事件网格的重试策略和卷功能可能很容易使系统瘫痪。

## <a name="dead-letter-events"></a>死信事件
当事件网格无法在特定时间段内或在尝试传递事件一定次数后传递事件时，它可以将未传递的事件发送到存储帐户。 此过程称为“死信处理”。 满足以下条件之一时，事件网格会将事件视为死信。 

- 事件未在生存期内传递
- 尝试传递事件的次数已超出限制

如果满足上述任一条件，则会将该事件删除或视为死信。  默认情况下，事件网格不启用死信处理。 若要启用该功能，在创建事件订阅时必须指定一个存储帐户来存放未送达的事件。 你将从此存储帐户中拉取事件来解决传递问题。

事件网格已进行所有重试尝试后会将事件发送到死信位置。 如果事件网格收到 400（错误请求）或 413（请求实体太大）响应代码，它会立即将事件发送到死信终结点。 这些响应代码指示事件传送将永远不会成功。

最后一次尝试发送事件与发送到死信位置之间有五分钟的延迟。 此延迟旨在减少 Blob 存储操作的数量。 如果死信位置已四小时不可用，则会丢弃该事件。

在设置死信位置之前，必须有一个包含容器的存储帐户。 在创建事件订阅时，需要提供此容器的终结点。 终结点的格式如下：`/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-name>/blobServices/default/containers/<container-name>`

你可能希望在事件发送到死信位置时收到通知。 若要使用事件网格来响应未送达的事件，请为死信 blob 存储[创建事件订阅](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)。 每当死信 blob 存储收到未送达的事件时，事件网格都会通知处理程序。 处理程序使用你希望采取的、用于协调未送达的事件的操作进行响应。 有关设置死信位置和重试策略的示例，请参阅 [死信和重试策略](manage-event-delivery.md)。

## <a name="delivery-event-formats"></a>传递事件格式
本部分提供了不同传递架构格式 (事件网格架构、CloudEvents 1.0 架构和自定义架构) 的事件和死信事件的示例。 有关这些格式的详细信息，请参阅 [事件网格架构](event-schema.md) 和 [Cloud Events 1.0 架构](cloud-event-schema.md) 文章。 

### <a name="event-grid-schema"></a>事件网格架构

#### <a name="event"></a>事件 
```json
{
    "id": "93902694-901e-008f-6f95-7153a806873c",
    "eventTime": "2020-08-13T17:18:13.1647262Z",
    "eventType": "Microsoft.Storage.BlobCreated",
    "dataVersion": "",
    "metadataVersion": "1",
    "topic": "/subscriptions/000000000-0000-0000-0000-00000000000000/resourceGroups/rgwithoutpolicy/providers/Microsoft.Storage/storageAccounts/myegteststgfoo",
    "subject": "/blobServices/default/containers/deadletter/blobs/myBlobFile.txt",    
    "data": {
        "api": "PutBlob",
        "clientRequestId": "c0d879ad-88c8-4bbe-8774-d65888dc2038",
        "requestId": "93902694-901e-008f-6f95-7153a8000000",
        "eTag": "0x8D83FACDC0C3402",
        "contentType": "text/plain",
        "contentLength": 0,
        "blobType": "BlockBlob",
        "url": "https://myegteststgfoo.blob.core.windows.net/deadletter/myBlobFile.txt",
        "sequencer": "00000000000000000000000000015508000000000005101c",
        "storageDiagnostics": { "batchId": "cfb32f79-3006-0010-0095-711faa000000" }
    }
}
```

#### <a name="dead-letter-event"></a>死信事件

```json
{
    "id": "93902694-901e-008f-6f95-7153a806873c",
    "eventTime": "2020-08-13T17:18:13.1647262Z",
    "eventType": "Microsoft.Storage.BlobCreated",
    "dataVersion": "",
    "metadataVersion": "1",
    "topic": "/subscriptions/0000000000-0000-0000-0000-000000000000000/resourceGroups/rgwithoutpolicy/providers/Microsoft.Storage/storageAccounts/myegteststgfoo",
    "subject": "/blobServices/default/containers/deadletter/blobs/myBlobFile.txt",    
    "data": {
        "api": "PutBlob",
        "clientRequestId": "c0d879ad-88c8-4bbe-8774-d65888dc2038",
        "requestId": "93902694-901e-008f-6f95-7153a8000000",
        "eTag": "0x8D83FACDC0C3402",
        "contentType": "text/plain",
        "contentLength": 0,
        "blobType": "BlockBlob",
        "url": "https://myegteststgfoo.blob.core.windows.net/deadletter/myBlobFile.txt",
        "sequencer": "00000000000000000000000000015508000000000005101c",
        "storageDiagnostics": { "batchId": "cfb32f79-3006-0010-0095-711faa000000" }
    },

    "deadLetterReason": "MaxDeliveryAttemptsExceeded",
    "deliveryAttempts": 1,
    "lastDeliveryOutcome": "NotFound",
    "publishTime": "2020-08-13T17:18:14.0265758Z",
    "lastDeliveryAttemptTime": "2020-08-13T17:18:14.0465788Z" 
}
```

### <a name="cloudevents-10-schema"></a>CloudEvents 1.0 架构

#### <a name="event"></a>事件

```json
{
    "id": "caee971c-3ca0-4254-8f99-1395b394588e",
    "source": "mysource",
    "dataversion": "1.0",
    "subject": "mySubject",
    "type": "fooEventType",
    "datacontenttype": "application/json",
    "data": {
        "prop1": "value1",
        "prop2": 5
    }
}
```

#### <a name="dead-letter-event"></a>死信事件

```json
{
    "id": "caee971c-3ca0-4254-8f99-1395b394588e",
    "source": "mysource",
    "dataversion": "1.0",
    "subject": "mySubject",
    "type": "fooEventType",
    "datacontenttype": "application/json",
    "data": {
        "prop1": "value1",
        "prop2": 5
    },

    "deadletterreason": "MaxDeliveryAttemptsExceeded",
    "deliveryattempts": 1,
    "lastdeliveryoutcome": "NotFound",
    "publishtime": "2020-08-13T21:21:36.4018726Z",
}
```

### <a name="custom-schema"></a>自定义架构

#### <a name="event"></a>事件

```json
{
    "prop1": "my property",
    "prop2": 5,
    "myEventType": "fooEventType"
}

```

#### <a name="dead-letter-event"></a>死信事件
```json
{
    "id": "8bc07e6f-0885-4729-90e4-7c3f052bd754",
    "eventTime": "2020-08-13T18:11:29.4121391Z",
    "eventType": "myEventType",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/00000000000-0000-0000-0000-000000000000000/resourceGroups/rgwithoutpolicy/providers/Microsoft.EventGrid/topics/myCustomSchemaTopic",
    "subject": "subjectDefault",
  
    "deadLetterReason": "MaxDeliveryAttemptsExceeded",
    "deliveryAttempts": 1,
    "lastDeliveryOutcome": "NotFound",
    "publishTime": "2020-08-13T18:11:29.4121391Z",
    "lastDeliveryAttemptTime": "2020-08-13T18:11:29.4277644Z",
  
    "data": {
        "prop1": "my property",
        "prop2": 5,
        "myEventType": "fooEventType"
    }
}
```


## <a name="message-delivery-status"></a>消息传送状态

事件网格使用 HTTP 响应代码确认已接收事件。 

### <a name="success-codes"></a>成功代码

事件网格**仅**将以下 HTTP 响应代码视为传送成功。 所有其他状态代码被视为传送失败，将会相应地重试传送或将事件加入死信队列。 收到成功状态代码后，事件网格认为传送已完成。

- 200 正常
- 201 Created
- 202 已接受
- 203 非权威信息
- 204 无内容

### <a name="failure-codes"></a>失败代码

不在上述集 (200-204) 内的所有其他代码被视为失败，将会重试。 某些代码已关联到下面所述的特定重试策略，所有其他代码遵循标准的指数退让模型。 请务必注意，由于事件网格体系结构的高度并行化特性，重试行为是不确定的。 

| 状态代码 | 重试行为 |
| ------------|----------------|
| 400 错误的请求 | 在 5 分钟或更长时间后重试（如果设置了死信，则立即加入死信队列） |
| 401 未授权 | 5 分钟或更长时间后重试 |
| 403 禁止访问 | 5 分钟或更长时间后重试 |
| 404 未找到 | 5 分钟或更长时间后重试 |
| 408 请求超时 | 2 分钟或更长时间后重试 |
| 413 请求实体太大 | 在 10 秒或更长时间后重试（如果设置了死信，则立即加入死信队列） |
| 503 服务不可用 | 30 秒或更长时间后重试 |
| 所有其他 | 10 秒或更长时间后重试 |


## <a name="next-steps"></a>后续步骤

* 若要查看事件传送的状态，请参阅[监视事件网格消息传送](monitor-event-delivery.md)。
* 若要自定义事件传送选项，请参阅[死信和重试策略](manage-event-delivery.md)。
