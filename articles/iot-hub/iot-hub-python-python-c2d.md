---
title: 使用 Azure IoT 中心发送云到设备消息 (Python) | Microsoft Docs
description: 如何使用用于 Python 的 Azure IoT SDK 将云到设备消息从 Azure IoT 中心发送到设备。 修改模拟设备应用以接收云到设备消息，并修改后端应用以发送云到设备消息。
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: robinsh
ms.openlocfilehash: 4cda59448856630468076ef63c51b8a216a31bd0
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/15/2019
ms.locfileid: "71001954"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-python"></a>使用 IoT 中心发送云到设备消息 (Python)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Azure IoT 中心是一项完全托管的服务，有助于在数百万台设备和单个解决方案后端之间实现安全可靠的双向通信。 [从设备将遥测数据发送到 IoT 中心](quickstart-send-telemetry-python.md)快速入门介绍了如何创建 IoT 中心、在其中预配设备标识，以及编写模拟设备应用来发送设备到云的消息。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

本教程在[从设备将遥测数据发送到 IoT 中心](quickstart-send-telemetry-python.md)的基础上编写。 其中了说明了如何：

* 通过 IoT 中心，将云到设备的消息从解决方案后端发送到单个设备。

* 在设备上接收云到设备的消息。

* 通过解决方案后端，请求确认收到从 IoT 中心发送到设备的消息（反馈）。

可以在 [IoT 中心开发人员指南](iot-hub-devguide-messaging.md)中找到有关云到设备消息的详细信息。

在本教程末尾，你将运行两个 Python 控制台应用：

* **SimulatedDevice.py**（[从设备将遥测数据发送到 IoT 中心](quickstart-send-telemetry-python.md)中创建的应用的修改版本），它连接到 IoT 中心并接收云到设备的消息。

* **SendCloudToDeviceMessage.py**，它将云到设备消息通过 IoT 中心发送到模拟设备应用，然后接收其传送确认。

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>先决条件

[!INCLUDE [iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-installation-notes.md)]

## <a name="receive-messages-in-the-simulated-device-app"></a>在模拟设备应用中接收消息

在本部分中，将创建一个 Python 控制台应用来模拟设备并从 IoT 中心接收云到设备消息。

1. 使用文本编辑器，创建一个 **SimulatedDevice.py** 文件。

2. 在 **SimulatedDevice.py** 文件的开头添加以下 `import` 语句和变量：

   ```python
    import threading
    from azure.iot.device import IoTHubDeviceClient

    RECEIVED_MESSAGES = 0
    ```

3. 将以下代码添加到 **SimulatedDevice.py** 文件。 将“{deviceConnectionString}”占位符值替换为你在[从设备将遥测数据发送到 IoT 中心](quickstart-send-telemetry-python.md)快速入门中创建的设备的设备连接字符串：

    ```python
    CONNECTION_STRING = "{deviceConnectionString}"
    ```

4. 添加以下函数，用以在控制台中输出收到的消息：

    ```python
    def message_listener(client):
        global RECEIVED_MESSAGES
        while True:
            message = client.receive_message()
            RECEIVED_MESSAGES += 1
            print("Message received")
            print( "    Data: <<{}>>".format(message.data) )
            print( "    Properties: {}".format(message.custom_properties))
            print( "    Total calls received: {}".format(RECEIVED_MESSAGES))
    ```

5. 添加以下代码，用以初始化客户端并等待接收云到设备消息：

    ```python
    def iothub_client_sample_run():
        try:
            client = iothub_client_init()

            message_listener_thread = threading.Thread(target=message_listener, args=(client,))
            message_listener_thread.daemon = True
            message_listener_thread.start()

            while True:
                time.sleep(1000)

        except KeyboardInterrupt:
            print ( "IoTHubDeviceClient sample stopped" )
    ```

6. 添加以下 main 函数：

    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python sample..." )
        print ( "IoTHubDeviceClient waiting for commands, press Ctrl-C to exit" )

        iothub_client_sample_run()
    ```

7. 保存并关闭 **SimulatedDevice.py** 文件。

## <a name="get-the-iot-hub-connection-string"></a>获取 IoT 中心连接字符串

在本文中，你将创建一项后端服务，用于通过你在[将遥测数据从设备发送到 IoT 中心](quickstart-send-telemetry-python.md)中创建的 IoT 中心发送云到设备消息。 若要发送云到设备消息，服务需要“服务连接”权限。 默认情况下，每个 IoT 中心都使用名为 **service** 的共享访问策略创建，该策略授予此权限。

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>发送云到设备的消息

在本部分中，将创建一个 Python 控制台应用，用于向模拟设备应用发送云到设备消息。 需要在[从设备将遥测数据发送到 IoT 中心](quickstart-send-telemetry-python.md)快速入门中添加的设备的设备 ID。 还需要先前在[获取 IoT 中心连接字符串](#get-the-iot-hub-connection-string)中复制的 IoT 中心连接字符串。

1. 使用文本编辑器，创建一个 **SendCloudToDeviceMessage.py** 文件。

2. 在 **SendCloudToDeviceMessage.py** 文件的开头添加以下 `import` 语句和变量：

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

3. 将以下代码添加到 **SendCloudToDeviceMessage.py** 文件。 将“{iot hub connection string}”和“{device id}”占位符值替换为之前记下的 IoT 中心连接字符串和设备 ID：

    ```python
    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"
    ```

4. 添加以下函数，用以在控制台中输出反馈消息：

    ```python
    def open_complete_callback(context):
        print ( 'open_complete_callback called with context: {0}'.format(context) )

    def send_complete_callback(context, messaging_result):
        context = 0
        print ( 'send_complete_callback called with context : {0}'.format(context) )
        print ( 'messagingResult : {0}'.format(messaging_result) )
    ```

5. 添加以下代码，以便在设备确认收到云到设备的消息时会消息发送到设备，并处理反馈消息：

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

6. 添加以下 main 函数：

    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Service Client Messaging Python sample..." )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_messaging_sample_run()
    ```

7. 保存并关闭 **SendCloudToDeviceMessage.py** 文件。

## <a name="run-the-applications"></a>运行应用程序

现在，已准备就绪，可以运行应用程序了。

1. 打开命令提示符，并安装**用于 Python 的 Azure IoT 中心设备 SDK**。

    ```shell
    pip install azure-iothub-device-client
    ```

2. 在命令提示符下，运行以下命令来侦听云到设备消息：

    ```shell
    python SimulatedDevice.py
    ```

    ![运行模拟设备应用](./media/iot-hub-python-python-c2d/simulated-device.png)

3. 打开一个新的命令提示符，并安装**用于 Python 的 Azure IoT 中心服务 SDK**。

    ```shell
    pip install azure-iothub-service-client
    ```

4. 在命令提示符下，运行以下命令发送一条云到设备消息并等待消息反馈：

    ```shell
    python SendCloudToDeviceMessage.py
    ```

    ![运行应用以发送云到设备的命令](./media/iot-hub-python-python-c2d/send-command.png)

5. 注意设备收到的消息。

    ![收到的消息](./media/iot-hub-python-python-c2d/message-received.png)

## <a name="next-steps"></a>后续步骤

在本教程中，已学习如何发送和接收云到设备的消息。

若要查看使用 IoT 中心完成端到端解决方案的示例，请参阅 [Azure IoT 远程监视解决方案加速器](https://azure.microsoft.com/documentation/suites/iot-suite/)。

若要了解有关使用 IoT 中心开发解决方案的详细信息，请参阅 [IoT 中心开发人员指南](iot-hub-devguide.md)。
