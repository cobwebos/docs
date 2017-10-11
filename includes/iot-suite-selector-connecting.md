> [!div class="op_single_selector"]
> * [在 Windows 上的 C](../articles/iot-suite/iot-suite-connecting-devices.md)
> * [在 Linux 上的 C](../articles/iot-suite/iot-suite-connecting-devices-linux.md)
> * [Node.js](../articles/iot-suite/iot-suite-connecting-devices-node.md)
> 
> 

## <a name="scenario-overview"></a>方案概述
在此方案中，你创建的设备，将以下遥测发送到远程监视[预配置解决方案][lnk-what-are-preconfig-solutions]:

* 外部温度
* 内部的温度
* 湿度

为简单起见，在设备上的代码生成示例值，但我们建议你通过连接到你的设备的实际传感器并发送实际遥测扩展示例。

设备也是能够应对解决方案仪表板和解决方案仪表板中设置的所需的属性值从调用的方法。

若要完成本教程，你需要有效的 Azure 帐户。 如果你没有帐户，可以在几分钟内创建一个免费试用帐户。 有关详细信息，请参阅[Azure 免费试用版][lnk-free-trial]。

## <a name="before-you-start"></a>开始之前
为你的设备编写任何代码之前，您必须设置远程监视预配置的解决方案，并设置该解决方案中的新自定义设备。

### <a name="provision-your-remote-monitoring-preconfigured-solution"></a>设置远程监视预配置的解决方案
本教程中创建的设备将数据发送到的实例[远程监视][ lnk-remote-monitoring]预配置解决方案。 如果你的 Azure 帐户中，您已经尚未配置远程监视的预配置的解决方案，使用以下步骤：

1. 上<https://www.azureiotsuite.com/>页上，单击 **+** 创建解决方案。
2. 单击**选择**上**远程监视**面板创建你的解决方案。
3. 上**创建远程监视解决方案**页上，输入**解决方案名称**的您的选择，选择**区域**你想要部署到，并选择要想要使用的 Azure 订阅。 然后单击**创建解决方案**。
4. 等待，直到完成设置过程。

> [!WARNING]
> 预配置的解决方案使用计费的 Azure 服务。 请确保它以避免任何不必要的费用完成时，会从你的订阅中删除预配置的解决方案。 你可以完全删除预配置的解决方案从你的订阅，请访问<https://www.azureiotsuite.com/>页。
> 
> 

当预配过程有关的远程监视解决方案完成后，请单击**启动**在浏览器中打开解决方案仪表板。

![解决方案仪表板][img-dashboard]

### <a name="provision-your-device-in-the-remote-monitoring-solution"></a>设置你的设备远程监视解决方案
> [!NOTE]
> 如果您已经在你的解决方案配置设备，则可以跳过此步骤。 你需要知道的设备凭据时创建客户端应用程序。
> 
> 

对于设备连接到预配置解决方案，它必须将自身标识到 IoT 中心使用的有效凭据。 你可以从解决方案仪表板检索设备凭据。 在本教程中稍后的客户端应用程序中包括的设备凭据。

若要将设备添加到远程监视解决方案，完成解决方案仪表板中的以下步骤：

1. 在仪表板的左下角，单击**将设备添加**。
   
   ![添加设备][1]
2. 在**自定义设备**面板中，单击**添加新**。
   
   ![添加自定义设备][2]
3. 选择**让我定义我自己的设备 ID**。 输入设备 ID，如**mydevice**，单击**检查 ID**以验证该名称尚未在使用中，然后单击**创建**来预配该设备。
   
   ![添加设备 ID][3]
4. 记下的设备凭据 （设备 ID、 IoT 中心主机名和设备密钥）。 客户端应用程序需要这些值可连接到远程监视解决方案。 然后单击**完成**。
   
    ![视图的设备凭据][4]
5. 在解决方案仪表板的设备列表中选择你的设备。 然后，在**设备详细信息**面板中，单击**启用设备**。 你的设备的状态现在为**运行**。 远程监视解决方案现在可以从你的设备接收的遥测，并调用在设备上的方法。

[img-dashboard]: ./media/iot-suite-selector-connecting/dashboard.png
[1]: ./media/iot-suite-selector-connecting/suite0.png
[2]: ./media/iot-suite-selector-connecting/suite1.png
[3]: ./media/iot-suite-selector-connecting/suite2.png
[4]: ./media/iot-suite-selector-connecting/suite3.png

[lnk-what-are-preconfig-solutions]: ../articles/iot-suite/iot-suite-what-are-preconfigured-solutions.md
[lnk-remote-monitoring]: ../articles/iot-suite/iot-suite-remote-monitoring-sample-walkthrough.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/