---
title: 使用 Blob 存储作为 Azure 堆栈中心上的检查点存储（预览）
description: 本文介绍如何在 Azure 堆栈中心（预览）的事件中心中使用 Blob 存储作为检查点存储。
services: event-hubs
documentationcenter: na
author: spelluru
ms.service: event-hubs
ms.topic: how-to
ms.date: 03/18/2020
ms.author: spelluru
ms.openlocfilehash: 1f0e4dea44007ef82cb4b700ff0be4a5579541d8
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2020
ms.locfileid: "80398917"
---
# <a name="use-blob-storage-as-checkpoint-store---event-hubs-on-azure-stack-hub-preview"></a>使用 Blob 存储作为检查点存储 - Azure 堆栈中心的事件中心（预览）
如果在支持与 Azure 上通常可用的版本的存储 Blob SDK 不同的环境中使用 Azure Blob 存储作为检查点存储，则需要使用代码将存储服务 API 版本更改为该环境支持的特定版本。 例如，如果您在[Azure 堆栈中心版本 2002 上运行事件中心](https://docs.microsoft.com/azure-stack/user/event-hubs-overview)，则存储服务的最高可用版本是版本 2017-11-09。 在这种情况下，您需要使用代码将存储服务 API 版本定位到 2017-11-09。 有关如何定位特定存储 API 版本的示例，请参阅 GitHub 上的以下示例： 

- [.NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/Sample10_RunningWithDifferentStorageVersion.cs)
- [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/EventProcessorWithOlderStorageVersion.java). 
- [JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/receiveEventsWithDownleveledStorage.js)或[TypeScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/receiveEventsWithDownleveledStorage.ts)， 
- Python -[同步](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob/samples/event_processor_blob_storage_example_with_storage_api_version.py)，[异步](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio/samples/event_processor_blob_storage_example_with_storage_api_version.py)

> [!IMPORTANT]
> Azure 堆栈中心上的事件中心当前处于[预览状态](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)，并且是免费的。 

如果运行事件中心接收器，使用 Blob 存储作为检查点存储，而不定位 Azure 堆栈中心支持的版本，您将收到以下错误消息：

```
The value for one of the HTTP headers is not in the correct format
```


## <a name="sample-error-message-in-python"></a>Python 中的示例错误消息
对于 Python，将`azure.core.exceptions.HttpResponseError`错误传递给 的错误`on_error(partition_context, error)``EventHubConsumerClient.receive()`处理程序。 但是，该方法`receive()`不会引发异常。 `print(error)`将打印以下异常信息：

```bash
The value for one of the HTTP headers is not in the correct format.

RequestId:f048aee8-a90c-08ba-4ce1-e69dba759297
Time:2020-03-17T22:04:13.3559296Z
ErrorCode:InvalidHeaderValue
Error:None
HeaderName:x-ms-version
HeaderValue:2019-07-07
```

记录器将记录两个警告，如下所示：

```bash
WARNING:azure.eventhub.extensions.checkpointstoreblobaio._blobstoragecsaio: 
An exception occurred during list_ownership for namespace '<namespace-name>.eventhub.<region>.azurestack.corp.microsoft.com' eventhub 'python-eh-test' consumer group '$Default'. 

Exception is HttpResponseError('The value for one of the HTTP headers is not in the correct format.\nRequestId:f048aee8-a90c-08ba-4ce1-e69dba759297\nTime:2020-03-17T22:04:13.3559296Z\nErrorCode:InvalidHeaderValue\nError:None\nHeaderName:x-ms-version\nHeaderValue:2019-07-07')

WARNING:azure.eventhub.aio._eventprocessor.event_processor:EventProcessor instance '26d84102-45b2-48a9-b7f4-da8916f68214' of eventhub 'python-eh-test' consumer group '$Default'. An error occurred while load-balancing and claiming ownership. 

The exception is HttpResponseError('The value for one of the HTTP headers is not in the correct format.\nRequestId:f048aee8-a90c-08ba-4ce1-e69dba759297\nTime:2020-03-17T22:04:13.3559296Z\nErrorCode:InvalidHeaderValue\nError:None\nHeaderName:x-ms-version\nHeaderValue:2019-07-07'). Retrying after 71.45254944090853 seconds
```



## <a name="next-steps"></a>后续步骤

请参阅以下文章了解分区和检查点：[平衡应用程序多个实例的分区负载](event-processor-balance-partition-load.md)
