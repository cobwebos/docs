---
title: "Azure IoT 中心入门 (Python) | Microsoft Docs"
description: "了解如何通过用于 Python 的 IoT SDK 将设备到云消息发送到 Azure IoT 中心。 创建模拟的设备和服务应用，以便通过 IoT 中心注册设备、发送消息和读取消息。"
services: iot-hub
author: dsk-2015
manager: timlt
editor: 
ms.service: iot-hub
ms.devlang: python
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/25/2017
ms.author: dkshir
ms.custom: na
ms.openlocfilehash: eec9eb9d5b5ecc0b33ce03f33b9f1600600f6c4e
ms.sourcegitcommit: e38120a5575ed35ebe7dccd4daf8d5673534626c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2017
---
# <a name="connect-your-simulated-device-to-your-iot-hub-using-python"></a>使用 Python 将模拟设备连接到 IoT 中心
[!INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

在本教程结束时，会创建两个 Python 应用：

* **CreateDeviceIdentity.py**，用于创建设备标识和关联的安全密钥以连接模拟设备应用。
* **SimulatedDevice.py**，使用前面创建的设备标识连接到 IoT 中心，并使用 MQTT 协议定期发送遥测消息。

> [!NOTE]
> [Azure IoT SDK][lnk-hub-sdks] 一文提供了各种 Azure IoT SDK 的相关信息，用户可以使用这些 SDK 构建可在设备和解决方案后端上运行的应用程序。
> 
> 

要完成本教程，需要以下各项：

* [Python 2.x 或 3.x][lnk-python-download]。 请确保根据安装程序的要求，使用 32 位或 64 位安装。 在安装过程中出现提示时，请确保将 Python 添加到特定于平台的环境变量中。 如果使用 Python 2.x，则可能需要[安装或升级 pip - Python 包管理系统][lnk-install-pip]。
* 如果使用 Windows OS，则请安装 [Visual C++ 可再发行组件包][lnk-visual-c-redist]，以便使用 Python 中的本机 DLL。
* [Node.js 4.0 或更高版本][lnk-node-download]。 请确保根据安装程序的要求，使用 32 位或 64 位安装。 这是安装 [IoT 中心资源管理器工具][lnk-iot-hub-explorer]所必需的。
* 有效的 Azure 帐户。 如果没有帐户，只需几分钟即可创建一个[免费帐户][lnk-free-trial]。

> [!NOTE]
> 适用于 `azure-iothub-service-client` 和 `azure-iothub-device-client` 的 pip 包目前仅供 Windows OS 使用。 对于 Linux/Mac OS，请参阅[准备适用于 Python 的开发环境][lnk-python-devbox]一文中特定于 Linux 和 Mac OS 的部分。
> 

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

现在已创建 IoT 中心。 请在本教程的其余部分使用 IoT 中心主机名和 IoT 中心连接字符串。

> [!NOTE]
> 也可使用基于 Python 或 Node.js 的 Azure CLI，在命令行中轻松创建 IoT 中心。 [使用 Azure CLI 2.0 创建 IoT 中心][lnk-azure-cli-hub]一文介绍了执行该操作的快速步骤。 
> 

## <a name="create-a-device-identity"></a>创建设备标识
本部分列出了创建 Python 控制台应用的步骤，该应用可在 IoT 中心的标识注册表中创建设备标识。 设备无法连接到 IoT 中心，除非它在标识注册表中具有条目。 有关详细信息，请参阅 [IoT 中心开发人员指南][lnk-devguide-identity]中的**标识注册表**部分。 运行此控制台应用时，它将生成唯一的设备 ID 和密钥，当设备向 IoT 中心发送设备到云的消息时，可以用于标识设备本身。

1. 打开命令提示符，并安装**用于 Python 的 Azure IoT 中心服务 SDK**。 在安装 SDK 之后关闭命令提示符。

    ```
    pip install azure-iothub-service-client
    ```

2. 创建名为 **CreateDeviceIdentity.py** 的 Python 文件。 在[所选 Python 编辑器/IDE][lnk-python-ide-list]（例如，默认的 [IDLE][lnk-idle]）中将其打开。

3. 添加以下代码，从服务 SDK 导入所需模块：

    ```python
    import sys
    import iothub_service_client
    from iothub_service_client import IoTHubRegistryManager, IoTHubRegistryManagerAuthMethod
    from iothub_service_client import IoTHubDeviceStatus, IoTHubError
    ```
2. 添加以下代码，将 `[IoTHub Connection String]` 的占位符替换为在上一部分为 IoT 中心创建的连接字符串。 可以将任何名称用作 `DEVICE_ID`。
   
    ```python
    CONNECTION_STRING = "[IoTHub Connection String]"
    DEVICE_ID = "MyFirstPythonDevice"
    ```
   [!INCLUDE [iot-hub-pii-note-naming-device](../../includes/iot-hub-pii-note-naming-device.md)]

3. 添加以下函数，输出部分设备信息。

    ```python
    def print_device_info(title, iothub_device):
        print ( title + ":" )
        print ( "iothubDevice.deviceId                    = {0}".format(iothub_device.deviceId) )
        print ( "iothubDevice.primaryKey                  = {0}".format(iothub_device.primaryKey) )
        print ( "iothubDevice.secondaryKey                = {0}".format(iothub_device.secondaryKey) )
        print ( "iothubDevice.connectionState             = {0}".format(iothub_device.connectionState) )
        print ( "iothubDevice.status                      = {0}".format(iothub_device.status) )
        print ( "iothubDevice.lastActivityTime            = {0}".format(iothub_device.lastActivityTime) )
        print ( "iothubDevice.cloudToDeviceMessageCount   = {0}".format(iothub_device.cloudToDeviceMessageCount) )
        print ( "iothubDevice.isManaged                   = {0}".format(iothub_device.isManaged) )
        print ( "iothubDevice.authMethod                  = {0}".format(iothub_device.authMethod) )
        print ( "" )
    ```
3. 添加以下函数，使用注册表管理器创建设备标识。 

    ```python
    def iothub_createdevice():
        try:
            iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)
            auth_method = IoTHubRegistryManagerAuthMethod.SHARED_PRIVATE_KEY
            new_device = iothub_registry_manager.create_device(DEVICE_ID, "", "", auth_method)
            print_device_info("CreateDevice", new_device)

        except IoTHubError as iothub_error:
            print ( "Unexpected error {0}".format(iothub_error) )
            return
        except KeyboardInterrupt:
            print ( "iothub_createdevice stopped" )
    ```
4. 最后，添加 main 函数（如下所示）并保存文件。

    ```python
    if __name__ == '__main__':
        print ( "" )
        print ( "Python {0}".format(sys.version) )
        print ( "Creating device using the Azure IoT Hub Service SDK for Python" )
        print ( "" )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_createdevice()
    ```
5. 在命令提示符处运行 **CreateDeviceIdentity.py**，如下所示：

    ```python
    python CreateDeviceIdentity.py
    ```
6. 此时会看到系统创建模拟设备。 记下此设备的 **deviceId** 和 **primaryKey**。 稍后在创建连接到作为设备的 IoT 中心的应用程序时需要这些值。

    ![创建设备成功][1]

> [!NOTE]
> IoT 中心标识注册表只存储设备标识，以启用对 IoT 中心的安全访问。 它存储设备 ID 和密钥作为安全凭据，以及启用或禁用标志（可用于禁用对单个设备的访问）。 如果应用程序需要存储其他特定于设备的元数据，则应使用特定于应用程序的存储。 有关详细信息，请参阅 [IoT 中心开发人员指南][lnk-devguide-identity]。
> 
> 


## <a name="create-a-simulated-device-app"></a>创建模拟设备应用程序
本部分列出了创建 Python 控制台应用的步骤，该应用可模拟设备并向 IoT 中心发送设备到云消息。

1. 打开新的命令提示符，并安装用于 Python 的 Azure IoT 中心设备 SDK，如下所示。 在安装之后关闭命令提示符。

    ```
    pip install azure-iothub-device-client
    ```
2. 创建名为 **SimulatedDevice.py** 的文件。 在所选 Python 编辑器/IDE（例如 IDLE）中打开该文件。

3. 添加以下代码，从设备 SDK 导入所需模块。

    ```python
    import random
    import time
    import sys
    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult
    from iothub_client import IoTHubMessage, IoTHubMessageDispositionResult, IoTHubError, DeviceMethodReturnValue
    ```
4. 添加以下代码，并将 `[IoTHub Device Connection String]` 的占位符替换为设备的连接字符串。 设备连接字符串通常采用 `HostName=<hostName>;DeviceId=<deviceId>;SharedAccessKey=<primaryKey>` 格式。 使用在上一部分创建的设备的 **deviceId** 和 **primaryKey** 分别替换 `<deviceId>` 和 `<primaryKey>`。 将 `<hostName>` 替换为 IoT 中心的主机名，通常为 `<IoT hub name>.azure-devices.net` 格式。

    ```python
    # String containing Hostname, Device Id & Device Key in the format
    CONNECTION_STRING = "[IoTHub Device Connection String]"
    # choose HTTP, AMQP or MQTT as transport protocol
    PROTOCOL = IoTHubTransportProvider.MQTT
    MESSAGE_TIMEOUT = 10000
    AVG_WIND_SPEED = 10.0
    SEND_CALLBACKS = 0
    MSG_TXT = "{\"deviceId\": \"MyFirstPythonDevice\",\"windSpeed\": %.2f}"    
    ```
5. 添加以下代码，定义发送确认回调。 

    ```python
    def send_confirmation_callback(message, result, user_context):
        global SEND_CALLBACKS
        print ( "Confirmation[%d] received for message with result = %s" % (user_context, result) )
        map_properties = message.properties()
        print ( "    message_id: %s" % message.message_id )
        print ( "    correlation_id: %s" % message.correlation_id )
        key_value_pair = map_properties.get_internals()
        print ( "    Properties: %s" % key_value_pair )
        SEND_CALLBACKS += 1
        print ( "    Total calls confirmed: %d" % SEND_CALLBACKS )
    ```
6. 添加以下代码，初始化设备客户端。

    ```python
    def iothub_client_init():
        # prepare iothub client
        client = IoTHubClient(CONNECTION_STRING, PROTOCOL)
        # set the time until a message times out
        client.set_option("messageTimeout", MESSAGE_TIMEOUT)
        client.set_option("logtrace", 0)
        client.set_option("product_info", "HappyPath_Simulated-Python")
        return client
    ```
7. 添加以下函数，将消息格式化并将其从模拟设备发送到 IoT 中心。

    ```python
    def iothub_client_telemetry_sample_run():

        try:
            client = iothub_client_init()
            print ( "IoT Hub device sending periodic messages, press Ctrl-C to exit" )
            message_counter = 0

            while True:
                msg_txt_formatted = MSG_TXT % (AVG_WIND_SPEED + (random.random() * 4 + 2))
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
                prop_text = "PropMsg_%d" % message_counter
                prop_map.add("Property", prop_text)

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
8. 最后，添加 main 函数。 

    ```python
    if __name__ == '__main__':
        print ( "Simulating a device using the Azure IoT Hub Device SDK for Python" )
        print ( "    Protocol %s" % PROTOCOL )
        print ( "    Connection string=%s" % CONNECTION_STRING )

        iothub_client_telemetry_sample_run()
    ```
9. 保存并关闭 **SimulatedDevice.py** 文件。 现在可以运行该应用了。

> [!NOTE]
> 为简单起见，本教程不实现任何重试策略。 在生产代码中，应该按 MSDN 文章 [Transient Fault Handling][lnk-transient-faults]（暂时性故障处理）中所述实施重试策略（例如指数性的回退）。
> 
> 

## <a name="receive-messages-from-your-simulated-device"></a>从模拟设备接收消息
若要从设备接收遥测消息，需使用 IoT 中心公开的与[事件中心][lnk-event-hubs-overview]兼容的终结点，以便读取设备到云消息。 阅读[事件中心入门][lnk-eventhubs-tutorial]教程，了解如何处理来自事件中心的消息，这些事件中心所对应的终结点与 IoT 中心的事件中心兼容。 在 Python 中，事件中心尚不支持遥测，因此可创建一个基于 [Node.js](iot-hub-node-node-getstarted.md#D2C_node) 或 [.NET](iot-hub-csharp-csharp-getstarted.md#D2C_csharp) 事件中心的控制台应用，以便从 IoT 中心读取设备到云消息。 本教程介绍如何使用 [IoT 中心资源管理器工具][lnk-iot-hub-explorer]读取这些设备消息。

1. 打开命令提示符并安装 IoT 中心资源管理器。 

    ```
    npm install -g iothub-explorer
    ```

2. 在命令提示符处运行以下命令，开始监视设备发出的设备到云消息。 在 `--login` 后的占位符中使用 IoT 中心的连接字符串。

    ```
    iothub-explorer monitor-events MyFirstPythonDevice --login "[IoTHub connection string]"
    ```

3. 打开新的命令提示符，导航到包含 **SimulatedDevice.py** 文件的目录。

4. 运行 **SimulatedDevice.py** 文件，该文件定期将遥测数据发送到 IoT 中心。 
   
    ```
    python SimulatedDevice.py
    ```
5. 运行前一部分的 IoT 中心资源管理器，在命令提示符处观察设备消息。 

    ![Python 设备到云消息][2]

## <a name="next-steps"></a>后续步骤
在本教程中，你已在 Azure 门户中配置了新的 IoT 中心，并在 IoT 中心的标识注册表中创建了设备标识。 已使用此设备标识来让模拟设备应用向 IoT 中心发送设备到云的消息。 已借助 IoT 中心资源管理器工具观察到 IoT 中心接收的消息。 

若要深入探索用于 Azure IoT 中心的 Python SDK 的用法，请访问[此 Git 中心存储库][lnk-python-github]。 若要查看用于 Python 的 Azure IoT 中心服务 SDK 的消息传送功能，可下载并运行 [iothub_messaging_sample.py][lnk-messaging-sample]。 若要了解如何通过用于 Python 的 Azure IoT 中心设备 SDK 进行设备端模拟，可下载并运行 [iothub_client_sample.py][lnk-client-sample]。

若要继续了解 IoT 中心入门知识并浏览其他 IoT 方案，请参阅：

* [连接设备][lnk-connect-device]
* [设备管理入门][lnk-device-management]
* [Azure IoT 边缘入门][lnk-iot-edge]

若要了解如何扩展 IoT 解决方案和如何大规模处理设备到云的消息，请参阅 [Process device-to-cloud messages][lnk-process-d2c-tutorial]（处理设备到云的消息）教程。
[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]

<!-- Images. -->
[1]: ./media/iot-hub-python-getstarted/createdevice.png
[2]: ./media/iot-hub-python-getstarted/sendd2cmessage.png

<!-- Links -->
[lnk-python-download]: https://www.python.org/downloads/
[lnk-visual-c-redist]: http://www.microsoft.com/download/confirmation.aspx?id=48145
[lnk-node-download]: https://nodejs.org/en/download/
[lnk-install-pip]: https://pip.pypa.io/en/stable/installing/
[lnk-azure-cli-hub]: https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-create-using-cli
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-idle]: https://docs.python.org/3/library/idle.html
[lnk-python-ide-list]: https://wiki.python.org/moin/IntegratedDevelopmentEnvironments
[lnk-iot-hub-explorer]: https://github.com/Azure/iothub-explorer
[lnk-python-github]: https://github.com/Azure/azure-iot-sdk-python
[lnk-messaging-sample]: https://github.com/Azure/azure-iot-sdk-python/blob/master/service/samples/iothub_messaging_sample.py
[lnk-client-sample]: https://github.com/Azure/azure-iot-sdk-python/blob/master/device/samples/iothub_client_sample.py

[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide-identity-registry.md
[lnk-event-hubs-overview]: ../event-hubs/event-hubs-overview.md
[lnk-python-devbox]: https://github.com/Azure/azure-iot-sdk-python/blob/master/doc/python-devbox-setup.md

[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-iot-edge]: iot-hub-linux-iot-edge-get-started.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/
