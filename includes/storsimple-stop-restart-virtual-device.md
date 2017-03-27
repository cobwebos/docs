#### <a name="to-stop-and-start-a-virtual-device"></a>停止和启动虚拟设备
若要停止虚拟设备，请单击其名称，然后单击“关闭” 。 虚拟设备在关闭时处于“正在停止” 状态。 虚拟设备在停止后处于“已停止” 状态。

使用以下 cmdlet 停止和启动虚拟设备。

`Stop-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`

`Start-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`

#### <a name="to-restart-a-virtual-device"></a>重新启动虚拟设备
如果虚拟设备正在运行，但你想要重新启动它，请单击其名称，然后单击“重新启动” 。 虚拟设备在重新启动时处于“正在重新启动” 状态。 虚拟设备可随时供你使用时处于“正在运行” 状态。

使用以下 cmdlet 重新启动虚拟设备。

`Restart-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`



<!--HONumber=Jan17_HO1-->


