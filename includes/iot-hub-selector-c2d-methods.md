> [!div class="op_single_selector"]
> * [Node.js](../articles/iot-hub/iot-hub-node-node-direct-methods.md)
> * [C#/Node.js](../articles/iot-hub/iot-hub-csharp-node-direct-methods.md)
> * [C#](../articles/iot-hub/iot-hub-csharp-csharp-direct-methods.md)
> * [Java](../articles/iot-hub/iot-hub-java-java-direct-methods.md)

Azure IoT 中心是一项完全托管的服务，可在数百万个设备和一个解决方案后端之间实现安全可靠的双向通信。 以前的教程（[IoT 中心入门] 和 [使用 IoT 中心发送云到设备的消息] ）介绍了 IoT 中心的设备到云和云到设备的基本消息传递功能。 用户还可以通过 IoT 中心从云调用设备上的非持久方法。 直接方法表示与设备进行的请求-答复式交互，类似于 HTTP 调用，因为它们不管是成功还是失败，速度都非常快（在用户指定的超时过后），会让用户知道调用的状态。 [调用设备上的直接方法][lnk-devguide-methods]更详细地介绍了各种直接方法，指导用户何时使用直接方法而不是云到设备消息或所需属性。

本教程演示如何：

* 使用 Azure 门户创建 IoT 中心，以及如何在 IoT 中心创建设备标识。
* 创建一个模拟设备应用，以便通过云调用其中的直接方法。
* 创建一个控制台应用，以便通过 IoT 中心调用模拟设备应用中的直接方法。

> [!NOTE]
> 目前只能在通过 MQTT 协议连接到 IoT 中心的设备上使用直接方法。 有关如何转换现有设备应用以使用 MQTT 的说明，请参阅 [MQTT 支持][lnk-devguide-mqtt]一文。


[lnk-devguide-methods]: ../articles/iot-hub/iot-hub-devguide-direct-methods.md
[lnk-devguide-mqtt]: ../articles/iot-hub/iot-hub-mqtt-support.md

[使用 IoT 中心发送云到设备的消息]: ../articles/iot-hub/iot-hub-csharp-csharp-c2d.md
[IoT 中心入门]: ../articles/iot-hub/iot-hub-node-node-getstarted.md