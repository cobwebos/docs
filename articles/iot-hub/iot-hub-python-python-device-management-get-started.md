---
title: Azure IoT 中心设备管理入门 (Python) | Microsoft Docs
description: 如何使用 IoT 中心设备管理进行远程设备重启。 使用适用于 Python 的 Azure IoT SDK 实现包含直接方法的模拟设备应用和调用直接方法的服务应用。
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 01/17/2020
ms.author: robinsh
ms.openlocfilehash: 6d6a50db42924d868b57cacc415246ee6990859c
ms.sourcegitcommit: 9add86fb5cc19edf0b8cd2f42aeea5772511810c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/09/2020
ms.locfileid: "77110471"
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

## <a name="prerequisites"></a>先决条件

[!INCLUDE [iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-v2-installation-notes.md)]

* 请确保已在防火墙中打开端口8883。 本文中的设备示例使用了 MQTT 协议，该协议通过端口8883进行通信。 此端口可能在某些企业和教育网络环境中被阻止。 有关此问题的详细信息和解决方法，请参阅[连接到 IoT 中心（MQTT）](iot-hub-mqtt-support.md#connecting-to-iot-hub)。

## <a name="create-an-iot-hub"></a>创建 IoT 中心

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>在 IoT 中心内注册新设备

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>创建模拟设备应用程序

本部分的操作：

* 创建一个 Python 控制台应用，用于响应通过云调用的直接方法

* 模拟设备重新启动

* 通过报告的属性，设备孪生查询可标识设备及设备上次重新启动的时间

1. 在命令提示符下，运行以下命令以安装**azure iot-设备**包：

    ```cmd/sh
    pip install azure-iot-device
    ```

2. 使用文本编辑器，在工作目录中创建一个名为**dmpatterns_getstarted_device**的文件。

3. 在 `import`dmpatterns_getstarted_device.py**文件开头添加以下** 语句。

    ```python
    import threading
    import time
    import datetime
    from azure.iot.device import IoTHubDeviceClient, MethodResponse
    ```

4. 添加**CONNECTION_STRING**变量。 将 `{deviceConnectionString}` 占位符值替换为设备连接字符串。 你之前在[IoT 中心内注册了](#register-a-new-device-in-the-iot-hub)此连接字符串。  

    ```python
    CONNECTION_STRING = "{deviceConnectionString}"
    ```

5. 添加以下函数回调，实现设备上的直接方法。

    ```python
    def reboot_listener(client):
        while True:
            # Receive the direct method request
            method_request = client.receive_method_request("rebootDevice")  # blocking call

            # Act on the method by rebooting the device...
            print( "Rebooting device" )
            time.sleep(20)
            print( "Device rebooted")

            # ...and patching the reported properties
            current_time = str(datetime.datetime.now())
            reported_props = {"rebootTime": current_time}
            client.patch_twin_reported_properties(reported_props)
            print( "Device twins updated with latest rebootTime")

            # Send a method response indicating the method request was resolved
            resp_status = 200
            resp_payload = {"Response": "This is the response from the device"}
            method_response = MethodResponse(method_request.request_id, resp_status, resp_payload)
            client.send_method_response(method_response)
    ```

6. 启动直接方法侦听器并等待。

    ```python
    def iothub_client_init():
        client = IoTHubDeviceClient.create_from_connection_string(CONNECTION_STRING)
        return client

    def iothub_client_sample_run():
        try:
            client = iothub_client_init()

            # Start a thread listening for "rebootDevice" direct method invocations
            reboot_listener_thread = threading.Thread(target=reboot_listener, args=(client,))
            reboot_listener_thread.daemon = True
            reboot_listener_thread.start()

            while True:
                time.sleep(1000)

        except KeyboardInterrupt:
            print ( "IoTHubDeviceClient sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python sample..." )
        print ( "IoTHubDeviceClient waiting for commands, press Ctrl-C to exit" )

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

1. 在命令提示符下，运行以下命令以安装**azure iot 中心**包：

    ```cmd/sh
    pip install azure-iot-hub
    ```

2. 使用文本编辑器，在工作目录中创建一个名为**dmpatterns_getstarted_service**的文件。

3. 在 `import`dmpatterns_getstarted_service.py**文件开头添加以下** 语句。

    ```python
    import sys, time

    from azure.iot.hub import IoTHubRegistryManager
    from azure.iot.hub.models import CloudToDeviceMethod, CloudToDeviceMethodResult, Twin
    ```

4. 添加以下变量声明。 将 `{IoTHubConnectionString}` 占位符值替换为之前在[获取 iot 中心连接字符串](#get-the-iot-hub-connection-string)中复制的 iot 中心连接字符串。 将 `{deviceId}` 占位符值替换为在[IoT 中心注册新设备](#register-a-new-device-in-the-iot-hub)中注册的设备 ID。

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
            # Create IoTHubRegistryManager
            registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

            print ( "" )
            print ( "Invoking device to reboot..." )

            # Call the direct method.
            deviceMethod = CloudToDeviceMethod(method_name=METHOD_NAME, payload=METHOD_PAYLOAD)
            response = registry_manager.invoke_device_method(DEVICE_ID, deviceMethod)

            print ( "" )
            print ( "Successfully invoked the device to reboot." )

            print ( "" )
            print ( response.payload )

            while True:
                print ( "" )
                print ( "IoTHubClient waiting for commands, press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= WAIT_COUNT:
                    twin_info = registry_manager.get_twin(DEVICE_ID)

                    if twin_info.properties.reported.get("rebootTime") != None :
                        print ("Last reboot time: " + twin_info.properties.reported.get("rebootTime"))
                    else:
                        print ("Waiting for device to report last reboot time...")

                    time.sleep(5)
                    status_counter += 1

        except Exception as ex:
            print ( "" )
            print ( "Unexpected error {0}".format(ex) )
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

   下面显示了对重新启动直接方法的设备响应：

   ![模拟设备应用输出](./media/iot-hub-python-python-device-management-get-started/device.png)

   下面显示了调用 reboot 直接方法并轮询设备克隆状态的服务：

   ![触发重新启动服务输出](./media/iot-hub-python-python-device-management-get-started/service.png)

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]