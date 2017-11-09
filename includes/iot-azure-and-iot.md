
# <a name="azure-and-the-internet-of-things"></a>Azure 和物联网

欢迎使用 Microsoft Azure 与物联网 (IoT)。 本文介绍云中 IoT 解决方案的常见特征。 IoT 解决方案需要设备（可能数以百万计）与解决方案后端之间有安全的双向通信。 例如，解决方案可能会使用自动化的预测分析，从设备到云的事件流中挖掘有用的见解。

## <a name="iot-solution-architecture"></a>IoT 解决方案体系结构

下图显示了典型 IoT 解决方案体系结构的关键元素。 此图忽略了具体的实现细节，例如所用 Azure 服务以及设备操作系统。 在此体系结构中，IoT 设备收集其发送到云网关的数据。 云网关使其他后端服务能够处理数据。 这些后端服务可以将数据传送到：

* 其他业务线应用程序。
* 通过仪表板或其他呈现设备进行操作的操作人员。

![IoT 解决方案体系结构][img-solution-architecture]

> [!NOTE]
> 有关 IoT 体系结构的深入介绍，请参阅 [Microsoft Azure IoT 参考体系结构][lnk-refarch]。

### <a name="device-connectivity"></a>设备连接

在 IoT 解决方案体系结构中，设备通常将遥测数据发送到云进行存储和处理。 例如，在预测性维护方案中，解决方案后端可以使用传感器数据流来判断特定的泵何时需要维护。 设备还可以通过读取来自云终结点的消息，接收和响应云到设备的消息。 在同一示例中，解决方案后端可将消息发送到泵站中的其他泵，以便在维护应开始之前先重新路由流量。 此过程可确保维护工程师到场后即可开始工作。

在 IoT 解决方案中，最大的挑战通常是以安全可靠的方式连接设备。 这是因为，相比于其他客户端（例如浏览器和移动应用），IoT 设备有不同的特征。 具体而言，IoT 设备具有以下特征：

* 通常是无人操作的嵌入式系统（与电话不同）。
* 可以部署到物理访问昂贵的远程位置。
* 可能只能通过解决方案后端来访问。 无法通过其他方式来与设备交互。
* 能力和处理资源可能都有限。
* 网络连接可能不稳定、缓慢或昂贵。
* 可能需要使用专属、自定义或行业特定的应用程序协议。
* 可以使用大量常见的硬件和软件平台来创建。

除了前述约束，任何 IoT 解决方案还必须是可缩放的、安全的和可靠的。

设备可以与云直接通信，也可以通过中介性网关与云通信，具体取决于通信协议和网络可用性。 IoT 体系结构通常混合使用这两种通信模式。

### <a name="data-processing-and-analytics"></a>数据处理和分析

在现代 IoT 解决方案中，数据处理可在云中或设备端进行。 设备端处理称为“边缘计算”。 数据处理位置的选择取决于多项因素，例如：

* 网络约束。 如果设备和云之间的带宽有限，则需进行更多的边缘处理。
* 响应时间。 如果需要在设备上进行近实时的操作，则最好是在设备中处理响应。 例如，紧急情况下需停止机器臂。
* 法定环境。 某些数据不能发送到云。

通常情况下，在边缘和云中进行的数据处理都是以下功能的组合：

* 接收大规模来自设备的遥测数据，并确定如何处理和存储该数据。
* 通过分析遥测数据为用户提供见解，不管是实时的还是事后的。
* 从云或网关设备向特定设备发送命令。

另外，IoT 云后端应提供：

* 设备注册功能，用于：
    * 预配设备。
    * 控制哪些设备有权连接到基础结构。
* 设备管理，目的是控制设备状态并监视设备活动。

例如，在预测性维护方案中，云后端存储历史遥测数据。 解决方案根据此数据确定特定泵上可能存在的异常行为，防止其引发真正的问题。 它可以使用数据分析，确定预防性解决方案会将命令发送回设备，以便执行纠正操作。 此过程在设备和云之间产生一个自动反馈循环，大大提高了解决方案效率。

### <a name="presentation-and-business-connectivity"></a>呈现和业务连接

呈现和业务连接层可让最终用户与 IoT 解决方案及设备交互。 它可让用户查看和分析从其设备收集的数据。 这些视图可以采用仪表板或 BI 报表的格式，以显示历史数据和/或接近实时的数据。 例如，操作员可检查特定抽水站的状态，并查看系统引发的任何警报。 还可以通过此层来集成 IoT 解决方案与现有业务线应用程序，将其绑定到企业业务流程或工作流中。 例如，预测性维护解决方案可集成计划系统，在解决方案识别出需要维护的泵时预约工程师到泵站进行检查。

[img-solution-architecture]: ./media/iot-azure-and-iot/iot-reference-architecture.png
[img-dashboard]: ./media/iot-azure-and-iot/iot-suite.png

[lnk-iot-hub]: ../articles/iot-hub/iot-hub-what-is-iot-hub.md
[lnk-iot-suite]: ../articles/iot-suite/iot-suite-overview.md
[lnk-machinelearning]: http://azure.microsoft.com/documentation/services/machine-learning/
[Azure IoT Suite]: http://azure.microsoft.com/solutions/iot
[lnk-protocol-gateway]:  ../articles/iot-hub/iot-hub-protocol-gateway.md
[lnk-refarch]: http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf
