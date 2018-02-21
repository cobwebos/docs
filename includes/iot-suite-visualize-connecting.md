## <a name="view-device-telemetry"></a>查看设备遥测数据

可以在解决方案中的“设备”页上查看从设备发送的遥测数据。

1. 在“设备”页上的设备列表中选择已预配的设备。 一个面板将显示有关设备的信息，其中包括设备遥测绘图：

    ![查看设备详细信息](media/iot-suite-visualize-connecting/devicesdetail.png)

1. 选择“压力”可更改遥测显示：

    ![查看压力遥测](media/iot-suite-visualize-connecting/devicespressure.png)

1. 若要查看有关设备的诊断信息，请向下滚动到“诊断”：

    ![查看设备诊断](media/iot-suite-visualize-connecting/devicesdiagnostics.png)

## <a name="act-on-your-device"></a>对设备执行操作

若要对设备调用方法，请使用远程监视解决方案中的“设备”页。 例如，在远程监视解决方案中，**冷却器**设备实现了 **FirmwareUpdate** 方法。

1. 选择“设备”可导航到解决方案中的“设备”页。

1. 在“设备”页上的设备列表中选择已预配的设备：

    ![选择物理设备](media/iot-suite-visualize-connecting/devicesselect.png)

1. 若要显示可对设备调用的方法列表，请选择“计划”。 若要计划要在多个设备上运行的方法，可以在列表中选择多个设备。 “计划”面板将显示普遍适用于所选择的所有设备的方法类型。

1. 选择 **FirmwareUpdate**，将作业名称设置为 **UpdatePhysicalChiller**。 将“固件版本”设置为 **2.0.0**，将“固件 URI”设置为 **http://contoso.com/updates/firmware.bin**，然后选择“应用”：

    ![计划固件更新](media/iot-suite-visualize-connecting/deviceschedule.png)

1. 模拟设备处理该方法时，一系列消息将显示在运行设备代码的控制台中。

1. 更新完成时，“设备”页上将显示新的固件版本：

    ![更新已完成](media/iot-suite-visualize-connecting/complete.png)

> [!NOTE]
> 若要跟踪解决方案中作业的状态，请选择“查看”。

## <a name="next-steps"></a>后续步骤

[自定义远程监视预配置的解决方案](../articles/iot-suite/iot-suite-remote-monitoring-customize.md)一文中介绍了自定义预配置的解决方案的一些方法。