<!--author=SharS last changed: 02/22/16-->

### <a name="to-configure-and-register-the-device"></a>配置和注册设备
1. 访问 StorSimple 设备串行控制台上的 Windows PowerShell 接口。 有关说明，请参阅 [使用 PuTTY 连接到设备串行控制台](#use-putty-to-connect-to-the-device-serial-console) 。 **请务必严格遵照该步骤，否则将无法访问控制台。**
2. 在打开的会话中，按一次 Enter 以启动命令提示符。 
3. 系统将提示你选择要为设备设置的语言。 指定语言后，按 Enter。 
   
    ![StorSimple 配置和注册设备 1](./media/storsimple-configure-and-register-device-gov/HCS_RegisterYourDevice1-gov-include.png)
4. 在显示的串行控制台菜单中，选择选项 1 以具有完全访问权限进行登录。 
   
    ![StorSimple 注册设备 2](./media/storsimple-configure-and-register-device-gov/HCS_RegisterYourDevice2-gov-include.png)
5. 执行以下步骤，为设备配置最低要求的网络设置。
   
   > [!IMPORTANT]
   > 需要在该设备的活动控制器上执行这些配置步骤。 串行控制台菜单指示标题消息中的控制器状态。 如果未连接到活动控制器，请断开连接，然后连接到活动控制器。
   > 
   > 
   
   1. 在命令提示符处，键入你的密码。 默认设备密码为 **Password1**。
   2. 输入以下命令：
      
        `Invoke-HcsSetupWizard`
   3. 随后将出现安装向导，以帮助你配置设备的网络设置。 提供以下信息： 
      
      * DATA 0 网络接口的 IP 地址
      * 子网掩码
      * 网关
      * 主 DNS 服务器的 IP 地址
      * 主 NTP 服务器的 IP 地址
      
      > [!NOTE]
      > 应用子网掩码和 DNS 设置时，可能需要等待几分钟。 
      > 
      > 
   4. 可以选择配置 Web 代理服务器。
      
      > [!IMPORTANT]
      > 尽管 Web 代理服务器配置是可选项，仍应注意，在使用 Web 代理时，只能在此处配置它。 有关详细信息，请转到 [配置你的设备的 Web 代理](../articles/storsimple/storsimple-configure-web-proxy.md)。 
      > 
      > 
6. 按 Ctrl + C 可以退出安装向导。
7. 按以下步骤安装更新：
   
   1. 使用以下 cmdlet 在两个控制器上设置 IP：
      
      `Set-HcsNetInterface -InterfaceAlias Data0 -Controller0IPv4Address <Controller0 IP> -Controller1IPv4Address <Controller1 IP>`
   2. 在命令提示符处，运行 `Get-HcsUpdateAvailability`。 当更新可用时，应通知用户。
   3. 运行 `Start-HcsUpdate`。 可以在任何节点上运行此命令。 更新将应用第一个控制器上，该控制器将故障转移，随后更新在另一个控制器上应用。
      
      可以通过运行 `Get-HcsUpdateStatus` 监视更新进度。    
      
      以下示例输出显示更新正在进行。
      
      ````
      Controller0>Get-HcsUpdateStatus
      RunInprogress       : True
      LastHotfixTimestamp : 4/13/2015 10:56:13 PM
      LastUpdateTimestamp : 4/13/2015 10:35:25 PM
      Controller0Events   :
      Controller1Events   : 
      ````
      
      以下示例输出指示更新已完成。
      
      ````
      Controller1>Get-HcsUpdateStatus
      
      RunInprogress       : False
      LastHotfixTimestamp : 4/13/2015 10:56:13 PM
      LastUpdateTimestamp : 4/13/2015 10:35:25 PM
      Controller0Events   :
      Controller1Events   :
      
      ````
      
      它可能需要长达 11 个小时，才能应用包括 Windows 更新在内的所有更新。

8. 在所有更新都已成功安装后，运行以下 cmdlet，确认已应用正确的软件更新：
   
     `Get-HcsSystem`
   
    你应该会看到以下版本：
   
   * HcsSoftwareVersion：6.3.9600.17491
   * CisAgentVersion：1.0.9037.0
   * MdsAgentVersion：26.0.4696.1433
9. 运行以下 cmdlet，确认已正确应用固件更新：
   
    `Start-HcsFirmwareCheck`。
   
     固件状态应为 **UpToDate**。
10. 运行以下 cmdlet，以将设备指向 Microsoft Azure 政府门户（因为它默认指向公共 Azure 经典门户）。 这将重新启动两个控制器。 我们建议使用两个 PuTTY 会话以便同时连接两个控制器，这样你可以查看每个控制器何时重新启动。
    
     `Set-CloudPlatform -AzureGovt_US`
    
    将显示确认消息。 接受默认值 (**Y**)。
11. 运行以下 cmdlet 以恢复安装：
    
     `Invoke-HcsSetupWizard`
    
     ![恢复安装向导](./media/storsimple-configure-and-register-device-gov/HCS_ResumeSetup-gov-include.png)
    
    恢复安装时，向导将为 Update 1 版本（这对应于版本 17469）。 
12. 接受网络设置。 接受每个设置后，你将看到一条验证消息。
13. 出于安全原因，设备管理员密码将在第一个会话后过期，因此需要现在更改密码。 出现提示时，提供设备管理员密码。 有效的设备管理员密码必须介于 8 到 15 个字符之间。 密码必须包含以下各项的其中三个组合：小写字母、大写字母、数字和特殊字符。
    
    <br/>![StorSimple 注册设备 5](./media/storsimple-configure-and-register-device-gov/HCS_RegisterYourDevice5_gov-include.png)
14. 安装向导中的最后一步使用 StorSimple Manager 服务注册设备。 为此，将需要使用在[步骤 2：获取服务注册密钥](#step-2-get-the-service-registration-key)中所获取的服务注册密钥。 提供注册密钥后，可能需要等待 2-3 分钟，然后注册该设备。
    
    > [!NOTE]
    > 可以随时按 Ctrl+C 退出安装向导。 如果已输入所有网络设置（DATA 0 的 IP 地址、子网掩码和网关），将保留这些条目。
    > 
    > 
    
    ![StorSimple 注册进度](./media/storsimple-configure-and-register-device-gov/HCS_RegistrationProgress-gov-include.png)
15. 注册设备后，将显示一个服务数据加密密钥。 复制此密钥并将其保存到一个安全位置。 **向 StorSimple Manager 服务注册其他设备时将需要此密钥以及该服务注册密钥。** 有关此密钥的详细信息，请参阅 [StorSimple 安全性](../articles/storsimple/storsimple-security.md) 。
    
    ![StorSimple 注册设备 7](./media/storsimple-configure-and-register-device-gov/HCS_RegisterYourDevice7_gov-include.png)    
    
    > [!IMPORTANT]
    > 要复制串行控制台窗口中的文本，只需选择该文本。 然后应能够将其粘贴到剪贴板或任何文本编辑器中。 
    > 
    > 请勿使用 Ctrl + C 复制服务数据加密密钥。 使用 Ctrl+C 会导致退出安装向导。 这样做的结果是，将不会更改设备管理员密码，并且设备将还原为默认密码。
    > 
    > 
16. 退出串行控制台。
17. 返回到 Azure 政府门户，并完成以下步骤：
    
    1. 双击 StorSimple Manager 服务，以访问“ **快速启动** ”页。
    2. 单击“ **查看连接的设备**”。
    3. 在“ **设备** ”页上，通过查看状态验证该设备是否已成功连接到该服务。 设备状态应为“ **联机**”。
       
        ![StorSimple 设备页](./media/storsimple-configure-and-register-device-gov/HCS_DeviceOnline-gov-include.png) 
       
        如果设备状态为“ **脱机**”，请等待几分钟以便设备联机。 
       
        如果几分钟后设备仍处于脱机状态，需要确保防火墙网络已按 [StorSimple 设备的网络要求](../articles/storsimple/storsimple-system-requirements.md)中所述进行配置。 
       
        确认端口 9354 已对出站通信打开，因为这将用于 StorSimple Manager 服务之间通信的服务总线。

