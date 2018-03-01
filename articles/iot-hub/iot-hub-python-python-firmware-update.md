---
title: "通过 Azure IoT 中心进行设备固件更新 (Python) | Microsoft Docs"
description: "如何使用 Azure IoT 中心上的设备管理进行设备固件更新。 使用用于 Python 的 Azure IoT SDK 实现模拟设备应用以及触发固件更新的服务应用。"
services: iot-hub
documentationcenter: .net
author: msebolt
manager: timlt
editor: 
ms.assetid: 70b84258-bc9f-43b1-b7cf-de1bb715f2cf
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/16/2018
ms.author: v-masebo
ms.openlocfilehash: e7ca0d95da3fd36e6dad081bad0aa5a178436159
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2018
---
# <a name="use-device-management-to-initiate-a-device-firmware-update-pythonpython"></a>使用设备管理启动设备固件更新 (Python/Python)
[!INCLUDE [iot-hub-selector-firmware-update](../../includes/iot-hub-selector-firmware-update.md)]

在[设备管理入门][lnk-dm-getstarted]教程中，已了解了如何使用[设备孪生][lnk-devtwin]和[直接方法][lnk-c2dmethod]基元来远程重新启动设备。 本教程使用相同的 IoT 中心基元，提供指南，并演示如何进行端到端模拟固件更新。  此模式在用于 Intel Edison 设备示例的固件更新实现中使用。

本教程演示如何：

* 创建一个 Python 控制台应用，其通过 IoT 中心在模拟设备应用上调用 firmwareUpdate 直接方法。
* 创建实现 **firmwareUpdate** 直接方法的模拟设备应用。 此方法会启动等待下载固件映像、下载固件映像以及最后应用固件映像的多阶段过程。 在更新的每个阶段中，设备使用报告属性来报告进度。

在本教程结束时，会获得两个 Python 控制台应用：

**dmpatterns_fwupdate_service.py**，它调用模拟设备应用中的直接方法、显示响应并定期（每隔 500 毫秒）显示更新的报告属性。

**dmpatterns_fwupdate_device.py**，它使用早前创建的设备标识连接到 IoT 中心、接收 firmwareUpdate 直接方法、运行一个多状态过程以模拟固件更新，包括：等待映像下载、下载新映像以及最后应用映像。

要完成本教程，需要以下各项：

* [Python 2.x 或 3.x][lnk-python-download]。 请确保根据安装程序的要求，使用 32 位或 64 位安装。 在安装过程中出现提示时，请确保将 Python 添加到特定于平台的环境变量中。 如果使用 Python 2.x，则可能需要[安装或升级 pip - Python 包管理系统][lnk-install-pip]。
* 如果使用 Windows OS，则请安装 [Visual C++ 可再发行组件包][lnk-visual-c-redist]，以便使用 Python 中的本机 DLL。
* 有效的 Azure 帐户。 （如果没有帐户，只需花费几分钟就能创建一个[免费帐户][lnk-free-trial]。）

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

## <a name="trigger-a-remote-firmware-update-on-the-device-using-a-direct-method"></a>使用直接方法在设备上触发远程固件更新
本部分创建一个 Python 控制台应用，用于在设备上启动远程固件更新。 该应用使用直接方法来启动更新，并使用设备孪生查询定期获取活动固件更新的状态。

1. 在命令提示符处，运行以下命令以安装 **azure-iothub-service-client**：
   
    ```cmd/sh
    pip install azure-iothub-service-client
    ```

1. 使用文本编辑器，在工作目录中创建 **dmpatterns_getstarted_service.py** 文件。

1. 在 **dmpatterns_getstarted_service.py** 文件开头添加以下 import 语句和变量。 将 `IoTHubConnectionString` 和 `deviceId` 替换为你在前面记下的值：
   
    ```python
    import sys
    import time

    import iothub_service_client
    from iothub_service_client import IoTHubDeviceTwin, IoTHubDeviceMethod, IoTHubError

    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"

    METHOD_NAME = "firmwareUpdate"
    METHOD_PAYLOAD = "{\"fwPackageUri\":\"test.com\"}"
    TIMEOUT = 60
    MESSAGE_COUNT = 5
    ```

1. 添加以下函数来调用直接方法并显示 firmwareUpdate 报告属性的值。 另外，添加 `main` 例程：
   
    ```python
    def iothub_firmware_sample_run():
        try:
            iothub_twin_method = IoTHubDeviceTwin(CONNECTION_STRING)

            print ( "" )
            print ( "Direct Method called." )
            iothub_device_method = IoTHubDeviceMethod(CONNECTION_STRING)
        
            response = iothub_device_method.invoke(DEVICE_ID, METHOD_NAME, METHOD_PAYLOAD, TIMEOUT)
            print ( response.payload )
        
            print ( "" )
            print ( "Device Twin queried, press Ctrl-C to exit" )
            while True:
                twin_info = iothub_twin_method.get_twin(DEVICE_ID)

                if "\"firmwareStatus\":\"standBy\"" in twin_info:
                    print ( "Waiting on device..." )
                elif "\"firmwareStatus\":\"downloading\"" in twin_info:
                    print ( "Downloading firmware..." )
                elif "\"firmwareStatus\":\"applying\"" in twin_info:
                    print ( "Download complete, applying firmware..." )
                elif "\"firmwareStatus\":\"completed\"" in twin_info:
                    print ( "Firmware applied" )
                    print ( "" )
                    print ( "Get reported properties from device twin:" )
                    print ( twin_info )
                    break
                else:
                    print ( "Unknown status" )

                status_counter = 0
                while status_counter <= MESSAGE_COUNT:
                    time.sleep(1)
                    status_counter += 1

        except IoTHubError as iothub_error:
            print ( "" )
            print ( "Unexpected error {0}" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "" )
            print ( "IoTHubService sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub firmware update Python sample..." )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_firmware_sample_run()
    ```

1. 保存并关闭 **dmpatterns_fwupdate_service.py** 文件。


## <a name="create-a-simulated-device-app"></a>创建模拟设备应用程序
本部分的操作：

* 创建一个 Python 控制台应用，用于响应通过云调用的直接方法
* 触发模拟固件更新
* 使用报告属性使设备孪生查询能够识别设备及其上次完成固件更新的时间

1. 在命令提示符处，运行以下命令来安装 **azure-iothub-device-client** 包：
   
    ```cmd/sh
    pip install azure-iothub-device-client
    ```

1. 使用文本编辑器，创建 **dmpatterns_fwupdate_device.py** 文件。

1. 在 **dmpatterns_fwupdate_device.py** 文件开头添加以下 import 语句和变量。 将 `deviceConnectionString` 替换为你的 IoT 中心的设备连接字符串：
   
    ```python
    import time, datetime
    import sys
    import threading

    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult
    from iothub_client import IoTHubError, DeviceMethodReturnValue

    SEND_REPORTED_STATE_CONTEXT = 0
    METHOD_CONTEXT = 0

    MESSAGE_COUNT = 10

    PROTOCOL = IoTHubTransportProvider.MQTT
    CONNECTION_STRING = "{deviceConnectionString}"
    CLIENT = IoTHubClient(CONNECTION_STRING, PROTOCOL)
    ```

1. 添加以下函数，用于提供报告属性更新和实现直接方法：
   
    ```python
    def send_reported_state_callback(status_code, user_context):
        print ( "Reported state updated." )

    def device_method_callback(method_name, payload, user_context):
        if method_name == "firmwareUpdate":
            print ( "Starting firmware update." )
            image_url = payload
            thr = threading.Thread(target=simulate_download_image, args=([payload]), kwargs={})
            thr.start()
    
            device_method_return_value = DeviceMethodReturnValue()
            device_method_return_value.response = "{ \"Response\": \"Firmware update started\" }"
            device_method_return_value.status = 200
            return device_method_return_value
    ```

1. 添加用于模拟固件映像的下载和应用的以下函数：
   
    ```python
    def simulate_download_image(image_url):
        time.sleep(15)
        print ( "Downloading image from: " + image_url )

        reported_state = "{\"firmwareStatus\":\"downloading\", \"downloadComplete\":\"" + str(datetime.datetime.now()) + "\"}"
        CLIENT.send_reported_state(reported_state, len(reported_state), send_reported_state_callback, SEND_REPORTED_STATE_CONTEXT)
        time.sleep(15)
    
        simulate_apply_image(image_url)
    
    def simulate_apply_image(image_url):
        print ( "Applying image from: " + image_url )

        reported_state = "{\"firmwareStatus\":\"applying\", \"startedApplyingImage\":\"" + str(datetime.datetime.now()) + "\"}"
        CLIENT.send_reported_state(reported_state, len(reported_state), send_reported_state_callback, SEND_REPORTED_STATE_CONTEXT)
        time.sleep(15)
    
        simulate_complete_image()

    def simulate_complete_image():
        print ( "Image applied." )

        reported_state = "{\"firmwareStatus\":\"completed\", \"lastFirmwareUpdate\":\"" + str(datetime.datetime.now()) + "\"}"
        CLIENT.send_reported_state(reported_state, len(reported_state), send_reported_state_callback, SEND_REPORTED_STATE_CONTEXT)
    ```

8. 添加以下函数，用于初始化设备孪生的报告属性，并等待直接方法调用。 另外，添加 `main` 例程：
   
    ```python
    def iothub_firmware_sample_run():
        try:
            CLIENT.set_device_method_callback(device_method_callback, METHOD_CONTEXT)

            reported_state = "{\"firmwareStatus\":\"standBy\", \"logTime\":\"" + str(datetime.datetime.now()) + "\"}"
            CLIENT.send_reported_state(reported_state, len(reported_state), send_reported_state_callback, SEND_REPORTED_STATE_CONTEXT)
            print ( "Device twins initialized." )
            print ( "IoTHubClient waiting for commands, press Ctrl-C to exit" )
        
            while True:
                status_counter = 0
                while status_counter <= MESSAGE_COUNT:
                    time.sleep(10)
                    status_counter += 1
            
        except IoTHubError as iothub_error:
            print ( "Unexpected error %s from IoTHub" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubClient sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python firmware update sample..." )
        print ( "    Protocol %s" % PROTOCOL )
        print ( "    Connection string=%s" % CONNECTION_STRING )

        iothub_firmware_sample_run()
    ```

> [!NOTE]
> 为简单起见，本教程不实现任何重试策略。 在生产代码中，应该按 MSDN 文章 [Transient Fault Handling](https://msdn.microsoft.com/library/hh675232.aspx)（暂时性故障处理）中所述实施重试策略（例如指数性的回退）。
> 


## <a name="run-the-apps"></a>运行应用
现在可以运行应用了。

1. 在命令提示符处，运行以下命令以开始侦听重新启动直接方法。
   
    ```cmd/sh
    python dmpatterns_fwupdate_device.py
    ```

1. 在另一个命令提示符处，运行以下命令以触发远程重新启动并查询设备孪生以查找上次重新启动时间。
   
    ```cmd/sh
    python dmpatterns_fwupdate_service.py
    ```

1. 可在控制台查看对直接方法的设备响应。 然后，记下在整个固件更新过程中对报告属性的更改。

    ![程序输出][1]


## <a name="next-steps"></a>后续步骤
在本教程中，已使用直接方法在设备上触发远程固件更新，并使用报告属性跟踪固件更新的进度。

若要了解如何扩展 IoT 解决方案以及在多个设备上计划方法调用，请参阅[计划和广播作业][lnk-tutorial-jobs]教程。

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-dm-getstarted]: iot-hub-node-node-device-management-get-started.md
[lnk-tutorial-jobs]: iot-hub-node-node-schedule-jobs.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-python-download]: https://www.python.org/downloads/
[lnk-visual-c-redist]: http://www.microsoft.com/download/confirmation.aspx?id=48145
[lnk-install-pip]: https://pip.pypa.io/en/stable/installing/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[1]: ./media/iot-hub-python-python-firmware-update/1.png
