---
title: 如何通过 Python 使用 Azure 队列存储 - Azure 存储
description: 了解如何通过 Python 使用 Azure 队列服务创建和删除队列，以及插入、获取和删除消息。
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/25/2020
ms.service: storage
ms.subservice: queues
ms.topic: how-to
ms.reviewer: dineshm
ms.custom: seo-javascript-october2019, devx-track-python
ms.openlocfilehash: 79334db46b6f035aabffcca133ed4f5cb8df3637
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88855562"
---
# <a name="how-to-use-azure-queue-storage-from-python"></a>如何通过 Python 使用 Azure 队列存储

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

## <a name="overview"></a>概述

本文演示使用 Azure 队列存储服务的常见方案。 涵盖的方案包括插入、速览、获取和删除队列消息。 还介绍了用于创建和删除队列的代码。

本文中的示例是用 Python 编写的，使用[用于 Python 的 Azure 队列存储客户端库]。 有关队列的详细信息，请参阅[后续步骤](#next-steps)部分。

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="download-and-install-azure-storage-sdk-for-python"></a>下载并安装用于 Python 的 Azure 存储 SDK

[用于 Python 的 Azure 存储 SDK](https://github.com/azure/azure-storage-python) 需要 Python 2.7、3.3 或更高版本。
 
### <a name="install-via-pypi"></a>通过 PyPI 安装

要通过 Python 包索引 (PyPI) 安装，请键入：

# <a name="python-v12"></a>[Python v12](#tab/python)

```console
pip install azure-storage-queue
```

# <a name="python-v2"></a>[Python v2](#tab/python2)

```console
pip install azure-storage-queue==2.1.0
```

---

> [!NOTE]
> 如果要从适用于 Python 的 Azure 存储 SDK 版本 0.36 或更早版本升级，请在安装最新软件包之前使用 `pip uninstall azure-storage` 卸载旧版 SDK。

有关其他安装方法，请参阅[适用于 Python 的 Azure SDK]。

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="configure-your-application-to-access-queue-storage"></a>配置应用程序以访问队列存储

# <a name="python-v12"></a>[Python v12](#tab/python)

可通过 [QueueClient](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient) 对象来处理队列。 在你希望以编程方式访问服务总线的任何 Python 文件中，将以下代码添加到顶部附近：

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_ImportStatements":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

可以通过 [QueueService](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2) 对象来处理队列。 以下代码创建 `QueueService` 对象。 在你希望以编程方式访问 Azure 存储的任何 Python 文件中，将以下代码添加到顶部附近：

```python
from azure.storage.queue import (
        QueueService, 
        QueueMessageFormat
)

import os, uuid
```

---

`os` 包支持检索环境变量。 `uuid` 包支持为队列名称生成唯一标识符。

## <a name="create-a-queue"></a>创建队列

连接字符串是从前面设置的 `AZURE_STORAGE_CONNECTION_STRING` 环境变量检索的。

# <a name="python-v12"></a>[Python v12](#tab/python)

以下代码使用存储连接字符串创建 `QueueClient` 对象。

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_CreateQueue":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

以下代码使用存储连接字符串创建 `QueueService` 对象。

```python
# Retrieve the connection string from an environment
# variable named AZURE_STORAGE_CONNECTION_STRING
connect_str = os.getenv("AZURE_STORAGE_CONNECTION_STRING")

# Create a unique name for the queue
queue_name = "queue-" + str(uuid.uuid4())

# Create a QueueService object which will
# be used to create and manipulate the queue
print("Creating queue: " + queue_name)
queue_service = QueueService(connection_string=connect_str)

# Create the queue
queue_service.create_queue(queue_name)
```

---

## <a name="insert-a-message-into-a-queue"></a>在队列中插入消息

# <a name="python-v12"></a>[Python v12](#tab/python)

若要在队列中插入消息，请使用 [send_message](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#send-message-content----kwargs-) 方法。

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_AddMessage":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

若要在队列中插入消息，可使用 [put_message](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2#put-message-queue-name--content--visibility-timeout-none--time-to-live-none--timeout-none-) 方法创建一条新消息并将其添加到队列中。

```python
message = u"Hello World"
print("Adding message: " + message)
queue_service.put_message(queue_name, message)
```

---

Azure 队列消息以文本形式存储。 如果要存储二进制数据，请在将消息放入队列之前设置 Base64 编码和解码函数。

# <a name="python-v12"></a>[Python v12](#tab/python)

在队列客户端对象上配置 Base64 编码和解码函数。

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_EncodeMessage":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

在队列服务对象上配置 Base64 编码和解码函数。

```python
# Setup Base64 encoding and decoding functions
queue_service.encode_function = QueueMessageFormat.binary_base64encode
queue_service.decode_function = QueueMessageFormat.binary_base64decode
```

---

## <a name="peek-at-messages"></a>扫视消息

# <a name="python-v12"></a>[Python v12](#tab/python)

可以通过调用 [peek_messages](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#peek-messages-max-messages-none----kwargs-) 方法来速览消息，而不必将其从队列中删除。 默认情况下，`peek_messages` 扫视单条消息。

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_PeekMessage":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

可以通过调用 [peek_messages](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2#peek-messages-queue-name--num-messages-none--timeout-none-) 方法来速览消息，而不必将其从队列中删除。 默认情况下，`peek_messages` 扫视单条消息。

```python
messages = queue_service.peek_messages(queue_name)

for peeked_message in messages:
    print("Peeked message: " + peeked_message.content)
```

---

## <a name="change-the-contents-of-a-queued-message"></a>更改已排队消息的内容

可以更改队列中现有消息的内容。 如果消息表示某个任务，则可以使用此功能来更新该任务的状态。

# <a name="python-v12"></a>[Python v12](#tab/python)

以下代码使用 [update_message](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#update-message-message--pop-receipt-none--content-none----kwargs-) 方法来更新消息。 可见性超时设为 0，这意味着消息会立刻出现且内容将更新。

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_ChangeMessage":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

以下代码使用 [update_message](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2#update-message-queue-name--message-id--pop-receipt--visibility-timeout--content-none--timeout-none-) 方法来更新消息。 可见性超时设为 0，这意味着消息会立刻出现且内容将更新。

```python
messages = queue_service.get_messages(queue_name)

for message in messages:
    queue_service.update_message(
        queue_name, message.id, message.pop_receipt, 0, u"Hello World Again")
```

---

## <a name="get-the-queue-length"></a>获取队列长度

可以获取队列中消息的估计数。

# <a name="python-v12"></a>[Python v12](#tab/python)

[get_queue_properties](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#get-queue-properties---kwargs-) 方法要求队列服务返回有关队列的属性，包括 `approximate_message_count`。

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_GetQueueLength":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

[get_queue_metadata](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2#get-queue-metadata-queue-name--timeout-none-) 方法要求队列服务返回有关队列的元数据，包括 `approximate_message_count`。

```python
metadata = queue_service.get_queue_metadata(queue_name)
count = metadata.approximate_message_count
print("Message count: " + str(count))
```

---

结果仅是近似值，因为在队列服务响应请求之后，可能添加或删除了消息。

## <a name="dequeue-messages"></a>取消消息的排队

通过两个步骤从队列中删除消息。 如果你的代码未能处理消息，此两步过程可确保你可以获取同一消息并重试。 在消息成功处理后调用 `delete_message`。

# <a name="python-v12"></a>[Python v12](#tab/python)

在调用 [receive_messages](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#receive-messages---kwargs-) 时，默认情况下会获得队列中的下一条消息。 从 `receive_messages` 返回的消息对于从此队列读取消息的任何其他代码都是不可见的。 默认情况下，此消息持续 30 秒不可见。 若要完成从队列中删除消息，还必须调用 [delete_message](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#delete-message-message--pop-receipt-none----kwargs-)。

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_DequeueMessages":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

在调用 [get_messages](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2#get-messages-queue-name--num-messages-none--visibility-timeout-none--timeout-none-) 时，默认情况下会获得队列中的下一条消息。 从 `get_messages` 返回的消息对于从此队列读取消息的任何其他代码都是不可见的。 默认情况下，此消息持续 30 秒不可见。 若要完成从队列中删除消息，还必须调用 [delete_message](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2#delete-message-queue-name--message-id--pop-receipt--timeout-none-)。

```python
messages = queue_service.get_messages(queue_name)

for message in messages:
    print("Deleting message: " + message.content)
    queue_service.delete_message(queue_name, message.id, message.pop_receipt)
```

---

可通过两种方式自定义队列中消息的检索。 首先，可获取一批消息（最多 32 条）。 其次，可以设置更长或更短的不可见超时时间，从而允许代码使用更多或更少时间来完全处理每个消息。

# <a name="python-v12"></a>[Python v12](#tab/python)

以下代码示例使用 [receive_messages](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#receive-messages---kwargs-) 方法成批获取消息。 然后，它使用嵌套的 `for` 循环来处理每批中的每条消息。 它还将每条消息的不可见超时时间设置为 5 分钟。

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_DequeueByPage":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

以下代码示例使用 [get_messages](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2#get-messages-queue-name--num-messages-none--visibility-timeout-none--timeout-none-) 方法在一次调用中获取 16 条消息。 然后，使用 `for` 循环处理每条消息。 它还将每条消息的不可见超时时间设置为 5 分钟。

```python
messages = queue_service.get_messages(queue_name, num_messages=16, visibility_timeout=5*60)

for message in messages:
    print("Deleting message: " + message.content)
    queue_service.delete_message(queue_name, message.id, message.pop_receipt)
```

---

## <a name="delete-a-queue"></a>删除队列

# <a name="python-v12"></a>[Python v12](#tab/python)

若要删除队列及其中包含的所有消息，请调用 [delete_queue](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#delete-queue---kwargs-) 方法。

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_DeleteQueue":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

若要删除队列及其中包含的所有消息，请调用 [delete_queue](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2#delete-queue-queue-name--fail-not-exist-false--timeout-none-) 方法。

```python
print("Deleting queue: " + queue_name)
queue_service.delete_queue(queue_name)
```

---

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="next-steps"></a>后续步骤

在了解了队列存储的基础知识后，可单击下面的链接了解详细信息。

* [Azure 队列 Python API 参考](/python/api/azure-storage-queue)
* [Python 开发人员中心](https://azure.microsoft.com/develop/python/)
* [Azure 存储服务 REST API](https://msdn.microsoft.com/library/azure/dd179355)

[用于 Python 的 Azure 队列存储客户端库]: https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-queue
[Azure SDK for Python]: https://github.com/azure/azure-sdk-for-python
[Azure Storage Team Blog]: https://techcommunity.microsoft.com/t5/azure-storage/bg-p/AzureStorageBlog
