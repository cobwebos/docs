---
title: 如何通过 Python 使用 Azure 队列存储-Azure 存储
description: 了解如何通过 Python 使用 Azure 队列服务创建和删除队列，以及插入、获取和删除消息。
author: mhopkins-msft
ms.service: storage
ms.author: mhopkins
ms.date: 09/17/2019
ms.subservice: queues
ms.topic: conceptual
ms.reviewer: cbrooks
ms.openlocfilehash: 18333d3da0bb444ea236a4fbda4d6b72d7647053
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/17/2019
ms.locfileid: "71059056"
---
# <a name="how-to-use-azure-queue-storage-from-python"></a>如何通过 Python 使用 Azure 队列存储

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>概述

本指南演示如何使用 Azure 队列存储服务执行常见方案。 这些示例用 Python 编写并使用[Microsoft Azure Storage SDK for Python]。 介绍的方案包括插入、查看、获取和删除队列消息以及创建和删除队列。 有关队列的详细信息，请参阅[后续步骤](#next-steps)部分。

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="download-and-install-azure-storage-sdk-for-python"></a>下载并安装用于 Python 的 Azure 存储 SDK

[用于 python 的 Azure 存储 SDK](https://github.com/azure/azure-storage-python)需要 Python 版本2.7、3.3 或更高版本。
 
### <a name="install-via-pypi"></a>通过 PyPi 安装

要通过 Python 包索引 (PyPI) 安装，请键入：

```bash
pip install azure-storage-queue
```

> [!NOTE]
> 如果要从适用于 Python 的 Azure 存储 SDK 版本 0.36 或更早版本升级，请在安装最新软件包之前使用 `pip uninstall azure-storage` 卸载旧版 SDK。

有关备用安装方法，请参阅[适用于 Python 的 Azure 存储 SDK](https://github.com/Azure/azure-storage-python/)。

## <a name="view-the-sample-application"></a>查看示例应用程序

若要查看和运行示例应用程序，该应用程序演示如何将 Python 与 Azure 队列配合使用，请参阅 [Azure Storage:Getting Started with Azure Queues in Python](https://github.com/Azure-Samples/storage-queue-python-getting-started)（Azure 存储：开始在 Python 中使用 Azure 队列）。 

若要运行示例应用程序，请确保已安装 `azure-storage-queue` 和 `azure-storage-common` 软件包。

## <a name="create-a-queue"></a>创建队列

可以通过 [QueueService](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice) 对象来处理队列。 下面的代码创建一个`QueueService`对象。 在希望在其中以编程方式访问 Azure 存储的任何 Python 文件中，将以下代码添加到文件的顶部附近：

```python
from azure.storage.queue import QueueService
```

以下代码使用存储帐户名称和帐户密钥创建一个 `QueueService` 对象。 将*我的帐户*和*mykey*替换为你的帐户名和密钥。

```python
queue_service = QueueService(account_name='myaccount', account_key='mykey')

queue_service.create_queue('taskqueue')
```

## <a name="insert-a-message-into-a-queue"></a>在队列中插入消息

若要在队列中插入消息，请使用[put_message](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#put-message-queue-name--content--visibility-timeout-none--time-to-live-none--timeout-none-)方法创建一条新消息并将其添加到队列中。

```python
queue_service.put_message('taskqueue', u'Hello World')
```

Azure 队列消息作为文本存储。 如果要存储二进制数据，请在将消息放入队列之前，对队列服务对象设置 Base64 编码和解码函数。

```python
# setup queue Base64 encoding and decoding functions
queue_service.encode_function = QueueMessageFormat.binary_base64encode
queue_service.decode_function = QueueMessageFormat.binary_base64decode
```

## <a name="peek-at-the-next-message"></a>扫视下一条消息

通过调用[peek_messages](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#peek-messages-queue-name--num-messages-none--timeout-none-)方法，可以查看队列前面的消息，而不必从队列中将其删除。 默认情况下`peek_messages` ，扫视单个消息。

```python
messages = queue_service.peek_messages('taskqueue')
for message in messages:
    print(message.content)
```

## <a name="dequeue-messages"></a>取消消息的排队

代码分两步从队列中删除消息。 调用[get_messages](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#get-messages-queue-name--num-messages-none--visibility-timeout-none--timeout-none-)时，默认情况下会获取队列中的下一条消息。 从 `get_messages` 返回的消息对于从此队列读取消息的任何其他代码都是不可见的。 默认情况下，此消息将持续 30 秒不可见。 若要完成从队列中删除消息，还必须调用 [delete_message](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#delete-message-queue-name--message-id--pop-receipt--timeout-none-)。 此删除消息的两步过程可确保当代码因硬件或软件故障而无法处理消息时，其他代码实例可以获取同一消息并重试。 代码在处理`delete_message`消息后立即调用。

```python
messages = queue_service.get_messages('taskqueue')
for message in messages:
    print(message.content)
    queue_service.delete_message('taskqueue', message.id, message.pop_receipt)
```

可以通过两种方式自定义队列中的消息检索。 首先，可以获取一批消息（最多 32 个）。 其次，可以设置更长或更短的不可见超时时间，从而允许代码使用更多或更少时间来完全处理每个消息。 下面的代码示例使用`get_messages`方法在一次调用中获取16条消息。 然后，使用 for 循环处理每条消息。 它还将每条消息的不可见超时时间设置为 5 分钟。

```python
messages = queue_service.get_messages(
    'taskqueue', num_messages=16, visibility_timeout=5*60)
for message in messages:
    print(message.content)
    queue_service.delete_message('taskqueue', message.id, message.pop_receipt)
```

## <a name="change-the-contents-of-a-queued-message"></a>更改已排队消息的内容

可以更改队列中现有消息的内容。 如果消息表示工作任务，则可以使用此功能来更新该工作任务的状态。 下面的代码使用[update_message](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#update-message-queue-name--message-id--pop-receipt--visibility-timeout--content-none--timeout-none-)方法来更新消息。 可见性超时设为 0，这意味着消息会立刻出现且内容将更新。

```python
messages = queue_service.get_messages('taskqueue')
for message in messages:
    queue_service.update_message(
        'taskqueue', message.id, message.pop_receipt, 0, u'Hello World Again')
```

## <a name="get-the-queue-length"></a>获取队列长度

可以获取队列中消息的估计数。 [Get_queue_metadata](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#get-queue-metadata-queue-name--timeout-none-)方法要求队列服务返回有关队列的元数据和`approximate_message_count`。 结果仅是近似值，因为在队列服务响应请求之后，可能添加或删除了消息。

```python
metadata = queue_service.get_queue_metadata('taskqueue')
count = metadata.approximate_message_count
```

## <a name="delete-a-queue"></a>删除队列

若要删除队列及其中包含的所有消息，请调用[delete_queue](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#delete-queue-queue-name--fail-not-exist-false--timeout-none-)方法。

```python
queue_service.delete_queue('taskqueue')
```

## <a name="next-steps"></a>后续步骤

现在，你已了解有关队列存储的基础知识，单击下面的链接可了解详细信息。

* [Azure 队列 Python API 参考](/python/api/azure-storage-queue)
* [Python 开发人员中心](https://azure.microsoft.com/develop/python/)
* [Azure 存储空间服务 REST API](https://msdn.microsoft.com/library/azure/dd179355)

[Azure Storage Team Blog]: https://blogs.msdn.com/b/windowsazurestorage/
[Microsoft Azure Storage SDK for Python]: https://github.com/Azure/azure-storage-python
