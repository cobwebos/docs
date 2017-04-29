<!--author=SharS last changed: 11/18/16-->

#### <a name="to-install-regular-updates-via-windows-powershell-for-storsimple"></a>通过 Windows PowerShell for StorSimple 安装常规更新
1. 打开设备串行控制台，并选择选项 1“使用完整访问权限登录”。 键入密码。 默认密码为 *Password1*。 
2. 在命令提示符处，键入：
   
     `Get-HcsUpdateAvailability`
   
    将通知更新可用以及更新是中断性还是非中断性。
3. 在命令提示符处，键入：
   
     `Start-HcsUpdate`
   
    将启动更新过程。

> [!IMPORTANT]
> * 此命令仅适用于常规更新。 仅在一个控制器上运行此命令，但这两个控制器都将更新。 
> * 你可能会注意到更新过程中的控制器故障转移；但故障转移不会影响系统可用性或运行。
> 
> 

