<!--author=SharS last changed: 9/17/15-->

#### <a name="to-install-regular-hotfixes-via-windows-powershell-for-storsimple"></a>For StorSimple 安装常规修补程序通过 Windows PowerShell
1. 连接到设备串行控制台。 有关详细信息，请参阅[步骤 1： 连接到串行控制台](../articles/storsimple/storsimple-update-device.md#step1)。
2. 在串行控制台菜单中，选择选项 1**完全访问权限登录**。 键入的密码。 默认密码是**Password1**。
3. 在命令提示符处，键入：
   
    ```
    Start-HcsHotfix
    ```
   
    > [!IMPORTANT]
    >
    > 此命令仅适用于常规修补程序。 只能有一个控制器上运行此命令，但将更新两个控制器。
    > 在更新过程; 可能会注意到的控制器故障转移但是，在故障转移不会影响系统可用性或操作。

4. 出现提示时，提供包含修补程序文件的网络共享文件夹的路径。
5. 系统将提示你进行确认。 类型**Y**继续进行修补程序安装。

