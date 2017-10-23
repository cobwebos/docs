---
title: "使用 Azure IoT Edge 模拟设备 (Windows) | Microsoft Docs"
description: "如何在 Windows 上使用 Azure IoT Edge 创建模拟设备，从而通过 Azure IoT Edge 网关将遥测数据发送到 IoT 中心。"
services: iot-hub
documentationcenter: 
author: chipalost
manager: timlt
editor: 
ms.assetid: 6a2aeda0-696a-4732-90e1-595d2e2fadc6
ms.service: iot-hub
ms.devlang: cpp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/19/2017
ms.author: andbuc
ms.openlocfilehash: 0aa1836ee1445894022b95fefc2338ef53698240
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-iot-edge-to-send-device-to-cloud-messages-with-a-simulated-device-windows"></a>使用 Azure IoT Edge，通过模拟设备发送设备到云的消息 (Windows)

[!INCLUDE [iot-hub-iot-edge-simulated-selector](../../includes/iot-hub-iot-edge-simulated-selector.md)]

[!INCLUDE [iot-hub-iot-edge-install-build-windows](../../includes/iot-hub-iot-edge-install-build-windows.md)]

## <a name="run-the-sample"></a>运行示例

build.cmd 脚本在 iot-edge 存储库的本地副本内的 "build" 文件夹中生成输出。 该输出包括此示例中使用的四个 IoT Edge 模块。

生成脚本会创建以下文件：

* logger.dll  放在 **build\\modules\\logger\\Debug** 文件夹中。
* iothub.dll 放在 **build\\modules\\iothub\\Debug** 文件夹中。
* identity\_map.dll 放在 **build\\modules\\identitymap\\Debug** 文件夹中。
* simulated\_device.dll 放在 **build\\modules\\simulated\_device\\Debug** 文件夹中。

将这些路径用于 **module path** 值，如 simulated\_device\_cloud\_upload\_win JSON 设置文件中所示。

simulated\_device\_cloud\_upload 示例过程使用 JSON 配置文件的路径作为命令行参数。 以下示例 JSON 文件位于 SDK 存储库的以下路径：**samples\\simulated\_device\_cloud\_upload\_sample\\src\\simulated\_device\_cloud\_upload\_win.json**。 除非修改了生成脚本，将 IoT Edge 模块或示例可执行文件放置在非默认位置，否则，此配置文件可按原样工作。

> [!NOTE]
> 模块路径相对于 simulated\_device\_cloud\_upload\_sample.exe 所在的目录。 示例 JSON 配置文件默认为在当前工作目录中写入 "deviceCloudUploadGatewaylog.log"。

在文本编辑器中，打开 **iot-edge** 存储库本地副本中的文件 **samples\\simulated\_device\_cloud\_upload\\src\\simulated\_device\_cloud\_upload\_win.json**。 此文件配置示例网关中的 IoT Edge 模块：

* **IoTHub** 模块连接到 IoT 中心。 将该模块配置为将数据发送到 IoT 中心。 具体而言，将 **IoTHubName** 值设置为 IoT 中心的名称，将 **IoTHubSuffix** 值设置为 **azure-devices.net**。 将“传输”值设置为 "HTTP"、"AMQP" 或 "MQTT" 其中的一个。 目前只有 "HTTP" 会针对所有设备消息共享一个 TCP 连接。 如果将值设置为 "AMQP" 或 "MQTT"，则网关将为每个设备维护与 IoT 中心的单独 TCP 连接。
* **mapping** 模块将模拟设备的 MAC 地址映射到 IoT 中心设备 ID。 将 **deviceId** 值设置为添加到 IoT 中心的两个设备的 ID。 将 **deviceKey** 值设置为两个设备的密钥。
* **BLE1** 和 **BLE2** 模块是模拟设备。 注意模块 MAC 地址如何与“映射”模块中的地址匹配。
* **Logger** 模块将网关活动记录到一个文件中。
* 下列示例所示的 module path 值相对于 simulated\_device\_cloud\_upload\_sample.exe 所在的目录。
* JSON 文件底部的 **links** 数组将 **BLE1** 和 **BLE2** 模块连接到 **mapping** 模块，并将 **mapping** 模块连接到 **IoTHub** 模块。 它还确保 **Logger** 模块记录所有消息。

```json
{
    "modules" :
    [
      {
        "name": "IotHub",
        "loader": {
          "name": "native",
          "entrypoint": {
            "module.path": "..\\..\\..\\modules\\iothub\\Debug\\iothub.dll"
          }
          },
          "args": {
            "IoTHubName": "<<insert here IoTHubName>>",
            "IoTHubSuffix": "<<insert here IoTHubSuffix>>",
            "Transport": "HTTP"
          }
        },
      {
        "name": "mapping",
        "loader": {
          "name": "native",
          "entrypoint": {
            "module.path": "..\\..\\..\\modules\\identitymap\\Debug\\identity_map.dll"
          }
          },
          "args": [
            {
              "macAddress": "01:01:01:01:01:01",
              "deviceId": "<<insert here deviceId>>",
              "deviceKey": "<<insert here deviceKey>>"
            },
            {
              "macAddress": "02:02:02:02:02:02",
              "deviceId": "<<insert here deviceId>>",
              "deviceKey": "<<insert here deviceKey>>"
            }
          ]
        },
      {
        "name": "BLE1",
        "loader": {
          "name": "native",
          "entrypoint": {
            "module.path": "..\\..\\..\\modules\\simulated_device\\Debug\\simulated_device.dll"
          }
          },
          "args": {
            "macAddress": "01:01:01:01:01:01",
            "messagePeriod" : 2000
          }
        },
      {
        "name": "BLE2",
        "loader": {
          "name": "native",
          "entrypoint": {
            "module.path": "..\\..\\..\\modules\\simulated_device\\Debug\\simulated_device.dll"
          }
          },
          "args": {
            "macAddress": "02:02:02:02:02:02",
            "messagePeriod" : 2000
          }
        },
      {
        "name": "Logger",
        "loader": {
          "name": "native",
          "entrypoint": {
            "module.path": "..\\..\\..\\modules\\logger\\Debug\\logger.dll"
          }
        },
        "args": {
          "filename": "deviceCloudUploadGatewaylog.log"
        }
      }
    ],
    "links" : [
        { "source" : "*", "sink" : "Logger" },
        { "source" : "BLE1", "sink" : "mapping" },
        { "source" : "BLE2", "sink" : "mapping" },
        { "source" : "mapping", "sink" : "IotHub" }
    ]
}
```

保存对配置文件所做的更改。

运行示例：

1. 在命令提示符下，导航到 iot-edge 存储库本地副本的 "build" 文件夹。
2. 运行以下命令：
   
    ```cmd
    samples\simulated_device_cloud_upload\Debug\simulated_device_cloud_upload_sample.exe ..\samples\simulated_device_cloud_upload\src\simulated_device_cloud_upload_win.json
    ```
3. 可使用[设备资源管理器][lnk-device-explorer]或 [iothub-explorer][lnk-iothub-explorer] 工具监视 IoT 中心从网关接收的消息。 例如，利用 iothub-explorer 使用通过命令监视设备到云的消息：

    ```cmd
    iothub-explorer monitor-events --login "HostName={Your iot hub name}.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey={Your IoT Hub key}"
    ```

## <a name="next-steps"></a>后续步骤

若要深入了解 IoT Edge 并尝试一些代码示例，请访问以下开发人员教程和资源：

* [使用 IoT Edge 从物理设备发送设备到云的消息][lnk-physical-device]
* [Azure IoT Edge][lnk-iot-edge]

若要进一步探索 IoT 中心的功能，请参阅：

* [IoT 中心开发人员指南][lnk-devguide]
* [从根本上保护 IoT 解决方案][lnk-securing]

<!-- Links -->
[lnk-iot-edge]: https://github.com/Azure/iot-edge/
[lnk-physical-device]: iot-hub-iot-edge-physical-device.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-securing]: iot-hub-security-ground-up.md
[lnk-device-explorer]: https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer
[lnk-iothub-explorer]: https://github.com/Azure/iothub-explorer/blob/master/readme.md
