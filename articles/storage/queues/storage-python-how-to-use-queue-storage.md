---
title: How to use Azure Queue storage v2.1 from Python - Azure Storage
description: Learn how to use the Azure Queue service v2.1 from Python to create and delete queues, and insert, get, and delete messages.
author: mhopkins-msft
ms.service: storage
ms.author: mhopkins
ms.date: 09/17/2019
ms.subservice: queues
ms.topic: conceptual
ms.reviewer: cbrooks
ms.custom: seo-javascript-october2019
ms.openlocfilehash: b5382a6a1ea381d57a026e9d42190152e38f7696
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74209527"
---
# <a name="how-to-use-azure-queue-storage-v21-from-python"></a>How to use Azure Queue storage v2.1 from Python

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

This article demonstrates common scenarios using the Azure Queue storage service. The scenarios covered include inserting, peeking, getting, and deleting queue messages, and creating and deleting queues.

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>概述

The samples in this article are written in Python and use the [用于 Python 的 Microsoft Azure 存储 SDK]. 有关队列的详细信息，请参阅[后续步骤](#next-steps)部分。

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="download-and-install-azure-storage-sdk-for-python"></a>Download and install Azure Storage SDK for Python

The [Azure Storage SDK for Python](https://github.com/azure/azure-storage-python) requires Python version 2.7, 3.3, or later.
 
### <a name="install-via-pypi"></a>通过 PyPi 安装

要通过 Python 包索引 (PyPI) 安装，请键入：

```bash
pip install azure-storage-blob==2.1.0
```

> [!NOTE]
> 如果要从适用于 Python 的 Azure 存储 SDK 版本 0.36 或更早版本升级，请在安装最新软件包之前使用 `pip uninstall azure-storage` 卸载旧版 SDK。

有关备用安装方法，请参阅[适用于 Python 的 Azure 存储 SDK](https://github.com/Azure/azure-storage-python/)。

## <a name="view-the-sample-application"></a>查看示例应用程序

To view and run a sample application that shows how to use Python with Azure Queues, see [Azure Storage: Getting Started with Azure Queues in Python](https://github.com/Azure-Samples/storage-queue-python-getting-started). 

若要运行示例应用程序，请确保已安装 `azure-storage-queue` 和 `azure-storage-common` 软件包。

## <a name="create-a-queue"></a>创建队列

可以通过 [QueueService](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice) 对象来处理队列。 The following code creates a `QueueService` object. 在希望在其中以编程方式访问 Azure 存储的任何 Python 文件中，将以下代码添加到文件的顶部附近：

```python
from azure.storage.queue import QueueService
```

以下代码使用存储帐户名称和帐户密钥创建一个 `QueueService` 对象。 Replace *myaccount* and *mykey* with your account name and key.

```python
queue_service = QueueService(account_name='myaccount', account_key='mykey')

queue_service.create_queue('taskqueue')
```

## <a name="insert-a-message-into-a-queue"></a>在队列中插入消息

To insert a message into a queue, use the [put_message](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#put-message-queue-name--content--visibility-timeout-none--time-to-live-none--timeout-none-) method to create a new message and add it to the queue.

```python
queue_service.put_message('taskqueue', u'Hello World')
```

Azure queue messages are stored as text. If you want to store binary data, setup Base64 encoding and decoding functions on the queue service object before putting a message in the queue.

```python
# setup queue Base64 encoding and decoding functions
queue_service.encode_function = QueueMessageFormat.binary_base64encode
queue_service.decode_function = QueueMessageFormat.binary_base64decode
```

## <a name="peek-at-the-next-message"></a>扫视下一条消息

You can peek at the message in the front of a queue without removing it from the queue by calling the [peek_messages](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#peek-messages-queue-name--num-messages-none--timeout-none-) method. By default, `peek_messages` peeks at a single message.

```python
messages = queue_service.peek_messages('taskqueue')
for message in messages:
    print(message.content)
```

## <a name="dequeue-messages"></a>取消消息的排队

代码分两步从队列中删除消息。 When you call [get_messages](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#get-messages-queue-name--num-messages-none--visibility-timeout-none--timeout-none-), you get the next message in a queue by default. 从 `get_messages` 返回的消息对于从此队列读取消息的任何其他代码都是不可见的。 默认情况下，此消息将持续 30 秒不可见。 若要完成从队列中删除消息，还必须调用 [delete_message](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#delete-message-queue-name--message-id--pop-receipt--timeout-none-)。 此删除消息的两步过程可确保当你的代码因硬件或软件故障而无法处理消息时，你的其他代码实例可以获取同一消息并重试。 Your code calls `delete_message` right after the message has been processed.

```python
messages = queue_service.get_messages('taskqueue')
for message in messages:
    print(message.content)
    queue_service.delete_message('taskqueue', message.id, message.pop_receipt)
```

你可以通过两种方式自定义队列中的消息检索。 首先，你可以获取一批消息（最多 32 个）。 其次，你可以设置更长或更短的不可见超时时间，从而允许你的代码使用更多或更少时间来完全处理每个消息。 The following code example uses the `get_messages` method to get 16 messages in one call. 然后，使用 for 循环来处理每条消息。 它还将每条消息的不可见超时时间设置为 5 分钟。

```python
messages = queue_service.get_messages(
    'taskqueue', num_messages=16, visibility_timeout=5*60)
for message in messages:
    print(message.content)
    queue_service.delete_message('taskqueue', message.id, message.pop_receipt)
```

## <a name="change-the-contents-of-a-queued-message"></a>更改已排队消息的内容

可以更改队列中现有消息的内容。 如果消息表示工作任务，则可以使用此功能来更新该工作任务的状态。 The code below uses the [update_message](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#update-message-queue-name--message-id--pop-receipt--visibility-timeout--content-none--timeout-none-) method to update a message. 可见性超时设为 0，这意味着消息会立刻出现且内容将更新。

```python
messages = queue_service.get_messages('taskqueue')
for message in messages:
    queue_service.update_message(
        'taskqueue', message.id, message.pop_receipt, 0, u'Hello World Again')
```

## <a name="get-the-queue-length"></a>获取队列长度

可以获取队列中消息的估计数。 The [get_queue_metadata](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#get-queue-metadata-queue-name--timeout-none-) method asks the queue service to return metadata about the queue, and the `approximate_message_count`. 结果仅是近似值，因为在队列服务响应请求之后，可能添加或删除了消息。

```python
metadata = queue_service.get_queue_metadata('taskqueue')
count = metadata.approximate_message_count
```

## <a name="delete-a-queue"></a>删除队列

To delete a queue and all the messages contained in it, call the [delete_queue](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#delete-queue-queue-name--fail-not-exist-false--timeout-none-) method.

```python
queue_service.delete_queue('taskqueue')
```

## <a name="next-steps"></a>后续步骤

Now that you've learned the basics of queue storage, follow these links to learn more.

* [Azure Queues Python API reference](/python/api/azure-storage-queue)
* [Python 开发人员中心](https://azure.microsoft.com/develop/python/)
* [Azure 存储空间服务 REST API](https://msdn.microsoft.com/library/azure/dd179355)

[Azure Storage Team Blog]: https://blogs.msdn.com/b/windowsazurestorage/
[用于 Python 的 Microsoft Azure 存储 SDK]: https://github.com/Azure/azure-storage-python
