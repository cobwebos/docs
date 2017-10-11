## <a name="customize-and-extend-the-device-management-actions"></a>自定义和扩展的管理操作的设备

你的 IoT 解决方案可以展开组定义的设备管理模式，或通过使用设备双向和云设备方法基元启用自定义模式。 设备管理操作的其他示例包括出厂重置、 固件更新、 软件更新、 电源管理、 网络和连接管理和数据加密。

## <a name="device-maintenance-windows"></a>设备维护时段

通常情况下，你将设备配置为最大程度减少中断和停机时间一次执行操作。 设备维护时段是一种常用的模式，以定义设备时应更新其配置的时间。 后端解决方案可以使用的设备双向所需的属性来定义并激活你启用了维护时段的设备的策略。 当设备收到了维护时段策略时，它可以使用设备双向的报告的属性报告策略的状态。 然后后, 端应用可以使用设备双向查询以证明到设备和每个策略的符合性。

## <a name="next-steps"></a>后续步骤

在本教程中，你可以使用直接的方法来触发远程重新启动设备上。 报告的属性用于报告设备上，从上次重新启动，并且查询设备双向来发现云环境带来的设备的上次重新启动时间。

若要继续开始使用 IoT 中心和设备管理模式，例如远程通过无线固件更新，请参阅：

[教程： 如何执行操作的固件更新][lnk-fwupdate]

若要了解如何扩展你的 IoT 解决方案和计划的方法调用在多台设备，请参阅[计划和广播的作业][ lnk-tutorial-jobs]教程。

若要继续入门 IoT 中心，请参阅[入门 IoT 边缘][lnk-iot-edge]。

[lnk-fwupdate]: ../articles/iot-hub/iot-hub-node-node-firmware-update.md
[lnk-tutorial-jobs]: ../articles/iot-hub/iot-hub-node-node-schedule-jobs.md
[lnk-iot-edge]: ../articles/iot-hub/iot-hub-linux-iot-edge-get-started.md