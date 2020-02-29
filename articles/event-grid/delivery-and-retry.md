---
title: Azure 事件网格传送和重试
description: 介绍 Azure 事件网格如何传送事件以及如何处理未送达的消息。
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 02/27/2020
ms.author: spelluru
ms.openlocfilehash: dda2fd98c4c0d330059156a5ec00baa97ffaf627
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2020
ms.locfileid: "77921056"
---
# <a name="event-grid-message-delivery-and-retry"></a>事件网格消息传送和重试

本文介绍了未确认送达时 Azure 事件网格如何处理事件。

事件网格提供持久传送。 它会将每个订阅的每条消息至少发送一次。 事件会立即发送到每个订阅的已注册终结点。 如果终结点未确认收到事件，事件网格会重试传送事件。

## <a name="batched-event-delivery"></a>批处理事件传递

事件网格默认为将每个事件分别发送到订阅服务器。 订阅者接收包含单个事件的数组。 您可以配置事件网格以分批传递事件以提高高吞吐量方案中的 HTTP 性能。

批处理传递具有两种设置：

* **每批的最大事件**数-每个批处理的最大事件数事件网格。 此数字永远不会超过，但如果发布时没有其他事件可用，则可能会传递较少的事件。 如果有更少的事件可用，事件网格不会延迟事件以创建批。 必须介于1到5000之间。
* **首选批大小（kb）** -批大小的目标上限（以 kb 为单位）。 与 max 事件类似，如果发布时有更多的事件，批大小可能会较小。 *如果*单个事件大于首选大小，则批处理可能大于首选的批大小。 例如，如果首选大小为 4 KB，将 10 KB 事件推送到事件网格，则 10 KB 事件仍会在其自己的批处理中传递，而不会被删除。

通过门户、CLI、PowerShell 或 Sdk 基于每个事件订阅配置的批处理交付。

### <a name="azure-portal"></a>Azure 门户： 
![批处理传递设置](./media/delivery-and-retry/batch-settings.png)

### <a name="azure-cli"></a>Azure CLI
创建事件订阅时，请使用以下参数： 

- **max-每批事件**数-批处理中的最大事件数。 必须是介于1和5000之间的数字。
- **首选批处理大小**（以 kb 为单位）首选批大小（kb）。 必须是介于1和1024之间的数字。

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

有关将 Azure CLI 与事件网格结合使用的详细信息，请参阅[使用 Azure CLI 将存储事件路由到 web 终结点](../storage/blobs/storage-blob-event-quickstart.md)。

## <a name="retry-schedule-and-duration"></a>重试计划和持续时间

传递消息后，事件网格会等待30秒响应。 30秒后，如果终结点未响应，则该消息将排队等待重试。 对于事件传送，事件网格使用指数性的回退重试策略。 事件网格最大限度地按以下计划重试传递：

- 10 秒
- 30 秒
- 1 分钟
- 5 分钟
- 10 分钟
- 30 分钟
- 1 小时
- 每小时最多24小时

如果终结点在3分钟内响应，事件网格将尝试最大程度地从重试队列中删除事件，但仍可能会收到重复项。

事件网格将小型随机化添加到所有重试步骤，并且如果终结点的运行状况始终不正常、较长一段时间较长，或者似乎已被淹没，找机会将跳过某些重试。

对于确定性行为，在[订阅重试策略](manage-event-delivery.md)中将事件时间设置为 "实时" 和 "最大传递尝试次数"。

默认情况下，事件网格会使所有在 24 小时内未送达的事件过期。 创建事件订阅时，可[自定义重试策略](manage-event-delivery.md)。 提供最大传递尝试次数（默认值为 30）和事件生存时间（默认为 1440 分钟）。

## <a name="delayed-delivery"></a>延迟交付

当终结点遇到传递失败时，事件网格将开始延迟向该终结点传递和重试事件。 例如，如果发布到终结点的前10个事件失败，事件网格将假定终结点遇到问题，并将延迟所有后续重试*和新*传递时间（在某些情况下，长达几个小时）。

延迟交付的功能用途是保护不正常的终结点以及事件网格系统。 如果不进行备份并延迟交付到不正常的终结点，事件网格的重试策略和卷功能可能很容易影响系统。

## <a name="dead-letter-events"></a>死信事件

当事件网格无法传递事件时，它会将未送达的事件发送到某个存储帐户。 此过程称为死信处理。 默认情况下，事件网格不启用死信处理。 若要启用该功能，在创建事件订阅时必须指定一个存储帐户来存放未送达的事件。 你将从此存储帐户中拉取事件来解决传递问题。

事件网格已进行所有重试尝试后会将事件发送到死信位置。 如果事件网格收到 400（错误请求）或 413（请求实体太大）响应代码，它会立即将事件发送到死信终结点。 这些响应代码指示事件传送将永远不会成功。

最后一次尝试发送事件与发送到死信位置之间有五分钟的延迟。 此延迟旨在减少 Blob 存储操作的数量。 如果死信位置已四小时不可用，则会丢弃该事件。

在设置死信位置之前，必须有一个包含容器的存储帐户。 在创建事件订阅时，需要提供此容器的终结点。 终结点的格式如下：`/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-name>/blobServices/default/containers/<container-name>`

你可能希望在事件发送到死信位置时收到通知。 若要使用事件网格来响应未送达的事件，请为死信 blob 存储[创建事件订阅](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)。 每当死信 blob 存储收到未送达的事件时，事件网格都会通知处理程序。 处理程序使用你希望采取的、用于协调未送达的事件的操作进行响应。

有关设置死信位置的示例，请参阅[死信和重试策略](manage-event-delivery.md)。

## <a name="message-delivery-status"></a>消息传送状态

事件网格使用 HTTP 响应代码确认已接收事件。 

### <a name="success-codes"></a>成功代码

事件网格**仅**将以下 HTTP 响应代码视为成功传递。 所有其他状态代码都被视为失败的传递，并将根据需要重试或死信。 接收到成功的状态代码后，事件网格会认为传递完成。

- 200 正常
- 201 Created
- 202 已接受
- 203非权威信息
- 204 无内容

### <a name="failure-codes"></a>失败代码

不在上述集（200-204）中的所有其他代码都被视为失败，并将重试。 有些特定的重试策略与下面所述的规则相关联，所有其他策略都遵循标准指数回退模型。 请务必记住，由于事件网格体系结构的高度并行化特性，重试行为是不确定的。 

| 状态代码 | 重试行为 |
| ------------|----------------|
| 400 错误请求 | 在5分钟或更多时间后重试（如果死信设置，则立即死信） |
| 401 未授权 | 5分钟后重试 |
| 403 禁止访问 | 5分钟后重试 |
| 404 未找到 | 5分钟后重试 |
| 408 请求超时 | 2分钟后重试 |
| 413 请求实体太大 | 10秒或更大后重试（如果死信设置，则立即死信） |
| 503 服务不可用 | 30秒或更高后重试 |
| 其他 | 10秒钟或以上后重试 |


## <a name="next-steps"></a>后续步骤

* 若要查看事件传送的状态，请参阅[监视事件网格消息传送](monitor-event-delivery.md)。
* 若要自定义事件传送选项，请参阅[死信和重试策略](manage-event-delivery.md)。
