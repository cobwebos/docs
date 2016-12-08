> [!div class="op_single_selector"]
> * [Node.js](../articles/iot-hub/iot-hub-node-node-twin-getstarted.md)
> * [C#](../articles/iot-hub/iot-hub-csharp-node-twin-getstarted.md)
> 
> 

## <a name="introduction"></a>介绍
设备克隆是存储设备状态信息（元数据、配置和条件）的 JSON 文档。 IoT 中心为连接到 IoT 中心的每台设备保留一个设备克隆。

使用设备克隆可以：

* 存储来自后端的设备元数据。
* 通过设备应用报告当前状态信息，例如可用功能和条件（例如，使用的连接方法）。
* 同步设备应用和后端之间的长时间运行工作流的状态（例如固件和配置更新）。
* 查询设备的元数据、配置或状态。

> [!NOTE]
> 设备克隆旨在执行同步以及查询设备的配置和条件。 [了解设备克隆][lnk-twins]中提供了有关何时使用设备克隆的详细信息。
> 
> 

设备克隆存储在 IoT 中心内，其中包含：

* *标记*，仅可由后端访问的设备元数据；
* *所需属性*，可以由后端修改以及由设备应用观察的 JSON 对象；以及
* *报告属性*，可由设备应用修改以及由后端读取的 JSON 对象。 标记和属性不能包含数组，但可以嵌套对象。

![][img-twin]

此外，应用后端可以根据上述所有数据查询设备克隆。
有关设备克隆的详细信息，请参阅[了解设备克隆][lnk-twins]，有关查询的详细信息，请参阅 [IoT 中心查询语言][lnk-query]。

> [!NOTE]
> 此时，只能从使用 MQTT 协议连接到 IoT 中心的设备访问设备克隆。 有关如何转换现有设备应用以使用 MQTT 的说明，请参阅 [MQTT 支持][lnk-devguide-mqtt]一文。
> 
> 

本教程演示如何：

* 创建将*标记*添加到设备克隆的后端应用，以及将其连接通道作为设备克隆上的*报告属性*进行报告的模拟设备应用。
* 使用标记上的筛选器和之前创建的属性通过后端应用查询设备。

<!-- images -->
[img-twin]: media/iot-hub-selector-twin-get-started/twin.png

<!-- links -->
[lnk-query]: ../articles/iot-hub/iot-hub-devguide-query-language.md
[lnk-twins]: ../articles/iot-hub/iot-hub-devguide-device-twins.md
[lnk-d2c]: ../articles/iot-hub/iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-methods]: ../articles/iot-hub/iot-hub-devguide-direct-methods.md
[lnk-devguide-mqtt]: ../articles/iot-hub/iot-hub-mqtt-support.md

<!--HONumber=Nov16_HO5-->


