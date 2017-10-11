#### <a name="to-stop-and-start-a-virtual-device"></a>若要停止和启动虚拟设备
若要停止虚拟设备，请单击其名称，然后单击**关闭**。 当虚拟设备正在关闭时，其状态将是**停止**。 停止虚拟设备后，其状态将是**已停止**。

使用以下 cmdlet 来停止和启动虚拟设备。

`Stop-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`

`Start-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`

#### <a name="to-restart-a-virtual-device"></a>若要重新启动虚拟设备
当虚拟设备正在运行并且你想要重新启动它时，请单击其名称，然后单击**重新启动**。 尽管虚拟设备正在重新启动，其状态将是**正在重新启动**。 可供您使用虚拟设备时，其状态将是**运行**。

使用以下 cmdlet 重新启动虚拟设备。

`Restart-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`

