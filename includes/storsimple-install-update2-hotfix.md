<!--author=alkohli last changed: 03/17/16-->

#### <a name="to-download-hotfixes"></a>若要下载修补程序
执行以下步骤以从 Microsoft Update 目录中下载软件更新。

1. 启动 Internet Explorer 并导航到[http://catalog.update.microsoft.com](http://catalog.update.microsoft.com)。
2. 如果这是你首次在此计算机上使用 Microsoft 更新目录，请单击**安装**当系统提示安装的 Microsoft Update 目录外接程序。
    ![安装目录](./media/storsimple-install-update2-hotfix/HCS_InstallCatalog-include.png)
3. 在 Microsoft 更新目录的搜索框中，输入你想要下载，例如的修补程序的知识库 (KB) 数**3121901**，然后单击**搜索**。
   
    该修补程序列表显示，例如， **StorSimple 8000 系列的累积软件捆绑更新 2.0**。
   
    ![搜索目录](./media/storsimple-install-update2-hotfix/HCS_SearchCatalog1-include.png)
4. 单击 **“添加”**。 该更新添加到购物篮中。
5. 上表中列出的任何其他的修补程序的搜索 (**3121900**， **3080728**， **3090322**，和**3121899**)，并添加每个购物篮。
6. 单击**查看购物篮**。
7. 单击“下载” 。 指定或**浏览**到想要显示的下载的本地位置。 更新进行下载到指定的位置，并更新与同名的子文件夹中放入。 也可以将文件夹复制到可从设备访问的网络共享。

> [!NOTE]
> 修补程序必须能够访问从两个控制器检测从对等控制器的任何潜在错误消息。
> 
> 

#### <a name="to-install-and-verify-regular-mode-hotfixes"></a>若要安装和验证常规模式修补程序
执行以下步骤来安装和验证常规模式修补程序。 如果你已安装它们使用 Azure 门户，跳到[安装和验证维护模式修补程序](#to-install-and-verify-maintenance-mode-hotfixes)。

1. 若要安装的修补程序，访问你的 StorSimple 设备串行控制台上的 Windows PowerShell 接口。 按照中的详细的说明[使用 PuTTy 连接到串行控制台](../articles/storsimple/storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console)。 在命令提示符下，按**Enter**。
2. 选择**选项 1**具有完全访问权限的设备登录。
3. 若要安装的修补程序，在命令提示符处，键入：
   
    `Start-HcsHotfix -Path <path to update file> -Credential <credentials in domain\username format>`
   
    在上面的命令的共享路径中使用 IP，而不是 DNS。 仅当访问经过身份验证的共享时，才使用凭据参数。
   
    我们建议你使用的凭据参数来访问共享。 打开到"everyone"的偶数共享通常未处于打开状态向未经身份验证的用户。
   
    示例输出所示。
   
    ```
    Controller0>Start-HcsHotfix -Path \\10.100.100.100\share
    \hcsmdssoftwareupdate.exe -Credential contoso\John

    Confirm

    This operation starts the hotfix installation and could reboot one or
    both of the controllers. If the device is serving I/Os, these will not
    be disrupted. Are you sure you want to continue?
    [Y] Yes [N] No [?] Help (default is "Y"): Y
    ```
4. 类型**Y**当系统提示确认安装修补程序。
5. 使用监视更新`Get-HcsUpdateStatus`cmdlet。
   
    下面的示例输出显示正在进行的更新。 `RunInprogress`将`True`当更新正在进行中。
   
    ```
    Controller0>Get-HcsUpdateStatus
    RunInprogress       : True
    LastHotfixTimestamp : 12/21/2015 10:36:13 PM
    LastUpdateTimestamp : 12/21/2015 10:35:25 PM
    Controller0Events   :
    Controller1Events   :
    ```
   
     下面的示例输出指示更新完成。 `RunInProgress`将`False`当更新已完成。
   
    ```
    Controller1>Get-HcsUpdateStatus

    RunInprogress       : False
    LastHotfixTimestamp : 12/21/2015 10:59:13 PM
    LastUpdateTimestamp : 12/21/2015 10:35:25 PM
    Controller0Events   :
    Controller1Events   :
    ```
   
   > [!NOTE]
   > 有时，cmdlet 报表`False`更新时仍在进行。 若要确保修补程序已完成，请稍等几分钟，重新运行此命令并验证`RunInProgress`是`False`。 如果是，已完成的修补程序。

6. 已完成软件更新之后，重复步骤 3-5 才能安装和监视的 SaaS 代理和 MDS 代理。 确保`all-hcsmdssoftwareupdate_0b438ddf0d5b686aada2378b754fac8c7f2160e9.exe`之前安装了`all-cismdsagentupdatebundle_f98e62f4d56c79e2a6644d027af7a2393a93827a.exe`。
7. 验证系统软件版本。 键入：
   
    `Get-HcsSystem`
   
    你应看到以下版本：
   
   * HcsSoftwareVersion: 6.3.9600.17673
   * CisAgentVersion: 1.0.9150.0
   * MdsAgentVersion: 30.0.4698.13
     
     如果版本号不更改应用此更新后，它指示未能应用修补程序。 你应看到此，请联系[Microsoft 支持](../articles/storsimple/storsimple-contact-microsoft-support.md)以获得进一步帮助。
8. 重复步骤 3-5 以安装剩余的常规模式修补程序。
   
   * LSI 驱动程序-KB3121900
   * Storport 更新-KB3080728
   * Spaceport 更新-KB3090322

#### <a name="to-install-and-verify-maintenance-mode-hotfixes"></a>若要安装和验证维护模式修补程序
使用 KB3121899 安装磁盘固件更新。 这些是中断性更新，需要大约 30 分钟时间才能完成。 你可以选择安装这些计划的维护时段中通过连接到设备串行控制台。

请注意，是否磁盘固件已是最新的你不需要安装这些更新。 运行`Get-HcsUpdateAvailability`从设备串行控制台来检查是否有可用更新以及更新是否发生中断性的 cmdlet （维护模式） 或非破坏性 （正则模式） 更新。

若要安装的磁盘固件更新，请按照下面的说明。

1. 将设备置于维护模式。 请注意，你不应使用 Windows PowerShell 远程处理连接到设备处于维护模式时。 而是当通过设备串行控制台连接的设备控制器上运行此 cmdlet。 键入：
   
    `Enter-HcsMaintenanceMode`
   
    示例输出所示。
   
        Controller0>Enter-HcsMaintenanceMode
        Checking device state...
   
        In maintenance mode, your device will not service IOs and will be disconnected from the Microsoft Azure StorSimple Manager service. Entering maintenance mode will end the current session and reboot both controllers, which takes a few minutes to complete. Are you sure you want to enter maintenance mode?
        [Y] Yes [N] No (Default is "Y"): Y
   
        -----------------------MAINTENANCE MODE------------------------
        Microsoft Azure StorSimple Appliance Model 8100
        Name: Update2-8100-SHG0997879L76YD
        Software Version: 6.3.9600.17664
        Copyright (C) 2014 Microsoft Corporation. All rights reserved.
        You are connected to Controller0 - Passive
        ---------------------------------------------------------------
   
        Serial Console Menu
        [1] Log in with full access
        [2] Log into peer controller with full access
        [3] Connect with limited access
        [4] Change language
        Please enter your choice>
   
    然后，两个控制器重新启动进入维护模式。
2. 若要安装磁盘固件更新，请键入：
   
    `Start-HcsHotfix -Path <path to update file> -Credential <credentials in domain\username format>`
   
    示例输出所示。
   
        Controller1>Start-HcsHotfix -Path \\10.100.100.100\share\DiskFirmwarePackage.exe -Credential contoso\john
        Enter Password:
        WARNING: In maintenance mode, hotfixes should be installed on each controller sequentially. After the hotfix is installed on this controller, install it on the peer controller.
        Confirm
        This operation starts a hotfix installation and could reboot one or both of the controllers. By installing new updates you agree to, and accept any additional terms associated with, the new functionality listed in the release notes (https://go.microsoft.com/fwLink/?LinkID=613790). Are you sure you want to continue?
        [Y] Yes [N] No (Default is "Y"): Y
        WARNING: Installation is currently in progress. This operation can take several minutes to complete.
3. 监视安装进度使用`Get-HcsUpdateStatus`命令。 已完成更新时`RunInProgress`更改为`False`。
4. 安装完成后，维护模式修补程序已在其的控制器安装重新启动。 作为具有完全访问权限的选项 1 登录并验证磁盘固件版本。 键入：
   
   `Get-HcsFirmwareVersion`
   
   预期的磁盘固件版本有：
   
   `XMGG, XGEG, KZ50, F6C2, VR08`
   
   示例输出所示。
   
       -----------------------MAINTENANCE MODE------------------------
       Microsoft Azure StorSimple Appliance Model 8100
       Name: Update2-8100-SHG0997879L76YD
       Software Version: 6.3.9600.17664
       Copyright (C) 2014 Microsoft Corporation. All rights reserved.
       You are connected to Controller1
       ---------------------------------------------------------------
   
       Controller1>Get-HcsFirmwareVersion
   
       Controller0 : TalladegaFirmware
         ActiveBIOS:0.45.0006
         BackupBIOS:0.45.0008
         MainCPLD:17.0.0005
         ActiveBMCRoot:2.0.000E
         BackupBMCRoot:2.0.000E
         BMCBoot:2.0.0001
         LsiFirmware:19.00.00.00
         LsiBios:07.37.00.00
         Battery1Firmware:06.29
         Battery2Firmware:06.29
         DomFirmware:X231600
         CanisterFirmware:3.5.0.32
         CanisterBootloader:5.03
         CanisterConfigCRC:0xD1B030A4
         CanisterVPDStructure:0x06
         CanisterGEMCPLD:0x17
         CanisterVPDCRC:0xEE3504B4
         MidplaneVPDStructure:0x0C
         MidplaneVPDCRC:0xA6BD4F64
         MidplaneCPLD:0x10
         PCM1Firmware:1.00|1.05
         PCM1VPDStructure:0x05
         PCM1VPDCRC:0x41BEF99C
         PCM2Firmware:1.00|1.05
         PCM2VPDStructure:0x05
         PCM2VPDCRC:0x41BEF99C
   
         DisksFirmware
         SEAGATE:ST400FM0073:XGEG
         SEAGATE:ST400FM0073:XGEG
         SEAGATE:ST400FM0073:XGEG
         SEAGATE:ST400FM0073:XGEG
         SEAGATE:ST4000NM0023:XMGG
         SEAGATE:ST4000NM0023:XMGG
         SEAGATE:ST4000NM0023:XMGG
         SEAGATE:ST4000NM0023:XMGG
         SEAGATE:ST4000NM0023:XMGG
         SEAGATE:ST4000NM0023:XMGG
         SEAGATE:ST4000NM0023:XMGG
         SEAGATE:ST4000NM0023:XMGG
   
    运行`Get-HcsFirmwareVersion`命令验证是否已更新的软件版本的第二个控制器上。 然后可以退出维护模式。 为此，请键入以下命令为每个设备控制器：
   
   `Exit-HcsMaintenanceMode`
5. 当你退出维护模式，请重新启动控制器。 后磁盘固件更新已成功应用，并且在设备退出维护模式下，返回到 Azure 经典门户。 请注意，门户可能不会显示安装 24 小时的维护模式更新。

