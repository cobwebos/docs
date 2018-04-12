---
title: 使用 Azure IoT 中心路由消息 (Python) | Microsoft Docs
description: 如何使用路由规则和自定义终结点将消息发送到其他后端服务，从而处理 Azure IoT 中心的设备到云消息。
services: iot-hub
documentationcenter: python
author: kgremban
manager: timlt
editor: ''
ms.assetid: bd9af5f9-a740-4780-a2a6-8c0e2752cf48
ms.service: iot-hub
ms.devlang: python
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/22/2018
ms.author: v-masebo;kgremban
ms.openlocfilehash: c6182f878076bae27b18d0556a77c8d95677e9c1
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/05/2018
---
# <a name="routing-messages-with-iot-hub-python"></a>使用 IoT 中心路由消息 (Python)

[!INCLUDE [iot-hub-selector-process-d2c](../../includes/iot-hub-selector-process-d2c.md)]

本教程是在 [IoT 中心入门]教程的基础上制作的。  本教程：

* 介绍如何以基于配置的轻松方式，使用路由规则发送设备到云的消息。
* 介绍如何隔离需要解决方案后端立即执行操作以进行进一步处理的交互式消息。  例如，设备可能将发送一条警报消息，触发在 CRM 系统中插入票证。  与此相反，数据点消息（例如温度遥测）则送入分析引擎。

在本教程最后，会运行 3 个 Python 控制台应用：

* **SimulatedDevice.py**（在 [IoT 中心入门]教程中创建的应用的修改版本）每秒发送一次设备到云的数据点消息，根据随机时间间隔发送设备到云的交互式消息。 此应用使用 MQTT 协议来与 IoT 中心通信。
* **ReadCriticalQueue.py** 从附加到 IoT 中心的服务总线队列中取消关键消息的排队。

> [!NOTE]
> IoT 中心对许多设备平台和语言（包括 C、Java 和 JavaScript）提供 SDK 支持。 若要了解如何将本教程中的设备替换为物理设备，以及如何将设备连接到 IoT 中心，请参阅 [Azure IoT 开发人员中心]。

要完成本教程，需要以下各项：

* [IoT 中心入门]教程的完整工作版本。
* [Python 2.x 或 3.x][lnk-python-download]。 请确保根据安装程序的要求，使用 32 位或 64 位安装。 在安装过程中出现提示时，请确保将 Python 添加到特定于平台的环境变量中。 如果使用 Python 2.x，则可能需要[安装或升级 pip - Python 包管理系统][lnk-install-pip]。
* 如果使用 Windows OS，则请安装 [Visual C++ 可再发行组件包][lnk-visual-c-redist]，以便使用 Python 中的本机 DLL。
* [Node.js 4.0 或更高版本][lnk-node-download]。 请确保根据安装程序的要求，使用 32 位或 64 位安装。 这是安装 [IoT 中心资源管理器工具][lnk-iot-hub-explorer]所必需的。
* 有效的 Azure 帐户。 （如果没有帐户，只需花费几分钟就能创建一个[免费帐户][lnk-free-trial]。）

我们建议另外阅读有关 [Azure 存储]和 [Azure 服务总线]的主题。


## <a name="send-interactive-messages-from-a-device-app"></a>从设备应用发送交互式消息
在本部分中，会修改在 [IoT 中心入门]教程中创建的设备应用，不定期发送需要立即处理的消息。

1. 使用文本编辑器打开 **SimulatedDevice.py** 文件。 此文件包含 [IoT 中心入门]教程中创建的 **SimulatedDevice** 应用的代码。

2. 将 **iothub_client_telemetry_sample_run** 函数替换为以下代码：

    ```python
    def iothub_client_telemetry_sample_run():

    try:
        client = iothub_client_init()
        print ( "IoT Hub device sending periodic messages, press Ctrl-C to exit" )
        message_counter = 0

        while True:
            random_seed = random.random()
            msg_txt_formatted = MSG_TXT % (AVG_WIND_SPEED + (random_seed * 4 + 2))
            # messages can be encoded as string or bytearray
            if (message_counter & 1) == 1:
                message = IoTHubMessage(bytearray(msg_txt_formatted, 'utf8'))
            else:
                message = IoTHubMessage(msg_txt_formatted)
            # optional: assign ids
            message.message_id = "message_%d" % message_counter
            message.correlation_id = "correlation_%d" % message_counter
            # optional: assign properties
            prop_map = message.properties()
            if random_seed > .5:
                if random_seed > .8:
                    prop_map.add("level", 'critical')
                else:
                    prop_map.add("level", 'storage')

            client.send_event_async(message, send_confirmation_callback, message_counter)
            print ( "IoTHubClient.send_event_async accepted message [%d] for transmission to IoT Hub." % message_counter )

            status = client.get_send_status()
            print ( "Send status: %s" % status )
            time.sleep(30)

            status = client.get_send_status()
            print ( "Send status: %s" % status )

            message_counter += 1

    except IoTHubError as iothub_error:
        print ( "Unexpected error %s from IoTHub" % iothub_error )
        return
    except KeyboardInterrupt:
        print ( "IoTHubClient sample stopped" )
    ```
   
    此方法会将 `"level": "critical"` 和 `"level": "storage"` 属性随机添加到设备发送的消息，此设备模拟需要应用程序后端立即执行操作的消息，或需要永久存储的消息。 应用程序支持基于消息正文的路由消息。
   
   > [!NOTE]
   > 可使用消息属性根据各种方案路由消息，包括冷路径处理和此处所示的热路径示例。

1. 保存并关闭 **SimulatedDevice.py** 文件。

    > [!NOTE]
    > 为简单起见，本教程不实现任何重试策略。 在生产代码中，应按 MSDN 文章 [Transient Fault Handling]（暂时性故障处理）中建议来实施重试策略（如指数退让）。


## <a name="add-queues-to-your-iot-hub-and-route-messages-to-them"></a>向 IoT 中心添加队列并向其路由消息

在本部分中，将创建一个服务总线队列和一个存储帐户并将其连接到 IoT 中心，还会配置 IoT 中心，根据消息是否存在某个属性将消息发送到该队列，并将所有消息发送到该存储帐户。 有关如何处理服务总线队列中的消息的详细信息，请参阅[队列入门][lnk-sb-queues-node]；有关如何管理存储，请参阅 [Azure 存储入门][Azure 存储]。

1. 按[队列入门][lnk-sb-queues-node]中所述，创建服务总线队列。 记下命名空间和队列名称。

    > [!NOTE]
    > 用作 IoT 中心终结点的服务总线队列和主题不得启用会话或重复检测选项。 如果启用了其中任一选项，该终结点会在 Azure 门户中显示为“无法访问”。

1. 在 Azure 门户中，打开 IoT 中心并单击“终结点”。

    ![IoT 中心的终结点][30]

1. 在“终结点”边栏选项卡中，单击顶部的“添加”，将队列添加到 IoT 中心。 将终结点命名为“CriticalQueue”，并使用下拉列表选择“服务总线队列”、队列所在的服务总线命名空间和队列名称。 完成后，单击底部的“确定”。  

    ![添加终结点][31]

1. 现在单击 IoT 中心的“路由”。 单击边栏选项卡顶部的“添加”，创建将消息路由到刚添加的队列的路由规则。 

    ![添加路由][34]

    输入名称，并选择“设备消息”作为数据源。 选择自定义终结点 **CriticalQueue** 作为路由规则终结点，并输入 `level="critical"` 作为查询字符串。 在底部单击“保存”。

    ![路由详细信息][32]

    请确保回退路由设为“开”。 此设置是 IoT 中心的默认配置。

    ![回退路由][33]

## <a name="optional-read-from-the-queue-endpoint"></a>（可选）从队列终结点读取

在本部分中，将创建一个 Python 控制台应用，用于读取来自 IoT 服务总线的关键消息。 请参阅[队列入门][lnk-sb-queues-node]中的更多信息。 

1. 打开新的命令提示符，并安装用于 Python 的 Azure IoT 中心设备 SDK，如下所示。 在安装之后关闭命令提示符。

    ```cmd/sh
    pip install azure-servicebus
    ```

    > [!NOTE]
    > 有关安装 **azure-servicebus** 包或有关进一步安装选项的问题，请参阅 [Python azure-servicebus 包][lnk-python-service-bus]。

1. 创建一个名为 **ReadCriticalQueue.py** 的文件。 在所选 Python 编辑器/IDE（例如 IDLE）中打开该文件。

1. 添加以下代码，从设备 SDK 导入所需模块：

    ```python
    from azure.servicebus import ServiceBusService, Message, Queue
    ```

1. 添加以下代码，并将占位符替换为服务总线的连接数据：

    ```python
    SERVICE_BUS_NAME = {serviceBusName}
    SHARED_ACCESS_POLICY_NAME = {sharedAccessPolicyName}
    SHARED_ACCESS_POLICY_KEY_VALUE = {sharedAccessPolicyKeyValue}
    QUEUE_NAME = {queueName}    
    ```

5. 添加以下代码以连接到服务总线并从中读取数据： 

    ```python
    def setup_client():
        bus_service = ServiceBusService(
        service_namespace=SERVICE_BUS_NAME,
        shared_access_key_name=SHARED_ACCESS_POLICY_NAME,
        shared_access_key_value=SHARED_ACCESS_POLICY_KEY_VALUE)

        while True:
            msg = bus_service.receive_queue_message(QUEUE_NAME, peek_lock=False)
            print(msg.body)
    ```

1. 最后，添加 main 函数。 

    ```python
    if __name__ == '__main__':
        setup_client()
    ```

1. 保存并关闭 **ReadCriticalQueue.py** 文件。 现在，已准备就绪，可以运行应用程序了。


## <a name="run-the-applications"></a>运行应用程序

现在，已准备就绪，可以运行应用程序了。

1. 打开命令提示符并安装 IoT 中心资源管理器。 

    ```cmd/sh
    npm install -g iothub-explorer
    ```

1. 在命令提示符处运行以下命令，开始监视设备发出的设备到云消息。 在 `--login` 后的占位符中使用 IoT 中心的连接字符串。

    ```cmd/sh
    iothub-explorer monitor-events [deviceId] --login "[IoTHub connection string]"
    ```

1. 打开新的命令提示符，导航到包含 **SimulatedDevice.py** 文件的目录。

1. 运行 **SimulatedDevice.py** 文件，该文件定期将遥测数据发送到 IoT 中心。 
   
    ```cmd/sh
    python SimulatedDevice.py
    ```

1. 若要运行 **ReadCriticalQueue** 应用程序，请在命令提示符或 shell 中导航到 **ReadCriticalQueue.py** 文件并执行以下命令：

   ```cmd/sh
   python ReadCriticalQueue.py
   ```

1. 运行前一部分的 IoT 中心资源管理器，在命令提示符处观察设备消息。 在 **ReadCriticalQueue** 应用程序中观察 `critical` 消息。

    ![Python 设备到云消息][2]


## <a name="optional-add-storage-container-to-your-iot-hub-and-route-messages-to-it"></a>（可选）将存储容器添加到 IoT 中心并向其路由消息

在本部分中，将创建一个存储帐户并将其连接到 IoT 中心，还会配置 IoT 中心，根据消息上的现有属性发送消息到该帐户。 有关如何管理存储的详细信息，请参阅 [Azure 存储入门][Azure 存储]。

 > [!NOTE]
   > _F1 免费_层下创建的 IoT 中心帐户局限于一个**终结点**。 如果并不局限于一个**终结点**，则除了 **CriticalQueue** 以外，还可以设置 **StorageContainer**，并同时运行两者。

1. 根据 [Azure 存储文档][lnk-storage]中所述创建存储帐户。 记下帐户名称。

2. 在 Azure 门户中，打开 IoT 中心并单击“终结点”。

3. 在“终结点”边栏选项卡中选择“CriticalQueue”终结点，单击“删除”。 单击“是”，然后单击“添加”。 将终结点命名为 **StorageContainer**，使用下拉列表选择“Azure 存储容器”，并创建**存储帐户**和**存储容器**。  请记下名称。  完成后，单击底部的“确定”。 

 > [!NOTE]
   > _F1 免费_层下创建的 IoT 中心帐户局限于一个**终结点**。 如果并不局限于一个**终结点**，则不需要删除 **CriticalQueue**。

4. 在 IoT 中心单击“路由”。 单击边栏选项卡顶部的“添加”，创建将消息路由到刚添加的队列的路由规则。 选择“设备消息”作为数据源。 输入 `level="storage"` 作为条件，并选择自定义终结点 **StorageContainer** 作为路由规则终结点。 在底部单击“保存”。  

    请确保回退路由设为“开”。 此设置是 IoT 中心的默认配置。

1. 确保以前的应用程序 **SimulatedDevice.py** 仍在运行。 

1. 在 Azure 门户中，转到自己的存储帐户，在“Blob 服务”下面单击“浏览 Blob...”。选择自己的容器，导航到并单击 JSON 文件，然后单击“下载”以查看数据。


## <a name="next-steps"></a>后续步骤

在本教程中，介绍了如何使用 IoT 中心的消息路由功能可靠地分派设备到云的消息。

若要查看使用 IoT 中心的完整端到端解决方案的示例，请参阅 [Azure IoT 套件][lnk-suite]。

若要了解有关使用 IoT 中心开发解决方案的详细信息，请参阅 [IoT 中心开发人员指南]。

若要详细了解 IoT 中心的消息路由，请参阅[使用 IoT 中心发送和接收消息][lnk-devguide-messaging]。

<!-- Images. -->
[2]: ./media/iot-hub-python-python-process-d2c/output.png

[30]: ./media/iot-hub-python-python-process-d2c/click-endpoints.png
[31]: ./media/iot-hub-python-python-process-d2c/endpoint-creation.png
[32]: ./media/iot-hub-python-python-process-d2c/route-creation.png
[33]: ./media/iot-hub-python-python-process-d2c/fallback-route.png
[34]: ./media/iot-hub-python-python-process-d2c/click-routes.png

<!-- Links -->
[lnk-python-download]: https://www.python.org/downloads/
[lnk-visual-c-redist]: http://www.microsoft.com/download/confirmation.aspx?id=48145
[lnk-node-download]: https://nodejs.org/en/download/
[lnk-install-pip]: https://pip.pypa.io/en/stable/installing/
[lnk-iot-hub-explorer]: https://github.com/Azure/iothub-explorer
[lnk-sb-queues-node]: ../service-bus-messaging/service-bus-python-how-to-use-queues.md

[Azure 存储]: https://azure.microsoft.com/documentation/services/storage/
[Azure 服务总线]: https://azure.microsoft.com/documentation/services/service-bus/

[IoT 中心开发人员指南]: iot-hub-devguide.md
[lnk-devguide-messaging]: iot-hub-devguide-messaging.md
[IoT 中心入门]: iot-hub-python-getstarted.md
[Azure IoT 开发人员中心]: https://azure.microsoft.com/develop/iot

[Transient Fault Handling]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-free-trial]: https://azure.microsoft.com/free/
[lnk-storage]: https://docs.microsoft.com/en-us/azure/storage/
[lnk-python-service-bus]: https://pypi.python.org/pypi/azure-servicebus