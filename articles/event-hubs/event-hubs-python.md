---
title: 使用 Python 发送和接收消息 - Azure 事件中心 |Microsoft Docs
description: 了解如何使用 Python 通过事件中心发送事件、接收事件和捕获事件流式处理。
keywords: ''
documentationcenter: ''
services: event-hubs
author: ShubhaVijayasarathy
manager: ''
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/26/2018
ms.author: shvija
ms.openlocfilehash: 88fdaec9e19c082a6fe981dc4d9a0e015335f1e2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60202966"
---
# <a name="how-to-use-azure-event-hubs-from-a-python-application"></a>如何通过 Python 应用程序使用 Azure 事件中心
Azure 事件中心是一个大数据流式处理平台和事件引入服务，每秒能够接收和处理数百万个事件。 事件中心可以处理和存储分布式软件和设备生成的事件、数据或遥测。 可以使用任何实时分析提供程序或批处理/存储适配器转换和存储发送到数据中心的数据。 有关详细信息，请参阅[事件中心简介](event-hubs-what-is-event-hubs.md)。 

本文提供了指向文章的链接，这些文章演示如何在使用 Python 编写的应用程序中执行以下任务：

- [将事件发送到事件中心](#send-events-to-event-hubs)
- [从事件中心接收事件](#receive-events-from-event-hubs)
- 从 Azure 存储读取捕获的事件数据。 

## <a name="prerequisites"></a>必备组件
- 按照以下快速入门之一创建活动中心：[Azure 门户](event-hubs-create.md)、[Azure CLI](event-hubs-quickstart-cli.md)、[Azure PowerShell](event-hubs-quickstart-powershell.md)、[Azure 资源管理器模板](event-hubs-resource-manager-namespace-event-hub.md)。 
- 安装在计算机上的 Python 3.4 或更高版本。

## <a name="install-python-package"></a>安装 Python 包

若要为事件中心安装 Python 包，请打开其路径中包含 Python 的命令提示符，然后运行以下命令： 

```bash
pip install azure-eventhub
```

## <a name="send-events-to-event-hubs"></a>将事件发送到事件中心
下列代码演示如何从 Python 应用程序将事件发送到事件中心： 

1. 创建变量以保存事件中心 URL、密钥名称和密钥值。 

    ```python
    # Import classes from Event Hubs python package
    from azure.eventhub import EventHubClient, Receiver, Offset
    
    # Address can be in either of these formats:
    # "amqps://<URL-encoded-SAS-policy>:<URL-encoded-SAS-key>@<mynamespace>.servicebus.windows.net/myeventhub"
    # "amqps://<mynamespace>.servicebus.windows.net/myeventhub"
    # For example:
    ADDRESS = "amqps://<MyEventHubNamspaceName>.servicebus.windows.net/<MyEventHubName>"
    
    # SAS policy and key are not required if they are encoded in the URL
    USER = "<Name of the access key. Default name: RootManageSharedAccessKey>"
    KEY = "<The access key>"
    ```

2. 使用发件人创建事件中心客户端、添加发件人、运行客户端以及发送事件，完成后关闭客户端。 

    ```python
    # Create an Event Hubs client
    client = EventHubClient(ADDRESS, debug=False, username=USER, password=KEY)
    
    # Add a sender to the client
    sender = client.add_sender(partition="0")
    
    # Run the Event Hub client
    client.run()
    
    # Send event to the event hub
    sender.send(EventData("<MyEventData>"))
    
    # Stop the Event Hubs client
    client.stop()
    
    ```

有关如何从使用 Python 编写的应用程序将事件发送到事件中心的完整教程，请参阅[本文](event-hubs-python-get-started-send.md)。

## <a name="receive-events-from-event-hubs"></a>从事件中心接收事件
下列代码演示如何接收来自 Python 应用程序事件中心的事件： 

```python

# Create an Event Hubs client
client = EventHubClient(ADDRESS, debug=False, username=USER, password=KEY)

# Add a receiver to the client
receiver = client.add_receiver(CONSUMER_GROUP, PARTITION, prefetch=5000, offset=OFFSET)

# Run the Event Hubs client
client.run()

# Receive event data from the event hub
for event_data in receiver.receive(timeout=100):
    last_offset = event_data.offset
    last_sn = event_data.sequence_number
    print("Received: {}, {}".format(last_offset, last_sn))

# Stop the Event Hubs client
client.stop()
```

有关如何接收来自 Python 编写的应用程序事件中心的事件的完整教程，请参阅[本文](event-hubs-python-get-started-receive.md)

## <a name="read-capture-event-data-from-azure-storage"></a>从 Azure 存储读取捕获的事件数据
下面的代码演示如何使用 Python 应用程序从 Azure Blob 存储读取捕获的事件数据：按照以下说明为事件中心启用捕获功能：[使用 Azure 门户启用事件中心捕获](event-hubs-capture-enable-through-portal.md)。 然后，在测试代码前将一些事件发送到事件中心。 

```python
import os
import string
import json
import avro.schema
from avro.datafile import DataFileReader, DataFileWriter
from avro.io import DatumReader, DatumWriter
from azure.storage.blob import BlockBlobService

def processBlob(filename):
    reader = DataFileReader(open(filename, 'rb'), DatumReader())
    dict = {}
    for reading in reader:
        parsed_json = json.loads(reading["Body"])
        if not 'id' in parsed_json:
            return
        if not dict.has_key(parsed_json['id']):
            list = []
            dict[parsed_json['id']] = list
        else:
            list = dict[parsed_json['id']]
            list.append(parsed_json)
    reader.close()
    for device in dict.keys():
        deviceFile = open(device + '.csv', "a")
        for r in dict[device]:
            deviceFile.write(", ".join([str(r[x]) for x in r.keys()])+'\n')

def startProcessing(accountName, key, container):
    print 'Processor started using path: ' + os.getcwd()
    block_blob_service = BlockBlobService(account_name=accountName, account_key=key)
    generator = block_blob_service.list_blobs(container)
    for blob in generator:
        #content_length == 508 is an empty file, so only process content_length > 508 (skip empty files)
        if blob.properties.content_length > 508:
            print('Downloaded a non empty blob: ' + blob.name)
            cleanName = string.replace(blob.name, '/', '_')
            block_blob_service.get_blob_to_path(container, blob.name, cleanName)
            processBlob(cleanName)
            os.remove(cleanName)
        block_blob_service.delete_blob(container, blob.name)
startProcessing('YOUR STORAGE ACCOUNT NAME', 'YOUR KEY', 'capture')

```

有关如何从使用 Python 编写的应用程序中读取 Azure Blob 存储中捕获的事件中心数据的完成教程，请参阅[本文](event-hubs-capture-python.md)

## <a name="github-samples"></a>GitHub 示例
可在 [azure-event-hubs-python Git 存储库](https://github.com/Azure/azure-event-hubs-python/) 中找到更多 Python 示例。

## <a name="next-steps"></a>后续步骤
阅读“概念”部分中的文章，从[事件中心功能概述](event-hubs-features.md)开始。
