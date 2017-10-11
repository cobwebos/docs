<!--author=SharS last changed: 9/17/15-->

#### <a name="to-install-maintenance-mode-updates-via-windows-powershell-for-storsimple"></a>若要安装维护模式更新通过 Windows PowerShell for StorSimple
1. 如果你尚未这样做，访问设备串行控制台，选择选项 1，**完全访问权限登录**。 
2. 键入的密码。 默认密码是**Password1**。
3. 在命令提示符处，键入：
   
     `Get-HcsUpdateAvailability` 
4. 如果有可用更新和更新是中断性的还是非破坏性，你将收到通知。 若要应用中断性更新，你需要将设备置于维护模式。 请参阅[步骤 2： 输入维护模式](../articles/storsimple/storsimple-update-device.md#step2)有关的说明。
5. 当你的设备处于维护模式，在命令提示符处，键入:`Start-HcsUpdate`
6. 系统将提示你进行确认。 确认更新后，它们将在你当前访问的控制器上安装它。 已安装这些更新后，将重新启动控制器。 
7. 监视更新的状态。 键入：
   
    `Get-HcsUpdateStatus`
   
    如果`RunInProgress`是`True`，更新是仍在进行。 如果`RunInProgress`是`False`，它指示更新已完成。  
8. 在当前控制器上安装更新后，已重新启动时连接到另一个控制器，然后执行步骤 1 至 6。
9. 这两个控制器都更新之后，退出维护模式。 请参阅[步骤 4： 退出维护模式](../articles/storsimple/storsimple-update-device.md#step4)有关的说明。

