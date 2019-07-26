---
title: 使用 Python 发送或接收事件-Azure 事件中心 |Microsoft Docs
description: 本文提供了创建 Python 应用程序的演练，该应用程序用于将事件发送到 Azure 事件中心。
services: event-hubs
author: ShubhaVijayasarathy
manager: femila
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 04/15/2019
ms.author: shvija
ms.openlocfilehash: 9018df73c85486f5ffc9b16c1dbb70d4d99fcc65
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/19/2019
ms.locfileid: "68360180"
---
# <a name="send-events-to-or-receive-events-from-event-hubs-using-python"></a>使用 Python 将事件发送到事件中心或从其接收事件

Azure 事件中心是一个大数据流式处理平台和事件引入服务，每秒能够接收和处理数百万个事件。 事件中心可以处理和存储分布式软件和设备生成的事件、数据或遥测。 可以使用任何实时分析提供程序或批处理/存储适配器转换和存储发送到数据中心的数据。 有关事件中心的详细概述，请参阅[事件中心概述](event-hubs-about.md)和[事件中心功能](event-hubs-features.md)。

本教程介绍了如何创建 Python 应用程序来将事件发送到事件中心或从其接收事件。 

> [!NOTE]
> 可以从 [GitHub](https://github.com/Azure/azure-event-hubs-python/tree/master/examples) 下载此用作示例的快速入门，将 `EventHubConnectionString` 和 `EventHubName` 字符串替换为事件中心值，并运行它。 或者，可以按照本教程中的步骤创建自己的解决方案。

## <a name="prerequisites"></a>系统必备

若要完成本教程，需要具备以下先决条件：

- Azure 订阅。 如果没有订阅，请在开始之前[创建一个免费帐户](https://azure.microsoft.com/free/)。
- Python 3.4 或更高版本。
- 使用 [Azure 门户](https://portal.azure.com)创建事件中心类型的命名空间，并获取应用程序与事件中心进行通信所需的管理凭据。 要创建命名空间和事件中心，请按照[此文](event-hubs-create.md)中的步骤操作。 然后，按照文章中的以下说明获取事件中心访问密钥的值：[获取连接字符串](event-hubs-get-connection-string.md#get-connection-string-from-the-portal)。 可在本教程后面编写的代码中使用该访问密钥。 默认密钥名称为：RootManageSharedAccessKey。

## <a name="install-python-package"></a>安装 Python 包

若要为事件中心安装 Python 包，请打开其路径中包含 Python 的命令提示符，然后运行以下命令： 

```bash
pip install azure-eventhub
```

## <a name="send-events"></a>发送事件

### <a name="create-a-python-script-to-send-events"></a>创建用于发送事件的 Python 脚本

接下来，创建将事件发送到事件中心的 Python 应用程序：

1. 打开你常用的 Python 编辑器，例如 [Visual Studio Code](https://code.visualstudio.com/)
2. 创建名为 send.py 的脚本。 此脚本将向事件中心发送 100 个事件。
3. 将以下代码粘贴到 send.py 中，将 ADDRESS、USER 和 KEY 值替换为你在上一节中从 Azure 门户获取的值： 

```python
import sys
import logging
import datetime
import time
import os

from azure.eventhub import EventHubClient, Sender, EventData

logger = logging.getLogger("azure")

# Address can be in either of these formats:
# "amqps://<URL-encoded-SAS-policy>:<URL-encoded-SAS-key>@<mynamespace>.servicebus.windows.net/myeventhub"
# "amqps://<mynamespace>.servicebus.windows.net/myeventhub"
# For example:
ADDRESS = "amqps://mynamespace.servicebus.windows.net/myeventhub"

# SAS policy and key are not required if they are encoded in the URL
USER = "RootManageSharedAccessKey"
KEY = "namespaceSASKey"

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
            sender.send(EventData(str(i)))
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

### <a name="run-application-to-send-events"></a>运行应用程序来发送事件

若要运行此脚本，请打开其路径中包含 Python 的命令提示符，然后运行以下命令：

```bash
start python send.py
```

祝贺你！ 现在已向事件中心发送消息。

## <a name="receive-events"></a>接收事件

### <a name="create-a-python-script-to-receive-events"></a>创建用于接收事件的 Python 脚本

接下来，创建从事件中心接收事件的 Python 应用程序：

1. 打开你常用的 Python 编辑器，例如 [Visual Studio Code](https://code.visualstudio.com/)
2. 创建名为 recv.py 的脚本。
3. 将以下代码粘贴到 recv.py 中，将 ADDRESS、USER 和 KEY 值替换为在上一节中从 Azure 门户所获取的值： 

```python
import os
import sys
import logging
import time
from azure.eventhub import EventHubClient, Receiver, Offset

logger = logging.getLogger("azure")

# Address can be in either of these formats:
# "amqps://<URL-encoded-SAS-policy>:<URL-encoded-SAS-key>@<mynamespace>.servicebus.windows.net/myeventhub"
# "amqps://<mynamespace>.servicebus.windows.net/myeventhub"
# For example:
ADDRESS = "amqps://mynamespace.servicebus.windows.net/myeventhub"

# SAS policy and key are not required if they are encoded in the URL
USER = "RootManageSharedAccessKey"
KEY = "namespaceSASKey"
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
        last_offset = event_data.offset
        last_sn = event_data.sequence_number
        print("Received: {}, {}".format(last_offset, last_sn))
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

### <a name="receive-events"></a>接收事件

若要运行此脚本，请打开其路径中包含 Python 的命令提示符，然后运行以下命令：

```bash
start python recv.py
```
 
## <a name="next-steps"></a>后续步骤
请阅读以下文章：

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Azure 事件中心的功能和术语](event-hubs-features.md)
- [事件中心常见问题解答](event-hubs-faq.md)

