---
title: 使用 Python 发送或接收事件-Azure 事件中心 |Microsoft Docs
description: 本文提供用于创建将事件发送到 Azure 事件中心的 Python 应用程序的演练。
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 01/08/2020
ms.author: spelluru
ms.openlocfilehash: a137f274744a6acec22d036f9f4c5c4a5174cc14
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/14/2020
ms.locfileid: "75942589"
---
# <a name="send-events-to-or-receive-events-from-event-hubs-using-python"></a>使用 Python 向事件中心发送事件或接收事件

Azure 事件中心是一个大数据流式处理平台和事件引入服务，每秒能够接收和处理数百万个事件。 事件中心可以处理和存储分布式软件和设备生成的事件、数据或遥测。 可以使用任何实时分析提供程序或批处理/存储适配器转换和存储发送到数据中心的数据。 有关事件中心的详细概述，请参阅[事件中心概述](event-hubs-about.md)和[事件中心功能](event-hubs-features.md)。

本教程介绍如何创建用于将事件发送到事件中心或从事件中心接收事件的 Python 应用程序。 

> [!IMPORTANT]
> 此快速入门使用 Azure 事件中心 Python SDK 版本5。 有关使用 Python SDK 旧版本1的快速入门，请参阅[此文](event-hubs-python-get-started-send.md)。 如果你使用的是版本1的 SDK，我们建议你将代码迁移到最新版本。 有关详细信息，请参阅[迁移指南](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/migration_guide.md)。


## <a name="prerequisites"></a>必备组件

若要完成本教程，需要具备以下先决条件：

- Azure 订阅。 如果没有订阅，请在开始之前[创建一个免费帐户](https://azure.microsoft.com/free/)。
- 按照[快速入门：使用 Azure 门户创建事件中心](event-hubs-create.md)中的说明创建的活动事件中心命名空间和事件中心。 记下命名空间和事件中心名称，以便稍后在本演练中使用。 
- 事件中心命名空间的共享访问密钥名称和主密钥值。 按照[获取连接字符串](event-hubs-get-connection-string.md#get-connection-string-from-the-portal)中的说明获取访问密钥名称和值。 默认访问密钥名称为 **RootManageSharedAccessKey**。 复制密钥名称和主密钥值，以便稍后在本演练中使用。 
- Python 2.7、3.5 或更高版本，安装和更新 `pip`。
- 事件中心的 Python 包。 若要安装该包，请在包路径中包含 Python 的命令提示符中运行以下命令： 
    
    ```cmd
    pip install azure-eventhub
    ```

    安装此包以使用 Azure Blob 存储作为检查点存储来接收事件。 

    ```cmd
    pip install azure-eventhub-checkpointstoreblobaio
    ```

## <a name="send-events"></a>发送事件
在本部分中，将创建一个 Python 脚本，用于将事件发送到之前创建的事件中心。 

1. 打开偏好的 Python 编辑器，例如 [Visual Studio Code](https://code.visualstudio.com/)。
2. 创建名为 send.py 的脚本。 此脚本将一批事件发送到之前创建的事件中心。 
3. 将以下代码粘贴到 send.py 中。 有关详细信息，请参阅代码注释。 

    ```python
    import asyncio
    from azure.eventhub.aio import EventHubProducerClient
    from azure.eventhub import EventData
    
    async def run():
        # create a producer client to send messages to the event hub
        # specify connection string to your event hubs namespace and 
            # the event hub name
        producer = EventHubProducerClient.from_connection_string(conn_str="EVENT HUBS NAMESPACE - CONNECTION STRING", eventhub_name="EVENT HUB NAME")
        async with producer:
            # create a batch
            event_data_batch = await producer.create_batch()
    
            # add events to the batch
            event_data_batch.add(EventData('First event '))
            event_data_batch.add(EventData('Second event'))
            event_data_batch.add(EventData('Third event'))
    
            # send the batch of events to the event hub
            await producer.send_batch(event_data_batch)
    
    loop = asyncio.get_event_loop()
    loop.run_until_complete(run())
    
    ```

    > [!NOTE]
    > 有关包含非常有用的注释的完整源代码，请参阅[GitHub 上的此文件](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/samples/async_samples/send_async.py)

## <a name="receive-events"></a>接收事件
此快速入门使用 Azure Blob 存储作为检查点存储。 检查点存储用于持久保存检查点（上一个读取位置）。  

### <a name="create-an-azure-storage-and-a-blob-container"></a>创建 Azure 存储和 blob 容器
按照以下步骤创建 Azure 存储帐户中的 blob 容器。 

1. [创建 Azure 存储帐户](../storage/common/storage-quickstart-create-account.md?tabs=azure-portal)
2. [创建一个 blob 容器](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)
3. [获取存储帐户的连接字符串](../storage/common/storage-configure-connection-string.md?#view-and-copy-a-connection-string)

    记下连接字符串和容器名称。 您将在接收代码中使用它们。 


### <a name="create-python-script-to-receive-events"></a>创建用于接收事件的 Python 脚本

在本部分中，将创建用于从事件中心接收事件的 Python 脚本：

1. 打开偏好的 Python 编辑器，例如 [Visual Studio Code](https://code.visualstudio.com/)。
2. 创建名为 recv.py 的脚本。
3. 将以下代码粘贴到 recv.py 中。 有关详细信息，请参阅代码注释。 

    ```python
    import asyncio
    from azure.eventhub.aio import EventHubConsumerClient
    from azure.eventhub.extensions.checkpointstoreblobaio import BlobCheckpointStore
    
    
    async def on_event(partition_context, event):
        # print the event data 
        print("Received the event: \"{}\" from the partition with ID: \"{}\"".format(event.body_as_str(encoding='UTF-8'), partition_context.partition_id))
    
        # update the checkpoint so that the program doesn't read the events 
        # that it has already read when you run it next time
        await partition_context.update_checkpoint(event)
    
    async def main():
        # create an Azure blob checkpoint store to store the checkpoints 
        checkpoint_store = BlobCheckpointStore.from_connection_string("AZURE STORAGE CONNECTION STRING", "BLOB CONTAINER NAME")
    
        # create a consumer client for the event hub
        client = EventHubConsumerClient.from_connection_string("EVENT HUBS NAMESPACE CONNECTION STRING", consumer_group="$Default", eventhub_name="EVENT HUB NAME", checkpoint_store=checkpoint_store)
        async with client:
            # call the receive method
            await client.receive(on_event=on_event)
    
    if __name__ == '__main__':
        loop = asyncio.get_event_loop()
        # run the main method
        loop.run_until_complete(main())    
    ```

    > [!NOTE]
    > 有关包含非常有用的注释的完整源代码，请参阅[GitHub 上的此文件](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/samples/async_samples/recv_with_checkpoint_store_async.py)


### <a name="run-the-receiver-app"></a>运行接收器应用

若要运行此脚本，请打开其路径中包含 Python 的命令提示符，然后运行以下命令：

```bash
python recv.py
```

### <a name="run-the-sender-app"></a>运行发送程序应用

若要运行此脚本，请打开其路径中包含 Python 的命令提示符，然后运行以下命令：

```bash
python send.py
```

应会在接收方窗口中看到发送到事件中心的消息。 


## <a name="next-steps"></a>后续步骤
在本快速入门中，你已异步发送和接收事件。 若要了解如何同步发送和接收事件，请参阅[此位置](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples/sync_samples)中的示例。

可在 GitHub 上的[此处](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples)找到所有示例（同步和异步）。 
