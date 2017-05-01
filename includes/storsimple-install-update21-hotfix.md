<!--author=alkohli last changed: 05/19/16-->

#### <a name="to-download-hotfixes"></a>下载修补程序
执行以下步骤，从 Microsoft 更新目录下载软件更新。

1. 启动 Internet Explorer 并导航到 [http://catalog.update.microsoft.com](http://catalog.update.microsoft.com)。
2. 如果这是你在此计算机上首次使用 Microsoft 更新目录，请在系统提示是否安装 Microsoft 更新目录外接程序时单击“安装”。
    ![安装目录](./media/storsimple-install-update2-hotfix/HCS_InstallCatalog-include.png)
3. 在 Microsoft 更新目录的搜索框中，输入要下载的修补程序的知识库 (KB) 编号（例如 **3179904**），然后单击“搜索”。
   
    随后将显示修补程序列表，例如“适用于 StorSimple 8000 系列的累积软件捆绑包更新 2.2”。
   
    ![搜索目录](./media/storsimple-install-update2-hotfix/HCS_SearchCatalog1-include.png)
4. 单击 **“添加”**。 该更新随即添加到购物篮中。
5. 搜索上表中所列的其他任何修补程序（**3103616**、**3146621**），然后将每个修补程序添加到购物篮中。
6. 单击“查看购物篮”。
7. 单击“下载”。 指定或**浏览**到下载项要保存到的本地位置。 更新随即会下载到指定的位置，放置在与更新名称相同的子文件夹中。 也可以将该文件夹复制到可通过设备访问的网络共享位置。

> [!NOTE]
> 必须能够同时从两个控制器访问修补程序，以便检测来自对等控制器的任何潜在错误消息。
> 
> 

#### <a name="to-install-and-verify-regular-mode-hotfixes"></a>安装和验证常规模式修补程序
执行以下步骤安装和验证常规模式修补程序。 如果已使用 Azure 门户安装这些修补程序，请直接跳到[安装和验证维护模式修补程序](#to-install-and-verify-maintenance-mode-hotfixes)。

1. 若要安装修补程序，请访问 StorSimple 设备串行控制台上的 Windows PowerShell 界面。 遵循 [Use PuTTy to connect to the serial console](../articles/storsimple/storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console)（使用 PuTTy 连接到串行控制台）中的详细说明。 在命令提示符下，按 **Enter**。
2. 选择“选项 1”，以完全访问权限登录到设备。 建议先在被动控制器中安装修补程序。
3. 若要安装修补程序，请在命令提示符下键入：
   
    `Start-HcsHotfix -Path <path to update file> -Credential <credentials in domain\username format>`
   
    请在上述命令的共享路径中使用 IP 而不是 DNS。 仅当要访问经过身份验证的共享时，才使用凭据参数。
   
    建议使用凭据参数来访问共享。 即使是向“任何人”开放的共享，通常也不会向未经身份验证的用户开放。
   
    根据提示提供密码。
   
    下面显示了示例输出。
   
    ```
    Controller0>Start-HcsHotfix -Path \\10.100.100.100\share
    \hcsmdssoftwareupdate.exe -Credential contoso\John

    Confirm

    This operation starts the hotfix installation and could reboot one or
    both of the controllers. If the device is serving I/Os, these will not
    be disrupted. Are you sure you want to continue?
    [Y] Yes [N] No [?] Help (default is "Y"): Y
    ```

4. 出现确认安装修补程序的提示时，请键入 **Y**。
   
   > [!IMPORTANT]
   > 安装 Update 2.2 时，请只安装开头为“all-hcsmdssoftwareudpate”的二进制文件。 不要安装开头为 all-cismdsagentupdatebundle 的 Cis 和 MDS 代理更新。 否则可能会导致出错。 

5. 使用 `Get-HcsUpdateStatus` 监视更新。 先在被动控制器上完成更新。 更新被动控制器之后，将发生故障转移，然后，更新将应用到另一个控制器。 两个控制器都更新后，更新即告完成。
   
    以下示例输出显示更新正在进行。 更新正在进行时，`RunInprogress` 为 `True`。
   
    ```
    Controller0>Get-HcsUpdateStatus
    RunInprogress       : True
    LastHotfixTimestamp :
    LastUpdateTimestamp : 5/5/2016 2:04:02 AM
    Controller0Events   :
    Controller1Events   :
    ```
   
     以下示例输出指示更新已完成。 更新完成时，`RunInProgress` 为 `False`。
   
    ```
    Controller0>Get-HcsUpdateStatus
    RunInprogress       : False
    LastHotfixTimestamp : 5/17/2016 9:15:55 AM
    LastUpdateTimestamp : 5/17/2016 9:06:07 AM
    Controller0Events   :
    Controller1Events   :
    ```

    > [!NOTE]
    > 当更新仍在进行时，cmdlet 偶尔会报告 `False`。 为了确保完成修补程序更新，请等待几分钟再重新运行此命令，然后检查 `RunInProgress` 是否为 `False`。 如果是，则表示修补程序更新完成。

1. 完成软件更新后，请检查系统软件版本。 键入：
   
    `Get-HcsSystem`
   
    你应该会看到以下版本：
   
   * `HcsSoftwareVersion: 6.3.9600.17708`
   * `CisAgentVersion: 1.0.9299.0`
   * `MdsAgentVersion: 30.0.4698.16` 
     
     如果在应用更新后版本号并未更改，则表示此修补程序未成功应用。 如果出现这种情况，请联系 [Microsoft 支持](../articles/storsimple/storsimple-contact-microsoft-support.md)获取进一步的帮助。
     
     > [!IMPORTANT]
     > 必须先通过 `Restart-HcsController` cmdlet 重新启动主动控制器，然后应用剩余的更新。 
     > 
     > 
2. 重复步骤 3-5 安装剩余的常规模式修补程序。
   
   * iSCSI 更新 KB3146621
   * WMI 更新 KB3103616
3. 如果要从 Update 2 更新，请跳过此步骤。 如果要从 Update 2 之前的版本更新，则还需要下载：

    - LSI 驱动程序 KB3121900

    - Spaceport 更新 KB3090322

    - Storport 更新 KB3080728

#### <a name="to-install-and-verify-maintenance-mode-hotfixes"></a>安装和验证维护模式修补程序
使用 KB3121899 安装磁盘固件更新。 这是一些干扰性的更新，大约需要 30 分钟才能完成。 可以选择在计划好的维护时段内，通过连接到设备串行控制台安装这些更新。

请注意，如果磁盘固件已是最新版本，则不需要安装这些更新。 从设备串行控制台运行 `Get-HcsUpdateAvailability` cmdlet 检查是否有可用的更新，以及更新是干扰性（维护模式）还是非干扰性（常规模式）更新。

若要安装磁盘固件更新，请遵循以下说明。

1. 将设备置于维护模式。 请注意，连接到处于维护模式的设备时，不应使用 Windows PowerShell 远程功能。 而应该在通过设备串行控制台连接时，在设备控制器上运行此 cmdlet。 键入：
   
    `Enter-HcsMaintenanceMode`
   
    下面显示了示例输出。
   
        Controller0>Enter-HcsMaintenanceMode
        Checking device state...
   
        In maintenance mode, your device will not service IOs and will be disconnected from the Microsoft Azure StorSimple Manager service. Entering maintenance mode will end the current session and reboot both controllers, which takes a few minutes to complete. Are you sure you want to enter maintenance mode?
        [Y] Yes [N] No (Default is "Y"): Y
   
        -----------------------MAINTENANCE MODE------------------------
        Microsoft Azure StorSimple Appliance Model 8100
        Name: Update2-8100-SHG0997879L76673
        Copyright (C) 2014 Microsoft Corporation. All rights reserved.
        You are connected to Controller0 - Passive
        ---------------------------------------------------------------
   
        Serial Console Menu
        [1] Log in with full access
        [2] Log into peer controller with full access
        [3] Connect with limited access
        [4] Change language
        Please enter your choice>
   
    然后，两个控制器将重新启动并进入维护模式。
2. 若要安装磁盘固件更新，请键入：
   
    `Start-HcsHotfix -Path <path to update file> -Credential <credentials in domain\username format>`
   
    下面显示了示例输出。
   
        Controller1>Start-HcsHotfix -Path \\10.100.100.100\share\DiskFirmwarePackage.exe -Credential contoso\john
        Enter Password:
        WARNING: In maintenance mode, hotfixes should be installed on each controller sequentially. After the hotfix is installed on this controller, install it on the peer controller.
        Confirm
        This operation starts a hotfix installation and could reboot one or both of the controllers. By installing new updates you agree to, and accept any additional terms associated with, the new functionality listed in the release notes (https://go.microsoft.com/fwLink/?LinkID=613790). Are you sure you want to continue?
        [Y] Yes [N] No (Default is "Y"): Y
        WARNING: Installation is currently in progress. This operation can take several minutes to complete.
3. 使用 `Get-HcsUpdateStatus` 命令监视安装进度。 当 `RunInProgress` 更改为 `False` 时，即表示更新完成。
4. 安装完成后，安装维护模式修补程序的控制器将重新启动。 使用选项 1 以完全访问权限登录，然后检查磁盘固件版本。 键入：
   
   `Get-HcsFirmwareVersion`
   
   预期的磁盘固件版本为：
   
   `XMGG, XGEG, KZ50, F6C2, VR08`
   
   下面显示了示例输出。
   
       -----------------------MAINTENANCE MODE------------------------
       Microsoft Azure StorSimple Appliance Model 8100
       Name: Update2-8100-SHG0997879L76YD
       Software Version: 6.3.9600.17705
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
   
    在第二个控制器上运行 `Get-HcsFirmwareVersion` 命令，验证软件版本是否已更新。 然后可以退出维护模式。 为此，请针对每个设备控制器键入以下命令：
   
   `Exit-HcsMaintenanceMode`
5. 退出维护模式时，控制器会重新启动。 成功应用磁盘固件更新并且设备退出维护模式后，将返回 Azure 经典门户。 请注意，门户在 24 小时内可能不会显示已安装维护模式更新。

