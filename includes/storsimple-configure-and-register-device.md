<!--author=alkohli last changed: 12/01/15-->


#### <a name="to-configure-and-register-the-device"></a>若要配置和注册设备
1. 访问在你的 StorSimple 设备串行控制台上的 Windows PowerShell 接口。 请参阅[使用 PuTTY 连接到设备串行控制台](#use-putty-to-connect-to-the-device-serial-console)有关的说明。 **请务必严格遵照该过程或你将无法访问控制台。**
2. 在打开的会话，按 enter 键一次以启动命令提示符。 
3. 系统将提示你选择的语言，你想要为你设备设置。 指定的语言，然后按 enter 键。 
   
    ![StorSimple 配置和注册的设备 1](./media/storsimple-configure-and-register-device/HCS_RegisterYourDevice1-include.png)
4. 在串行控制台菜单中显示，选择选项 1 以具有完全访问权限登录。 
   
    ![StorSimple 注册设备 2](./media/storsimple-configure-and-register-device/HCS_RegisterYourDevice2-include.png)
   
     完成步骤 5-12 以配置你的设备的最小所需的网络设置。 **这些配置步骤需要设备在活动控制器上执行。** 串行控制台菜单中指示横幅消息中的控制器状态。 如果你未连接到主动控制器，断开连接，然后连接到主动控制器。
5. 在命令提示符处，键入你的密码。 默认设备密码**Password1**。
6. 键入下列命令：
   
     `Invoke-HcsSetupWizard` 
7. 安装向导将显示帮助你配置设备的网络设置。 提供以下信息： 
   
   * DATA 0 网络接口的 IP 地址
   * 子网掩码
   * Gateway
   * 主 DNS 服务器的 IP 地址
   * 主 NTP 服务器的 IP 地址
     
     > [!NOTE]
     > 你可能需要等待几分钟让子网掩码和要应用的 DNS 设置。 如果你收到"设备不准备就绪。" 错误消息，请检查主动控制器 DATA 0 网络接口上的物理网络连接。
     > 
     > 
8. （可选） 配置 web 代理服务器。 尽管 web 代理配置是可选的但**请注意，如果你使用 web 代理，你可以仅配置它此处**。 有关详细信息，请转到[配置你的设备的 web 代理](../articles/storsimple/storsimple-configure-web-proxy.md)。 如果在此步骤中遇到任何问题，请参阅的故障排除指南[web 代理配置期间发生的错误](../articles/storsimple/storsimple-troubleshoot-deployment.md#errors-during-the-optional-web-proxy-settings)。

     > [!NOTE]
     > 可以随时可以退出安装向导中按 Ctrl + C。 将保留发布此命令之前应用的任何设置。

1. 出于安全原因，设备管理员密码过期后的第一个会话，并将需要将其更改为后续会话。 出现提示时，提供设备管理员密码。 有效的设备管理员密码必须介于 8 到 15 个字符之间。 密码必须包含小写字符、 大写字符、 数字和特殊字符的组合。
2. StorSimple 快照管理器密码也在此处设置。 与你运行 StorSimple 快照管理器的 Windows 主机验证设备时，你可以使用此密码。 出现提示时，提供一个 14 到 15 个字符的密码。 密码必须包含以下三种的组合： 小写、 大写、 数字和特殊字符。 
   
   ![StorSimple 注册设备 4](./media/storsimple-configure-and-register-device/HCS_RegisterYourDevice4-include.png)
   
   你可以重置从 StorSimple Manager 服务接口的 StorSimple 快照管理器密码。 有关详细步骤，请转到[更改使用 StorSimple Manager 服务的 StorSimple 密码](../articles/storsimple/storsimple-change-passwords.md)。
   
   若要在此步骤解决任何问题，请参阅的故障排除指南[与密码相关的错误](../articles/storsimple/storsimple-troubleshoot-deployment.md#errors-related-to-device-administrator-and-storsimple-snapshot-manager-passwords)。
3. 安装向导中的最后一步使用 StorSimple Manager 服务注册你的设备。 为此，你将需要在步骤 2 中获取服务注册密钥。 提供注册密钥后，你可能需要等待 2-3 分钟，然后注册该设备。
   
   若要解决任何可能的设备注册失败，请参阅[设备注册期间的错误](../articles/storsimple/storsimple-troubleshoot-deployment.md#errors-during-device-registration)。 有关详细的疑难解答，你还可以对引用[逐步故障排除示例](../articles/storsimple/storsimple-troubleshoot-deployment.md#step-by-step-storsimple-troubleshooting-example)。
4. 注册设备之后，将显示一个服务数据加密密钥。 复制此密钥并将其保存在安全的位置。
   
   > [!WARNING]
   > 使用服务注册密钥向 StorSimple Manager 服务注册其他设备将需要使用此密钥。 请参阅[StorSimple 安全性](../articles/storsimple/storsimple-security.md)有关此密钥的详细信息。
   > 
   > 
   
    ![StorSimple 注册设备 6](./media/storsimple-configure-and-register-device/HCS_RegisterYourDevice6-include.png)
   
    若要从串行控制台窗口复制文本，只需选择的文本。 你应能够将其粘贴到剪贴板或任何文本编辑器中。 请勿使用 Ctrl + C 复制服务数据加密密钥。 使用 Ctrl + C 将导致你退出安装向导。 因此，将不会更改设备管理员密码和 StorSimple 快照管理器密码，设备将恢复默认密码。
5. 退出串行控制台。
6. 返回到 Azure 经典门户中，并完成以下步骤：
   
   1. 双击你的 StorSimple Manager 服务访问**快速启动**页。
   2. 单击**查看连接的设备**。
   3. 上**设备**页上，验证设备是否成功连接到该服务通过查看状态。 设备状态应该是**联机**。 如果设备状态为**脱机**，请稍等几分钟以便设备联机。
   
   ![StorSimple 设备页](./media/storsimple-configure-and-register-device/HCS_DevicesPageM-include.png) 
   
   > [!IMPORTANT]
   > 设备处于联机状态后，你必须在开始此步骤中拔出的网络电缆插入。
   > 
   > 

设备成功注册并不处于联机状态后，你可以运行`Test-HcsmConnection -Verbose`若要确保网络连接正常。 有关此 cmdlet 的详细使用情况，请转到[Test-hcsmconnection cmdlet 参考](https://technet.microsoft.com/library/dn715782.aspx)。

![可用的视频](./media/storsimple-configure-and-register-device/Video_icon.png)**可用的视频**

若要观看的视频，演示如何配置你的设备通过 Windows PowerShell for StorSimple 和注册，请单击[此处](https://azure.microsoft.com/documentation/videos/initialize-the-storsimple-appliance/)。

