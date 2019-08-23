---
title: Azure IoT 中心设备管理入门 (Python) | Microsoft Docs
description: 如何使用 IoT 中心设备管理进行远程设备重启。 使用适用于 Python 的 Azure IoT SDK 实现包含直接方法的模拟设备应用和调用直接方法的服务应用。
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: robinsh
ms.openlocfilehash: 287dbd3d6da4aa2bf5bd1da652cdeaeda3136321
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/22/2019
ms.locfileid: "69907742"
---
# <a name="get-started-with-device-management-python"></a>设备管理入门 (Python)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

本教程演示如何：

* 使用 Azure 门户创建 IoT 中心，以及如何在 IoT 中心创建设备标识。

* 创建包含重新启动该设备的直接方法的模拟设备应用。 直接方法是从云中调用的。

* 创建一个 Python 控制台应用，它通过 IoT 中心调用模拟设备应用中的重新启动直接方法。

在本教程结束时，会获得两个 Python 控制台应用：

* **dmpatterns_getstarted_device.py**，它使用先前创建的设备标识连接到 IoT 中心，接收重新启动直接方法，模拟物理重新启动，并报告上次重新启动的时间。

* **dmpatterns_getstarted_service.py**，它调用模拟设备应用中的直接方法，显示响应，并显示更新后的报告属性。

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

下面是先决条件的安装说明。

[!INCLUDE [iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-installation-notes.md)]

## <a name="create-an-iot-hub"></a>创建 IoT 中心

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>在 IoT 中心内注册新设备

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>创建模拟设备应用程序

本部分的操作：

* 创建一个 Python 控制台应用，用于响应通过云调用的直接方法

* 模拟设备重新启动

* 通过报告的属性，设备孪生查询可标识设备及设备上次重新启动的时间

1. 在命令提示符处，运行以下命令以安装 azure-iot-device-client 包：

    ```cmd/sh
    pip install azure-iothub-device-client
    ```

   > [!NOTE]
   > 用于 azure iothub 和 azure iothub 设备客户端的 pip 包目前仅适用于 Windows 操作系统。 对于 Linux/Mac OS, 请参阅为[Python 发布准备开发环境](https://github.com/Azure/azure-iot-sdk-python/blob/master/doc/python-devbox-setup.md)中的 linux 和 Mac OS 特定部分。
   >

2. 使用文本编辑器, 在工作目录中创建一个名为**dmpatterns_getstarted_device**的文件。

3. 在 **dmpatterns_getstarted_device.py** 文件开头添加以下 `import` 语句。

    ```python
    import random
    import time, datetime
    import sys

    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult, IoTHubError, DeviceMethodReturnValue
    ```

4. 添加变量（包括 CONNECTION_STRING 变量）和客户端初始化。  将`{deviceConnectionString}`占位符值替换为设备连接字符串。 你之前在[IoT 中心内注册了](#register-a-new-device-in-the-iot-hub)此连接字符串。  

    ```python
    CONNECTION_STRING = "{deviceConnectionString}"
    PROTOCOL = IoTHubTransportProvider.MQTT

    CLIENT = IoTHubClient(CONNECTION_STRING, PROTOCOL)

    WAIT_COUNT = 5

    SEND_REPORTED_STATE_CONTEXT = 0
    METHOD_CONTEXT = 0

    SEND_REPORTED_STATE_CALLBACKS = 0
    METHOD_CALLBACKS = 0
    ```

5. 添加以下函数回调，实现设备上的直接方法。

    ```python
    def send_reported_state_callback(status_code, user_context):
        global SEND_REPORTED_STATE_CALLBACKS

        print ( "Device twins updated." )

    def device_method_callback(method_name, payload, user_context):
        global METHOD_CALLBACKS

        if method_name == "rebootDevice":
            print ( "Rebooting device..." )
            time.sleep(20)

            print ( "Device rebooted." )

            current_time = str(datetime.datetime.now())
            reported_state = "{\"rebootTime\":\"" + current_time + "\"}"
            CLIENT.send_reported_state(reported_state, len(reported_state), send_reported_state_callback, SEND_REPORTED_STATE_CONTEXT)

            print ( "Updating device twins: rebootTime" )

        device_method_return_value = DeviceMethodReturnValue()
        device_method_return_value.response = "{ \"Response\": \"This is the response from the device\" }"
        device_method_return_value.status = 200

        return device_method_return_value
    ```

6. 启动直接方法侦听器并等待。

    ```python
    def iothub_client_init():
        if CLIENT.protocol == IoTHubTransportProvider.MQTT or client.protocol == IoTHubTransportProvider.MQTT_WS:
            CLIENT.set_device_method_callback(device_method_callback, METHOD_CONTEXT)

    def iothub_client_sample_run():
        try:
            iothub_client_init()

            while True:
                print ( "IoTHubClient waiting for commands, press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= WAIT_COUNT:
                    time.sleep(10)
                    status_counter += 1

        except IoTHubError as iothub_error:
            print ( "Unexpected error %s from IoTHub" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubClient sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python sample..." )
        print ( "    Protocol %s" % PROTOCOL )
        print ( "    Connection string=%s" % CONNECTION_STRING )

        iothub_client_sample_run()
    ```

7. 保存并关闭 **dmpatterns_getstarted_device.py** 文件。

> [!NOTE]
> 为简单起见，本教程不实现任何重试策略。 在生产代码中，应该按文章 [Transient Fault Handling](/azure/architecture/best-practices/transient-faults)（暂时性故障处理）中所述实施重试策略（例如指数性的回退）。

## <a name="get-the-iot-hub-connection-string"></a>获取 IoT 中心连接字符串

[!INCLUDE [iot-hub-howto-device-management-shared-access-policy-text](../../includes/iot-hub-howto-device-management-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>使用直接方法在设备上触发远程重新启动

此部分将创建一个 Python 控制台应用，以使用直接方法在设备上启动远程重新启动。 该应用使用设备孪生查询来搜索该设备的上次重新启动时间。

1. 在命令提示符处，运行以下命令以安装 azure-iot-service-client 包：

    ```cmd/sh
    pip install azure-iothub-service-client
    ```

   > [!NOTE]
   > 用于 azure iothub 和 azure iothub 设备客户端的 pip 包目前仅适用于 Windows 操作系统。 对于 Linux/Mac OS, 请参阅为[Python 发布准备开发环境](https://github.com/Azure/azure-iot-sdk-python/blob/master/doc/python-devbox-setup.md)中的 linux 和 Mac OS 特定部分。
   >

2. 使用文本编辑器, 在工作目录中创建一个名为**dmpatterns_getstarted_service**的文件。

3. 在 **dmpatterns_getstarted_service.py** 文件开头添加以下 `import` 语句。

    ```python
    import sys, time
    import iothub_service_client

    from iothub_service_client import IoTHubDeviceMethod, IoTHubError, IoTHubDeviceTwin
    ```

4. 添加以下变量声明。 将`{IoTHubConnectionString}`占位符值替换为之前在[获取 iot 中心连接字符串](#get-the-iot-hub-connection-string)中复制的 iot 中心连接字符串。 将`{deviceId}`占位符值替换为在[IoT 中心注册新设备](#register-a-new-device-in-the-iot-hub)中注册的设备 ID。

    ```python
    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"

    METHOD_NAME = "rebootDevice"
    METHOD_PAYLOAD = "{\"method_number\":\"42\"}"
    TIMEOUT = 60
    WAIT_COUNT = 10
    ```

5. 添加以下函数以调用设备方法重新启动目标设备，然后查询设备孪生并获取上次重新启动时间。

    ```python
    def iothub_devicemethod_sample_run():
        try:
            iothub_twin_method = IoTHubDeviceTwin(CONNECTION_STRING)
            iothub_device_method = IoTHubDeviceMethod(CONNECTION_STRING)

            print ( "" )
            print ( "Invoking device to reboot..." )

            response = iothub_device_method.invoke(DEVICE_ID, METHOD_NAME, METHOD_PAYLOAD, TIMEOUT)

            print ( "" )
            print ( "Successfully invoked the device to reboot." )

            print ( "" )
            print ( response.payload )

            while True:
                print ( "" )
                print ( "IoTHubClient waiting for commands, press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= WAIT_COUNT:
                    twin_info = iothub_twin_method.get_twin(DEVICE_ID)

                    if twin_info.find("rebootTime") != -1:
                        print ( "Last reboot time: " + twin_info[twin_info.find("rebootTime")+11:twin_info.find("rebootTime")+37])
                    else:
                        print ("Waiting for device to report last reboot time...")

                    time.sleep(5)
                    status_counter += 1

        except IoTHubError as iothub_error:
            print ( "" )
            print ( "Unexpected error {0}".format(iothub_error) )
            return
        except KeyboardInterrupt:
            print ( "" )
            print ( "IoTHubDeviceMethod sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub Service Client DeviceManagement Python sample..." )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_devicemethod_sample_run()
    ```

6. 保存并关闭 **dmpatterns_getstarted_service.py** 文件。

## <a name="run-the-apps"></a>运行应用

你现在已准备好运行应用。

1. 在命令提示符处，运行以下命令以开始侦听重新启动直接方法。

    ```cmd/sh
    python dmpatterns_getstarted_device.py
    ```

2. 在另一个命令提示符处，运行以下命令以触发远程重新启动并查询设备孪生以查找上次重新启动时间。

    ```cmd/sh
    python dmpatterns_getstarted_service.py
    ```

3. 可在控制台查看对直接方法的设备响应。

   下面显示了对重新启动直接方法的设备响应:

   ![模拟设备应用输出](./media/iot-hub-python-python-device-management-get-started/device.png)

   下面显示了调用 reboot 直接方法并轮询设备克隆状态的服务:

   ![触发重新启动服务输出](./media/iot-hub-python-python-device-management-get-started/service.png)

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]