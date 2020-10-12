---
title: 使用 Blob 存储作为 Azure Stack Hub（预览版）上的检查点存储
description: 本文介绍如何使用 Blob 存储作为 Azure Stack Hub（预览版）上事件中心的检查点存储。
ms.topic: how-to
ms.date: 06/23/2020
ms.openlocfilehash: 1c876ed36be6aec9395a1acba3a1deb25a47de2c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87039219"
---
# <a name="use-blob-storage-as-checkpoint-store---event-hubs-on-azure-stack-hub-preview"></a>使用 Blob 存储作为检查点存储 - Azure Stack Hub（预览版）上的事件中心
如果你在一个环境中使用 Azure Blob 存储作为检查点存储，该环境支持与 Azure 上通常可用的存储 Blob SDK 版本不同的版本，那么你需要使用代码将存储服务 API 版本更改为该环境支持的特定版本。 例如，如果运行[基于 Azure Stack Hub 版本 2002 的事件中心](/azure-stack/user/event-hubs-overview)，则存储服务的最高可用版本为 2017-11-09。 在这种情况下，需要使用代码将存储服务 API 版本设定为 2017-11-09。 如需通过示例来了解如何以特定的存储 API 版本为目标，请参阅“GitHub 上的这些示例”： 

- [.NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/Sample10_RunningWithDifferentStorageVersion.cs)
- [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/EventProcessorWithCustomStorageVersion.java)。 
- [JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/javascript/receiveEventsWithApiSpecificStorage.js) 或 [TypeScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/typescript/src/receiveEventsWithApiSpecificStorage.ts) 
- Python - [同步](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob/samples/receive_events_using_checkpoint_store_storage_api_version.py)、[异步](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio/samples/receive_events_using_checkpoint_store_storage_api_version_async.py)

> [!IMPORTANT]
> Azure Stack 集线器上的事件中心目前处于 [预览阶段](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) ，并且是免费的。 

如果不针对 Azure Stack Hub 支持的版本运行使用 Blob 存储作为检查点存储的事件中心接收器，则会收到以下错误消息：

```
The value for one of the HTTP headers is not in the correct format
```


## <a name="sample-error-message-in-python"></a>Python 中的示例错误消息
对于 Python，错误 `azure.core.exceptions.HttpResponseError` 将传递到 `EventHubConsumerClient.receive()` 的错误处理程序 `on_error(partition_context, error)`。 但是，`receive()` 方法不会引发异常。 `print(error)` 将输出以下异常信息：

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

请参阅以下文章，了解分区和检查点：[跨应用程序的多个实例均衡分区负载](event-processor-balance-partition-load.md)
