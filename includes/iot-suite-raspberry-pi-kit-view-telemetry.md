## <a name="view-the-telemetry"></a>查看遥测数据

Raspberry Pi 现在正在将遥测发送到远程监视解决方案。 你可以在解决方案仪表板上查看的遥测数据。 你还可以从解决方案仪表板向 Raspberry Pi 中发送消息。

- 导航到解决方案仪表板。
- 选择你的设备中**到视图的设备**下拉列表。
- 从 Raspberry Pi 的遥测数据显示在仪表板。

![显示从 Raspberry Pi 的遥测][img-telemetry-display]

## <a name="act-on-the-device"></a>在设备上执行操作

从解决方案的仪表板，你可以在 Raspberry Pi 上调用方法。 当 Raspberry Pi 连接到远程监视解决方案时，它将发送有关它支持的方法的信息。

- 在解决方案仪表板中，单击**设备**访问**设备**页。 选择在你树莓 Pi**设备列表**。 然后选择**方法**:

    ![列出在仪表板中的设备][img-list-devices]

- 上**调用方法**页上，选择**LightBlink**中**方法**下拉列表。

- 选择**InvokeMethod**。 LED 多次连接到 Raspberry Pi 闪烁。 Raspberry Pi 上的应用将确认发送回解决方案仪表板：

    ![显示方法历史记录][img-method-history]

- 你可以切换 LED 打开和关闭使用**ChangeLightStatus**方法替换**LightStatusValue**设置为**1**为上或**0**为关闭。

> [!WARNING]
> 如果你将在你的 Azure 帐户中运行的远程监视解决方案，则被支付在运行的时。 有关远程监视解决方案运行时减少消耗的详细信息，请参阅[配置 Azure IoT 套件预配置解决方案出于演示目的][lnk-demo-config]。 从你的 Azure 帐户中删除预配置的解决方案，使用它完成。


[img-telemetry-display]: media/iot-suite-raspberry-pi-kit-view-telemetry/telemetry.png
[img-list-devices]: media/iot-suite-raspberry-pi-kit-view-telemetry/listdevices.png
[img-method-history]: media/iot-suite-raspberry-pi-kit-view-telemetry/methodhistory.png

[lnk-demo-config]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/configure-preconfigured-demo.md