> [!div class="op_single_selector"]
> * [Linux](../articles/iot-hub/iot-hub-linux-iot-edge-get-started.md)
> * [Windows](../articles/iot-hub/iot-hub-windows-iot-edge-get-started.md)
> 
> 

本文详细演练 [Hello World 示例代码][lnk-helloworld-sample]，演示 [Azure IoT Edge][lnk-iot-edge] 体系结构的基本组件。 该示例使用 Azure IoT Edge 生成一个简单的网关，每隔 5 秒将“hello world”消息记录到文件中。

本文介绍的内容包括：

* Hello World 示例体系结构：说明如何将 [Azure IoT Edge 体系结构概念][lnk-edge-concepts]应用到 Hello World 示例，以及如何将这些组件组合到一起。
* **如何生成示例**：生成示例所需的步骤。
* **如何运行示例**：运行示例所需的步骤。 
* **典型输出**：运行示例时预期获得的输出的示例。
* **代码段**：代码段的集合，显示 Hello World 示例如何实现重要的 IoT Edge 网关组件。


## <a name="hello-world-sample-architecture"></a>Hello World 示例体系结构
Hello World 示例体现了上一部分所述概念。 Hello World 示例所实现的 IoT Edge 网关具有一个管道，该管道包含两个 IoT Edge 模块：

* *hello world* 模块每 5 秒创建一条消息，并将该消息传递给记录器模块。
* *记录器* 模块将接收的消息写入文件。

![使用 Azure IoT Edge 构建的 Hello World 示例的体系结构][4]

如前一部分所述，Hello World 模块并不是每隔 5 秒就直接将消息传递给记录器模块， 而是每隔 5 秒将消息发布到中转站。

Logger 模块从中转站接收消息并对消息执行操作，将消息内容写入文件。

Logger 模块只使用来自中转站的消息，而不会将新消息发布到中转站。

![中转站如何在 Azure IoT Edge 中的模块之间路由消息][5]

上图显示了 Hello World 示例的体系结构，同时显示了在[存储库][lnk-iot-edge]中对示例不同部分进行实现的源文件的相对路径。 请自行浏览代码，或作为指导可使用本文中的代码段。

<!-- Images -->
[4]: media/iot-hub-iot-edge-getstarted-selector/high_level_architecture.png
[5]: media/iot-hub-iot-edge-getstarted-selector/detailed_architecture.png

<!-- Links -->
[lnk-helloworld-sample]: https://github.com/Azure/iot-edge/tree/master/samples/hello_world
[lnk-iot-edge]: https://github.com/Azure/iot-edge
[lnk-edge-concepts]: ../articles/iot-hub/iot-hub-iot-edge-overview.md