---
title: 使用 Azure IoT 中心发送云到设备消息 (Python) | Microsoft Docs
description: 如何使用用于 Python 的 Azure IoT SDK 将云到设备消息从 Azure IoT 中心发送到设备。 修改模拟设备应用以接收云到设备消息，并修改后端应用以发送云到设备消息。
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: robinsh
ms.custom: mqtt, devx-track-python
ms.openlocfilehash: ad6399a4713520ca0550d143cf3f19f87d55337c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87876795"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-python"></a>使用 IoT 中心发送云到设备消息 (Python)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Azure IoT 中心是一项完全托管的服务，有助于在数百万台设备和单个解决方案后端之间实现安全可靠的双向通信。 [从设备将遥测数据发送到 IoT 中心](quickstart-send-telemetry-python.md)快速入门介绍了如何创建 IoT 中心、在其中预配设备标识，以及编写模拟设备应用来发送设备到云的消息。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

本教程在[从设备将遥测数据发送到 IoT 中心](quickstart-send-telemetry-python.md)的基础上编写。 其中了说明了如何：

* 通过 IoT 中心，将云到设备的消息从解决方案后端发送到单个设备。

* 在设备上接收云到设备的消息。

可以在 [IoT 中心开发人员指南](iot-hub-devguide-messaging.md)中找到有关云到设备消息的详细信息。

在本教程末尾，你将运行两个 Python 控制台应用：

* **SimulatedDevice.py**（[从设备将遥测数据发送到 IoT 中心](quickstart-send-telemetry-python.md)中创建的应用的修改版本），它连接到 IoT 中心并接收云到设备的消息。

* SendCloudToDeviceMessage.py  ，它将云到设备消息通过 IoT 中心发送到模拟设备应用。

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>先决条件

[!INCLUDE [iot-hub-include-python-v2-installation-notes](../../includes/iot-hub-include-python-v2-installation-notes.md)]

* 确保已在防火墙中打开端口 8883。 本文中的设备示例使用 MQTT 协议，该协议通过端口 8883 进行通信。 在某些公司和教育网络环境中，此端口可能被阻止。 有关解决此问题的更多信息和方法，请参阅[连接到 IoT 中心(MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)。

## <a name="receive-messages-in-the-simulated-device-app"></a>在模拟设备应用上接收消息

在本部分中，将创建一个 Python 控制台应用来模拟设备并从 IoT 中心接收云到设备消息。

1. 在工作目录中的命令提示符下，安装**适用于 Python 的 Azure IoT 中心设备 SDK**：

    ```cmd/sh
    pip install azure-iot-device
    ```

1. 使用文本编辑器，创建一个名为“SimulatedDevice.py”  的文件。

1. 在 **SimulatedDevice.py** 文件的开头添加以下 `import` 语句和变量：

    ```python
    import threading
    import time
    from azure.iot.device import IoTHubDeviceClient

    RECEIVED_MESSAGES = 0
    ```

1. 将以下代码添加到 **SimulatedDevice.py** 文件。 将 `{deviceConnectionString}` 占位符值替换为在将 [遥测从设备发送到 IoT 中心](quickstart-send-telemetry-python.md) 快速入门中创建的设备的设备连接字符串：

    ```python
    CONNECTION_STRING = "{deviceConnectionString}"
    ```

1. 添加以下函数，用以在控制台中输出收到的消息：

    ```python
    def message_listener(client):
        global RECEIVED_MESSAGES
        while True:
            message = client.receive_message()
            RECEIVED_MESSAGES += 1
            print("\nMessage received:")

            #print data and both system and application (custom) properties
            for property in vars(message).items():
                print ("    {0}".format(property))

            print( "Total calls received: {}".format(RECEIVED_MESSAGES))
            print()
    ```

1. 添加以下代码，用以初始化客户端并等待接收云到设备消息：

    ```python
    def iothub_client_sample_run():
        try:
            client = IoTHubDeviceClient.create_from_connection_string(CONNECTION_STRING)

            message_listener_thread = threading.Thread(target=message_listener, args=(client,))
            message_listener_thread.daemon = True
            message_listener_thread.start()

            while True:
                time.sleep(1000)

        except KeyboardInterrupt:
            print ( "IoT Hub C2D Messaging device sample stopped" )
    ```

1. 添加以下 main 函数：

    ```python
    if __name__ == '__main__':
        print ( "Starting the Python IoT Hub C2D Messaging device sample..." )
        print ( "Waiting for C2D messages, press Ctrl-C to exit" )

        iothub_client_sample_run()
    ```

1. 保存并关闭 SimulatedDevice.py  文件。

## <a name="get-the-iot-hub-connection-string"></a>获取 IoT 中心连接字符串

在本文中，你会创建一个后端服务，以通过在[将遥测数据从设备发送到 IoT 中心](quickstart-send-telemetry-python.md)中创建的 IoT 中心，发送云到设备消息。 若要发送云到设备消息，服务需要“服务连接”权限。  默认情况下，每个 IoT 中心都使用名为 **service** 的共享访问策略创建，该策略授予此权限。

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>发送云到设备的消息

在本部分中，将创建一个 Python 控制台应用，用于向模拟设备应用发送云到设备消息。 需要在[从设备将遥测数据发送到 IoT 中心](quickstart-send-telemetry-python.md)快速入门中添加的设备的设备 ID。 还需要以前在[获取 IoT 中心连接字符串](#get-the-iot-hub-connection-string)中复制的 IoT 中心连接字符串。

1. 在工作目录中，打开命令提示符并安装安装适用于 Python 的 Azure IoT 中心服务 SDK  。

   ```cmd/sh
   pip install azure-iot-hub
   ```

1. 使用文本编辑器，创建一个名为“SendCloudToDeviceMessage.py”  的文件。

1. 在 **SendCloudToDeviceMessage.py** 文件的开头添加以下 `import` 语句和变量：

    ```python
    import random
    import sys
    from azure.iot.hub import IoTHubRegistryManager

    MESSAGE_COUNT = 2
    AVG_WIND_SPEED = 10.0
    MSG_TXT = "{\"service client sent a message\": %.2f}"
    ```

1. 将以下代码添加到 **SendCloudToDeviceMessage.py** 文件。 将 `{iot hub connection string}` 和 `{device id}` 占位符值替换为之前记下的 IoT 中心连接字符串和设备ID：

    ```python
    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"
    ```

1. 添加以下代码，以便将消息发送到设备：

    ```python
    def iothub_messaging_sample_run():
        try:
            # Create IoTHubRegistryManager
            registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

            for i in range(0, MESSAGE_COUNT):
                print ( 'Sending message: {0}'.format(i) )
                data = MSG_TXT % (AVG_WIND_SPEED + (random.random() * 4 + 2))

                props={}
                # optional: assign system properties
                props.update(messageId = "message_%d" % i)
                props.update(correlationId = "correlation_%d" % i)
                props.update(contentType = "application/json")

                # optional: assign application properties
                prop_text = "PropMsg_%d" % i
                props.update(testProperty = prop_text)

                registry_manager.send_c2d_message(DEVICE_ID, data, properties=props)

            try:
                # Try Python 2.xx first
                raw_input("Press Enter to continue...\n")
            except:
                pass
                # Use Python 3.xx in the case of exception
                input("Press Enter to continue...\n")

        except Exception as ex:
            print ( "Unexpected error {0}" % ex )
            return
        except KeyboardInterrupt:
            print ( "IoT Hub C2D Messaging service sample stopped" )
    ```

1. 添加以下 main 函数：

    ```python
    if __name__ == '__main__':
        print ( "Starting the Python IoT Hub C2D Messaging service sample..." )

        iothub_messaging_sample_run()
    ```

1. 保存并关闭 **SendCloudToDeviceMessage.py** 文件。

## <a name="run-the-applications"></a>运行应用程序

现在，已准备就绪，可以运行应用程序了。

1. 在工作目录中的命令提示符处，运行以下命令以侦听云到设备消息：

    ```shell
    python SimulatedDevice.py
    ```

    ![运行模拟设备应用](./media/iot-hub-python-python-c2d/device-1.png)

1. 在工作目录中打开一个新的命令提示符，然后运行以下命令以发送云到设备消息：

    ```shell
    python SendCloudToDeviceMessage.py
    ```

    ![运行应用以发送云到设备的命令](./media/iot-hub-python-python-c2d/service.png)

1. 记下设备收到的消息。

    ![收到的消息](./media/iot-hub-python-python-c2d/device-2.png)

## <a name="next-steps"></a>后续步骤

在本教程中，已学习如何发送和接收云到设备的消息。

若要查看使用 IoT 中心完成端到端解决方案的示例，请参阅 [Azure IoT 远程监视解决方案加速器](https://azure.microsoft.com/documentation/suites/iot-suite/)。

若要了解有关使用 IoT 中心开发解决方案的详细信息，请参阅 [IoT 中心开发人员指南](iot-hub-devguide.md)。
