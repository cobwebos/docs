---
title: 使用 Azure IoT 中心发送云到设备消息 (Python) | Microsoft Docs
description: 如何使用用于 Python 的 Azure IoT SDK 将云到设备消息从 Azure IoT 中心发送到设备。 修改模拟设备应用以接收云到设备消息，并修改后端应用以发送云到设备消息。
services: iot-hub
documentationcenter: python
author: msebolt
manager: timlt
editor: ''
ms.assetid: 3ca8a78f-ade2-46e8-8a49-d5d599cdf1f1
ms.service: iot-hub
ms.devlang: javascript
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/22/2018
ms.author: v-masebo
ms.openlocfilehash: 7fd8bd87a4b7cf5a0f55b9fa7b5ab2ed43083d2c
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2018
---
# <a name="send-cloud-to-device-messages-with-iot-hub-python"></a>使用 IoT 中心发送云到设备消息 (Python)
[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]


## <a name="introduction"></a>介绍
Azure IoT 中心是一项完全托管的服务，有助于在数百万台设备和单个解决方案后端之间实现安全可靠的双向通信。 [IoT 中心入门]教程演示了如何创建 IoT 中心、在其中预配设备标识，以及编写用来发送“设备到云”消息的模拟设备应用。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

本教程是在 [IoT 中心入门]的基础上制作的。 其中了说明了如何：

* 通过 IoT 中心，将云到设备的消息从解决方案后端发送到单个设备。
* 在设备上接收云到设备的消息。
* 通过解决方案后端，请求确认收到从 IoT 中心发送到设备的消息（反馈）。

可以在 [IoT 中心开发人员指南][IoT Hub developer guide - C2D]中找到有关云到设备消息的详细信息。

在本教程末尾，你将运行两个 Python 控制台应用：

* **SimulatedDevice.py**，这是在 [IoT 中心入门]中创建的应用的修改版本，它连接到 IoT 中心并接收云到设备消息。
* **SendCloudToDeviceMessage.py**，它将云到设备消息通过 IoT 中心发送到模拟设备应用，然后接收其传送确认。

> [!NOTE]
> IoT 中心通过 Azure IoT 设备 SDK 对许多设备平台和语言（包括 C、Java 和 Javascript）提供 SDK 支持。 有关如何将设备连接到本教程中的代码（通常是连接到 Azure IoT 中心）的逐步说明，请参阅 [Azure IoT 开发人员中心]。
> 

要完成本教程，需要以下各项：

* [Python 2.x 或 3.x][lnk-python-download]。 请确保根据安装程序的要求，使用 32 位或 64 位安装。 在安装过程中出现提示时，请确保将 Python 添加到特定于平台的环境变量中。 如果使用 Python 2.x，则可能需要[安装或升级 pip - Python 包管理系统][lnk-install-pip]。
* 如果使用 Windows OS，则请安装 [Visual C++ 可再发行组件包][lnk-visual-c-redist]，以便使用 Python 中的本机 DLL。
* 有效的 Azure 帐户。 （如果没有帐户，只需花费几分钟就能创建一个[免费帐户][lnk-free-trial]。）

> [!NOTE]
> 适用于 `azure-iothub-service-client` 和 `azure-iothub-device-client` 的 pip 包目前仅供 Windows OS 使用。 对于 Linux/Mac OS，请参阅[准备适用于 Python 的开发环境][lnk-python-devbox]一文中特定于 Linux 和 Mac OS 的部分。
> 


## <a name="receive-messages-in-the-simulated-device-app"></a>在模拟设备应用中接收消息
在本部分中，将创建一个 Python 控制台应用来模拟设备并从 IoT 中心接收云到设备消息。

1. 使用文本编辑器，创建一个 **SimulatedDevice.py** 文件。

1. 在 **SimulatedDevice.py** 文件的开头添加以下 `import` 语句和变量：
   
    ```python
    import time
    import sys
    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult
    from iothub_client import IoTHubMessage, IoTHubMessageDispositionResult, IoTHubError

    RECEIVE_CONTEXT = 0
    WAIT_COUNT = 10
    RECEIVED_COUNT = 0
    RECEIVE_CALLBACKS = 0
    ```

1. 将以下代码添加到 **SimulatedDevice.py** 文件。 将“{deviceConnectionString}”占位符值替换为在 [IoT 中心入门]教程中创建的设备的设备连接字符串：
   
    ```python
    # choose AMQP or AMQP_WS as transport protocol
    PROTOCOL = IoTHubTransportProvider.AMQP
    CONNECTION_STRING = "{deviceConnectionString}"
    ```

1. 添加以下函数，用以在控制台中输出收到的消息：
   
    ```python
    def receive_message_callback(message, counter):
        global RECEIVE_CALLBACKS
        message_buffer = message.get_bytearray()
        size = len(message_buffer)
        print ( "Received Message [%d]:" % counter )
        print ( "    Data: <<<%s>>> & Size=%d" % (message_buffer[:size].decode('utf-8'), size) )
        map_properties = message.properties()
        key_value_pair = map_properties.get_internals()
        print ( "    Properties: %s" % key_value_pair )
        counter += 1
        RECEIVE_CALLBACKS += 1
        print ( "    Total calls received: %d" % RECEIVE_CALLBACKS )
        return IoTHubMessageDispositionResult.ACCEPTED

    def iothub_client_init():
        client = IoTHubClient(CONNECTION_STRING, PROTOCOL)

        client.set_message_callback(receive_message_callback, RECEIVE_CONTEXT)

        return client

    def print_last_message_time(client):
        try:
            last_message = client.get_last_message_receive_time()
            print ( "Last Message: %s" % time.asctime(time.localtime(last_message)) )
            print ( "Actual time : %s" % time.asctime() )
        except IoTHubClientError as iothub_client_error:
            if iothub_client_error.args[0].result == IoTHubClientResult.INDEFINITE_TIME:
                print ( "No message received" )
            else:
                print ( iothub_client_error )
    ```

1. 添加以下代码，用以初始化客户端并等待接收云到设备消息：
   
    ```python
    def iothub_client_init():
        client = IoTHubClient(CONNECTION_STRING, PROTOCOL)

        client.set_message_callback(receive_message_callback, RECEIVE_CONTEXT)

        return client

    def iothub_client_sample_run():
        try:
            client = iothub_client_init()

            while True:
                print ( "IoTHubClient waiting for commands, press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= WAIT_COUNT:
                    status = client.get_send_status()
                    print ( "Send status: %s" % status )
                    time.sleep(10)
                    status_counter += 1

        except IoTHubError as iothub_error:
            print ( "Unexpected error %s from IoTHub" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubClient sample stopped" )

        print_last_message_time(client)
    ```

1. 添加以下 main 函数：
   
    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python sample..." )
        print ( "    Protocol %s" % PROTOCOL )
        print ( "    Connection string=%s" % CONNECTION_STRING )

        iothub_client_sample_run()
    ```

1. 保存并关闭 **SimulatedDevice.py** 文件。


## <a name="send-a-cloud-to-device-message"></a>发送云到设备的消息
在本部分中，将创建一个 Python 控制台应用，用于向模拟设备应用发送云到设备消息。 需要使用 [IoT 中心入门]教程中添加的设备的设备 ID。 还需要中心的 IoT 中心连接字符串（位于 [Azure 门户]）。

1. 使用文本编辑器，创建一个 **SendCloudToDeviceMessage.py** 文件。

1. 在 **SendCloudToDeviceMessage.py** 文件的开头添加以下 `import` 语句和变量：
   
    ```python
    import random
    import sys
    import iothub_service_client
    from iothub_service_client import IoTHubMessaging, IoTHubMessage, IoTHubError

    OPEN_CONTEXT = 0
    FEEDBACK_CONTEXT = 1
    MESSAGE_COUNT = 1
    AVG_WIND_SPEED = 10.0
    MSG_TXT = "{\"service client sent a message\": %.2f}"
    ```

1. 将以下代码添加到 **SendCloudToDeviceMessage.py** 文件。 将“{IoTHubConnectionString}”占位符值替换为在 [IoT 中心入门]教程中创建的中心的 IoT 中心连接字符串。 将“{deviceId}”占位符替换为在 [IoT 中心入门]教程中添加的设备的设备 ID：
   
    ```python
    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"
    ```

1. 添加以下函数，用以在控制台中输出反馈消息：
   
    ```python
    def open_complete_callback(context):
        print ( 'open_complete_callback called with context: {0}'.format(context) )

    def send_complete_callback(context, messaging_result):
        context = 0
        print ( 'send_complete_callback called with context : {0}'.format(context) )
        print ( 'messagingResult : {0}'.format(messaging_result) )
    ```

1. 添加以下代码，以便在设备确认收到云到设备的消息时会消息发送到设备，并处理反馈消息：
   
    ```python
    def iothub_messaging_sample_run():
        try:
            iothub_messaging = IoTHubMessaging(CONNECTION_STRING)

            iothub_messaging.open(open_complete_callback, OPEN_CONTEXT)

            for i in range(0, MESSAGE_COUNT):
                print ( 'Sending message: {0}'.format(i) )
                msg_txt_formatted = MSG_TXT % (AVG_WIND_SPEED + (random.random() * 4 + 2))
                message = IoTHubMessage(bytearray(msg_txt_formatted, 'utf8'))

                # optional: assign ids
                message.message_id = "message_%d" % i
                message.correlation_id = "correlation_%d" % i
                # optional: assign properties
                prop_map = message.properties()
                prop_text = "PropMsg_%d" % i
                prop_map.add("Property", prop_text)

                iothub_messaging.send_async(DEVICE_ID, message, send_complete_callback, i)

            try:
                # Try Python 2.xx first
                raw_input("Press Enter to continue...\n")
            except:
                pass
                # Use Python 3.xx in the case of exception
                input("Press Enter to continue...\n")

            iothub_messaging.close()

        except IoTHubError as iothub_error:
            print ( "Unexpected error {0}" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubMessaging sample stopped" )
    ```

1. 添加以下 main 函数：
   
    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Service Client Messaging Python sample..." )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_messaging_sample_run()
    ```

1. 保存并关闭 **SendCloudToDeviceMessage.py** 文件。


## <a name="run-the-applications"></a>运行应用程序
现在，已准备就绪，可以运行应用程序了。

1. 打开命令提示符，并安装**用于 Python 的 Azure IoT 中心设备 SDK**。

    ```
    pip install azure-iothub-device-client
    ```

1. 在命令提示符下，运行以下命令来侦听云到设备消息：
   
    ```shell
    python SimulatedDevice.py 
    ```
   
    ![运行模拟设备应用][img-simulated-device]

1. 打开一个新的命令提示符，并安装**用于 Python 的 Azure IoT 中心服务 SDK**。

    ```
    pip install azure-iothub-service-client
    ```

1. 在命令提示符下，运行以下命令发送一条云到设备消息并等待消息反馈：
   
    ```shell
    python SendCloudToDeviceMessage.py 
    ```
   
    ![运行应用以发送云到设备的命令][img-send-command]
   
1. 注意设备收到的消息。

    ![收到的消息][img-message-recieved]


## <a name="next-steps"></a>后续步骤
在本教程中，已学习如何发送和接收云到设备的消息。 

若要查看使用 IoT 中心完成端到端解决方案的示例，请参阅 [Azure IoT 套件]。

若要了解有关使用 IoT 中心开发解决方案的详细信息，请参阅 [IoT 中心开发人员指南]。

<!-- Images -->
[img-simulated-device]: media/iot-hub-python-python-c2d/simulated-device.png
[img-send-command]:  media/iot-hub-python-python-c2d/send-command.png
[img-message-recieved]: media/iot-hub-python-python-c2d/message-recieved.png

<!-- Links -->
[lnk-python-download]: https://www.python.org/downloads/
[lnk-visual-c-redist]: http://www.microsoft.com/download/confirmation.aspx?id=48145
[lnk-node-download]: https://nodejs.org/en/download/
[lnk-install-pip]: https://pip.pypa.io/en/stable/installing/
[IoT 中心入门]: iot-hub-node-node-getstarted.md
[IoT Hub developer guide - C2D]: iot-hub-devguide-messaging.md
[IoT 中心开发人员指南]: iot-hub-devguide.md
[Azure IoT 开发人员中心]: http://www.azure.com/develop/iot
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md
[Transient Fault Handling]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Azure 门户]: https://portal.azure.com
[Azure IoT 套件]: https://azure.microsoft.com/documentation/suites/iot-suite/
