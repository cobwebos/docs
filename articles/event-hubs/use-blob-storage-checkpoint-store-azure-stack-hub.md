---
title: 使用 Blob 存储作为 Azure Stack 中心（预览版）上的检查点存储
description: 本文介绍如何使用 Blob 存储作为 Azure Stack 中心（预览版）上的事件中心中的检查点存储。
services: event-hubs
documentationcenter: na
author: spelluru
ms.service: event-hubs
ms.topic: how-to
ms.date: 03/18/2020
ms.author: spelluru
ms.openlocfilehash: 2938099383c32eac493e4b4bb620f03c76ca5c44
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "82023643"
---
# <a name="use-blob-storage-as-checkpoint-store---event-hubs-on-azure-stack-hub-preview"></a>使用 Blob 存储作为 Azure Stack 集线器上的检查点存储-事件中心（预览）
如果将 Azure Blob 存储用作支持不同版本的存储 Blob SDK 的环境中的检查点存储，而不是 Azure 上通常可用的版本，则需要使用代码将存储服务 API 版本更改为该环境支持的特定版本。 例如，如果你正在[Azure Stack 中心版本2002上运行事件中心](https://docs.microsoft.com/azure-stack/user/event-hubs-overview)，则存储服务的最高可用版本为2017-11-09 版。 在这种情况下，需要使用代码将存储服务 API 版本设定为2017-11-09。 有关如何以特定存储 API 版本为目标的示例，请参阅 GitHub 上的以下示例： 

- [.NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/Sample10_RunningWithDifferentStorageVersion.cs)
- [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/EventProcessorWithCustomStorageVersion.java)。 
- [JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/javascript/receiveEventsWithApiSpecificStorage.js)或[TypeScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/typescript/src/receiveEventsWithApiSpecificStorage.ts) 
- Python-[同步](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob/samples/receive_events_using_checkpoint_store_storage_api_version.py)、[异步](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio/samples/receive_events_using_checkpoint_store_storage_api_version_async.py)

> [!IMPORTANT]
> Azure Stack 集线器上的事件中心目前处于[预览阶段](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)，并且是免费的。 

如果在不以 Azure Stack 中心支持的版本为目标的情况下，将使用 Blob 存储的事件中心接收器作为检查点存储运行，则将收到以下错误消息：

```
The value for one of the HTTP headers is not in the correct format
```


## <a name="sample-error-message-in-python"></a>Python 中的示例错误消息
对于 Python， `azure.core.exceptions.HttpResponseError`会将错误传递给的错误处理程序`on_error(partition_context, error)` `EventHubConsumerClient.receive()`。 但该方法`receive()`不会引发异常。 `print(error)`将打印以下异常信息：

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

请参阅以下文章了解分区和检查点：[跨应用程序的多个实例平衡分区负载](event-processor-balance-partition-load.md)
