---
title: 如何通过 Python 使用 Azure 队列存储 2.1-Azure 存储
description: 了解如何使用 Python 中的 Azure 队列服务2.1 来创建和删除队列，以及插入、获取和删除消息。
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
# <a name="how-to-use-azure-queue-storage-v21-from-python"></a>如何通过 Python 使用 Azure 队列存储2。1

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

本文演示使用 Azure 队列存储服务的常见方案。 介绍的方案包括插入、扫视、获取和删除队列消息以及创建和删除队列。

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>概述

本文中的示例用 Python 编写并使用[用于 Python 的 Microsoft Azure 存储 SDK]。 有关队列的详细信息，请参阅[后续步骤](#next-steps)部分。

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="download-and-install-azure-storage-sdk-for-python"></a>下载并安装用于 Python 的 Azure 存储 SDK

[用于 Python 的 Azure 存储 SDK](https://github.com/azure/azure-storage-python) 需要 Python 2.7、3.3 或更高版本。
 
### <a name="install-via-pypi"></a>通过 PyPi 安装

要通过 Python 包索引 (PyPI) 安装，请键入：

```bash
pip install azure-storage-blob==2.1.0
```

> [!NOTE]
> 如果要从适用于 Python 的 Azure 存储 SDK 版本 0.36 或更早版本升级，请在安装最新软件包之前使用 `pip uninstall azure-storage` 卸载旧版 SDK。

有关备用安装方法，请参阅[适用于 Python 的 Azure 存储 SDK](https://github.com/Azure/azure-storage-python/)。

## <a name="view-the-sample-application"></a>查看示例应用程序

若要查看并运行演示如何结合使用 Python 和 Azure 队列的示例应用程序，请参阅[Azure 存储：在 Python 中使用 Azure 队列入门](https://github.com/Azure-Samples/storage-queue-python-getting-started)。 

若要运行示例应用程序，请确保已安装 `azure-storage-queue` 和 `azure-storage-common` 软件包。

## <a name="create-a-queue"></a>创建队列

可以通过 [QueueService](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice) 对象来处理队列。 以下代码创建 `QueueService` 对象。 在希望在其中以编程方式访问 Azure 存储的任何 Python 文件中，将以下代码添加到文件的顶部附近：

```python
from azure.storage.queue import QueueService
```

以下代码使用存储帐户名称和帐户密钥创建一个 `QueueService` 对象。 将 myaccount 和 mykey 替换为帐户名称和密钥。

```python
queue_service = QueueService(account_name='myaccount', account_key='mykey')

queue_service.create_queue('taskqueue')
```

## <a name="insert-a-message-into-a-queue"></a>在队列中插入消息

若要在队列中插入消息，可使用 [put_message](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#put-message-queue-name--content--visibility-timeout-none--time-to-live-none--timeout-none-) 方法创建一条新消息并将其添加到队列中。

```python
queue_service.put_message('taskqueue', u'Hello World')
```

Azure 队列消息以文本形式存储。 如果要存储二进制数据，请在将消息放入队列之前，在队列服务对象上设置 Base64 编码和解码函数。

```python
# setup queue Base64 encoding and decoding functions
queue_service.encode_function = QueueMessageFormat.binary_base64encode
queue_service.decode_function = QueueMessageFormat.binary_base64decode
```

## <a name="peek-at-the-next-message"></a>扫视下一条消息

通过调用 [peek_messages](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#peek-messages-queue-name--num-messages-none--timeout-none-) 方法，可以扫视队列最前面的消息，而不必从该队列中将其删除。 默认情况下，`peek_messages` 扫视单条消息。

```python
messages = queue_service.peek_messages('taskqueue')
for message in messages:
    print(message.content)
```

## <a name="dequeue-messages"></a>取消消息的排队

代码分两步从队列中删除消息。 在调用 [get_messages](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#get-messages-queue-name--num-messages-none--visibility-timeout-none--timeout-none-) 时，默认情况下会获得队列中的下一条消息。 从 `get_messages` 返回的消息对于从此队列读取消息的任何其他代码都是不可见的。 默认情况下，此消息将持续 30 秒不可见。 若要完成从队列中删除消息，还必须调用 [delete_message](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#delete-message-queue-name--message-id--pop-receipt--timeout-none-)。 此删除消息的两步过程可确保当代码因硬件或软件故障而无法处理消息时，其他代码实例可以获取同一消息并重试。 代码在处理消息后会立即调用 `delete_message`。

```python
messages = queue_service.get_messages('taskqueue')
for message in messages:
    print(message.content)
    queue_service.delete_message('taskqueue', message.id, message.pop_receipt)
```

可以通过两种方式自定义队列中的消息检索。 首先，可以获取一批消息（最多 32 个）。 其次，可以设置更长或更短的不可见超时时间，从而允许代码使用更多或更少时间来完全处理每个消息。 以下代码示例使用 `get_messages` 方法在一次调用中获取 16 条消息。 然后，使用 for 循环处理每条消息。 它还将每条消息的不可见超时时间设置为 5 分钟。

```python
messages = queue_service.get_messages(
    'taskqueue', num_messages=16, visibility_timeout=5*60)
for message in messages:
    print(message.content)
    queue_service.delete_message('taskqueue', message.id, message.pop_receipt)
```

## <a name="change-the-contents-of-a-queued-message"></a>更改已排队消息的内容

可以更改队列中现有消息的内容。 如果消息表示工作任务，则可以使用此功能来更新该工作任务的状态。 以下代码使用 [update_message](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#update-message-queue-name--message-id--pop-receipt--visibility-timeout--content-none--timeout-none-) 方法来更新消息。 可见性超时设为 0，这意味着消息会立刻出现且内容将更新。

```python
messages = queue_service.get_messages('taskqueue')
for message in messages:
    queue_service.update_message(
        'taskqueue', message.id, message.pop_receipt, 0, u'Hello World Again')
```

## <a name="get-the-queue-length"></a>获取队列长度

可以获取队列中消息的估计数。 [get_queue_metadata](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#get-queue-metadata-queue-name--timeout-none-) 方法要求队列服务返回有关队列的元数据和 `approximate_message_count`。 结果仅是近似值，因为在队列服务响应请求之后，可能添加或删除了消息。

```python
metadata = queue_service.get_queue_metadata('taskqueue')
count = metadata.approximate_message_count
```

## <a name="delete-a-queue"></a>删除队列

若要删除队列及其中包含的所有消息，请调用 [delete_queue](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#delete-queue-queue-name--fail-not-exist-false--timeout-none-) 方法。

```python
queue_service.delete_queue('taskqueue')
```

## <a name="next-steps"></a>后续步骤

在了解了队列存储的基础知识后，可单击下面的链接了解详细信息。

* [Azure 队列 Python API 参考](/python/api/azure-storage-queue)
* [Python 开发人员中心](https://azure.microsoft.com/develop/python/)
* [Azure 存储服务 REST API](https://msdn.microsoft.com/library/azure/dd179355)

[Azure Storage Team Blog]: https://blogs.msdn.com/b/windowsazurestorage/
[用于 Python 的 Microsoft Azure 存储 SDK]: https://github.com/Azure/azure-storage-python
