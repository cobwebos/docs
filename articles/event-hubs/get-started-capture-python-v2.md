---
title: 从 Python 应用读取 Azure 事件中心捕获的数据 |Microsoft Docs
description: 本文介绍如何编写 Python 代码以捕获发送到事件中心的数据，并从 Azure 存储读取捕获的事件数据。
services: event-hubs
documentationcenter: ''
author: spelluru
editor: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2019
ms.author: spelluru
ms.openlocfilehash: 43223f7cb9ed254340c99d235d494d1e93583c7f
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/21/2020
ms.locfileid: "76293532"
---
# <a name="capture-event-hubs-data-in-azure-storage-and-read-it-using-python"></a>在 Azure 存储中捕获事件中心数据，并使用 Python 进行读取 
可以使用配置事件中心，以便在 Azure 存储或 Azure Data Lake Storage 中捕获发送到事件中心的数据。 本文介绍如何使用写入 Python 代码将事件发送到事件中心，并从 Azure blob 存储读取捕获的数据。 有关此功能的详细信息，请参阅[事件中心捕获功能概述](event-hubs-capture-overview.md)。

此示例使用 [Azure Python SDK](https://azure.microsoft.com/develop/python/) 来演示捕获功能。 Sender.py 程序以 JSON 格式将模拟的环境遥测数据发送到事件中心。 事件中心配置为使用捕获功能将此数据成批地写入到 Blob 存储。 capturereader.py 应用读取这些 blob，为每个设备创建一个附加文件。 然后该应用将数据写入到 .csv 文件中。

> [!IMPORTANT]
> 此快速入门使用 Azure 事件中心 Python SDK 版本5。 有关使用 Python SDK 旧版本1的快速入门，请参阅[此文](event-hubs-capture-python.md)。 如果你使用的是版本1的 SDK，我们建议你将代码迁移到最新版本。 有关详细信息，请参阅[迁移指南](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/migration_guide.md)。

在本快速入门中，请执行以下操作： 

> [!div class="checklist"]
> * 在 Azure 门户中创建 Azure Blob 存储帐户和容器。
> * 使用 Azure 门户创建事件中心命名空间。
> * 创建启用了捕获功能的事件中心，并将其连接到存储帐户。
> * 使用 Python 脚本将数据发送到事件中心。
> * 使用另一个 Python 脚本通过事件中心捕获功能读取并处理文件。

## <a name="prerequisites"></a>必备组件

- Python 2.7、3.5 或更高版本，安装和更新 `pip`。
- Azure 订阅。 如果没有订阅，请在开始之前[创建一个免费帐户](https://azure.microsoft.com/free/)。
- [在命名空间中创建事件中心命名空间和事件中心](event-hubs-create.md)。 记下事件中心命名空间的名称、事件中心的名称和命名空间的主访问密钥。 按照 "[获取连接字符串](event-hubs-get-connection-string.md#get-connection-string-from-the-portal)" 一文中的说明获取访问密钥。 默认密钥名称为： **RootManageSharedAccessKey**。 本教程不需要连接字符串。 只需主密钥。 
- 按照以下步骤创建**Azure 存储帐户**和**blob 容器**：
    1. [创建 Azure 存储帐户](../storage/common/storage-quickstart-create-account.md?tabs=azure-portal)。
    2. [在存储中创建 blob 容器](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)。 
    3. [获取存储帐户的连接字符串](../storage/common/storage-configure-connection-string.md#view-and-copy-a-connection-string)。

        记下**连接字符串**和**容器名称**。 稍后将在代码中使用它们。 
- 按照以下说明启用事件中心的**捕获**功能：[使用 Azure 门户启用事件中心捕获](event-hubs-capture-enable-through-portal.md)。 选择你在上一步中创建的存储帐户和 blob 容器。 你还可以在创建事件中心时启用该功能。 

## <a name="create-a-python-script-to-send-events-to-your-event-hub"></a>创建用于将事件发送到事件中心的 Python 脚本
在本部分中，将创建一个 Python 脚本，用于将200事件（10个设备 * 20 个事件）发送到事件中心。 这些事件是以 JSON 格式发送的示例环境阅读。 

1. 打开常用的 Python 编辑器，如 [Visual Studio Code][Visual Studio Code]。
2. 创建名为 **sender.py** 的脚本。 
3. 将以下代码粘贴到 sender.py 中。 有关详细信息，请参阅代码注释。 
   
    ```python
    import time
    import os
    import uuid
    import datetime
    import random
    import json
    
    from azure.eventhub import EventHubProducerClient, EventData
    
    # this scripts simulates production of events for 10 devices
    devices = []
    for x in range(0, 10):
        devices.append(str(uuid.uuid4()))
    
    # create a producer client to produce/publish events to the event hub
    producer = EventHubProducerClient.from_connection_string(conn_str="EVENT HUBS NAMESAPCE CONNECTION STRING", eventhub_name="EVENT HUB NAME")
    
    for y in range(0,20):    # for each device, produce 20 events 
        event_data_batch = producer.create_batch() # create a batch. you will add events to the batch later. 
        for dev in devices:
            # create a dummy reading
            reading = {'id': dev, 'timestamp': str(datetime.datetime.utcnow()), 'uv': random.random(), 'temperature': random.randint(70, 100), 'humidity': random.randint(70, 100)}
            s = json.dumps(reading) # convert reading into a JSON string
            event_data_batch.add(EventData(s)) # add event data to the batch
        producer.send_batch(event_data_batch) # send the batch of events to the event hub
    
    # close the producer    
    producer.close()
    ```
4. 替换脚本中的以下值：
    1. 将 `EVENT HUBS NAMESPACE CONNECTION STRING` 替换为事件中心命名空间的连接字符串。
    2. 将 `EVENT HUB NAME` 替换为事件中心的名称。 
5. 运行脚本，将事件发送到事件中心。 
6. 在 Azure 门户中，可以验证事件中心是否已收到消息。 切换到 "**指标**" 部分中的 "**消息**" 视图。 刷新页面以更新图表。 它可能需要几秒钟时间才能显示收到的消息。 

    [![验证事件中心是否收到消息](./media/get-started-capture-python-v2/messages-portal.png)](./media/get-started-capture-python-v2/messages-portal.png#lightbox)

## <a name="create-a-python-script-to-read-your-capture-files"></a>创建用于读取捕获文件的 Python 脚本
在此示例中，捕获的数据存储在 Azure Blob 存储中。 本部分中的脚本从你的 Azure 存储中读取捕获数据文件，并生成 CSV 文件，以便你可以轻松地打开和查看内容。 你将在应用程序的当前工作目录中看到10个文件。 这些文件将包含10个设备的环境读数。 

1. 在 Python 编辑器中，创建一个名为**capturereader.py**的脚本。 此脚本读取捕获的文件，并为每个设备创建一个文件，用于仅写入该设备的数据。
2. 将以下代码粘贴到 capturereader.py 中。 有关详细信息，请参阅代码注释。 
   
    ```python
    import os
    import string
    import json
    import uuid
    import avro.schema
    
    from azure.storage.blob import ContainerClient, BlobClient
    from avro.datafile import DataFileReader, DataFileWriter
    from avro.io import DatumReader, DatumWriter
    
    
    def processBlob2(filename):
        reader = DataFileReader(open(filename, 'rb'), DatumReader())
        dict = {}
        for reading in reader:
            parsed_json = json.loads(reading["Body"])
            if not 'id' in parsed_json:
                return
            if not parsed_json['id'] in dict:
                list = []
                dict[parsed_json['id']] = list
            else:
                list = dict[parsed_json['id']]
                list.append(parsed_json)
        reader.close()
        for device in dict.keys():
            filename = os.getcwd() + '\\' + str(device) + '.csv'
            deviceFile = open(filename, "a")
            for r in dict[device]:
                deviceFile.write(", ".join([str(r[x]) for x in r.keys()])+'\n')
    
    def startProcessing():
        print('Processor started using path: ' + os.getcwd())
        # create a blob container client
        container = ContainerClient.from_connection_string("AZURE STORAGE CONNECTION STRING", container_name="BLOB CONTAINER NAME")
        blob_list = container.list_blobs() # list all the blobs in the container
        for blob in blob_list:
            #content_length == 508 is an empty file, so only process content_length > 508 (skip empty files)        
            if blob.size > 508:
                print('Downloaded a non empty blob: ' + blob.name)
                # create a blob client for the blob
                blob_client = ContainerClient.get_blob_client(container, blob=blob.name)
                # construct a file name based on the blob name
                cleanName = str.replace(blob.name, '/', '_')
                cleanName = os.getcwd() + '\\' + cleanName 
                with open(cleanName, "wb+") as my_file: # open the file to write. create if it doesn't exist. 
                    my_file.write(blob_client.download_blob().readall()) # write blob contents into the file
                processBlob2(cleanName) # convert the file into a CSV file
                os.remove(cleanName) # remove the original downloaded file
                # delete the blob from the container after it's read
                container.delete_blob(blob.name)
    
    startProcessing()    
    ```
4. 将 `<AZURE STORAGE CONNECTION STRING>` 替换为 Azure 存储帐户的连接字符串。 在本教程中创建的容器的名称为： "**捕获**"。 如果为容器使用了不同的名称，请将 `capture` 替换为存储帐户中容器的名称。 

## <a name="run-the-scripts"></a>运行脚本
1. 打开其路径中包含 Python 的命令提示符，并运行以下命令，安装 Python 必备组件包：
   
   ```
   pip install azure-storage-blob
   pip install azure-eventhub
   pip install avro-python3
   ```
2. 将目录更改为保存 sender.py 和 capturereader.py 的位置，然后运行以下命令：
   
   ```
   python sender.py
   ```
   
   此命令将启动一个新的 Python 进程，用于运行发送程序。
3. 等待几分钟让捕获运行。 然后，在原始命令窗口中键入以下命令：
   
   ```
   python capturereader.py
   ```

   此捕获处理器使用本地目录下载存储帐户/容器中的所有 blob。 它将处理任何不为空的内容，并将结果以 .csv 文件的形式写入到本地目录。

## <a name="next-steps"></a>后续步骤
请[查看 GitHub 上](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples)的 Python 示例。 


[Azure portal]: https://portal.azure.com/
[Overview of Event Hubs Capture]: event-hubs-capture-overview.md
[1]: ./media/event-hubs-archive-python/event-hubs-python1.png
[About Azure storage accounts]:../storage/common/storage-create-storage-account.md
[Visual Studio Code]: https://code.visualstudio.com/
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
