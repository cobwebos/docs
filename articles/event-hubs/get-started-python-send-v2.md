---
title: 使用 Python 向/从 Azure 事件中心发送/接收事件（最新版）
description: 本文演练如何创建一个可使用最新 azure/event-hubs 版本 5 包向/从 Azure 事件中心发送/接收事件的 Python 应用程序。
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.date: 01/30/2020
ms.author: spelluru
ms.openlocfilehash: d977ae9ea8b78664ac1d3a318f58553da696c089
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2020
ms.locfileid: "76906356"
---
# <a name="send-events-to-or-receive-events-from-event-hubs-by-using-python-azure-eventhub-version-5"></a>使用 Python（azure-eventhub 版本 5）向/从事件中心发送/接收事件

Azure 事件中心是一个大数据流式处理平台和事件引入服务，每秒能够接收和处理数百万个事件。 事件中心可以处理和存储分布式软件和设备生成的事件、数据或遥测。 可以使用任何实时分析提供程序或批处理/存储适配器转换和存储发送到事件中心的数据。 有关详细信息，请参阅[事件中心概述](event-hubs-about.md)和[事件中心的功能](event-hubs-features.md)。

本快速入门介绍如何创建可向/从事件中心发送/接收事件的 Python 应用程序。

> [!IMPORTANT]
> 本快速入门使用 Azure 事件中心 Python SDK 版本 5。 有关使用 Python SDK 版本 1 的快速入门，请参阅[此文](event-hubs-python-get-started-send.md)。 

## <a name="prerequisites"></a>必备条件

若要完成本快速入门，需要具备以下先决条件：

- Azure 订阅。 如果没有订阅，请在开始之前[创建一个免费帐户](https://azure.microsoft.com/free/)。
- 有效的事件中心命名空间和事件中心。 若要创建它们，请按照以下文章中的说明操作：[快速入门：使用 Azure 门户创建事件中心](event-hubs-create.md)。 记下命名空间和事件中心名称，以便稍后在本快速入门中使用。
- 事件中心命名空间的共享访问密钥名称和主密钥值。 按照[获取事件中心连接字符串](event-hubs-get-connection-string.md#get-connection-string-from-the-portal)中的说明获取访问密钥名称和值。 默认访问密钥名称为 *RootManageSharedAccessKey*。 记下密钥名称和主密钥值，以便稍后在本快速入门中使用。
- Python 2.7、3.5 或更高版本，装有 PIP 且已更新。
- 事件中心的 Python 包。 

    若要安装该包，请在包路径中包含 Python 的命令提示符中运行以下命令：

    ```cmd
    pip install azure-eventhub
    ```

    安装以下包，以使用 Azure Blob 存储作为检查点存储来接收事件：

    ```cmd
    pip install azure-eventhub-checkpointstoreblob-aio
    ```

## <a name="send-events"></a>发送事件
在本部分，你将创建一个 Python 脚本，用于将事件发送到前面创建的事件中心。

1. 打开常用的 Python 编辑器，如 [Visual Studio Code](https://code.visualstudio.com/)。
2. 创建名为 send.py  的脚本。 此脚本将一批事件发送到前面创建的事件中心。
3. 将以下代码粘贴到 *send.py* 中：

    ```python
    import asyncio
    from azure.eventhub.aio import EventHubProducerClient
    from azure.eventhub import EventData

    async def run():
        # Create a producer client to send messages to the event hub.
        # Specify a connection string to your event hubs namespace and
            # the event hub name.
        producer = EventHubProducerClient.from_connection_string(conn_str="EVENT HUBS NAMESPACE - CONNECTION STRING", eventhub_name="EVENT HUB NAME")
        async with producer:
            # Create a batch.
            event_data_batch = await producer.create_batch()

            # Add events to the batch.
            event_data_batch.add(EventData('First event '))
            event_data_batch.add(EventData('Second event'))
            event_data_batch.add(EventData('Third event'))

            # Send the batch of events to the event hub.
            await producer.send_batch(event_data_batch)

    loop = asyncio.get_event_loop()
    loop.run_until_complete(run())

    ```

    > [!NOTE]
    > 有关完整源代码（包括参考性注释），请参阅 [GitHub send_async.py 页](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/samples/async_samples/send_async.py)。

## <a name="receive-events"></a>接收事件
本快速入门使用 Azure Blob 存储作为检查点存储。 检查点存储用于保存检查点（即，上次读取位置）。  

### <a name="create-an-azure-storage-account-and-a-blob-container"></a>创建 Azure 存储帐户和 Blob 容器
执行以下步骤，创建 Azure 存储帐户并在其中创建 Blob 容器：

1. [创建 Azure 存储帐户](../storage/common/storage-account-create.md?tabs=azure-portal)
2. [创建一个 blob 容器](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)
3. [获取存储帐户的连接字符串](../storage/common/storage-configure-connection-string.md?#view-and-copy-a-connection-string)

请务必记下连接字符串和容器名称，供稍后在接收代码中使用。


### <a name="create-a-python-script-to-receive-events"></a>创建用于接收事件的 Python 脚本

在本部分，你将创建一个 Python 脚本用于从事件中心接收事件：

1. 打开常用的 Python 编辑器，如 [Visual Studio Code](https://code.visualstudio.com/)。
2. 创建名为 recv.py 的脚本  。
3. 将以下代码粘贴到 *recv.py* 中：

    ```python
    import asyncio
    from azure.eventhub.aio import EventHubConsumerClient
    from azure.eventhub.extensions.checkpointstoreblobaio import BlobCheckpointStore


    async def on_event(partition_context, event):
        # Print the event data.
        print("Received the event: \"{}\" from the partition with ID: \"{}\"".format(event.body_as_str(encoding='UTF-8'), partition_context.partition_id))

        # Update the checkpoint so that the program doesn't read the events
        # that it has already read when you run it next time.
        await partition_context.update_checkpoint(event)

    async def main():
        # Create an Azure blob checkpoint store to store the checkpoints.
        checkpoint_store = BlobCheckpointStore.from_connection_string("AZURE STORAGE CONNECTION STRING", "BLOB CONTAINER NAME")

        # Create a consumer client for the event hub.
        client = EventHubConsumerClient.from_connection_string("EVENT HUBS NAMESPACE CONNECTION STRING", consumer_group="$Default", eventhub_name="EVENT HUB NAME", checkpoint_store=checkpoint_store)
        async with client:
            # Call the receive method.
            await client.receive(on_event=on_event)

    if __name__ == '__main__':
        loop = asyncio.get_event_loop()
        # Run the main method.
        loop.run_until_complete(main())    
    ```

    > [!NOTE]
    > 有关完整源代码（包括附加的参考注释），请转到 [GitHub recv_with_checkpoint_store_async.py 页](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/samples/async_samples/recv_with_checkpoint_store_async.py)。


### <a name="run-the-receiver-app"></a>运行接收器应用

若要运行此脚本，请打开其路径中包含 Python 的命令提示符，然后运行以下命令：

```bash
python recv.py
```

### <a name="run-the-sender-app"></a>运行发送器应用

若要运行此脚本，请打开其路径中包含 Python 的命令提示符，然后运行以下命令：

```bash
python send.py
```

接收器窗口应会显示已发送到事件中心的消息。


## <a name="next-steps"></a>后续步骤
在本快速入门中，你以异步方式发送和接收了事件。 若要了解如何以同步方式发送和接收事件，请参阅 [GitHub sync_samples 页](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples/sync_samples)。

有关 GitHub 上的所有示例（包括同步和异步），请参阅[适用于 Python 的 Azure 事件中心客户端库示例](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples)。
