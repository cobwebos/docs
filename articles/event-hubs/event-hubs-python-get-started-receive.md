---
title: 使用 Python 从 Azure 事件中心接收事件 | Microsoft Docs
description: 使用 Python 从 Azure 事件中心接收事件入门
services: event-hubs
author: sethmanheim
manager: femila
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 07/26/2018
ms.author: sethm
ms.openlocfilehash: 9707f0cf5a76c0ac4006b53ca8166d3b42fc9af4
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2018
ms.locfileid: "50240556"
---
# <a name="receive-events-from-event-hubs-using-python"></a>使用 Python 从事件中心接收事件

Azure 事件中心是一个具备高度伸缩性的事件管理系统，每秒可处理大量事件，从而使应用程序能够处理和分析连接设备和其他系统所产生的海量数据。 收集到事件中心后，可使用进程内处理程序或通过转发到其他分析系统，接收和处理事件。

若要了解有关事件中心的详细信息，请参阅[事件中心概述][Event Hubs overview]。

本教程介绍如何通过以 Python 编写的应用程序从事件中心接收事件。 若要发送事件，请参阅[相应的发送文章](event-hubs-python-get-started-send.md)。

本教程中的代码取自[以下 GitHub 示例](https://github.com/Azure/azure-event-hubs-python/tree/master/examples)，可以检查这些代码，查看包括导入语句和变量声明的功能完善的应用程序。 其他示例提供在同一 GitHub 文件夹中。

## <a name="prerequisites"></a>先决条件

若要完成本教程，需要具备以下先决条件：

- Python 3.4 或更高版本。
- 现有事件中心命名空间和事件中心。 请按[本文](event-hubs-create.md)中的说明创建以下实体。 

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]


## <a name="install-python-package"></a>安装 Python 包

若要为事件中心安装 Python 包，请打开其路径中包含 Python 的命令提示符，然后运行以下命令： 

```bash
pip install azure-eventhub
```

## <a name="create-a-python-script-to-receive-events"></a>创建用于接收事件的 Python 脚本

接下来，创建从事件中心接收事件的 Python 应用程序：

1. 打开常用的 Python 编辑器，如 [Visual Studio Code][Visual Studio Code]。
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
    receiver = client.add_receiver(CONSUMER_GROUP, PARTITION, prefetch=5000, offset=OFFSET)
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

## <a name="receive-events"></a>接收事件

若要运行此脚本，请打开其路径中包含 Python 的命令提示符，然后运行以下命令：

```bash
start python recv.py
```
 
## <a name="next-steps"></a>后续步骤
在本快速入门中，你已创建从事件中心接收消息的 Python 应用程序。 若要了解如何使用 Python 将事件发送到事件中心，请参阅[从事件中心发送事件 - Python ](event-hubs-python-get-started-send.md)。

<!-- Links -->
[Event Hubs overview]: event-hubs-about.md
[Visual Studio Code]: https://code.visualstudio.com/
[free account]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
