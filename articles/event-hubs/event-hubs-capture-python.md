---
title: "Azure 事件中心捕获演练 | Microsoft Docs"
description: "此示例使用 Azure Python SDK 来演示如何使用事件中心捕获功能。"
services: event-hubs
documentationcenter: 
author: djrosanova
manager: timlt
editor: 
ms.assetid: bdff820c-5b38-4054-a06a-d1de207f01f6
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/05/2017
ms.author: sethm
ms.openlocfilehash: 97cadbde2ddedade1a8688f1380b9ff9194613e7
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2018
---
# <a name="event-hubs-capture-walkthrough-python"></a>事件中心捕获演练：Python

捕获是 Azure 事件中心的一项功能。 可使用该功能自动将事件中心内的流数据传送到所选的 Azure Blob 存储帐户。 此功能使对实时流数据执行批处理操作变得轻松容易。 本文介绍如何通过 Python 使用事件中心捕获功能。 有关事件中心捕获的详细信息，请参阅[概述文章](event-hubs-capture-overview.md)。

此示例使用 [Azure Python SDK](https://azure.microsoft.com/develop/python/) 来演示捕获功能。 Sender.py 程序以 JSON 格式将模拟的环境遥测数据发送到事件中心。 事件中心配置为使用捕获功能将此数据成批地写入到 Blob 存储。 capturereader.py 应用读取这些 blob，为每个设备创建一个附加文件。 然后该应用将数据写入到 .csv 文件中。

## <a name="what-youll-accomplish"></a>你要完成的操作

1. 使用 Azure 门户创建 Azure Blob 存储帐户及其中的 blob 容器。
2. 使用 Azure 门户创建事件中心命名空间。
3. 使用 Azure 门户创建启用了捕获功能的事件中心。
4. 使用 Python 脚本将数据发送到事件中心。
5. 使用另一个 Python 脚本从捕获中读取文件并处理这些文件。

## <a name="prerequisites"></a>先决条件

- Python 2.7.x
- Azure 订阅
- 活动的[事件中心命名空间和事件中心](event-hubs-create.md)

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="create-an-azure-blob-storage-account"></a>创建 Azure Blob 存储帐户
1. 登录到 [Azure 门户][Azure portal]。
2. 在门户的左窗格中，选择“新建” > “存储” > “存储帐户”。
3. 在“创建存储帐户”窗格中完成选择，然后选择“创建”。
   
   ![“创建存储帐户”窗格][1]
4. 看到“部署成功”消息后，选择新存储帐户名，并在“概要”窗格中选择“Blob”。 “Blob 服务”窗格打开时，选择顶部的“+ 容器”。 将容器命名为“capture”，然后关闭“Blob 服务”窗格。
5. 选择左侧窗格中的“访问密钥”，复制存储帐户名称和 **key1** 的值。 将这些值保存到记事本或其他临时位置。

## <a name="create-a-python-script-to-send-events-to-your-event-hub"></a>创建用于将事件发送到事件中心的 Python 脚本
1. 打开常用的 Python 编辑器，如 [Visual Studio Code][Visual Studio Code]。
2. 创建名为 **sender.py** 的脚本。 此脚本将向事件中心发送 200 个事件。 它们是以 JSON 格式发送的简单环境读数。
3. 将以下代码粘贴到 sender.py 中：
   
   ```python
   import uuid
   import datetime
   import random
   import json
   from azure.servicebus import ServiceBusService
   
   sbs = ServiceBusService(service_namespace='INSERT YOUR NAMESPACE NAME', shared_access_key_name='RootManageSharedAccessKey', shared_access_key_value='INSERT YOUR KEY')
   devices = []
   for x in range(0, 10):
       devices.append(str(uuid.uuid4()))
   
   for y in range(0,20):
       for dev in devices:
           reading = {'id': dev, 'timestamp': str(datetime.datetime.utcnow()), 'uv': random.random(), 'temperature': random.randint(70, 100), 'humidity': random.randint(70, 100)}
           s = json.dumps(reading)
           sbs.send_event('INSERT YOUR EVENT HUB NAME', s)
       print y
   ```
4. 更新前面的代码，以使用在创建事件中心命名空间时获得的命名空间名称、密钥值和事件中心名称。

## <a name="create-a-python-script-to-read-your-capture-files"></a>创建用于读取捕获文件的 Python 脚本

1. 填写该窗格并选择“创建”。
2. 创建名为 capturereader.py 的脚本。 此脚本读取捕获的文件，并为每个设备创建一个文件，用于仅写入该设备的数据。
3. 将以下代码粘贴到 capturereader.py 中：
   
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
4. 在调用 `startProcessing` 时粘贴存储帐户名称和密钥的相应值。

## <a name="run-the-scripts"></a>运行脚本
1. 打开其路径中包含 Python 的命令提示符，并运行以下命令，安装 Python 必备组件包：
   
   ```
   pip install azure-storage
   pip install azure-servicebus
   pip install avro
   ```
   
   如果使用的是早期版本的 Azure 存储或 Azure，可能需要使用“--upgrade”选项。
   
   还可能需要运行以下命令（在大多数系统上不必要）：
   
   ```
   pip install cryptography
   ```
2. 将目录更改为保存 sender.py 和 capturereader.py 的位置，然后运行以下命令：
   
   ```
   start python sender.py
   ```
   
   此命令将启动一个新的 Python 进程，用于运行发送程序。
3. 等待几分钟让捕获运行。 然后，在原始命令窗口中键入以下命令：
   
   ```
   python capturereader.py
   ```

   此捕获处理器使用本地目录下载存储帐户/容器中的所有 blob。 它将处理任何不为空的内容，并将结果以 .csv 文件的形式写入到本地目录。

## <a name="next-steps"></a>后续步骤

访问以下链接可以了解有关事件中心的详细信息：

* [事件中心捕获概述][Overview of Event Hubs Capture]
* [使用事件中心的示例应用程序](https://github.com/Azure/azure-event-hubs/tree/master/samples)
* [事件中心概述][Event Hubs overview]

[Azure portal]: https://portal.azure.com/
[Overview of Event Hubs Capture]: event-hubs-capture-overview.md
[1]: ./media/event-hubs-archive-python/event-hubs-python1.png
[About Azure storage accounts]:../storage/common/storage-create-storage-account.md
[Visual Studio Code]: https://code.visualstudio.com/
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
