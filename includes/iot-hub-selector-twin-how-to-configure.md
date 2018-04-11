> [!div class="op_single_selector"]
> * [Node.js](../articles/iot-hub/iot-hub-node-node-twin-how-to-configure.md)
> * [C#/Node.js](../articles/iot-hub/iot-hub-csharp-node-twin-how-to-configure.md)
> * [C#](../articles/iot-hub/iot-hub-csharp-csharp-twin-how-to-configure.md)
> * [Java](../articles/iot-hub/iot-hub-java-java-twin-how-to-configure.md)
> * [Python](../articles/iot-hub/iot-hub-python-python-twin-how-to-configure.md)
> 
> 

## <a name="introduction"></a>介绍

[IoT 中心设备孪生入门][lnk-twin-tutorial]介绍了如何使用标记来设置设备元数据， 并说明了如何使用报告的属性通过设备应用接收设备条件，然后使用类似 SQL 的语言查询此信息。

本教程介绍如何使用设备孪生的所需属性和报告的属性来远程配置设备应用。 可以使用设备孪生中报告的属性和所需属性通过多个步骤来配置设备应用程序，并可在所有设备中查看此操作的状态。 有关在配置设备时所需角色的详细信息，请参阅[使用 IoT 中心进行设备管理概述][lnk-dm-overview]。

[!INCLUDE [iot-hub-basic](iot-hub-basic-whole.md)]

简而言之，使用设备孪生将允许解决方案后端为托管的设备指定所需配置，而不需要发送特定的命令。 设备负责设置更新其配置状态的最佳方式（对于特定设备条件影响即时执行特定命令功能的 IoT 方案而言十分重要），同时持续报告更新过程的当前状态和潜在的错误条件。 此模式可用来管理大量设备，因为它让解决方案后端能够跨所有设备完全掌握配置流程状态。

> [!TIP]
> 在以更具交互性的方式控制设备的方案中（例如，通过用户控制的应用打开风扇），请考虑使用[直接方法][lnk-methods]。

在本教程中，解决方案后端更改目标设备的遥测配置，这样一来，设备应用就可以应用配置更新。 例如，某项配置更新可能要求软件模块重启，这在本教程中以简单的延迟进行模拟。

解决方案后端采用以下方式将配置存储在设备孪生的所需属性中：

        {
            ...
            "properties": {
                ...
                "desired": {
                    "telemetryConfig": {
                        "configId": "{id of the configuration}",
                        "sendFrequency": "{config}"
                    }
                }
                ...
            }
            ...
        }

由于配置可能是复杂对象，因此会为它们分配唯一 ID（哈希值或 [GUID][lnk-guid]）。


设备应用以镜像报告属性中的所需属性 **telemetryConfig** 的形式报告其当前配置：

        {
            "properties": {
                ...
                "reported": {
                    "telemetryConfig": {
                        "configId": "{id of the current configuration}",
                        "sendFrequency": "{current configuration}",
                        "status": "Success",
                    }
                }
                ...
            }
        }

请注意，报告的 **telemetryConfig** 具有 **status** 这个其他属性，该属性用于报告配置更新过程的状态。

收到新的所需配置时，设备应用通过更改以下状态报告挂起的配置：

        {
            "properties": {
                ...
                "reported": {
                    "telemetryConfig": {
                        "configId": "{id of the current configuration}",
                        "sendFrequency": "{current configuration}",
                        "status": "Pending",
                        "pendingConfig": {
                            "configId": "{id of the pending configuration}",
                            "sendFrequency": "{pending configuration}"
                        }
                    }
                }
                ...
            }
        }

然后，在稍后的某个时间，设备应用会通过更新属性报告此操作是成功还是失败。 解决方案后端可以跨所有设备随时查询配置流程的状态。

本教程演示如何：

* 创建一个模拟设备应用，用于接收来自解决方案后端的配置更新，以及将多个更新作为配置更新过程的*报告属性*进行报告。
* 创建一个后端应用，用于更新设备的所需配置，并查询配置更新流程。

<!-- links -->

[lnk-methods]: ../articles/iot-hub/iot-hub-devguide-direct-methods.md
[lnk-dm-overview]: ../articles/iot-hub/iot-hub-device-management-overview.md
[lnk-twin-tutorial]: ../articles/iot-hub/iot-hub-node-node-twin-getstarted.md
[lnk-guid]: https://en.wikipedia.org/wiki/Globally_unique_identifier
