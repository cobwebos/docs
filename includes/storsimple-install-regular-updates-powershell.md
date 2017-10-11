<!--author=SharS last changed: 11/18/16-->

#### <a name="to-install-regular-updates-via-windows-powershell-for-storsimple"></a>For StorSimple 安装常规更新通过 Windows PowerShell
1. 打开设备串行控制台并选择选项 1，**完全访问权限登录**。 键入的密码。 默认密码是*Password1*。 
2. 在命令提示符处，键入：
   
     `Get-HcsUpdateAvailability`
   
    如果有可用更新和更新是中断性的还是非破坏性，你将收到通知。
3. 在命令提示符处，键入：
   
     `Start-HcsUpdate`
   
    在更新过程将启动。

> [!IMPORTANT]
> * 此命令仅适用于常规更新。 只能有一个控制器上运行此命令，但将更新两个控制器。 
> * 在更新过程; 可能会注意到的控制器故障转移但是，在故障转移不会影响系统可用性或操作。
> 
> 

