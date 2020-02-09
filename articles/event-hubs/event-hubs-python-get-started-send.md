---
title: 使用 Python（旧版）向/从 Azure 事件中心发送/接收事件
description: 本演练介绍如何创建和运行 Python 脚本，这些脚本使用旧的 azure-eventhub 版本 1 包向/从 Azure 事件中心发送/接收事件。
services: event-hubs
author: spelluru
manager: femila
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.date: 01/15/2020
ms.author: spelluru
ms.openlocfilehash: 654ccd6352dc0b671cc3becdafd2f1e1102dd39e
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2020
ms.locfileid: "76902936"
---
# <a name="quickstart-send-and-receive-events-with-event-hubs-using-python-azure-eventhub-version-1"></a>快速入门：使用 Python（azure-eventhub 版本 1）向/从事件中心发送/接收事件

Azure 事件中心是一个大数据流式处理平台和事件引入服务，每秒能够接收和处理数百万个事件。 事件中心可以处理和存储分布式软件与设备发出的事件、数据或遥测信息。 可以使用任何实时分析提供程序或批处理/存储适配器转换和存储发送到数据中心的数据。 有关事件中心的详细信息，请参阅 [Azure 事件中心](event-hubs-about.md) 和 [Azure 事件中心的功能和术语](event-hubs-features.md)。

本快速入门介绍如何创建用于向/从事件中心发送/接收事件的 Python 应用程序。 

> [!WARNING]
> 本快速入门适用于 Azure 事件中心 Python SDK 版本 1。 我们建议你将代码[迁移](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/migration_guide.md)到 [Python SDK 版本 5](get-started-python-send-v2.md)。

 
 

## <a name="prerequisites"></a>必备条件

若要完成本快速入门，需要具备以下先决条件：

- Azure 订阅。 如果没有订阅，请在开始之前[创建一个免费帐户](https://azure.microsoft.com/free/)。
- 按照以下文档中的说明创建的活动事件中心命名空间和事件中心：[快速入门：使用 Azure 门户创建事件中心](event-hubs-create.md)。 记下命名空间和事件中心名称，以便稍后在本演练中使用。 
- 事件中心命名空间的共享访问密钥名称和主密钥值。 按照[获取连接字符串](event-hubs-get-connection-string.md#get-connection-string-from-the-portal)中的说明获取访问密钥名称和值。 默认访问密钥名称为 **RootManageSharedAccessKey**。 复制密钥名称和主密钥值，以便稍后在本演练中使用。 
- Python 3.4 或更高版本，其中已安装并更新 `pip`。
- 事件中心的 Python 包。 若要安装该包，请在包路径中包含 Python 的命令提示符中运行以下命令： 
  
  ```cmd
  pip install azure-eventhub==1.3.*
  ```


## <a name="send-events"></a>发送事件

若要创建将事件发送到事件中心的 Python 应用程序：

> [!NOTE]
> 如果你不想要学习本快速入门，可以从 GitHub 下载并运行[示例应用](https://github.com/Azure/azure-event-hubs-python/tree/master/examples)。 请将 `EventHubConnectionString` 和 `EventHubName` 字符串替换为你的事件中心值。

1. 打开偏好的 Python 编辑器，例如 [Visual Studio Code](https://code.visualstudio.com/)。
2. 创建名为 *send.py* 的新文件。 此脚本将向事件中心发送 100 个事件。
3. 将以下代码粘贴到 *send.py*中（请将事件中心的 \<namespace>、\<eventhub>、\<AccessKeyName> 和 \<primary key value> 替换为自己的值）： 
   
   ```python
   import sys
   import logging
   import datetime
   import time
   import os
   
   from azure.eventhub import EventHubClient, Sender, EventData
   
   logger = logging.getLogger("azure")
   
   # Address can be in either of these formats:
   # "amqps://<URL-encoded-SAS-policy>:<URL-encoded-SAS-key>@<namespace>.servicebus.windows.net/eventhub"
   # "amqps://<namespace>.servicebus.windows.net/<eventhub>"
   # SAS policy and key are not required if they are encoded in the URL
   
   ADDRESS = "amqps://<namespace>.servicebus.windows.net/<eventhub>"
   USER = "<AccessKeyName>"
   KEY = "<primary key value>"
   
   try:
       if not ADDRESS:
           raise ValueError("No EventHubs URL supplied.")
   
       # Create Event Hubs client
       client = EventHubClient(ADDRESS, debug=False, username=USER, password=KEY)
       sender = client.add_sender(partition="0")
       client.run()
       try:
           start_time = time.time()
           for i in range(100):
               print("Sending message: {}".format(i))
               message = "Message {}".format(i)
               sender.send(EventData(message))
       except:
           raise
       finally:
           end_time = time.time()
           client.stop()
           run_time = end_time - start_time
           logger.info("Runtime: {} seconds".format(run_time))
   
   except KeyboardInterrupt:
       pass
   ```
   
4. 保存文件。 

若要运行该脚本，请从 *send.py* 保存到的目录运行以下命令：

```cmd
start python send.py
```

祝贺你！ 现在已向事件中心发送消息。

## <a name="receive-events"></a>接收事件

若要创建从事件中心接收事件的 Python 应用程序：

1. 在 Python 编辑器中，创建名为 *recv.py* 的文件。
2. 将以下代码粘贴到 *recv.py*中（请将事件中心的 \<namespace>、\<eventhub>、\<AccessKeyName> 和 \<primary key value> 替换为自己的值）： 
   
   ```python
   import os
   import sys
   import logging
   import time
   from azure.eventhub import EventHubClient, Receiver, Offset
   
   logger = logging.getLogger("azure")
   
   # Address can be in either of these formats:
   # "amqps://<URL-encoded-SAS-policy>:<URL-encoded-SAS-key>@<mynamespace>.servicebus.windows.net/myeventhub"
   # "amqps://<namespace>.servicebus.windows.net/<eventhub>"
   # SAS policy and key are not required if they are encoded in the URL
   
   ADDRESS = "amqps://<namespace>.servicebus.windows.net/<eventhub>"
   USER = "<AccessKeyName>"
   KEY = "<primary key value>"
   
   
   CONSUMER_GROUP = "$default"
   OFFSET = Offset("-1")
   PARTITION = "0"
   
   total = 0
   last_sn = -1
   last_offset = "-1"
   client = EventHubClient(ADDRESS, debug=False, username=USER, password=KEY)
   try:
       receiver = client.add_receiver(
           CONSUMER_GROUP, PARTITION, prefetch=5000, offset=OFFSET)
       client.run()
       start_time = time.time()
       for event_data in receiver.receive(timeout=100):
           print("Received: {}".format(event_data.body_as_str(encoding='UTF-8')))
           total += 1
   
       end_time = time.time()
       client.stop()
       run_time = end_time - start_time
       print("Received {} messages in {} seconds".format(total, run_time))
   
   except KeyboardInterrupt:
       pass
   finally:
       client.stop()
   ```
   
4. 保存文件。

若要运行该脚本，请从 *recv.py* 保存到的目录运行以下命令：

```cmd
start python recv.py
```

## <a name="next-steps"></a>后续步骤
有关事件中心的详细信息，请参阅以下文章：

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Azure 事件中心的功能和术语](event-hubs-features.md)
- [事件中心常见问题解答](event-hubs-faq.md)

