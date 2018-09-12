---
title: 使用 Python 将事件发送到 Azure 事件中心 | Microsoft Docs
description: 使用 Python 将事件发送到事件中心入门
services: event-hubs
author: sethmanheim
manager: femila
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 07/26/2018
ms.author: sethm
ms.openlocfilehash: 762e21cfc7d16b614eb637c569f8bfc5b6115db1
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/05/2018
ms.locfileid: "43703259"
---
# <a name="send-events-to-event-hubs-using-python"></a>使用 Python 将事件发送到事件中心

Azure 事件中心是一个具备高度伸缩性的事件管理系统，每秒可处理大量事件，从而使应用程序能够处理和分析连接设备和其他系统所产生的海量数据。 收集到事件中心后，可使用进程内处理程序或通过转发到其他分析系统，接收和处理事件。

若要了解有关事件中心的详细信息，请参阅[事件中心概述][Event Hubs overview]。

本教程介绍如何从以 Python 编写的应用程序中将事件发送到事件中心。 若要接收事件，请参阅[相应的接收文章](event-hubs-python-get-started-receive.md)。

本教程中的代码取自[以下 GitHub 示例](https://github.com/Azure/azure-event-hubs-python/tree/master/examples)，可以检查这些代码，查看包括导入语句和变量声明的功能完善的应用程序。 其他示例在同一 GitHub 文件夹中提供。

## <a name="prerequisites"></a>先决条件

若要完成本教程，需要具备以下先决条件：

- Python 3.4 或更高版本。
- 现有事件中心命名空间和事件中心。 请按[本文](event-hubs-create.md)中的说明创建以下实体。 

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]


## <a name="install-python-package"></a>安装 Python 包

若要安装事件中心的 Python 包，请打开其路径中包含 Python 的命令提示符，然后运行以下命令： 

```bash
pip install azure-eventhub
```

## <a name="create-a-python-script-to-send-events"></a>创建用于发送事件的 Python 脚本

接下来，创建将事件发送到事件中心的 Python 应用程序：

1. 打开常用的 Python 编辑器，如 [Visual Studio Code][Visual Studio Code]。
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

## <a name="send-events"></a>发送事件

若要运行该脚本，请打开其路径中包含 Python 的命令提示符，然后运行以下命令：

```bash
start python send.py
```
 
## <a name="next-steps"></a>后续步骤

现在已使用 Python 将事件发送到事件中心，若要接收事件，请参阅[相应的接收文章](event-hubs-python-get-started-receive.md)。

若要了解有关事件中心的详细信息，请访问以下页面：

* [事件中心概述][Event Hubs overview]
* [创建事件中心](event-hubs-create.md)
* [事件中心常见问题解答](event-hubs-faq.md)

<!-- Links -->
[Event Hubs overview]: event-hubs-about.md
[Visual Studio Code]: https://code.visualstudio.com/
[free account]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
