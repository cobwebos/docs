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

在为设备编写任何代码之前，部署远程监视预配置解决方案，并向该解决方案添加一个新的物理设备。

### <a name="deploy-your-remote-monitoring-preconfigured-solution"></a>部署远程监视预配置解决方案

本教程中创建的**冷却器**设备会将数据发送到[远程监视](../articles/iot-suite/iot-suite-remote-monitoring-explore.md)预配置解决方案的实例中。 如果尚未在 Azure 帐户中预配远程监视预配置解决方案，请参阅[部署远程监视预配置解决方案](../articles/iot-suite/iot-suite-remote-monitoring-deploy.md)

当远程监视解决方案的部署过程完成后，单击“启动”，以在浏览器中打开解决方案仪表板。

![解决方案仪表板](media/iot-suite-selector-connecting/dashboard.png)

### <a name="add-your-device-to-the-remote-monitoring-solution"></a>将设备添加到远程监视解决方案

> [!NOTE]
> 如果已在解决方案中添加了设备，则可以跳过此步骤。 不过，下一步骤需要设备连接字符串。 可以从 [Azure 门户](https://portal.azure.com)或使用 [az iot](https://docs.microsoft.com/cli/azure/iot?view=azure-cli-latest) CLI 工具检索设备的连接字符串。

对于连接到预配置解决方案的设备，该设备必须使用有效的凭据将自身标识到 IoT 中心。 将设备添加到解决方案时，有机会保存包含这些凭据的设备连接字符串。 在本教程中，稍后会在客户端应用程序中添加设备连接字符串。

若要在远程监视解决方案中添加设备，请在解决方案中的“设备”页上完成以下步骤：

1. 选择“+ 新建设备”，并选择“物理”作为**设备类型**：

    ![添加物理设备](media/iot-suite-selector-connecting/devicesprovision.png)

1. 输入 **Physical-chiller** 作为设备 ID。 选择“对称密钥”和“自动生成密钥”选项：

    ![选择设备选项](media/iot-suite-selector-connecting/devicesoptions.png)

1. 选择“应用”。 然后记下**设备 ID**、**主密钥**和**连接字符串主密钥**值：

    ![检索凭据](media/iot-suite-selector-connecting/credentials.png)

现在，你已向远程监视预配置解决方案添加了物理设备，并记下了其设备连接字符串。 在以下各部分中，你将实现使用设备连接字符串连接到解决方案的客户端应用程序。

客户端应用程序实现内置的**冷却器**设备模型。 预配置解决方案设备模型指定有关设备的以下信息：

* 设备报告给解决方案的属性。 例如，**冷却器**设备报告有关其固件和位置的信息。
* 由设备发送到解决方案的遥测数据类型。 例如，**冷却器**设备发送温度、湿度和压力值。
* 可以在解决方案中计划的、要在设备上运行的方法。 例如，**冷却器**设备必须实现 **Reboot**、**FirmwareUpdate**、**EmergencyValveRelease** 和 **IncreasePressure** 方法。