---
title: Azure IoT 中心直接方法 (Python) | Microsoft Docs
description: 如何使用 Azure IoT 中心直接方法。 使用 Azure IoT SDKs for Python 实现包含直接方法的模拟设备应用和调用直接方法的服务应用。
services: iot-hub
documentationcenter: ''
author: kgremban
manager: timlt
editor: ''
ms.assetid: ea9c73ca-7778-4e38-a8f1-0bee9d142f04
ms.service: iot-hub
ms.devlang: python
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/22/2018
ms.author: v-masebo;kgremban
ms.openlocfilehash: 973dba8682e50af0434c557aa088d15e7a004b45
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/05/2018
---
# <a name="use-direct-methods-on-your-iot-device-with-python"></a>通过 Python 使用 IoT 设备上的直接方法
[!INCLUDE [iot-hub-selector-c2d-methods](../../includes/iot-hub-selector-c2d-methods.md)]

在本教程结束时，会获得两个 Python 控制台应用：

* **CallMethodOnDevice.py**，用于调用模拟设备应用中的方法并显示响应。
* **SimulatedDevice.py**，可使用前面创建的设备标识连接到 IoT 中心，并响应通过云调用的方法。

> [!NOTE]
> [Azure IoT SDK][lnk-hub-sdks] 一文提供了各种 Azure IoT SDK 的相关信息，用户可以使用这些 SDK 构建可在设备和解决方案后端上运行的应用程序。
> 

要完成本教程，需要以下各项：

* [Python 2.x 或 3.x][lnk-python-download]。 请确保根据安装程序的要求，使用 32 位或 64 位安装。 在安装过程中出现提示时，请确保将 Python 添加到特定于平台的环境变量中。 如果使用 Python 2.x，则可能需要[安装或升级 pip - Python 包管理系统][lnk-install-pip]。
* 如果使用 Windows OS，则请安装 [Visual C++ 可再发行组件包][lnk-visual-c-redist]，以便使用 Python 中的本机 DLL。
* 有效的 Azure 帐户。 （如果没有帐户，只需花费几分钟就能创建一个[免费帐户][lnk-free-trial]。）

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity-portal.md)]


## <a name="create-a-simulated-device-app"></a>创建模拟设备应用程序
在本部分中，将创建一个 Python 控制台应用，用于响应通过云调用的方法。

1. 使用文本编辑器，新建一个 **SimulatedDevice.py** 文件。

1. 在 **SimulatedDevice.py** 文件的开头添加以下 `import` 语句和变量：
   
    ```python
    import time
    import sys
    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult, IoTHubError, DeviceMethodReturnValue
    
    WAIT_COUNT = 5
    METHOD_CONTEXT = 0
    METHOD_CALLBACKS = 0

    # chose MQTT or MQTT_WS as transport protocol
    PROTOCOL = IoTHubTransportProvider.MQTT
    CONNECTION_STRING = "<deviceConnectionString>"
    ```

1. 添加以下函数，实现设备方法回调和设备上的直接方法：
   
    ```python
    def device_method_callback(method_name, payload, user_context):
        global METHOD_CALLBACKS
    
        if method_name == "DeviceMethod":
            onDeviceMethod()
        
        print ( "\nMethod callback called with:\nmethodName = %s\npayload = %s\ncontext = %s" % (method_name, payload, user_context) )
        METHOD_CALLBACKS += 1
    
        print ( "Total calls confirmed: %d\n" % METHOD_CALLBACKS )
        device_method_return_value = DeviceMethodReturnValue()
        device_method_return_value.response = "{ \"Response\": \"This is the response from the device\" }"
        device_method_return_value.status = 200
    
        return device_method_return_value

    def onDeviceMethod():
        print ( "Direct method called." )
    ```

1. 添加以下函数，打开与 IoT 中心的连接并初始化方法侦听器：
   
    ```python
    def iothub_client_init():
        # prepare iothub client
        client = IoTHubClient(CONNECTION_STRING, PROTOCOL)

        if client.protocol == IoTHubTransportProvider.MQTT or client.protocol == IoTHubTransportProvider.MQTT_WS:
            client.set_device_method_callback(device_method_callback, METHOD_CONTEXT)

        return client
    ```

1. 添加 main 函数：

    ```python
    def iothub_client_sample_run():
        try:
            client = iothub_client_init()

            while True:
                print ( "IoTHubClient waiting for direct method call, press Ctrl-C to exit" )

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
        print ( "Starting the IoT Hub Python direct methods sample..." )
        print ( "    Protocol %s" % PROTOCOL )
        print ( "    Connection string=%s" % CONNECTION_STRING )

        iothub_client_sample_run()
    ```

1. 保存并关闭 **SimulatedDevice.py** 文件。

> [!NOTE]
> 为简单起见，本教程不实现任何重试策略。 在生产代码中，应按 MSDN 文章 [Transient Fault Handling][lnk-transient-faults]（暂时性故障处理）中所述实施重试策略（例如连接重试）。
> 


## <a name="call-a-method-on-a-device"></a>调用设备上的方法
在本部分中，将创建一个 Python 控制台应用，用于调用模拟设备应用中的方法并显示响应。


1. 使用文本编辑器，新建一个 **CallMethodOnDevice.py** 文件。

1. 在 **CallMethodOnDevice.py** 文件的开头添加以下 `import` 语句和变量：
   
    ```python
    import sys
    import iothub_service_client
    from iothub_service_client import IoTHubDeviceMethod, IoTHubError

    CONNECTION_STRING = "<IoTHubConnectionString>"
    DEVICE_ID = "<deviceId>"

    METHOD_NAME = "DeviceMethod"
    METHOD_PAYLOAD = "{\"method_number\":\"42\"}"
    TIMEOUT = 60
    ```

1. 添加以下函数，实现设备方法回调和设备上的直接方法：
   
    ```python
    def iothub_devicemethod_sample_run():
        try:
            iothub_device_method = IoTHubDeviceMethod(CONNECTION_STRING)

            response = iothub_device_method.invoke(DEVICE_ID, METHOD_NAME, METHOD_PAYLOAD, TIMEOUT)

            print ( "" )
            print ( "Device Method called" )
            print ( "Device Method name       : {0}".format(METHOD_NAME) )
            print ( "Device Method payload    : {0}".format(METHOD_PAYLOAD) )
            print ( "" )
            print ( "Response status          : {0}".format(response.status) )
            print ( "Response payload         : {0}".format(response.payload) )

            try:
                # Try Python 2.xx first
                raw_input("Press Enter to continue...\n")
            except:
                pass
                # Use Python 3.xx in the case of exception
                input("Press Enter to continue...\n")

        except IoTHubError as iothub_error:
            print ( "" )
            print ( "Unexpected error {0}".format(iothub_error) )
            return
        except KeyboardInterrupt:
            print ( "" )
            print ( "IoTHubDeviceMethod sample stopped" )
    ```

1. 添加 main 函数：

    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Service Client DeviceMethod Python sample..." )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_devicemethod_sample_run()
    ```

1. 保存并关闭 **CallMethodOnDevice.py** 文件。

## <a name="run-the-apps"></a>运行应用
现在可以运行应用了。

1. 在命令提示符处运行以下命令，开始侦听从 IoT 中心发出的方法调用：
   
    ```cmd/sh
    python SimulatedDevice.py
    ```
   
    ![][7]

1. 在命令提示符处运行以下命令，开始监视 IoT 中心：
   
    ```cmd/sh
    python CallMethodOnDevice.py
    ```
   
    ![][8]

1. 此时会看到设备通过输出消息对方法进行响应，而调用该方法的应用程序则会显示来自设备的响应：
   
    ![][9]

## <a name="next-steps"></a>后续步骤
在本教程中，你已在 Azure 门户中配置了新的 IoT 中心，并在 IoT 中心的标识注册表中创建了设备标识。 已通过此设备标识启用模拟设备应用的相关功能，使之能够响应通过云调用的方法。 还创建了一个应用，用于调用设备上的方法并显示来自设备的响应。 

若要继续了解 IoT 中心入门知识并浏览其他 IoT 方案，请参阅：

* [IoT 中心入门]
* [在多台设备上安排作业][lnk-devguide-jobs]

若要了解如何扩展 IoT 解决方案以及在多个设备上计划方法调用，请参阅[计划和广播作业][lnk-tutorial-jobs]教程。

<!-- Images. -->
[7]: ./media/iot-hub-python-python-direct-methods/run-simulated-device.png
[8]: ./media/iot-hub-python-python-direct-methods/run-callmethodondevice.png
[9]: ./media/iot-hub-python-python-direct-methods/methods-output.png

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/
[lnk-python-download]: https://www.python.org/downloads/
[lnk-visual-c-redist]: http://www.microsoft.com/download/confirmation.aspx?id=48145
[lnk-install-pip]: https://pip.pypa.io/en/stable/installing/

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-tutorial-jobs]: iot-hub-node-node-schedule-jobs.md
[lnk-devguide-methods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

[Send Cloud-to-Device messages with IoT Hub]: iot-hub-csharp-csharp-c2d.md
[Process Device-to-Cloud messages]: iot-hub-csharp-csharp-process-d2c.md
[IoT 中心入门]: iot-hub-node-node-getstarted.md
