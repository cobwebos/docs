> [!div class="op_single_selector"]
> * [Linux](../articles/iot-hub/iot-hub-linux-iot-edge-simulated-device.md)
> * [Windows](../articles/iot-hub/iot-hub-windows-iot-edge-simulated-device.md)

[模拟设备云上传示例]这一演练介绍如何使用 [Azure IoT Edge][lnk-sdk] 从模拟设备将设备到云遥测发送到 IoT 中心。

本文介绍的内容包括：

* 体系结构：关于[模拟设备云上传示例]的体系结构信息。
* **生成并运行**：生成并运行示例所需的步骤。

## <a name="architecture"></a>体系结构

[模拟设备云上传示例]介绍如何创建将遥测从模拟设备发送到 IoT 中心的网关。 设备可能无法直接连接到 IoT 中心，因为：

* IoT 中心无法理解设备使用的通信协议。
* 设备不够智能，无法记住 IoT 中心分配给它的标识。

IoT Edge 网关可通过以下方式解决这些问题：

* 网关理解设备所使用的协议，因此接收到来自该设备的设备到云遥测后，使用 IoT 中心理解的协议将这些消息转发到 IoT 中心。

* 网关将 IoT 中心标识映射到设备，并在设备向 IoT 中心发送消息时充当代理。

下图显示了示例的主要组件，包括 IoT Edge 模块：

![关系图 - 模拟设备消息通过网关转到 IoT 中心][1]

此示例包含三个模块，它们构成可网关：
1. 协议引入模块
1. MAC &lt;-&gt; IoT 中心 ID 模块
1. IoT 中心通信模块

模块彼此之间不直接传递消息。 模块将消息发布到内部中转站，该中转站通过订阅机制将消息传递给其他模块。 有关详细信息，请参阅 [Azure IoT Edge 入门][lnk-gw-getstarted]。

![关系图 - 网关模块与中转站通信][2]

### <a name="protocol-ingestion-module"></a>协议引入模块

在从设备获取数据，通过网关将数据传递到云中的过程中，将协议引入模块作为起点。 

示例中，此模块：

1. 创建模拟温度数据。 如果使用物理设备，该模块会从这些物理设备读取数据。
1. 创建消息。
1. 将模拟温度数据置于消息内容中。
1. 将 MAC 地址为虚构的属性添加到消息中。
1. 向链中的下一模块提供该消息。

协议引入模块是源代码中的 simulated_device.c。

### <a name="mac-lt-gt-iot-hub-id-module"></a>MAC &lt;-&gt; IoT 中心 ID 模块

MAC &lt;-&gt; IoT中心 ID 模块充当转换器。 此示例使用 MAC 地址作为唯一的设备标识符，将其与 IoT 中心设备标识相关联。 不过，可以编写自己的模块，让其使用其他的唯一标识符。 例如，设备可能具有唯一序列号或者遥测数据可能包括唯一的嵌入式设备名。

示例中，此模块：

1. 扫描具有 MAC 地址属性的消息。
1. 如果存在 MAC 地址，它会向消息添加另一个具有 IoT 中心设备密钥的属性。 
1. 向链中的下一模块提供该消息。

开发人员在 MAC 地址和 IoT 中心标识之间设置映射，将模拟设备与 IoT 中心设备标识相关联。 开发人员手动将映射添加到模块配置中。

MAC &lt;-&gt; IoT 中心 ID 模块是源代码中的 identitymap.c。 

### <a name="iot-hub-communication-module"></a>IoT 中心通信模块

IoT 中心通信模块开放从网关到 IoT 中心的单个 HTTP 连接。 HTTP 是 IoT 中心理解的三大协议之一。 此模块通过在一个连接上多路复用来自所有设备的连接，使你可以不必开放每个设备的连接。 此方法使单个网关能够连接多个设备。 

示例中，此模块：

1. 采用具有 IoT 中心设备密钥属性（由前一模块分配）的消息。 
1. 使用 HTTP 协议将消息内容发送到 IoT 中心。 

IoT 中心通信模块是源代码中的 iothub.c。

## <a name="before-you-get-started"></a>准备工作

开始之前，必须：

* 在 Azure 订阅中[创建一个 IoT 中心][lnk-create-hub]。 在此示例演练中，需要 IoT 中心的名称。 如果没有帐户，只需几分钟即可创建一个[免费帐户][lnk-free-trial]。
* 将两个设备添加到 IoT 中心，并记下其 ID 和设备密钥。 可使用[设备资源管理器][lnk-device-explorer]或 [iothub-explorer][lnk-iothub-explorer] 工具将设备添加到 IoT 中心，并检索其密钥。


<!-- Images -->
[1]: media/iot-hub-iot-edge-simulated-selector/image1.png
[2]: media/iot-hub-iot-edge-simulated-selector/image2.png

<!-- Links -->
[模拟设备云上传示例]: https://github.com/Azure/iot-edge/blob/master/samples/simulated_device_cloud_upload/README.md
[lnk-sdk]: https://github.com/Azure/iot-edge
[lnk-gw-getstarted]: ../articles/iot-hub/iot-hub-linux-iot-edge-get-started.md
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-device-explorer]: https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer
[lnk-iothub-explorer]: https://github.com/Azure/iothub-explorer/blob/master/readme.md
[lnk-create-hub]: ../articles/iot-hub/iot-hub-create-through-portal.md