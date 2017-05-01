<!--author=SharS last changed: 9/17/15-->

#### <a name="to-install-maintenance-mode-updates-via-windows-powershell-for-storsimple"></a>通过 Windows PowerShell for StorSimple 安装维护模式更新
1. 如果尚未执行此操作，请访问设备串行控制台并选择选项 1“使用完整访问权限登录”。 
2. 键入密码。 默认密码为 **Password1**。
3. 在命令提示符处，键入：
   
     `Get-HcsUpdateAvailability` 
4. 将通知更新可用以及更新是中断性还是非中断性。 若要应用中断性更新，需要将设备置于维护模式。 有关说明，请参阅[步骤 2：进入维护模式](../articles/storsimple/storsimple-update-device.md#step2)。
5. 当设备处于维护模式时，在命令提示符处键入：`Start-HcsUpdate`
6. 系统将提示进行确认。 确认更新后，它们将安装在你当前正在访问的控制器上。 已安装这些更新后，将重新启动控制器。 
7. 监视更新的状态。 键入：
   
    `Get-HcsUpdateStatus`
   
    如果 `RunInProgress` 是 `True`，表示更新仍在进行中。 如果 `RunInProgress` 是 `False`，表示更新已完成。  
8. 在更新已安装在当前控制器上并重新启动后，连接到另一个控制器，然后执行步骤 1 到 6。
9. 在这两个控制器都已更新后，退出维护模式。 有关说明，请参阅[步骤 4：退出维护模式](../articles/storsimple/storsimple-update-device.md#step4)。

