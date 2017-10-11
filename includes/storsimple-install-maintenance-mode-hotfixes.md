<!--author=SharS last changed: 9/17/15-->

#### <a name="to-install-maintenance-mode-hotfixes-via-windows-powershell-for-storsimple"></a>For StorSimple 安装维护模式修补程序通过 Windows PowerShell
> [!IMPORTANT]
> 在维护模式下，你需要首先应用修补程序，在一个控制器上，然后在另一个控制器。
> 
> 

1. 将设备置于维护模式。 请参阅[步骤 2： 输入维护模式](../articles/storsimple/storsimple-update-device.md#step2)有关如何进入维护模式的说明。
2. 若要应用此修补程序，请键入：
   
     `Start-HcsHotfix` 
3. 出现提示时，提供包含修补程序文件的网络共享文件夹的路径。
4. 系统将提示你进行确认。 类型**Y**继续进行修补程序安装。
5. 在一个控制器上应用修补程序后，登录到另一个控制器上。 用于与前一个控制器，请应用修补程序。
6. 当应用修补程序之后，退出维护模式。 请参阅[步骤 4： 退出维护模式](../articles/storsimple/storsimple-update-device.md#step4)有关的说明。

