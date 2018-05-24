---
title: 使用 Azure IoT 中心设备孪生属性 (Python) | Microsoft Docs
description: 如何使用 Azure IoT 中心设备孪生配置设备。 使用 Azure IoT SDK for Python 实现模拟设备应用，并实现可使用设备孪生更改设备配置的服务应用。
services: iot-hub
documentationcenter: .net
author: kgremban
manager: timlt
editor: ''
ms.assetid: d0bcec50-26e6-40f0-8096-733b2f3071ec
ms.service: iot-hub
ms.devlang: python
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/12/2018
ms.author: kgremban
ms.openlocfilehash: f6e002eb570ade7fc4008cc69e6042bd3dd97f7e
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/17/2018
---
# <a name="use-desired-properties-to-configure-devices-python"></a>使用所需属性配置设备 (Python)
[!INCLUDE [iot-hub-selector-twin-how-to-configure](../../includes/iot-hub-selector-twin-how-to-configure.md)]

在本教程结束时，会获得两个 Python 控制台应用：

* SimulateDeviceConfiguration.py，一个模拟设备应用，它等待所需配置更新并报告模拟配置更新过程的状态。
* SetDesiredConfigurationAndQuery.py，一个后端应用，用于在设备上设置所需配置并查询配置更新过程。

> [!NOTE]
> [Azure IoT SDK][lnk-hub-sdks] 文章介绍了可用于构建设备和后端应用的 Azure IoT SDK。
> 
> 

要完成本教程，需要以下各项：

* [Python 2.x 或 3.x][lnk-python-download]。 请确保根据安装程序的要求，使用 32 位或 64 位安装。 在安装过程中出现提示时，请确保将 Python 添加到特定于平台的环境变量中。 如果使用 Python 2.x，则可能需要[安装或升级 pip - Python 包管理系统][lnk-install-pip]。
* 如果使用 Windows OS，则请安装 [Visual C++ 可再发行组件包][lnk-visual-c-redist]，以便使用 Python 中的本机 DLL。
* 有效的 Azure 帐户。 （如果没有帐户，只需花费几分钟就能创建一个[免费帐户][lnk-free-trial]。）

如果已按照[设备孪生入门][lnk-twin-tutorial]教程执行了操作，则已经有一个 IoT 中心和一个名为 **myDeviceId** 的设备标识；可以跳到[创建模拟设备应用][lnk-how-to-configure-createapp]部分。

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

## <a name="create-the-simulated-device-app"></a>创建模拟设备应用
在此部分中，会创建一个 Python 控制台应用，它作为 myDeviceId 连接到中心，等待所需配置更新，然后对模拟配置更新过程报告更新。

1. 在命令提示符处使用以下命令安装 Azure IoT Python 设备 SDK：
   
    ```cmd/sh
    pip install azure-iothub-device-client
    ```

1. 使用文本编辑器新建一个 SimulateDeviceConfiguration.py 文件。

1. 将以下代码添加到 SimulateDeviceConfiguration.py 文件，并将 {device connection string} 占位符替换为创建 myDeviceId 设备标识时复制的设备连接字符串：

    ```python   
    import time
    import sys

    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult, IoTHubError

    CONNECTION_STRING = "{deviceConnectionString}"
    PROTOCOL = IoTHubTransportProvider.MQTT

    CLIENT = IoTHubClient(CONNECTION_STRING, PROTOCOL)

    WAIT_COUNT = 5

    TWIN_CONTEXT = 0
    SEND_REPORTED_STATE_CONTEXT = 0

    TWIN_CALLBACKS = 0
    SEND_REPORTED_STATE_CALLBACKS = 0

    CONFIG_ID = "1"
    TWIN_PAYLOAD = "{\"configId\":" + CONFIG_ID + ",\"sendFrequency\":\"24h\"}"
    ```

    客户端对象公开从设备与设备孪生进行交互所需的所有方法。 在下一代码中，将更新处理程序附加到所需属性上，然后再添加一个处理程序，通过比较 configId 验证实际存在配置更改请求。configId 随后会调用可开始更改配置的方法。 为简单起见，上一代码对初始配置使用硬编码默认值。 实际的应用可能会从本地存储加载该配置。
   
   > [!IMPORTANT]
   > 始终会在设备连接时立即发出所需属性更改事件，因此请务必先检查所需属性中是否有实际的更改，再执行任何操作。
   > 

1. 将以下代码添加到 SimulateDeviceConfiguration.py 文件的末尾：

    ```python
    def device_twin_callback(update_state, payload, user_context):
        global TWIN_CALLBACKS
        global CONFIG_ID

        desired_configId = payload[payload.find("configId")+11:payload.find("configId")+12]
        if CONFIG_ID != desired_configId:
            print ( "" )
            print ( "Reported pending config change: %s" % payload)
        
            desired_sendFrequency = payload[payload.find("sendFrequency")+17:payload.find("sendFrequency")+19]
            print ( "...desired configId: " + desired_configId)
            print ( "...desired sendFrequency: " + desired_sendFrequency)
            new_payload = "{\"configId\":" + desired_configId + ",\"sendFrequency\":\"" + desired_sendFrequency + "\"}"
            CLIENT.send_reported_state(new_payload, len(new_payload), send_reported_state_callback, SEND_REPORTED_STATE_CONTEXT)
        
        CONFIG_ID = desired_configId
    
    def send_reported_state_callback(status_code, user_context):
        global SEND_REPORTED_STATE_CALLBACKS
    
        print ( "" )
        print ( "Device twins updated." )
    ```
   
    device_twin_callback 方法使用配置更新请求更新本地设备孪生上报告的属性，并设置 _configId_。 成功更新设备孪生之后，显示一则更新消息。 为节省带宽，仅通过指定要修改的属性（在上述代码中名为 TWIN_PAYLOAD）而不是替换整个文档来更新报告属性。
   
   > [!NOTE]
   > 本教程不模拟并发配置更新的任何行为。 某些配置更新进程在更新运行过程中可能能够适应目标配置的更改，某些配置更新进程则可能必须将它们排队，而其他配置更新进程会拒绝它们并显示错误情况。 请确保考虑特定配置过程所需的行为，并在开始配置更改之前添加相应的逻辑。
   > 

1. 将以下代码添加到 SimulateDeviceConfiguration.py 文件的末尾： 

    ```python
    def iothub_client_init():
        if CLIENT.protocol == IoTHubTransportProvider.MQTT or client.protocol == IoTHubTransportProvider.MQTT_WS:
            CLIENT.set_device_twin_callback(device_twin_callback, TWIN_CONTEXT)

    def iothub_client_sample_run():
        try:
            iothub_client_init()
        
            CLIENT.send_reported_state(TWIN_PAYLOAD, len(TWIN_PAYLOAD), send_reported_state_callback, SEND_REPORTED_STATE_CONTEXT)

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

1. 运行设备应用：
   
    ```cmd/sh
    python SimulateDeviceConfiguration.py
    ```
   
    应该看到消息 `Device twins updated.`。 保持运行该应用。


## <a name="create-the-service-app"></a>创建服务应用
在本部分中，将创建一个 Python 控制台应用，该应用通过新的遥测配置对象更新与 myDeviceId 关联的设备孪生的所需属性。 然后，它会查询在 IoT 中心内存储的设备孪生，并显示所需的设备配置与报告的设备配置之间的差异。

1. 在命令提示符处使用以下命令安装 Azure IoT Python 服务 SDK：
   
    ```cmd/sh
    pip install azure-iothub-service-client
    ```

1. 使用文本编辑器新建一个 SetDesiredAndQuery.py 文件。

1. 将以下代码添加到 SetDesiredAndQuery.py 文件，并将 {IoTHubConnectionString} 占位符替换为创建中心时复制的 IoT 中心连接字符串，{deviceId} 占位符替换为设备的名称：

    ```python
    import sys, time
    import iothub_service_client

    from iothub_service_client import IoTHubError, IoTHubDeviceTwin

    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"

    UPDATE_JSON = "{\"properties\":{\"desired\":{\"configId\":3, \"sendFrequency\":\"" + sys.argv[1:][0] + "\"}}}"
    TIMEOUT = 60
    WAIT_COUNT = 10
    ```

1. 在 SetDesiredAndQuery.py 文件末尾添加以下代码：

    ```python
    def iothub_devicetwin_sample_run():
        try:
            iothub_twin_method = IoTHubDeviceTwin(CONNECTION_STRING)
        
            twin_info = iothub_twin_method.get_twin(DEVICE_ID)
            print ( "" )
            print ( "Device Twin before update    :" )
            print ( "{0}".format(twin_info) )

            twin_info = iothub_twin_method.update_twin(DEVICE_ID, UPDATE_JSON)
            print ( "" )
            print ( "Device Twin after update     :" )
            print ( "{0}".format(twin_info) )
        
            while True:
                time.sleep(10)

                twin_info = iothub_twin_method.get_twin(DEVICE_ID)
                print ( "" )
                print ( "Device Twin after client change initiated    :" )
                print ( "{0}".format(twin_info) )
        
                print ( "" )
                print ( "IoT Hub service sample complete, press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= WAIT_COUNT:
                    time.sleep(30)
                
                    status_counter += 1

        except IoTHubError as iothub_error:
            print ( "" )
            print ( "Unexpected error {0}".format(iothub_error) )
            return
        except KeyboardInterrupt:
            print ( "" )
            print ( "IoTHubDeviceMethod sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub Service Client DeviceTwins Python sample..." )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_devicetwin_sample_run()
    ```

1. 在运行 SimulateDeviceConfiguration.py 时，同时在新的命令提示符处使用以下命令运行应用程序：

    ```cmd/sh
    python SetDesiredAndQuery.py 5m
    ```
   
    应会看到报告的配置 ID 从 1 更改为 2，此时新的活动发送频率为 5 分钟而不是 24 小时。


## <a name="next-steps"></a>后续步骤
在本教程中，从后端应用将所需配置设置为所需属性，并编写一个模拟设备应用来检测该更改及模拟更新过程（将其状态作为报告属性报告给设备孪生）。

使用下列资源了解如何执行以下操作：

* 通过 [IoT 中心入门][lnk-iothub-getstarted]教程学习如何从设备发送遥测数据，
* 查看[计划和广播作业][lnk-schedule-jobs]教程，学习如何对大型设备集计划或执行操作。
* 通过[使用直接方法][lnk-methods-tutorial]教程学习如何以交互方式控制设备（例如从用户控制的应用打开风扇）。

<!-- links -->
[lnk-python-download]: https://www.python.org/downloads/
[lnk-visual-c-redist]: http://www.microsoft.com/download/confirmation.aspx?id=48145
[lnk-install-pip]: https://pip.pypa.io/en/stable/installing/

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-twin-notifications]: iot-hub-devguide-device-twins.md#back-end-operations
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-dm-overview]: iot-hub-device-management-overview.md
[lnk-twin-tutorial]: iot-hub-python-twin-getstarted.md
[lnk-schedule-jobs]: iot-hub-node-node-schedule-jobs.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/
[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-iot-edge]: iot-hub-linux-iot-edge-get-started.md
[lnk-iothub-getstarted]: iot-hub-python-getstarted.md
[lnk-methods-tutorial]: iot-hub-python-python-direct-methods.md

[lnk-guid]: https://en.wikipedia.org/wiki/Globally_unique_identifier

[lnk-how-to-configure-createapp]: iot-hub-node-node-twin-how-to-configure.md#create-the-simulated-device-app
