
# <a name="azure-and-internet-of-things"></a>Azure 和物联网

欢迎使用 Microsoft Azure 与物联网 (IoT)。 本文介绍可以使用 Azure 服务部署的 IoT 解决方案的一般特征。 IoT 解决方案需要大量设备与解决方案后端之间存在安全的双向通信。 解决方案后端可能会使用自动化的预测分析，从设备到云的事件流中挖掘有用的见解。

[Azure IoT 中心][lnk-iot-hub]是所有使用 Azure 服务的 IoT 解决方案的重要构建基块。 IoT 中心是一项完全托管的服务，可在数百万个 IoT 设备和一个解决方案后端之间实现安全可靠的双向通信。 

[Azure IoT 套件][lnk-iot-suite]可针对特定 IoT 方案提供此体系结构的完整端到端实现。 例如：

* *远程监视*解决方案让用户能够监视设备的状态，例如自动贩卖机。
* 预见性维护解决方案可帮助预测设备的维护需求（例如远端泵站的水泵），以及避免意外停机。
* “连接工厂”解决方案用于连接并监视工业设备。

## <a name="iot-solution-architecture"></a>IoT 解决方案体系结构

下图显示了典型的 IoT 解决方案体系结构。 该关系图未包含任何特定 Azure 服务的名称，但介绍了常规 IoT 解决方案体系结构中的重要元素。 在此体系结构中，IoT 设备收集其发送到云网关的数据。 云网关使其他后端服务能够处理数据。 解决方案后端通过仪表板或报表将数据传递给业务线应用程序或人工操作员。

![IoT 解决方案体系结构][img-solution-architecture]

> [!NOTE]
> 有关 IoT 体系结构的深入介绍，请参阅 [Microsoft Azure IoT 参考体系结构][lnk-refarch]。

### <a name="device-connectivity"></a>设备连接

在此 IoT 解决方案中，设备将遥测数据（例如泵站的传感器读数）发送到云终结点以进行存储和处理。 在预测性维护方案中，解决方案后端可以使用传感器数据流来判断特定的泵何时需要维护。 设备还可以通过读取来自云终结点的消息，接收和响应云到设备的消息。 例如，在预见性维护方案中，解决方案后端可将消息发送到抽水站中的其他水泵，以便在维护应开始之前先重新路由流量。 此过程将确保维护工程师可立即开始解决问题。

IoT 项目面临的最大挑战之一是如何可靠且安全地将设备连接到解决方案后端。 相比于其他客户端（例如浏览器和 Mobile Apps），IoT 设备有不同的特征。 IoT 设备：

* 通常是无人操作的嵌入式系统。
* 可以部署到物理访问昂贵的远程位置。
* 可能只能通过解决方案后端来访问。 无法通过其他方式来与设备交互。
* 能力和处理资源可能都有限。
* 网络连接可能不稳定、缓慢或昂贵。
* 可能需要使用专属、自定义或行业特定的应用程序协议。
* 可以使用大量常见的硬件和软件平台来创建。

除了上述需求之外，所有 IoT 解决方案还必须提供可扩展性、安全性和可靠性。 使用传统技术（例如 Web 容器和消息传送代理）时，所产生的一系列连接需求不仅难以实现，而且实现起来非常耗时。 使用 Azure IoT 中心和 Azure IoT 设备 SDK 可以更轻松地实现符合这些要求的解决方案。

设备可以直接与云网关终结点通信。 如果设备无法使用云网关支持的任何通信协议，则可以通过中间网关进行连接。 例如，[Azure IoT 协议网关][lnk-protocol-gateway]可以在设备不能使用 IoT 中心支持的任何协议时执行协议转换。

### <a name="data-processing-and-analytics"></a>数据处理和分析

在云中，IoT 解决方案后端是处理大部分数据的位置。 IoT 解决方案后端：

* 接收大规模来自设备的遥测数据，并确定如何处理和存储该数据。 
* 可能允许从云向特定设备发送命令。
* 提供设备注册功能，使用此功能可以预配设备，并控制哪些设备可以连接到基础结构。
* 用户可以跟踪设备状态并监视设备活动。

在预见性维护方案中，解决方案后端存储历史遥测数据。 解决方案后端可以使用此数据来识别可指示特定水泵已达到维护时间的模式。

IoT 解决方案可以包含自动反馈循环。 例如，解决方案后端中的分析模块可从遥测数据中识别出特定设备的温度超出正常工作级别。 然后，解决方案可以将命令发送到该设备，指示它采取纠正措施。

### <a name="presentation-and-business-connectivity"></a>呈现和业务连接

呈现和业务连接层可让最终用户与 IoT 解决方案及设备交互。 它可让用户查看和分析从其设备收集的数据。 这些视图可以采用仪表板或报表的格式，以显示历史数据和/或接近实时的数据。 例如，操作员可检查特定泵站的状态，并查看系统引发的任何警报。 此层还可集成 IoT 解决方案与现有业务线应用程序，以链接企业业务流程或工作流。 例如，预测性维护解决方案可集成计划系统。 在解决方案识别出需要维护的泵时，计划系统会预约工程师到泵站检查。

![IoT 解决方案仪表板][img-dashboard]

[img-solution-architecture]: ./media/iot-azure-and-iot/iot-reference-architecture.png
[img-dashboard]: ./media/iot-azure-and-iot/iot-suite.png

[lnk-iot-hub]: ../articles/iot-hub/iot-hub-what-is-iot-hub.md
[lnk-iot-suite]: ../articles/iot-suite/iot-suite-overview.md
[lnk-machinelearning]: http://azure.microsoft.com/documentation/services/machine-learning/
[Azure IoT Suite]: http://azure.microsoft.com/solutions/iot
[lnk-protocol-gateway]:  ../articles/iot-hub/iot-hub-protocol-gateway.md
[lnk-refarch]: http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf
