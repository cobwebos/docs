---
title: 使用 Python 向/从 Azure 事件中心发送/接收事件（最新版）
description: 本文演练如何创建一个可使用最新 azure/event-hubs 版本 5 包向/从 Azure 事件中心发送/接收事件的 Python 应用程序。
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.date: 02/11/2020
ms.author: spelluru
ms.openlocfilehash: 6b16398c7c1fd53562df7e4ac8e801a8c97162f6
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82159431"
---
# <a name="send-events-to-or-receive-events-from-event-hubs-by-using-python-azure-eventhub-version-5"></a>使用 Python（azure-eventhub 版本 5）向/从事件中心发送/接收事件
本快速入门介绍如何使用 **azure-eventhub 版本 5** Python 包向事件中心发送事件以及从事件中心接收事件。

> [!IMPORTANT]
> 本快速入门使用最新的 azure-eventhub 版本 5 包。 有关使用旧的 azure-eventhub 版本 1 包的快速入门，请参阅[使用 azure-eventhub 版本 1 发送和接收事件](event-hubs-python-get-started-send.md)。 

## <a name="prerequisites"></a>先决条件
如果不熟悉 Azure 事件中心，请在阅读本快速入门之前参阅[事件中心概述](event-hubs-about.md)。 

若要完成本快速入门，需要具备以下先决条件：

- **Microsoft Azure 订阅**。 若要使用 Azure 服务（包括 Azure 事件中心），需要一个订阅。  如果没有现有的 Azure 帐户，可以注册[免费试用](https://azure.microsoft.com/free/)帐户，或者在[创建帐户](https://azure.microsoft.com)时使用 MSDN 订阅者权益。
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
- **创建事件中心命名空间和事件中心**。 第一步是使用 [Azure 门户](https://portal.azure.com)创建事件中心类型的命名空间，并获取应用程序与事件中心进行通信所需的管理凭据。 要创建命名空间和事件中心，请按照[此文](event-hubs-create.md)中的步骤操作。 然后，按照以下文章中的说明获取**事件中心命名空间的连接字符串**：[获取连接字符串](event-hubs-get-connection-string.md#get-connection-string-from-the-portal)。 稍后将在本快速入门中使用连接字符串。

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

> [!NOTE]
> 如果在 Azure Stack Hub 上运行，该平台支持的存储 Blob SDK 版本可能不同于通常在 Azure 上提供的版本。 例如，如果在 [Azure Stack Hub 版本 2002](https://docs.microsoft.com/azure-stack/user/event-hubs-overview) 上运行，则存储服务的最高可用版本为版本 2017-11-09。 在这种情况下，除了执行本部分中的步骤以外，还需要添加相关代码，将存储服务 API 版本 2017-11-09 作为目标。 如需通过示例来了解如何以特定的存储 API 版本为目标，请参阅 GitHub 上的[同步](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob/samples/receive_events_using_checkpoint_store_storage_api_version.py)和[异步](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio/samples/receive_events_using_checkpoint_store_storage_api_version_async.py)示例。 有关 Azure Stack Hub 上支持的 Azure 存储服务版本的详细信息，请参阅 [Azure Stack Hub 存储：差异和注意事项](https://docs.microsoft.com/azure-stack/user/azure-stack-acs-differences)。


### <a name="create-an-azure-storage-account-and-a-blob-container"></a>创建 Azure 存储帐户和 Blob 容器
执行以下步骤，创建 Azure 存储帐户并在其中创建 Blob 容器：

1. [创建 Azure 存储帐户](../storage/common/storage-account-create.md?tabs=azure-portal)
2. [创建一个 blob 容器](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)
3. [获取存储帐户的连接字符串](../storage/common/storage-configure-connection-string.md)

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
