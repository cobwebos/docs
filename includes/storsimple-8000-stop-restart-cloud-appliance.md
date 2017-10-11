#### <a name="to-stop-and-start-a-cloud-appliance"></a>若要停止和启动云设备

1. 若要停止云设备，请转到你的云设备的 VM。
    ![StorSimple 云设备虚拟机](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart1.png)

2. 从命令栏中，单击**停止**。

    ![StorSimple 云设备虚拟机](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart2.png)

3. 当系统提示确认，请单击**是**。

    ![StorSimple 云设备虚拟机](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart3.png)

4. 当你停止 VM 时，它获取释放了会话。 虽然正在停止云设备，其状态将是**Deallocating**。 停止云设备后，其状态将是**已停止 （释放）**。

    ![StorSimple 云设备虚拟机](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart4.png)

5. 虚拟机停止后，单击**启动**（按钮将变为可用） 来启动虚拟机。 云设备已启动后，其状态将是**已启动**。

    ![StorSimple 云设备虚拟机](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart5.png)

使用以下 cmdlet 来停止和启动云设备。

`Stop-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`

`Start-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`

#### <a name="to-restart-a-cloud-appliance"></a>若要重新启动云设备

要重新启动云设备，请转到你的云设备的 VM。 从命令栏中，单击**重新启动**。 出现提示时，确认重新启动。 可供您使用云设备时，其状态将是**运行**。

![StorSimple 云设备虚拟机](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart6.png)

使用以下 cmdlet 重新启动云设备。

`Restart-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`

