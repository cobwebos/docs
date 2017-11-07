> [!div class="op_single_selector"]
> * [Windows 上的 C](../articles/iot-suite/iot-suite-connecting-devices.md)
> * [Linux 上的 C](../articles/iot-suite/iot-suite-connecting-devices-linux.md)
> * [Node.js（通用）](../articles/iot-suite/iot-suite-connecting-devices-node.md)
> * [Raspberry Pi 上的 Node.js](../articles/iot-suite/iot-suite-connecting-pi-node.md)
> * [Raspberry Pi 上的 C](../articles/iot-suite/iot-suite-connecting-pi-c.md)

本教程实施一个可将以下遥测数据发送到远程监视[预配置解决方案](../articles/iot-suite/iot-suite-what-are-preconfigured-solutions.md)的**冷却器**设备：

* 温度
* 压力
* 湿度

为简单起见，代码会生成**冷却器**的示例遥测值。 可以通过将真实的传感器连接到设备并发送真实的遥测数据，在本示例的基础上融会贯通。

示例设备还会：

* 将元数据发送到解决方案，以描述设备的功能。
* 针对通过解决方案中的“设备”页触发的操作做出响应。
* 针对通过解决方案中的“设备”页发送的配置更改做出响应。

要完成此教程，需要一个有效的 Azure 帐户。 如果没有帐户，只需花费几分钟就能创建一个免费试用帐户。 有关详细信息，请参阅 [Azure 免费试用](http://azure.microsoft.com/pricing/free-trial/)。

## <a name="before-you-start"></a>开始之前

在为设备编写任何代码之前，必须先预配远程监视预配置解决方案，并在该解决方案中预配新的自定义设备。

### <a name="provision-your-remote-monitoring-preconfigured-solution"></a>预配远程监视预配置解决方案

本教程中创建的**冷却器**设备会将数据发送到[远程监视](../articles/iot-suite/iot-suite-remote-monitoring-explore.md)预配置解决方案的实例中。 如果尚未在 Azure 帐户中预配远程监视预配置解决方案，请参阅[部署远程监视预配置解决方案](../articles/iot-suite/iot-suite-remote-monitoring-deploy.md)

远程监视解决方案的预配过程完成之后，请单击“启动”，在浏览器中打开解决方案仪表板。

![解决方案仪表板](media/iot-suite-selector-connecting/dashboard.png)

### <a name="provision-your-device-in-the-remote-monitoring-solution"></a>在远程监视方案中预配设备

> [!NOTE]
> 如果已在解决方案中预配了设备，则可以跳过此步骤。 创建客户端应用程序时需要设备凭据。

对于连接到预配置解决方案的设备，该设备必须使用有效的凭据将自身标识到 IoT 中心。 可以在解决方案的“设备”页中检索设备凭据。 在本教程中，稍后会在客户端应用程序中添加设备凭据。

若要在远程监视解决方案中添加设备，请在解决方案中的“设备”页上完成以下步骤：

1. 选择“预配”，并选择“物理”作为**设备类型**：

    ![预配物理设备](media/iot-suite-selector-connecting/devicesprovision.png)

1. 输入 **Physical-chiller** 作为设备 ID。 选择“对称密钥”和“自动生成密钥”选项：

    ![选择设备选项](media/iot-suite-selector-connecting/devicesoptions.png)

若要找到设备在连接到预配置解决方案时必须使用的凭据，请在浏览器中导航到 Azure 门户。 登录到订阅。

1. 找到包含远程监视解决方案所用 Azure 服务的资源组。 该资源组与预配的远程监视解决方案同名。

1. 在此资源组中导航到 IoT 中心。 然后选择“设备资源管理器”：

    ![设备资源管理器](media/iot-suite-selector-connecting/deviceexplorer.png)

1. 选择在远程监视解决方案中的“设备”页上创建的**设备 ID**。

1. 记下“设备 ID”和“设备密钥”值。 添加用于将设备连接到解决方案的代码时，将要使用这些值。

现已在远程监视预配置解决方案中预配了一个物理设备。 在以下部分中，我们将会实现使用设备凭据连接到解决方案的客户端应用程序。

客户端应用程序实现内置的**冷却器**设备模型。 预配置解决方案设备模型指定有关设备的以下信息：

* 设备报告给解决方案的属性。 例如，**冷却器**设备报告有关其固件和位置的信息。
* 由设备发送到解决方案的遥测数据类型。 例如，**冷却器**设备发送温度、湿度和压力值。
* 可以在解决方案中计划的、要在设备上运行的方法。 例如，**冷却器**设备必须实现 **Reboot**、**FirmwareUpdate**、**EmergencyValveRelease** 和 **IncreasePressuree** 方法。