<!--author=SharS last changed: 9/17/15-->

#### <a name="to-install-regular-hotfixes-via-windows-powershell-for-storsimple"></a>通过 Windows PowerShell for StorSimple 安装常规修补程序
1. 连接到设备串行控制台。 有关详细信息，请参阅[步骤 1：连接到串行控制台](../articles/storsimple/storsimple-update-device.md#step1)。
2. 在串行控制台菜单中，选择选项 1“使用完整访问权限登录”。 键入密码。 默认密码为 **Password1**。
3. 在命令提示符处，键入：
   
    ```
    Start-HcsHotfix
    ```
   
    > [!IMPORTANT]
    >
    > 此命令仅适用于常规修补程序。 仅在一个控制器上运行此命令，但这两个控制器都将更新。
    > 在更新过程中，你可能会注意到控制器故障转移；但是，故障转移不会影响系统可用性或操作。

4. 出现提示时，提供包含修补程序文件的网络共享文件夹的路径。
5. 系统会提示进行确认。 键入 **Y** 继续进行修补程序安装。

