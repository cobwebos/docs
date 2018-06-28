---
title: Azure IoT 中心设备孪生入门 (Python) | Microsoft Docs
description: 如何使用 Azure IoT 中心设备孪生添加标记，并使用 IoT 中心查询。 使用用于 Python 的 Azure IoT SDK 来实现一个模拟设备应用，并实现可添加标记和运行 IoT 中心查询的一个服务应用。
author: kgremban
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 12/04/2017
ms.author: kgremban
ms.openlocfilehash: 08e457febaa7522cac86e63c0c187d1e8e49daff
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2018
ms.locfileid: "34634967"
---
# <a name="get-started-with-device-twins-python"></a>设备孪生入门 (Python)
[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

在本教程结束时，会获得两个 Python 控制台应用：

* **AddTagsAndQuery.py**，这是一个 Python 后端应用，用于添加标记并查询设备孪生。
* **ReportConnectivity.py**，这是一个 .Python 应用，用于模拟使用早先创建的设备标识连接到 IoT 中心的设备，并报告其连接状况。

> [!NOTE]
> [Azure IoT SDK][lnk-hub-sdks] 文章介绍了可用于构建设备和后端应用的 Azure IoT SDK。
> 
> 

要完成本教程，需要以下各项：

* [Python 2.x 或 3.x][lnk-python-download]。 请确保根据安装程序的要求，使用 32 位或 64 位安装。 在安装过程中出现提示时，请确保将 Python 添加到特定于平台的环境变量中。 如果使用 Python 2.x，则可能需要[安装或升级 pip - Python 包管理系统][lnk-install-pip]。
* 如果使用 Windows OS，则请安装 [Visual C++ 可再发行组件包][lnk-visual-c-redist]，以便使用 Python 中的本机 DLL。
* 有效的 Azure 帐户。 （如果没有帐户，只需花费几分钟就能创建一个[免费帐户][lnk-free-trial]。）

> [!NOTE]
> 适用于 `azure-iothub-service-client` 和 `azure-iothub-device-client` 的 pip 包目前仅供 Windows OS 使用。 对于 Linux/Mac OS，请参阅[准备适用于 Python 的开发环境][lnk-python-devbox]一文中特定于 Linux 和 Mac OS 的部分。
> 

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

## <a name="create-the-service-app"></a>创建服务应用
在本部分中，将创建一个 Python 控制台应用，该应用将位置元数据添加到与 **{Device Id}** 关联的设备孪生。 然后，该应用将选择位于 Redmond 的设备来查询存储在 IoT 中心的设备孪生，然后查询报告移动电话网络连接的设备孪生。

1. 打开命令提示符，并安装**用于 Python 的 Azure IoT 中心服务 SDK**。 在安装 SDK 之后关闭命令提示符。

    ```
    pip install azure-iothub-service-client
    ```

1. 使用文本编辑器，新建一个 **AddTagsAndQuery.py** 文件。

3. 添加以下代码，从服务 SDK 导入所需模块：

    ```python
    import sys
    import iothub_service_client
    from iothub_service_client import IoTHubRegistryManager, IoTHubRegistryManagerAuthMethod
    from iothub_service_client import IoTHubDeviceTwin, IoTHubError
    ```
2. 添加以下代码，将 `[IoTHub Connection String]` 和 `[Device Id]` 的占位符替换为在前面的部分中创建的 IoT 中心的连接字符串和设备 ID。
   
    ```python
    CONNECTION_STRING = "[IoTHub Connection String]"
    DEVICE_ID = "[Device Id]"

    UPDATE_JSON = "{\"properties\":{\"desired\":{\"location\":\"Redmond\"}}}"

    UPDATE_JSON_SEARCH = "\"location\":\"Redmond\""
    UPDATE_JSON_CLIENT_SEARCH = "\"connectivity\":\"cellular\""
    ```

1. 将以下代码添加到 **AddTagsAndQuery.py** 文件：
   
     ```python
    def iothub_service_sample_run():
        try:
            iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)
        
            iothub_registry_statistics = iothub_registry_manager.get_statistics()
            print ( "Total device count                       : {0}".format(iothub_registry_statistics.totalDeviceCount) )
            print ( "Enabled device count                     : {0}".format(iothub_registry_statistics.enabledDeviceCount) )
            print ( "Disabled device count                    : {0}".format(iothub_registry_statistics.disabledDeviceCount) )
            print ( "" )

            number_of_devices = iothub_registry_statistics.totalDeviceCount
            dev_list = iothub_registry_manager.get_device_list(number_of_devices)
        
            iothub_twin_method = IoTHubDeviceTwin(CONNECTION_STRING)

            for device in range(0, number_of_devices):
                if dev_list[device].deviceId == DEVICE_ID:
                    twin_info = iothub_twin_method.update_twin(dev_list[device].deviceId, UPDATE_JSON)
        
            print ( "Devices in Redmond: " )        
            for device in range(0, number_of_devices):
                twin_info = iothub_twin_method.get_twin(dev_list[device].deviceId)
         
                if twin_info.find(UPDATE_JSON_SEARCH) > -1:
                    print ( dev_list[device].deviceId )
        
            print ( "" )
        
            print ( "Devices in Redmond using cellular network: " )
            for device in range(0, number_of_devices):
                twin_info = iothub_twin_method.get_twin(dev_list[device].deviceId)
                
                if twin_info.find(UPDATE_JSON_SEARCH) > -1:
                    if twin_info.find(UPDATE_JSON_CLIENT_SEARCH) > -1:
                        print ( dev_list[device].deviceId )

        except IoTHubError as iothub_error:
            print ( "Unexpected error {0}".format(iothub_error) )
            return
        except KeyboardInterrupt:
            print ( "IoTHub sample stopped" )
    ```
   
    **Registry** 对象公开从该服务与设备孪生交互所需的所有方法。 此代码将首先初始化 **Registry** 对象，然后更新 **deviceId** 的设备孪生，最后运行两个查询。 第一个查询仅选择位于 **Redmond43** 工厂的设备的设备孪生，第二个查询将查询细化为仅选择还要通过移动电话网络连接的设备。
   
1. 在 **AddTagsAndQuery.py** 的末尾添加以下代码来实现 **iothub_service_sample_run** 函数：
   
    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Device Twins Python service sample..." )

        iothub_service_sample_run()
    ```

1. 使用以下方法运行应用程序：
   
    ```cmd/sh
    python AddTagsAndQuery.py
    ```
   
    在查询位于 **Redmond43** 的所有设备的查询结果中，应该会看到一个设备，而在将结果限制为使用蜂窝网络的设备的查询结果中没有任何设备。
   
    ![第一个查询][1]

在下一部分中，创建的设备应用将报告连接信息，并更改上一部分中查询的结果。

## <a name="create-the-device-app"></a>创建设备应用
在本部分中，将创建一个 Python 控制台应用，该应用以你的 **{Device Id}** 身份连接到中心，然后更新其设备孪生的报告属性，来说明它是使用手机网络进行连接的。

1. 打开命令提示符，并安装**用于 Python 的 Azure IoT 中心服务 SDK**。 在安装 SDK 之后关闭命令提示符。

    ```
    pip install azure-iothub-device-client
    ```

1. 使用文本编辑器，新建一个 **ReportConnectivity.py** 文件。

3. 添加以下代码，从服务 SDK 导入所需模块：

    ```python
    import time
    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult, IoTHubError
    ```

2. 添加以下代码，将 `[IoTHub Device Connection String]` 的占位符替换为在前面的部分中创建的 IoT 中心设备的连接字符串。
   
    ```python
    CONNECTION_STRING = "[IoTHub Device Connection String]"

    # choose HTTP, AMQP, AMQP_WS or MQTT as transport protocol
    PROTOCOL = IoTHubTransportProvider.MQTT

    TIMER_COUNT = 5
    TWIN_CONTEXT = 0
    SEND_REPORTED_STATE_CONTEXT = 0
    ```

1. 将以下代码添加到 **ReportConnectivity.py** 文件以实现设备孪生功能：

    ```python
    def device_twin_callback(update_state, payload, user_context):
        print ( "" )
        print ( "Twin callback called with:" )
        print ( "    updateStatus: %s" % update_state )
        print ( "    payload: %s" % payload )

    def send_reported_state_callback(status_code, user_context):
        print ( "" )
        print ( "Confirmation for reported state called with:" )
        print ( "    status_code: %d" % status_code )

    def iothub_client_init():
        client = IoTHubClient(CONNECTION_STRING, PROTOCOL)

        if client.protocol == IoTHubTransportProvider.MQTT or client.protocol == IoTHubTransportProvider.MQTT_WS:
            client.set_device_twin_callback(
                device_twin_callback, TWIN_CONTEXT)

        return client

    def iothub_client_sample_run():
        try:
            client = iothub_client_init()

            if client.protocol == IoTHubTransportProvider.MQTT:
                print ( "Sending data as reported property..." )

                reported_state = "{\"connectivity\":\"cellular\"}"

                client.send_reported_state(reported_state, len(reported_state), send_reported_state_callback, SEND_REPORTED_STATE_CONTEXT)

            while True:
                print ( "Press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= TIMER_COUNT:
                    status = client.get_send_status()
                    time.sleep(10)
                    status_counter += 1 
        except IoTHubError as iothub_error:
            print ( "Unexpected error %s from IoTHub" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubClient sample stopped" )
    ```   

    **Client** 对象公开从该设备与设备孪生交互所需的所有方法。 上面的代码首先初始化 **Client** 对象，然后检索你的设备的设备孪生，并使用连接信息更新其报告属性。

1. 在 **ReportConnectivity.py** 的末尾添加以下代码来实现 **iothub_client_sample_run** 函数：
   
    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Device Twins Python client sample..." )

        iothub_client_sample_run()
    ```

1. 运行设备应用
   
    ```cmd/sh
    python ReportConnectivity.py
    ```
   
    应当会看到关于设备孪生已更新的确认。

    ![更新孪生][2]

6. 既然设备报告其连接的信息，该信息应显示在两个查询中。 回过头来再次运行查询：
   
    ```cmd/sh
    python AddTagsAndQuery.py
    ```
   
    这一次，两个查询结果中应当都会显示你的 **{Device Id}**。
   
    ![第二个查询][3]

## <a name="next-steps"></a>后续步骤
在本教程中，你已在 Azure 门户中配置了新的 IoT 中心，并在 IoT 中心的标识注册表中创建了设备标识。 已从后端应用以标记形式添加了设备元数据，并编写了模拟的设备应用，用于报告设备孪生中的设备连接信息。 你还学习了如何使用注册表查询此信息。

使用下列资源了解如何执行以下操作：

* 通过 [IoT 中心入门][lnk-iothub-getstarted]教程学习如何从设备发送遥测数据，
* 通过[使用所需属性配置设备][lnk-twin-how-to-configure]教程学习如何使用设备孪生的所需属性配置设备，
* 通过[使用直接方法][lnk-methods-tutorial]教程学习如何以交互方式控制设备（例如从用户控制的应用打开风扇）。

<!-- images -->
[1]: media/iot-hub-python-twin-getstarted/1.png
[2]: media/iot-hub-python-twin-getstarted/2.png
[3]: media/iot-hub-python-twin-getstarted/3.png

<!-- links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[lnk-python-download]: https://www.python.org/downloads/
[lnk-visual-c-redist]: http://www.microsoft.com/download/confirmation.aspx?id=48145
[lnk-install-pip]: https://pip.pypa.io/en/stable/installing/
[lnk-python-devbox]: https://github.com/Azure/azure-iot-sdk-python/blob/master/doc/python-devbox-setup.md

[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-identity]: iot-hub-devguide-identity-registry.md

[lnk-iothub-getstarted]: iot-hub-python-getstarted.md
[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-iot-edge]: iot-hub-linux-iot-edge-get-started.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/

[lnk-twin-how-to-configure]: iot-hub-node-node-twin-how-to-configure.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md

[lnk-methods-tutorial]: iot-hub-node-node-direct-methods.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
