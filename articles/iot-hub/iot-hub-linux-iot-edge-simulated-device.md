---
title: "使用 Azure IoT Edge 模拟设备 (Linux) | Microsoft Docs"
description: "如何在 Linux 上使用 Azure IoT Edge 创建模拟设备，从而通过 IoT Edge 网关将遥测数据发送到 IoT 中心。"
services: iot-hub
documentationcenter: 
author: chipalost
manager: timlt
editor: 
ms.assetid: 11e7bf28-ee3d-48d6-a386-eb506c7a31cf
ms.service: iot-hub
ms.devlang: cpp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/09/2017
ms.author: andbuc
ms.translationtype: Human Translation
ms.sourcegitcommit: ff2fb126905d2a68c5888514262212010e108a3d
ms.openlocfilehash: 5349960373ae6815862c5f79a69dd6d5d9d624ab
ms.contentlocale: zh-cn
ms.lasthandoff: 06/17/2017


---

# <a name="use-azure-iot-edge-to-send-device-to-cloud-messages-with-a-simulated-device-linux"></a>使用 Azure IoT Edge，通过模拟设备发送设备到云的消息 (Linux)

[!INCLUDE [iot-hub-iot-edge-simulated-selector](../../includes/iot-hub-iot-edge-simulated-selector.md)]

[!INCLUDE [iot-hub-iot-edge-install-build-linux](../../includes/iot-hub-iot-edge-install-build-linux.md)]

## <a name="how-to-run-the-sample"></a>如何运行示例

**build.sh** 脚本在 **iot-edge** 存储库的本地副本内的 **build** 文件夹中生成输出。 该输出包括此示例中使用的四个 IoT Edge 模块。

生成脚本位置：

* liblogger.so 放在 build/modules/logger 文件夹中。
* libiothub.so 放在 build/modules/iothub 文件夹中。
* lib\_identity\_map.so 放在 build/modules/identitymap 文件夹中。
* libsimulated\_device.so 放在 build/modules/simulated\_device 文件夹中。

如以下 JSON 设置文件中所示，将这些路径用于 module path 值：

模拟的 \_device\_cloud\_upload\_ 示例过程使用 JSON 配置文件的路径作为命令行参数。 以下示例 JSON 文件位于 SDK 存储库的以下路径：samples\\simulated\_device\_cloud\_upload\_sample\\src\\simulated\_device\_cloud\_upload\_sample\_lin.json。 除非修改了生成脚本，将 IoT Edge 模块或示例可执行文件放置在非默认位置，否则，此配置文件可按原样工作。

> [!NOTE]
> 模块路径相对于从中运行 simulated\_device\_cloud\_upload\_sample 可执行文件的目录，而不是可执行文件所在的目录。  示例 JSON 配置文件默认为在当前工作目录中写入 "deviceCloudUploadGatewaylog.log"。

在文本编辑器中，打开 iot-edge 存储库本地副本中的文件 samples/simulated\_device\_cloud\_upload\_sample/src/simulated\_device\_cloud\_upload\_lin.json。 此文件配置示例网关中的 IoT Edge 模块：

* **IoTHub** 模块连接到 IoT 中心。 将该模块配置为将数据发送到 IoT 中心。 具体而言，将 **IoTHubName** 值设置为 IoT 中心的名称，将 **IoTHubSuffix** 值设置为 **azure-devices.net**。 将“传输”值设置为 "HTTP"、"AMQP" 或 "MQTT" 其中的一个。 目前只有 "HTTP" 会针对所有设备消息共享一个 TCP 连接。 如果将值设置为 "AMQP" 或 "MQTT"，则网关将为每个设备维护与 IoT 中心的单独 TCP 连接。
* **mapping** 模块将模拟设备的 MAC 地址映射到 IoT 中心设备 ID。 确保 **deviceId** 值与添加到 IoT 中心的两台设备的 ID 一致，确保 **deviceKey** 值包含两台设备的密钥。
* **BLE1** 和 **BLE2** 模块是模拟设备。 注意 MAC 地址如何与“映射”模块中的地址匹配。
* **Logger** 模块将网关活动记录到一个文件中。
* 示例中所示的 module path 值假定从 iot-edge 存储库本地副本的 "build" 文件夹运行示例。
* JSON 文件底部的 **links** 数组将 **BLE1** 和 **BLE2** 模块连接到 **mapping** 模块，并将 **mapping** 模块连接到 **IoTHub** 模块。 它还确保 **Logger** 模块记录所有消息。

```json
{
    "modules": [
        {
            "name": "IotHub",
          "loader": {
            "name": "native",
            "entrypoint": {
              "module.path": "./modules/iothub/libiothub.so"
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
              "module.path": "./modules/identitymap/libidentity_map.so"
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
              "module.path": "./modules/simulated_device/libsimulated_device.so"
            }
            },
            "args": {
              "macAddress": "01:01:01:01:01:01"
            }
          },
        {
            "name": "BLE2",
          "loader": {
            "name": "native",
            "entrypoint": {
              "module.path": "./modules/simulated_device/libsimulated_device.so"
            }
            },
            "args": {
              "macAddress": "02:02:02:02:02:02"
            }
          },
        {
            "name": "Logger",
          "loader": {
            "name": "native",
            "entrypoint": {
              "module.path": "./modules/logger/liblogger.so"
            }
            },
            "args": {
              "filename": "deviceCloudUploadGatewaylog.log"
            }
          }
    ],
    "links": [
        {
            "source": "*",
            "sink": "Logger"
        },
        {
            "source": "BLE1",
            "sink": "mapping"
        },
        {
            "source": "BLE2",
            "sink": "mapping"
        },
        {
            "source": "mapping",
            "sink": "IotHub"
        }
    ]
}
```

保存对配置文件所做的更改。

运行示例：

1. 在 shell 中，浏览到 **iot-edge/build** 文件夹。
2. 运行以下命令：
   
    ```sh
    ./samples/simulated_device_cloud_upload/simulated_device_cloud_upload_sample ../samples/simulated_device_cloud_upload/src/simulated_device_cloud_upload_lin.json
    ```
3. 可使用[设备资源管理器][lnk-device-explorer]或 [iothub-explorer][lnk-iothub-explorer] 工具监视 IoT 中心从网关接收的消息。 例如，利用 iothub-explorer 使用通过命令监视设备到云的消息：

    ```sh
    iothub-explorer monitor-events --login "HostName={Your iot hub name}.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey={Your IoT Hub key}"
    ```

## <a name="next-steps"></a>后续步骤

若要深入了解 Azure IoT Edge 并尝试一些代码示例，请访问以下开发人员教程和资源：

* [使用 Azure IoT Edge 从物理设备发送设备到云的消息][lnk-physical-device]
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

