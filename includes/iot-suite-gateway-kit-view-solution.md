## <a name="view-the-solution-dashboard"></a>查看解决方案的仪表板

解决方案仪表板，可管理已部署的解决方案。 例如，可以查看遥测数据、 添加设备，并调用方法。

1. 在设置完成且预配置解决方案的磁贴指示**准备**，选择**启动**以在新选项卡中打开远程监视解决方案门户网站。

    ![启动预配置的解决方案][img-launch-solution]

1. 默认情况下，解决方案门户显示*仪表板*。 导航到解决方案门户使用上页面的左侧的菜单的其他区域。

    ![远程监视的预配置的解决方案仪表板][img-menu]

## <a name="add-a-device"></a>添加设备

对于设备连接到预配置解决方案，它必须将自身标识到 IoT 中心使用的有效凭据。 你可以从解决方案仪表板检索设备凭据。 在本教程中稍后的客户端应用程序中包括的设备凭据。

若要将设备添加到远程监视解决方案，完成解决方案仪表板中的以下步骤：

1. 在仪表板的左下角，单击**将设备添加**。

   ![添加设备][1]

1. 在**自定义设备**面板中，单击**添加新**。

   ![添加自定义设备][2]

1. 选择**让我定义我自己的设备 ID**。 输入设备 ID，如**device01**，单击**检查 ID**以验证你尚未在解决方案中，使用名称，然后单击**创建**来预配该设备。

   ![添加设备 ID][3]

1. 记下的设备凭据 (**设备 ID**， **IoT 中心主机名**，和**设备密钥**)。 Intel NUC 上的客户端应用程序需要这些值可连接到远程监视解决方案。 然后单击**完成**。

    ![视图的设备凭据][4]

1. 在解决方案仪表板的设备列表中选择你的设备。 然后，在**设备详细信息**面板中，单击**启用设备**。 你的设备的状态现在为**运行**。 远程监视解决方案现在可以从你的设备接收的遥测，并调用在设备上的方法。

[img-launch-solution]: media/iot-suite-gateway-kit-view-solution/launch.png
[img-menu]: media/iot-suite-gateway-kit-view-solution/menu.png
[1]: media/iot-suite-gateway-kit-view-solution/suite0.png
[2]: media/iot-suite-gateway-kit-view-solution/suite1.png
[3]: media/iot-suite-gateway-kit-view-solution/suite2.png
[4]: media/iot-suite-gateway-kit-view-solution/suite3.png