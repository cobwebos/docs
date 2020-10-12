---
title: Azure IoT 中心设备孪生入门 (Python) | Microsoft Docs
description: 如何使用 Azure IoT 中心设备孪生添加标记，并使用 IoT 中心查询。 使用用于 Python 的 Azure IoT SDK 来实现一个模拟设备应用，并实现可添加标记和运行 IoT 中心查询的一个服务应用。
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: robinsh
ms.custom: mqtt, devx-track-python
ms.openlocfilehash: 12b1d083ae1481f7c8b5fe60cac9156a56aeaa0a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87875466"
---
# <a name="get-started-with-device-twins-python"></a>设备孪生入门 (Python)

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

在本教程结束时，会获得两个 Python 控制台应用：

* **AddTagsAndQuery.py**，这是一个 Python 后端应用，用于添加标记并查询设备孪生。

* **ReportConnectivity.py**，这是一个 .Python 应用，用于模拟使用早先创建的设备标识连接到 IoT 中心的设备，并报告其连接状况。

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>必备条件

[!INCLUDE [iot-hub-include-python-v2-installation-notes](../../includes/iot-hub-include-python-v2-installation-notes.md)]

* 确保已在防火墙中打开端口 8883。 本文中的设备示例使用 MQTT 协议，该协议通过端口 8883 进行通信。 在某些公司和教育网络环境中，此端口可能被阻止。 有关解决此问题的更多信息和方法，请参阅[连接到 IoT 中心(MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)。

## <a name="create-an-iot-hub"></a>创建 IoT 中心

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>在 IoT 中心内注册新设备

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="get-the-iot-hub-connection-string"></a>获取 IoT 中心连接字符串

[!INCLUDE [iot-hub-howto-twin-shared-access-policy-text](../../includes/iot-hub-howto-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-custom-connection-string](../../includes/iot-hub-include-find-custom-connection-string.md)]

## <a name="create-the-service-app"></a>创建服务应用

在本部分中，将创建一个 Python 控制台应用，该应用将位置元数据添加到与 **{Device ID}** 关联的设备孪生。 然后，该应用将选择位于 Redmond 的设备来查询存储在 IoT 中心的设备孪生，然后查询报告移动电话网络连接的设备孪生。

1. 在工作目录中，打开命令提示符并安装安装适用于 Python 的 Azure IoT 中心服务 SDK  。

   ```cmd/sh
   pip install azure-iot-hub
   ```

2. 使用文本编辑器，新建一个 **AddTagsAndQuery.py** 文件。

3. 添加以下代码，从服务 SDK 导入所需模块：

   ```python
   import sys
   from time import sleep
   from azure.iot.hub import IoTHubRegistryManager
   from azure.iot.hub.models import Twin, TwinProperties, QuerySpecification, QueryResult
   ```

4. 添加以下代码。 将 `[IoTHub Connection String]` 替换为在[获取 IoT 中心连接字符串](#get-the-iot-hub-connection-string)中复制的 IoT 中心连接字符串。 将 `[Device Id]` 替换为在[在 IoT 中心注册新设备](#register-a-new-device-in-the-iot-hub)中注册的设备 ID。
  
    ```python
    IOTHUB_CONNECTION_STRING = "[IoTHub Connection String]"
    DEVICE_ID = "[Device Id]"
    ```

5. 将以下代码添加到 **AddTagsAndQuery.py** 文件：

    ```python
    def iothub_service_sample_run():
        try:
            iothub_registry_manager = IoTHubRegistryManager(IOTHUB_CONNECTION_STRING)

            new_tags = {
                    'location' : {
                        'region' : 'US',
                        'plant' : 'Redmond43'
                    }
                }

            twin = iothub_registry_manager.get_twin(DEVICE_ID)
            twin_patch = Twin(tags=new_tags, properties= TwinProperties(desired={'power_level' : 1}))
            twin = iothub_registry_manager.update_twin(DEVICE_ID, twin_patch, twin.etag)

            # Add a delay to account for any latency before executing the query
            sleep(1)

            query_spec = QuerySpecification(query="SELECT * FROM devices WHERE tags.location.plant = 'Redmond43'")
            query_result = iothub_registry_manager.query_iot_hub(query_spec, None, 100)
            print("Devices in Redmond43 plant: {}".format(', '.join([twin.device_id for twin in query_result.items])))

            print()

            query_spec = QuerySpecification(query="SELECT * FROM devices WHERE tags.location.plant = 'Redmond43' AND properties.reported.connectivity = 'cellular'")
            query_result = iothub_registry_manager.query_iot_hub(query_spec, None, 100)
            print("Devices in Redmond43 plant using cellular network: {}".format(', '.join([twin.device_id for twin in query_result.items])))

        except Exception as ex:
            print("Unexpected error {0}".format(ex))
            return
        except KeyboardInterrupt:
            print("IoT Hub Device Twin service sample stopped")
    ```

     IoTHubRegistryManager 对象公开从服务与设备孪生进行交互所需的所有方法。 此代码将首先初始化 IoTHubRegistryManager  对象，然后更新 DEVICE_ID  的设备孪生，最后运行两个查询。 第一个查询仅选择位于 Redmond43  工厂的设备的设备孪生，第二个查询将查询细化为仅选择还通过移动电话网络连接的设备。

6. 在 **AddTagsAndQuery.py** 的末尾添加以下代码来实现 **iothub_service_sample_run** 函数：

    ```python
    if __name__ == '__main__':
        print("Starting the Python IoT Hub Device Twin service sample...")
        print()

        iothub_service_sample_run()
    ```

7. 使用以下方法运行应用程序：

    ```cmd/sh
    python AddTagsAndQuery.py
    ```

    在查询位于 **Redmond43** 的所有设备的查询结果中，应该会看到一个设备，而在将结果限制为使用蜂窝网络的设备的查询结果中没有任何设备。

    ![第一个显示 Redmond 中所有设备的查询](./media/iot-hub-python-twin-getstarted/service-1.png)

在下一部分中，创建的设备应用将报告连接信息，并更改上一部分中查询的结果。

## <a name="create-the-device-app"></a>创建设备应用

在本部分中，将创建一个 Python 控制台应用，用于连接到你的中心作为 **{设备 ID}**，然后更新其设备克隆的报告属性，以包含使用蜂窝网络连接的信息。

1. 在工作目录中的命令提示符下，安装**适用于 Python 的 Azure IoT 中心设备 SDK**：

    ```cmd/sh
    pip install azure-iot-device
    ```

2. 使用文本编辑器，新建一个 **ReportConnectivity.py** 文件。

3. 添加以下代码，从设备 SDK 导入所需模块：

    ```python
    import time
    import threading
    from azure.iot.device import IoTHubModuleClient
    ```

4. 添加以下代码。 将 `[IoTHub Device Connection String]` 占位符值替换为在[在 IoT 中心注册新设备](#register-a-new-device-in-the-iot-hub)中复制的设备连接字符串。

    ```python
    CONNECTION_STRING = "[IoTHub Device Connection String]"
    ```

5. 将以下代码添加到 **ReportConnectivity.py** 文件以实现设备孪生功能：

    ```python
    def twin_update_listener(client):
        while True:
            patch = client.receive_twin_desired_properties_patch()  # blocking call
            print("Twin patch received:")
            print(patch)

    def iothub_client_init():
        client = IoTHubModuleClient.create_from_connection_string(CONNECTION_STRING)
        return client

    def iothub_client_sample_run():
        try:
            client = iothub_client_init()

            twin_update_listener_thread = threading.Thread(target=twin_update_listener, args=(client,))
            twin_update_listener_thread.daemon = True
            twin_update_listener_thread.start()

            # Send reported 
            print ( "Sending data as reported property..." )
            reported_patch = {"connectivity": "cellular"}
            client.patch_twin_reported_properties(reported_patch)
            print ( "Reported properties updated" )

            while True:
                time.sleep(1000000)
        except KeyboardInterrupt:
            print ( "IoT Hub Device Twin device sample stopped" )
    ```

    IoTHubModuleClient  对象公开从该设备与设备孪生交互所需的所有方法。 上面的代码在初始化 IoTHubModuleClient  对象后，检索你的设备的设备孪生，并使用连接信息更新其报告属性。

6. 在 **ReportConnectivity.py** 的末尾添加以下代码来实现 **iothub_client_sample_run** 函数：

    ```python
    if __name__ == '__main__':
        print ( "Starting the Python IoT Hub Device Twin device sample..." )
        print ( "IoTHubModuleClient waiting for commands, press Ctrl-C to exit" )

        iothub_client_sample_run()
    ```

7. 运行设备应用：

    ```cmd/sh
    python ReportConnectivity.py
    ```

    应当会看到设备孪生报告属性已更新的确认。

    ![从设备应用更新报告属性](./media/iot-hub-python-twin-getstarted/device-1.png)

8. 既然设备报告其连接的信息，该信息应显示在两个查询中。 回过头来再次运行查询：

    ```cmd/sh
    python AddTagsAndQuery.py
    ```

    这一次，两个查询结果中应当都会显示你的 **{Device ID}** 。

    ![第二次查询服务应用](./media/iot-hub-python-twin-getstarted/service-2.png)

    在设备应用中，你将看到“已收到服务应用发送的所需属性孪生修补程序”的确认。

    ![在设备应用上接收所需属性](./media/iot-hub-python-twin-getstarted/device-2.png)

## <a name="next-steps"></a>后续步骤

在本教程中，你已在 Azure 门户中配置了新的 IoT 中心，并在 IoT 中心的标识注册表中创建了设备标识。 已从后端应用以标记形式添加了设备元数据，并编写了模拟的设备应用，用于报告设备孪生中的设备连接信息。 你还学习了如何使用注册表查询此信息。

使用下列资源了解如何执行以下操作：

* 通过 [IoT 中心入门](quickstart-send-telemetry-python.md)教程学习如何从设备发送遥测数据。

* 按照[使用所需属性配置设备](tutorial-device-twins.md)教程使用设备孪生的所需属性配置设备。

* 通过[使用直接方法](quickstart-control-device-python.md)教程学习如何以交互方式控制设备（例如从用户控制的应用打开风扇）。
