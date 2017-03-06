> [!div class="op_single_selector"]
> * [Windows 上的 C](../articles/iot-suite/iot-suite-connecting-devices.md)
> * [Linux 上的 C](../articles/iot-suite/iot-suite-connecting-devices-linux.md)
> * [mbed 上的 C](../articles/iot-suite/iot-suite-connecting-devices-mbed.md)
> * [Node.js](../articles/iot-suite/iot-suite-connecting-devices-node.md)
> 
> 

## <a name="scenario-overview"></a>方案概述
在此方案中，创建会将以下遥测数据发送到远程监视[预配置解决方案][lnk-what-are-preconfig-solutions]的设备：

* 外部温度
* 内部温度
* 湿度

为简单起见，设备上的代码将生成示例值，但我们建议你通过将实际传感器连接到设备并发送实际的遥测数据来扩展此示例。

该设备还能响应从解决方案仪表板中调用的方法，以及在解决方案仪表板中设置的所需属性值。

要完成此教程，需要一个有效的 Azure 帐户。 如果你没有帐户，只需花费几分钟就能创建一个免费试用帐户。 有关详细信息，请参阅 [Azure 免费试用][lnk-free-trial]。

## <a name="before-you-start"></a>开始之前
在为设备编写任何代码之前，必须先预配远程监视预配置解决方案，并在该解决方案中预配新的自定义设备。

### <a name="provision-your-remote-monitoring-preconfigured-solution"></a>预配远程监视预配置解决方案
本教程中创建的设备会将数据发送到[远程监视][lnk-remote-monitoring]预配置解决方案的实例中。 如果尚未在 Azure 帐户中预配远程监视预配置解决方案，请使用以下步骤：

1. 在 <https://www.azureiotsuite.com/> 页上，单击“+”创建解决方案。
2. 单击“远程监视”面板上的“选择”创建解决方案。
3. 在“创建远程监视解决方案”页上，输入所选的**解决方案名称**，选择要部署到的**区域**，然后选择要使用的 Azure 订阅。 然后单击“创建解决方案”。
4. 等待预配过程完成。

> [!WARNING]
> 预配置的解决方案使用可计费的 Azure 服务。 当使用完预配置的解决方案之后，请务必将它从订阅中删除，以避免产生任何不必要的费用。 只需访问 <https://www.azureiotsuite.com/> 页，即可将预配置的解决方案从订阅中完全删除。
> 
> 

远程监视解决方案的预配过程完成之后，请单击“启动”，在浏览器中打开解决方案仪表板。

![解决方案仪表板][img-dashboard]

### <a name="provision-your-device-in-the-remote-monitoring-solution"></a>在远程监视方案中预配设备
> [!NOTE]
> 如果你已在解决方案中预配了设备，则可以跳过此步骤。 创建客户端应用程序时需要知道设备凭据。
> 
> 

对于连接到预配置解决方案的设备，该设备必须使用有效的凭据将自身标识到 IoT 中心。 可以从解决方案仪表板中检索设备凭据。 在本教程中，稍后将在客户端应用程序中添加设备凭据。

若要在远程监视解决方案中添加设备，请在解决方案仪表板中完成以下步骤：

1. 在仪表板左下角，单击“添加设备”。
   
   ![添加设备][1]
2. 在“自定义设备”面板中，单击“新增”。
   
   ![添加自定义设备][2]
3. 选择“让我定义我自己的设备 ID”。 输入设备 ID（例如“mydevice”），单击“检查 ID”验证该名称是否尚未使用，然后单击“创建”预配设备。
   
   ![添加设备 ID][3]
4. 记下设备凭据（设备 ID、IoT 中心主机名和设备密钥）。 客户端应用程序需要这些值才能连接到远程监视解决方案。 然后单击“Done”（完成）。
   
    ![查看设备凭据][4]
5. 在解决方案仪表板中的设备列表中选择设备。 然后，在“设备详细信息”面板中，单击“启用设备”。 设备状态现在为“正在运行”。 远程监视解决方案现在可以从设备接收遥测数据，并在设备上调用方法。

[img-dashboard]: ./media/iot-suite-selector-connecting/dashboard.png
[1]: ./media/iot-suite-selector-connecting/suite0.png
[2]: ./media/iot-suite-selector-connecting/suite1.png
[3]: ./media/iot-suite-selector-connecting/suite2.png
[4]: ./media/iot-suite-selector-connecting/suite3.png

[lnk-what-are-preconfig-solutions]: ../articles/iot-suite/iot-suite-what-are-preconfigured-solutions.md
[lnk-remote-monitoring]: ../articles/iot-suite/iot-suite-remote-monitoring-sample-walkthrough.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/