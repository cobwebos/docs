## <a name="view-device-telemetry-in-the-dashboard"></a>在仪表板视图设备遥测
远程监视解决方案中的仪表板，可查看你的设备将发送到 IoT 中心的遥测数据。

1. 在你的浏览器，返回到远程监视解决方案仪表板，单击**设备**在左侧面板中导航到**的设备列表**。
2. 在**的设备列表**，你应看到你的设备的状态是**运行**。 否则，请单击**启用设备**中**设备详细信息**面板。
   
    ![查看设备状态][18]
3. 单击**仪表板**若要返回到仪表板，请选择你的设备中**到视图的设备**下拉以查看其遥测数据。 从示例应用程序遥测是针对内部的温度的 50 个单位、 55 单位外部温度和湿度为 50 个单位。
   
    ![查看设备遥测][img-telemetry]

## <a name="invoke-a-method-on-your-device"></a>调用你的设备上的方法
远程监视解决方案中的仪表板，可对你的设备通过 IoT 中心调用的方法。 例如，远程监视解决方案中，你可以调用方法以模拟重新启动设备。

1. 在远程监视解决方案仪表板，单击**设备**在左侧面板中导航到**的设备列表**。
2. 单击**设备 ID**为你的设备中**的设备列表**。
3. 在**设备详细信息**面板中，单击**方法**。
   
    ![设备方法][13]
4. 在**方法**下拉列表中，选择**InitiateFirmwareUpdate**，然后在**FWPACKAGEURI**输入 dummy URL。 单击**调用方法**以在设备上调用方法。
   
    ![调用设备方法][14]
   

5. 你看到一条消息，在控制台中运行你设备的代码，当设备处理方法。 方法的结果添加到解决方案门户中的历史记录：

    ![查看方法历史记录][img-method-history]

## <a name="next-steps"></a>后续步骤
文章[自定义预配置解决方案][ lnk-customize]描述你可以扩展此示例的一些方法。 可能的扩展名包括使用真实传感器以及实现其他命令。

你可以详细了解[azureiotsuite.com 站点上的权限][lnk-permissions]。

[13]: ./media/iot-suite-visualize-connecting/suite4.png
[14]: ./media/iot-suite-visualize-connecting/suite7-1.png
[18]: ./media/iot-suite-visualize-connecting/suite10.png
[img-telemetry]: ./media/iot-suite-visualize-connecting/telemetry.png
[img-method-history]: ./media/iot-suite-visualize-connecting/history.png
[lnk-customize]: ../articles/iot-suite/iot-suite-guidance-on-customizing-preconfigured-solutions.md
[lnk-permissions]: ../articles/iot-suite/iot-suite-permissions.md
