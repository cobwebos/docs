#### <a name="to-stop-and-start-a-cloud-appliance"></a>停止和启动云设备

1. 若要停止云设备，请转到云设备的 VM。
    ![StorSimple 云设备虚拟机](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart1.png)

2. 在命令栏中，单击“停止”。

    ![StorSimple 云设备虚拟机](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart2.png)

3. 出现确认提示时，单击“是”。

    ![StorSimple 云设备虚拟机](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart3.png)

4. 当停止某个 VM 时，它将被解除分配。 当云设备正在停止时，其状态为“正在解除分配”。 在云设备停止后，其状态为“已停止（已解除分配）。

    ![StorSimple 云设备虚拟机](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart4.png)

5. 在 VM 停止后，单击“启动”（按钮变得可用）以启动 VM。 在云设备已启动后，其状态为“已启动”。

    ![StorSimple 云设备虚拟机](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart5.png)

使用以下 cmdlet 停止和启动云设备。

`Stop-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`

`Start-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`

#### <a name="to-restart-a-cloud-appliance"></a>重新启动云设备

若要重新启动云设备，请转到云设备的 VM。 在命令栏中，单击“重新启动”。 出现提示时，确认重新启动。 当云设备准备就绪可供使用时，其状态为“正在运行”。

![StorSimple 云设备虚拟机](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart6.png)

使用以下 cmdlet 重新启动云设备。

`Restart-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`

